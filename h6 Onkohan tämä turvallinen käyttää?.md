# h6 Onkohan tämä turvallinen käyttää?  

## Johdanto

Tavoitteena oli tutkia Tapo C200 -kameran ohjelmiston turvallisuutta koulussa opituilla menetelmillä. Yritin selvittää ohjelmiston rakennetta, tiedostojärjestelmiä sekä ohjelmiston salasanaa ja salaukseen liittyviä toimintoja.  
Suoritin tehtävän Kali Linux -virtuaalikoneessa, käyttämällä **file**-, **binwalk**-, **strings**-, **grep**-, **readelf**-, ja GDB-työkaluja.  
Yritin purkaa ohjelmiston analysoitavaan muotoon jotta voisin tutkia kameran sisältämää Linux-pohjaista tiedostojärjestelmää ja sen ohjelmistoja.  
Tarkoituksena oli selvittää mahdolliset haavoittuvuudet, turvallisuuteen liittyvät ominaisuudet ja mahdolliset ongelmat sekä mahdollisesti saada salasana selville.  

## Tekninen osuus

Aloitin lataamalla kameran ohjelmiston omaan Kali virtuaalikoneeseen.  
Lähdin tutkimaan tiedostoa ensiksi **file** komennolla, jotta selviäisi millainen tiedosto on kyseessä:  
<img width="1471" height="96" alt="image" src="https://github.com/user-attachments/assets/5f9cc158-dc9d-4a36-94fc-3a97cd65c2ca" />  
File komennon tulokseksi tuli **"data"**, eli tiedosto ei nyt täsmää mihinkään tunnettuun formaattiin.  
Kokeilin seuraavaksi **binwalk**:ia: 
<img width="1534" height="213" alt="image" src="https://github.com/user-attachments/assets/397b30b1-9a78-4fdd-8107-2f2b3c5f972e" />  
**Binwalk** ei nyt löytänyt mitään, joten voidaan olettaa että tiedosto ei ole suoraan analysoitavasa muodossa.  
Tämän jälkeen lähdin hyödyntämään kurssimateriaaleista ja githubista löytyvää **tp-link-decrypt** työkalua.  
Tein itselleni **decrypt_tool** hakemiston mihin purin ladatun **tp-link-decrypt.tar.gz** tiedoston, **tar xzvf tp-link-decrypt.tar.gz -C decrypt_tool**:    
<img width="879" height="156" alt="image" src="https://github.com/user-attachments/assets/ac3091a7-eec9-46be-b4ce-a264e978fe1e" />  
Tarkistin vielä **ls** komennolla että tiedosto näkyi purettuna hakemiston sisällä:  
<img width="554" height="102" alt="image" src="https://github.com/user-attachments/assets/c37e46cb-84ef-49e9-80d5-9e4152ff7984" />  
Nyt voi siirtyä **tp-link-decrypt** hakemiston sisälle ja katsoa mitä siellä sisällä on:  
<img width="1845" height="216" alt="image" src="https://github.com/user-attachments/assets/8be60901-e94c-4147-97b1-9e6255d1e147" />  
Huomataan **"README"** tiedosto, joten avataan se ekana **cat** komennolla ja luetaan läpi:  
<img width="817" height="86" alt="image" src="https://github.com/user-attachments/assets/21920169-c3d0-4262-bd0d-2971a2a70840" />  
**"README"** tiedostossa on ohjeet siihen miten jatketaan:  
<img width="1888" height="282" alt="image" src="https://github.com/user-attachments/assets/742e23ac-aa90-437f-8fac-259ae76f189f" />  
Eli ekana hoidetaan riippuvuudet kuntoon **./preinstall.sh** komennolla:  
<img width="819" height="66" alt="image" src="https://github.com/user-attachments/assets/dc2d509c-431f-46ee-96e3-cdd9c2d25517" />  
Latauksessa minulle tuli seuraavat ongelmat vastaan:  
````
Error: Unable to locate package binutils-mips-linux-gnu
[WARNING] Failed to install binutils-mips-linux-gnu. Skipping.
mips-linux-gnu-nm: [WARNING] mips-linux-gnu-nm not found.
````
Ongelmista huolimatta lähdin silti kokeilemaan ohjeen seuraavaa vaihetta eli **./extract_keys.sh**. Komennon ajamisen jälkeen tuli hirveä litania erilaista tekstiä, mutta siitä tärkein oli:    
<img width="1041" height="40" alt="image" src="https://github.com/user-attachments/assets/bc2ca398-c63e-411b-9b23-34faeebbd95c" />  
Eli avaimien extraction onnistui ja skripti kirjoitti RSA-avaimet **"include"** kansioon, aikaisemmista virheilmoituksista huolimatta.  
Sitten käytin **make** komentoa **src** kansion lähdekoodin kääntämiseen ja avaimien linkittämiseen siihen.  
<img width="821" height="87" alt="image" src="https://github.com/user-attachments/assets/4a9c3b16-23aa-40d5-802b-5d07e00252ea" />  
Nyt voi lähteä purkamaan kameran ohjelmistoa **bin/tp-link-decrypt** avulla:  
````
bin/tp-link-decrypt ~/Tapo_C200v3_en_1.4.2_Build_250313_Rel.40499n_up_boot-signed_1747894968535.bin
````
<img width="1903" height="571" alt="image" src="https://github.com/user-attachments/assets/047bcaee-4e66-4509-9d5a-2b734db8e113" />  
Nyt näemme TP-linkin ohjelmiston key/iv:n:  

````
key/iv:
KEY=9c6ba1d761e4eee17dfde90cfed603bd
IV=8778f31423815ce85e9f186b60507edd
````

Avaimen ja IV:n arvo löytyivät TP-linkin omasta julkisesta GPL-paketista, eikä niiden löytämiseen tarvinnut käyttää brute force murtoa. Tässä tulee confidentialy näkökulma mieleen, sillä kuka tahansa voi lukea ohjelmiston sisällön, eikä vain valmistaja.  
Nyt kun siirrytään kotihakemistoon niin huomataan **ls** komennolla uusi tiedosto:  
<img width="1406" height="36" alt="image" src="https://github.com/user-attachments/assets/0759c4ef-26b6-451c-9258-fa84b6489671" />  
Tämä on ohjelmiston purettu tiedosto, jota voidaan lähteä nyt analysoimaan **file** ja **binwalk** komennoilla.  
Ensiksi ajoin uudestaan **file** komennon ja se tulosti saman tuloksen kuin viimeksi, eli **"data"**:  
<img width="1543" height="97" alt="image" src="https://github.com/user-attachments/assets/82964c0a-76af-4407-b0b6-fc2a313a6d60" />  
Mutta nyt kun tämän jälkeen ajoi uudestaan **binwalk** komennon niin tulostui ihan hirveä määrä kaikkea tietoa:  
<img width="1891" height="660" alt="image" src="https://github.com/user-attachments/assets/04616e73-6e65-43db-abd2-aa26fadbe399" />  
XZ comressed data rivejä on paljon enemmän kuin mitä kuvassa näkyy, mutta kuvasta tärkein havainto on:  

````
OS: Linux, CPU: MIPS, image type: OS Kernel Image, compression type: lzma, image name: "mips Ingenic Linux-3.10.14"
````

Eli tästä selviää että laite käyttää **MIPS** arkkitehtuuria, minkä takia sain aikaisemmin varoituksia puuttuvista MIPS-työkaluista.  
**Binwalk**:in viimeisimmistä riveistä löytyy toinen tärkeä rivi:  
<img width="1905" height="60" alt="image" src="https://github.com/user-attachments/assets/053a51b8-127d-46cc-9be4-4cc6f28a33ff" />  

````
4063744 0x3E0200 Squashfs filesystem, little endian, version 4.0, compression:xz, size: 3032084 bytes, 96 inode
````

Tältä riviltä selviää laitteen käyttöjärjestelmän tiedostorakenne.  
Seuraavaksi lähdin purkamaan **"squashfs"** osiota.  
Nyt kun on tarkka offset tiedossa eli **"4063744 0x3E0200"**, niin on helpompaa purkaa vain se osa, eikä taas koko tiedostoa. Eli **binwalkataan** taas:    
<img width="1655" height="57" alt="image" src="https://github.com/user-attachments/assets/50fb037c-71d8-4601-9f11-033608c89448" />  
Ja nyt tarkistetaan **ls -la** komennolla onnistuiko purku vai ei:  
<img width="1760" height="63" alt="image" src="https://github.com/user-attachments/assets/540d4972-28dd-4d6f-8ab4-5b38a11ca201" />  
Aivan viimeisimpänä listasta löytyi **"squashfs-root"** kansio, eli binwalk tunnisti **"squashfs"** osion ja onnistui purkamaan sen:  
<img width="989" height="36" alt="image" src="https://github.com/user-attachments/assets/008bb286-c821-4595-ad6d-1e84fda2381f" />  
Nyt sitten siirryin tuohon **"squashfs-root"** kansioon ja katsoin mitä siellä on **ls** komennolla:  
<img width="1899" height="114" alt="image" src="https://github.com/user-attachments/assets/a1345606-0f1d-431a-8490-c588451c44df" />  
Tämän jälkeen lähdin tutkimaan **strings** komennolla **bin/main**:ia ja yhdistin sen grep komentoon, löytääkseni helpommin tärkeät funktionimet:
<img width="1909" height="90" alt="image" src="https://github.com/user-attachments/assets/a5c1c2ae-e13a-41e3-b4ee-ffddf58393dc" />  

````
strings bin/main | grep -iE "passwd|admin|default|login|auth"
````

Tästä tulostui selkeä lista merkkijonoja jotka viittaavat ohjelman toiminnallisuuteen. Binääriin jätetyt ohjelmaan liittyvät nimet, debug ja lokitekstit voivat helpottaa analysointia ja reverse engineeringiä.    
Silmääni iskivät funktiot:  
**gen_root_passwd** mikä nimen perusteella generoi root salasanan.  
<img width="271" height="35" alt="image" src="https://github.com/user-attachments/assets/25306a93-123b-49f1-bfd2-45039e31d40b" />  

**update_root_passwd_for_encrypt**, mikä voisi tarkoittaa että salasana salataan ennen tallennusta.  
<img width="507" height="33" alt="image" src="https://github.com/user-attachments/assets/9077106b-48e6-4a81-95bb-ed0c4a64bf8b" />  

**[HUB_MANAGE]root_passwd:%s**, tämä voisi tarkoittaa että ohjelmassa on loki- tai viestikenttä, jossa käsitellään rootin salasanaa.  
<img width="448" height="36" alt="image" src="https://github.com/user-attachments/assets/d2aaea98-2af1-448a-a810-51c78175a641" />  

**factory_passwd**, voisi olla tehdasasetusten salasana tai liittyy jotenkin sen käsittelyyn.  
<img width="263" height="44" alt="image" src="https://github.com/user-attachments/assets/04be3a1a-3b69-4f34-9a1b-b54b0f3328a8" />  

**WWW-Authenticate: Digest realm="%s",algorithm="MD5"**, binääristä löytyi Digest autentikointiin liittyvä merkkijono, jonka algoritmiksi ilmoitetaan MD5 mikä on jo kryptografisesti vanhentunut menetelmä.  
<img width="870" height="42" alt="image" src="https://github.com/user-attachments/assets/fa1c9dec-41a0-4c76-ac65-bae57584959c" />  

**auth_rsa_decrypt**, osa autentikoinnista vaikuttaisi käyttävän taas RSA-salausta  
<img width="294" height="39" alt="image" src="https://github.com/user-attachments/assets/7186f7b4-53fc-41ae-8faa-d3ece06a5f9b" />  

Kuitenkaan pelkän **strings** tulosten perusteella ei pysty varmistamaan mitä nämä merkkijonot oikeasti tekevät.  

Tästä sitten yritin tutkia debuggerilla **bin/main**:ia ja listasta löytyvää **gen_root_passwd** funktiota:  
<img width="1312" height="717" alt="image" src="https://github.com/user-attachments/assets/c88fe84b-9052-45d5-9b7f-200fd9a20235" />  
Ja sehän ei onnistunut. Kokeilin disassembloida funktion, mutta se ei onnistunut koska symbolitaulu ei ole käytössä ja koska käyttämäni debugger ei perustu MIPS-arkkitehtuuriin.  
Kun tästä ei nyt sitten tullut mitään haluttua tulosta, siirryin kameran dump-tiedoston kimppuun jotta saisin ehkä jotain järkevää tehtyä.  
Aloitin taas **file** ja **binwalk** komennoilla:  
<img width="669" height="189" alt="image" src="https://github.com/user-attachments/assets/c1bb1f76-711b-41e0-9308-14d652e5ec02" />  
**file** komennosta tulostui taas sama **"data"** ja **binwalkista** hirveä litania tietoa, mutta dumpin tiedoissa oli uutta tietoa:  
<img width="1913" height="88" alt="image" src="https://github.com/user-attachments/assets/5b36bf7c-cf14-4aaa-9856-fdf439a8ffeb" />  
Dumpissa on U-Bootloader mitä kameran ohjelmiston tiedoissa ei ollut.  
Seuraavaksi lähdin purkamaan dumppia **binwalk -e  dump-tapo-c200v3-1.4.2.bin**   
<img width="740" height="60" alt="image" src="https://github.com/user-attachments/assets/287fca54-3c74-42bf-adfc-67ff4da6c63d" />  
Purkamisen jälkeen tarkistin **ls -la** komennolla löytyykö sieltäkin **squashfs-root** kansio:  
<img width="999" height="33" alt="image" src="https://github.com/user-attachments/assets/529b4781-5003-4c66-af50-e927746bc830" />  
Ja siellähän se oli.
Yritin tästä sitten etsiä **grep**:illä onko dumpissa mitään selväkielisiä salasanoihin, avaimiin, käyttäjiin ja autentikointiin liittyviä viittauksia.  
<img width="1893" height="758" alt="image" src="https://github.com/user-attachments/assets/6089d2e3-5a4f-4087-b1f9-83a773a32998" />  
Tästä ei nyt suoraan paljastunut salasanaa tai mitään arkaluontoista tietoa, mutta näkyviin tuli toimintoja jotka liittyvät admin-salasanan muuttamiseen, käyttäjätileihin, P2P-salasanan sekä AES-avaimen hakemiseen, joita voisi pitää mahdollisina hyökkäyskohteina.    
Tässä kohtaa alkoi tuntumaan aika toivottamalta, kun ei saanut oikein mitään järkevää tehtyä tai selvitettyä.  
Ajattelin vielä että voisin **file** komennolla tarkistaa millainen tiedosto **bin/main** on:  
<img width="1894" height="128" alt="image" src="https://github.com/user-attachments/assets/797913c3-8905-44ab-bb51-2537f3d39001" />  
Tästä selkeästi huomasi että **bin/main** on stripped, eli binääristä on poistettu symbolitaulun tietoja. Tämä on tietoturvan kannalta hyvä asia koska tämä parantaa ohjelman suojausta analysointa vastaan.    
Päätin vielä kokeilla **readelf** ja **grep** komennoilla löytää funktiosymboleita jotka viittaisivat salasanaan:  
<img width="1659" height="82" alt="image" src="https://github.com/user-attachments/assets/3522a814-efd4-4456-bb8d-858959b360e7" />  
Eipä löytynyt.  
Tässä kohtaa päätin lopettaa analyysin kanssa painimisen, kun omat taidot loppuivat kesken enkä keksinyt uusia tapoja lähteä tutkimaan ohjelmistoa.  

## Onko mahdollista käyttää hyödyksi löytämiä haavoittuvuuksia?















