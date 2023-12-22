# 3. Code Base structure
  <a name="code_base_structure"></a>  
  
   This section is intended to onboard developers on the [DASH] project code base; 
  - how the game is architectured [go](#overall_architecture)
  - the included Files [go](#files)
  - the System items  [go](#external_librairies)
  - the Libraries it relies on [go](#external_librairies)
  - the Services included in the engine [go](#Services)
  - a focus on the room initialization process [go](#room_initialization)
  - a focus on the Player’s Avatar object.
  
  # 3.A. Overall Architecture
  <a name="overall_architecture"></a>  
     
  This is a schematization of the overall game code architecture.  
  1-Game relies on data files:  
    - Configuration files are built by developers and will not change based on Player’s actions (statistics for enemies for example).  
    - User files allow to save and load Player’s progresses and preferences.  
  2-The code base is supported by External Libraries to extend GML features.  
  3-The project is organized around two categories of items:  
    - ‘System’ items, that manage the engine.  
    - ‘Standard’ Objects, those that are placed in the Room).  
  4-Some Services process specific dimensions of the game (like audio, resolution...).  

![image](https://github.com/MichelVGameMaker/DASH_WIKI/assets/62699812/132d4522-bb33-49a1-9fb3-bf5f6b267bbd)
   
  # 3.B. Files
  <a name="files"></a>  
   
  #### 3.B.1. User files 
 
  User files allow to save and load Player’s progresses and preferences.  
  There are three User file:  
  - FILE_DEVICE ("device.json") to save Device data. See the Device Struct description later-on.  
  - FILE_USER ("user.json") to save User data. See the User Struct description later-on.  
  - FILE_RUN ("__1.json") to save Player’s progresses. See the Run Struct description later-on.  

  #### 3.B.2. Configuration files 

  Configuration files are built by developers and will not change based on Player’s actions.
  There are two sets of files:
   - Level design files from LTtk.
   - Localization files for all texts.

  > Work in progress:
  > This section is to be detailed later-on.
  > Note that ‘Entities configuration files’ is mentioned on the schema but this is an error. It is not a file but a Script asset within Game Maker Studio.
  
  
  # 3.C. External Libraries overview
  <a name="external_librairies"></a>  
  
  The [DASH] project is using many external Libraries (most of them not from me) to extend the native features offered by Game Maker Studio.  
  - For some Libraries, it is important to be aware of them, because they impact how the [DASH] project is organized and works (IOTA, DYNAMO). I tried to explain those specificities in the Anatomy of an Object part.  
  - There are other Libraries that might be good to be aware of, to know their features are available but this is not mandatory.
  
  This section lists all Libraries used by the [DASH] project, introducing them briefly and explaining how to use them. All of them have dedicated documentation (on the internet or in GML Scripts) linked.  
  
  #### 3.C.1. Libraries overview  

| Libaries    | Features                                                       |
| ----------- | -----------------------------------------------------------    |
| Input       | Keyboard/mouse/gamepad support.                                |
| Scribble    | Text rendering.                                                |
| Lexicon     | Localization system.                                           |
| Bulb        | 2D lightning system.                                           |
| Iota        | Delta timing and time dilation.                                |
| Chatterbox  | Multi-branching dialog.                                        |
| Dynamo      | Live-updating of variables from configuration scripts or note. |
| Snitch      | Error logging and reporting.                                   |
| TXR         | Custom-scripting engine.                                       |
| AnimLib     | Sprite animation system.                                       |
| HitboxLib   | Hitboxes system.                                               |
| LDtk import | Import levels from LDtk level editor to Game Maker.            |
| YYLib       | Read and write .yy files.                                      |
| Edi_Thor    | Console to inspect instances.                                  |
| Deb_Hug     | Debugging helpers tied to a console.                           |
| imguigml    | User interface widgets, implementation of imgui.               |   
   

  #### 3.C.1. INPUT (By JujuAdams) https://www.jujuadams.com/Input/#/5.6/

  Input is a robust and very rich Library to manage keyboard/mouse/gamepad support.  
  How to:
  You should not worry about INPUT. If you need to call its functions, please refer to the above linked documentation.  

  #### 3.C.2. SCRIBBLE (By JujuAdams)
  
  #### 3.C.3. LEXICON (By TabularElf)

  #### 3.C.4. BULB (By JujuAdams)

  BULB is a Library to implement 2D lightning.    
  How to:
  BULB relies on a Renderer, every source of light and every occlude (=shadow-caster) should be added to this Renderer. Be aware that the Renderer is destroyed and re-created in each room by the oSYS_Room_Engine controller.  
  To facilitate the use of BULB, the Occluder Behavior is a ‘wrapper’ for the instructions required by [DASH] project.

  #### 3.C.5. IOTA (By JujuAdams)https://github.com/JujuAdams/iota

  IOTA is a Library to implement delta timing and time dilation (slow-motion effects).  
  How to:
  It is good to know that [DASH] uses IOTA. It impacts the project structure, as per where the usual Step event instructions are located. (see Anatomy of an object).  
  It has two ‘clocks’: one for the game (‘WORLD_CLOCK’) and one specific for the Player (‘PLAYER_CLOCK’).
  This allows, for example, to slow down Enemies but not the Player. The system processes (loading, menu) use the regular Game Maker step event.  
  In case you are updating or creating an Object, make sure to follow IOTA’s requirements explained in Anatomy of an object.  

  #### 3.C.6. CHATTERBOX (By JujuAdams)

  #### 3.C.7. DYNAMO (By JujuAdams)

  DYNAMO is a Library to live-update global variables. It comes with an .exe file to process some actions prior to compiling. 
  How to:
  Enemies and Player variables are defined in the database.gml Script. It contains values for all their variables organized in structs. You can change variables at runtime, changes will be detected and live-applied.  
   Upon update, DYNAMO will automatically run entities_set_variable() function which will calculate additional variables and pass them all to the active enemies and to the Player. 
 
  #### 3.C.8. SNITCH (By JujuAdams) https://www.jujuadams.com/Snitch/#/4.0/

  SNITCH is a Library to trace errors and comes with many other features like interfacing to sentry.io or GameAnalytics. This part is not implemented yet.  
  How to:
  The shw(string, string,...) function outputs results to the console. You can configure SNITCH to catch error log. Please see Juju's Git on SNITCH there is a wiki with explanations.  

  #### 3.C.9. TINY EXPRESSION RUNTIME – TXR (By Yellow Afterlife) https://yal.cc/docs/gm/txr/	   

  Tiny eXpression Runtime lets you compile and run snippets of GML-like code. I use it as a replacement for execute_string to customize Room / Level with Scripting and Events.  
  How to:
  TXR use a GML-like syntax. Game Maker constants (object indexes for instance) and Functions needs to be added to TXR engine by calling txr_constant_add() and txr_function_add.
  TXR instructions (string) first needs to be compiled into a TXR program (most likely when processing is low, like upon game start or level start) that can then be called at runtime.  
    
  #### 3.C.10. XCAMERA (By xDGameStudios)

  XCAMERA is a Camera System, labeled as a Demo / Example project, I reworked a bit to adapt it to my needs.
  How to:
  XCAMERA constructs a struct holding all required data and offering methods to trigger camera effects (called ‘tasks’). In [DASH] project, a global variable labelled with the ‘CAMERA’ macro, stores this structs. CAMERA is refreshed on every Room / Level change in the Room Initialization (as explained in Initializing Services). Effect can be called with CAMERA.shake(), CAMERA.center()…  

  #### 3.C.11. ANIM (By Me)

  Because I use IOTA and delta timing, I use this Library to process my sprites in synch with the game logic frequency.
  How to:
  Most objects have a 'sprite' struct constructed with this Library in the create event. The animation is processed (image increment) in end_cycle() with sprite.step() and is drawn in draw event with sprite.draw().This is also explained in anatomy of an objects.  

  #### 3.C.12. HITBOX (By Me)

  This Library creates and manages hitbox to hurt entities.  
  How to:
  You will mainly interact with this Library by creating hitboxes with hitbox_create(). They come with a bunch of parameters and there are some tricks to be aware of.

  #### 3.C.13. LDTK TOOLS ('forked' from Evoleo and Ailwuful)

  #### 3.C.14. YY (By Me) [go](#YY_Library_Doc()

  YY is only called by the LDtk importer, so you should not worry about this.  
  YY is a small collection of functions to adjust Game Maker room files and object files. It is intended to pair with level editing tools to facilitate import into Game Maker project.  

  #### 3.C.15. EDI_THOR (By Me)

  EDI_THOR is a visual console that allows to change variables in your instances on the fly during runtime (which requires imguigml). It is a generic tool I built.  
  How to: See the debugging section, to see how to use this.  

  #### 3.C.16. DEB_HUG (By Me) [go](#DEB_HUG_Library_Doc_English()

  DEB_HUG is simple Library to quickly include small debug features into your game (draw something like a collision mask, show_debug_console some data like a timer, deactivating all traps to test a level).
  How to:
  Some debugging features are available in the [debug] entry of the Debugging main menu.
  Should you want to add your own features, a feature is added to DEB_HUG pane with one function call to deb_hug_add_toogle() or deb_hug_add_button() in Game Maker Studio. (see the custom debugging features).  

  #### 3.C.17. IMGUIGML (By Rousr)

  Imguigml is GML implementation of imguigml by rousr. I use it for my debugging tools, namely EDI_THOR, DEB_HUG, the debugging main menu, the LDtk importer.  
  How to:
  You should not worry about IMGUIGML. If you need to call its functions, please see the associated documentation within the project.  
  
  
  # 3.D. System Items
  <a name="external_librairies"></a>  
  
  #### 3.D.1. Initializer Object   

  The Initializer (oSys_Game_init Object) does not appear in the schema because it disappears after Game start.  
  It oversees the initialization of all System items and Services composing the flesh of the Engine.  
  A singleton instance of this Controller Object is created in the very first room called ROOM_INIT.
  It processes everything and is then destroyed by moving to another Room.  
  The id of this singleton is saved in a global variable accessed with the INITIALIZER macro.  

  Initialization process works like so:  
  1-create sys_state Struct.  
  2-create Device and User Structs.  
  3-create empty Run Struct.  
  4-Create Game Engine Singleton.  
  5-Initialize each System Services (from the Game Engine).  
  6-Enable and configure debug tools: EDI_THOR, DEB_HUG, SPANER.  

  > Note:  
  > Services would need to be initialized more progressively when loading the game and loading file would need to use asynchronous loading. 

  > Work in progress:  
  > Initialization has been recently transferred from the Game Engine (oSYS_Engine) to the Initializer Object. 
  > You will notice that Services are stored in the Game Engine Object (see later on) but are started by the Initializer. 
  > This is why it is messy, duplicated and not yet finalized. 

  #### 3.D.2. System State Struct
  
  The Game System State is stored into a dedicated Struct.  
  It manages the Game’s state (game in a menu, game in a run...).  
  The Initializer creates one instance of this Struct at the very beginning of the initialization process.  
 The id of this struct is saved in a global variable accessed with the SYS_STATE macro.  

  State Management propagates changes to all System items and Services to keep everything working properly.  
  The most important events are:
  - Game Initialization (‘boot’) obviously where the Initializer do its thing.  
  - Run Start/End where run specific Services are initialized (Run Struct, Bulb for the lights...).  
  - Room Transition where Services need to reset or be cleaned-up (Camera, Bulb...).  
  - Run Pause/Menu/Interaction to manage Player’s Avatar and inputs accordingly.  

  > Work in progress:  
  > System state is the start of an approach to move Engine’s data and methods to a Struct instead of the Game Engine Object. 
  > 1-For now, it only covers the Game State value but is intended to be expanded.  
  > 2-The way state changes are propagated is a bit messy for now.  

  #### 3.D.3. Device and User Structs
  
  User’s context data is stored in two Structs: Device and User.  
  The Device Struct represents the actual real-life device running the Game. It only holds technical data.  
  The User Struct represents the real-life user playing the Game. It holds user details and preferences.   
  The ids of those structs are saved in two global variables accessed with the DEVICE and the RUN macro.
  When running the Game the first time, the Initializer creates a Device and a User Structs, set to default values.  
  On any change, those Structs are saved to a local save file in appdata.  
  When running the Game again, the Device and the User Structs are loaded from those files.  

  #### 3.D.4. Run Struct
  
  Game’s progresses are saved in a Run Struct.  
  The Run Struct represents the progresses of the Player in the Game. It is composed of four dimensions: the Character, the World, the Story and the KPIs.  
  When running the Game, the Initializer creates a Run Struct, set to empty values.  
  When starting a new Game (I call it a new ‘Run’) or loading an existing one, the Game Engine will create a Run, based on default values or loaded from a local save file.  

  #### 3.D.5. Game Engine

  Core Engine (oSys_Engine Object) acts as a super Controller, processing multiple processes, instead of having one Controller for each domain. 
  The Initializer creates one singleton instance of this Object after loading Device and User data.  
  The id of this singleton is saved in a global variable accessed with the ENGINE macro.  

  The Game Engine (oSYS_Engine) centralizes specialized Services (a Service offers a set of functions to complement Game Maker Studio features).  
  oSYS_Engine is used to start each Services (The Initializer does that), to call Services' features or to run regular instructions (every Step for example) to process progressive actions.  
  
   
  # 3.E. Services overview
  <a name="Services"></a>  
  
  The [DASH] project is using Game Services (centralized int the Game Engine (oSYS_Engine)) to extend the native features offered by Game Maker Studio. It is important to understand those Services because they are called many times in the project.  
  This section lists all the Services available in the [DASH] project, introducing their functions / methods briefly.  

+ add RENDERER
+add a global variable labelled with the ‘CAMERA’ macro, stores this structs. CAMERA is refreshed on every Room / Level change in the Room Initialization (as explained in Initializing Services). Effect can be called with CAMERA.shake(), CAMERA.center()…  

  #### 3.E.4. Services anatomy 

  For the sake of organization, each Service is currently located in a specific user_event of the Game Engine (oSYS_Engine).

  Most Services are defined by two or three components:  
  -1-a service_initialize() function, to set the data structure required by the Service.  
  -2-a set of methods that allows to call the Services' features from anywhere in the project, as one would call the GML native functions.  
    Services’ functions are instance’s methods defined in the Game Engine’s user_event. They need to be called with ENGINE.do_something().  
  -3-also, five Services run regular instructions (in the Step or End_Step events) to process progressive actions: 
    - Slowmo Library, in the Step event of oSYS_Engine (to process delta timing and slow-motion effects every step).  
    - Direktor Library, in the Step event of oSYS_Engine (to process a cinematic if one is found active),
    - Bruit Library, in the Step event of oSYS_Engine (to adjust audio to account for attenuation or slow-motion and to stop assets that are not playing anymore).  
    - Resolution Library, in the Step event and Draw event of oSYS_Engine (in case a resolution change is active).  
    - Input, in a begin_cycle IOTA clock.  
    - Bulb, somewhere to be found. Well Bulb is not referenced as a service. 

  > Coming soon...  
  > Updating inputs in IOTA is bad practice. It should be moved to a regular step event, after accounting for momentary inputs, and adjusting buffer in slowmotion mode.  
  (All those processes could also have been managed with Timesources).  

  Please note that some Libraries encapsulate Libraries because I try to keep distinction / readability / control.
  But, be aware that Libraries run initialization instructions in their dedicated Scripts and may have their own Timesources or Controller Object. 
  It means you will not find every initialization and step instructions in the Game Engine (oSYS_Engine).  

 #### 3.E.1. Services overview  

  There are two categories of Services:
  - Game-level Services are initialized on Game Initialization and never stops, even on user’s pause.
  - Run-level Services are only relevant during a Run (Bulb’s lights, for example, are only required while evolving in the levels, not outside a Run).

| Services    | Features                                               |Category|
| ----------- | ------------------------------------------------------ |--------|
| Game_State  | Change game state. Duplicated to sys_state.            |Game    |
| Sentry      | Not implemented yet.                                   |Game    |
| Resolution  | Manages display and resolutions.                       |Game    |
| Audio       |                                                        |Game    |
| Font        | Two instructions to setup Scribble Library.            |Game    |
| Text        | Configures Dialog database and localization.           |Game    |
| Inputs      | Wrapper functions to input Library.                    |Game    |
| Transition  | Trigger transition (pair with WRP Objects).            |Run     |
| Slow mo     | Trigger and manage slow-motion effects.                |Run     |
| Parallax    | Allows to apply parallax to layers.                    |Run     |
| Light       | To be added in the object                              |Run     |
| Misc        | Create game juice effects.                             |Run     |
| TXR         | Configure TXR Library for custom-scripting.            |Run     |
| Cinematik   | Configure Direktor for cinematics.                     |Run     |
| Dialog      | Display and manage Dialog.                             |Run     |

  #### 3.E.2. Game_State

  Game_State is the Service responsible for ‘properly’ changing game state.

  **set_game_state:** Change the current Game State: booting, menu, play...(clean inputs properly, activate/deactivate player, pause/unpause game).  
  **boot_to_menu:**  Very first transition to menu screen. Could use set_game_state.             
  **menu_to_run:**  Transitions from menu screen to the Game. Could use set_game_state.            
  **run_to_menu:**  Transitions from the Game to the menu screen. Could use set_game_state.             
  **run_to_game_over:**  Freezes game when player dies.                
  **run_to_dialog:**  Displays a Dialog and set the Game to the associated State.  
  **dialog_to_stop:**  Stops the current Dialog.                           

  > Code Cleaning:
  > I should use sys_state and stop using this. But I am in the middle of the migration.
  > boot_to_menu, menu_to_run, run_to_menu and run_to_game_over could use set_game_state.  

  #### 3.E.3. Transition

  Transition is the Service responsible for triggering Room/Level transitions. It pairs with WRP_ Objects that points where to move the Player’s Avatar to. 

  **transition_init_service:**  Initializes room transition variables. Intended to be ran at game start.  
  **transition_run_to_room:**  Changes room during the run.   
  **transition_ldtk_run_to_room:**  Changes room during the run, using data from LDtk level editor.   

  #### 3.E.4. Sentry

  #### 3.E.5. Font

  Font is the Service responsible for rendering text.  

  There are just a few instructions for configuring Scribble. There are no associated functions.  
  
  Scribble is Juju's Library for rendering text. The initialization of Scribble itself is done in the associated Library and is happening prior to the creation of the Game Engine controller.
  You can call directly Scribble functions.  

  #### 3.E.6. Text

  **language_init_Services():**    
  **language_set_language():**  Changes language setting and refresh the associated dialog file in Chatterbox database.  
  **lookup_YARN_file():**  Lookup table to return the proper YARN (dialog) file name, depending on language code. 
  **lookup_lexicon_language():**  Lookup table to return the proper language name (as I have written it in the LEXICON.csv file) depending on language code

  > Language code, Language name etc.... is a bit tricky.  
  > - language code is "en", "fr"... from ISO639.  
  > - lexicon_language is "english", "french"... as defined by the header row of the included lexicon file. lookup_lexicon_language is used as a hardcoded transcodification.  
  > - yarn.file change based on the language "EN.yarn", "FR.yarn"... lookup_yarn_file is used as a hardcoded transcodification.  
  > To add a language:  
  >  - go to included file.  
  >  - locate LEXICON_FILE .csv file in this folder and open it.  
  >  - add the language to the first/header row (ex "French") of the LEXICON_FILE .csv file.   
  >  - add the transcodification to the switch statement from lookup_lexicon_language and lookup_yarn_file functions so that "french" returns "fr" and "FR.yarn".   
  >  - make sure FR.yarn file exists with the exact same structure as others yarn files.  

  #### 3.E.7. Inputs

  Inputs is the Service responsible for managing user's inputs. It heavily relies on Juju adam's INPUT Library (see the associated folder). 
 
  **clear_player_inputs:** Clear inputs data, maybe useful upon pausing or changing Game State.  
  **update_inputs:** Root inputs depending on the current Game State.    

  #### 3.E.8. Resolution

  Resolution is the Service responsible for managing display and resolutions, namely window dimensions, gui_surface dimensions, application surface dimensions.  
It allows to manage width/height for the game, width/height for the gui, to update the associated user settings and to process resolution changes over multiple steps.  

  **resolution_init_service():** Initialize resolution variables. Intended to be ran at game start.  
  **resolution_set_scale():** Setter to change the window size.  
  **resolution_set_dimension():** Setter to calculate the best display properties. - thanks to Pixelated Pope !  

> Please note that the Resolution Services also run two processes in step event and draw event:  
>  - __resolution_change_step: to manage resolution changes, phase by phase, to avoid glitch (window size, application surface, gui surface).  
>  - __resolution_change_draw: to manage fade-in and fade-out during resolution changes.  

  #### 3.E.9. Slowmo

  Slowmo is the Service responsible for managing delta time and time dilation. It heavily relies on Juju adam's IOTA Library (see the associated folder)
  It allows to launch "slowmotion effects" for a specified time frame. Multiple effects can run in parallel and the bigest slowmo will be the one applied. 
  Player and Game have specific slowmo factor so that Player can run at full speed while everything else run slow, when perfect dash is peformed.  

  **slowmo_init_service:** Initializes the service by declaring associated variables.  
  **slowmo_stop_service:** Stops the Services by cleaning the associated variables.  
  **slowmo_start:** Start an anonymous slowmotion effect. It cannot be accessed specifically after that.
  **slowmo_start_game_only:** Same as slowmo_start but will not impact player (used for perfect dash). Start a slowmotion effect only for Game's clock.  
  **slowmo_start_named:** Starts a slowmotion effect and give it a name for dev to be able to fetch / stop it.  
  **slowmo_stop_named:** Stops the slowmotion with the associated name.  
  **slowmo_stop_all():** Stops all slowmotion effects. Named or not.  

  > Please note that the SlowMo service also run two processes in step event:  
  > __slowmo_step: Process delta timing (running cycles instruction for all registered instances for the two clocks) and time dilation (running alarms and destroyng terminated slowmotion effects). This function is intended to be run every step.  
  > __slowmo_refresh(): Update variables for the service and apply the refreshed time dilation to the two clocks.

  Also the color_shading_start method is stored here... could go in miscleanous.

  #### 3.E.10. Audio
  **bruit_init_service:** Initialize the Services by declaring associated variables.  
  **bruit_clean_service:** Stops the Services by cleaning the associated variables.  
  **bruit_set_sound_gain:** Changes audio settings for sounds.  
  **bruit_set_music_gain:** Changes audio settings for music.  
 
  > Please note that the Audio service also run two processes in step event:  
  > __bruit_step to process all sounds, i.e. stop dead sounds and measure scaled sounds life.
  > __bruit_adjust_slowmo to adjust pitch for all playing sounds to emulate a change in the slow motion effect.  

Also, two global functions allow to play sounds from any scope:
- bruit_play_sound_game() Plays the indicated sound asset with passed pitch & gain modificatons.
- bruit_play_sound_game_scale() Plays the indicated sound asset with passed pitch & gain modificatons. The function will increase the pitch if a sound using the same asset has been played in a 60 steps window.  

  #### 3.E.11. TXR

  TXR is the Service responsible for configuring TXR Library.

  There are just a few instructions for adding useful constants and functions that will be available for custom scripting.
  There are no associated functions. You can use TXR functions directly. See 4. Playing, testing and debugging.

  #### 3.E.12. Cinematik

  Cinematik is the Service responsible for processing cinematics.  
  
  There are just a few instructions for setting up and configuring Direktor Library.  
  There are no associated functions. You can call directly Direktor functions from the CINEMA struct.

  #### 3.E.13. Parallax

  Parallax is the Service responsible for processing parallax effects for background and front layers.  

  **parallax_init_service():** Initializes the service by declaring associated variables.  
  **parallax_add_layer():** Registers a layer with its parameters and then updates sprites coordinates accordingly to the layer’s parallax factors. Intended to be used on room start to override sprites coordinates from the IDE.  
> Please note that the Parallax service also run one process in step event:  
>  - parallax_step to actually process parallax by adjusting layers positions according to their registered parameters.  

  #### 3.E.14. Misc       

  Misc is the Service that allows to create game juice effects.  
 
  **juice_shake:** runs a special screen shake effect, including some Zoom-in and Zoom-out.  
  
   
  # 3.F. Room initialization
  <a name="room_initialization"></a>  
  
  While Player is playing, each Room is initialized after its creation. This is achieved by a oSYS_Room_Engine object. One instance of this object is placed in each Room in Game Maker Room Editor.
  
  #### 3.F.1. The two steps of initialization

  oSYS_Room_Engine processes the initialization of instances in the room in two steps. 
  - First in its Create event, to create everything instances in the room.  
  - Then in its Step event, to initialize everything. Then the step event will run only once.    

  The order of instructions is important because some instances need to wait for all creations to be over before initializing. For example:  
  - Levers need to build a reference to another instance that it controls (a Door for instance). This can only occur once all instances (in particular the Door) have been created, that is why the initialization occurs in a step event after the creation.  
  - Traps auto-rotate to aim outside of walls. Thus, Traps can only be initialized after walls have been created.  

    #### 3.F.2. Create event – create every instances

  oSYS_Room_Engine also processes the adjustment of Services for the new room. 
  CAMERA
  PLAYER transition ? 
In create event:  
  - Creating ShaderController instances to apply shader effects to specific layers only.  
  - Creating BULB renderer for this room. Cleaned in clean-up.
  - Scaling a background object. Wich is only for prototyping.
 engine_room_init_create_instances();	// tiles must be there  tiler_process_replacements(_layer_id, SYS_TILE_SIZE, _lookup) for Tiles and then Tiles_Items.
  - Create a new CAMERA struct (stopping all current camera’s tasks) for this room.
  - Stop all slowmotion effects.
  - Create a particles system and particle type for this room. Cleaned in clean-up.
  - Clean layers from the ENGINE’s Parallax service and add the room’s layers.

    #### 3.F.3. Step event - initialize the room
 
  - engine_room_init_start_instances();	// instances must be there
  - engine_room_init_start_player();		// transition door must be there
  - engine_room_init_start_camera();		// player must be there
  - engine_room_init_create_path_grid();	// solid must be there
  - ENGINE.set_game_state(e_engine.run_play);

    #### 3.F.4. Step event

  - ENGINE.parallax_step(); - go in engine to clean project
  - RENDERER.Update(0, 0, room_width, room_height); - go in engine to clean project
  - RENDERER.Draw(0, 0, room_width, room_height): drawing the shadow surface from BULB Library. See the list of external Libraries below. [go](#librairies_and_Services  Can go in engine to clean project

  > Please note that the regular particles (without parallax) are refreshed with game’s clock for IOTA. To stay in synch.

  > Please note that, for prototyping parallaxed particles in the front layer, I am creating particles in IOTA’s tick for the ROOM_init object.  
  
  
  # 3.G. Focus on the Player’s Avatar
  
  This part introduces the code base for the ‘Player’, understand the Player’s Avatar in the game. The code is very basic (because it was developed for prototyping) but should be ok to read.
  
  Gameplay variables
  Remember that the Player uses run entities_set_variable() in its Create event to declare and set all gameplay variables. This function fetches values from database.gml Script asset, from the ‘Player_data’ struct.
  
  Timers:
  Player is using a lot of x_timer variables to count down (or up) alarms. They are incremented or decremented each IOTA’s regular cycle in Player’s user_event_2.
  x_timer variables pair with X_TIME or X_TIMING variable that specify the associated time frame.

   Player’s ‘step’ is structured like so;
  1-Update timers.
  2-Run the current state function.
  3-Move the Avatar and manage collision. Movement may depend on the current state.
  4-Manage the Avatar’s interaction with interactive items.
  5-Move entities that are attached to the Avatar: light, shield…

  States functions:
  States process the following actions in that order:
  1-State entrance instructions if applicable.
    Changes the sprite and animation (sometimes accounting for transitions), play some SFX/VFX, resets timers and other state variables (number of jumps...).
  2-Adjust sprite and animation to the current situation (Player’s input, regular smoke and other FX).
  3-Emulate Player’s input if applicable (if Avatar is standing over an edge for example).
  4-Update velocity (acceleration and friction)
  5-Transition to another state if conditions are met (this changes the current state but does not do anything for now, state entrance is only processed on the next step in 1. where the sprite will change etc..).
