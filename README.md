# SMAC_MORL
* [SMAC 정보 정리 notion(미완성)](https://certain-shaker-74f.notion.site/SMAC-d7824b06a41c432e9e6ba78279e2c3ec?pvs=4)
## StarCraft2 설치
* https://github.com/oxwhirl/smac 참고

* StarCraft2 최신 버전 설치 (23.06.29 기준 4.10)
	- 설치는 $Base_DIR/StarCraftII에 하는 것 추천 (그렇지 않을 경우, 환경변수 SC2PATH를 설정)
	- 압축을 풀때 암호는 iagreetotheeula 이다.
```
$ cd $BASE_DIR
$ wget https://blzdistsc2-a.akamaihd.net/Linux/SC2.4.10.zip
(만약 permission denied 뜨면, sudo 붙여서)
$ unzip SC2.4.10.zip
```

SMAC_maps 설치한다.
```
$ cd $BASE_DIR/StarCraft2/Maps
$ wget https://github.com/oxwhirl/smac/releases/download/v0.1-beta1/SMAC_Maps.zip
$ unzip SMAC_Maps.zip
```

## conda 환경 및 pip 및 SMAC 설치
- https://github.com/oxwhirl/smac 가 3rd_library에 있음
```
$ conda create -n SMAC python=3
$ pip install -r requirements.txt
$ cd 3rd_library/smac
$ pip install -e .
```

## 실행방법
```
$ python algo/mppo/mppo.py --env 8m --eplen 120 --epochs 1000
```
* python 상위 module의 import를 위해 sys.path.append를 해준다. (mppo.py line 12)
* sys.path.append(os.path.join(os.environ['HOME'], 'SMAC_MORL'))
* 만약 이상한 pysc2 연결 에러 발생하면, StarCraft2 재설치
* mppo는 문헌님이 짜신 알고리즘. 이런식으로 MORL 알고리즘도 추가하면 좋을것 같음

### Argument
```
parser.add_argument(--env', type=str, default='8m')
parser.add_argument('--hid', type=int, default=64)
parser.add_argument('--l', type=int, default=2)
parser.add_argument('--gamma', type=float, default=0.99)
parser.add_argument('--seed', '-s', type=int, default=0)
parser.add_argument('--cpu', type=int, default=4)
parser.add_argument('--steps', type=int, default=4000)
parser.add_argument('--epochs', type=int, default=50)
parser.add_argument('--exp_name', type=str, default='SMAC_8m_mppo')
parser.add_argument('--framework', default="smac", choices=['smac'])
parser.add_argument('--eplen', type=int, default=100)
parser.set_defaults(record=False)
```

## 기본적으로 제공되는 Env
| Name | Ally Units | Enemy Units | Type | Max Ep Len | 
| :---: | :---: | :---: | :---:| :---: |
| 3m | 3 Marines | 3 Marines | homogeneous & symmetric | 60 |
| 8m | 8 Marines | 8 Marines | homogeneous & symmetric | 120 | 
| 25m | 25 Marines | 25 Marines | homogeneous & symmetric | 150 |
| 2s3z |  2 Stalkers & 3 Zealots |  2 Stalkers & 3 Zealots | heterogeneous & symmetric | 120 |
| 3s5z |  3 Stalkers &  5 Zealots |  3 Stalkers &  5 Zealots | heterogeneous & symmetric | 150 |
| MMM |  1 Medivac, 2 Marauders & 7 Marines | 1 Medivac, 2 Marauders & 7 Marines | heterogeneous & symmetric | 150 |
| 5m_vs_6m | 5 Marines | 6 Marines | homogeneous & asymmetric | 70 |
| 8m_vs_9m  | 8 Marines | 9 Marines | homogeneous & asymmetric | 120 |
| 10m_vs_11m | 10 Marines | 11 Marines | homogeneous & asymmetric | 150 |
| 27m_vs_30m | 27 Marines | 30 Marines | homogeneous & asymmetric | 180 |
| 3s5z_vs_3s6z | 3 Stalkers & 5 Zealots | 3 Stalkers & 6 Zealots  | heterogeneous & asymmetric | 170 |
| MMM2 |  1 Medivac, 2 Marauders & 7 Marines |  1 Medivac, 3 Marauders & 8 Marines | heterogeneous & asymmetric | 180 |
| 2m_vs_1z | 2 Marines | 1 Zealot | micro-trick: alternating fire | 150 |
| 2s_vs_1sc| 2 Stalkers  | 1 Spine Crawler | micro-trick: alternating fire | 300 |
| 3s_vs_3z | 3 Stalkers | 3 Zealots | micro-trick: kiting | 150 |
|  3s_vs_4z | 3 Stalkers | 4 Zealots |  micro-trick: kiting | 200 |
| 3s_vs_5z | 3 Stalkers | 5 Zealots |  micro-trick: kiting | 250 |
| 6h_vs_8z | 6 Hydralisks  | 8 Zealots | micro-trick: focus fire | 150 |
| corridor | 6 Zealots  | 24 Zerglings | micro-trick: wall off | 400 |
| bane_vs_bane | 20 Zerglings & 4 Banelings  | 20 Zerglings & 4 Banelings | micro-trick: positioning | 200 |
| so_many_banelings| 7 Zealots  | 32 Banelings | micro-trick: positioning | N/A |
| 2c_vs_64zg| 2 Colossi  | 64 Zerglings | micro-trick: positioning | 400 |
| 1c3s5z | 1 Colossi & 3 Stalkers & 5 Zealots | 1 Colossi & 3 Stalkers & 5 Zealots | heterogeneous & symmetric | 180 |

## New Env 생성
1. StarCraft2 Editor에서 map/scenario 생성 [StarCraft2 Editor Guide](https://s2editor-guides.readthedocs.io/)
2. 적을 자동으로 공격하지않는 special RL units 만드는 법 [Here](https://docs.google.com/document/d/1BfAM_AtZWBRhUiOBcMkb_uK4DAZW3CpvO79-vnEOKxA/edit?usp=sharing)
3. 3rd_library/smac/smac/env/starcraft2/maps/smac_maps.py 에 추가
4. 만약, 새로운 RL 유닛(탱크 등)이 생성되면 3rd_library/smac/smac/env/starcraft2/starcraft2.py 에 새로운 id 추가 필요
5. 특히 2가지 이상의 유닛이 포함된 맵: _init_ally_unit_types() 함수에서 유닛 id 수동으로 설정

'''
기존 유닛 (아마도..?)
marine_id, marauder_id, medivac_id, stalker_id, zealot_id, colossus_id, hydralisk_id,
zergling_id, baneling_id
'''
### 원문 README
Users can extend SMAC by adding new maps/scenarios. To this end, one needs to:
- Design a new map/scenario using StarCraft II Editor:
  - Please take a close look at the existing maps to understand the basics that we use (e.g. Triggers, Units, etc),
  - We make use of special RL units which never automatically start attacking the enemy. [Here](https://docs.google.com/document/d/1BfAM_AtZWBRhUiOBcMkb_uK4DAZW3CpvO79-vnEOKxA/edit?usp=sharing) is the step-by-step guide on how to create new RL units based on existing SC2 units,
- Add the map information in [smac_maps.py](https://github.com/oxwhirl/smac/blob/master/smac/env/starcraft2/maps/smac_maps.py),
- The newly designed RL units have new ids which need to be handled in [starcraft2.py](https://github.com/oxwhirl/smac/blob/master/smac/env/starcraft2/starcraft2.py). Specifically, for heterogenious maps containing more than one unit types, one needs to manually set the unit ids in the `_init_ally_unit_types()` function.

## 결과물 예시
### 3m
![](https://github.com/monhoney/SMAC_MORL/blob/main/asset/3m.gif)

### 8m
![](https://github.com/monhoney/SMAC_MORL/blob/main/asset/8m.gif)
