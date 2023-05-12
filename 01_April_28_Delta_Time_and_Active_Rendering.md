## Game Loop

In most games, the main loop often consists of 2 methods: `update` and `draw`

The game loop will continuously call these two functions: `update` and `draw`. During the `update` function, the data of all game objects are updated, whereas in the `draw` function, the objects are rendered to the screen. These functions are called at a high frequency, allowing for a seamless animation of the game's progress.

### Processing

In Processing, they already have a hidden game loop. Your `void draw()` function will be called automatically 60 times per second (by default) after `void setup()` is called.

```java
void setup() {
    size(400, 300);
}

void draw() {
    // ...
}
```

### Java

In Java (awt), they use <u>passive rendering</u> by default. The *"render function"* will only be called when there is an event happening, such as mouse moving and mouse click. In order to make your game run smoothly, you need to setup an <u>active rendering</u> system on your own. Like this:

```java
import javax.swing.*;
import java.awt.*;
import java.awt.image.*;

public class Game extends JFrame {

    private final BufferStrategy bufferStrategy;
    private final Canvas canvas;
    private boolean running = false;
    public int width;
    public int height;

    Game() {
        super();
        // Configuring Window
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setTitle("Game Window");
        setIgnoreRepaint(true); // Disable auto repaint so that we can handle rendering ourselves

        // Configure the canvas and add to the window
        canvas = new Canvas();
        canvas.setIgnoreRepaint(true);
        canvas.setSize(600, 400);
        add(canvas);
        pack();

        // Double buffering
        canvas.createBufferStrategy(2);
        bufferStrategy = canvas.getBufferStrategy();

        width = canvas.getWidth();
        height = canvas.getHeight();
    }

    /**
     * Gets called to render object to the screen
     * @param g graphics to render
     * @param dt delta time between current frame and previous frame in seconds
     * @param now time since window launched in seconds 
     */
    public void onDraw(Graphics2D g, double dt, double now) {
        // ...
    }
    
    /**
     * Gets called to update
     * @param now current time in seconds
     * @param dt delta time between this update and previous update
     */
	public void onUpdate(double now, double dt) {
        // ...
    }
    
    /**
     * Start the game loop
     */
    public final void mainLoop() {
        if (running) { // Check if this method was called twice
            throw new IllegalStateException("The game loop is already running!");
        }

        double launchTime = System.nanoTime() / 1e9d; // Get current time and convert to seconds
        double lastTickTime = 0;

        running = true;
        setVisible(true);
        while (running) { // This is "the hidden loop of Processing"
            // Calculate delta time between current frame and previous frame
            double now = System.nanoTime() / 1e9d - launchTime; // Get time since launch in seconds
            double dt = now - lastTickTime;
            lastTickTime = now;

            // Update width and height values
            width = canvas.getWidth();
            height = canvas.getHeight();
            
            // Update game's data
            onUpdate(now, dt); // Always update no matter if the context is lost or not

            // Render
            if (!bufferStrategy.contentsLost()){ // Only render when the context is not lost
            	Graphics2D g = (Graphics2D) bufferStrategy.getDrawGraphics(); // Get draw graphics
                onDraw(g, dt, now); // Render to the buffer
                bufferStrategy.show(); // Swap buffers (display the rendered result)
            }
        }
    }
}
```

Notice that `bufferStrategy` is used. This is for *double buffering*. Basically, when we draw objects onto the screen, we don't want it to appear on the screen one by one. We want all objects to be drawn and appear instantly on the screen at the same time. To do that, we need to create double buffer. Therefore, we can render the scene on to one buffer first, then swap it to it show onto the screen (`bufferStrategy.show()`), and then render the second frame to the other buffer, and call the swap function again (`bufferStrategy.show()`) to swap the second buffer with the first one.

<iframe src="01_April_28_Delta_Time_and_Active_Rendering_swap_buffers.html" width="400" height="400"></iframe>

---

## Delta Time

In order to make games run smoothly and be stable, we need to include delta time in the `update` function. Delta time can prevent your game's physics simulation from breaking or cause unfairness due to performance problems (ex. unstable framerates, low framerates on low-end PCs). It can also make your code somewhat make more sense.

I'll be using the following Processing template for demonstration.

> Since Processing only has `draw`, I'll create two more functions to separate update and render tasks

```java
void setup() {
  size(500, 400);
  onStart();
}

long lastUpdateTime = millis();

void draw() {
  // Calcuate delta time
  long now = millis();
  long dt = now - lastUpdateTime;
  lastUpdateTime = now;
  
  onUpdate(now / 1000.0f, dt / 1000.0f);
  onRender();
  
}

void onStart() {
  
}

void onUpdate(float now, float dt) {

}

void onRender() {
  
}
```

 Let's use a moving ball as an example.

```java
// Moving ball without delta time

float x = 0; // x-coordinate
float v = 50; // speed

void onUpdate(float now, float dt) {
    x += v;
    if (x > width) {
        x = 0;
    }
}

void onRender() {
    background(255);
    fill(255);
    circle(x, height / 2, 32);
}
```

The Result will be somewhat like this:

<iframe src="01_April_28_Delta_Time_and_Active_Rendering_no_delta_time.html" width="400" height="200"> </iframe>

As you can see, without delta time, different framerate (or tick rate) will affect how fast the ball moves.

Below is an example with delta time.

```java
// Moving ball with delta time

float x = 0;
float v = 50; // speed (pixels/second)

void onUpdate(float now, float dt) {
    x += v * dt;
    if (x > width) {
        x = 0;
    }
}

void onRender() {
    background(255);
    fill(255);
    circle(x, height / 2, 32);
}
```

<iframe src="01_April_28_Delta_Time_and_Active_Rendering_with_deltatime.html" width="400" height="200"> </iframe>

With delta time, the object moves at a constant velocity regardless of the system's performance.
