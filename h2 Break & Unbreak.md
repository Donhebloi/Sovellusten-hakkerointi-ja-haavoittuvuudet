# a) Break into 010-staff-only.

Aloitin ensiksi lataamalla ja unzippaamalla Teron tehtävät:  
````
wget https://terokarvinen.com/hack-n-fix/teros-challenges.zip
unzip teros-challenges.zip
````
<img width="1400" height="153" alt="image" src="https://github.com/user-attachments/assets/aaaf2931-bd56-4967-a88e-21647360149b" />  

<img width="537" height="123" alt="image" src="https://github.com/user-attachments/assets/84b020a3-45bd-485f-84b1-9f418b728fe4" />  

Siirryin **cd challenges/10-staff-only/** komennolla tehtävään ja pääsin aloittamaan tehtävän **python3 staff-only.py** komennolla:  
<img width="771" height="239" alt="image" src="https://github.com/user-attachments/assets/23b4f0a7-4800-4024-9adc-867e1e67babf" />  

Siirryin ohjelman antamaan **http://127.0.0.1:500** linkkiin.  
Tehtävän ongelmana on se, että salasanakenttään ei voi syöttää muuta kuin numeroita:  
<img width="937" height="396" alt="image" src="https://github.com/user-attachments/assets/b6a2e6ac-9a11-4ca5-88b9-60f016c00264" />  

Lähdin tutkimaan sivustoa F12 developer tools työkalulla. Avasin **Element picker** työkalun **Cntrl+Shift+C**:llä, jonka avulla pääsin näkemään sivun htlm koodin.  
<img width="517" height="185" alt="image" src="https://github.com/user-attachments/assets/c0d1dc1c-0c65-42f5-b753-6c1bc3ce2632" />  

Koodista paljastui **input type="number"**, minkä takia salasanakenttään ei pysty syöttämään muuta kuin numeroita. Muutin **number**:in **text** muuttujaksi:  
<img width="501" height="180" alt="image" src="https://github.com/user-attachments/assets/09a4d823-9ee7-4c97-a4ad-6463f924ff8a" />  

Nyt kun kokeili syöttää tekstiä salasanakenttään niin ei tullut enään "Please enter a number" promptia, eli Client puolen tarkistuksen onnistui kiertämään.    
Nyt pystyi lähtemään kokeilemaan SQL-injektiota. Muutin taas **number**:in **text** muuttujaksi ja kokeilin syöttää **' OR 1=1--** salasanakenttään.  
Tästä sain vastaukseksi "Your password is foo" ja huomasin että **' OR 1=1--** ehto on nyt sivun htlm koodissa. Eli **' OR 1=1--** ei ollut mikään turha yritys, vaikka se ei nyt täysin onnistunut.  
<img width="456" height="157" alt="image" src="https://github.com/user-attachments/assets/3b9cda6f-a628-45bb-ac30-efc84a9fbf84" />  

<img width="514" height="110" alt="image" src="https://github.com/user-attachments/assets/9d0fbb24-c205-4ae7-918c-453e05a703d8" />  

Kokeilin sitten **LIMIT** lauseketta. Eli taas, muutin **number**:in **text** muuttujaksi ja kirjoitin salasanakenttään **' OR 1=1 LIMIT 1,1--** mutta tästä sain vastaukseksi "Your password is (not found)":  
<img width="460" height="102" alt="image" src="https://github.com/user-attachments/assets/698793f5-9a38-4a73-8f7e-a6d42ce41670" />  

Yritin sitten samaa uudestaan, mutta ehtona oli **' OR 1=1 LIMIT 2,1--**, joka sitten onnistui! Sivustolle tuli lippu näkyviin:  
<img width="965" height="104" alt="image" src="https://github.com/user-attachments/assets/76576bc3-0593-482c-b00a-e2951285820c" />  

````
SUPERADMIN%%rootALL-FLAG{Tero-e45f8764675e4463db969473b6d0fcdd}
````

# Fix the 010-staff-only vulnerability from source code. Demonstrate with a test that your solution works.

Seuraavaksi sitten piti etsiä koodista haavoittuvuus ja korjata se. Avasin koodin microlla, **micro staff-only.py** ja lähdin etsimään virhettä. Etsin koodista kohtaa jossa PIN-koodi liitetään SQL-kyselyyn. Tämä löytyi riviltä 22, mistä sitten virhe selvisi:  
<img width="1057" height="36" alt="image" src="https://github.com/user-attachments/assets/ae9b99e3-09da-4357-b0f5-4db0214bb559" />  

Tässä siis yhdistyy käyttäjän syöttämä merkkijono osaksi SQL-komentoa. Koodi pitää korjata niin, että käyttäjän data ja SQL-rakenne pysyvät erillään, eikä sotkeennu. Muutin koodia muokkaamalla SQL-komentoriviä ja lisäämällä toisen **pin** muuttujan:  
<img width="895" height="185" alt="image" src="https://github.com/user-attachments/assets/db6fe395-3f16-44f5-a17a-dfc6bc12908b" />  

Tästä sitten kokeilin samaa injektiota, eli muutin **number**:in **text** muuttujaksi ja syötin **' OR 1=1 LIMIT 2,1--** salasanakenttään:  
<img width="781" height="606" alt="Näyttökuva 2026-09-20 151420" src="https://github.com/user-attachments/assets/3168d3de-4567-423a-9c4f-8e34367d509f" />  

Nyt ei tullut lippua näkyviin! Eli haavoittuvuuden korjaaminen onnistui. Kokeilin vielä syöttää "123" PIN-koodin salasanakenttään jotta näkisin että ohjelma toimii normaalisti:  
<img width="699" height="246" alt="image" src="https://github.com/user-attachments/assets/6d4afd7f-29d3-48e2-a1fc-3fe299a1f815" />  

"Your password is Somedude" tuli näkyviin, joten ohjelma toimii normaalisti.  

# Solve dirfuzt-1 from the article Karvinen 2023: Find Hidden Web Directories - Fuzz URLs with ffuf.

Aloitin lataamalla ffufin ja ffufia varten tarvittavan sanalistan: 
````
sudo apt-get update
sudo apt-get install ffuf
wget https://raw.githubusercontent.com/danielmiessler/SecLists/master/Discovery/Web-Content/common.txt
````
Sitten latasin **dirfuzt-1** tehtävän ja annoin sille tarvittavat oikeudet **chmod u+x dirfuzt-1**. Nyt tehtävän pystyy ajamaan:  
<img width="538" height="218" alt="image" src="https://github.com/user-attachments/assets/4e4b07b0-6f80-4b7e-8b93-04b222eaf99c" />  

Nyt kun avaa linkin, avautuu "dirfutz-1 - Nothing, nil, null, nada." sivusto:  
<img width="764" height="319" alt="image" src="https://github.com/user-attachments/assets/60d70e39-a490-4dea-bf59-ba611ddaf275" />  

Tästä sitten tein ensimmäisen ffuf ajon, jossa lähdin tutkimaan **common.txt**:iä ja kohde URL:ää, komennolla **ffuf -w common.txt -u http://127.0.0.2:8000/FUZZ**.
<img width="1372" height="873" alt="image" src="https://github.com/user-attachments/assets/bfefd601-973c-4b52-b6fa-c2da1f7c1032" />  

Tästä tulostui ihan hirveä määrä erilaista tietoa, jota pitäisi filtteröidä, jotta löytäisi mitään kiinnostavaa. Melkein kaikilta riveiltä löytyi tämä sama tieto: [Status: 200, Size: 154, Words: 9, Lines: 10, Duration: 0ms]
Lähdin filtteröimään listaa koon mukaan **ffuf -w common.txt -u http://127.0.0.2:8000/FUZZ -fs 154**:  
<img width="1330" height="879" alt="image" src="https://github.com/user-attachments/assets/2729523e-4813-4b72-a918-f28393641d42" />  

Nyt tulostui huomattavasti pienempi lista, josta löytyy hakemistoon liittyviä polkuja ja adminiin liittyvä polku.  
Kokeilin nyt lisätä kohde URL:ään **.git/** sekä **wp-admin** ja avata ne selaimessa:  
````
http://127.0.0.2:8000/.git/
ja
http://127.0.0.2:8000/wp-admin
````
<img width="624" height="236" alt="Näyttökuva 2026-09-20 165744" src="https://github.com/user-attachments/assets/bf58da52-47ee-4699-a55b-16f1c81a6538" />  

<img width="561" height="228" alt="Näyttökuva 2026-09-20 165826" src="https://github.com/user-attachments/assets/5714102c-f42f-4c6e-82d9-88338208dfae" />  

Kummatkin avautuivat ja liput tulivat näkyviin! 
````
git lippu FLAG{tero-git-3cc87212bcd411686a3b9e547d47fc51}
Admin lippu FLAG{tero-wpadmin-3364c855a2ac87341fc7bcbda955b580}
````

# d) Break into 020-your-eyes-only.

Aloitin siirtymällä tehtävänkansioon ja tekemällä ympäristön tehtävää varten:  
````
cd challenges/020-your-eyes-only
sudo apt-get -y install virtualenv
virtualenv virtualenv/ -p python3 --system-site-packages
source virtualenv/bin/activate
````
<img width="721" height="138" alt="image" src="https://github.com/user-attachments/assets/8668851d-6205-468c-88b2-344c957a19f6" />  

<img width="934" height="388" alt="image" src="https://github.com/user-attachments/assets/2ac9a9bd-abe7-443c-b7ba-8f279d88ced9" />  

Ympäristön valmistumisen jälkeen aloitin Djangon asennuksen. Ensiksi avasin **cat** komennolla **requirements.txt** tiedoston ja sen jälkeen latasin siellä näkyvän Django version.  
<img width="993" height="318" alt="image" src="https://github.com/user-attachments/assets/c4e215b8-626f-46cd-a5e2-8659c4c7ffc0" />  

Sitten rupesin päivittämään tietokantaa:  
````
cd logtin/
./manage.py makemigrations; ./manage.py migrate
````
<img width="1039" height="288" alt="image" src="https://github.com/user-attachments/assets/db9316ca-bb71-4a0e-b5ce-986e095bc329" />  

Tämän jälkeen serverin pystyi käynnistämään **./manage.py runserver** ja siirtymään linkin sivulle:  
<img width="1038" height="295" alt="image" src="https://github.com/user-attachments/assets/b452d933-8a99-4d2c-8630-6cf562536f37" />  

<img width="1278" height="565" alt="image" src="https://github.com/user-attachments/assets/5de9e82e-01f7-4c77-b2f1-71cd5ef9ac9b" />  

Loin sivustolle käyttäjän, jonka jälkeen pääsi "show my data" sivulle, mutta "admin dashboard" sivulla tuli "403 forbidden" vastaan.  
<img width="1139" height="937" alt="image" src="https://github.com/user-attachments/assets/ce93521e-74a7-4542-9f08-97b765e3ea1f" />  

<img width="736" height="174" alt="image" src="https://github.com/user-attachments/assets/4bed87af-92ac-4db3-8ac3-056af7b90ab7" />  

403 Forbidden viittaa siihen että pääsy on kielletty oikeuksien puutteen takia joten lähdin tutkimaan asiaa.  
Lähdin fuzzaamaan Django-sovellusta **ffuf -w common.txt -u http://127.0.0.1:8000/FUZZ -t 10**.   
Materiaaleissa lukee että kehitys serveri on hidas, joten kannattaa rajoittaa pyyntöjä. Pienensin ffufin thread-määrän tuolla **-t 10** parametrilla joka hidasti ffuf ajon aikaa mutta sen ansiosta serveri ruuhkautunut eikä palauttanut mitään virheitä.  
<img width="1539" height="756" alt="image" src="https://github.com/user-attachments/assets/a5391d13-e9dc-458c-992f-705c2801a3fb" />  

Fuzzaus löysi "admin-console" polun, jolla on "status: 301", mikä tarkoittaa pysyvää uudelleenohjausta. Etusivuilla oleva "admin dashboard" nappi vei **/admin-dashboard/** osoitteeseen, eikä tuohon "admin-console" osoitteeseen.  
Kokeilin sitten vaihtaa ton linkissä olevan dashboardin consoleksi:  
````
http://127.0.0.1:8000/admin-console/
````
<img width="1201" height="523" alt="image" src="https://github.com/user-attachments/assets/99e93df0-4f5b-41f3-89b8-e19c78d2dfcc" />  

Pääsin admin consoleen. Ohjelmoinnissa on siis käynyt virhe, **admin-dashboard**:ille on annettu oikeat oikeudet, kun taas **admin-console**:lle ei ole, minkä takia admin consoleen pääsee käsiksi.  

# e) Fix the 020-your-eyes-only vulnerability. Demonstrate with a test that your solution works.

Lähdin korjaamaan lähdekoodia etsimällä sen **find . -name "views.py"** komennolla  
<img width="1052" height="114" alt="image" src="https://github.com/user-attachments/assets/3139281f-d238-4c13-bfc5-c95094ff5865" />  

Avasin **hats/views.py** ja rupesin tutkimaan sitä:  
<img width="1236" height="570" alt="image" src="https://github.com/user-attachments/assets/59daf83a-1465-48d6-a236-48b55ffbd94a" />  

Koodissa on kolme eri luokkaa kirjautumiselle. Ekat kaksi luokkaa on tehty oikein, mutta kolmannesta luokasta löytyy virhe:  
<img width="897" height="162" alt="image" src="https://github.com/user-attachments/assets/02abf0d0-5384-46e2-9571-a2293a63c62f" />  

Toisin kuin koodin toisessa luokassa, kolmannesta luokasta puuttuu **and self.request.user.is.staff** tarkistus, eli ohjelma ei tarkista sitä, onko käyttäjä admin vai ei.  
Lisäsin koodiin puuttuvan tarkistuksen:  
<img width="1219" height="149" alt="image" src="https://github.com/user-attachments/assets/23384d31-16e7-4bd9-bf25-87ad2589188a" />  

Lähdin kokeilemaan toimiiko muutos, eli käynnistin serverin uusiksi **./manage.py runserver** ja menin ensiksi osoitteeseen **http://127.0.0.1:8000/admin-dashboard/**:  
<img width="647" height="226" alt="image" src="https://github.com/user-attachments/assets/89203f11-f2d9-4a05-93e3-5282f2683b1a" />  

Se oli edelleenkin sama 403 Forbidden. Sitten menin **http://127.0.0.1:8000/admin-console/** osoitteeseen:  
<img width="631" height="233" alt="image" src="https://github.com/user-attachments/assets/f03dbbba-351b-428a-962d-b4049c41a644" />  

Ja nyt sekin on Forbidden! 



