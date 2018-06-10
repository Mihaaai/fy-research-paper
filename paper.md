# Analizarea rețelelor de coautori folosind Spark

## Motivația lucrării și studiul situației actuale

Rețelele personale sunt îndelung studiate de către sociologi pentru a descoperi tipare în modul în care oamenii își aleg prieteni sau colaboratori. O rețea de acest fel este ușor modelată sub forma unui graf, unde nodurile reprezintă oamenii și muchiile grafului reprezintă faptul că doi oameni au o relație de colaborare. Muchiile pot fi ponderate de o cuantificare a acestei colaborări. În cadrul unei rețele de copii de grădiniță, de exemplu, muchiile reprezintă relația de prietenie dintre doi copii, și pot fi ponderate de numărul jocurilor la care aleg să participe împreună fără influența profesorilor. 
Desigur, analiza acestor tipuri de rețele este utilă și în cuantificarea influențelor pe care unii colaboratori le pot exercita într-o rețea. Rețelele personale pot fi întocmite în toate mediile în care există oameni care colaborează unul cu celălalt, indiferent de forma de colaborare. Revenind la exemplul copiilor dintr-o grădiniță, se poate observa dacă copii din straturi sociale diferite (bogați, săraci, clasa medie) au o preferință de prieteni în același strat. [@cite : vezi mail-ul lui Șerbănuță, "Plan de atac", cu Gabi în CC, pentru un autor care după care e denumit un pachet în R]. [@todo : caut pe net încă câteva exemple de studii asupra rețelelor umane].

Lucrarea de față își propune să abordeze rețelele de coautori în cadrul cercetării.
Rețelele de coautori au fost studiate și în trecut, însă rezultatele oferite de aceste analize sunt contradictorii, mai ales cele privind modul în care autorii își aleg colaboratorii. Unele lucrări argumentează că legăturile de colaborare vin în principal din homofilie, preferința cercetătorilor de a lucra cu persoane cu interese sau personalități similare. [@cite] Alte păreri favorizează cauza efectului Mathew. Acest efect reprezintă tendința cercetătorilor de a-și alege parteneri de lucru din cei care au deja succes și vizibilitate, pentru a se propulsa ei înșiși. [@cite] De asemenea, analizele de până acum nu au o opinie comună în ceea ce privește măsura în care numărul de citări al unui articol este influențat de numărul de coautori al acestuia.
Toate acestea fac cercetarea rețelelor de coautori un domeniu activ, în care un nou studiu poate avea un impact puternic. Astfel, aplicația noastră își propune să ofere înspre analiză astfel de rețele și să adauge acestor rețele o caracteristică care le lipsește studiilor de până acum. 
Cele două efecte, cel de homofilie și Mathew, sunt greu de separat în rezultatele actuale pentru că studiile acestea oferă doar o perspectivă statică asupra rețelelor. Ele oferă o viziune asupra a multe și diverse populații de cercetători, însă doar la un moment dat în timp. Proiectul nostru își propune să adauge o dimensiune longitudinală asupra acestor rețele, oferind posibilitatea de a analiza evoluția acestora în timp. Folosind această abordare, ponderea efectelor și a influențelor coautorilor ar putea fi mult mai vizibilă. Spre exemplu, se poate urmări evoluția diverșilor caracteristici ale cercetătorilor unei rețele (e.g. numărul de citări ale articolelor lor sau indicele Hirsch) și se poate verifica corelația dintre creșterile valorilor acestor indicatori și adăugarea sau eliminarea unor coautori. [@cite : verifică sursele din propunerea de cercetare a lui Șerbănuță + Gabi]

Proiectul nostru constă în alcătuirea acestor rețele personale prin modelarea informațiilor preluate dintr-o bază de date și expunerea acestor rețele pentru o viitoare analiză statistică. În plus, dată fiind mărimea considerabilă a bazei de date alese ( aproximativ [@todo numărul de publicații și numărul de autori al MAG]) , ne asigurăm că viitoarele analize statistice vor fi relevante. Pentru a fi capabili să modelăm o cantitate atât de mare de date, am ales să folosim Spark, un framework de procesare distribuită, pe care îl vom descrie în detaliu în secțiunea referitoare la tehnologiile folosite. Deși rezultatele obținute vor fi specifice rețelelor de coautori, arhitectura aplicației poate fi aplicată oricărui tip de rețea personală, cu ușoare modificări pentru a putea acomoda noile surse de date și structura acestor date. 

În continuare vom prezenta structura rețelelor personale obținute, arhitectura aplicației și modul în care am format rețelele.

## Structura unei rețele de coautori

Rețeaua de coautori este modelată, ca majoritatea rețelelor personale, sub forma unui graf. Fiecare nod al grafului reprezintă un cercetător, iar o muchie între două noduri reprezintă faptul că doi cercetători au colaborat la scrierea unor articole. Graful este orientat și simetric, dat fiind că o legătură de la cercetătorul *X* la cercetătorul *Y* vine de la sine și cu legătura inversă : dacă *X* colaborează cu *Y*, atunci este de la sine înțeles că și *Y* colaborează cu *X*. 

În afară de această semantică, fiecare nod și muchie conține date relevante pentru analiză.
Pentru fiecare nod, reținem identificatorul unic al cercetătorului în baza de date, cât și date agregate despre acesta: numărul de publicații la care a participat, numărul de citări pe care le-au obținut aceste publicații și indicele Hirsch al autorului. Indicele Hirsch este o măsură de cuantificare atât a productivității, cât și a impactului unui autor. Acest indice a fost inventat în 2005 de către Jorge E. Hirsch și este definit astfel: un cercetător are un index Hirsch cu valoarea *h* dacă a publicat *h* lucrări dintre care toate au fost citate de cel puțin *h* ori. Este de menționat că acest indice este relevant doar în comparații referitoare la cercetători din același domeniu de studiu, deoarece convențiile de citare diferă de la un domeniu la altul.[@todo: un exemplu de diferență între felul în care citările funcționează în două domenii de studiu diferite] [@cite : https://en.wikipedia.org/wiki/H-index]

Rețele personale care pot fi extrase cu ajutorul aplicației noastre sunt de două tipuri: rețele statice, care reprezintă statusul unei rețele în momentul actual, și rețelele dinamice, care oferă informații despre evoluția statusului unei rețele în timp. Cele două tipuri diferă prin structura muchiilor care leagă două noduri în graf. Vom explica, pe rând, structura unei muchii pentru fiecare tip de rețea.
Pentru cele statice, muchiile au mai puține date agregate și astfel sunt mai ușor de calculat. Pe lângă identificatorii unici ai celor doi autori care colaborează, o muchie reține și numărul de publicații pe care cei doi autori le-au scris împreună. 
Pentru rețelele dinamice, reținem pentru fiecare muchie și o listă cu toți anii în care cei doi cercetători au scris articole împreună. 


Rețelele statice sunt practic incluse în cele din urmă, dar cuprind

## Bibliografie
- Tehnologii folosite  
    - Databricks
[Cum este optimizat Spark SQL și Dataframes](https://databricks.com/blog/2015/04/13/deep-dive-into-spark-sqls-catalyst-optimizer.html)
[Proiectul Tungsten](https://databricks.com/blog/2015/04/28/project-tungsten-bringing-spark-closer-to-bare-metal.html)
[Performanțele Databricks](https://people.csail.mit.edu/matei/papers/2015/vldb_spark.pdf)
[Introducere în Databricks și exemple de notebook-uri](https://docs.azuredatabricks.net/_static/notebooks/azure/gentle-introduction-to-apache-spark-azure.html)
    - Spark 
[Performanțe](https://opensource.com/business/15/1/apache-spark-new-world-record)
[Avantajele Spark față de Hadoop](https://www.quora.com/What-are-resilient-distributed-datasets-RDDs-How-do-they-help-Spark-with-its-awesome-speed)
[Documentația Spark](https://spark.apache.org/docs/latest/index.html)
[Dataframe, Dataset sau RDD](https://databricks.com/blog/2016/07/14/a-tale-of-three-apache-spark-apis-rdds-dataframes-and-datasets.html)

    - Azure
[Arhitecturi Big Data](https://docs.microsoft.com/en-us/azure/architecture/guide/architecture-styles/big-data)
[Tehnici de design pentru aplicații Cloud](https://docs.microsoft.com/en-us/azure/architecture/guide/)

## Citări
- **Arnab Sinha, Zhihong Shen, Yang Song, Hao Ma, Darrin Eide, Bo-June (Paul) Hsu, and Kuansan Wang. 2015. An Overview of Microsoft Academic Service (MAS) and Applications. In Proceedings of the 24th International Conference on World Wide Web (WWW ’15 Companion). ACM, New York, NY, USA, 243-246. DOI=http://dx.doi.org/10.1145/2740908.2742839**

