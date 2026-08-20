### a)
Ensin luodaan C koodille tiedosto, itse tein sen VI:llä. Nimesin tiedoston "vi apua.c"  

Tehdään tiedoston sisälle ihan perus "Hello, World"  
<img width="529" height="221" alt="Näyttökuva 2026-08-20 155358" src="https://github.com/user-attachments/assets/15270a5a-6a24-4ae6-9835-1e05ba346038" />  
Koodin kirjoittamisen jälkeen ajetaan komento:  
````
gcc apua.c -o apua
````
Tämän jälkeen kokeillaan toimiiko koodi:
````
./apua
````
<img width="294" height="102" alt="image" src="https://github.com/user-attachments/assets/43fcc92c-8f7f-4825-9a3f-c198d81de7b6" />  

Nähdään että koodi tulostui oikein.  
Nyt voidaan ihmetellä binääriä:  
````
file apua
````
<img width="1896" height="141" alt="Näyttökuva 2026-08-20 155307" src="https://github.com/user-attachments/assets/191852a4-4532-422f-a7eb-2b06aa5080ec" />  


## Lähteet
https://www.geeksforgeeks.org/c/compiling-a-c-program-behind-the-scenes/ Käytin tätä koodin kirjoittamiseen ja gcc compiler komentoon.
Luennolta sain tiedot miten pääsee binääriin käsiksi. 
