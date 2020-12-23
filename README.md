# Snake_Game
Welcome to my snake game my final project for CS50.  I used Java to create this basic snake game.  This is a regular snakes and ladders game, where you will be facing the computer. You and the computer start at square 1, and the first one to square 36 wins, however, there will be preset squares which will be the snakes or ladders which will either boost you up or pulls you down.  I also added a special feature where you can rename your player name to whatever you want.  

## GUI Components
I used my knowledge of object-oriented programming, hence, I use dthe MVC method to create this game.
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
