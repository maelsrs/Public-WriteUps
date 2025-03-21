# 🏴‍☠️ Finisteg  

### 🛠 Type : `Reverse`  
### 🎯 Difficulté : `Facile`  

## 📝 Description

Ce challenge est un reverse d'APK, un inédit pour moi.  

> 💡 **Note** : Je n'ai pas résolu ce challenge de la manière attendue (modification de l'APK avec des outils comme `apktool`), mais avec une approche alternative.

---

## 💍 Décompilation de l'APK  

Pour analyser l'APK, j'ai utilisé [Java Decompiler](http://www.javadecompilers.com/apk), mais j'aurais pu employer `jadx`, que je n'avais pas installé.  

Parmi les nombreux fichiers, j'ai identifié la classe principale :  
📌 `sources/com/example/finisteg/MainActivity.java`  

Voici son code principal :  

```java
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    final EditText flagInput = (EditText) findViewById(R.id.flagInput);
    BitmapFactory.Options o = new BitmapFactory.Options();
    o.inScaled = false;
    final Bitmap bm = BitmapFactory.decodeResource(getResources(), R.drawable.breizhctf_logo, o);
    ((Button) findViewById(R.id.check_button)).setOnClickListener(new View.OnClickListener() {
        public void onClick(View v) {
            if (MainActivity.this.decodeBase64(MainActivity.this.extractTextFromImage(bm)).equals(flagInput.getText().toString())) {
                Toast.makeText(MainActivity.this, "Flag Good!", 1).show();
            } else {
                Toast.makeText(MainActivity.this, "Flag NOT Good...", 1).show();
            }
        }
    });
}
```

🔎 L'objectif est que l'entrée utilisateur corresponde à la sortie de la fonction :  
```java
this.decodeBase64(MainActivity.this.extractTextFromImage(bm))
```

L'image `breizhctf_logo.png` située dans `resources/res/drawable/` semble donc contenir un texte caché.

---

## 🤨 Analyse de `extractTextFromImage()`

Lors de la décompilation, la fonction a été mal reconstruite, ce qui rend son analyse difficile :  

```java
/* JADX WARNING: Removed duplicated region for block */
/* Code decompiled incorrectly, please refer to instructions dump. */
public java.lang.String extractTextFromImage(android.graphics.Bitmap r14) { ... }
```

J'ai donc **reformatté** et **clarifié** le code a l'aide de [Claude](https://claude.ai) :  

```java
private String extractTextFromImage(Bitmap image) {
    int width = image.getWidth();
    int height = image.getHeight();
    StringBuilder binaryText = new StringBuilder();
    int[] colorOrder = {0, 1, 2};

    for (int y = 0; y < height; y++) {
        for (int x = 0; x < width; x++) {
            int pixel = image.getPixel(x, y);
            int colorIndex = binaryText.length() % 3;
            int colorValue = (colorOrder[colorIndex] == 0) ? (pixel >> 16) & 0xFF :
                             (colorOrder[colorIndex] == 1) ? (pixel >> 8) & 0xFF :
                                                              pixel & 0xFF;

            binaryText.append(colorValue & 1);

            if (binaryText.length() % 8 == 0 && binaryText.substring(binaryText.length() - 8).equals("00000000")) {
                return binaryToString(binaryText.substring(0, binaryText.length() - 8));
            }
        }
    }
    return "No text found...";
}
```

---

## 🐍 Extraction en Python  

Plutôt que d'exécuter ce code en **Java** (que je n'avais pas sur cet OS), j’ai réécrit l’extraction en **Python** :  

```python
from PIL import Image
import base64

def extract_text_from_image(image_path):
    img = Image.open(image_path)
    width, height = img.size
    binary_data = ""

    extract_order = [0, 1, 2]
    for y in range(height):
        for x in range(width):
            pixel = img.getpixel((x, y))
            channel = extract_order[len(binary_data) % 3]
            binary_data += str((pixel[channel] & 1))

            if len(binary_data) % 8 == 0 and binary_data[-8:] == "00000000":
                return binary_to_string(binary_data[:-8])

    return "No text found..."

def binary_to_string(binary_text):
    return "".join(chr(int(binary_text[i:i+8], 2)) for i in range(0, len(binary_text), 8))

extracted_data = extract_text_from_image("breizhctf_logo.png")
flag = base64.b64decode(extracted_data).decode()
print("Flag:", flag)
```

---

## 🏁 Flag  

Le script nous donne le flag :  
```
BZHCTF{4PK_4ND_5tEG4N0_T0_5t4rT}
```

---

### 🚀 Conclusion  

Ce challenge combinait **reverse** et **stéganographie**, ce qui faisait de lui un challenge très intéréssant, même si pas exceptionellement dur.

Le **Java** est sûrement le language avec lequel j'ai le plus de connaissances, alors cela m'a bien aidé, vu le nombre astronomique de fichiers dans l'apk.

### 📝  Autres Write-Ups du même challenge

https://jean-marie.mineau.eu/finisteg.html
