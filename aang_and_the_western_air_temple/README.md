# Make Project

**DUE May 5th at 11:59 PM**

You will develop your own 8-bit game for the final project. You can
use TIC-80, NES, or the VCS. You can work alone or with a partner. You
can create an original game, or mash-up existing games, but your game
should be more than simply a remake; be creative!

One of the key outcomes of this assignment is to reflect on the design
of the game from a software point of view. Use some of the game
programming patterns we covered in class. 


## Learning Objectives

- develop a novel video game;
- more practice with Lua (C or 6502-ASM);
- employ programming patterns;
- think and write critically about games.

## Deliverable

Submit your make study on GitHub (preferably as a markdown document,
but if not, as a PDF), specifically reflecting on the design of your
game and which patterns you employed (or tried to employ). Your
TIC-80 game should be exported to HTML like we did in the earlier
TIC-80 assignment. Your write-up should be clear and look nice.  You
will present your work during the final exam period.

# Make Project: The Last Airbender

## Inspiration

Avatar: The Last Airbender is an immensely popular animated television series aired on Nickelodeon in the mid 2000’s. It quickly gained immense popularity due to its intricate world-building (which it drew from many past works), its compelling characters, and engaging storyline. With a blend of humor, action, and drama, the show appeals to both children and adults alike. We are both fans of the show (Ken in particular), which served as motivation for making a video game based on its story. In addition, the main character, Aang, has a variety of elemental abilities that lend him many tools for solving problems. This translates well into the realm of game design as it provides a character to the player with a variety of possible actions, emphasizing player choice in approaching situations within the game. In addition the show’s world is endlessly engaging and provides space for many imagined stories, including the storyline of our game. 

Beyond our personal interest in the show, Avatar: The Last Airbender has a rich history of cultural significance. The show is heavily influenced by Asian-Pacific culture, drawing from Japanese, Chinese, Buddhist, and Innuit traditions. Sadly, we were not able to recreate all of this in the game, such as the stunning set of martial arts used by the artists to convey character traits. The creators made a conscious effort to ensure that their portrayal of these cultures was respectful and authentic, and as a result, the show has been praised for its positive representation of diverse cultures. In addition, it has many messages involving environmentalism, anti-imperialism, and anti-war. Its themes of compassion, forgiveness, and redemption have also resonated with viewers, making it a beloved and impactful series. We try to carry these themes into our story. 

You play as Aang. The Western Air Temple has been overrun by normally-benevolent spirits, hungry for the cosmic energy of the Avatar. You must navigate the temple and relight all 31 Spirit Lanterns by hand with your firebending. In addition, you can satiate spirits by hitting them with fire (X button), or slow them with water (S button). In addition you can levitate with your air bending (A button). This is useful, as if you get too close to an unsatiated spirit, it will drain you (you will die and respawn at the last Spirit Lantern). You can only take so many drainings (5 lives) before you must rest and attempt to re-light the Lanterns (you will respawn at the beginning of the level). However, particularly in areas where there are many spirits, you can bend a shield around yourself (Z Button) to temporarily prevent spirits from draining you. 
	
This fits well into Avatar: The Last Airbender as the Avatar is expected to be the bridge between the human world and the spirit world. In addition, though mechanically interesting, this story is distinctly non-violent, attempting to achieve harmony with the spirits, rather than banish them. 

## Game Mechanics and Design Patterns

Our game uses a variety of different design patterns to handle a range of problems, so the remainder of this essay will be a technical explanation of the games functionality and design principles, followed by a brief tutorial. 
The game runs on a **Game Loop**, whose main function is taking in player input and moving enemies. The spirits are stored in an indexed table with a pointer to its first and last elements. The maximum pointer increments as more spirits are added to the game, and the minimum pointer increments as they are removed from the game. Similarly, fire and water objects are also stored in such lists. Fire and water sprites disappear when they leave the screen, at which point they stop being updated or detected for collisions to free up computational resources. 

The spirits however are computationally intensive, and impossible to simulate across the map. Thus, we represent different sections of the level with a **State** variable (called state). The states divide the game world up, and when you are in a given state, it runs its simulation, and importantly, works to detect if you are leaving that zone and entering an adjacent state. Then we run an **Update Method** on the game world, which clears the sprites from the previous state and spawns new sprites in the new area. It spawns 8 sprites within a range of each unlit spirit lantern. These spirit lantern locations are stored in a list, and to save space and code requirements we work with **Lazy Initialization** to determine where to spawn spirits, as for each spirit lantern, it reads the background map to see if it has been lit yet, rather than store booleans for every lantern. Each state had to be handwritten, and therefore there was some significant overlap. 
	
To save time during code writing, and space during execution, we used Lua’s **Prototype** feature. All **State** objects contain only their distinct fields, the rest being stored in a “state0” object. This makes it less likely we mistype something and get a bug, and would allow for state scaling. In a procedurally generated game (which we briefly considered) it would also allow for a lot more states and thus a larger world. Thus in a sense we are also using **Flyweight** storage. Thus the game loop: take inputs, move sprites, detect collisions, detect state changes, render, is not overly complex but still has interesting challenges requiring useful design patterns. 

## Game

The following is a brief tutorial and explanation of the more difficult mechanics. 

Use the arrow keys to navigate through the world, using A to fly. Be careful when flying to avoid the spirits floating in the air, even close to the ground. Hopping and giving them fire energy will calm them. Your other elemental abilities work as follows: Z gives you a three second earth shield. This is very important later in the game to drop into areas full of spirits. X shoots out bits of fire that feed spirits, pacifying them. Water calms them so that they stop moving, but will still drain you if you get too close. In addition, note that if you are standing at a lit Spirit Lantern, you will not be drained, making them safe havens, even in seas of spirits. This is also useful as you must watch your ‘element meters’ in the bottom left. Should you run out of air power, your flight will stutter. Spam X or S, and you will run out of fire and water, and be left with no way to calm the spirits. Be drained, and you respawn at the last spirit lantern. 

Navigate through the world, and light all the spirit lanterns. Should you later return to an area with lit lanterns, the spirits will no longer be there. Here are some other tips. At the surface level, be careful not to fly off the top of the screen. This will result in losing a life. Use the beginning, without spirits, to practice the flow of using your elements. 

Note: (Should you come to a new area with unlit lanterns and no spirits, try leaving where you came from, and re-entering. This may mean you accidentally did not pass over the barrier required to trigger spirit spawning. This may occur as you move from the surface down below the cliffs. It is best to stay near the platforms of the Air Temple). 

