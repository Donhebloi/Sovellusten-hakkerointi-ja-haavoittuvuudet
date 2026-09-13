# h6 Onkohan tämä turvallinen käyttää?  

Aloitin lataamalla kameran ohjelmiston omaan Kali virtuaalikoneeseen.  
Lähdin tutkimaan tiedostoa ensiksi **file** komennolla, jotta selviäisi millainen tiedosto on kyseessä:  
<img width="1471" height="96" alt="image" src="https://github.com/user-attachments/assets/5f9cc158-dc9d-4a36-94fc-3a97cd65c2ca" />  
File komennon tulokseksi tuli **"data"**, eli tiedosto ei nyt täsmää mihinkään tunnettuun formaattiin.  
Kokeilin seuraavaksi **binwalk**:ia: 
<img width="1534" height="213" alt="image" src="https://github.com/user-attachments/assets/397b30b1-9a78-4fdd-8107-2f2b3c5f972e" />  
**Binwalk** ei nyt löytänyt mitään, joten voidaan olettaa että data on salattu.  
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
