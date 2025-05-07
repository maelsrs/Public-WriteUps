
# 🏰 The Ancient Citadel  

### 🛠 Type : `OSINT`  
### 🎯 Difficulté : `Moyenne`  

## 🖼️ Image

![Image](ancientcitadel.png)

## 🔍 Recherche d'indices  

Le premier indice donné dans l'énigme montre une image d'un château avec des éléments architecturaux distinctifs. J'ai commencé par utiliser **Google Images**, mais cela ne donnait pas de résultats satisfaisants. Cependant, en utilisant **Yandex**, j'ai pu trouver une page qui semblait correspondre à l'image, ainsi que le nom **Castillo Brunet**.

## 🌍 Trouver la localisation exacte  

Une fois avoir trouvé le nom **Castillo Brunet**, j'ai recherché davantage d'informations en ligne. Un site officiel du gouvernement chilien, [monumentos.gob.cl](https://www.monumentos.gob.cl/monumentos/monumentos-historicos/castillo-brunet), fournit des détails sur ce château, situé dans la ville de **Viña del Mar**, dans la région de **Valparaíso**, au Chili.

J'ai également utilisé **Google Maps** pour examiner l'emplacement exact de ce château via Street View :  
[Google Maps](https://www.google.fr/maps/@-33.0218953,-71.5620242,3a,75y,29.58h,63.8t/data=!3m7!1e1!3m5!1sr7sCfUis5iB71rQtA-4q-A!2e0!6shttps:%2F%2Fstreetviewpixels-pa.googleapis.com%2Fv1%2Fthumbnail%3Fcb_client%3Dmaps_sv.tactile%26w%3D900%26h%3D600%26pitch%3D26.198830238868304%26panoid%3Dr7sCfUis5iB71rQtA-4q-A%26yaw%3D29.58227933180718!7i16384!8i8192?hl=fr&entry=ttu&g_ep=EgoyMDI1MDMxOS4yIKXMDSoASAFQAw%3D%3D).


## 🔑 Le premier essai  

En suivant la logique de l'énigme, j'ai tenté de valider le flag avec un format approximatif. Voici le premier essai :  
```
HTB{Iberia_104_2520000_Viña_del_Mar_Valparaíso}
```
Cependant, cela ne passait pas. Après 30 minutes de recherche, j'ai découvert que le **code postal** était incorrect, même si la ville était la même. En vérifiant sur [Tripadvisor](https://www.tripadvisor.es/ShowUserReviews-g295425-d9798635-r594707095-Brunet_Castle-Vina_del_Mar_Valparaiso_Region.html), j'ai pu trouver le **code postal** correspondant



## 🏁 Flag  

Finalement, le flag correct était :  
```
HTB{Iberia_104_2571409_Viña_del_Mar_Valparaíso}
```