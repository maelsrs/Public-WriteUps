# 🔍 ProfileEditor  

### 🛠 Type : `Web`  
### 🎯 Difficulté : `Facile`  

---

## 📝 Analyse du challenge  

Dans ce challenge, nous avons accès au code source, ce qui permet d'analyser la logique de l'application.  

- Pas de mode debug Flask activé.  
- Pas de vulnérabilité de type **session hijacking**.  
- Rien de suspect à première vue.

Voyons maintenant le site en lui-même :  
- Connexion et navigation fonctionnent sans problème, mais très peu de choses a faire
---

## 🛠️ Analyse du code  

Une ligne particulière attire l'attention :

```python
profiles_file = 'profile/' + session.get('username')
```

Ici, `username` est directement concaténé à un chemin de fichier, ce qui laisse entrevoir un **path traversal**

En modifiant le `username`, il est possible d'accéder à d'autres fichiers du serveur.

---

## 💀 Exploitation  

En injectant `../flag.txt` comme nom d'utilisateur, nous réussissons à lire le contenu du fichier contenant le flag en allant sur notre profile.

---

## 🏁 Flag  

Flag :  
```
PWNME{afb7daa3a595522882f6a9efae30c4ec}
```

---

## 🚀 Conclusion  

Ce challenge met en évidence une faille classique de **path traversal**, qui peut permettre l'accès à des fichiers sensibles du serveur. Il ne faut jamais faire confiance a l'utilisateur et toujours penser a __sanitize__ ses inputs.


### 📝  Autres Write-Ups du même challenge

https://a51f221b.vercel.app/blog/PwnMeCTF-2025