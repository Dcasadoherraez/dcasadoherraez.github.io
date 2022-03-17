Image segmentation is a computer vision field that has been widely explored for different applications such as disease detection, self-driving vehicles and aerial map creation. It's main concept is to extract information from the image by identifying different regions  that correspond to different objects or classes. However, computing power plays an important role on being able to perform this pixelwise inference, thus being a limiting factor for small devices (wearables, phones, surveillance cameras...). 

The goal of this project is to attempt to solve this limitation for internet-connected edge devices by taking the image segmentation inference step to a remote GPU-capable machine and transmitting the encoded video via a TCP client-server network. The data flow pipeline consists of all the steps from the video capture to the image segmentation.  


GitHub Repo: [FlowCloud](https://github.com/Dcasadoherraez/cloud-image-segmentation)
