# Anomaly Detection using Spark MLlib and Spark Streaming
An Anomaly Detection example using Spark MLlib for training and Spark Streaming for testing. Slides are available <a href="http://www.slideshare.net/KeiraZhou2/anomaly-detection-using-spark-mllib-and-spark-streaming" target="_blank">here</a> 

## The Model

### Anomaly Detection Model
This model is using KMeans(<a href="http://spark.apache.org/docs/latest/mllib-clustering.html#k-means" target="_blank">Spark MLlib K-means</a>) approach and it is trained on **"normal"** dataset only. After the model is trained, the **centroid** of the "normal" dataset will be returned as well as a **threshold**. During the validation stage, any data points that are further than the **threshold** from the **centroid** are considered as **"anomalies"**.

### Dataset
The dataset is downloaded from <a href= "http://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html" target="_blank">KDD Cup 1999 Data for Anomaly Detection</a>.

**Training Set**: The training set is separated from the whole dataset with the data points that are labeled as **"normal"** only.

**Validation Set**: The validation set is using the whole dataset. All data points that are NOT labeled as **"normal"** are considered as **"anomalies"**.


## Spark
This application is for learning and testing purpose, thus the program is running as Spark local on a Mac pro. However, the code should be similar if deployed onto a cluster. 

### The Code
The majority of the code mainly follows the tutorial from Sean Owen, Cloudera (<a href= "https://www.youtube.com/watch?v=TC5cKYBZAeI" target="_blank">Video</a>, <a href= "http://www.slideshare.net/CIGTR/anomaly-detection-with-apache-spark" target="_blank">Slides-1</a>, <a href= "http://www.slideshare.net/cloudera/anomaly-detection-with-apache-spark-2" target="_blank">Slides-2</a>). Couple of modifcations have been made to fit personal interest:

- Instead of training multiple clusters, the code only trains on "normal" data points
- Only one cluster center is recorded and threshold is set to the last of the furthest 2000 data points
- During later validating stage, all points that are further than the threshold is labeled as "anomaly"

	
### Spark Application
The code is organized to run as a Spark Application. The application does "offline training" (Spark) and "online learning" (Spark Streaming).

**Training**: Training is run as a batch job. To compile and run, go to folder <a href= "https://github.com/keiraqz/anomaly-detection/tree/master/spark-train" target="_blank">spark-train</a> and run:

	sbt assambly
	sbt package
	spark-submit --class AnomalyDetection \
				target/scala-2.11/anomalydetection_2.11-1.0.jar

	
**Validation**: Validation is run as a streaming job. Currently the application reads the input data from a local file. In an ideal situation, the program will read the data from some ingestion tools such as Kafka. Also, the trained model (centroid and threshold) is also saved in a local file. In production, the information should be saved into a database. The output of the testing should also be saved into a database. To compile and run, go to folder <a href= "https://github.com/keiraqz/anomaly-detection/tree/master/streaming-validation" target="_blank">streaming-validation</a> and run:

	sbt assambly
	sbt package
	spark-submit --class AnomalyDetectionTest \
		 	--jars target/scala-2.11/AnomalyDetectionTest-assembly-1.0.jar \
		 		target/scala-2.11/anomalydetectiontest_2.11-1.0.jar



### Spark Shell
You can also play around with the code in Spark Shell. In terminal, start Spark shell:
	
	./spark-shell

Follow the steps of training in file: <a href= "https://github.com/keiraqz/anomaly-detection/blob/master/train-shell.scala" target="_blank">train-shell.scala</a>. Valication is in file: <a href= "https://github.com/keiraqz/anomaly-detection/blob/master/validation-shell.scala" target="_blank">validation-shell.scala</a>. Note that the validation code for Spark Shell does NOT use Spark Streaming. It's in batch processing form.
