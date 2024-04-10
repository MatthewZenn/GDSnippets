# Procedural Terrain

Procedural generation is all over the place. It mostly plays a role in creating level content in rogue-like and survival games. A simple Terraria-style 2D level generator can be done as follows: Create a ```Node2D``` scene to act as our chunk generator. For every tile add a ```StaticBody2D``` with a ```CollisionShape``` and ```Sprite2D``` as it's children, with each texture being assigned to the sprite. Add additional Sprite only textures for decorations, like grass and flowers. Then add a script to the scene:
<br>
```gdscript
extends Node2D
#Called when the node enters the scene tree for the first time.
func _ready():
	generate()

func generate(): #Generates the terrain
	var textures = [$ore, $moss]
	var plants = [$plant, $tulip, $flower]
	var noise = FastNoiseLite.new()
	noise.seed = randf_range(-1000, 1000)*randf()

	for x in range(0, get_window().size.x+16, 16): #This code gnerates a noisey line of grass blocks at a determined hight.
		var k = round(noise.get_noise_1d(x)*3)*16 + 1080/1.5
		var grass_block = $grass.duplicate()
		grass_block.global_position = Vector2(x,k)
		add_child(grass_block)

		var spawn = randf() #This is used to roll a random number.
		if spawn < 0.15: #Decorations are also spawned in at random in this pass.
			var plant = plants.pick_random().duplicate()
			plant.global_position = Vector2(x,k-16)
			add_child(plant)
		elif spawn > 0.75:
			var plant = $plant.duplicate()
			plant.global_position = Vector2(x,k-16)
			add_child(plant)

		for y in range(k+16, 1080, 16): #Then every space below the grass is filled in with stone.
			var i = randf() #this is used to roll a random number.
			if i > 0.25:
				var block = $stone.duplicate()
				block.global_position = Vector2(x,y)
				add_child(block)
			else:
				var block = textures.pick_random().duplicate()
				block.global_position = Vector2(x,y)
				add_child(block)
```
I use ```FastNoiseLite``` to generate one-dimensional noise with a randomly picked seed value. The code is set up to snap the placed blocks to a 16px grid. In my code, I use arrays to randomly select decorations and special blocks. The end results looks like this:
<br>
![Terrain](images/terrian.gif)

In the above code I assigned variables random floats whenever I needed to roll a dice. But a much cleaner and more versitile solution would be to create a function that assignes a global variable:
<br>
```gdscript
var coin = 0 #Place at top of code.

func coinFlip():
	coin = randf() #randf() will generate a float value between 0 and 1.

coinFlip() #Called wherever needed.
```

The main disadvantage to this, is that it can sometimes break then things when multiple functions are using it. But for our terrain generator that only needs to run once, it works just fine.