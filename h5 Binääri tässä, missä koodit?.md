# 1 - main.cpp 

Kävimme tunnilla läpi mikä GDB on ja sen perusasiat jonka jälkeen päästiin tekemään esimerkkitehtävää. 
Esimerkkitehtävässä koodi ei toiminut oikein niin sitä selviteltiin debuggerin avulla.  
Alunperin kun ohjelma ajettiin niin saatiin tulokseksi **0**:  
<img width="271" height="206" alt="image" src="https://github.com/user-attachments/assets/c2c76bc3-8c26-4929-b409-875440cc2dc8" />  
Aloitettiin kääntämällä ohjelma  
````
g++ main.cpp -g -Wall -Werror -o main-dbg
````
ja käynnistämällä debugger  
````
gdb ./main-dbg
````
Asetettiin breakpoint riville 11
````
break 11
````
ja ajettiin ohjelma **run** komennolla  
Valitsin esimerkkinumeroksi **4**  
Käytimme **step** komentoa päästääksemme factorial funktion sisään  
Asetettiin **watch n** ja **watch result**   
Sitten katsottiin **next** ja **continue** komennoilla mitä arvoja **n** ja **result** saavat.  
<img width="670" height="120" alt="image" src="https://github.com/user-attachments/assets/d801c665-119d-4c10-b9ba-0fc050bf49b4" />  

<img width="1020" height="772" alt="image" src="https://github.com/user-attachments/assets/73091d3a-4583-46a3-950f-c88ef3e0e148" />  

Tässä saadaan ohjelman bugi selville. Ohjelman pitäisi kertoa **result** alkuperäisellä **n** arvolla, mutta se kertoo sen aina yhtä pienemmällä arvolla.  
Korjataan koodi avaamalla se ja muokkaamalla nanossa **nano main.cpp**  
Alkuperäinen koodi:  
<img width="337" height="632" alt="image" src="https://github.com/user-attachments/assets/0b9846c3-cc94-4105-9b48-a58e2749c05e" />  

Korjattu koodi:  
<img width="345" height="636" alt="image" src="https://github.com/user-attachments/assets/7029319d-c018-4031-a70b-c742dc85195a" />  
Koodin silmukkaa muutettiin siis:  
````
long result(1);
while(n > 0)
{
    result *= n;
    n--;
}
return result;
````
Käännetään ohjelma uudelleen **g++ main.cpp -g -Wall -Werror -o main-dbg** ja ajetaan se **./main-dbg**  
<img width="256" height="99" alt="image" src="https://github.com/user-attachments/assets/711c160a-c858-4ecb-b41a-3c021f66d001" />  

Nyt ohjelma toimii ja tulostaa oikean arvon, eli tässä tapauksessa 24.  


# 2 - Lab0  
Tässä pääsi itsenäisesti ihmettelemään ja harjoittelemaan debuggerin käyttöä. Tarkoituksena olisi etsiä ohjelman virhe ja korjata se.  
Aloitettiin ensiksi unzippaamalla ohjelma:  
<img width="448" height="184" alt="image" src="https://github.com/user-attachments/assets/1cef911f-20a6-42a4-8d94-70174175efaf" />  

Jonka jälkeen kokeiltiin ajaa ohjelma jotta nähtäisiin mitä se yrittää tulostaa:  
<img width="315" height="186" alt="image" src="https://github.com/user-attachments/assets/5a865be2-bd2b-4cc1-a14c-0f9198dd693d" />  

Tästä huomasimme että tulostuksen elementit eivät ole oikein.  
Lähdettiin sitten tutkimaan koodia debuggerin avulla:  
**g++ buggy_program.c -g -Wall -Werror -o buggy-dbg**  
Käynnistettiin debugger:  
**gdb ./buggy-dbg**  
Tulostettiin koodi näkyviin **list** komennolla:  
<img width="1134" height="732" alt="image" src="https://github.com/user-attachments/assets/5f2dd6e6-76c7-453e-838f-52923d413e67" />  

Huomattiin että koodi on kirjoitettu väärin, jonka takia elementit tulostuivat väärin aikaisemmin.  
Lähdimme korjaamaan koodia avaamalla se nanossa, **nano buggy_program.c** ja muokkaamalla sitä:  
<img width="974" height="309" alt="image" src="https://github.com/user-attachments/assets/20814323-f9a3-409e-ae6a-e44116599b65" />  

Tämän jälkeen kun avattiin ja ajettiin ohjelma debuggerissa niin huomattiin että ohjelma tulostui oikein:  
<img width="1038" height="686" alt="image" src="https://github.com/user-attachments/assets/6a19d2c3-25a5-4432-af29-30e30e99fbd3" />  


# 3 - Lab1
Lähdetään tutkimaan minkä takia ohjelma kaatuu ajamalla se **run** komennolla debuggerissa:  
<img width="1019" height="210" alt="image" src="https://github.com/user-attachments/assets/8a2b896c-b1ae-4f04-b7a2-065759c9c1f5" />  

Huomataan että ohjelma kaatuu **print_scrambled** funktiossa.  
Aletaan korjaamaan ongelmaa avaamalla koodi nanossa **nano gdb_example1.c**  
Alkuperäinen koodi:  
<img width="527" height="459" alt="image" src="https://github.com/user-attachments/assets/61e6f213-c564-4e5f-95ab-ec03b1ee8743" />  

Lisätään koodiin: 
````
if (message == NULL) {
 return;
}
````  
Tallennetaan uusi lisäys koodiin ja hyökätään sitten takaisin debuggerin kimppuun.  
Pelkästään nanossa tallentaminen ei riitä, pitää ajaa myös debuggerin komento uudestaan jotta nähtäisiin tuliko muutokset voimaan. Eli:  
````
gcc gdb_example1.c -g -Wall -Werror -o gdb_example1-dbg  
````
Sitten mennään debuggeriin **gdb ./gdb_example1** ja ajetaan ohjelma uudestaan **run** komennolla:  
<img width="1024" height="725" alt="image" src="https://github.com/user-attachments/assets/9d02f6b5-0103-41cd-867f-a9bfcd25b594" />  

Nähdään että ohjelma menee nyt ongelmitta läpi ja tulostui teksti **Khoor/#zruog1**  
Ajoin ohjelman vielä uudestaan **./gdb_example1** komennolla jotta tulos näkyisi paremmin.  


# 4 - Lab2
Ensiksi unzipataan tehtävä, **unzip lab2** ja siirrytään tehtävän hakemistoon **cd** komennolla.  
Tutkitaan **ls** komennolla mitäs kaikkea tehtävän hakemistosta löytyy.  
Nähdään README.md tiedosto joten luetaan se ensiksi ja seurataan annettuja ohjeita.  
<img width="760" height="822" alt="image" src="https://github.com/user-attachments/assets/3a133e77-d7b4-4a95-a4d1-983b164d1abe" />  

Tehtävän **passtr** salasana sekä lippu löydetään ihan vaan lähdekoodia tutkimalla. Avataan lähdekoodin tiedosto, **cat passtr.c** ja katsotaan mitä sisällä on.  
<img width="1288" height="457" alt="image" src="https://github.com/user-attachments/assets/21db8d71-6157-47b3-9e68-323701fa6851" />  

Kokeillaan vielä varmuudenvuoksi toimiiko salasana ja saadaanko lippu tulostettua, ajamalla **passtr** ohjelma.  
Eli **./passtr** ja sen jälkeen syötetään salasana "sala-hakkeri-321"  
<img width="913" height="121" alt="image" src="https://github.com/user-attachments/assets/beb5cafa-b2ac-47ed-b819-456ee24b21e0" />  

Salasana toimii niin kuin pitääkin ja saatiin Teron lippu tulostettua. **FLAG{Tero-d75ee66af0a68663f15539ec0f46e3b1}**  
Toisen salasanan ja lipun saaminen on hankalempaa koska ei ole **passtr2o** ohjelman lähdekoodia saatavilla joten selvitetään se debuggerin avulla.
Debugger auki **gdb ./passtr2o** ja ekana listasin ohjelman funktiot **info functions** :  
<img width="552" height="457" alt="image" src="https://github.com/user-attachments/assets/5f38f56b-c0d6-4179-b741-9b6468e4e0f0" />  

Jonka jälkeen lähdin purkaamaan **main** funktiota, **disassemble main** komennolla:  
<img width="945" height="884" alt="image" src="https://github.com/user-attachments/assets/c7b4695f-c9ac-463c-bae1-b76ec8bf0100" />  

Main funktio näkyy nyt assembly koodina josta voi lähteä etsimään kutsuja muihin funktioihin.  
Koodista löytyy kutsut funktioihin:  
````
call scanf = lukee mitä on syötetty
call mAsdf3a = Vaikuttaa tarkistusfunktiolta
jne 0x1119 = Jos tulos ei ole tietty arvo, skipataan EaseEA
call EaseEAs = Tulostuu vain jos ehto täyttyy
call printf = Tulostaa jotain, oli se väärin tai ei
````
Tässä kohtaa vaikuttaa siltä, että **mAsdf3a** tarkistaa salasanan ja **EaseEA** tulostaa halutun lopputuloksen jos salasana on oikein.  
Lähdetään tutkimaan **mAsdf3a:ta** **disassemble mAsdf3a** komennolla:  
<img width="785" height="868" alt="image" src="https://github.com/user-attachments/assets/76bca2a1-1f66-434a-b45a-784bb4da3f03" />  

Main kutsuu **mAsdf3a:ta** kahdella parametrilla: **RDI** ja **RSI**, joista yksi on oikea salasana ja toinen itse syöttämä salasana.  
<img width="750" height="137" alt="image" src="https://github.com/user-attachments/assets/ace3f5b7-f318-4cd8-909c-6f514f2ec0d7" />  

Koodista myös löytyy kaava millä päästään selvittämään salasanaa, eli jos indeksi on pariton niin vähennetään 7 ja jos indeksi on parillinen, niin lisätään 3.  
Aletaan selvittämään salasanaa **break** komennon avulla debuggerissa:  
<img width="1016" height="321" alt="image" src="https://github.com/user-attachments/assets/600016e1-827b-468b-b6ba-331643cfa387" />  

Eli asetetaan breakpoint **mAsdf3a** funktioon, ajetaan ohjelma ja annetaan esimerkkisalasana, tulostetaan **rdi** desimaalina ja avataan se **x/s $rdi** komennolla.  
Nyt päästiin käsiksi kovakoodattuun salasanaan **"anLTj4u8"**.  
Kovakoodattu salasana ratkaistaan aikaisemmin löydetyllä kaavalla, eli parittomasta indeksistä vähennetään 7 ja parilliseen indeksiin lisätään 3.  
**"anLTj4u8"** merkkijonon pystyy muuntamaan **ASCII** taulukkoa käyttämällä, jonka saa esille **man ascii** komennolla.  
<img width="1190" height="879" alt="image" src="https://github.com/user-attachments/assets/82f4c7f9-6dd5-4ec6-8143-c7c153d7bd4c" /> (kuvassa vain osa taulukosta, itse taulukko jatkuu vielä)  

| Indeksi | Merkki | Parillinen/Pariton | Muunnos | Tulos |
| ------- | ------ | ------------------ | ------- | ----- | 
| 0       | a      | Parillinen         | +3      | d     | 
| 1       | n      | Pariton            | -7      | g     |
| 2       | L      | Parillinen         | +3      | O     |
| 3       | T      | Pariton            | -7      | M     |
| 4       | j      | Parillinen         | +3      | m     | 
| 5       | 4      | Pariton            | -7      | -     | 
| 6       | u      | Parillinen         | +3      | x     |
| 7       | 8      | Pariton            | -7      | 1     |  

Eli salasanaksi saatiin **"dgOMm-x1"**.  
Kokeillaan vielä että toimiiko salasana oikeasti ajamalla **./passtr2o**  
<img width="883" height="124" alt="image" src="https://github.com/user-attachments/assets/2e14321d-3641-4642-8fac-2ec1af8b942e" />  

Toimii! Saatiin Larin lippu näkyviin. **FLAG{Lari-rsvRDx04WMBZpuwg4qfYwzdcvVa0oym}**  
Tehtävässä opin käyttämään debuggeria paremmin, miten tutkitaan assembly koodia, tunnistamaan paremmin funktiokutsuja, funktion parametreista ja x/s komennon käyttötarkoituksen.  

# 5 - Lab3  
Valitsin ihan ensimmäisen crackme haasteen tehtäväksi eli **crackme01.64**.    
Ensiksi ajoin ohjelman random tekstillä jotta näkisin mitä se tulostaa:  
<img width="441" height="72" alt="image" src="https://github.com/user-attachments/assets/ee744a0e-6e35-40c3-911b-91bda18de91c" />  

Kokeilin **cat** komennolla avata ohjelman mutta eihän siitä saanut mitään selvää:  
<img width="1893" height="581" alt="image" src="https://github.com/user-attachments/assets/33df0152-496a-4ac5-bcee-a96e53cbeaa7" />  

Tämän jälkeen käytin **strings** komentoa jotta näkisin ohjelman tulostuskelpoiset merkkijonot:  
<img width="497" height="883" alt="image" src="https://github.com/user-attachments/assets/3de3283e-3984-4078-9156-dbb194f2dc98" />  

Listauksesta löytyy **password1** jota kokeilin ajaa ohjelman kanssa, eli **./crackme01.64 password1**:  
<img width="432" height="71" alt="image" src="https://github.com/user-attachments/assets/d1b62a6a-9c7f-4eee-afc5-3f1c3092b33a" />  

Ja sehän toimi! Tämä olikin sen verran simppeli tehtävä ettei edes tarvinnut debuggeria käyttää, kun salasana löytyi jo **strings** komennon avulla.  


## Lähteet

Tehtävänannon dynaamisen analyysin luentomateriaali  
Tehtävänannon GDB Cheat Sheet  
ASCII manuaali terminaalissa  
Assembly Instructions Guide - https://github.com/7etsuo/x86




