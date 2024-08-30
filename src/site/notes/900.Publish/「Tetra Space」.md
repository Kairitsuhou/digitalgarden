---
{"dg-home":null,"dg-publish":true,"dg-note-icon":"2","cover":"![](https://github.com/Kairitsuhou/ImageHost/blob/main/Publish%20%E3%80%8ATetra%20Space%E3%80%8B.png?raw=true)","description":"プレイヤーはキャラクターとスペースを操作できる。スペースを組み合わせることで、キャラクターが目的を果たせるような組合を構築する。","tags":["project/TetraSpace"],"platform":"GameMakerStudio2,Aseprite","creation date":"2024-08-29","completion date":"","permalink":"/900.Publish/「Tetra Space」/","dgPassFrontmatter":true,"noteIcon":"2"}
---


---
## ゲーム紹介
プレイヤーはキャラクターとスペースを操作できる。スペースを組み合わせることで、キャラクターが目的を果たせるような組合を構築する。

## キャラクター、カメラとコントロール（3C）
### 18×17ピクセルキャラクターのスプライトシート
![Publish TS player_Idle_strip4.png](/img/user/700.Attachments/Publish%20TS%20player_Idle_strip4.png)
- Idle

![Publish TS player_walk_strip4.png](/img/user/700.Attachments/Publish%20TS%20player_walk_strip4.png)
- Move

![Publish TS player_jump_strip6.png](/img/user/700.Attachments/Publish%20TS%20player_jump_strip6.png)
- Jump

### ロックされたランドスケープビューの中の複数の空間

### 関連された2つの操作システム
- 移動（A、D）、ジャンプ（Space）、インタラクト（E）を使用して、キャラクターを操作します。
- ドラッグ（マウスの左ボタン）または回り（マウスの右ボタン）を使用して、キャラクターを目的に到着します。

## 美術デザイン（一部）
![Publish　TSmap.jpg](/img/user/700.Attachments/Publish%E3%80%80TSmap.jpg)

## ゲームプレビュー画像
![Publish TS view(1).png](/img/user/700.Attachments/Publish%20TS%20view(1).png)
![Publish TS view(2).png](/img/user/700.Attachments/Publish%20TS%20view(2).png)
![Publish TS view(3).png](/img/user/700.Attachments/Publish%20TS%20view(3).png)

## プログラミングデザイン（一部）
### キャラクター・ステートマシン
```GML
walk_speed = 2;
jump_speed = 6.5

speed_acc = 0.9

// 移动和跳跃基本判定
hsp = 0;// horizontal speed - 水平速度
vsp = 0;// vertical speed - 垂直速度

grav = 0.3;// 重力

move_direction = 0;
face_towards = 1;

// 状态机
character_state = init_state_machine();
set_init_state(character_state,"idle");

add_state(character_state,"idle");
add_state(character_state,"walk");
add_state(character_state,"rise");
add_state(character_state,"fall");
add_state(character_state,"static");
add_state(character_state,"null");

set_player_animation();

// 状态机阶段
shoot_stage = 0;
row_stage = 0;
```

```GML
// 操作映射自定义变量
player_input();

if restart
{
	game_restart();
}

// 状态机
switch(character_state.curr_state_name)
{
	#region Idle
	case "idle" :
		#region step into state, just excute for 1 frame
		if is_enter_state(character_state)
		{
			enter_state(character_state);
			animation_set(anm_idle);
			vsp = 0;
		}
		#endregion
		
		#region State Logic
		
		if is_move() // to walk
		{
			set_next_state(character_state,"walk");
		}
		
		if is_jump() // to jump
		{
			set_next_state(character_state,"rise");
			set_jump_speed(jump_speed);
		}
		
		if not is_on_ground(col_collider)
		{
			set_next_state(character_state,"fall");
		}
		
		#endregion
		
		#region step out state, just excute for 1 frame
		if is_leave_state(character_state)
		{
			leave_state(character_state);
			// code here
		}
		#endregion
	break;
	#endregion
	
	#region Walk
	case "walk" :
		#region step into state, just excute for 1 frame
		if is_enter_state(character_state)
		{
			enter_state(character_state);
			animation_set(anm_walk);
			vsp = 0;
		}
		#endregion
		
		#region State Logic
		
		set_move_direction();
		set_x_speed(move_direction*walk_speed);
		set_face_towards();
		
				
		if is_collide_x(hsp,col_collider)
		{
			collide_horizontal(col_collider)
		}
		
		set_coordirate()
		
		if not is_move() // to idle
		{
			set_next_state(character_state,"idle");
		}
		
		if is_jump() // to rise
		{
			set_next_state(character_state,"rise");
			set_jump_speed(jump_speed);
		}
		
		if not is_on_ground(col_collider) // to fall
		{
			set_next_state(character_state,"fall");
		}
		
		#endregion
		
		#region step out state, just excute for 1 frame
		if is_leave_state(character_state)
		{
			leave_state(character_state);
			// code here
		}
		#endregion
	break;
	#endregion
	
	#region Rise
	case "rise" :
		#region step into state, just excute for 1 frame
		if is_enter_state(character_state)
		{
			enter_state(character_state);
			animation_set(anm_rise);
		}
		#endregion
		
		#region State Logic
		
		set_move_direction();
		set_face_towards();
		set_x_speed_ext(face_towards*walk_speed,speed_acc);
		
		if is_collide_x(hsp,col_collider)
		{
			collide_horizontal(col_collider)
		}
		
		if is_collide_y(vsp,col_collider)
		{
			collide_vertical(col_collider)
		}
		
		set_coordirate();
		by_gravity();
		
		if vsp >= 0 // to fall
		{
			set_next_state(character_state,"fall");
		}
		
		#endregion
		
		#region step out state, just excute for 1 frame
		if is_leave_state(character_state)
		{
			leave_state(character_state);
			// code here
		}
		#endregion
	break;
	#endregion
	
	#region Fall
	case "fall" :
		#region step into state, just excute for 1 frame
		if is_enter_state(character_state)
		{
			enter_state(character_state);
			animation_set(anm_fall);
		}
		#endregion
		
		#region State Logic
		
		set_move_direction();
		set_face_towards();
		set_x_speed_ext(face_towards*walk_speed,speed_acc);
		
		//if is_collide_x(hsp,lev_floor)
		//{
		//	collide_horizontal(lev_floor)
		//}
		
		//if is_collide_y(vsp,lev_floor)
		//{
		//	collide_vertical(lev_floor)
		//}
		collide_horizontal(col_collider)
		collide_vertical(col_collider)
		
		set_coordirate();
		by_gravity();
	
		if is_on_ground(col_collider) // to idle
		{
			set_next_state(character_state,"idle");
		}
		
		#endregion
		
		#region step out state, just excute for 1 frame
		if is_leave_state(character_state)
		{
			leave_state(character_state);
			// code here
		}
		#endregion
	break;
	#endregion
	
	#region Static
	case "static" :
		#region step into state, just excute for 1 frame
		if is_enter_state(character_state)
		{
			enter_state(character_state);
			// code here
		}
		#endregion
		
		#region State Logic
		
		
		#endregion
		
		#region step out state, just excute for 1 frame
		if is_leave_state(character_state)
		{
			leave_state(character_state);
			// code here
		}
		#endregion
	break;
	#endregion
	
	#region Null
	case "null" :
		#region step into state, just excute for 1 frame
		if is_enter_state(character_state)
		{
			enter_state(character_state);
			// code here
		}
		#endregion
		
		#region State Logic
		
		
		#endregion
		
		#region step out state, just excute for 1 frame
		if is_leave_state(character_state)
		{
			leave_state(character_state);
			// code here
		}
		#endregion
	break;
	#endregion
}

#region State Jump
if is_jump_state(character_state)
{
	jump_state(character_state)
}
#endregion


```

### スペース・ステートマシン
```GML
// 状态机
block_state = init_state_machine();
set_init_state(block_state,"Idle");

add_state(block_state,"Idle");
add_state(block_state,"Drag");
add_state(block_state,"null");

mouseOver = false

x_offset = 0
y_offset = 0

ori_x = 0
ori_y = 0

var mask = instance_create_layer(x,y,"Level_Front",env_mask)
mask.sprite_index = sprite_index
mask.image_index = 1

containInstances = ds_list_create()
instance_place_list(x,y,env_mask,containInstances,false)
instance_place_list(x,y,env_all,containInstances,false)
instance_place_list(x,y,col_door,containInstances,false)
instance_place_list(x,y,col_wall,containInstances,false)
```

```GML
// 状态机
switch(block_state.curr_state_name)
{
	#region Idle
	case "Idle" :
		#region step into state, just excute for 1 frame
		if is_enter_state(block_state)
		{
			enter_state(block_state);
			
		}
		#endregion
		
		#region State Logic
		
		if mouseOver and mouse_check_button_pressed(mb_left)
		{
			set_next_state(block_state,"Drag");
			
			ori_x = x
			ori_y = y
			
			x_offset = mouse_x - x
			y_offset = mouse_y - y
			
			//ds_list_clear(containInstances)
			
			instance_place_list(x,y,cha_player,containInstances,false)
			
			var num = ds_list_size(containInstances)
			
			for (var i = 0; i < num; ++i) 
			{
				if containInstances[| i].object_index == cha_player
				{
					set_next_state(cha_player.character_state,"static")
				}
				
				containInstances[| i].drag_x_offset = x - containInstances[| i].x
				containInstances[| i].drag_y_offset = y - containInstances[| i].y
			}
		}
		
		#endregion
		
		#region step out state, just excute for 1 frame
		if is_leave_state(block_state)
		{
			leave_state(block_state);
			// code here
		}
		#endregion
	break;
	#endregion

	#region Drag
	case "Drag" :
		#region step into state, just excute for 1 frame
		if is_enter_state(block_state)
		{
			enter_state(block_state);
			
		}
		#endregion
		
		#region State Logic
		
		
		x = mouse_x - x_offset
		y = mouse_y - y_offset
		
		if x < 0 x = 0
		if y < 0 y = 0
		if x+sprite_width > room_width x = room_width-sprite_width
		if y+sprite_height > room_height y = room_height-sprite_height
		
		var num = ds_list_size(containInstances)
		for (var i = 0; i < num; ++i) 
		{
			containInstances[| i].x = x - containInstances[| i].drag_x_offset
			containInstances[| i].y = y - containInstances[| i].drag_y_offset
		}
		
		if mouse_check_button_released(mb_left)
		{
			var target_x = (x+cell_width*0.5) div cell_width * cell_width
			var target_y = (y+cell_height*0.5) div cell_height * cell_height
			
			if place_meeting(target_x,target_y,env_space)
			{
				x = ori_x
				y = ori_y
			}
			else
			{
				x = target_x
				y = target_y
			}
			
			for (var i = 0; i < num; ++i) 
			{
				if containInstances[| i].object_index == cha_player
				{
					set_next_state(cha_player.character_state,"idle")
				}
				
				containInstances[| i].x = x - containInstances[| i].drag_x_offset
				containInstances[| i].y = y - containInstances[| i].drag_y_offset
			}
			
			var pos = ds_list_find_index(containInstances,instance_find(cha_player,0))
			ds_list_delete(containInstances,pos)
			
			mgr_terrain.setAllObstacle = true
			set_next_state(block_state,"Idle")
		}
		
		#endregion
		
		#region step out state, just excute for 1 frame
		if is_leave_state(block_state)
		{
			leave_state(block_state);
			
		}
		#endregion
	break;
	#endregion

	#region Null
	case "Null" :
		#region step into state, just excute for 1 frame
		if is_enter_state(block_state)
		{
			enter_state(block_state);
			// code here
		}
		#endregion
		
		#region State Logic
		
		
		#endregion
		
		#region step out state, just excute for 1 frame
		if is_leave_state(block_state)
		{
			leave_state(block_state);
			// code here
		}
		#endregion
	break;
	#endregion
}

#region State Jump
if is_jump_state(block_state)
{
	jump_state(block_state)
}
#endregion
```