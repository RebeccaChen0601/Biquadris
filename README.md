# Biquadris
A two-player Tetris game

Introduction:

Biquadris is a two player competition version of the game Tetris. It consists of a two boards, with each player takes turns to manipulate a block. If a row is fulfilled, the row can be eliminated. When the block of one the of board is unable to fill, the corresponding player lose. Score will be calculated according to the number of lines and blocks eliminated.


Overview:

The structure of our Biquadris follows the Model View Controller Architectural Pattern. 
    Model:
        Defines data structures. Models update changes to View. 
        
            BoardManager(Player):    
            Model for the state of players. Manipulated by the Controller classes, it controls the movement of Block class and the       
            corresponding result including elimination of the rows of Cells, the Score class update, and the triggered special    
            actions
			
            Board: 
            Model for vector of vector of Cell.  
			
			Cell:
			Model for a single square on the Board, using Observer pattern to update changes to the View class

			Block: 
			Model for a four-cell object that implements a transformation matrix and a rotation matrix to process Block movements

			Score:
 			Model for the scoreboard that calculates the score of each player, updates the high score, and determine the winner of each 
			round.

			Level:
 			Model(superclass) for the different levels. 
			
	View:
	Defines display(UI) using View class observed from Model classes.
	View produce the display and interact with the user. It serves two purposes: using text to display or using an Xwindow to do 
	graphical display. The function fillcell, fillstring, and unfillstring is a wrapper for the function in Xwindow, through making it a 
	wrapper, we can have a safeguard inside the function, to make sure the Xwindow is defined before we draw to the screen. It is also
	easier for other class to call the function.  The View class is an observer of the Cell class.  After the Cell has been set or 
	unset, it calls a function inside view which modifies the Xwindow. This way, each class has achieved single responsibility. 
	
	Controller:
	We used a Controller and a KeyController class (extra feature) to manipulate the model class BoardManager according to user input.
	The method runGame is responsible for start running the game. Then it interprets user input commands such as “3lef”, and “0ri” and 
	calls the appropriate methods in boardManager class. Other methods are private. The resetGame() method is used to reset the Model
	classes after each game ends.  The askSpecialAction() method is used to handle user inputs for what special action to use when it
	occurs. This also demonstrates MVC principal as all parts dealing with user inputs are dealt with in the Controller class. The
	restartGame() and endGame() are responsible for applying the respectively changes to Model classes during restart and quit process.

Design:
	In the design of the project, we used several techniques to solve design challenges:
	
	Observer Pattern: 
	The Observer pattern plays a key role in the Model-View-Controller architectural pattern. The Cell and Board classes are Subjects,
	and the View class is the Observer. When a subject changes state, View class is notified and updated make make changes to the
	screen.Each cell inside the board is also an observer to the block class. After the block has shifted, it will notify the 
	corresponding cell to set and unset to store the right state of the block.
	
	Factory Pattern: 
	We defined a separate Level Class which is an abstract class to create the Block object. In our case, we create a char that 
	represents the Block type in order to avoid the return of pointers. There are different concrete subclasses such as Level0, Level1… 
	Those classes implemented their own logic or randomness of creating new Blocks so that it is flexible if new levels need to be
	added.

	Polymorphism:
	The Block class and the Level class use a single interface to entitle to different types. Apply dynamic dispatch that determines the
	type of the object at runtime with the keywords virtual and override on these two classes.
	
	Single Responsibility Principle:
	Our codes are well designed so that every class has responsibility over a single part of the functionality in the project. Block 
	class only handle its own transformation without knowing how how and when it is generated or how view is displayed.

	RAII Idiom:
	We used unique_pointer, vector, and map in the BoardManager(Player) class so that even if something throw, the program won’t crash 
	because of memory leak. There is no delete in our code.
	
	Reducing compilation dependencies:
	We forward declared the View class to the Cell, NextBlock, Board classes to void compilation dependencies(cycles). 

	Low Coupling & High Cohesion:
	The degree of module dependencies is low. No friend class and no public fields are used. In addition, with Block responsible for 
	Block movements, Board and Cell(as containers) responsible for notifying the view to update, BoardManager responsible for 
	manipulating the Blocks, Score responsible for calculating scores, Level responsible for creating different Blocks... Each class is 
	responsible for one task to realize high cohesion.
	
	MVC Architectural Pattern: 
	The users will use the Controller class to input commands that manipulate the Model classes, including the BoardManager(Player), 
	Block, Board, Cell, Level, and Score. The Model classes handle the data and update the View class, which presents the Models’ data
	to the user.

Resilience to Change:

	Abstract classes: Level
	The level class is designed as an abstract virtual class. There are currently 5 level subclasses, level 0 to 4 in the game. This 
	allows the possibility to add more potential level subclasses which demonstrates scalability.  
	
	Inheritance hierarchy: Block
	The block class is designed as an abstract virtual class.  Each type of block is defined in its own subclass. If we want to add 
	another type of block, we can create another block subclass for this type of block. 

	Separate Controller class
	The controller class is responsible for handling user input and set up the game such as initializing the BoardManager class and the 
	Score class.  The runGame method in the Controller is responsible for interpreting user inputs and call methods in the BoardManager
	class to apply changes. If we need to add a new command, we would add one more condition to the runGame method to interpret that
	command. In addition, if we want to add a brand new input system such as listening to Keyboard directly instead of interpreting text
	commands, we can add a new method in the controller class to handle it.

	Separate View class
	The main responsibility of the view class is to provide text display and graphical display that interact with the user. Through 
	separating the view class, we can easily add another display if we wish to. Since the View class's only responsibility is to produce
	output, it only has two fields, a boolean to indicate whether we use graphical display or not, and a pointer to an Xwindow if we 
	wish to have a graphical display. In other class, we have a function that notifies the view class if something has changed, 
	therefore we can minimize the number of times we redraw to the screen.
	In this case, the View class is purely an observer to other class.

Extra Credit Features:

	Keyboard Listener:
	We implemented a KeyController class which extends the functionality of the Controller class with a minimum recompilation on the 
	existing classes(it only needs to recompile the main class). The keyboard mapping will be shown below:
		“J” for left
		“K” for down
		“L” for right
		“I” for Clockwise rotation
		“U” for CounterClockwise rotation
		“Space” for drop
		“R” for reset
		“E” for exit
		“S” for skip
		“+” for levelup
		“-” for leveldown
		“H” for heavy
		“B” for blind
		To implement the KeyController functionality, we modify the XWindow class by adding a pollEvent function which takes in the 
		KeyPress event from the user and returns the keycode. The KeyController Class listens to events triggered by the XWindow and 
		executes the appropriate reaction to these events. 

	Skip a block feature:
	A new command “skip” is added to skip a block. The user can enter as minimal as “sk” to issue this command. The command skips the 
	current block and pops up the next block. However, score points are deducted by the ( current level add 5). 

	ScoreBoard/highScore:
	The graphical display and the text display of the game will show who won the game and each player’s respective high score when the 
	game finishes and the player chooses to exit.
	
