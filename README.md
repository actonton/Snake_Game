## Table of contents
* [Snake_Game](#snake_game)
	* [GUI Components](#gui-components)
		* [Views](#views)
			* [GameWindow](#gamewindow)
			* [NameWindow](#namewindow)
			* [BoardPanel](#boardpanel)
		* [Controls](#controls)
			* [PlayPanel](#playpanel)
			* [ControlPanel](#controlpanel)
		* [Models](#models)

# Snake_Game
Welcome to my snake game my final project for CS50.  I used Java to create this basic snake game.  This is a regular snakes and ladders game, where you will be facing the computer. You and the computer start at square 1, and the first one to square 36 wins, however, there will be preset squares which will be the snakes or ladders which will either boost you up or pulls you down.  I also added a special feature where you can rename your player name to whatever you want. This snake game will be presented in a GUI format to apply what I have learnt from using the MVC framework.   

## GUI Components
I used my knowledge of object-oriented programming, hence, I use dthe MVC framework to create this game.
### Views

#### GameWindow
The GameWindow class is essential the opening window of my snakes and ladders game.   
``` java
import model.*;
import java.awt.*;
import javax.swing.*;

public class GameWindow extends JFrame
{
    public GameWindow(Game game)
    {   
        setup();
        build(game);
        setVisible(true);   
    }
    private void setup()
    {   
        setSize(650, 590);
        setDefaultCloseOperation(EXIT_ON_CLOSE);    
    } 
    private void build(Game game)
    {   
        add(new PlayPanel(game)); 
    }
}
```

#### NameWindow
The NameWindow class is the main window for the customisation of the player name.  
``` java
import model.*;
import java.awt.*;
import java.awt.event.*;
import javax.swing.*;

public class NameWindow extends JFrame implements MyObserver
{   
    private Game game;
    private JTextField name = new JTextField(10);
    private JButton button = new JButton("Ok");
    private JLabel confirm = new JLabel("");
    private JPanel panel = new JPanel();
    public NameWindow(Game game)
    {  
        this.game = game;
        game.attach(this);
        setup();
        build(game);
        setVisible(true);   
    }
    private void setup()
    {   
        setSize(200, 200);
        setDefaultCloseOperation(HIDE_ON_CLOSE);    
    }     
    private void build(Game game)
    {   
         panel.add(new JLabel("Enter player name"));
         
         name.setText(game.player().getName());
         panel.add(name);
         panel.add(button);
         panel.add(confirm);
         button.addActionListener(new AddListener(game));
         add(panel);
    }
    public void update()
    {           
        confirm.setForeground(Color.blue);
        confirm.setText("Name updated");
    }
    public class AddListener implements ActionListener
    {   
       private Game game;
        public AddListener(Game game)
        {   
          this.game = game;
        }
        public void actionPerformed(ActionEvent e)
        {   
            
                game.player().setName(name.getText());
                update();
        }
    }
}

```

#### BoardPanel
The BoardPanel class is essential the class that applies the graphics of the game.

``` java
import model.*;
import java.awt.*;
import javax.swing.*;

public class BoardPanel extends JPanel implements MyObserver
{   
    
    private Game game;
    
    public BoardPanel(Game game)
    {   
        this.game = game;
        game.attach(this);
        game.player().attach(this);
        game.computer().attach(this);
        setup();
        build();
    }
        
    private void setup()
    {   
        setBorder(BorderFactory.createLineBorder(Color.blue)); 
        setBackground(Color.white);
        setPreferredSize(new Dimension(500, 500));
        
    }

    private void build()
    {   
        
    }

  /** paintComponent - overrides parent method
   * redraws all the images on screen
   */
  protected void paintComponent(Graphics g)
  {
        super.paintComponent(g);
        
        Image im = Toolkit.getDefaultToolkit().getImage("board.jpg");    
        g.drawImage(im, 0,0,this.getWidth(),this.getHeight(),this);
        im = Toolkit.getDefaultToolkit().getImage("topHat.jpg");//computer image
        int position = game.computer().getScore();
        g.drawImage(im,getX(position),getY(position),50,50,this);
        im = Toolkit.getDefaultToolkit().getImage("car.jpg");//computer image
        position = game.player().getScore();
        g.drawImage(im,getX(position),getY(position),50,50,this);
  }    
  public int getX(int position)
  {
      int result;
      switch (position)
      {
          case 0:
          case 1: 
          case 12:
          case 13:
          case 24:
          case 25:
          case 36:
              result = 50;
               break;
          case 2: 
          case 11:
          case 14:
          case 23:
          case 26:
          case 35:
              result = 150;
              break;
          case 3:
          case 10:
          case 15:
          case 22:
          case 27:
          case 34:
              result = 250;
              break;
          case 4: 
          case 9:
          case 16:
          case 21:
          case 28:
          case 33:
              result = 350;
              break;
           case 5:
           case 8:
           case 17:
           case 20:
           case 29:
           case 32:
                result = 450;
                break;
          default: 
              result = 550;
              break;
      }
      return result;
    }
  public int getY(int position)
  {
      int result;
      switch (position)
      {
          case 0:
          case 1:
          case 2:
          case 3:
          case 4:
          case 5:
          case 6:
                result = 400;
                break;
          case 7:
          case 8:
          case 9:
          case 10:
          case 11:
          case 12:
                result = 325;
                break;
          case 13:
          case 14:
          case 15:
          case 16:
          case 17:
          case 18:
                result = 250;
                break;
          case 19:
          case 20:
          case 21:
          case 22:
          case 23:
          case 24:
                result = 175;
                break;
          case 25:
          case 26:
          case 27:
          case 28:
          case 29:
          case 30:
                result = 100;
                break;
          default: 
              result = 25;
              break;
      }
      return result;
  }
    
    public void update()
    {   
       repaint();
    }


}

``` 

### Controls

#### PlayPanel
The PlayPanel class is a controller which the player can start playing the game.  It will apply grid constraints and places the payer icons on square 1 when the game is loaded.

``` java
import java.awt.*;
import javax.swing.*;
import java.awt.event.*;
import java.awt.*;
import model.*;

public class PlayPanel extends JPanel implements MyObserver
{
    private GridBagLayout bag = new GridBagLayout();
    private Game game;
    private PlayListener playListener;
    private ControlPanel controlPanel;
    private BoardPanel boardPanel;

    public PlayPanel(Game game)
    {   
        setup();
        this.game = game;
        game.player().attach(this);
        game.computer().attach(this);
        build(game);   
    }

    public void setup()
    {   
        setLayout(bag);    
    }

    public void build(Game game)
    {
        playListener = new PlayListener();
        boardPanel = new BoardPanel(game);
        controlPanel = new ControlPanel(game, playListener);
        add(boardPanel);
        place(boardPanel, 1, 1, 0, 0, 1, 3);
        add(controlPanel);
        place(controlPanel, 1, 1, 0, 1, 1, 1);
    }
    public void update()
    {
        boardPanel.repaint();//refresh positions of the player icons
    }
    private void place(Component comp, int w, int h, int x, int y, double wx, double wy)
    {
        GridBagConstraints cons = new GridBagConstraints();
        cons.gridwidth = w;
        cons.gridheight = h;
        cons.gridx = x;
        cons.gridy = y;
        cons.weightx = wx;
        cons.weighty = wy;
        cons.fill = GridBagConstraints.BOTH;
        bag.setConstraints(comp, cons);
    }
    private class PlayListener implements ActionListener
    {
        public void actionPerformed(ActionEvent e)
        {
            if (!game.end())
                game.play();
        }
    }
}
```

#### ControlPanel
The ControlPanel class are for the button 'Change Player Name' so it has an added functionality to open the NameWindow for the player to change their name.  

``` java
import model.*;
import java.awt.*;
import java.awt.event.*;
import javax.swing.*;

public class ControlPanel extends JPanel implements MyObserver
{
    private JButton name = new JButton("Change Player Name");
    private JLabel position = new JLabel("Position");
    private JTextField computer = new JTextField(5);
    private JTextField player = new JTextField(5);
    private JLabel computerName = new JLabel();
    private JLabel playerName = new JLabel();
    private JButton play = new JButton("Play");
    private Game game;
    
    public ControlPanel(Game game, ActionListener listener)
    {   
        this.game = game;
        game.attach(this);
        game.player().attach(this);
        game.computer().attach(this);
        setup(listener);
        build();
    }
        
    private void setup(ActionListener listener)
    {   
        setBorder(BorderFactory.createLineBorder(Color.blue));
        play.addActionListener(listener);   
        name.addActionListener(new NameListener());
    }
    private void build()
    {   
        add(name);
        add(position);
        computerName.setText(game.computer().getName());
        add(computerName);
        add(computer);
        playerName.setText(game.player().getName());
        add(playerName);
        add(player);
        add(play);
    }
    public void update()
    {           
        playerName.setText(game.player().getName());
        player.setText(Integer.toString(game.player().getScore()));
        computer.setText(Integer.toString(game.computer().getScore()));
    }
     
    private class NameListener implements ActionListener
    {   
        public void actionPerformed(ActionEvent e)
        {   
            NameWindow nameWindow = new NameWindow(game);
        }
    }

}
```
### Models
The models consists of Player, Game, Board, Layout, LadderSquare, SnakeSquare, Square, Dice, Updater, and Myobserver.  All of these calsses represents an object or element carrying data. The Updater and MyObserver classes are the logic to update controller if its data changes. 

#### Player
``` java
package model;


/**
 * Write a description of class Player here.
 */
public class Player extends Updater
{
   private int score = 0;
   private String name;

    /**
     * Constructor for objects of class Player
     */
    public Player(String name)
    {
       this.name = name;
    }
    /**
     * play - roll the dice and increment the score
     */
    public void play(int number)
    {
        System.out.println(name + " rolled a " + number);
        move(number);
    }   
    /**
     * move - move the player position
     */
    public void move(int number)
    {
        score += number;
        updateViews();
    }
    /**
     * getScore - returns the value of the score attribute
     */
    public int getScore()
    {
        return score;
    }
    /**
     * getName - returns the value of the name attribute
     */
    public String getName()
    {
        return name;
    }
    /**
     * setName - set the value of the name attribute
     */
    public void setName(String name)
    {
        this.name = name;
        updateViews();
    }
    /**
     * toString - returns the value of the attributes as a String
     */
    public String toString()
    {
        return name + " " + score;
    }
}

```
#### Game
``` java
package model;

import java.util.*;
/**
 * Write a description of class Game here.
 * 
 */
public class Game extends Updater
{
    private Player player = new Player("Player");
    private Player computer = new Player("Computer");
    private Dice dice = new Dice();
    private Board board = new Board();
    /**
     * Constructor for objects of class Game
     */
    public Game()
    {
    }
    
    /**
     * end - determines if the game is over
     */
    public boolean end()
    {
        int HIGHEST_SCORE = 36;
        return (player.getScore() >= HIGHEST_SCORE || computer.getScore() >= HIGHEST_SCORE);
    }
    /**
     * play - each player has their turn
     */
    public void play()
    {
        play(player);
        play(computer);
    }
    /**
     * play - each player has their turn
     */
    private void play(Player player)
    {
        player.play(dice.roll());
        board.move(player);
    }
    public Player player()
    {
        return player;
    }
    public Player computer()
    {
        return computer;
    }
    /**
     * show - display the data for each player
     */
    public void show()
    {
        String s = "    Player Score: " + player.toString();
        s+="\n    Computer Score: " + computer.toString();
        System.out.println(s);
    }
    
}

```
#### Board
``` java
package model;
import java.util.*;

public class Board extends Updater
{
    private LinkedList<Square> squares = new LinkedList<Square>();
    private int counter = 0;
    /**
     * Constructor for objects of class Board
     */
    public Board()
    {
        setBoard();
    }
    private void setBoard()
    {
        for(Layout layout: Layout.values())//for all 36 squares
        {
            squares.add(createSquare(++counter, layout));
        }
    }
    private Square createSquare(int number, Layout layout)//factory
    {
        if(layout.getType() == 0)
            return new Square(number, getColour(number));
        else if(layout.getType() == 1)
            return new LadderSquare(number, getColour(number), layout.getSpaces());
        else//type is 2
            return new SnakeSquare(number, getColour(number), layout.getSpaces());
    }

    private String getColour(int counter)
    {
        if(counter % 2 == 0)// if counter is even
            return "Aqua";
        return "Yellow";
    }
    public void move(Player player)
    {
        int score = player.getScore();
        if (score < squares.size())
        {
            Square square = squares.get(score -1);//find the square the player is on
            square.move(player);//move player (if snake or ladder)
        }
    }
}

```
#### Layout
``` java
package model;

public enum Layout
{
    One(0,0), Two(0,0), Three(1,13), Four(0,0), Five(1,2), Six(0,0),
    Seven(0,0), Eight(0,0), Nine(0,0), Ten(0,0), Eleven(0,0), Twelve(2,10),
    Thirteen(0,0), Forteen(2,3), Fifteen(1,10), Sixteen(0,0), Seventeen(2,13), Eighteen(1, 2),
    Nineteen(0,0), Twenty(0,0), TwentyOne(1,11), TwentyTwo(0,0), TwentyThree(0,0), TwentyFour(0,0),
    TwentyFive(0,0), TwentySix(0,0), TwentySeven(0,0), TwentyEight(0,0), TwentyNine(0,0), Thirty(0,0),
    ThirtyOne(2, 12), ThirtyTwo(0,0), ThirtyThree(0,0), ThirtyFour(0,0), ThirtyFive(2, 13), ThirtySix(0,0);
    private int type; //0 for Square, 1 for Ladder 2 for Snake
    private int spaces;
    private Layout(int type, int spaces)
    {
        this.type = type;
        this.spaces = spaces;
    }
    
    public int getType()
    {
        return type;
    }
    
    public int getSpaces()
    {
        return spaces;
    }
}
```
#### LadderSquare
``` java
package model;

public class LadderSquare extends Square
{
    public LadderSquare(int id, String colour, int spacesToMove)
    {
        super(id, colour);
        super.spacesToMove = spacesToMove;
    }
    
    public void move(Player player)
    {
        player.move(spacesToMove);
        System.out.println("Ladder! Move forward " + spacesToMove + " spaces");
        super.move(player);
    }
}
```
#### SnakeSquare
``` java
package model;

public class SnakeSquare extends Square
{
    public SnakeSquare(int id, String colour, int spacesToMove)
    {
        super(id, colour);
        super.spacesToMove = spacesToMove;
    }
    
    public void move(Player player)
    {
        player.move(-spacesToMove);
        System.out.println("Snake! Move backward " + spacesToMove + " spaces");
        super.move(player);
    }
}
```
#### Square
``` java
package model;

public class Square
{
    protected int id;
    protected String colour;
    protected int spacesToMove = 0;
    
    public Square(int id, String colour)
    {
        this.id = id;
        this.colour = colour;
    }
    
    public void move(Player player)
    {
        System.out.println(player.toString());
    }
}
```
#### Dice
``` java
package model;

public class Dice extends Updater
{
    private final int SIDES = 6;
    /**
     * Constructor for objects of class Dice
     */
    public Dice()
    {
    }
    /**
     * roll - rolls the dice 
     */
    public int roll()
    {
	 return (int) ((Math.random()*SIDES)+1); 
    }
}

```
#### Updater
``` java
package model;

import java.util.*;

public class Updater 
{   
    private LinkedList<MyObserver> views = new LinkedList<MyObserver>();
    
    public void attach(MyObserver o)
    {   
        if (views == null)
            views = new LinkedList<MyObserver>();
        views.add(o);   
    } 
    public void detach(MyObserver o)
    {   
        views.remove(o);   
    }             
    public void updateViews()
    {   
        if(views != null)
        {
            Iterator<MyObserver> it = views.iterator();
            while (it.hasNext())
                it.next().update();
        }
    }
}

```
#### MyObserver
``` java
package model;


abstract public interface MyObserver
{   
    abstract void update(); 
}

```
