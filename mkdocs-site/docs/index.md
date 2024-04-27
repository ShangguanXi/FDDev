# Farmer's Delight Bedrock Dev Doc

> [!NOTE]
>
> 当前文档对应的农夫乐事版本为1.6.0+

我们提供了一些Tag和其他接口来让附属更好的与农夫乐事本体进行交互。

## 物品可堆肥注册

使用如下tag注册：

```
"farmersdelight:is_fertilizer.xxx"“
```

xxx代表可堆肥性，使用整数，代表xxx%。物品的百分比越高，使用时增加一层堆肥层数的概率就越高。

示例：

```
{
	"minecraft:tags": {
		"tags": [
			"farmersdelight:is_fertilizer.65"//每次和堆肥桶交互有65%的几率使堆肥桶增加一层堆肥层数。
		]
	}
}
```



## 砧板配方注册

### 砧板放置注册

#### 模组物品

使用如下tag注册：

当工具类型为item的时候：

```
"farmersdelight:can_cut.item.xxx"
```

xxx代表工具的赋命名空间标识符。

示例：

```
{
	"minecraft:tags": {
		"tags": [
			"farmersdelight:can_cut.item.minecraft:nether_star"//使用下界之星切割
		]
	}
}
```

当工具类型为tag的时候：

```
"farmersdelight:can_cut.tag.xxx"
```

xxx代表工具所具有的标签。

示例：

```
{
	"minecraft:tags": {
		"tags": [
			"farmersdelight:can_cut.tag.farmersdelight:is_knife"//使用具有farmersdelight:is_knife标签的物品切割
		]
	}
}
```

#### 原版物品

对于原版的物品，我们提供了两种注册方式：分别是通过计分板+ticks.json或者脚本（sapi）。

##### 计分板+ticks.json

首先执行下列命令：

```
/scoreboard objectives add farmersdelight_xxx_cutting_board dummy
```

xxx_cutting_board为配方function的文件名，xxx可自定义。

再在

farmersdelight/cutting_board_recipe_registries/xxx.function里写入如下命令：

```
scriptevent farmersdelight:cutting_board_recipe namespace:identifier
...
scoreboard objectives remove farmersdelight_xxx_cutting_board
```

namespace:identifier为要被切割的物品的赋命名空间标识符。(目前仅支持被刀切割)

##### 脚本

通过worldInitialize事件在世界加载的时候注册，当然也可以通过其他的事件进行注册。应注意的是，配方只应注册一次。

注册方式如下:

```javascript
import {world } from "@minecraft/server";

world.getDimension("overworld").runCommandAsync("scriptevent farmersdelight:cutting_board_recipe namespace:identifier");
//或
world.getDimension("overworld").runCommandAsync("function xxx");
```

xxx为function文件，里面的内容应为：

```
scriptevent farmersdelight:cutting_board_recipe namespace:identifier
'''
scriptevent farmersdelight:cutting_board_recipe namespace:identifier
```

#### 方块

正在实现，敬请期待。

### 砧板物品展示注册

我们通过粒子来让砧板显示对应的物品

#### 模组物品

粒子的id要与物品的id一致。

示例：

```json
{
	"format_version": "1.10.0",
	"particle_effect": {
		"description": {
			"basic_render_parameters": {
				"material": "particles_alpha",
				"texture": "textures/items/wheat_dough"//纹理路径
			},
			"identifier": "farmersdelight:wheat_dough"//面团物品
		},
		"components": {
			"minecraft:emitter_lifetime_once": {
				"active_time": 0.12
			},
			"minecraft:emitter_rate_steady": {
				"max_particles": 250,
				"spawn_rate": 1250
			},
			"minecraft:emitter_shape_box": {
				"offset": [0, 0.01563, 0],
				"half_dimensions": [0, 0.01563, 0],
				"direction": "outwards"
			},
			"minecraft:particle_appearance_billboard": {
				"facing_camera_mode": "emitter_transform_xz",
				"size": [0.25, 0.25],
				"uv": {
					"texture_height": 16,
					"texture_width": 16,
					"uv": [0, 0],
					"uv_size": [16, 16]
				}
			},
			"minecraft:particle_initial_speed": 0,
			"minecraft:particle_lifetime_expression": {
				"max_lifetime": 0.05
			},
			"minecraft:particle_motion_dynamic": {},
			"minecraft:particle_appearance_lighting": {}
		}
	}
}
```

#### 原版物品

粒子的id为farmersdelight:minecraft_物品id

注意，物品id为原版物品的赋命名空间标识符去掉命名空间之后剩余的那一部分。

示例：

```json
{
	"format_version": "1.10.0",
	"particle_effect": {
		"description": {
			"basic_render_parameters": {
				"material": "particles_alpha",
				"texture": "textures/items/beef_raw"//纹理路径
			},
			"identifier": "farmersdelight:minecraft_beef"//生牛肉
		},
		"components": {
			"minecraft:emitter_lifetime_once": {
				"active_time": 0.12
			},
			"minecraft:emitter_rate_steady": {
				"max_particles": 250,
				"spawn_rate": 1250
			},
			"minecraft:emitter_shape_box": {
				"offset": [0, 0.01563, 0],
				"half_dimensions": [0, 0.01563, 0],
				"direction": "outwards"
			},
			"minecraft:particle_appearance_billboard": {
				"facing_camera_mode": "emitter_transform_xz",
				"size": [0.25, 0.25],
				"uv": {
					"texture_height": 16,
					"texture_width": 16,
					"uv": [0, 0],
					"uv_size": [16, 16]
				}
			},
			"minecraft:particle_initial_speed": 0,
			"minecraft:particle_lifetime_expression": {
				"max_lifetime": 0.05
			},
			"minecraft:particle_motion_dynamic": {},
			"minecraft:particle_appearance_lighting": {}
		}
	}
}
```

### 砧板配方结果注册

我们使用战利品表来生成物品在砧板上被处理之后返回的结果。

战利品表的路径应为:

物品命名空间/cutting_board/物品标识符.json

示例：

当farmersdelight:apple_pie被处理时，其战利品表路径应为farmersdelight/cutting_board/apple_pie_slice.json



## 炉灶/煎锅配方注册

### 炉灶/煎锅放置注册

#### 模组物品

使用如下tag注册：

```
"farmersdelight:can_cook"
```

示例：

```
{
	"minecraft:tags": {
		"tags": [
			"farmersdelight:can_cook"
		]
	}
}
```

#### 原版物品

对于原版的物品，我们提供了两种注册方式：分别是通过计分板+ticks.json或者脚本（sapi）。

##### 计分板+ticks.json

首先执行下列命令：

```
/scoreboard objectives add farmersdelight_xxx_cook dummy
```

xxx_cook为配方function的文件名，xxx可自定义。

再在

farmersdelight/cook_recipe_registries/xxx.function里写入如下命令：

```
scriptevent farmersdelight:cook namespace:identifier
...
scoreboard objectives remove farmersdelight_xxx_cook
```

namespace:identifier为要被切割的物品的赋命名空间标识符。(目前仅支持被刀切割)

##### 脚本

通过worldInitialize事件在世界加载的时候注册，当然也可以通过其他的事件进行注册。应注意的是，配方只应注册一次。

注册方式如下:

```javascript
import {world } from "@minecraft/server";

world.getDimension("overworld").runCommandAsync("scriptevent farmersdelight:cook namespace:identifier");
//或
world.getDimension("overworld").runCommandAsync("function xxx");
```

xxx为function文件，里面的内容应为：

```
scriptevent farmersdelight:cook namespace:identifier
'''
scriptevent farmersdelight:cook namespace:identifier
```

### 炉灶物品展示注册

我们通过粒子来让炉灶在烧制物品时显示对应的物品。

#### 模组物品

粒子的id应为farmersdelight:stove_物品id

注意，物品id为物品的赋命名空间标识符去掉命名空间之后剩余的那一部分。

示例：

```json
{
	"format_version": "1.10.0",
	"particle_effect": {
		"description": {
			"basic_render_parameters": {
				"material": "particles_alpha",
				"texture": "textures/items/raw_clawster"//纹理路径
			},
			"identifier": "farmersdelight:stove_raw_clawster"//生龙虾
		},
		"components": {
			"minecraft:emitter_lifetime_once": {
				"active_time": 0.12
			},
			"minecraft:emitter_rate_steady": {
				"max_particles": 60,
				"spawn_rate": 1250
			},
			"minecraft:emitter_shape_box": {
				"direction": "outwards",
				"half_dimensions": [0, 0.009375, 0],
				"offset": [0, 0.009375, 0]
			},
			"minecraft:particle_appearance_billboard": {
				"facing_camera_mode": "emitter_transform_xz",
				"size": [0.15, 0.15],
				"uv": {
					"texture_height": 16,
					"texture_width": 16,
					"uv": [0, 0],
					"uv_size": [16, 16]
				}
			},
			"minecraft:particle_initial_speed": 0,
			"minecraft:particle_lifetime_expression": {
				"max_lifetime": 0.05
			},
			"minecraft:particle_motion_dynamic": {},
			"minecraft:particle_appearance_lighting": {}
		}
	}
}
```

#### 原版物品

粒子的id应为farmersdelight:minecraft_stove_物品id

注意，物品id为原版物品的赋命名空间标识符去掉命名空间之后剩余的那一部分。

示例：

```json
{
	"format_version": "1.10.0",
	"particle_effect": {
		"description": {
			"basic_render_parameters": {
				"material": "particles_alpha",
				"texture": "textures/items/fish_clownfish_raw"
			},
			"identifier": "farmersdelight:minecraft_stove_tropical_fish"//热带鱼
		},
		"components": {
			"minecraft:emitter_lifetime_once": {
				"active_time": 0.12
			},
			"minecraft:emitter_rate_steady": {
				"max_particles": 60,
				"spawn_rate": 1250
			},
			"minecraft:emitter_shape_box": {
				"direction": "outwards",
				"half_dimensions": [0, 0.009375, 0],
				"offset": [0, 0.009375, 0]
			},
			"minecraft:particle_appearance_billboard": {
				"facing_camera_mode": "emitter_transform_xz",
				"size": [0.15, 0.15],
				"uv": {
					"texture_height": 16,
					"texture_width": 16,
					"uv": [0, 0],
					"uv_size": [16, 16]
				}
			},
			"minecraft:particle_initial_speed": 0,
			"minecraft:particle_lifetime_expression": {
				"max_lifetime": 0.05
			},
			"minecraft:particle_motion_dynamic": {},
			"minecraft:particle_appearance_lighting": {}
		}
	}
}
```

### 煎锅物品展示注册

我们通过粒子来让煎锅在烧制物品时显示对应的物品。

#### 模组物品

粒子的id应为farmersdelight:skillet_物品id

注意，物品id为物品的赋命名空间标识符去掉命名空间之后剩余的那一部分。

示例：

```json
{
	"format_version": "1.10.0",
	"particle_effect": {
		"description": {
			"basic_render_parameters": {
				"material": "particles_alpha",
				"texture": "textures/items/raw_clawster"
			},
			"identifier": "farmersdelight:skillet_raw_clawster"//生龙虾
		},
		"components": {
			"minecraft:emitter_lifetime_once": {
				"active_time": 0.12
			},
			"minecraft:emitter_rate_steady": {
				"max_particles": 60,
				"spawn_rate": 1250
			},
			"minecraft:emitter_shape_box": {
				"offset": [0, 0.01563, 0],
				"half_dimensions": [0, 0.01563, 0],
				"direction": "outwards"
			},
			"minecraft:particle_appearance_billboard": {
				"facing_camera_mode": "emitter_transform_xz",
				"size": [0.25, 0.25],
				"uv": {
					"texture_height": 16,
					"texture_width": 16,
					"uv": [0, 0],
					"uv_size": [16, 16]
				}
			},
			"minecraft:particle_initial_speed": 0,
			"minecraft:particle_lifetime_expression": {
				"max_lifetime": 0.05
			},
			"minecraft:particle_motion_dynamic": {},
			"minecraft:particle_appearance_lighting": {}
		}
	}
}
```

#### 原版物品

粒子的id应为farmersdelight:minecraft_skillet_物品id

注意，物品id为原版物品的赋命名空间标识符去掉命名空间之后剩余的那一部分。

示例：

```json
{
	"format_version": "1.10.0",
	"particle_effect": {
		"description": {
			"basic_render_parameters": {
				"material": "particles_alpha",
				"texture": "textures/items/fish_clownfish_raw"//纹理路径
			},
			"identifier": "farmersdelight:minecraft_skillet_tropical_fish"//热带鱼
		},
		"components": {
			"minecraft:emitter_lifetime_once": {
				"active_time": 0.12
			},
			"minecraft:emitter_rate_steady": {
				"max_particles": 60,
				"spawn_rate": 1250
			},
			"minecraft:emitter_shape_box": {
				"offset": [0, 0.01563, 0],
				"half_dimensions": [0, 0.01563, 0],
				"direction": "outwards"
			},
			"minecraft:particle_appearance_billboard": {
				"facing_camera_mode": "emitter_transform_xz",
				"size": [0.25, 0.25],
				"uv": {
					"texture_height": 16,
					"texture_width": 16,
					"uv": [0, 0],
					"uv_size": [16, 16]
				}
			},
			"minecraft:particle_initial_speed": 0,
			"minecraft:particle_lifetime_expression": {
				"max_lifetime": 0.05
			},
			"minecraft:particle_motion_dynamic": {},
			"minecraft:particle_appearance_lighting": {}
		}
	}
}
```

### 炉灶配方结果注册

我们使用战利品表来生成物品在砧板上被处理之后返回的结果。

战利品表的路径应为:

物品命名空间/cook/物品标识符.json

示例：

当farmersdelight:bacon被处理时，其战利品表路径应为farmersdelight/cook/bacon.json



## 厨锅配方注册

### 厨锅配方

厨锅的配方为无序配方，一共有6个合成槽和一个容器槽（可选）。一个合成槽可对应一个或多个物品。当一个配方位置对应一个物品时，其应该为Object，当对于多个物品时，其应该为Array。物品的类型可以为item也可以为tag。

对于一个完整的厨锅配方，他的json结构应为如下示例:

```json
{
	"identifer": "namespace:identifer",
	"tags": ["cooking_pot"],
    //使用南瓜作为容器
	"container": {
		"item": "minecraft:pumpkin"
	},
	"priority": 0,
	"time": 200,
	"ingredients": [
        {
			"item": "corn_delight:cornbread"
		},
		[
            {
			"item": "minecraft:carrot"
            }, 
            {
			"item": "farmersdelight:is_onion"
            },
            {
			"item": "minecraft:beetroot"
            }
        ]
	],
	"result": {
		"item": "namespace:identifer",
        "count": 2
	}
}
```

identifer和result要返回的物品id要保持一致。

### 配方注册

我们提供了两种注册方式：分别是通过计分板+ticks.json或者脚本（sapi）。

#### 计分板+ticks.json

首先执行下列命令：

```
/scoreboard objectives add farmersdelight_xxx_cooking_pot dummy
```

xxx_cooking_pot为配方function的文件名，xxx可自定义。

再在

farmersdelight/cooking_pot_recipe_registries/xxx.function里写入如下命令：

```
scriptevent farmersdelight:cooking_pot_recipe {...}
...
scoreboard objectives remove farmersdelight_xxx_cooking_pot
```

{...}是厨锅配方被压缩为一行后的json文本。

#### 脚本

通过worldInitialize事件在世界加载的时候注册，当然也可以通过其他的事件进行注册。应注意的是，配方只应注册一次。

注册方式如下:

```javascript
import {world } from "@minecraft/server";

world.getDimension("overworld").runCommandAsync("scriptevent farmersdelight:cooking_pot_recipe {...}");
//或
world.getDimension("overworld").runCommandAsync("function xxx");
```

xxx为function文件，里面的内容应为：

```
scriptevent farmersdelight:cooking_pot_recipe {...}
'''
scriptevent farmersdelight:cooking_pot_recipe {...}
```

{...}是厨锅配方被压缩为一行后的json文本。

### 厨锅配方UI展示

想在厨锅配方UI里展示自己配方，其应该使用非侵入性修改的方式往chest.recipe_grid_panel的controls添加控件。

一个厨锅配方UI示例：

```json
{
	"crabbersdelight_recipe_part_1": {
		"type": "panel",
		"size": [150, 38],
		"controls": [{
			"bisque@chest.cooking_pot_recipe": {
				"texture": "textures/gui/recipe/cooking_recipe",
				"offset": [37.5, 0],
				"size": [75, 38],
				"controls": [{
					"cooking_pot_recipe_cell@chest.cooking_pot_recipe_cell": {
						"controls": [{
							"cell0": {
								"type": "image",
								"offset": [-23, -7],
								"size": [7.5, 7.5],
								"texture": "textures/items/onion"
							}
						}, {
							"cell1": {
								"type": "image",
								"offset": [-14.625, -7],
								"size": [7.5, 7.5],
								"texture": "textures/items/rice"
							}
						}, {
							"cell2": {
								"type": "image",
								"offset": [-6.25, -7],
								"size": [7.5, 7.5],
								"texture": "textures/items/milk_bottle"
							}
						}, {
							"cell3": {
								"type": "image",
								"offset": [-23, 1.5],
								"size": [7.5, 7.5],
								"texture": "textures/items/carrot"
							}
						}, {
							"cell4@chest.is_seafood": {
								"offset": [-14.625, 1.5],
								"size": [7.5, 7.5]
							}
						}, {
							"container": {
								"type": "image",
								"offset": [6, 10.875],
								"size": [7.5, 7.5],
								"texture": "textures/items/bowl"
							}
						}, {
							"result": {
								"type": "image",
								"offset": [21, -5.25],
								"size": [7.5, 7.5],
								"texture": "textures/items/bisque"
							}
						}]
					}
				}]
			}
		}, {
			"cooked_clawster@chest.cooking_pot_recipe": {
				"texture": "textures/gui/recipe/cooking_recipe",
				"offset": [-37.5, 0],
				"size": [75, 38],
				"controls": [{
					"clam_bake@chest.cooking_pot_recipe_cell": {
						"$cell0": "textures/items/raw_clawster",
						"$result": "textures/items/cooked_clawster"
					}
				}]
			}
		}]
	},
	"crabbersdelight_recipe_grid_1": {
		"type": "grid",
		"size": [150, 38],
		"grid_dimensions": [1, 1],
		"grid_item_template": "chest.crabbersdelight_recipe_part_1"
	},
	"recipe_grid_panel": {
	//使用非侵入性修改的方式往chest.recipe_grid_panel的controls添加控件。
		"modifications": [{
			"array_name": "controls",
			"operation": "insert_back",
			"value": [{
				"crabbersdelight_recipe_grid_1@chest.crabbersdelight_recipe_grid_1": {}
			}]
		}]
	}
}
```

关于jsonUI的使用，这里不在赘述，请自行查阅相关资料。

[我的世界基岩版 UI 文档 (jsonui.netlify.app)](https://jsonui.netlify.app/)

[Intro to JSON UI | Bedrock Wiki](https://wiki.bedrock.dev/json-ui/json-ui-intro.html)



## 橱柜注册

对于一个完整的橱柜，其应该包括厨锅方块和橱柜实体。

### 橱柜方块

橱柜方块应有如下tag：

```
"farmersdelight:cabinet"
```

示例：

```json
{
	"tag:farmersdelight:cabinet": {}
}
```

### 橱柜实体

橱柜实体的namespace:identifier应与橱柜方块的namespace:identifier保持一致。

示例：

```json
{
    "format_version": "1.16.0",
    "minecraft:entity": {
        "description": {
            "identifier": "farmersdelight:acacia_cabinet",
            "is_spawnable": false,
            "is_summonable": true,
            "is_experimental": false
        },
        "component_groups": {
            "farmersdelight:acacia_cabinet_despawn": {
                "minecraft:despawn": {},
                "minecraft:instant_despawn": {
                    "remove_child_entities": false
                }
            },
            "farmersdelight:cabinet_open": {
                "minecraft:entity_sensor": {
                    "minimum_count": 1,
                    "relative_range": true,
                    "sensor_range": 6,
                    "event_filters": {
                        "test": "has_container_open",
                        "subject": "other",
                        "operator": "==",
                        "value": false
                    },
                    "event": "farmersdelight:cabinet_try_close"
                }
            }
        },
        "components": {
            "minecraft:damage_sensor": {
                "triggers": {
                    "on_damage": {},
                    "deals_damage": false
                }
            },
            "minecraft:nameable": {},
            "minecraft:timer": {
                "looping": true,
                "randomInterval": true,
                "time": [
                    0.0,
                    0.0
                ],
                "time_down_event": {
                    "event": "farmersdelight:cabinet_tick"
                }
            },
            "minecraft:persistent": {},
            "minecraft:inventory": {
                "inventory_size": 27,
                "can_be_siphoned_from": true,
                "container_type": "inventory"
            },
            "minecraft:physics": {
                "has_collision": false,
                "has_gravity": false
            },
            "minecraft:scale": 0,
            "minecraft:type_family": {
                "family": [
                    "farmersdelight_tick_block_entity"
                ]
            },
            "minecraft:is_hidden_when_invisible": {},
            "minecraft:breathable": {
                "breathes_solids": true
            },
            "minecraft:health": {
                "value": 1,
                "max": 1,
                "min": 1
            },
            "minecraft:collision_box": {
                "width": 1.0,
                "height": 0.5
            }
        },
        "events": {
            "farmersdelight:despawn": {
                "add": {
                    "component_groups": [
                        "farmersdelight:acacia_cabinet_despawn"
                    ]
                }
            },
            "farmersdelight:cabinet_close": {
                "remove": {
                    "component_groups": [
                        "farmersdelight:cabinet_open"
                    ]
                }
            },
            "farmersdelight:cabinet_interact": {
                "add": {
                    "component_groups": [
                        "farmersdelight:cabinet_open"
                    ]
                }
            },
            "farmersdelight:cabinet_try_close": {},
            "farmersdelight:cabinet_tick": {}
        }
    }
}
```



## 方块食物类（有基座）注册

正在施工ing。



## 派类食物注册

正在施工ing。



### 农夫乐事标签列表

#### 物品

正在施工ing。

#### 方块

正在施工ing。



## 示例包

### 蟹农乐事

[CrabbersDelightV1.3.0-1.20.8x (mediafire.com)](https://www.mediafire.com/file/dqfqqb39eitmlgk/CrabbersDelightV1.3.0-1.20.8x.mcaddon/file)

### 玉米乐事

[CornDelightV1.1.0-1.20.8x (mediafire.com)](https://www.mediafire.com/file/izlagf6wbw8hgq8/CornDelightV1.1.0-1.20.8x.mcaddon/file)

### 农夫乐事本体开源地址(GPLv3 License)

[FarmersDelightBedrock (github.com)](https://github.com/ShangguanXi/FarmersDelightBedrock)

