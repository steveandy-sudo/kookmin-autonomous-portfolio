# Learning architecture and evaluation record

This note connects the imitation-learning source to its evaluation method. The learning approach was developed during the project; the final competition strategy used rule-based driving.

## Canonical-image steering policy

The `resnet18_lidar` implementation fuses a canonical road image with a synchronized 2D LiDAR scan. Canonical images represent the road in a consistent bird's-eye view before the steering model receives them.

```text
Canonical road image -> ResNet18 image encoder --+
                                                +-> feature concatenation
LiDAR ranges + validity mask -> 1D CNN encoder ---+       |
                                                        v
                                            steering regression head
                                                        |
                                                        v
                                              normalized steering
                                                        |
                                                        v
                                   ROS 2 command scaling, smoothing,
                                   steering limits, and speed selection
```

| Component | Implementation |
| --- | --- |
| Image branch | ResNet18 feature encoder; default input is RGB `3 x 90 x 160` |
| LiDAR branch | 1D convolutions over normalized ranges and a validity mask; default input is `2 x 360` |
| Fusion and output | Concatenated features, fully connected regression head, and one `tanh` steering output |
| Control wrapper | Command scaling/sign, steering bounds, temporal smoothing, speed selection, synchronization and sensor-timeout checks |

These are source-level architecture and default configuration facts. The original checkpoint and run configuration are needed to establish the settings of a particular experiment.

## Dataset and evaluation method

1. **Record paired observations and commands.** The recorder collects camera images, LiDAR scans, motor commands, mission labels, timestamps, and session identifiers. The dataset builder filters unusable records and can require synchronized scan data.
2. **Split complete sessions.** The builder assigns all rows with one `session_id` to a single split. Generated canonical-image variants preserve the original session identifier so alternate views of one drive stay together. This reduces leakage between nearby frames; it does not by itself establish generalization to a new course or vehicle.
3. **Train steering regression.** Motor-angle commands are normalized to the configured command scale. Training weights samples by steering magnitude and the recovery label, selects the checkpoint with the lowest validation loss, and supports TorchScript export.
4. **Evaluate recorded commands.** The evaluator calculates MAE, RMSE, maximum absolute error, and grouped errors by session, mission label, steering range, and speed-command range. These are offline command-prediction errors. Closed-loop driving needs separate trajectory, intervention, and completion records.

### Units and interpretation

Several retained field names end in `_deg`. The data path records the motor's angle command, normalizes it, and scales predictions back to that command range. Treat the reported errors as **XYCAR steering-command units** unless a corresponding physical-angle calibration is supplied. Similarly, the speed bins use recorded command values rather than a verified speed measurement.

The evaluator returns zero for an empty error bin. A zero bin value alone therefore does not establish perfect performance; bin sample counts or the row-level predictions are needed.

## Reading the implementation

| Step | Source |
| --- | --- |
| Recorder | [common_recorder_node.py](../src/il_data_tools/il_data_tools/common_recorder_node.py) |
| Dataset filtering and session split | [dataset_builder_common.py](../src/il_data_tools/il_data_tools/dataset_builder_common.py) |
| Source-session preservation | [canonical_dataset_converter.py](../src/il_data_tools/il_data_tools/canonical_dataset_converter.py) |
| Input preprocessing | [policy_dataset.py](../src/il_data_tools/scripts/policy_dataset.py) · [image_preprocessing.py](../src/il_data_tools/scripts/image_preprocessing.py) |
| Fusion model | [policy_models.py](../src/il_data_tools/scripts/policy_models.py) |
| Training and validation | [train_policy.py](../src/il_data_tools/scripts/train_policy.py) |
| Offline evaluation | [eval_policy.py](../src/il_data_tools/scripts/eval_policy.py) |
| Runtime inference | [policy_inference_node.py](../src/il_data_tools/il_data_tools/policy_inference_node.py) |
| Collection-to-evaluation orchestration | [canonical_pipeline.py](../src/il_data_tools/il_data_tools/canonical_pipeline.py) |

## Archived evidence status

The imported source includes a [metrics JSON](../src/il_data_tools/models/drive_canonical_policy_metrics.json) dated July 16, 2026 and an earlier [model README](../src/il_data_tools/models/README.md). Their sample counts, model hashes, and reported errors differ. They are preserved as imported records, with the mismatch documented here.

Numerical performance remains provisional until a corresponding checkpoint, dataset/split manifest, run configuration, and prediction records are matched. The checkpoint weights and underlying dataset are not included in this repository. The JSON contains some collection and test-session metadata, but it does not allow an independent reconstruction of the complete train/validation/test assignment or establish which checkpoint was deployed on the vehicle.

The current public evidence therefore supports the implemented architecture, evaluation procedure, and development history. A future experiment record can pair the matched artifacts with session counts, per-condition errors, a baseline, and a separately documented closed-loop trial.
