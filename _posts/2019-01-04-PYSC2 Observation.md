---
title:          "pysc2 features"
date:           2019-01-04
categories:     normal
---

# PYSC2

## Observation
### Feature Layers
#### Minimap `저화질로 축소된 전체 지도`
- **height_map**: 지형 레벨
- **visibility**: 맵의 어떤부분이 가려지는지, 보여졌는지, 보고있는지 나타냄
- **creep**: 맵의 어떤부분이 저그의 creep 으로 덮혀있는지 나타냄
- **camera**: 맵의 어떤부분이 **screen** 에서 보여지고 있는지 나타냄
- **player_id**: ID 와 함께 유닛이 누구의 소유인지 나타냄
- **player_relative**: 어떤 유닛이 아군 vs 적군 인지 나타냄 [0, 4] 사이의 범위를 가짐 [background, self, ally, neutral, enemy] `(self 는 뭐지?)`
- **selected**: 어떤 유닛이 선택 되었는지 나타냄

#### Screen `고화질의 카메라 스크린 화면`
- **height_map**: 지형 레벨
- **visibility**: 맵의 어떤부분이 가려지는지, 보여졌는지, 보고있는지 나타냄
- **creep**: 맵의 어떤부분이 저그의 creep 으로 덮혀있는지 나타냄
- **power**:  Which parts have protoss power, only shows your power
- **player_id**: ID 와 함께 유닛이 누구의 소유인지 나타냄
- **player_relative**: 어떤 유닛이 아군 vs 적군 인지 나타냄 [0, 4] 사이의 범위를 가짐 [background, self, ally, neutral, enemy]`(self 는 뭐지?)`
- **unit_type**: 유닛 종류 ID, pysc2/lib/units.py 에서 확인할 수 있음
- **selected**: 어떤 유닛이 선택 되었는지 나타냄
- **hit_points**: 유닛의 체력
- **energy**: 유닛의 에너지(마나일듯)
- **shields**: 유닛의 쉴드, 프로토스만 나타남
- **unit_density**: How many units are in this pixel
	> pixel 이 화면의 pixel 을 말하는 것인지 확인이 필요함
- **unit_density_aa**: An anti-aliased version of unit_density with a maximum of 16 per unit per pixel. This gives you sub-pixel unit location and size. For example if a unit is exactly 1 pixel diameter, `unit_density` will show it in exactly 1 pixel regardless of where in that pixel it is actually centered. `unit_density_aa` will instead tell you how much of each pixel is covered by the unit. A unit that is smaller than a pixel and centered in the pixel will give a value less than the max. A unit with diameter 1 centered near the corner of a pixel will give roughly a quarter of its value to each of the 4 pixels it covers. If multiple units cover a pixel their proportion of the pixel covered will be summed, up to a max of 256.


#### Structured

The game offers a fair amount of structured data which agents aren't expected to read from pixels. Instead these are given as tensors with direct semantic meaning.

##### [](https://github.com/deepmind/pysc2/blob/master/docs/environment.md#general-player-information)General player information

A  `(11)`  tensor showing general information.

-   player_id
-   minerals
-   vespene
-   food used (otherwise known as supply)
-   food cap
-   food used by army
-   food used by workers
-   idle worker count
-   army count
-   warp gate count (for protoss)
-   larva count (for zerg)

##### [](https://github.com/deepmind/pysc2/blob/master/docs/environment.md#control-groups)Control groups

A  `(10, 2)`  tensor showing the (unit leader type and count) for each of the 10 control groups. The indices in this tensor are referenced by the  `control-group`  action.

[Control groups](http://learningsc2.com/tag/control-groups/)  are a way to remember a selection set so that you can recall them easily later.

##### [](https://github.com/deepmind/pysc2/blob/master/docs/environment.md#single-select)Single Select

A  `(7)`  tensor showing information about a selected unit.

-   unit type
-   player_relative
-   health
-   shields
-   energy
-   transport slot taken if it's in a transport
-   build progress as a percentage if it's still being built

##### [](https://github.com/deepmind/pysc2/blob/master/docs/environment.md#multi-select)Multi Select

A  `(n, 7)`  tensor with the same as  [single select](https://github.com/deepmind/pysc2/blob/master/docs/environment.md#single-select)  but for all  `n`  selected units. The indices in this tensor are referenced by the`select_unit`  action.

##### [](https://github.com/deepmind/pysc2/blob/master/docs/environment.md#cargo)Cargo

A  `(n, 7)`  tensor similar to  [single select](https://github.com/deepmind/pysc2/blob/master/docs/environment.md#single-select), but for all the units in a transport. The indices in this tensor are referenced by the  `unload`  action.

##### [](https://github.com/deepmind/pysc2/blob/master/docs/environment.md#build-queue)Build Queue

A  `(n, 7)`  tensor similar to  [single select](https://github.com/deepmind/pysc2/blob/master/docs/environment.md#single-select), but for all the units being built by a production building. The indices in this tensor are referenced by the  `build_queue`  action.

##### [](https://github.com/deepmind/pysc2/blob/master/docs/environment.md#available-actions)Available Actions

A  `(n)`  tensor listing all the action ids that are available at the time of this observation.

##### [](https://github.com/deepmind/pysc2/blob/master/docs/environment.md#last-actions)Last Actions

A  `(n)`  tensor listing all the action ids that were made successfully since the last observation. An action that was attempted but failed is not included here.

##### [](https://github.com/deepmind/pysc2/blob/master/docs/environment.md#action-result)Action Result

A  `(n)`  tensor (usually size 1) giving the result of the action. The values are listed in  [error.proto](https://github.com/Blizzard/s2client-proto/blob/master/s2clientprotocol/error.proto)

##### [](https://github.com/deepmind/pysc2/blob/master/docs/environment.md#alerts)Alerts

A  `(n)`  tensor (usually empty, occasionally size 1, max 2) for when you're being attacked in a major way.
