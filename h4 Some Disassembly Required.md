# a) Install Ghidra

Aloitin päivittämällä järjestelmän **sudo apt-get update** ja sen jälkeen latasin Ghidran **sudo apt-get install ghidra**:  
<img width="913" height="639" alt="image" src="https://github.com/user-attachments/assets/8b462888-2a59-4757-8459-e1510b0c6955" />  

# b) rever-C

Aloitin purkamalla tehtävätiedoston, **unzip ezbin-challenges.zip**
<img width="552" height="304" alt="image" src="https://github.com/user-attachments/assets/aa3eb19f-5144-41fc-8f31-7875c7b3bdfa" />  

Tästä siirryin sitten Ghidran puolelle. Aloitin luomalla uuden projektin, eli File -> New project, valitsin projektin hakemistoksi oman "challenges" hakemiston ja annoin projektille nimeksi "läksyt".  
Projektin luomisen jälkeen, importtasin siihen "packd" tiedoston, File -> Import File ja valitsemalla "packd" tiedoston.  
<img width="266" height="171" alt="image" src="https://github.com/user-attachments/assets/0295435d-ee76-4f1d-bdf7-afd41014a77a" />  

Yritin alkaa analysoimaan tiedostoa, mutta en löytänyt "main" funktiota mistään. Tajusin sitten että **packd** tiedosto pitäisi varmaan purkaa eka, ennenkuin yrittää analysoida sitä. Tarkistin ekana että voisiko **packd** tiedosto olla UPX pakattu, **strings** ja **grep** komennoilla:  
**strings packd | grep -1 upx**:  
<img width="1093" height="134" alt="image" src="https://github.com/user-attachments/assets/60a4f0f2-8668-402f-a7d7-a74bffb1f1cc" />

Sehän oli pakattu. Sitten purin ja nimesin tiedoston uudelleen:  
**upx -d packd -o packd_unpacked**  
<img width="1088" height="291" alt="image" src="https://github.com/user-attachments/assets/8fc06df2-5f7b-49b5-a9e0-1f9e356b20b9" />  

Nyt kun tiedosto oli purettu, niin lähdin yrittämään analysointia Ghidralla uudestaan. Ekana poistin projektistani edellisen "packd" tiedoston ja importtasin sen tilalle uuden "packd_unpacked" tiedoston:  
<img width="240" height="163" alt="image" src="https://github.com/user-attachments/assets/338d9ff2-9fe6-40bd-a8c5-01a7cf18c877" />  

Nyt kun ohjelma analysoitiin, niin Ghidran symbol tree taulukosta, functions kohdasta löytyi se main osio.  
<img width="243" height="234" alt="image" src="https://github.com/user-attachments/assets/4bdc6054-39d7-454b-bb3d-e0246e27e595" />  

Main osiota klikaamalla, se avautui decompiler kohtaan koodina:  
<img width="446" height="301" alt="image" src="https://github.com/user-attachments/assets/8880ade1-9733-4393-ae10-0b4557e70541" />  

Tästä lähdin sitten nimeämään muuttujia uusiksi, jotka kuvaisivat ohjelman toimintoja vähän järkevämmin:  
````
int iVar1 -> int input - Tämä on käyttäjän syöte
char local_28 -> char password_input - Tätä syötettä verrataan oikeaan salasanaan
````
<img width="515" height="272" alt="image" src="https://github.com/user-attachments/assets/b9d0bd84-3848-45de-a4e7-f0cb90757835" />  

Ohjelma toimii siis niin, että se lukee käyttäjän syötteen ja vertaa sitä ohjelman salasanaan. Jos käyttäjän syöte on oikein, saadaan flägi esille, jos ei ole oikein, niin tulostuu "Sorry, no bonus" teksti.

# C) If backwards

Aloitin tehtävän samalla tavalla kuin edellisenkin, eli importtasin "passtr" tiedoston omaan "läksyt" projektiin ja avasin sen analysointi työkalulla.  
Siirryin tiedoston main funktioon, **if** muuttujan riville. Tästä "listing" näkymään tuli **JNZ** rivi esiin, mitä piti muokata. Muistin tunnilla käydyn esimerkin avulla, että **JNZ** pitää muuttaa **JZ**:ksi mutta en tarkalleen muistanut miksi ja mitä tuo **JNZ** meinaa, joten hain netistä lisää tietoa siitä.  
````
JNZ = Jump if Not Zero - Jos salasana on väärin, hyppää pois onnistumis-haarasta
JZ = Jump if Zero - Jos salasana on oikein, hyppää onnistumis-haaraan.
````
Eli tästä sitten muutin **JNZ**:n, **JZ**:ksi, jotta ohjelma alkaisi toimimaan väärinpäin.  
Alunperin:  
<img width="513" height="277" alt="image" src="https://github.com/user-attachments/assets/ad90c96d-6e7d-4360-b0ba-6a8ec5b0ce57" />  

<img width="388" height="21" alt="image" src="https://github.com/user-attachments/assets/1569bbcb-812a-4167-a104-0b72ecabb2b6" />  

Muokattuna:  
<img width="420" height="21" alt="image" src="https://github.com/user-attachments/assets/94e0572c-1a89-442f-b440-1d8022629ef0" />  

<img width="516" height="262" alt="image" src="https://github.com/user-attachments/assets/6fa8ca50-06d1-49d6-b575-7bdfef0b3124" />  

Decompile kohdasta näkee nyt, että koodissa ehto on vaihtanut paikkaa.  





