## Sep 1 : Intruction to Virtual Joystick

Everdered how your soldier moves in Mini Militia?
Want to know how shadow moves around and battles in Shadow Fight series?
Have a Desire to achieve player control like Bloody Harry?
Or just excited to learn about Virtual Joysticks?

Then your search ends here.

In this blog, I will show you how to implement a virtual joystick in Unity.
Now, keep your favourite joystick design in your mind and follow the simple steps.

And you will have your own working model of Virtual Joystick right in your hands or rather I would say, right under your thumb!

![](http://www.theappguruz.com/app/uploads/2016/06/final-output.com-video-to-gif.gif)

## Step 2 : Scene Setup

Setup your scene as shown in following pictures:

![](http://www.theappguruz.com/app/uploads/2016/06/scene-hierarchy.png)

![](http://www.theappguruz.com/app/uploads/2016/06/scene.png)

For now, I’m using a simple 2D image to represent our player.

### Note

Do not forget to set anchor point and pivot point for your Joystick Container, or you will find your joystick on different positions on different resolutions.

![](http://www.theappguruz.com/app/uploads/2016/06/anchor-points.png)

## Step 3 : Scripting
Attach this script to JoystickContainer

### VJHandler.cs
```csharp
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.EventSystems;
public class VJHandler : MonoBehaviour,IDragHandler, IPointerUpHandler, IPointerDownHandler {
    private Image jsContainer;
    private Image joystick;
    
    public Vector3 InputDirection ;
    
    void Start(){
        
        jsContainer = GetComponent<Image>();
        joystick = transform.GetChild(0).GetComponent<Image>(); //this command is used because there is only one child in hierarchy
        InputDirection = Vector3.zero;
    }
    
    public void OnDrag(PointerEventData ped){
        Vector2 position = Vector2.zero;
        
        //To get InputDirection
        RectTransformUtility.ScreenPointToLocalPointInRectangle
                (jsContainer.rectTransform, 
                ped.position,
                ped.pressEventCamera,
                out position);
            
            position.x = (position.x/jsContainer.rectTransform.sizeDelta.x);
            position.y = (position.y/jsContainer.rectTransform.sizeDelta.y);
            
            float x = (jsContainer.rectTransform.pivot.x == 1f) ? position.x *2 + 1 : position.x *2 - 1;
            float y = (jsContainer.rectTransform.pivot.y == 1f) ? position.y *2 + 1 : position.y *2 - 1;
            
            InputDirection = new Vector3 (x,y,0);
            InputDirection = (InputDirection.magnitude > 1) ? InputDirection.normalized : InputDirection;
            
            //to define the area in which joystick can move around
            joystick.rectTransform.anchoredPosition = new Vector3 (InputDirection.x * (jsContainer.rectTransform.sizeDelta.x/3)
                                                                   ,InputDirection.y * (jsContainer.rectTransform.sizeDelta.y)/3);
            
    }
    
    public void OnPointerDown(PointerEventData ped){
        
        OnDrag(ped);
    }
    
    public void OnPointerUp(PointerEventData ped){
        
        InputDirection = Vector3.zero;
        joystick.rectTransform.anchoredPosition = Vector3.zero;
    }
}
```

Notice that we are using EventSystem namespace here to handle touch events in our project.

As Unity Docs would say,


**Let's dive in our methods now:**

1 - **OnDrag()**: It passes one argument of type PointerEventData. This class is associated with mouse and touch events. We will only need a couple of values from it, so I'm skipping the description.
**Still if you want to check all the description about it, follow this link:**

- https://docs.unity3d.com/ScriptReference/EventSystems.PointerEventData.html
The function **RectTransformUtility.ScreenPointToLocalPointInRectangle** returns true if the RectTransform of gameobject is interacted. And also gives the location of the point of interaction.

After that we’ve calculated the Input Direction and the position of our joystick.

2 - **OnPointerDown()**: This is used to invoke **OnDrag()** method. This will allow us to get the effect as soon as we touch/click our joystick, even if we are not dragging the stick.
Remove this invocation and see the difference in behaviour in play mode.

3 - **OnPointerUp()**: It sets InputDirection and joystick position to zero.

### PlayerMovment.cs
```csharp
using UnityEngine;
using System.Collections;
public class MovePlayers : MonoBehaviour {
    public float moveSpeed = 05f;
    public VJHandler jsMovement;
    
    private Vector3 direction;
    private float xMin,xMax,yMin,yMax;
    
    void Update () {
        
        direction = jsMovement.InputDirection; //InputDirection can be used as per the need of your project
        
        if(direction.magnitude != 0){
        
            transform.position += direction * moveSpeed;
            transform.position = new Vector3(Mathf.Clamp(transform.position.x,xMin,xMax),Mathf.Clamp(transform.position.y,yMin,yMax),0f);//to restric movement of player
        }    
    }    
    
    void Start(){
    
        //Initialization of boundaries
        xMax = Screen.width - 50; // I used 50 because the size of player is 100*100
        xMin = 50; 
        yMax = Screen.height - 50;
        yMin = 50;
    }
}
```

Attach this script to player gameobject.

**Dont't forget:**

- Assign the **JoystickContainer** gameobject to **jsMovement** in editor before you hit play.

This script has only one method. **Update()**. Which gets the InputDirection from our previous script and moves our player according to it.

Now hit Play and see how well your joystick handles your player. I Hope now you got the basic idea about joysticks.

Now you can create joysticks with your own awesome graphics and excellent concepts.

## Step 4 : Examples
Let me give you some samples before we part.

![](http://www.theappguruz.com/app/uploads/2016/06/sample.png)

![](http://www.theappguruz.com/app/uploads/2016/06/sample-1.png)

![](http://www.theappguruz.com/app/uploads/2016/06/sample-2.png)
