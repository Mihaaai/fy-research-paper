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
[@image: un graf orientat simetric cu nume de cercetători ca noduri]

În afară de această semantică, fiecare nod și muchie conține date relevante pentru analiză.
Pentru fiecare nod, reținem identificatorul unic al cercetătorului în baza de date, cât și date agregate despre acesta: numărul de publicații la care a participat, numărul de citări pe care le-au obținut aceste publicații și indicele Hirsch al autorului. Indicele Hirsch este o măsură de cuantificare atât a productivității, cât și a impactului unui autor. Acest indice a fost inventat în 2005 de către Jorge E. Hirsch și este definit astfel: un cercetător are un index Hirsch cu valoarea *h* dacă a publicat *h* lucrări dintre care toate au fost citate de cel puțin *h* ori. Este de menționat că acest indice este relevant doar în comparații referitoare la cercetători din același domeniu de studiu, deoarece convențiile de citare diferă de la un domeniu la altul.[@todo: un exemplu de diferență între felul în care citările funcționează în două domenii de studiu diferite] [@cite : https://en.wikipedia.org/wiki/H-index]

Rețele personale care pot fi extrase cu ajutorul aplicației noastre sunt de două tipuri: rețele statice, care reprezintă statusul unei rețele în momentul actual, și rețelele dinamice, care oferă informații despre evoluția statusului unei rețele în timp. Cele două tipuri diferă prin structura muchiilor care leagă două noduri în graf. Vom explica, pe rând, structura unei muchii pentru fiecare tip de rețea.
Muchiile rețelelor statice rețin, pe lângă identificatorii unici ai celor doi autori care colaborează, și numărul de publicații pe care cei doi autori le-au scris împreună. 
[@image: O diagramă UML cu o muchie statică între două noduri, care să arate structura nodurilor și a muchiilor]

Rețelele dinamice sunt mai complexe și modelează evoluția autorilor și a lucrărilor acestora pe parcursul unui interval de ani dat. Spre exemplu, o muchie a acestui tip de rețea va reține o structură care expune, pentru fiecare an din ultimii zece ani, numărul de citări al fiecărui articol la finalul acelui an și numărul de citări însumat al acestor articole în anul respectiv.
[@image: O diagramă UML cu structura unei muchii dinamice : 
	dictionar{ an: obiect{ 	- dictionar:{id_articol : nr_citări }
							- int: nr_citări_însumate}
			 }]

Datorită faptului că rețelele statice au mai puține date agregate decât cele dinamice, acestea sunt mai ușor de calculat și au o dimensiune mai redusă, ceea ce le face mai ușor de stocat și de expus grafic. Din această cauză, deși poate părea redundant faptul că informațiile din cele statice sunt incluse în informațiile din cele dinamice, ușurința lor de modelare le face excelente în scopul previzualizării. De exemplu, se pot urmări mai întâi factori precum dimensiunea rețelei și numărul mediu de publicații scrise în coutorat, și apoi a se decide dacă rețeaua în cauză merită să fie analizată mai amănunțit, folosind versiunea sa dinamică.

În cele ce urmează vom prezenta sursa noastră de date în modelarea acestor rețele, cât și modul în care le-am obținut și problemele întâlnite pe parcurs.

## Microsoft Academic Graph

Microsoft Academic Graph, prescurtat în continuare **MAG**, este o bază de date care conține informații despre jurnale, conferințe și publicații ștințifice, despre autorii acestora și despre diverse domenii de studiu ale cercetării. După cum spune și numele, informația este modelată sub forma unui graf cu enități ce modelează toate aceste activități de cercetare. Fiind preluate cu ajutorul motorului de căutare Bing, datele sunt updatate săptămânal și sunt folosite de către Microsoft în servicii precum Microsoft Academic, Cortana, Word sau însăși Bing. [@cite: https://www.microsoft.com/en-us/research/project/microsoft-academic-graph, https://docs.microsoft.com/en-us/azure/cognitive-services/academic-knowledge/home ] La momentul actual, baza de date are o dimensiune de *388 GB*, ceea ce o face să conțină o cantitate îndeajuns de mare de informație pentru a fi relevantă statistic. Acest factor, împreună cu prezența informațiilor despre citările fiecărui articol, fac din Microsoft Academic Graph o alegere potrivită pentru scopul nostru, extragerea rețelelor personale de coutori.

[????????? @question: Să adaug diagrama de mai jos sau credeți că este prea detaliată și aglomerată ????????? ]
[@image: Diagrama cu schema MAG-ului : https://docs.microsoft.com/en-us/azure/cognitive-services/academic-knowledge/images/academicgraphschema.png ]

Pentru a prelua datele din MAG, am avut la dispoziție două opțiuni: să folosim API-ul Microsoft Academic Knowledege sau să importăm întreaga baza de date în Azure Data Lake Store. 
API-ul constă în patru puncte de acces de tip REST pentru preluarea informației din MAG: *interpret*, *evaluate*, *calcHistogram* și *graph search*. Metoda *evaluate* este principala folosită, împreună cu o sintaxă specială, pentru a interoga graful, iar celelalte trei sunt folosite fie pentru a ușura sau rafina căutarea (*interpret*, respectiv *graph traversal*), fie pentru a oferi date statistice despre atributele entităților din graf(*calcHistogram*). [@plus: pot detalia despre cum funcționează celelalte metode ale API-ului][@cite : https://docs.microsoft.com/en-us/azure/cognitive-services/academic-knowledge/home ]

Existența acestor metode predefinite și accesibilitatea API-ului ne-au făcut să optăm pentru această modalitate de a accesa datele din MAG, însă la începutul proiectului am descoperit câteva dezavantaje majore ale acestei abordări. 
Pentru a înțelege și experimenta modul de funcționare al API-ului, am folosit *Postman*, un program folosit pentru a crea și a testa cereri HTTP. În urma acestor experimente, am observat că metoda *evaluate* putea întoarce cel mult 1000 de entități odată, ceea ce creea nevoia de multe cereri HTTP pentru a extrage cantitatea mare de date.[@plus: pot detalia folosirea API-ului explicând faptul că încercam un request de 3 ori până să-l consider "erorare" și să explic atributul offset al metodei *evaluate*] În plus, cererile HTTP eșuau spontan și repetat când foloseam metoda pentru a extrage mai mult de un milion de entități în total. Aceste fapte ar fi dus la o complexitate mărită a nivelului de tratare al erorilor aplicației, la un timp îndelungat de extragere a datelor cauzat de numărul mare de cereri necesare pentru a obtine toate datele și la eventuale întârzieri neașteptate din cauza erorilor API-ului. În al doilea rând, am ajuns la concluzia că, folosind metoda *evaluate* putem obține date doar despre două milioane de publicații ștințifice. În același timp, Microsoft Academic, deci și baza de date MAG peste care este construit, oferă informații despre peste 175 de milioane de publicații, la momentul actual.[@cite: https://academic.microsoft.com ]. 

Datorită acestor inconveniențe, am decis să folosim Azure Data Lake Store, denumit în continuare **ADLS**, pentru a obține datele din MAG. Deși setările necesare pentru a importa baza de date în ADLS au durat mai mult și a fost necesar să primim aprobarea echipei Microsoft, rezultatele au fost cu mult mai satisfăcătoare față de folosirea API-ului. 

## MAG în Azure Data Lake Store

ADLS este un serviciu de stocare în cloud, oferit de către Microsoft Azure. Serviciul asigură durabilitatea și siguranța datelor stocate prin copierea acestora. Redundanța astfel creată face posibilă recuperarea datelor în cazul erorilor neașteptate. De asemenea, serviciul poate stoca fișiere de orice mărime și de orice tip, de la câțiva kilobytes la petabytes. ADLS este conceput și optimizat pentru a expune date spre procesare și analiză, exact scopul proiectului nostru. De asemenea, sistemul de fișiere pe care îl folosește, *ADL*, este un sistem distribuit perfect compatibil cu Hadoop Distributed File System, acesta fiind compatibil la rândul lui cu Spark, framework-ul pe care îl folosim la procesarea distribuită necesară proiectului nostru. [@cite: https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-overview ]

Pentru a ne putea conecta la ADLS, trebuie să folosim sistemul de autentificare Azure Active Directory, sau **Azure AD**. Pentru acest sistem de autentificare avem două opțiuni: autentificare de tip *end-user*, sau *service-to-service*. Am ales să folosim a doua variantă dat fiind faptul că este metoda preferată de Microsoft pentru a încărca datele în ADLS, dar și pentru că este varianta care permite accesul automatizat la ADLS, fapt convenabil pentru aplicația noastră Spark pe care o vom descrie în capitolele următoare.
Autentificarea service-to-service are ca rezultat faptul că operațiile de citire și scriere în ADLS vor fi intermediate de o aplicație Active Directory Web, numită în continuare **ADW**, care primește drepturile necesare de operație asupra sistemului de fișiere. ADW este disponibilă în cloud și, după adăugarea drepturilor de acces necesare, alte aplicații se pot conecta la ea cu următoarele credențiale: id-ul și cheia secretă a ADW, cât și id-ul contului Azure care găzduiește ADW. După cum spuneam mai sus, echipa Microsoft folosește aceste credențiale pentru a se conecta la aplicația noastră ADW și a încărca, săptămânal, o copie actualizată a MAG-ului în serviciul nostru de stocare. [@cite: https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory#step-1-create-an-active-directory-web-application, https://microsoftdocs.github.io/MAG/Getting-MAG-On-ADLS ]

Acum, că avem baza de date MAG disponibilă în totalitate în ADLS, vom prezenta tehnicile folosite pentru a obține rețelele personale din această bază de date.

## Obținerea rețelelor statice

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

