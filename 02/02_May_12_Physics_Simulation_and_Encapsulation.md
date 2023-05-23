## Simulate Physics

I will continue using the template from the first lesson.

```java
void onStart() {
}

void onUpdate(float now, float dt) {
}

void onRender() {
}
```

### Bouncing ball example

Every physical object has a few properties. A bouncing ball has at least a y-coordinate, and a y-velocity. 

```java
float y = 0;
float vy = 0;

void onStart() {
    y = height / 2; // Set ball y-coordinate to the center of the screen
}

void onRender() {
    background(0);
    ellipse(width / 2, y, 20, 20); // Render the ball
}

void onUpdate(float now, float dt) {
    y += vy * dt;
    vy += 1000 * dt;
}
```

<iframe src='02_May_12_Physics_Simulation_and_Encapsulation_bouncing_ball.html' width="400" height="400"> </iframe>

---

Now, let's add more physics properties (x-coordinate, x-velocity), and make it bounce off walls.

```java
float y = 0, x = 0;
float vx = 0, vy = 0;

void onStart() {
    y = height / 2; // Set ball y-coordinate to the center of the screen
    x = height / 2; // Set ball x-coordinate to the center of the screen
    
    vx = (float) Math.random() * 1000 - 500; // Set x-velocity to a random number between -500 and 500
    vy = (float) Math.random() * 200 - 100; // Set y-velocity to random number between -100 and 100
}

void onRender() {
    background(0);
    ellipse(x, y, 20, 20); // Render the ball
}

void onUpdate(float now, float dt) {
    y += vy * dt; // Change y-coordinate by y-velocity
    x += vx * dt; // Change x-coordinate by x-velocity
    vy += 1000 * dt; // Simulate gravity (1000px/s^2)
    
    // Coordinate will be set to the border and velocity will be inverted using abs() to prevent overshooting
    if (vy > height) { // Inverse y-velocity and prevent overshoot
      y = height;
      vy = -abs(vy);
    }
    if (x < 0) { // Inverse x-velocity and prevent overshoot
        x = 0;
        vx = abs(vx);
    }
    if (x > width) { // Inverse x-velocity and prevent overshoot
        x = width;
        vx = -abs(vx);
    }   
}
```

<iframe src='02_May_12_Physics_Simulation_and_Encapsulation_bouncing_ball_full.html' width="400" height="400"> </iframe>

---

**Encapsulate**

We can encapsulate all the physics properties into a class. (Move all the code into the class)

```java
class Ball {
    float x = 0, y = 0;
    float vx = 0, vy = 0;
    
    Ball() { // Constructor: this will be called when an instance of this class is created
        // Move the ball to the center of the screen
        this.x = width / 2;
        this.y = height / 2;
    
    	this.vx = (float) Math.random() * 1000 - 500; // Randomize x-velocity
    	this.vy = (float) Math.random() * 200 - 100; // Randomize y-velocity
    }
    
    void onUpdate(float dt) {
        this.x += this.vx * dt; // Move x
        this.y += this.vy * dt; // Move y
        this.vy += 1000 * dt; // Simulate gravity
        
        if (vy > height) {
          y = height;
          vy = -abs(vy);
        }
        if (x < 0) {
            x = 0;
            vx = abs(vx);
        }
        if (x > width) {
            x = width;
            vx = -abs(vx);
        }   
    }
    
    void onRender() {
        ellipse(x, y, 20, 20); // Render the ball
    }
}
```
Now, to add a ball to the game, just simply create a instance of the ball and call its `onRender` and `onUpdate` methods.

```java
Ball ball;

void onStart() {
    ball = new Ball();
}

void onRender() {
    background(0);
    ball.onRender();
}

void onUpdate(float now, float dt) {
    ball.onUpdate(dt);
}
```

Similarly, if you want to simulate multiple balls at the same time:

```java
Ball[] ballArr = new Ball[10];

void onStart() {
    for (int i = 0; i < ballArr.length; i++) {
        ballArr[i] = new Ball();
    }
}

void onRender() {
    background(0);
    for (int i = 0; i < ballArr.length; i++) {
        ballArr[i].onRender();
    }
}

void onUpdate(float now, float dt) {
    for (int i = 0; i < ballArr.length; i++) {
        ballArr[i].onUpdate(dt);
    }
}
```

<iframe src='02_May_12_Physics_Simulation_and_Encapsulation_bouncing_ball_oop.html' width="400" height="400"> </iframe>