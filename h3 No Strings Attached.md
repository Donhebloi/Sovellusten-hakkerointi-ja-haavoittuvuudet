# Ympäristö

Käyttöjärjestelmä: Kali GNU  
Laitteisto: Thinkpad T14, Oracle VirtualBox Manager  
Selain: Firefox  
Verkko: NAT<br/>  
  

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
