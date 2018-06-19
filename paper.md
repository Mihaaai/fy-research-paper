
# Analizarea rețelelor de coautori folosind Spark

## Motivația lucrării și studiul situației actuale

Rețelele personale sunt îndelung studiate de către sociologi pentru a descoperi tipare în modul în care oamenii își aleg prieteni sau colaboratori. O rețea de acest fel este ușor modelată sub forma unui graf, unde nodurile reprezintă oamenii și muchiile grafului reprezintă faptul că doi oameni au o relație de colaborare. Muchiile pot fi ponderate de o cuantificare a acestei colaborări. În cadrul unei rețele de copii de grădiniță, de exemplu, muchiile reprezintă relația de prietenie dintre doi copii, și pot fi ponderate de numărul jocurilor la care aleg să participe împreună fără influența profesorilor. 
Desigur, analiza acestor tipuri de rețele este utilă și în cuantificarea influențelor pe care unii colaboratori le pot exercita într-o rețea. Rețelele personale pot fi întocmite în toate mediile în care există oameni care colaborează unul cu celălalt, indiferent de forma de colaborare. Revenind la exemplul copiilor dintr-o grădiniță, se poate observa dacă copii din straturi sociale diferite (bogați, săraci, clasa medie) au o preferință de prieteni în același strat. [@cite : poate printre studiile lui Tom Snijders]. [@todo : caut pe net încă câteva exemple de studii asupra rețelelor umane].

Lucrarea de față își propune să abordeze rețelele de coautori în cadrul cercetării.
Rețelele de coautori au fost studiate și în trecut, însă rezultatele oferite de aceste analize sunt contradictorii, mai ales cele privind modul în care autorii își aleg colaboratorii. Unele lucrări argumentează că legăturile de colaborare vin în principal din homofilie, preferința cercetătorilor de a lucra cu persoane cu interese sau personalități similare. [@cite] Alte păreri favorizează cauza efectului Mathew. Acest efect reprezintă tendința cercetătorilor de a-și alege parteneri de lucru din cei care au deja succes și vizibilitate, pentru a se propulsa ei înșiși. [@cite] De asemenea, analizele de până acum nu au o opinie comună în ceea ce privește măsura în care numărul de citări al unui articol este influențat de numărul de coautori al acestuia.
Toate acestea fac cercetarea rețelelor de coautori un domeniu activ, în care un nou studiu poate avea un impact puternic. Astfel, aplicația noastră își propune să ofere înspre analiză astfel de rețele și să adauge acestor rețele o caracteristică care le lipsește studiilor de până acum. 
Cele două efecte, cel de homofilie și Mathew, sunt greu de separat în rezultatele actuale pentru că studiile acestea oferă doar o perspectivă statică asupra rețelelor. Ele oferă o viziune asupra a multe și diverse populații de cercetători, însă doar la un moment dat în timp. Proiectul nostru își propune să adauge o dimensiune longitudinală asupra acestor rețele, oferind posibilitatea de a analiza evoluția acestora în timp. Folosind această abordare, ponderea efectelor și a influențelor coautorilor ar putea fi mult mai vizibilă. Spre exemplu, se poate urmări evoluția diverșilor caracteristici ale cercetătorilor unei rețele (e.g. numărul de citări ale articolelor lor sau indicele Hirsch) și se poate verifica corelația dintre creșterile valorilor acestor indicatori și adăugarea sau eliminarea unor coautori. [@cite :  sursele menționate în propunerea de cercetare a dumneavoastră + Gabi]

Proiectul nostru constă în alcătuirea acestor rețele personale prin modelarea informațiilor preluate dintr-o bază de date și expunerea acestor rețele pentru o viitoare analiză statistică. În plus, dată fiind mărimea considerabilă a bazei de date alese, aproximativ 175 de milioane de publicații și 210 de milioane de articole, ne asigurăm că viitoarele analize statistice vor fi relevante. Pentru a fi capabili să modelăm o cantitate atât de mare de date, am ales să folosim Spark, un framework de procesare distribuită, pe care îl vom descrie în detaliu în secțiunea referitoare la tehnologiile folosite. Deși rezultatele obținute vor fi specifice rețelelor de coautori, arhitectura aplicației poate fi aplicată oricărui tip de rețea personală, cu ușoare modificări pentru a putea acomoda noile surse de date și structura acestor date. 

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
Autentificarea *service-to-service* are ca rezultat faptul că operațiile de citire și scriere în ADLS vor fi intermediate de o aplicație Active Directory Web, numită în continuare **ADW**, care primește drepturile necesare de operație asupra sistemului de fișiere. ADW este disponibilă în cloud și, după adăugarea drepturilor de acces necesare, alte aplicații se pot conecta la ea cu următoarele credențiale: id-ul și cheia secretă a ADW, cât și id-ul contului Azure care găzduiește ADW. După cum spuneam mai sus, echipa Microsoft folosește aceste credențiale pentru a se conecta la aplicația noastră ADW și a încărca, săptămânal, o copie actualizată a MAG-ului în serviciul nostru de stocare. [@cite: https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory#step-1-create-an-active-directory-web-application, https://microsoftdocs.github.io/MAG/Getting-MAG-On-ADLS ] De aici, datele vor fi preluate de componenta Spark a aplicației noastre și vor fi transformate în rețelele personale de coautori.

## Introducere în Apache Spark

Apache Spark este un framework folosit pentru procesarea Big Data, și reprezintă în același timp și principala tehnologie folosită în proiectul nostru pentru obținerea rețelelor personale. Performanțele sale și de ce am ales să folosim acest framework sunt detalii ce vor fi discutate pe larg în secțiunea despre tehnologii a lucrării de față. Dar pentru moment, pentru a ușura înțelegerea metodologiei folosite în acest proiect, vom explica care sunt principalele structuri de date folosite în Spark și principiile centrale ale acestui framework. Toate acestea au fost folosite extensiv în obținerea rețelelor personale și au jucat un rol important în întocmirea acestui proiect. 

În genere, o aplicație Spark este reprezentată de o componentă *driver* care apelează funcția *main()* a programului și execută pe nodurile de tip *worker* ale unui cluster, operațiile în paralalel care apar. Comunicarea dintre *driver* și nodurile *worker* este intermediată de un *Cluster Manager*, care orchestrează împărțirea sarcinilor în interiorul cluster-ului. *Cluster Managerul* poate fi manager-ul de sine stătător din Spark, sau orice manager compatibil, cum ar fi Mesos, Yarn, sau Kubernetes. [@cite: https://jaceklaskowski.gitbooks.io/mastering-apache-spark/content/spark-architecture.html, https://spark.apache.org/docs/latest/rdd-programming-guide.html#overview ]
[@image: arhitectura Spark https://jaceklaskowski.gitbooks.io/mastering-apache-spark/content/spark-architecture.html ]
 Aplicația noastră folosește Azure Databricks drept *cluster manager*. 

Principala structură de date folosită de Spark este un **RDD** (Resilient Distributed Dataset), o colecție imutabilă  și distribuită de elemente către nodurile cluster-ului, care suportă diverse operații în paralel. RDD-urile pot fi create având ca sursă un fișier dintr-un sistem de fișiere distribuit sau o secvență de elemente generată în *driver*. Folosind un mecanism de *cache*, un RDD poate fi salvat în memorie pentru a putea fi reutilizat cu ușurință de către sarcinile programului. De asemenea, după cum spune și termenul "Resilient", acest tip de colecție se recuperează automat în urma erorilor neașteptate ale cluster-ului.

Există două tipuri de operații aplicabile unui RDD: transformările și acțiunile. O transformare crează un nou RDD, cu elemente modificate plecând de la un RDD deja existent, în timp ce o acțiune execută calcule cu elementele unui RDD și întoarce valoarea obținută programului *driver*. 
Un mare avantaj al Spark este faptul că transformările sunt mereu executate în mod leneș. Aceasta înseamnă că procesarea elementelor și calculele necesare nu au loc la executarea transformării, ci doar când RDD-ul transformat este supus unei acțiuni. Până în acel moment, orice transformare se adaugă unui plan de execuție, fără a fi de fapt aplicată. Acest lucru ne permite să ne verificăm codul mult mai ușor și să modelăm cantități mari de date, precum sursa noastră MAG, fără ca nodurile să execute operațiile de fiecare dată. 
O acțiune cere ca un rezultat să se întoarcă la *driver*, și atunci planul de execuție al RDD-ului trebuie pus în aplicare pentru a avea un rezultat tangibil. Un dezavantaj al acestui proces este că toate transformările din plan trebuie executate mereu, de la cap la coadă, de fiecare dată când acțiunea este apelată, chiar dacă este aceeași. Dar dacă alegem să persistăm RDD-ul în memorie, atunci problema este rezolvată, rezultatele transformărilor fiind reținute după ce au fost executate prima oară.[ @cite: https://spark.apache.org/docs/latest/rdd-programming-guide.html#overview ]

Alte abstractizări importante în Spark sunt Dataset și DataFrame, care împart același API în implementarea lor. Acestea sunt și ele colecții imutabile și distribuite de elemente, precum RDD-ul, dar beneficiază de optimizări mai puternice în spate. Fac parte din Spark SQL, un modul care permite execuția de cereri SQL către sursele de date distribuite. Acest modul folosește caracteristici funcționale din Scala precum *pattern-matching* și *quasiquotes* pentru a optimiza cererile Spark. 
Dataset vine cu o optimizare în plus, în privința tipului de date al entității pe care o conține. Pentru a serializa obiectele din Dataset, este folosit un *Encoder* specializat, specific tipului obiectului dat spre serializare. Acest *Encoder* este reprezentat de cod generat dinamic și transformă obiectul într-o secvență de biți a cărei format îi permite să poată facă unele operații precum filtrarea și sortarea fără să mai deserializeze obiectul. [@cite: https://spark.apache.org/docs/latest/sql-programming-guide.html, https://databricks.com/blog/2015/04/13/deep-dive-into-spark-sqls-catalyst-optimizer.html ]
[@image: Optimizatorul Spark SQL : https://databricks.com/wp-content/uploads/2015/04/Screen-Shot-2015-04-12-at-8.41.26-AM-1024x235.png ]

După cum am spus, DataFrame-ul și Dataset-ul împart același API. Mai exact, DataFrame-ul este definit ca un Dataset care nu ține cont de tipul elementelor din interior. Elementele dintr-un DataFrame sunt de tipul *Row*, iar un *Row* poate conține oricâte atribute de orice tip, cu nume asociat fiecărui atribut. Se poate observa cu ușurință că această structură este foarte similară cu un tabel SQL și, de altfel, metode specifice SQL-ului (*select*, *join*) pot fi aplicate atât DataFrame-ului cât și Dataset-ului. Aceste colecții oferă o interfață facilă de a interacționa cu datele structurate și, împreună cu cererile Spark SQL, folosesc același motor de executare, ceea ce permite trecerea ușoară de la un una la alta. [@cite: https://databricks.com/blog/2016/07/14/a-tale-of-three-apache-spark-apis-rdds-dataframes-and-datasets.html ]
[@image: Dataset și Dataframe : https://databricks.com/wp-content/uploads/2016/06/Unified-Apache-Spark-2.0-API-1.png]

Deși RDD-ul este abstractizarea de bază a Spark, este și cea mai veche, celelalte două colecții fiind construite peste aceasta. De aceea, este recomandat ca RDD-urile să fie folosite doar pentru operații distribuite de nivel jos, cum ar fi salvarea colecțiilor distribuite în fișiere sau manipularea partițiilor ocupate de date în nodurile *worker*. Spre deosebire de RDD, API-ul Dataset este recomandat pentru oprații cu o abstractizare mai înaltă, precum analizarea și procesarea datelor într-un mod interactiv, ceea ce se potrivește mai bine nevoilor proiectului nostru. În plus, dat fiind că Dataset-urile sunt construite peste RDD-uri, cele două colecții pot fi transformate cu ușurință din una în cealaltă. [@cite: https://databricks.com/blog/2016/07/14/a-tale-of-three-apache-spark-apis-rdds-dataframes-and-datasets.html ] 

De-a lungul lucrării, vom preciza caracteristicile Spark folosite pentru a întocmi diverse acțiuni în stadiile de prelucrare, modelare și procesare a datelor. Astfel, informațiile de mai sus vor fi benefice în a înțelege de ce am ales anumite metode și de ce anumite abordări funcționează. 

## De la Azure Data Lake Store către Apache Spark

Pentru a putea citi datele din ADLS, am autorizat componenta noastră Spark folosind același tip de autentificare *service-to-service* către ADW, prezentată mai sus. Cele trei credențiale au fost adăugate în configurațiile *Spark Session* și *Spark Context*, două entități care dau mai departe configurațiile către principalele structuri de date folosite în Spark, *RDD*, *Dataframe* și *Dataset*.

Datele stocate în ADLS au o structură asemănătoare cu o bază de date relațională. Datele sunt reprezentate sub forma unor fișiere text, care conțin informații în formă tabulară: câte o enitate pe linie, cu atributele fiecărei entități despărțite de caracterul tab('\t'). Fiecare fișier conține informații despre un anumit tip de entitate. De exemplu, avem fișierul *Authors.txt*, cu informații despre autori, *Papers.txt*, cu date despre publicații, și *PapersAuthorsAffiliations.txt*, care servește drept o tabelă de legătură între autori, articole și parteneriatele autorilor.

Pentru a putea modela datele cu ușurință, am încărcat fișierele text în memorie, am segmentat liniile după caracterul separator '\t' și am creat câte o clasă de obiecte pentru fiecare entitate reprezentată de un fișier. Dată fiind dimensiunea mare a unor fișiere, de la 10 *GB* la aproape 70 *GB*, am stocat toate obiectele extrase dintr-un fișier într-un *Dataset* corespunzător clasei de obiecte. Astfel, fiecare fișier din baza de date are un Dataset corespunzător cu același nume în aplicația noastră. 

Deținând informația despre tipul obiectelor, procesările pe care le facem asupra colecției vor beneficia de optimizările *Encoder*-ului. În plus, evaluările leneșe specifice Spark ne dau posibilitatea de a efectua transformări peste aceste fișiere de dimensiuni mari într-un timp scurt, urmând să așteptăm doar atunci când apelăm o acțiune asupra Dataset-ului.

## Datele primare și agregate pentru construirea rețelei statice
Pentru a putea crea rețeaua personală a unui cercetător, trebuie să putem obține următoarele date primare :

- Articolele scrise de către un anumit cercetător
- Autorii unui anumit articol
- Cercetătorii care activează într-un anumit domeniu de studiu

Am definit funcții care obțin aceste date folosind Dataset-urile entităților principale din MAG, precum *Authors*, *Papers* și *FieldsOfStudy*, legate printr-o metodă *join* cu Dataset-urile de legătură *PaperAuthorAffiliation* și *PaperFieldsOfStudy*. Am denumit funcțiile reprezentativ, *authorsOfPaper*, *papersofAuthor* și *authorsOfField*. 
???[@plus: eventual explic complexitatea timp/spațiu a operațiilor]

Deși nu participă în algoritmul de construire a rețelei, avem nevoie și de date agregate referitoare la cercetători, pentru a fi expuse în rețea pentru viitoarele analize statistice:

- Numărul de citări al autorului
- Numărul de publicații al autorului
- Indicatorul Hirsch al autorului

Primele două sunt deja prezente în baza de date MAG ca atribute în tabelul *Authors*, deci foarte ușor de obținut. Vom descrie în continuare algoritmul folosit pentru a calcula indicatorul Hirsch.
Mai întâi, obținem toate publicațiile autorului și le ordonăm descrescător după numărul de citări obținute. În această așezare, indicele Hirsch este poziția ultimei publicații pentru care numărul de citări este mai mare sau egal cu numărul de ordine al poziției sale. De exemplu, să considerăm un autor cu patru publicații, fiecare având 60, 32, 10, respectiv 3 citări. Index-ul Hirsch al acestui cercetător este 3, pentru că lucrarea de pe poziția 4 are doar 3 citări( mai puține decât indicele poziției, 4), iar lucrarea de pe poziția 3 are 10 citări (mai multe decât indicele poziției, 3). [@cite: https://en.wikipedia.org/wiki/H-index ] Algoritmul este ușor de implementat, folosind funcția primară de mai sus, *papersOfAuthor*. 
[??????? @question: Credeți că este nevoie de specificarea complexității ????????] 
[@plus: eventual explic complexitatea timp/spațiu a operației]

Având la dispoziție datele primare și datele agregate, acum vom descrie modul de obținere a unei rețele personale.


## Algoritmul de construire al unei rețele statice

O rețea personală este reprezentată de o listă de noduri și o listă de muchii, ale căror structură am discutat-o în prealabil în secțiunea "*Structura unei rețele personale*". Pentru a afla coautorii unui cercetător, vom prelua toate publicațiile lui, și pentru fiecare din acestea vom alege ceilalți autori ai publicației înafară de el. Toate acestea sunt efectuate folsoind funcțiile din secțiunea de mai sus. 

Este posibil ca un cercetător să scrie mai multe publicații în colaborare cu un altul, așa că algoritmul urmărește, pentru fiecare publicație, două cazuri: coautorii noi descoperiți și cei deja descoperiți.
Pentru un coautor care nu a mai fost găsit printre autorii unei publicații până atunci, adăugăm două noi muchii direcționate între el și cercetătorul "sursă" al rețelei, ponderate cu 1 pentru că până acum au o singură publicație la care au colaborat amândoi. 

În cel de-al doilea caz, pentru un coautor care a fost descoperit înainte, nu mai este nevoie să adăugăm o nouă muchie, ci doar să le actualizăm pe cele vechi incrementând numărul publicațiilor scrise împreună cu 1. 

Dorim ca rețeaua personală a unui cercetător să evidențieze întreaga "familie" de colaborare, și de aceea, aplicăm recursiv algoritmul de mai sus pentru toți coautorii "sursei", de data aceasta avându-i pe fiecare dintre ei ca "sursă". Ulterior, vom calcula coautorii coautorilor și tot așa, până când nu mai descoperim noi coautori.

Pentru a implementa aceasta, vom folosi o listă care să rețină cercetătorii ce vor fi "surse". Inițial, în listă va fi cercetătorul pentru care este construită rețeaua, iar mai apoi, fiecare coautor nou descoperit va fi adăugat în această listă. După ce terminăm de parcurs publicațiile "sursei", o ștergem pe aceasta din listă și repetăm tot algoritmul pentru următoarea "sursă" din listă. Vom continua să iterăm acest algoritm până când lista de "surse" va fi goală. [@todo: acest algoritm trebuie implementat]
[@plus: Dacă mărimile acestor rețele personale sunt relativ mici, aș putea adăuga un algoritm pentru a calcula și a combina rețelele personale ale tuturor autorilor dintr-un domeniu de studiu. Mai este și considerentul de timp, dat fiind că mai trebuie să implementez și versiunea dinamică a unei rețele, să fac câteva grafice, și să fac câteva măsurări ale timpului de execuție]

## Construirea rețelelor dinamice

Pentru calculul rețelelor personale dinamice, vom avea nevoie de aceleași date primare ca și rețelele statice, cu o mențiune în plus: 
- Numărul de citări primite de un articol într-un anume an

Am creat o funcție care, folosind Dataset-urile *Papers* și *PaperReferences*, numără câte dintre toate articolele apărute într-un an dat citează un articol dat. Funcția folosește aceleași metode *join* și *filter* ca mai sus.
Folosind această informație primară, am calculat următoarele date agregate:
- Numărul total de citări pe care le deține un articol într-un anume an
- Indicatorul Hirsch al unui autor într-un anume an

Observăm că rezolvarea primei probleme duce la calculul simplu al celei de-a doua. Folosind același algoritm ca și pentru rețelele statice, putem calcula acest indicator pentru orice autor odată ce avem o listă cu numărul total de citări ale fiecărui articol al său într-un anume an. Așa că vom explica în detaliu doar tehnica folosită pentru prima problemă.

Utilizând funcția primară definită mai sus, putem calcula numărul de citări primit de un articol în fiecare an de la publicarea sa până în prezent. Având aceste numere, obținem numărul cumulat de citări într-un anumit an însumând toate citările la anul publicației până în acel an. Pentru că numărul de citări cumulate într-un an depinde mereu de numărul de citări cumulate în anul anterior, observăm că funcția noastră are proprietatea Markov. [@cite: https://en.wikipedia.org/wiki/Markov_property ]. Pentru a evita repetarea acelorași calcule de mai multe ori, funcția noastră întoarce o secvență cu numărul de citări cumulate pentru fiecare an de la publicația articolului până în prezent. Iar în continuare, de fiecare dată când avem nevoie de datele agregate în discuție, este suficient să accesăm această secvență, nemaifiind nevoie să reapelăm funcția. Pentru a beneficia de timp de acces mic, am modelat secvența sub forma unei tabele de dispersie, indexată după an.

Construirea unei rețele dinamice este foarte asemănătoare cu cea a unei rețele statice, construirea muchiilor fiind singura diferență.
Pentru fiecare coautor nou găsit, calculăm tabela de dispersie prezentată mai sus pentru fiecare articol de-al său scris în colaborare cu cercetătorul "sursă". Apoi, modelăm această tabelă conform structurii discutate în *Structura unei rețele personale*, și o adăugăm ca pondere a muchiei dintre coautor și sursă.
Ca în cazul rețelelor statice, întreg algoritmul este repetat recursiv pentru a adăuga în rețea toată "familia" de colaborare a "sursei".


## Utilizarea rețelelor personale

Scopul proiectului de față este obținerea rețelelor personale, ci nu analizarea lor statistică, acest fapt fiind lăsat la latitudinea viitorilor utilizatori. Pentru aceștia, am întocmit această secțiune pentru a exemplifica cum aceste rețele pot fi extrase din mediul Azure sau chiar analizate în continuare folosind Spark.

Creatorii Apache Spark au întomit o librărie pentru procesări distribuite de grafuri, numită GraphFrames. Este ușor de văzut că, dat fiind că rețelele personale extrase de noi iau forma unui graf, această librărie li se potrivește perfect. Librăria oferă diverși algoritmi specifici grafurilor, precum *PageRank*, calcularea celui mai scurt drum, numărarea triunghiurilor sau găsiri de tipare. Un GraphFrame se construiește dintr-un DataFrame de noduri, care trebuie să conțină un atribut identificator unic, și un DataFrame de muchii, care să conțină două atribute cu identificatorul unic al nodului sursă, respectiv nodului destinație. Dat fiind că rețelele noastre vin sub forma unor Dataset-uri cu entități corespunzătoare, transformarea acestora în DataFrame-uri se face folosind o conversie standard din Spark. [@cite: https://graphframes.github.io/ ] Acestea sunt metode de analiză structurală, mai degrabă decât statistică. 

Pentru cea din urmă se poate folosi programul Siena, disponibil și ca o librărie pentru limbajul R (RSiena). Acest program este conceput de mai mulți cercetători, printre care și Tom Snijders, și se concentrează pe analiza statistică a rețelelor sociale. Siena explorează rețelele longitudinal, de-a lungul unei perioade de timp, iar modelele sale de noduri sunt structurate ca un lanț Markov, afișând informația cumulată de la un moment la altul. [@cite: https://www.stats.ox.ac.uk/~snijders/siena/ ] Astfel, modul în care am construit rețelele le face compatibile cu acest program.	

Pentru a putea fi analizate de Siena, rețelele trebuie stocate local. Ceea ce nu este o problemă, pentru că, din mediul Databricks, odată ce am obținut rețelele, le putem scrie într-un fișier în ADLS, de unde le putem descărca cu un singur click.

## Vizualizarea rețelelor personale

De asemenea, poate fi util să creezi vizualizări ale rețelelor personale, pentru a le prezenta înainte sau după analiză statistică. Dată fiind modelarea lor sub forma unui graf, este convenabil să le reprezentăm în format GEXF(Graph Exchange XML Format). Acesta este un format similar XML-ului în care informații despre nodurile, muchiile și datele agregate ale acestora pot fi salvate. În această formă, grafurile pot fi încărcate în diverse programe de vizualizare. 
Unul din acestea este Gephi, o aplicație open-source pentru modelarea și analizarea rețelelor. Folosind acest program, utilizatorii pot partiționa rețelele după anumite atribute, pot controla mărimea și culoarea nodurilor cât și a muchiilor. [@cite: https://gephi.org/users/publications/ ]. 
În plus, dezvoltatorii pot folosi rețelele în acest format pentru a oferi rețelele spre analiză într-un client web, folosind librăria de JavaScript *Linkurious*. Librăria este construită pentru grafuri cu până la miliarde de noduri, deci rețelele noastre personale vor putea fi procesate fără probleme. Totuși *Linkurious* este o librărie complexă care oferă mai multe funcționalități, precum filtrare, securitatea informațiilor și detectarea anomaliilor. Pentru cei care vor doar să obțină o vizualizare interactivă, recomandăm librăria de JavaScript *D3* (Data-Driven-Documents). Această librărie este open-source și este folosită pentru vizualizări diverse, de la histograme la rețele, deci are un grad mare de libertate.

## Scalabilitatea proiectului 
[@todo: Aici adaug niște comparații de timp pentru rețele personale mici, comparativ cu rețele mari. Și comparații pentru sample-urile(de 1000) de elemente și întreaga bază de date. Până acum nu am folosit sample-urile, de aceea nu am descris nicăieri obținerea lor de până acum. Dacă după ce termin algoritmii, văd o ușurință în folosirea acelor sample-uri pentru testare, aș putea adăuga o secțiune și despre acestea].

## Tehnologii folosite
[@todo: despre Databricks și performanțele sale și de ce am ales-o în comparație cu un environment standalone]
[@todo: despre Spark și performanțele sale și de ce l-am ales în comparație cu Hadoop, de exemplu]
[@todo: de ce am ales Scala în comparație cu Python, pentru Spark]
[????? @question: La începutul anului, mi-ați spus că este de preferat să prezint tehnologiile doar din punct de vedere al motivelor pentru care au fost alese. Rămâne valabilă această opinie ?????]

[?????? @question: Ce credeți, să adaug informații despre bursa Azure4Research ? Cât să motivez alegerea platformei Azure pentru proiect ??????] 
[@todo: de ce am ales Azure(bursa Azure4Research)]


## Bibliografie
- Tehnologii folosite  
    - Databricks
[Performanțele Databricks](https://people.csail.mit.edu/matei/papers/2015/vldb_spark.pdf)
[Introducere în Databricks și exemple de notebook-uri](https://docs.azuredatabricks.net/_static/notebooks/azure/gentle-introduction-to-apache-spark-azure.html)
    - Spark 
[Performanțe](https://opensource.com/business/15/1/apache-spark-new-world-record)
[Avantajele Spark față de Hadoop](https://www.quora.com/What-are-resilient-distributed-datasets-RDDs-How-do-they-help-Spark-with-its-awesome-speed)
[Documentația Spark](https://spark.apache.org/docs/latest/index.html)
[Dataframe, Dataset sau RDD](https://databricks.com/blog/2016/07/14/a-tale-of-three-apache-spark-apis-rdds-dataframes-and-datasets.html)
[Cum este optimizat Spark SQL și Dataframes](https://databricks.com/blog/2015/04/13/deep-dive-into-spark-sqls-catalyst-optimizer.html)
[Proiectul Tungsten](https://databricks.com/blog/2015/04/28/project-tungsten-bringing-spark-closer-to-bare-metal.html)
    - Azure
[Arhitecturi Big Data](https://docs.microsoft.com/en-us/azure/architecture/guide/architecture-styles/big-data)
[Tehnici de design pentru aplicații Cloud](https://docs.microsoft.com/en-us/azure/architecture/guide/)

## Citări
=======
# Analizarea rețelelor de coautori folosind Spark

## Motivația lucrării și studiul situației actuale

Rețelele personale sunt îndelung studiate de către sociologi pentru a descoperi tipare în modul în care oamenii își aleg prieteni sau colaboratori. O rețea de acest fel este ușor modelată sub forma unui graf, unde nodurile reprezintă oamenii și muchiile grafului reprezintă faptul că doi oameni au o relație de colaborare. Muchiile pot fi ponderate de o cuantificare a acestei colaborări. În cadrul unei rețele de copii de grădiniță, de exemplu, muchiile reprezintă relația de prietenie dintre doi copii, și pot fi ponderate de numărul jocurilor la care aleg să participe împreună fără influența profesorilor. 
Desigur, analiza acestor tipuri de rețele este utilă și în cuantificarea influențelor pe care unii colaboratori le pot exercita într-o rețea. Rețelele personale pot fi întocmite în toate mediile în care există oameni care colaborează unul cu celălalt, indiferent de forma de colaborare. Revenind la exemplul copiilor dintr-o grădiniță, se poate observa dacă copii din straturi sociale diferite (bogați, săraci, clasa medie) au o preferință de prieteni în același strat. [@cite : poate printre studiile lui Tom Snijders]. [@todo : caut pe net încă câteva exemple de studii asupra rețelelor umane].

Lucrarea de față își propune să abordeze rețelele de coautori în cadrul cercetării.
Rețelele de coautori au fost studiate și în trecut, însă rezultatele oferite de aceste analize sunt contradictorii, mai ales cele privind modul în care autorii își aleg colaboratorii. Unele lucrări argumentează că legăturile de colaborare vin în principal din homofilie, preferința cercetătorilor de a lucra cu persoane cu interese sau personalități similare. [@cite] Alte păreri favorizează cauza efectului Mathew. Acest efect reprezintă tendința cercetătorilor de a-și alege parteneri de lucru din cei care au deja succes și vizibilitate, pentru a se propulsa ei înșiși. [@cite] De asemenea, analizele de până acum nu au o opinie comună în ceea ce privește măsura în care numărul de citări al unui articol este influențat de numărul de coautori al acestuia.
Toate acestea fac cercetarea rețelelor de coautori un domeniu activ, în care un nou studiu poate avea un impact puternic. Astfel, aplicația noastră își propune să ofere înspre analiză astfel de rețele și să adauge acestor rețele o caracteristică care le lipsește studiilor de până acum. 
Cele două efecte, cel de homofilie și Mathew, sunt greu de separat în rezultatele actuale pentru că studiile acestea oferă doar o perspectivă statică asupra rețelelor. Ele oferă o viziune asupra a multe și diverse populații de cercetători, însă doar la un moment dat în timp. Proiectul nostru își propune să adauge o dimensiune longitudinală asupra acestor rețele, oferind posibilitatea de a analiza evoluția acestora în timp. Folosind această abordare, ponderea efectelor și a influențelor coautorilor ar putea fi mult mai vizibilă. Spre exemplu, se poate urmări evoluția diverșilor caracteristici ale cercetătorilor unei rețele (e.g. numărul de citări ale articolelor lor sau indicele Hirsch) și se poate verifica corelația dintre creșterile valorilor acestor indicatori și adăugarea sau eliminarea unor coautori. [@cite :  sursele menționate în propunerea de cercetare a dumneavoastră + Gabi]

Proiectul nostru constă în alcătuirea acestor rețele personale prin modelarea informațiilor preluate dintr-o bază de date și expunerea acestor rețele pentru o viitoare analiză statistică. În plus, dată fiind mărimea considerabilă a bazei de date alese, aproximativ 175 de milioane de publicații și 210 de milioane de articole, ne asigurăm că viitoarele analize statistice vor fi relevante. Pentru a fi capabili să modelăm o cantitate atât de mare de date, am ales să folosim Spark, un framework de procesare distribuită, pe care îl vom descrie în detaliu în secțiunea referitoare la tehnologiile folosite. Deși rezultatele obținute vor fi specifice rețelelor de coautori, arhitectura aplicației poate fi aplicată oricărui tip de rețea personală, cu ușoare modificări pentru a putea acomoda noile surse de date și structura acestor date. 

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
Autentificarea *service-to-service* are ca rezultat faptul că operațiile de citire și scriere în ADLS vor fi intermediate de o aplicație Active Directory Web, numită în continuare **ADW**, care primește drepturile necesare de operație asupra sistemului de fișiere. ADW este disponibilă în cloud și, după adăugarea drepturilor de acces necesare, alte aplicații se pot conecta la ea cu următoarele credențiale: id-ul și cheia secretă a ADW, cât și id-ul contului Azure care găzduiește ADW. După cum spuneam mai sus, echipa Microsoft folosește aceste credențiale pentru a se conecta la aplicația noastră ADW și a încărca, săptămânal, o copie actualizată a MAG-ului în serviciul nostru de stocare. [@cite: https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory#step-1-create-an-active-directory-web-application, https://microsoftdocs.github.io/MAG/Getting-MAG-On-ADLS ] De aici, datele vor fi preluate de componenta Spark a aplicației noastre și vor fi transformate în rețelele personale de coautori.

## Introducere în Apache Spark

Apache Spark este un framework folosit pentru procesarea Big Data, și reprezintă în același timp și principala tehnologie folosită în proiectul nostru pentru obținerea rețelelor personale. Performanțele sale și de ce am ales să folosim acest framework sunt detalii ce vor fi discutate pe larg în secțiunea despre tehnologii a lucrării de față. Dar pentru moment, pentru a ușura înțelegerea metodologiei folosite în acest proiect, vom explica care sunt principalele structuri de date folosite în Spark și principiile centrale ale acestui framework. Toate acestea au fost folosite extensiv în obținerea rețelelor personale și au jucat un rol important în întocmirea acestui proiect. 

În genere, o aplicație Spark este reprezentată de o componentă *driver* care apelează funcția *main()* a programului și execută pe nodurile de tip *worker* ale unui cluster, operațiile în paralalel care apar. Comunicarea dintre *driver* și nodurile *worker* este intermediată de un *Cluster Manager*, care orchestrează împărțirea sarcinilor în interiorul cluster-ului. *Cluster Managerul* poate fi manager-ul de sine stătător din Spark, sau orice manager compatibil, cum ar fi Mesos, Yarn, sau Kubernetes. [@cite: https://jaceklaskowski.gitbooks.io/mastering-apache-spark/content/spark-architecture.html, https://spark.apache.org/docs/latest/rdd-programming-guide.html#overview ]
[@image: arhitectura Spark https://jaceklaskowski.gitbooks.io/mastering-apache-spark/content/spark-architecture.html ]
 Aplicația noastră folosește Azure Databricks drept *cluster manager*. 

Principala structură de date folosită de Spark este un **RDD** (Resilient Distributed Dataset), o colecție imutabilă  și distribuită de elemente către nodurile cluster-ului, care suportă diverse operații în paralel. RDD-urile pot fi create având ca sursă un fișier dintr-un sistem de fișiere distribuit sau o secvență de elemente generată în *driver*. Folosind un mecanism de *cache*, un RDD poate fi salvat în memorie pentru a putea fi reutilizat cu ușurință de către sarcinile programului. De asemenea, după cum spune și termenul "Resilient", acest tip de colecție se recuperează automat în urma erorilor neașteptate ale cluster-ului.

Există două tipuri de operații aplicabile unui RDD: transformările și acțiunile. O transformare crează un nou RDD, cu elemente modificate plecând de la un RDD deja existent, în timp ce o acțiune execută calcule cu elementele unui RDD și întoarce valoarea obținută programului *driver*. 
Un mare avantaj al Spark este faptul că transformările sunt mereu executate în mod leneș. Aceasta înseamnă că procesarea elementelor și calculele necesare nu au loc la executarea transformării, ci doar când RDD-ul transformat este supus unei acțiuni. Până în acel moment, orice transformare se adaugă unui plan de execuție, fără a fi de fapt aplicată. Acest lucru ne permite să ne verificăm codul mult mai ușor și să modelăm cantități mari de date, precum sursa noastră MAG, fără ca nodurile să execute operațiile de fiecare dată. 
O acțiune cere ca un rezultat să se întoarcă la *driver*, și atunci planul de execuție al RDD-ului trebuie pus în aplicare pentru a avea un rezultat tangibil. Un dezavantaj al acestui proces este că toate transformările din plan trebuie executate mereu, de la cap la coadă, de fiecare dată când acțiunea este apelată, chiar dacă este aceeași. Dar dacă alegem să persistăm RDD-ul în memorie, atunci problema este rezolvată, rezultatele transformărilor fiind reținute după ce au fost executate prima oară.[ @cite: https://spark.apache.org/docs/latest/rdd-programming-guide.html#overview ]

Alte abstractizări importante în Spark sunt Dataset și DataFrame, care împart același API în implementarea lor. Acestea sunt și ele colecții imutabile și distribuite de elemente, precum RDD-ul, dar beneficiază de optimizări mai puternice în spate. Fac parte din Spark SQL, un modul care permite execuția de cereri SQL către sursele de date distribuite. Acest modul folosește caracteristici funcționale din Scala precum *pattern-matching* și *quasiquotes* pentru a optimiza cererile Spark. 
Dataset vine cu o optimizare în plus, în privința tipului de date al entității pe care o conține. Pentru a serializa obiectele din Dataset, este folosit un *Encoder* specializat, specific tipului obiectului dat spre serializare. Acest *Encoder* este reprezentat de cod generat dinamic și transformă obiectul într-o secvență de biți a cărei format îi permite să poată facă unele operații precum filtrarea și sortarea fără să mai deserializeze obiectul. [@cite: https://spark.apache.org/docs/latest/sql-programming-guide.html, https://databricks.com/blog/2015/04/13/deep-dive-into-spark-sqls-catalyst-optimizer.html ]
[@image: Optimizatorul Spark SQL : https://databricks.com/wp-content/uploads/2015/04/Screen-Shot-2015-04-12-at-8.41.26-AM-1024x235.png ]

După cum am spus, DataFrame-ul și Dataset-ul împart același API. Mai exact, DataFrame-ul este definit ca un Dataset care nu ține cont de tipul elementelor din interior. Elementele dintr-un DataFrame sunt de tipul *Row*, iar un *Row* poate conține oricâte atribute de orice tip, cu nume asociat fiecărui atribut. Se poate observa cu ușurință că această structură este foarte similară cu un tabel SQL și, de altfel, metode specifice SQL-ului (*select*, *join*) pot fi aplicate atât DataFrame-ului cât și Dataset-ului. Aceste colecții oferă o interfață facilă de a interacționa cu datele structurate și, împreună cu cererile Spark SQL, folosesc același motor de executare, ceea ce permite trecerea ușoară de la un una la alta. [@cite: https://databricks.com/blog/2016/07/14/a-tale-of-three-apache-spark-apis-rdds-dataframes-and-datasets.html ]
[@image: Dataset și Dataframe : https://databricks.com/wp-content/uploads/2016/06/Unified-Apache-Spark-2.0-API-1.png]

Deși RDD-ul este abstractizarea de bază a Spark, este și cea mai veche, celelalte două colecții fiind construite peste aceasta. De aceea, este recomandat ca RDD-urile să fie folosite doar pentru operații distribuite de nivel jos, cum ar fi salvarea colecțiilor distribuite în fișiere sau manipularea partițiilor ocupate de date în nodurile *worker*. Spre deosebire de RDD, API-ul Dataset este recomandat pentru oprații cu o abstractizare mai înaltă, precum analizarea și procesarea datelor într-un mod interactiv, ceea ce se potrivește mai bine nevoilor proiectului nostru. În plus, dat fiind că Dataset-urile sunt construite peste RDD-uri, cele două colecții pot fi transformate cu ușurință din una în cealaltă. [@cite: https://databricks.com/blog/2016/07/14/a-tale-of-three-apache-spark-apis-rdds-dataframes-and-datasets.html ] 

De-a lungul lucrării, vom preciza caracteristicile Spark folosite pentru a întocmi diverse acțiuni în stadiile de prelucrare, modelare și procesare a datelor. Astfel, informațiile de mai sus vor fi benefice în a înțelege de ce am ales anumite metode și de ce anumite abordări funcționează. 

## De la Azure Data Lake Store către Apache Spark

Pentru a putea citi datele din ADLS, am autorizat componenta noastră Spark folosind același tip de autentificare *service-to-service* către ADW, prezentată mai sus. Cele trei credențiale au fost adăugate în configurațiile *Spark Session* și *Spark Context*, două entități care dau mai departe configurațiile către principalele structuri de date folosite în Spark, *RDD*, *Dataframe* și *Dataset*.

Datele stocate în ADLS au o structură asemănătoare cu o bază de date relațională. Datele sunt reprezentate sub forma unor fișiere text, care conțin informații în formă tabulară: câte o enitate pe linie, cu atributele fiecărei entități despărțite de caracterul tab('\t'). Fiecare fișier conține informații despre un anumit tip de entitate. De exemplu, avem fișierul *Authors.txt*, cu informații despre autori, *Papers.txt*, cu date despre publicații, și *PapersAuthorsAffiliations.txt*, care servește drept o tabelă de legătură între autori, articole și parteneriatele autorilor.

Pentru a putea modela datele cu ușurință, am încărcat fișierele text în memorie, am segmentat liniile după caracterul separator '\t' și am creat câte o clasă de obiecte pentru fiecare entitate reprezentată de un fișier. Dată fiind dimensiunea mare a unor fișiere, de la 10 *GB* la aproape 70 *GB*, am stocat toate obiectele extrase dintr-un fișier într-un *Dataset* corespunzător clasei de obiecte. Astfel, fiecare fișier din baza de date are un Dataset corespunzător cu același nume în aplicația noastră. 

Deținând informația despre tipul obiectelor, procesările pe care le facem asupra colecției vor beneficia de optimizările *Encoder*-ului. În plus, evaluările leneșe specifice Spark ne dau posibilitatea de a efectua transformări peste aceste fișiere de dimensiuni mari într-un timp scurt, urmând să așteptăm doar atunci când apelăm o acțiune asupra Dataset-ului.

## Datele primare și agregate pentru construirea rețelei statice
Pentru a putea crea rețeaua personală a unui cercetător, trebuie să putem obține următoarele date primare :

- Articolele scrise de către un anumit cercetător
- Autorii unui anumit articol
- Cercetătorii care activează într-un anumit domeniu de studiu

Am definit funcții care obțin aceste date folosind Dataset-urile entităților principale din MAG, precum *Authors*, *Papers* și *FieldsOfStudy*, legate printr-o metodă *join* cu Dataset-urile de legătură *PaperAuthorAffiliation* și *PaperFieldsOfStudy*. Am denumit funcțiile reprezentativ, *authorsOfPaper*, *papersofAuthor* și *authorsOfField*. 
???[@plus: eventual explic complexitatea timp/spațiu a operațiilor]

Deși nu participă în algoritmul de construire a rețelei, avem nevoie și de date agregate referitoare la cercetători, pentru a fi expuse în rețea pentru viitoarele analize statistice:

- Numărul de citări al autorului
- Numărul de publicații al autorului
- Indicatorul Hirsch al autorului

Primele două sunt deja prezente în baza de date MAG ca atribute în tabelul *Authors*, deci foarte ușor de obținut. Vom descrie în continuare algoritmul folosit pentru a calcula indicatorul Hirsch.
Mai întâi, obținem toate publicațiile autorului și le ordonăm descrescător după numărul de citări obținute. În această așezare, indicele Hirsch este poziția ultimei publicații pentru care numărul de citări este mai mare sau egal cu numărul de ordine al poziției sale. De exemplu, să considerăm un autor cu patru publicații, fiecare având 60, 32, 10, respectiv 3 citări. Index-ul Hirsch al acestui cercetător este 3, pentru că lucrarea de pe poziția 4 are doar 3 citări( mai puține decât indicele poziției, 4), iar lucrarea de pe poziția 3 are 10 citări (mai multe decât indicele poziției, 3). [@cite: https://en.wikipedia.org/wiki/H-index ] Algoritmul este ușor de implementat, folosind funcția primară de mai sus, *papersOfAuthor*. 
[??????? @question: Credeți că este nevoie de specificarea complexității ????????] 
[@plus: eventual explic complexitatea timp/spațiu a operației]

Având la dispoziție datele primare și datele agregate, acum vom descrie modul de obținere a unei rețele personale.


## Algoritmul de construire al unei rețele statice

O rețea personală este reprezentată de o listă de noduri și o listă de muchii, ale căror structură am discutat-o în prealabil în secțiunea "*Structura unei rețele personale*". Pentru a afla coautorii unui cercetător, vom prelua toate publicațiile lui, și pentru fiecare din acestea vom alege ceilalți autori ai publicației înafară de el. Toate acestea sunt efectuate folsoind funcțiile din secțiunea de mai sus. 

Este posibil ca un cercetător să scrie mai multe publicații în colaborare cu un altul, așa că algoritmul urmărește, pentru fiecare publicație, două cazuri: coautorii noi descoperiți și cei deja descoperiți.
Pentru un coautor care nu a mai fost găsit printre autorii unei publicații până atunci, adăugăm două noi muchii direcționate între el și cercetătorul "sursă" al rețelei, ponderate cu 1 pentru că până acum au o singură publicație la care au colaborat amândoi. 

În cel de-al doilea caz, pentru un coautor care a fost descoperit înainte, nu mai este nevoie să adăugăm o nouă muchie, ci doar să le actualizăm pe cele vechi incrementând numărul publicațiilor scrise împreună cu 1. 

Dorim ca rețeaua personală a unui cercetător să evidențieze întreaga "familie" de colaborare, și de aceea, aplicăm recursiv algoritmul de mai sus pentru toți coautorii "sursei", de data aceasta avându-i pe fiecare dintre ei ca "sursă". Ulterior, vom calcula coautorii coautorilor și tot așa, până când nu mai descoperim noi coautori.

Pentru a implementa aceasta, vom folosi o listă care să rețină cercetătorii ce vor fi "surse". Inițial, în listă va fi cercetătorul pentru care este construită rețeaua, iar mai apoi, fiecare coautor nou descoperit va fi adăugat în această listă. După ce terminăm de parcurs publicațiile "sursei", o ștergem pe aceasta din listă și repetăm tot algoritmul pentru următoarea "sursă" din listă. Vom continua să iterăm acest algoritm până când lista de "surse" va fi goală. [@todo: acest algoritm trebuie implementat]
[@plus: Dacă mărimile acestor rețele personale sunt relativ mici, aș putea adăuga un algoritm pentru a calcula și a combina rețelele personale ale tuturor autorilor dintr-un domeniu de studiu. Mai este și considerentul de timp, dat fiind că mai trebuie să implementez și versiunea dinamică a unei rețele, să fac câteva grafice, și să fac câteva măsurări ale timpului de execuție]

## Construirea rețelelor dinamice

Pentru calculul rețelelor personale dinamice, vom avea nevoie de aceleași date primare ca și rețelele statice, cu o mențiune în plus: 
- Numărul de citări primite de un articol într-un anume an

Am creat o funcție care, folosind Dataset-urile *Papers* și *PaperReferences*, numără câte dintre toate articolele apărute într-un an dat citează un articol dat. Funcția folosește aceleași metode *join* și *filter* ca mai sus.
Folosind această informație primară, am calculat următoarele date agregate:
- Numărul total de citări pe care le deține un articol într-un anume an
- Indicatorul Hirsch al unui autor într-un anume an

Observăm că rezolvarea primei probleme duce la calculul simplu al celei de-a doua. Folosind același algoritm ca și pentru rețelele statice, putem calcula acest indicator pentru orice autor odată ce avem o listă cu numărul total de citări ale fiecărui articol al său într-un anume an. Așa că vom explica în detaliu doar tehnica folosită pentru prima problemă.

Utilizând funcția primară definită mai sus, putem calcula numărul de citări primit de un articol în fiecare an de la publicarea sa până în prezent. Având aceste numere, obținem numărul cumulat de citări într-un anumit an însumând toate citările la anul publicației până în acel an. Pentru că numărul de citări cumulate într-un an depinde mereu de numărul de citări cumulate în anul anterior, observăm că funcția noastră are proprietatea Markov. [@cite: https://en.wikipedia.org/wiki/Markov_property ]. Pentru a evita repetarea acelorași calcule de mai multe ori, funcția noastră întoarce o secvență cu numărul de citări cumulate pentru fiecare an de la publicația articolului până în prezent. Iar în continuare, de fiecare dată când avem nevoie de datele agregate în discuție, este suficient să accesăm această secvență, nemaifiind nevoie să reapelăm funcția. Pentru a beneficia de timp de acces mic, am modelat secvența sub forma unei tabele de dispersie, indexată după an.

Construirea unei rețele dinamice este foarte asemănătoare cu cea a unei rețele statice, construirea muchiilor fiind singura diferență.
Pentru fiecare coautor nou găsit, calculăm tabela de dispersie prezentată mai sus pentru fiecare articol de-al său scris în colaborare cu cercetătorul "sursă". Apoi, modelăm această tabelă conform structurii discutate în *Structura unei rețele personale*, și o adăugăm ca pondere a muchiei dintre coautor și sursă.
Ca în cazul rețelelor statice, întreg algoritmul este repetat recursiv pentru a adăuga în rețea toată "familia" de colaborare a "sursei".


## Utilizarea rețelelor personale

Scopul proiectului de față este obținerea rețelelor personale, ci nu analizarea lor statistică, acest fapt fiind lăsat la latitudinea viitorilor utilizatori. Pentru aceștia, am întocmit această secțiune pentru a exemplifica cum aceste rețele pot fi extrase din mediul Azure sau chiar analizate în continuare folosind Spark.

Creatorii Apache Spark au întomit o librărie pentru procesări distribuite de grafuri, numită GraphFrames. Este ușor de văzut că, dat fiind că rețelele personale extrase de noi iau forma unui graf, această librărie li se potrivește perfect. Librăria oferă diverși algoritmi specifici grafurilor, precum *PageRank*, calcularea celui mai scurt drum, numărarea triunghiurilor sau găsiri de tipare. Un GraphFrame se construiește dintr-un DataFrame de noduri, care trebuie să conțină un atribut identificator unic, și un DataFrame de muchii, care să conțină două atribute cu identificatorul unic al nodului sursă, respectiv nodului destinație. Dat fiind că rețelele noastre vin sub forma unor Dataset-uri cu entități corespunzătoare, transformarea acestora în DataFrame-uri se face folosind o conversie standard din Spark. [@cite: https://graphframes.github.io/ ] Acestea sunt metode de analiză structurală, mai degrabă decât statistică. 

Pentru cea din urmă se poate folosi programul Siena, disponibil și ca o librărie pentru limbajul R (RSiena). Acest program este conceput de mai mulți cercetători, printre care și Tom Snijders, și se concentrează pe analiza statistică a rețelelor sociale. Siena explorează rețelele longitudinal, de-a lungul unei perioade de timp, iar modelele sale de noduri sunt structurate ca un lanț Markov, afișând informația cumulată de la un moment la altul. [@cite: https://www.stats.ox.ac.uk/~snijders/siena/ ] Astfel, modul în care am construit rețelele le face compatibile cu acest program.	

Pentru a putea fi analizate de Siena, rețelele trebuie stocate local. Ceea ce nu este o problemă, pentru că, din mediul Databricks, odată ce am obținut rețelele, le putem scrie într-un fișier în ADLS, de unde le putem descărca cu un singur click.

## Vizualizarea rețelelor personale

De asemenea, poate fi util să creezi vizualizări ale rețelelor personale, pentru a le prezenta înainte sau după analiză statistică. Dată fiind modelarea lor sub forma unui graf, este convenabil să le reprezentăm în format GEXF(Graph Exchange XML Format). Acesta este un format similar XML-ului în care informații despre nodurile, muchiile și datele agregate ale acestora pot fi salvate. În această formă, grafurile pot fi încărcate în diverse programe de vizualizare. 
Unul din acestea este Gephi, o aplicație open-source pentru modelarea și analizarea rețelelor. Folosind acest program, utilizatorii pot partiționa rețelele după anumite atribute, pot controla mărimea și culoarea nodurilor cât și a muchiilor. [@cite: https://gephi.org/users/publications/ ]. 
În plus, dezvoltatorii pot folosi rețelele în acest format pentru a oferi rețelele spre analiză într-un client web, folosind librăria de JavaScript *Linkurious*. Librăria este construită pentru grafuri cu până la miliarde de noduri, deci rețelele noastre personale vor putea fi procesate fără probleme. Totuși *Linkurious* este o librărie complexă care oferă mai multe funcționalități, precum filtrare, securitatea informațiilor și detectarea anomaliilor. Pentru cei care vor doar să obțină o vizualizare interactivă, recomandăm librăria de JavaScript *D3* (Data-Driven-Documents). Această librărie este open-source și este folosită pentru vizualizări diverse, de la histograme la rețele, deci are un grad mare de libertate.

## Scalabilitatea proiectului 
[@todo: Aici adaug niște comparații de timp pentru rețele personale mici, comparativ cu rețele mari. Și comparații pentru sample-urile(de 1000) de elemente și întreaga bază de date. Până acum nu am folosit sample-urile, de aceea nu am descris nicăieri obținerea lor de până acum. Dacă după ce termin algoritmii, văd o ușurință în folosirea acelor sample-uri pentru testare, aș putea adăuga o secțiune și despre acestea].

## Tehnologii folosite
[@todo: despre Databricks și performanțele sale și de ce am ales-o în comparație cu un environment standalone]
[@todo: despre Spark și performanțele sale și de ce l-am ales în comparație cu Hadoop, de exemplu]
[@todo: de ce am ales Scala în comparație cu Python, pentru Spark]
[????? @question: La începutul anului, mi-ați spus că este de preferat să prezint tehnologiile doar din punct de vedere al motivelor pentru care au fost alese. Rămâne valabilă această opinie ?????]

[?????? @question: Ce credeți, să adaug informații despre bursa Azure4Research ? Cât să motivez alegerea platformei Azure pentru proiect ??????] 
[@todo: de ce am ales Azure(bursa Azure4Research)]


## Bibliografie
- Tehnologii folosite  
    - Databricks
[Performanțele Databricks](https://people.csail.mit.edu/matei/papers/2015/vldb_spark.pdf)
[Introducere în Databricks și exemple de notebook-uri](https://docs.azuredatabricks.net/_static/notebooks/azure/gentle-introduction-to-apache-spark-azure.html)
    - Spark 
[Performanțe](https://opensource.com/business/15/1/apache-spark-new-world-record)
[Avantajele Spark față de Hadoop](https://www.quora.com/What-are-resilient-distributed-datasets-RDDs-How-do-they-help-Spark-with-its-awesome-speed)
[Documentația Spark](https://spark.apache.org/docs/latest/index.html)
[Dataframe, Dataset sau RDD](https://databricks.com/blog/2016/07/14/a-tale-of-three-apache-spark-apis-rdds-dataframes-and-datasets.html)
[Cum este optimizat Spark SQL și Dataframes](https://databricks.com/blog/2015/04/13/deep-dive-into-spark-sqls-catalyst-optimizer.html)
[Proiectul Tungsten](https://databricks.com/blog/2015/04/28/project-tungsten-bringing-spark-closer-to-bare-metal.html)
    - Azure
[Arhitecturi Big Data](https://docs.microsoft.com/en-us/azure/architecture/guide/architecture-styles/big-data)
[Tehnici de design pentru aplicații Cloud](https://docs.microsoft.com/en-us/azure/architecture/guide/)

## Citări

- **Arnab Sinha, Zhihong Shen, Yang Song, Hao Ma, Darrin Eide, Bo-June (Paul) Hsu, and Kuansan Wang. 2015. An Overview of Microsoft Academic Service (MAS) and Applications. In Proceedings of the 24th International Conference on World Wide Web (WWW ’15 Companion). ACM, New York, NY, USA, 243-246. DOI=http://dx.doi.org/10.1145/2740908.2742839**