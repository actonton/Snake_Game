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
