1. <a href="#section:Requirements" data-reference-type="ref" data-reference="section:Requirements">Requirements</a>.
2. <a href="#section:HighLvl" data-reference-type="ref" data-reference="section:HighLvl">High level design</a>.
3. <a href="#section:Res" data-reference-type="ref" data-reference="section:Res">Results</a>.

## Requirements <span id="section:Requirements" label="section:Requirements"></span>

The first phase was to analyze the requirements. They could be summarized as follows:

1. Implement/Develop a streetview-like tool for a store (with sections, ailes, and products)
2. User should be able to move between 3D pictures through different parts of the store (switch between different positions and aisles)
3. No special computing requirements should be needed to run the app. Ex. No necessity for high RAM, good GPU, good CPU ...
4. The loading time of the app should be <= 10s (currently 200s)
5. User must be able to move quickly between steps
6. Scalable in number of aisles and new store sections
7. The images must be acquired from an image server database to allow for modification
8. Low number of users access at the same time (5 to 10)

Knowing the requirements, it's possible to study the available options in the market. The current implementation was based on [A-Frame](https://aframe.io/), however this API preloaded all the 3D images (each one being 6MB on HD) and the app startup time was 2 minutes in the best case. Other options such as [EvoVR](https://webobook.com/api-virtual-tours) and [TourCreator](https://arvr.google.com/tourcreator/) did not allow for store-specific customization and were oriented at businesses that wanted to showcase a single static tour with predefined images. 

As a result, building a framework from scratch would benefit meeting all the requirements while at the same time adding flexible customization possibilities for the future.

## High level design <span id="section:HighLvl" label="section:HighLvl"></span>

The system design can be divided into three main questions: 1) How can we make a web visualization in software? 2) What's the visualization strategy? 3) How can we handle that in harware?  

Let's try to tackle each of them briefly one by one.

# How do we create the visualization in software?

Starting from the most basic level, we could start from scratch using [OpenGL](https://www.opengl.org/) that allows 3D rendering of vector graphics. However, creating visualizations in such a low level framework would bring way too many obstacles at first. Moreover, it's not usable in web, which brings us to [WebGL](https://get.webgl.org/). Still, this keeps us in the vector-level renderization level. Going a level higher, and built on WebGL, there is the JavaScript library [Three.js](https://threejs.org/) that makes the process of building a 3D scenario more intuitive. 

At first, it might seem that Three.js might just be oriented towards simple web graphics. You can se below an example I built from the tutorials at [Three.js Fundamentals](https://threejs.org/manual/) to learn the basics on camera perspectives and movement synchronization.

<div  style="text-align: center">
<figure>
<img class="image-container" src="images/tank.gif" id="fig:pipeline" alt="Video transmision via TCP server (left branch) and image segmentation in the TCP client" /><figcaption aria-hidden="true">Tutorial example using a tank minigame</figcaption>
</figure>
</div>

Having this in mind, it is possible to create a world full of spheres, with each sphere's texture corresponding to one 360º shot. 

Furthermore, one of the great advantages of Three.js is that it uses GPU if available for rendering, which really speeds up the app in most computers. 

There's also another few facts. First, loading HD textures takes a long time. Second, the used must be able to switch between scenes quickly. This brings us to the need of having to use a low-res picture for an initial texture loading (like the blurry phase in Google Streetview), and then, if the user stays in place, the high-res photo can be loaded so that he can search for the item he's looking for. This is handled using asynchronous promises. The promise is ignored if the user switches fast and is retrieved if static.

# What's the visualization strategy for the store?

The question that comes up now is. How do we transition from one sphere to the next one? There are two main ways to do this, either shift the entire scene towards the camera, or move the camera to the next scene. We'll talk about caching and memory later, for now, let's say we are shifting the entire scene towards us. Same procedure can be applied to change aisles, which can be added to the sides. 

It's good to note that there is no need to have a specific number of shots for each aisle, as the visualization goes from the first to the last according to the time they were taken. Similar approach is taken for the aisles.

# How do we handle the visualization in hardware?

Until now everything has been just moving forward in the implementation. However, what happens if we try to cache N*10MB high-res images in RAM? The system crashes after a few walks around the store. Therefore it is necessary to drop the previous sphere-keeping strategy. A new sphere is created at every scene change and the previous ones are disposed. Future implementations could use local caching of the surrounding scenes.

## Results <span id="section:Res" label="section:Res"></span>

The results can be visualized above. The loading time of the app is <5s in the local machine. Transition between scenes is smooth allowing for quick movement, and some extra features such as zoom and aisle selection make the visualization easy to use!



