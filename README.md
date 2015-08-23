# ADE #

## What is ADE? ##

The Agent Development Architecture (ADE) is an implementation of the
DIARC Cognitive Architecture. ADE consists, mainly, of a set of
*components* that provide useful functionality. For example, the
LRFComponent provides an interface to a Laser Range Finder, and the
PioneerComponent provides an interface to the Pioneer robot. An
instance of ADE consists of a set of components whose connections and
status are managed by one or more *Registries*.

This package provides a demo of a subset of ADE which you can quickly
try out for yourself! To do so, just double click on "ADE".

## Demo Walkthrough ##

Examining the ADE file shows that it links to the .ADE shell script, which in turn executes the following command in the src directory:

`./ant run-registry -Df=config/sajarSim.config -Dargs="-g"`

(If this won't run the script on your system, simply navigate in your terminal to the src directory and run the command above directly)

This command starts an *ADE Registry* and then starts all of the *ADE
Components* listed in `config/sajarSim.config`.

### What's in a Config File? ###
This config file starts seven components encapsulated in
STARTCOMPONENT-ENDCOMPONENT blocks. Feel free to change their contents
or to switch to a different Config file to get different behavior!

1. `com.adesim.ADESimEnvironmentComponent`
    * This starts the simulated robot environment.
    * Examine the component's "componentargs" line. We can see two arguments:
        * `-cfg com/adesim/config/200BostonReverse.xml` : This specifies which map to use.
        * `-g` : This starts the visualization, so that we can see what is going on.
2. `com.adesim.SimPioneerComponent`
    * This starts the simulated Pioneer Robot, and uses the following arguments:
        * `-initpose -2.5 0 4.7` : specifies the initial X,Y,T pose of the robot.
3. `com.motion.MotionComponent`
    * provides simple movement functionality (e.g., the ability to
      traverse forward, travel to a particular point, or turn a certain
      amount), and uses the following arguments: 
        * `-deadreck` : specifies that the robot uses dead reckoning to localize. 
        * `-critical 0.6` : specifies the critical distance at which the robot must stop for fear of hitting an obstacle.
        * `-deftv 0.35` : specifies the robot's default translational velocity
        * `-defrv 0.2` : specifies the robot's default rotational velocity
        * `-slowtv 0.25` : specifies the robot's slow translational velocity
        * `-slowrv 0.2` : specifies the robot's slow rotational velocity
        * `-tol 0.8` : specifies the robot's travel tolerance
        * `-teps 0.1` : specifies the robot's turning tolerance
4. `com.action.GoalManagerPriority`
    * The ADE Goal Manager, which, well, manages goals, and which uses the following arguments:
        * `-component com.interfaces.NLPComponent` : indicates that the GM needs to find an NLPComponent to connect to.
        * `-component com.motion.MotionComponent` : indicates that the GM needs to find a MotionComponent to connect to.
        * `-component com.adesim.SimPioneerComponent` : indicates
          that the GM needs to find a SimPioneerComponent to connect
          to. 
        * `-dbfilesupp com/action/db/muri-nofunnybusiness.xml` : specifies
          the set of supplemental actions the GM can use to pursue goals
          beyond those actions available to it by default. 
        * `-script listen self --` : specifies the robot's default behavior.
        * `-agentname self` : specifies the name of the agent.
5. `com.discourse.TLDLDiscourseComponent`
    * The NLP component used for syntactic and semantic parsing, which uses the following arguments:
        * `-action` : indicates that this component needs to find an action manager to connect to.
        * `-actor cbot` : specifies the name of the agent to use in discourse.
        * `-dict com/discourse/TL_DL_parser/newautonomy.dict` : specifies the parser's lexicon / grammar.
6. `com.simspeech.SimSpeechRecognitionComponent`
    * The simulated speech recognizer, which uses the following arguments:
        * `-g` : starts the interface used to input commands.
        * `-cfg com/simspeech/config/newautonomy` : specifies the set of utterances to display in the interface
        * `-nlp com.discourse.TLDLDiscourseComponent` : tells the simulated speech recognizer to look for a
          TLDLDiscourseComponent to connect to.
7. `com.simspeech.SimSpeechProductionComponent`
    * Simulated speech output, using the following argument:
        * `-g` : causes simulated speech output to be displayed in popup
          windows (in addition to being displayed in the command line. 


### Running the Demo ###
When you run the ADE script, you will see the following three visualization windows pop up:

* The Registry:
    * This is the central hub where all the ADE components connect.
    * Mousing over a component shows what other components that
      component has connections to. 
    * Right-clicking on a component provides a variety of options. 
      By Right-clicking on the Goal Manager and selecting the "Action
      Manager" visualization, you will be able to see the various goals
      currently being pursued by the Goal Manager.
    * This visualization appears because of the `-g` provided in the
      `-Dargs` list above. 
* The ADESim Environment
    * This shows the simulated pioneer positioned in our lab.
    * The robot appears as it does because, in the config file
      specified above, two adesim  components are started: 
        1. An ADESimEnvironmentComponent with a particular specified map.
        2. A SimPioneerComponent with a particular initial pose
    * You can drag the robot around in the environment. This is
      particularly useful for seeing in what positions the robot can and
      cannot maneuver. 
    * Click "View" and check all of the unchecked boxes. You will now
      see some green lines and some yellow lines. These are,
      respectively, the robot's laser and camera fields of view.  Try
      dragging the robot over so that the red box is in its field of
      view. You will see a circle appear around it, signifying that the
      robot's simulated camera has "recognized" the box.
* Simulated Speech Recognition
    * Using this interface, you can tell the robot to stop, go straight,
      turn left, and turn right.  If there's not enough room to
      maneuver, the robot won't respond, but you should see a message in
      the terminal specifying why, and, if all of the "View" boxes are
      checked, a small blue dot should be apparent on the side of the
      robot where an obstacle is preventing movement.

### Running the Speech Demo ###
While the ADE demo described above uses /simulated/ speech input and
output, running the ADE-SPEECH demo found in the same directory uses
/unsimulated/ speech recognition and speech output, i.e., you can talk
to the robot, and it can talk to you!

Assuming you have a working microphone and speakers, running this demo
will allow you to command the robot using utterances such as "Go
straight" "Turn Left" and "Stop", and allow you to hear the robot's
responses over your speakers.

If you run this demo and aren't able to speak to the robot, try opening
`com/sphinx4/config/tom_speechtest.xml` and changing `selectmixer` to
a different number.

### Starting Components Individually ###

Let's try running a few components by starting them individually.

First, run the registry: `/ant run-registry`

Next, start the simulator: `./ant run-adesimenvironmentcomponent -Dargs='-g'`

Finally, add a simulated robot: `./ant run-simpioneercomponent`

To drive the simulated robot around the simulated environment, use the
arrow keys. You can also drag the robot around with your mouse. 
To see the laser readings, go to `view->show laser lines`. To see the
world from a 3D perspective, go to `3D->Create 3D View` To use a
different map, add to the sim environment's args string `-config
com/adesim/config/[name-of-map-you-want.xml]` 

### Running a Physical Robot###
If you have an iRobot Create and/or Hokuyo URG LRF, you can start them
using a similiar process:

First, run the registry: `./ant run-registry`

Next, start the create: `./ant run-createcomponent -Dargs="-port=/dev/ttyACM0"`

Then, start the LRF Component: `./ant run-urglrfcomponent -Dargs="-port /dev/ttyAMA0 -g"`

Finally, start the keyboard joystick: `./ant run-keyboardjoystickcomponent`

### Running Components Distributedly ###
Now let's look at running parts of the system on the robot and parts on another computer. Here's how we do that:

On the remote computer:
Run the registry: `./ant run-registry -Dk=config/hosts/[yourname].hosts -Dl=[your ip]`

For this to work, the robot computer needs to appear in your hosts file ( (e.g., config/hosts/matt.hosts) on the remote computer, and vice versa. 

On the robot:

Start the create: `./ant run-createcomponent -Dargs="-port=/dev/ttyACM0" -Dr=[your ip] -Dl=[the robot's ip]`

**If this doesn't work, try ttyAMA0 instead.**

Start the LRF Component: `/ant run-urglrfcomponent -Dr=[your ip] -Dl=[the robot's ip]`

Back on the remote computer: 

Run the keyboard component: `/ant run-keyboardjoystickcomponent -Dr=[your ip] -Dl=[your ip]`
