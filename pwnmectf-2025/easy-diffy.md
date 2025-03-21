# 🛠 Easy Diffy

### 🎯 Catégorie : `Cryptographie`
### 🔥 Difficulté : `Facile`

## 📝 Description
Ce challenge met en avant une faiblesse dans l'échange de clés Diffie-Hellman lorsque le générateur `g` est mal choisi. Ici, `g = p-1`, ce qui réduit drastiquement l'espace des clés possibles, rendant l'attaque triviale.


Notre objectif est de retrouver la clé de chiffrement et déchiffrer le message.

## 🔎 Analyse du code

Voici le script Python qui génère les paramètres et chiffre le message :

```python
from Crypto.Util.number import getPrime, long_to_bytes
from Crypto.Util.Padding import pad, unpad
from Crypto.Cipher import AES
from hashlib import sha256

import os

flag = b"REDACTED"

p = getPrime(1536)
g = p-1

a = getPrime(1536)
b = getPrime(1536)

A = pow(g, a, p)
B = pow(g, b, p)

assert pow(A, b, p) == pow(B, a, p)

C = pow(B, a, p)

key = long_to_bytes(C)
key = sha256(key).digest()[:16]

cipher = AES.new(key, AES.MODE_ECB)
ciphertext = cipher.encrypt(pad(flag, AES.block_size))

print(f"{p = }")
print(f"{g = }")
print("ciphertext =", ciphertext.hex())
```

#### 📌 Sortie du script :

```
p = 174052774335651853087321900451795431774240591645094501021151463030703022582562...
g = 174052774335651853087321900451795431774240591645094501021151463030703022582562...
ciphertext = f2803af955eebc0b24cf872f3c9e3c1fdd072c6da1202fe3c7250fd1058c0bc810b052...
```

## 🛠 Explication de la vulnérabilité
Lorsqu'on choisit `g = p-1`, les puissances de `g` prennent des valeurs très limitées :
- Si `a` et `b` sont pairs, alors `C = 1`
- Si `a` ou `b` est impair, alors `C = p-1`

Cela signifie que la clé secrète partagée `C` n'a que deux valeurs possibles, ce qui permet de facilement la deviner et de décrypter le message.


## 💀 Exploitation  

```python
from Crypto.Util.number import long_to_bytes
from Crypto.Util.Padding import unpad
from Crypto.Cipher import AES
from hashlib import sha256

# Paramètres donnés
p = 1740527743356518530873219004517954317742405916450945010211514630307030225825627940655848700898186119703288416676610512180281414181211686282526701502342109420226095690170506537523420657033019751819646839624557146950127906808859045989204720555752289247833349649020285507405445896768256093961814925065500513967524214087124440421275882981975756344900858314408284866222751684730112931487043308502610244878601557822285922054548064505819094588752116864763643689272130951
g = p - 1
ciphertext_hex = "f2803af955eebc0b24cf872f3c9e3c1fdd072c6da1202fe3c7250fd1058c0bc810b052cf99ebfe424ce82dc31a3ba94f"
ciphertext = bytes.fromhex(ciphertext_hex)

# Exploitation : Avec g = p-1, le secret partagé C ne peut être que 1 ou p-1
possible_C_values = [1, p - 1]

for C in possible_C_values:
    key = long_to_bytes(C)
    key = sha256(key).digest()[:16]
    
    try:
        cipher = AES.new(key, AES.MODE_ECB)
        decrypted = unpad(cipher.decrypt(ciphertext), AES.block_size)
        if b'PWNME{' in decrypted:
            print(f"✅ Flag trouvé : {decrypted.decode()}")
    except Exception:
        continue
```

## 🏁 Flag  
En exécutant le script ci-dessus, on trouve directement le flag :

```bash
python3 decode.py
✅ Flag trouvé : PWNME{411_my_h0m13s_h4t35_sm411_Gs}
```

## 🚀 Conclusion  

Ce challenge illustre un piège classique en cryptographie : un mauvais choix de générateur peut complètement compromettre la sécurité d'un échange de clés.

### 📝  Autres Write-Ups du même challenge

https://ctftime.org/writeup/40065