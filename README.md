# Obama_face_dect
Detect obama faces base with faceCascade in python3

Background<br />
Object detection using Haar feature-based cascade classifiers is an effective face detection method proposed by Paul Viola and Michael Jones in their paper, “Rapid Object Detection using a Boosted Cascade of Simple Features” in 2001. The approach uses machine learning to construct a cascade function that is trained with many positive images (images of faces) and negative images (images without faces). Existing training files for front faces have already been generated and there are numerous publicly available trained object databases (pedestrian, car, sign, etc). An assignment later in the semester will include training your own individual classifier. For now, we will visit the step of extract features from an image and comparing them to a trained set. For this, some of the example Haar features shown in below image are used. Each feature filter (also called a kernel or a mask) generates a single value obtained by subtracting sum of pixels under white rectangle from sum of pixels under black rectangle.<br />
 
All possible sizes and locations of each kernel are used to calculate the feature results. This tends to amount to a lot of computation. For example, for a 24x24 window, there are over 160000 features. For each feature calculation, the system must find the sum of pixels under white and black rectangles. To solve this efficiently, the integral images is exploited to simplify the calculation of sum of pixels, how large may be the number of pixels, to an operation involving just four pixels. <br />

Among the numerous features calculated, most of them are irrelevant. For example, consider the image below. Top row shows two good features. The first feature selected seems to focus on the property that the region of the eyes is often darker than the region of the nose and cheeks. The second feature selected relies on the property that the eyes are darker than the bridge of the nose. But the same windows applying on cheeks or any other place is irrelevant. So how do we select the best features out of 160000+ features? The process is achieved by Adaptive Boosting (Adaboost).<br />
 


For AdaBoost, the system applies each and every feature on all the training images. For each feature, it finds the best threshold which will classify the faces to positive and negative. But obviously, there will be errors or misclassifications. The system select the features with minimum error rate, which means selecting the features that best classifies the face and non-face images. The process is not as simple as this. Each image is given an equal weight in the beginning. After each classification, weights of misclassified images are increased. Then again same process is done. New error rates are calculated and new weights. The process is continued until required accuracy or error rate is achieved or required number of features are found.<br />

In an image, most of the image region is non-face region. So it is a better idea to have a simple method to check if a window is not a face region. If it is not, discard it in a single shot. Don’t process it again. Instead focus on region where there can be a face. This way, we can find more time to check a possible face region. For this Viola and Jones introduced the concept of Cascade of Classifiers. Instead of applying all the 6000 features on a window, group the features into different stages of classifiers and apply one-by-one. Normally first few stages will contain very less number of features. If a window fails the first stage, discard it. This frees the system from consider running remaining features on it. If it passes, apply the second stage of features and continue the process. The window that passes all stages is a face region. <br />

The original Viola and Jones detector had 6000+ features with 38 stages with 1, 10, 25, 25 and 50 features in first five stages. (Two features in the above image is actually obtained as the best two features from Adaboost). According to authors, on an average, 10 features out of 6000+ are evaluated per sub-window. This is a simple intuitive explanation of how Viola-Jones face detection works. Read paper for more details.<br />


Haar-cascade Detection in OpenCV<br />

OpenCV comes with a trainer as well as detector. If you want to train your own classifier for any object like car, planes etc. you can use OpenCV to create one. Its full details are given here: Cascade Classifier Training. This assignment deals with detection not training. OpenCV already contains many pre-trained classifiers for face, eyes, smile etc. Those XML files are stored in opencv/data/haarcascades/ folder. Let’s create face and eye detector with OpenCV. First we need to load the required XML classifiers. Then load our input image (or video) in grayscale mode.<br />

To find the faces in the image, the cascade.detectMultiScale function will be used. If faces are found, it returns the positions of detected faces as Rect(x,y,w,h). Once we get these locations, we can create a Region of Interest (ROI) for the face and apply eye detection on this ROI (since eyes are always on the face.)<br />




Parameters:	<br />
cascade – Haar classifier cascade that can be loaded from XML or YAML file using When the cascade is not needed anymore, release it using cvReleaseHaarClassifierCascade(&cascade).<br />
image – Matrix of the type CV_8U containing an image where objects are detected.<br />
scaleFactor – Parameter specifying how much the image size is reduced at each image scale.<br />
minNeighbors – Parameter specifying how many neighbors each candidate rectangle should have to retain it. The higher the number the fewer false positives you will get. If this parameter is set to something larger than 0, the algorithm will group intersecting rectangles and only return those that have overlapping rectangle greater than or equal to the minimum number of nearest neighbors. If this parameter is set to 0, all rectangles will be returned and no grouping will happen, which means the results may have intersecting rectangles for a single face.<br />
flags – Parameter type of Haar detection<br />
minSize – Minimum possible object size. Objects smaller than that are ignored.<br />
maxSize – Maximum possible object size. Objects larger than that are ignored.<br />

Dataset:<br />
Label__Number Images<br />
A__91<br />
B__71<br />
C__81<br />
D__31<br />
E__101<br />
