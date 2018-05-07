Research

  

-   Graph Convolutional Networks : [https://tkipf.github.io/graph-convolutional-networks/](https://tkipf.github.io/graph-convolutional-networks/)
    
-   TensorFlow ( ML Library ) - distribution based on graphs, each graph node is a computation on a certain machine
    
-   Open Academic Graph : open-source vesion of Microsoft’s [https://www.openacademic.ai/oag/](https://www.openacademic.ai/oag/)
    
-   Microsoft Academic Graph API : api to get the academic data from Microsoft
    

(10, 000 transactions free per month, only available in West US) ( ~ 300 GB )

[https://azure.microsoft.com/en-us/services/cognitive-services/academic-knowledge/](https://azure.microsoft.com/en-us/services/cognitive-services/academic-knowledge/)

-   Harvard Data Science Course : [https://www.youtube.com/playlist?list=PLb4G5axmLqiuneCqlJD2bYFkBwHuOzKus](https://www.youtube.com/playlist?list=PLb4G5axmLqiuneCqlJD2bYFkBwHuOzKus)
    

  

Academic Search Engines :

-   Google Scholar : search scholarly articles; indexing is based on value and citation
    
-   Microsoft Academic : [https://academic.microsoft.com](https://academic.microsoft.com)
    

Large Datasets :

-   [https://www.quora.com/Where-can-I-find-large-datasets-open-to-the-public](https://www.quora.com/Where-can-I-find-large-datasets-open-to-the-public)
    
-   [https://github.com/caesar0301/awesome-public-datasets](https://github.com/caesar0301/awesome-public-datasets)
    

  

Answers :

-   Azure4Research : primim resursele pentru un an
    

-   Propunere de max. 1000 de cuvinte / 3 pagini
    
-   Cererile tehnice (cate core-uri, spațiu, etc.)
    
-   Contact : azurerfp@microsoft.com.
    
-   Primim și datele / acces la api prin acest grant ?
    

  

-   Azure Free Trial :
    

-   170 dollars to spend on their services for a month
    
-   One year access to some services ( MAG api possibly included ? )
    

  

-   Apache Spark on windows ; Dependencies : Scala, Hadoop winutils ; installation link here
    

[https://hernandezpaul.wordpress.com/2016/01/24/apache-spark-installation-on-windows-10/](https://hernandezpaul.wordpress.com/2016/01/24/apache-spark-installation-on-windows-10/)

  

-   Open Academic Graph : open-source vesion of Microsoft’s [https://www.openacademic.ai/oag/](https://www.openacademic.ai/oag/)
    
-   Azure 4 Research : rent Azure cloud computing for research [https://azure4research.azurewebsites.net/proposal/](https://azure4research.azurewebsites.net/proposal/) (#academicgraph in the submission title, Data Science at the beginning of the title), 2015
    

1. Familiarizarea cu schemele json pentru bazele de date existente. Asta va folosi indiferent de modul ales de continuare.

2. schițarea unui proiect care ar putea folosi baza de date și estimarea (măcar grosieră) a resurselor hardware necesare.

3. Investigarea posibilității folosirii MAG pe platforma azure. Avantaje/dezavantaje. Familiarizarea cu api-ul lor, dar mai ales elaborarea unui draft al proiectului de la 2 pe care să îl trimitem la Microsoft.

4. Investigarea posibilității folosirii bazei de date open. Avantaje/dezavantaje. Căutarea unui limbaj/unei platforme de procesare potrivită.

  
  
  

Analiza Big Data

  
  

Spark

  

What is it ? : [https://www.infoq.com/articles/apache-spark-introduction](https://www.infoq.com/articles/apache-spark-introduction)

Framework de procesare Big Data, open-source

  

Ruleaza peste infrastructura de date HDFS a Hadoop. Poate fi instalat atât într-un cluster YARN, cât și în versiune standalone ( să ruleze singur ?)

  

Suportă operații pe o varietate de tipuri de date(text, grafuri) și surse de date (batch, stream)

  

Suportă interogări SQL și operații complexe precum învățare automată și calcule pe grafuri

  

Spark folosește Grafuri Orientate Aciclice pentru procesarea datelor. Spre deosebire de Hadoop MapReduce, unde o serie de job-uri MapReduce trebuiau să se aștepte una pe alta, în spark job-urile pot lucra cu aceleași date din memorie.

  

Spark calculeaza procesările mai întâi în memoria internă, iar când aceasta e plină, scrie pe disk. Păstrarea rezultatelor în memoria internă ajută în cazul în care se lucrează cu același set de date de mai multe ori.

  

Spark folosește de asemenea tehnici de “lazy evaluation” pentru a optimiza evaluărea interogărilor de big data

  

Ofera API-uri in Scala, Python, R

  

Spark MLib, Spark GraphX

  
  
  

Arhitectura Spark :

  

Orice aplicație Spark conține un program driver care rulează funcția main() a utilizatorului și apoi execută operații în paralel pe cluster.

  

Spark este format din 3 componente, partea de date, parte de procesare API și partea de management. Prima parte se ocupa cu sistemul de stocare al datelor, a doua cu operațiile și procesările care au loc asupra datelor și a treia cu managementul resurselor peste un cluster / standalone.

![](https://lh3.googleusercontent.com/kVaS5mEDHPqH8W95Ku6SDxBI6BfV5m_xY8Yhqt8HhtByof8YMG2qFRGk88tiWcbYGnJLKcqyb74qrL8rkQVNIyxg11kuFvcwPxtOhHr6tvQ6hyjYDOS_MxlX9WfxbONt9-hjBKZR)

  

RDD-urile sunt o primă abstractizare a Spark (Resilient Distributed Datasets). Un RDD este o colecție de elemente distribuite între nodurile sistemului, pe care se pot executa operații în paralel.

  

RDD-urile sunt create prin o transformare aplicată unui fișier din sistem sau unei colecții Scala din programul conducător.

  

RDD-urile sunt imutabile : o transformare asupra unui RDD întoarce un RDD nou, nu îl modifică pe cel anterior

  

RDD-urile suportă 2 tipuri de operații :

-   Transformări :
    

-   Nu sunt calculate evaluări, este doar întors un nou RDD
    
-   map, filter, flatMap, groupByKey, reduceByKey, aggregateByKey, pipe, coalesce
    

-   Acțiuni :
    

-   Sunt calculate toate interogările de date din RDD și este întoarsă o valoare
    
-   reduce, collect, count, first, take, countByKey, foreach.
    

  
  
  
  

Variabile partajate :

  

By default, when Spark runs a function in parallel as a set of tasks on different nodes, it ships a copy of each variable used in the function to each task.

O funcție rulată în paralel este văzuta ca o mulțime de task-uri pe mai multe noduri. În mod implicit, Spark transmite câte o copie a fiecărei variabile din funcție către fiecare task. Uneori, o variabila trebuie partajată între task-uri sau între un task și programul conducător. Astfel apar variabilele partajate (shared variables).

  

Exista 2 feluri de variabile partajate. Acestea se pot folosi pentru a partaja informații între nodurile aplicației.

-   Variabile Broadcast : se menține variabila în cache-ul fiecărei mașini. Variabila este read-only. Aceasta nu se trimite prin copii odată cu fiecare task. Acest caz de variabilă se poate folosi în situația în care vrem să partajăm un data set mare între mai multe noduri.
    
-   Variabile Acumulatori : În acumulatori se pot adăuga valori de către fiecare nod (mașină) în paralel. Valoarea unui acumulator nu poate fi citită decât de nodul care a inițiat procesarea paralelizată. Pot fi folosiți, de exemplu, în aplicații de numărare sau însumare.
    

  

Calculating resources requirements :

-   [https://stackoverflow.com/questions/37871194/how-to-tune-spark-executor-number-cores-and-executor-memory](https://stackoverflow.com/questions/37871194/how-to-tune-spark-executor-number-cores-and-executor-memory)
    

Why Spark ?

-   Apache Spark new World Record [https://opensource.com/business/15/1/apache-spark-new-world-record](https://opensource.com/business/15/1/apache-spark-new-world-record)
    
-   Spark RDD advantages over Hadoop [https://www.quora.com/What-are-resilient-distributed-datasets-RDDs-How-do-they-help-Spark-with-its-awesome-speed](https://www.quora.com/What-are-resilient-distributed-datasets-RDDs-How-do-they-help-Spark-with-its-awesome-speed)
    

  

Future of Spark :

-   Spark future with Databrick Inc. [https://people.csail.mit.edu/matei/papers/2015/vldb_spark.pdf](https://people.csail.mit.edu/matei/papers/2015/vldb_spark.pdf)
    

  

Sources :

-   [https://www.infoq.com/articles/apache-spark-introduction](https://www.infoq.com/articles/apache-spark-introduction)
    
-   [https://spark.apache.org/docs/latest/rdd-programming-guide.html#overview](https://spark.apache.org/docs/latest/rdd-programming-guide.html#overview)
    

  
  
  
  

Next :

-   Spark basics :
    

-   [https://spark.apache.org/docs/latest/rdd-programming-guide.html](https://spark.apache.org/docs/latest/rdd-programming-guide.html)
    

- Cluster setup :

-   [https://spark.apache.org/docs/latest/cluster-overview.html](https://spark.apache.org/docs/latest/cluster-overview.html)
    
-   [https://spark.apache.org/docs/latest/submitting-applications.html](https://spark.apache.org/docs/latest/submitting-applications.html)
    
-   [https://spark.apache.org/docs/latest/spark-standalone.html](https://spark.apache.org/docs/latest/spark-standalone.html)
    
-   [https://spark.apache.org/docs/latest/configuration.html](https://spark.apache.org/docs/latest/configuration.html)
    

-   [https://spark.apache.org/docs/latest/tuning.html](https://spark.apache.org/docs/latest/tuning.html)
    

  
  
  

Microsoft Azure for Research

  

Documentation : [https://docs.microsoft.com/en-us/azure/](https://docs.microsoft.com/en-us/azure/)

Arhitecture : [https://docs.microsoft.com/en-us/azure/architecture/guide/](https://docs.microsoft.com/en-us/azure/architecture/guide/)

Research Program : [https://www.microsoft.com/en-us/research/academic-program/microsoft-azure-for-research/?from=http%3A%2F%2Fresearch.microsoft.com%2Fen-us%2Fprojects%2Fazure%2Fawards.aspx](https://www.microsoft.com/en-us/research/academic-program/microsoft-azure-for-research/?from=http%3A%2F%2Fresearch.microsoft.com%2Fen-us%2Fprojects%2Fazure%2Fawards.aspx)

  
  
  
  

Cloud-ul permite o nouă abordare asupra arhitecturilor aplicațiilor moderne.

  

![fmm.PNG](https://lh6.googleusercontent.com/ee1kvHgV-ws9UqTSo9b8KrdCyLJ8ZVmQkw0GHwJy-eT1X-zViqS2LEuXFfmuQEYPFJZ7zyxr-PiTCJ9IOKpUdAZVzGVPGWnrzZnhbl1Jm4cPfY-hQL3ZbE9Eu6nU92w7PGYtLS3o)

[https://docs.microsoft.com/en-us/azure/architecture/guide/](https://docs.microsoft.com/en-us/azure/architecture/guide/)

  
  
  
  
  

Arhitectura cloud :

-   Big Data [https://docs.microsoft.com/en-us/azure/architecture/guide/architecture-styles/big-data](https://docs.microsoft.com/en-us/azure/architecture/guide/architecture-styles/big-data)
    

  

![fmm.PNG](https://lh4.googleusercontent.com/qYo0YsfG86FmwGHxldncI_kw_ZHZmpounFYzpKWwonIJVsfmannZUyXxtuIBeJF78M032UH7PEggnUNt7Ub28y_yo1DHpxuksf_Uc8BKq98PwW9tuT5yIVF67VzgxJiSjDOVgdvx)

  

Orice aplicație de tip Big Data începe prin a avea o componentă de sursă de date. Sursele pot fi multiple și diverse, dar în cazul nostru datele sunt omogene și structurate.

  

După preluarea datelor din sursele necesare, acestea sunt  de obicei stocate într-un sistem distribuit, care poate susține cantități mari de date, în diverse formate. Pentru această componentă am putea folosi Azure Data Lake sau containere blob din Azure Storage.

  

Procesarea datelor presupune, în majoritatea cazurilor, citirea diverselor date și stocarea lor în alte fișiere, pregătindu-le astfel pentru analiza. Pentru partea de procesare putem folosi serviciile Spark / Hadoop din cluster-ul HDInsight.

  

După procesare, datele sunt acum structurate și permit preluarea acestora pentru analiza. Diverse programe de analiză sunt Azure SQL Data Warehouse și SQL Spark din HDInsight.

  

Scopul final al aplicațiilor de Big Data este de obicei obținerea de corelații sau fapte din mulțimea mare de date procesată. Pentru analiza datelor, se poate folosi un serviciu precum Azure Analysis Services, un notebook de analiză Jupyter. Pentru analiza datelor la scară largă, se poate folosi Microsoft R Server sau Spark standalone.

  

Ultima dar nu cea din urmă, orchestrarea aplicației este printre cele mai importante componente. Ea creează fluxurile între componentele aplicației și permite parcursul datelor de la surse către rapoartele analitice. Aceste procese pot fi făcute automat cu ajutorul Apache Data Factory sau Apache Oozie sau Sqoop.

  
  
  

Principii de programare :

  

-   Paralelizarea procesării datelor și posibilitatea de a separa datele într-un sistem distribuit precum HDFS
    
-   Partitionarea datelor, pe principii precum timpul la care se vor procesa datele
    
-   Semantici schema-on-read
    
-   Transformă datele, și apoi extrage și încarcă
    
-   Uneori, se preferă timpuri mai lungi de procesare față de costul mai mare al sub-utilizării unui cluster. De exemplu, un proces poate dura 8 ore rulând pe patru noduri, dar le folosește pe primele două doar 2 ore. Astfel, dacă ar rula doar pe 2 noduri, ar rula mai încet, dar nu dublu ca timp. Deci în cazul de față, costul nu merită timpul de execuție.
    
-   Separarea resurselor de cluset. De exemplu, este posibil ca o aplicație ce folosește și Spark și Hive să meargă mai repede dacă cele 2 sunt folosite în clustere separate.
    
-   Datele personale sau securizate ar trebui prelucrate înainte să ajungă în Data Lake.
    

  

Azure oferă servicii de IaaS, PaaS și FaaS.

IaaS (Infrastructure as a Service) oferă cele mai multe opțiuni de configurare. Serviciul acesta constă în valabilitatea unor mașini virtuale, pe care utilizatorii sunt liberi să și le configureze și să-și instaleze aplicațiile pe acestea.

PaaS (Platform) oferă un mediu de hosting manage-uit, în care utilizatorii doar specifică resursele de care au nevoie.

FaaS (Function)ia și mai multă responsabilitate de pe umerii utlizatorilor. Aceștia doar își trimit codul, iar serviciul alocă automat resursele de care codul are nevoie pentru a rula eficient.

  

Choosing compute options :

-   [https://docs.microsoft.com/en-us/azure/architecture/guide/technology-choices/compute-comparison](https://docs.microsoft.com/en-us/azure/architecture/guide/technology-choices/compute-comparison)
    

Microsoft Academic Graph :

-   [https://azure.microsoft.com/en-us/services/cognitive-services/academic-knowledge/](https://azure.microsoft.com/en-us/services/cognitive-services/academic-knowledge/)
    

Machine learning with spark in the cloud :

-   https://docs.microsoft.com/en-us/azure/machine-learning/preview/how-to-use-mmlspark
    

  

Next :

-   What technology compute to choose ?
    
-   What are the requirements for an Azure proposal ? (how much space, number of cores, hours of computation )
    

  

Need 150 GB - 300 GB - 500 GB.

  

Hours of computation : 231 *

  

Citation needed : “Cloud computing resources were provided by a [Microsoft Azure for Research](https://www.microsoft.com/en-us/research/academic-program/microsoft-azure-for-research/) award”

: citation available here [https://www.microsoft.com/en-us/resea7  rch/project/microsoft-academic-graph/](https://www.microsoft.com/en-us/research/project/microsoft-academic-graph/)

  

Services Example :

-   Data Science VM : [https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.windows-data-science-vm](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.windows-data-science-vm)
    

  

Processing Json Data with Spark SQL :

-   [http://blog.antlypls.com/blog/2016/01/30/processing-json-data-with-sparksql/](http://blog.antlypls.com/blog/2016/01/30/processing-json-data-with-sparksql/)
    

  

Gettings started with Azure HDinsight :

-   Introduction to Spark on HdInsight : [https://docs.microsoft.com/en-us/azure/hdinsight/spark/apache-spark-overview](https://docs.microsoft.com/en-us/azure/hdinsight/spark/apache-spark-overview)
    
-   Deploy HDInsight
    

[https://docs.microsoft.com/en-us/azure/hdinsight/spark/apache-spark-jupyter-spark-sql](https://docs.microsoft.com/en-us/azure/hdinsight/spark/apache-spark-jupyter-spark-sql)

-   HDInsight with Blob Storage
    

[https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-use-blob-storage](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-use-blob-storage)

-   HDInsight with Data Lake Store [https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)
    
-   Run Interactive Queries : [https://docs.microsoft.com/en-us/azure/hdinsight/spark/apache-spark-load-data-run-query](https://docs.microsoft.com/en-us/azure/hdinsight/spark/apache-spark-load-data-run-query)
    
-   Delete HdInsight cluster [https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-delete-cluster](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-delete-cluster)
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjEyNzIzMjgwM119
-->