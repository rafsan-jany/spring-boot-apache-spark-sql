# spring-boot-big-data-apache-spark-sql
This project includes a brief but informative and simple explanation of Apache Spark and Spark SQL terms with Spring Boot implementation. There are few structured examples to clear the concept and terms in Apache Spark and Spark SQL altogether. This could be helpful for beginners as well as intermediates. If you do not know nothing about Apache Spark, you are welcome!

## Apache Spark <br/>

Apache Spark is a computational engine that can <br/>
 - schedule and distribute an application computation consisting of many tasks
 - split the computation into separate smaller tasks and run them in local or different servers within the cluster
 - maximize the power of parallelism
 - use in-memory storage for intermediate computation <br/>

That is why Apache Spark much faster than Hadoop MapReduce.

## Architecture

Apache Spark uses a **master-slave architecture**, meaning one node coordinates the computations that will execute in the other nodes.<br/>
![image](https://user-images.githubusercontent.com/27615818/125204831-f059e500-e2a0-11eb-935a-5a7b9ad48e40.png) <br/>

The **master node is the central coordinator** which will run the **driver program**. The driver program will **split a Spark job** into smaller **tasks and execute** them across many **distributed workers**. The driver program will **communicate** with the distributed worker nodes through a **SparkSession**. <br/>

There are ways to install and execute a Spark application using different configurations. You could configure Spark to run the driver program and executor in the same
single JVM in a laptop, different JVMs, or different JVMs across a cluster. The **local configuration** means the **driver program, spark executors, and cluster manager** will run all in the **same JVM** [(hellocodeclub)](https://www.hellocodeclub.com/apache-spark-java-tutorial-simplest-guide-to-get-started/#Set_Up_Spark_Java_Program). <br/>

### Another defination for better understanding : <br/>
A Spark Application consists of a **Driver Program** and a group of **Executors** on the cluster. The Driver is a process that executes the main program of your Spark application and creates the **SparkContext** that coordinates the execution of jobs (more on this later). The executors are processes running on the worker nodes of the cluster which are responsible for executing the tasks the driver process has assigned to them. <br/>
The cluster manager (such as Mesos or YARN) is responsible for the allocation of physical resources to Spark Applications [(towardsdatascience)](https://towardsdatascience.com/sparksession-vs-sparkcontext-vs-sqlcontext-vs-hivecontext-741d50c9486a). <br/>

![image](https://user-images.githubusercontent.com/27615818/125205348-8abb2800-e2a3-11eb-8224-3087a2688445.png) <br/>

## Entry Points
Every Spark Application needs an entry point that allows it to communicate with data sources and perform certain operations such as reading and writing data. <br/> 
### In **Spark 1.x**, three entry points were introduced:
1. **SparkContext** <br/>
The SparkContext is used by the Driver Process of the Spark Application in order to establish a communication with the cluster and the resource managers in order to coordinate and execute jobs. SparkContext also enables the access to the other two contexts, namely SQLContext and HiveContext (more on these entry points later on) [(towardsdatascience)](https://towardsdatascience.com/sparksession-vs-sparkcontext-vs-sqlcontext-vs-hivecontext-741d50c9486a). <br/>
In order to create a SparkContext, you will first need to create a Spark Configuration (SparkConf) as shown below: <br/>
    ```
        // CREATE SPARK CONTEXT
        SparkConf conf = new SparkConf().setAppName("AppName").setMaster("local[3]");
        JavaSparkContext sparkContext = new JavaSparkContext(conf);
    ```
2. **SQLContext** <br/>
SQLContext is the entry point to SparkSQL which is a Spark module for structured data processing. Once SQLContext is initialised, the user can then use it in order to           perform various “sql-like” operations over Datasets The SparkContext is used by the Driver Process of the Spark Application in order to establish a communication with the cluster and the resource managers in order to coordinate and execute jobs. SparkContext also enables the access to the other two contexts, namely SQLContext and HiveContext (more on these entry points later on)and Dataframes. it’s an entry point to Spark when you wanted to program and use Spark RDD. <br/>
In order to create a SQLContext, you first need to instantiate a SparkContext as shown below: <br/>
    ```
        // CREATE SPARK SQL CONTEXT
        SparkConf conf = new SparkConf().setAppName("AppName").setMaster("local[3]");
        JavaSparkContext sparkContext = new JavaSparkContext(conf);
        SQLContext sqlContext = new SQLContext(sparkContext);
    ```
3. **HiveContext** <br/>
If your Spark Application needs to communicate with Hive and you are using Spark < 2.0 then you will probably need a HiveContext. For Spark 1.5+, HiveContext also offers   support for window functions. SparkSession is an entry point to Spark and creating a SparkSession instance would be the first statement you would write to program with RDD, DataFrame and Dataset [(sparkbyexamples)](https://sparkbyexamples.com/spark/sparksession-vs-sparkcontext/). <br/>
     ```
        // CREATE SPARK HIVE CONTEXT
        SparkConf conf = new SparkConf().setAppName("AppName").setMaster("local[3]");
        JavaSparkContext sparkContext = new JavaSparkContext(conf);
        HiveContext hiveContext = new HiveContext(sparkContext);
    ```  
`Note that if you are using the spark-shell, SparkContext is already available through the variable called sc.` <br/>
### In **Spark 2.x**, A entry point was introduced: <br/>
1. **SparkSession** <br/>
Since Spark 2.x, a new entry point called SparkSession has been introduced that essentially combined all functionalities available in the three aforementioned contexts. SparkSession replaces both SQLContext and HiveContext. Additionally, it gives to developers immediate access to SparkContext. Spark Session follows builder pattern : 
     ```
        // CREATE SPARK SESSION
        SparkSession spark = SparkSession
                .builder()
                .appName("AppName")
                .master("local[2]")
                .getOrCreate();
    ```    
`Note that if you are using the spark-shell, SparkSession is already available through the variable called spark.` <br/>
### Why do I need Spark session when I already have Spark context?
1. to create multiple session (spark.newSession()) [(medium)](https://medium.com/@achilleus/spark-session-10d0d66d1d24) 
2. to add new single or multiple columns (withColumn() or on select()) [(sparkbyexamples)](https://sparkbyexamples.com/spark/spark-add-new-column-to-dataframe/) <br/>

## Important Keywords
       
**DataFrame** <br/>

**Spark SQL introduced a tabular data abstraction called a DataFrame since Spark 1.3.**
This API is useful when we want to handle structured and semi-structured, distributed data.
DataFrames store data in a more efficient manner than RDDs, this is because they use the immutable, in-memory, resilient, distributed, and parallel capabilities of RDDs but they also apply a schema to the data. DataFrame is a distributed collection of tabular data organized into rows and named columns. <br/>
**Since Spark 2.0 DataFrame became a Dataset of type Row,** `so we can use a DataFrame as an alias for a Dataset<Row>.` <br/>
 
**Datasets** <br/>
 
A dataset is a set of structured, strongly-typed collection of domain-specific objects that can be transformed in parallel using functional or relational operations. They provide the familiar object-oriented programming style plus the benefits of type safety since datasets can check syntax and catch errors at compile time.
**Dataset is an extension of DataFrame, thus we can consider a DataFrame an untyped view of a dataset.** Each Dataset also has an **untyped view called a DataFrame, which is a Dataset of Row**. <br/><br/>
Operations available on Datasets are divided into **transformations** and **actions**. **Transformations** are the ones that **produce new Datasets**, and **actions** are the ones that **trigger computation and return results**. Example **transformations** include **map, filter, select, and aggregate (groupBy).** Example **actions** include **count, show, or writing data out to file systems.**

Datasets are "lazy", i.e. computations are only triggered when an action is invoked. Internally, a Dataset represents a logical plan that describes the computation required to produce the data. When an action is invoked, Spark's query optimizer optimizes the logical plan and generates a physical plan for efficient execution in a parallel and distributed manner. <br/>
The most common way to create a Dataset is by pointing **Spark** to some files on storage systems, using the **read function** available on a **SparkSession.** <br/> <br/>
  `Dataset<Person> people = spark.read().parquet("...").as(Encoders.bean(Person.class));` [(spark)](https://spark.apache.org/docs/latest/api/java/index.html?org/apache/spark/sql/Dataset.html) <br/><br/>
  
**RDDs** <br/>  
 
The Resilient Distributed Dataset or RDD is Spark's primary programming abstraction. It represents a collection of elements that is: immutable, resilient, and distributed. RDDs are resilient because of Spark's built-in fault recovery mechanics. Spark relies on the fact that RDDs memorize how they were created so that we can easily trace back the lineage to restore the partition.
An RDD encapsulates a large dataset, Spark will automatically distribute the data contained in RDDs across our cluster and parallelize the operations we perform on them.
We can create RDDs only through operations of data in stable storage or operations on other RDDs.
Fault tolerance is essential when we deal with large sets of data and the data is distributed on cluster machines. 
There are two types of operations we can do on RDDs: **Transformations** and **Actions** [(baeldung)](https://www.baeldung.com/java-spark-dataframe-dataset-rdd).

` JavaRDD<String> videos = sparkContext.textFile("data/youtube/USvideos.csv"); `
  
**DataFrame, Datasets and RDDs Summary** <br/><br/>
To sum up, we should use DataFrames or Datasets when we need domain-specific APIs, we need high-level expressions such as aggregation, sum, or SQL queries. Or when we want type safety at compile time. On the other hand, we should use RDDs when data is unstructured and we don't need to implement a specific schema or when we need low-level transformations and actions [(baeldung)](https://www.baeldung.com/java-spark-dataframe-dataset-rdd). <br/>
  
**createorReplaceTempView**<br/>
 
Often we might want to **store the spark Dataframe as the table and query it**, to **convert Dataframe into temporary view** that is available for **only that spark session**, we use registerTempTable or **createOrReplaceTempView** (Spark > = 2.0) on our spark Dataframe. createOrReplaceTempView creates (or replaces if that view name already exists) a lazily evaluated "view" that you can then use like a hive table in Spark SQL. 
  
The CreateOrReplaceTempView will create a temporary view of the table on memory, it is not persistent at this moment but you can run SQL query on top of that. If you want to save it you can either persist or use saveAsTable to save. <br/>

First, we read data in csv format and then convert to data frame and create a temp view. <br/>
  `val data =  spark.read.format("csv").option("header","true").option("inferSchema","true").load("FileStore/campaign.csv")` <br/> <br/>
To print the schema <br/>
  `data.printSchema` <br/> <br/>
To create a temp view <br/>
  `data.createOrReplaceTempView("Data")` <br/><br/>
We can run SQL queries on top the table view we just created <br/>
  `%sql select Week as Date,Campaign Type,Engagements,Country from Data orderby Date asc` <br/><br/>
  
  
  
  ### Good Resource
  1. [tutorialkart](https://www.tutorialkart.com/apache-spark/spark-read-json-file-to-rdd/)
  


