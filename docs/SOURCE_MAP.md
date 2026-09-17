# Source and review record

| Local package | Original branch | Commit | Original path |
| --- | --- | --- | --- |
| `src/il_data_tools` | `main` | `0ae216c6255e25404560948f3e6d5479a7a7ba8f` | `src/il_data_tools` |
| `src/my_rule` | `main` | `0ae216c6255e25404560948f3e6d5479a7a7ba8f` | `src/study/my_rule` |
| `src/xycar_map_nav` | `main` | `0ae216c6255e25404560948f3e6d5479a7a7ba8f` | `src/xycar_map_nav` |
| `src/xycar_parking_nav` | `빠킹` | `79d25b045c0e7c4f46ec8419166e203b5e2f26a4` | `xycar_ws/src/xycar_parking_nav` |

Source repository: <https://github.com/steveandy-sudo/kookmin_autonomous_competition_teamKAI>

Every file in the four package directories was copied byte-for-byte except three model-weight files in `my_rule/models/`:

- `final.pt`
- `kookmin_objects_best_20260804.pt`
- `my_rule_lane.pt`

The original team repository has additional packages required for full ROS 2 execution. This copy is organized for code reading and provenance review. The package manifests retain their original license declarations; no new repository-wide license is asserted here.

## Before making this repository public

- Confirm which files reflect Junghun Hwang's direct changes versus other team members' work.
- Confirm the team is comfortable with this curated copy being public, including configuration, maps, and test data included in the selected packages.
- Decide whether to keep all four packages or reduce the copy further after that review.
- If a runnable standalone release is desired, add and test the missing ROS 2 packages, dependencies, model assets, and hardware setup separately.

This repository can remain private while the review is in progress. The [original public team repository](https://github.com/steveandy-sudo/kookmin_autonomous_competition_teamKAI) remains available for full history.
