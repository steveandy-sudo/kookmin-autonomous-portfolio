# Source record

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

## Attribution and verification

The repository README describes Junghun Hwang's contributions by function. The selected packages preserve the shared team implementation and original maintainer declarations. The file manifest records source paths and revisions for attribution.

The initial collection verified 251 source files against the local checkouts of the revisions above and checked Python syntax. These checks do not establish a standalone ROS 2 build, real-vehicle performance, or automatic integration of race driving with parking. Each subsystem retains its original launch paths.

The [original team repository](https://github.com/steveandy-sudo/kookmin_autonomous_competition_teamKAI) contains the development history and remaining workspace packages.
