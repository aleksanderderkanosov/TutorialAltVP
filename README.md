# TutorialAltVP

## Setting up a UE project for Virtual Production

### a. Setting up an Unreal project:
   1. Open the Unreal project;
   2. Add a CineCameraActor to the scene (Place Actors -> Cinematic -> Cine Camera Actor);
   3. Connect the Composure plugin (Edit -> Plugins -> Composure);
   4. Window ->Virtual Production-> Composure Compositing -> Click RMB-> Create New Comp -> Empty Comp Shot.
   
  **Important: In this and the following paragraphs, do not forget to save the changes so that they are displayed in the project.**

### b. Setting up a video stream from the camera in Unreal:
**Attention! The following sequence of steps is relevant for Blackmagic video capture cards. For other manufacturers, the steps may differ.**
  1. Right-click on the Compositing Element (created in the previous step) -> Add Layer Element -> Media Plate;
  2. In the Content Browser RMB -> Media -> BlackmagicMediaSource;
  3. Go to the created BlackmagicMediaSource -> Blackmagic -> Configuration -> select the configuration that corresponds to the camera settings -> Apply;
  4. In the Content Browser RMB -> Media -> Media Player -> check the box "Video output MediaTexture asset" -> OK;
  5. Go into the Media Player and in the MediaSources tab (below) double-click on the BlackmagicMediaSource created in step 2.3 - a video stream from the camera should appear in the player window.
  
  **Important: The Media Player must be open in a separate window during operation - when it is closed, the video stream will stop. Also each time you start Play it is required to repeat steps from step 3.5 to get the video stream again.**
  
  6. Go to the Details tab of the MediaPlate component (see section 2.1) -> Composure -> Input -> Inputs ->Disclose Media Source -> in Media Source we transfer the Media Texture that was automatically created together with MediaPlayer in step 2.4. Check that the Media Plate now receives video from the camera.
  7. Adjust the chromakey: MediaPlate -> Composure -> Transform/Compositing passes -> Transform passes -> Expand Chroma Keying -> Key Colors -> Add Element (+) -> select the desired chromakey color using Color Picker or a dropper (![image](https://user-images.githubusercontent.com/69207595/217859928-904ed66c-545e-44d7-8ec3-413e6c778758.png)) on the camera video.

### c. Add a virtual camera: 
right-click on Composing Element -> Add Layer Element -> CG Layer. The image from the CineCameraActor camera you added in step 1.2 should automatically appear in its window.

### d. Let's set up the material for compositing: 
  1. Go to Compositing Element (see point 1.4) -> Details -> Composure -> Transform/Compositing passes -> Transform passes -> Add Element (+);
  2. Go inside TransformPass and see the missing Material you want to create: click on None -> Create New Asset -> Material;
  3. Go to the created Material -> tab Details -> Material -> Material Domain -> Post Process;
  4. RMB in Material Graph -> TextureSampleParameter2D -> give it a name exactly like MediaPlate;
  5.Duplicate the previous node and give it exactly the same name as the CG Layer;
  6. RMB in Material Graph -> Over -> connect MediaPlate RGBA texture output to input A of the Over node, and CG Layer RGBA texture output to input B;
  7. Connect the RGBA output of the Over node to the Emissive Color of the Material Graph node:
  ![image](https://user-images.githubusercontent.com/69207595/217860640-d7544a0c-acae-4e74-917b-c1ef506a9b2f.png)
  8. Check Compositing: RMB on Compositing Element (see point 1.4) -> Preview.

### e. Setting up Antilatency tracking:
  1. [Install](https://developers.antilatency.com/HowTo/SdkPackageToUnreal_en.html#Installation) the Antilatency SDK in the project;
  2. Follow steps 1 and 5 of the [instructions](https://developers.antilatency.com/HowTo/SdkPackageToUnreal_en.html#Using) (steps 2-4 are unnecessary);
  3. Go to AntilatencySDK Content ⇾ Blueprints ⇾ Samples ⇾ Pawns and transfer BP_AltSimplePawn to the scene (remember to make sure Location and Rotation are in zero positions);
  4. Attach the child object CineCameraActor (item 1.2) to BP_AltSimplePawn;
  5. Check tracking: Play -> F8 -> click on CineCameraActor in Outliner -> in the window the camera should move respectively with the tracker.

### f. Adjust the position of the tracker relative to the camera:
  1. Set the [Socket](https://developers.antilatency.com/Terms/Socket_en.html) on the camera so that the [Environment](https://developers.antilatency.com/Terms/Environment_en.html) is clearly visible to the tracker when shooting;
  2. Measure the distance from the camera's POV (center of the matrix) to [the origin of the tracker's coordinates](https://developers.antilatency.com/Hardware/Alt_en.html#Coordinate_system), and the tracker's rotation relative to the camera;
  3. Record the measurements in the [Antilatency Service](https://developers.antilatency.com/Software/AntilatencyService_en.html): in the [Placements tab](https://developers.antilatency.com/Software/AntilatencyService/Placements_en.html) create a new [Placement](https://developers.antilatency.com/Terms/Placement_en.html) and record the data; the origin of the coordinates (the point between the eyes of the head model) will correspond to the camera's POV.
  4. Save the created Placement and set it "Set as default".

### g. Done! 
Now we can see the result of compositing with camera tracking, for example, in Editor: tab Composure Compositing -> Compositing Element (point 2.4) -> Outputs -> Outputs -> Add Element (+) -> OutputPass -> Player Viewport Compositing Output. The result of compositing will be output to the Editor window; you can also output the compositing to a separate window or to the [Sequencer](https://docs.unrealengine.com/5.1/en-US/real-time-compositing-with-sequencer-in-unreal-engine/).
