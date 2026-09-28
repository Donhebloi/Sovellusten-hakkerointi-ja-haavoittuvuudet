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

Koodin muokkaamisen jälkeen käänsin tiedoston jotta uusi koodi alkaisi toimimaan **gcc -o passtr passtr.c**:  
<img width="563" height="61" alt="image" src="https://github.com/user-attachments/assets/7dcb28f5-dc5c-4adf-85d3-59a6b81025a2" />  

Nyt **strigns** komennon tuloste näytti erilaiselta:  
<img width="1056" height="610" alt="image" src="https://github.com/user-attachments/assets/316fa136-9d3f-457c-b9a0-5e041939ed3d" />  

Kokeilin vielä että ohjelma toimii normaalisti:  
<img width="1047" height="132" alt="image" src="https://github.com/user-attachments/assets/f02ed67e-472c-4f2c-8905-3168f88498db" />  

Ja sehän toimi.  
Tämä oli nyt aika todella heikkoa heikkoa obfuskointia, eikä mitään oikeaa salausta. Koodia olisi varmasti voinut muuttaa paljon tehokkaammaksi niin että salasana on täysin piilossa **strings** komennolta, mutta en halunnut kopioida ja tehdä sellaisia asioita mitä en itse ymmärrä ollenkaan.  




