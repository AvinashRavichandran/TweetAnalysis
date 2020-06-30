# TweetAnalysis

# Step 1: Download and Setup Spark and Scala IDE
Ensure you have JDK already setup, verify it using the below command, if not go ahead download and setup your JAVA_HOME environment variable.

```
$ java -version
java version “1.8.0_91”
$ echo JAVA_HOME
/Library/Java/JavaVirtualMachines/jdk1.8.0_91.jdk/Contents/Home
```

Download Spark from: http://spark.apache.org/downloads.html.
Run a test scala code from the downloaded directory using Spark Shell.

```
./bin/spark-shell
scala> sc.parallelize(1 to 1000).count()
res1: Long = 1000
```

Finally install scala IDE built on top of eclipse from: http://scala-ide.org/download/sdk.html

# Step 2: Create the project with Twitter App credentials

Create a twitter app using https://api.twitter.com/ and then fill in the following in a text file.
```
consumerKey 
consumerSecret 
accessToken 
accessTokenSecret
```

Setup a scala project in IDE and create the following scala code that prints out live tweets as they stream using Spark Streaming.

```
package com.hmovielabs.sparkstreaming

import org.apache.spark._
import org.apache.spark.SparkContext._
import org.apache.spark.streaming._
import org.apache.spark.streaming.twitter._
import org.apache.spark.streaming.StreamingContext._
import org.apache.log4j.Level
import Utilities._

object PrintTweets {
 
  def main(args: Array[String]) {

    // Configure Twitter credentials using twitter.txt
    setupTwitter()
    val ssc = new StreamingContext("local[*]", "PrintTweets", Seconds(1))
    setupLogging()

    // Create a DStream from Twitter using our streaming context
    val tweets = TwitterUtils.createStream(ssc, None)
    
    // Now extract the text of each status update into RDD's using map()
    val statuses = tweets.map(status => status.getText())
    statuses.print()
    
    ssc.start()
    ssc.awaitTermination()
  }  
}
```
# Step 3: Run the application
Building and running the above should continuous stream tweets to your console. 
