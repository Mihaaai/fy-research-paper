
<p>Open Academic Graph : open-source vesion of Microsoft’s <a href="https://www.openacademic.ai/oag/">https://www.openacademic.ai/oag/</a></p>

<p>Azure 4 Research : inchiriem resurse Cloud pentru un scop de cercetare <a href="https://azure4research.azurewebsites.net/proposal/">https://azure4research.azurewebsites.net/proposal/</a> (#academicgraph in the submission title, Data Science at the beginning of the title), 2015</p>
</li>
</ul>
<ol>
<li>
<p>Familiarizarea cu schemele json pentru bazele de date existente. Asta va folosi indiferent de modul ales de continuare.</p>
</li>
<li>
<p>schițarea unui proiect care ar putea folosi baza de date și estimarea (măcar grosieră) a resurselor hardware necesare.</p>
</li>
<li>
<p>Investigarea posibilității folosirii MAG pe platforma azure. Avantaje/dezavantaje. Familiarizarea cu api-ul lor, dar mai ales elaborarea unui draft al proiectului de la 2 pe care să îl trimitem la Microsoft.</p>
</li>
<li>
<p>Investigarea posibilității folosirii bazei de date open. Avantaje/dezavantaje. Căutarea unui limbaj/unei platforme de procesare potrivită.</p>
</li>
</ol>
<p>Analiza Big Data</p>
<p>Spark</p>
<p>What is it ? : <a href="https://www.infoq.com/articles/apache-spark-introduction">https://www.infoq.com/articles/apache-spark-introduction</a></p>
<p>Framework de procesare Big Data, open-source</p>
<p>Ruleaza peste infrastructura de date HDFS a Hadoop. Poate fi instalat atât într-un cluster YARN, cât și în versiune standalone ( să ruleze singur ?)</p>
<p>Suportă operații pe o varietate de tipuri de date(text, grafuri) și surse de date (batch, stream)</p>
<p>Suportă interogări SQL și operații complexe precum învățare automată și calcule pe grafuri</p>
<p>Spark folosește Grafuri Orientate Aciclice pentru procesarea datelor. Spre deosebire de Hadoop MapReduce, unde o serie de job-uri MapReduce trebuiau să se aștepte una pe alta, în spark job-urile pot lucra cu aceleași date din memorie.</p>
<p>Spark calculeaza procesările mai întâi în memoria internă, iar când aceasta e plină, scrie pe disk. Păstrarea rezultatelor în memoria internă ajută în cazul în care se lucrează cu același set de date de mai multe ori.</p>
<p>Spark folosește de asemenea tehnici de “lazy evaluation” pentru a optimiza evaluărea interogărilor de big data</p>
<p>Ofera API-uri in Scala, Python, R</p>
<p>Spark MLib, Spark GraphX</p>
<p>Arhitectura Spark :</p>
<p>Orice aplicație Spark conține un program driver care rulează funcția main() a utilizatorului și apoi execută operații în paralel pe cluster.</p>
<p>Spark este format din 3 componente, partea de date, parte de procesare API și partea de management. Prima parte se ocupa cu sistemul de stocare al datelor, a doua cu operațiile și procesările care au loc asupra datelor și a treia cu managementul resurselor peste un cluster / standalone.</p>
<p><img src="https://lh3.googleusercontent.com/kVaS5mEDHPqH8W95Ku6SDxBI6BfV5m_xY8Yhqt8HhtByof8YMG2qFRGk88tiWcbYGnJLKcqyb74qrL8rkQVNIyxg11kuFvcwPxtOhHr6tvQ6hyjYDOS_MxlX9WfxbONt9-hjBKZR" alt=""></p>
<p>RDD-urile sunt o primă abstractizare a Spark (Resilient Distributed Datasets). Un RDD este o colecție de elemente distribuite între nodurile sistemului, pe care se pot executa operații în paralel.</p>
<p>RDD-urile sunt create prin o transformare aplicată unui fișier din sistem sau unei colecții Scala din programul conducător.</p>
<p>RDD-urile sunt imutabile : o transformare asupra unui RDD întoarce un RDD nou, nu îl modifică pe cel anterior</p>
<p>RDD-urile suportă 2 tipuri de operații :</p>
<ul>
<li>
<p>Transformări :</p>
</li>
<li>
<p>Nu sunt calculate evaluări, este doar întors un nou RDD</p>
</li>
<li>
<p>map, filter, flatMap, groupByKey, reduceByKey, aggregateByKey, pipe, coalesce</p>
</li>
<li>
<p>Acțiuni :</p>
</li>
<li>
<p>Sunt calculate toate interogările de date din RDD și este întoarsă o valoare</p>
</li>
<li>
<p>reduce, collect, count, first, take, countByKey, foreach.</p>
</li>
</ul>
<p>Variabile partajate :</p>
<p>By default, when Spark runs a function in parallel as a set of tasks on different nodes, it ships a copy of each variable used in the function to each task.</p>
<p>O funcție rulată în paralel este văzuta ca o mulțime de task-uri pe mai multe noduri. În mod implicit, Spark transmite câte o copie a fiecărei variabile din funcție către fiecare task. Uneori, o variabila trebuie partajată între task-uri sau între un task și programul conducător. Astfel apar variabilele partajate (shared variables).</p>
<p>Exista 2 feluri de variabile partajate. Acestea se pot folosi pentru a partaja informații între nodurile aplicației.</p>
<ul>
<li>
<p>Variabile Broadcast : se menține variabila în cache-ul fiecărei mașini. Variabila este read-only. Aceasta nu se trimite prin copii odată cu fiecare task. Acest caz de variabilă se poate folosi în situația în care vrem să partajăm un data set mare între mai multe noduri.</p>
</li>
<li>
<p>Variabile Acumulatori : În acumulatori se pot adăuga valori de către fiecare nod (mașină) în paralel. Valoarea unui acumulator nu poate fi citită decât de nodul care a inițiat procesarea paralelizată. Pot fi folosiți, de exemplu, în aplicații de numărare sau însumare.</p>
</li>
</ul>
<p>Calculating resources requirements :</p>
<ul>
<li><a href="https://stackoverflow.com/questions/37871194/how-to-tune-spark-executor-number-cores-and-executor-memory">https://stackoverflow.com/questions/37871194/how-to-tune-spark-executor-number-cores-and-executor-memory</a></li>
</ul>
<p>Why Spark ?</p>
<ul>
<li>
<p>Apache Spark new World Record <a href="https://opensource.com/business/15/1/apache-spark-new-world-record">https://opensource.com/business/15/1/apache-spark-new-world-record</a></p>
</li>
<li>
<p>Spark RDD advantages over Hadoop <a href="https://www.quora.com/What-are-resilient-distributed-datasets-RDDs-How-do-they-help-Spark-with-its-awesome-speed">https://www.quora.com/What-are-resilient-distributed-datasets-RDDs-How-do-they-help-Spark-with-its-awesome-speed</a></p>
</li>
</ul>
<p>Future of Spark :</p>
<ul>
<li>Spark future with Databrick Inc. <a href="https://people.csail.mit.edu/matei/papers/2015/vldb_spark.pdf">https://people.csail.mit.edu/matei/papers/2015/vldb_spark.pdf</a></li>
</ul>
<p>Sources :</p>
<ul>
<li>
<p><a href="https://www.infoq.com/articles/apache-spark-introduction">https://www.infoq.com/articles/apache-spark-introduction</a></p>
</li>
<li>
<p><a href="https://spark.apache.org/docs/latest/rdd-programming-guide.html#overview">https://spark.apache.org/docs/latest/rdd-programming-guide.html#overview</a></p>
</li>
</ul>
<p>Next :</p>
<ul>
<li>
<p>Spark basics :</p>
</li>
<li>
<p><a href="https://spark.apache.org/docs/latest/rdd-programming-guide.html">https://spark.apache.org/docs/latest/rdd-programming-guide.html</a></p>
</li>
<li>
<p>Cluster setup :</p>
</li>
<li>
<p><a href="https://spark.apache.org/docs/latest/cluster-overview.html">https://spark.apache.org/docs/latest/cluster-overview.html</a></p>
</li>
<li>
<p><a href="https://spark.apache.org/docs/latest/submitting-applications.html">https://spark.apache.org/docs/latest/submitting-applications.html</a></p>
</li>
<li>
<p><a href="https://spark.apache.org/docs/latest/spark-standalone.html">https://spark.apache.org/docs/latest/spark-standalone.html</a></p>
</li>
<li>
<p><a href="https://spark.apache.org/docs/latest/configuration.html">https://spark.apache.org/docs/latest/configuration.html</a></p>
</li>
<li>
<p><a href="https://spark.apache.org/docs/latest/tuning.html">https://spark.apache.org/docs/latest/tuning.html</a></p>
</li>
</ul>
<p>Microsoft Azure for Research</p>
<p>Documentation : <a href="https://docs.microsoft.com/en-us/azure/">https://docs.microsoft.com/en-us/azure/</a></p>
<p>Arhitecture : <a href="https://docs.microsoft.com/en-us/azure/architecture/guide/">https://docs.microsoft.com/en-us/azure/architecture/guide/</a></p>
<p>Research Program : <a href="https://www.microsoft.com/en-us/research/academic-program/microsoft-azure-for-research/?from=http%3A%2F%2Fresearch.microsoft.com%2Fen-us%2Fprojects%2Fazure%2Fawards.aspx">https://www.microsoft.com/en-us/research/academic-program/microsoft-azure-for-research/?from=http%3A%2F%2Fresearch.microsoft.com%2Fen-us%2Fprojects%2Fazure%2Fawards.aspx</a></p>
<p>Cloud-ul permite o nouă abordare asupra arhitecturilor aplicațiilor moderne.</p>
<p><img src="https://lh6.googleusercontent.com/ee1kvHgV-ws9UqTSo9b8KrdCyLJ8ZVmQkw0GHwJy-eT1X-zViqS2LEuXFfmuQEYPFJZ7zyxr-PiTCJ9IOKpUdAZVzGVPGWnrzZnhbl1Jm4cPfY-hQL3ZbE9Eu6nU92w7PGYtLS3o" alt="fmm.PNG"></p>
<p><a href="https://docs.microsoft.com/en-us/azure/architecture/guide/">https://docs.microsoft.com/en-us/azure/architecture/guide/</a></p>
<p>Arhitectura cloud :</p>
<ul>
<li>Big Data <a href="https://docs.microsoft.com/en-us/azure/architecture/guide/architecture-styles/big-data">https://docs.microsoft.com/en-us/azure/architecture/guide/architecture-styles/big-data</a></li>
</ul>
<p><img src="https://lh4.googleusercontent.com/qYo0YsfG86FmwGHxldncI_kw_ZHZmpounFYzpKWwonIJVsfmannZUyXxtuIBeJF78M032UH7PEggnUNt7Ub28y_yo1DHpxuksf_Uc8BKq98PwW9tuT5yIVF67VzgxJiSjDOVgdvx" alt="fmm.PNG"></p>
<p>Orice aplicație de tip Big Data începe prin a avea o componentă de sursă de date. Sursele pot fi multiple și diverse, dar în cazul nostru datele sunt omogene și structurate.</p>
<p>După preluarea datelor din sursele necesare, acestea sunt  de obicei stocate într-un sistem distribuit, care poate susține cantități mari de date, în diverse formate. Pentru această componentă am putea folosi Azure Data Lake sau containere blob din Azure Storage.</p>
<p>Procesarea datelor presupune, în majoritatea cazurilor, citirea diverselor date și stocarea lor în alte fișiere, pregătindu-le astfel pentru analiza. Pentru partea de procesare putem folosi serviciile Spark / Hadoop din cluster-ul HDInsight.</p>
<p>După procesare, datele sunt acum structurate și permit preluarea acestora pentru analiza. Diverse programe de analiză sunt Azure SQL Data Warehouse și SQL Spark din HDInsight.</p>
<p>Scopul final al aplicațiilor de Big Data este de obicei obținerea de corelații sau fapte din mulțimea mare de date procesată. Pentru analiza datelor, se poate folosi un serviciu precum Azure Analysis Services, un notebook de analiză Jupyter. Pentru analiza datelor la scară largă, se poate folosi Microsoft R Server sau Spark standalone.</p>
<p>Ultima dar nu cea din urmă, orchestrarea aplicației este printre cele mai importante componente. Ea creează fluxurile între componentele aplicației și permite parcursul datelor de la surse către rapoartele analitice. Aceste procese pot fi făcute automat cu ajutorul Apache Data Factory sau Apache Oozie sau Sqoop.</p>
<p>Principii de programare :</p>
<ul>
<li>
<p>Paralelizarea procesării datelor și posibilitatea de a separa datele într-un sistem distribuit precum HDFS</p>
</li>
<li>
<p>Partitionarea datelor, pe principii precum timpul la care se vor procesa datele</p>
</li>
<li>
<p>Semantici schema-on-read</p>
</li>
<li>
<p>Transformă datele, și apoi extrage și încarcă</p>
</li>
<li>
<p>Uneori, se preferă timpuri mai lungi de procesare față de costul mai mare al sub-utilizării unui cluster. De exemplu, un proces poate dura 8 ore rulând pe patru noduri, dar le folosește pe primele două doar 2 ore. Astfel, dacă ar rula doar pe 2 noduri, ar rula mai încet, dar nu dublu ca timp. Deci în cazul de față, costul nu merită timpul de execuție.</p>
</li>
<li>
<p>Separarea resurselor de cluset. De exemplu, este posibil ca o aplicație ce folosește și Spark și Hive să meargă mai repede dacă cele 2 sunt folosite în clustere separate.</p>
</li>
<li>
<p>Datele personale sau securizate ar trebui prelucrate înainte să ajungă în Data Lake.</p>
</li>
</ul>
<p>Azure oferă servicii de IaaS, PaaS și FaaS.</p>
<p>IaaS (Infrastructure as a Service) oferă cele mai multe opțiuni de configurare. Serviciul acesta constă în valabilitatea unor mașini virtuale, pe care utilizatorii sunt liberi să și le configureze și să-și instaleze aplicațiile pe acestea.</p>
<p>PaaS (Platform) oferă un mediu de hosting manage-uit, în care utilizatorii doar specifică resursele de care au nevoie.</p>
<p>FaaS (Function)ia și mai multă responsabilitate de pe umerii utlizatorilor. Aceștia doar își trimit codul, iar serviciul alocă automat resursele de care codul are nevoie pentru a rula eficient.</p>
<p>Choosing compute options :</p>
<ul>
<li><a href="https://docs.microsoft.com/en-us/azure/architecture/guide/technology-choices/compute-comparison">https://docs.microsoft.com/en-us/azure/architecture/guide/technology-choices/compute-comparison</a></li>
</ul>
<p>Microsoft Academic Graph :</p>
<ul>
<li><a href="https://azure.microsoft.com/en-us/services/cognitive-services/academic-knowledge/">https://azure.microsoft.com/en-us/services/cognitive-services/academic-knowledge/</a></li>
</ul>
<p>Machine learning with spark in the cloud :</p>
<ul>
<li><a href="https://docs.microsoft.com/en-us/azure/machine-learning/preview/how-to-use-mmlspark">https://docs.microsoft.com/en-us/azure/machine-learning/preview/how-to-use-mmlspark</a></li>
</ul>
<p>Next :</p>
<ul>
<li>
<p>What technology compute to choose ?</p>
</li>
<li>
<p>What are the requirements for an Azure proposal ? (how much space, number of cores, hours of computation )</p>
</li>
</ul>
<p>Need 150 GB - 300 GB - 500 GB.</p>
<p>Hours of computation : 231 *</p>
<dl>
<dt>Citation needed : “Cloud computing resources were provided by a <a href="https://www.microsoft.com/en-us/research/academic-program/microsoft-azure-for-research/">Microsoft Azure for Research</a> award”</dt>
<dd>citation available here <a href="https://www.microsoft.com/en-us/research/project/microsoft-academic-graph/">https://www.microsoft.com/en-us/resea7  rch/project/microsoft-academic-graph/</a></dd>
</dl>
<p>Services Example :</p>
<ul>
<li>Data Science VM : <a href="https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.windows-data-science-vm">https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.windows-data-science-vm</a></li>
</ul>
<p>Processing Json Data with Spark SQL :</p>
<ul>
<li><a href="http://blog.antlypls.com/blog/2016/01/30/processing-json-data-with-sparksql/">http://blog.antlypls.com/blog/2016/01/30/processing-json-data-with-sparksql/</a></li>
</ul>
<p>Gettings started with Azure HDinsight :</p>
<ul>
<li>
<p>Introduction to Spark on HdInsight : <a href="https://docs.microsoft.com/en-us/azure/hdinsight/spark/apache-spark-overview">https://docs.microsoft.com/en-us/azure/hdinsight/spark/apache-spark-overview</a></p>
</li>
<li>
<p>Deploy HDInsight</p>
</li>
</ul>
<p><a href="https://docs.microsoft.com/en-us/azure/hdinsight/spark/apache-spark-jupyter-spark-sql">https://docs.microsoft.com/en-us/azure/hdinsight/spark/apache-spark-jupyter-spark-sql</a></p>
<ul>
<li>HDInsight with Blob Storage</li>
</ul>
<p><a href="https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-use-blob-storage">https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-use-blob-storage</a></p>
<ul>
<li>
<p>HDInsight with Data Lake Store <a href="https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal">https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal</a></p>
</li>
<li>
<p>Run Interactive Queries : <a href="https://docs.microsoft.com/en-us/azure/hdinsight/spark/apache-spark-load-data-run-query">https://docs.microsoft.com/en-us/azure/hdinsight/spark/apache-spark-load-data-run-query</a></p>
</li>
<li>
<p>Delete HdInsight cluster <a href="https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-delete-cluster">https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-delete-cluster</a></p>
</li>
</ul>

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTM1NzM1OTM2OSwxMTQzMjc0MDc4XX0=
-->