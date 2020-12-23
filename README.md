# Snake_Game
Welcome to my snake game my final project for CS50.  I used Java to create this basic snake game.  This is a regular snakes and ladders game, where you will be facing the computer. You and the computer start at square 1, and the first one to square 36 wins, however, there will be preset squares which will be the snakes or ladders which will either boost you up or pulls you down.  I also added a special feature where you can rename your player name to whatever you want. This snake game will be presented in a GUI format to apply what I have learnt from using the MVC layout.   

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
