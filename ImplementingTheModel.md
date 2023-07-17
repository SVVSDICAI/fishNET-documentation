# Implementing the Model

FishNET V1 utilized two separate models [trained using Teachable Machine](./ApproachesToTrainingTheModel.md):
1. [**The Unary Classifier**](https://github.com/SVVSDICAI/FishNetStreamAnalyzer/tree/main/models/unary_classifier) - classifies images containing fish out of all video frames.
2. [**The Species Classifier**](https://github.com/SVVSDICAI/FishNetStreamAnalyzer/tree/main/models/species_classifier) - is passed all outputs of the unary classifier that have been determined to contain fish and groups them into one of the target species.

FishNET's two model approach grew out of the project's initial goal of simply identifying images containing fish.  Rather than starting over to classify species, we decided to use both models, and only try to classify the species if there is a high confidence of a fish being in the image.  The advantage of this approach is that in the future, we can replace the unary classifier with a simpler algorithm (movement detection for example), reducing the number of frames that must be analyzed by the species classifier.

Both models take an numpy array with shape `(224, 224)` as input storing the images.  Calling the unary classifier using `unary_model(input)[0][0]` returns a float representing the confidence that the image contains a fish.  The FishNET back-end takes all images that return such a result greater than 0.5 to contain fish and sends them for further analysis.

The species classifier returns a numpy array with one dimension corresponding to the number of classes (ie the number of fish species).  The current model has the classes ordered as:
```python
["White Sucker", "Black Bullhead Catfish", "Plains Topminnow", "Brown Trout", "Creek Chub"]
```
Calling the species classifier using `species_model(input)[0][i]` where `i` is the index of each species above, will return the confidence that the fish is that species.

The model performance on each class is displayed below:
![](https://lh6.googleusercontent.com/Qtrmi6Iy8N0d3nxzlSOAdf194RHkNEgnKcqygd9K57IWMPxAcCuoBJvHiKYMjt7xmvOSW6JvU3LqsHDco4PvXgX1FAuX_RzckxWSWr_SXUfUIZwTu7JLeObFeatZ4Zc3WJkdB1tiYHhewBYF0_RwRoo)
