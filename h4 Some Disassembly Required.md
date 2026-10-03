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

Aloitin tehtävän samalla tavalla kuin edellisenkin, eli importtasin "passtr" tiedoston omaan "läksyt" projektiin ja avasin sen Ghidralla.  
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

Nyt sitten piti exporttaa muokattu tiedosto. Eli File -> Export program, valitsin formaatiksi "Original File" ja muutin tiedoston nimeksi "passtr_fixed"  
<img width="335" height="195" alt="image" src="https://github.com/user-attachments/assets/350f1efd-bc2f-4f82-a599-893114ccfc7c" />

Sitten annoin ohjelmalle ajo-oikeudet, **chmod u+x passtr_fixed**:  
<img width="378" height="66" alt="image" src="https://github.com/user-attachments/assets/fb5fbb1a-528a-452f-8521-7b9508cbc75a" />  
<img width="816" height="22" alt="image" src="https://github.com/user-attachments/assets/96e6f20c-072d-43e4-8e0f-613858421c4a" />  


Kokeilin sitten toimiiko ohjelma niin kuin pitää. Syötin eka koodista löytyvän oikean salasanan, eli "sala-hakkeri-321" ja sehän ei toiminut:  
<img width="308" height="140" alt="image" src="https://github.com/user-attachments/assets/e06e5ed5-8c8c-4ec9-8398-61b290299a32" />  

Syötin sitten jonkun random salasanan:  
<img width="976" height="145" alt="image" src="https://github.com/user-attachments/assets/750dbc0f-1c61-4c0f-9aeb-24d2ffe27a96" />  

Ja se toimi. Eli ohjelma toimii nyt onnistuneesti väärinpäin.  

# d) Nora CrackMe

Aloitin hakemalla Nora CrackMe tehtävien linkin GitHubista ja kloonasin tiedostot itselleni, **git clone https://github.com/NoraCodes/crackmes.git**:  
<img width="1003" height="238" alt="image" src="https://github.com/user-attachments/assets/afb82e04-be08-4e64-acea-01f35c21a39c" />  

Tästä siirryin sitten omaan **crackmes** hakemistoon ja avasin siellä olevan README tiedoston, josta löytyi ohjeet miten tehtävät saa toimimaan.  
<img width="1915" height="56" alt="image" src="https://github.com/user-attachments/assets/92fe1f32-cbe4-4752-906c-b1ab7b8dfccf" />  

Lähdin sitten kääntämään tiedostot binääriksi **make** komennolla:  
<img width="1002" height="581" alt="image" src="https://github.com/user-attachments/assets/4fee048c-003b-4ac3-8717-3de54fb516a6" />  

# e) Nora crackme01. Solve the binary.

Importtasin käännetyn **crackme01** tiedoston omaan Ghidra projektiin ja lähdin tutkimaan sitä. Lähdin katsomaan main funktiota, josta paljastui heti salasana "password1":  
<img width="359" height="365" alt="image" src="https://github.com/user-attachments/assets/9c3c2459-bf1f-48f8-826b-103fce06d48e" />  

Kokeilin sitten toimiiko salasana kun ohjelman ajaa:  
<img width="397" height="79" alt="image" src="https://github.com/user-attachments/assets/b4615464-d007-4906-8c1c-aec665b4858b" />  

Ja se toimi sillä.  

# e) Nora crackme01e. Solve the binary.

Lähdin tekemään tehtävää samalla tavalla kuin aikasempaakin, eli importtasin tehtävän Ghidra projektiini ja rupesin analysoimaan main funktiota:  
<img width="356" height="369" alt="image" src="https://github.com/user-attachments/assets/c30487a6-60f4-4c9d-b4e7-74d2df8c5cf0" />  

Koodista löytyi salasana "slm!paas.k", jota lähin kokeilemaan:  
<img width="438" height="82" alt="image" src="https://github.com/user-attachments/assets/810694aa-4175-4ee6-828b-18e7bd64d9e4" />  

Mutta se ei toiminutkaan. Lähdin tästä googlailemaan tuota "event not found" tulostetta ja löysin ohjeet, jossa kerrottiin, että "!" käytetään komentohistorian laajennukseen, ja ohjeistettiin käyttämään yksittäis hipsukoita tämän estämiseen, joten lähdin kokeilemaan niitä:  
<img width="462" height="82" alt="image" src="https://github.com/user-attachments/assets/ad1961c1-dd32-43f9-b112-1066ca9e2475" />  

Ja nyt ohjelma toimi, kun lisäsi hipsukat salasanan ympärille!

# f) Nora crackme02.

Aloitin tehtävän taas importtaamalla sen omaan Ghidra projektiini, avaamalla mainin decompileriin ja tuijottamalla koodia. Kuvan koodissa olen jo muokannut muuttujien nimiä.  
<img width="386" height="484" alt="image" src="https://github.com/user-attachments/assets/01e8bf6d-1535-4018-ba71-957a58e513dc" />  

Koodista löytyi salasana "password1", mitä lähdin kokeilemaan, mutta se ei toiminut.  
<img width="407" height="79" alt="image" src="https://github.com/user-attachments/assets/881de0bb-fff1-49d2-b729-198b8817e0ac" />  

Olin aika hukassa tässä kohtaa, eikä koodin tuijottaminen edistänyt yhtään mitään, joten kysäsin tekoälyltä (Claude Sonnet 5) apua koodin ymmärtämisessä ja vinkkejä. Sain ehdotukseksi laskea Pythonilla, mitä jokainen merkki salasanasta "passsword1" tarkoittaa ASCII-taulukossa, joten lähdin työstämään sitä.  
Koska koodissa ei varrata käyttäjän antamaa merkkiä suoraan "password1":n, vaan (expected_char - 1):een, niin pitää ottaa tuo -1 python laskuun mukaan. 
Avasin **python3** komentotulkissa ja rupesin laskemaan jokaista merkkiä yksitellen, funktiolla **chr(ord('')-1)**  
<img width="615" height="602" alt="image" src="https://github.com/user-attachments/assets/6af2f684-0952-4f12-aadd-8ce689f0e339" />  

Tulokseksi sain "o`rrvnqc0". Kokeilin sitten että toimiiko tämä, vai ei:  
<img width="420" height="87" alt="image" src="https://github.com/user-attachments/assets/ae0857c6-19c3-407b-bfa5-ba9a4ae25960" />  

No tulokseksi tuli "bquote". Lähdin sitten hakemaan tietoa että mitäs tämä meinaa ja selvisi että komentorivi tulkitsee tämän keskeneräisenä komentona.  
Yritin sitten samaa merkkiriviä, mutta nyt lisäsin siihen yksittäis hipsukat mukaan:  
<img width="436" height="110" alt="image" src="https://github.com/user-attachments/assets/8266a811-0ee9-4cdc-8ba2-4c09f0ac2b7e" />  

Ja nyt se sitten toimi! 

## Lähteet

echo "#!" fails -- "event not found" - https://stackoverflow.com/questions/11816122/echo-fails-event-not-found.  
Understanding Branch Control Instructions in 8086: JMP, JNZ, and LOOP Explained - https://magica.com/youtube-summarizer/understanding-branch-control-instructions-in-8086-jmp-jnz-and-loop-explained-_IW0stOy1Cs.  
What causes bquote prompt? - https://labex.io/questions/what-causes-bquote-prompt-504238.  
Karvinen, T. Sovellusten hakkerointi - https://terokarvinen.com/application-hacking/.  



