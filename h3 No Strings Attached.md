# Ympäristö

Käyttöjärjestelmä: Kali GNU  
Laitteisto: Thinkpad T14, Oracle VirtualBox Manager  
Selain: Firefox  
Verkko: NAT  

  
# a

Aloitin unzippaamalla tehtävän **unzip ezbin-challenges.zip**  
<img width="627" height="300" alt="image" src="https://github.com/user-attachments/assets/2e17b776-1e78-4fbb-ae7c-9edbb38f4072" />  

Purkamisen jälkeen yritin ajaa ohjelman **./passtr**, mutta ongelmana oli se että en tiennyt oikeaa salasanaa:  
<img width="570" height="147" alt="image" src="https://github.com/user-attachments/assets/23f33a88-6b6e-44a5-a28b-dbfa42421e61" />  

Lähdin tästä sitten etsimään salasanaa **strings** komennolla. Eli **strings passtr**:  
<img width="1045" height="621" alt="image" src="https://github.com/user-attachments/assets/96632368-5962-482d-a0ec-efb3fad69c69" />

Sieltähän löytyi salasana ja lippu: 
````
sala-hakkeri-321
FLAG{Tero-d75ee66af0a68663f15539ec0f46e3b1}
````

Kokeilin vielä uudestaan ajaa **passtr** ohjelman ja syöttää siihen löydetyn salasanan:  
<img width="1043" height="147" alt="image" src="https://github.com/user-attachments/assets/4aeb4760-e676-464d-92b6-90033eda32b3" />  

Nyt ohjelma päästi sisään ja näytti jo aikaisemmin löydetyn lipun.  


# b

Lähdin tutkimaan alkuperäistä koodia avaamalla sen **micro passtr.c**:  
<img width="567" height="57" alt="image" src="https://github.com/user-attachments/assets/8efe7ed0-9c68-44af-871f-98dd9f1dffb7" />  

<img width="990" height="455" alt="image" src="https://github.com/user-attachments/assets/ff58263a-9e96-4790-b7b6-189283110b54" />  


Koodaaminen on itsellä erittäin heikkoa niin käytin tähän apuna tekoälyä (Claude).  
Alkuperäisessä koodissa salasana näkyy selkeästi luettavana merkkijonona. "Parannetussa" koodissa salasana on käännetty toisinpäin, eli se on edelleenkin helposti löydettävissä ja luettavissa, mutta ehkä ei kuitenkaan enään ihan niin selkeästi esillä kuin aikasemmin.  
Muokattu koodi:  
<img width="1368" height="701" alt="image" src="https://github.com/user-attachments/assets/063f75b1-1d9f-4e11-b2e0-bfeec449c7ca" />  

Koodin muokkaamisen jälkeen käänsin tiedoston jotta uusi koodi alkaisi toimimaan, eli **gcc -o passtr passtr.c**:  
<img width="563" height="61" alt="image" src="https://github.com/user-attachments/assets/7dcb28f5-dc5c-4adf-85d3-59a6b81025a2" />  

Nyt **strigns** komennon tuloste näytti erilaiselta:  
<img width="1056" height="610" alt="image" src="https://github.com/user-attachments/assets/316fa136-9d3f-457c-b9a0-5e041939ed3d" />  

Kokeilin vielä että ohjelma toimii normaalisti:  
<img width="1047" height="132" alt="image" src="https://github.com/user-attachments/assets/f02ed67e-472c-4f2c-8905-3168f88498db" />  

Ja sehän toimi.  
Tämä oli nyt aika todella heikkoa obfuskointia, eikä mitään oikeaa salausta. Koodia olisi varmasti voinut muuttaa paljon tehokkaammaksi niin että salasana on täysin piilossa **strings** komennolta, mutta en halunnut kopioida ja tehdä sellaisia asioita mitä en itse ymmärrä ollenkaan.  


# c

Siirryin **packd** hakemistoon ja kokeilin ajaa ohjelman, eli **./packd**. Ohjelma kysyi salasanaa kuten aikaisemmassa tehtävässä:  
<img width="574" height="138" alt="image" src="https://github.com/user-attachments/assets/fd340218-e586-4802-bb3f-797efb5a5ab5" />  

Kokeilin sitten samaa taktiikkaa kuten ensimmäisessä tehtävässä, eli **strings** komentoa:  
<img width="542" height="787" alt="image" src="https://github.com/user-attachments/assets/0092da2c-3d3c-49a0-9612-fbc89956c091" />  

Tällä kertaa ei kuitenkaan paljastunut salasanaa ja lippua **strings** komennon avulla.  
**Strings** komennolla paljastui kuitenkin että **packd** tiedosto on pakattu "UPX" ohjelmalla:  
<img width="1243" height="59" alt="image" src="https://github.com/user-attachments/assets/fe9442c0-9d15-480d-a8fb-90602c030210" />  

En ollut ennestään tuttu "UPX" ohjelman kanssa, joten lähdin netistä etsimään tietoa siitä ja miten "UPX" ohjelman voisi purkaa.  
Löysin netistä ohjeet missä kerrottiin **"-d"** parametrin käytöstä kun haluaa purkaa UPX pakatun ohjelman. Tajusin sitten vielä kurkata UPX:n **help** sivuille, eli **upx --help**, josta paljastui myös tuo **-d** parametri:  
<img width="1185" height="403" alt="image" src="https://github.com/user-attachments/assets/e97408ab-4834-4507-8cce-d7f644e7cf39" />  

Kokeilin sitten tuota **"-d"** parametria, ajamalla komennon **"upx -d packd"**:  
<img width="1165" height="299" alt="image" src="https://github.com/user-attachments/assets/f7808491-1acc-4cce-bdb1-f9a8b922f404" />  

Oletin, että komento toimii koska "Unpacked 1 file" teksti tuli esille.  
Tästä lähdin sitten taas kokeilemaan **strings** komentoa:  
<img width="1021" height="581" alt="image" src="https://github.com/user-attachments/assets/5003eaea-bcd9-44ee-a0da-62cdf9bcf291" />  

Nyt sitten UPX:n purkamisen jälkeen, **strings** komennolla tuli salasana ja lippu esiin!  
````
piilos-AnAnAs
Yes! That's the password. FLAG{Tero-0e3bed0a89d8851da933c64fefad4ff2}
````
Kokeilin vielä että salasana oikeasti toimii, eli ajoin ohjelman **./packd** ja syötin löydetyn salasanan:  
<img width="1050" height="157" alt="image" src="https://github.com/user-attachments/assets/6ebc2132-5b47-4d37-b946-d0db611088ca" />  

Nyt ohjelma päästi sisälle ja lippu tuli taas esille!  
