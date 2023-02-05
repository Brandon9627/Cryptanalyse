## 1.	Implémentation du chiffrement de Vigenère

**Exercice 1 : Implémentez un programme (en C par exemple) qui demande à l’utilisateur de saisir un texte, et qui l’affiche.**


```python
message = input('>')
print(message)
```

    >hjbm
    hjbm
    

**Exercice 2 : Modifiez votre programme pour qu’il convertisse toutes les lettres en minuscules, et qu’il enlève tous les autres caractères.**

Exemple: 
user$ prog2 
Entrez un texte : Le soleil brille! 
Texte non chiffré : lesoleilbrille



```python
import re


message = input('>').lower()
newMessage = re.sub(r'[^a-z]', '', message.lower())
print(newMessage)


```

    >bYYGHVHJjkbnnm098908
    byyghvhjjkbnnm
    

**Exercice 3 : Implémentez une fonction qui prend en entrée deux lettres minuscules (l’une étant une lettre du texte non chiffré, et l’autre une lettre de la clé), et qui retourne la lettre minuscule correspondante chiffrée.**

Exemple : 
user$ prog3 m b 
b = 2 
m + 2 = o



```python
def encoding(letter, key):
    return chr(ord(key) + ord(letter) - 96)

message = input('>').lower()
print(encoding(message[0], message[1]))

```

    >mb
    o
    

**Exercice 4 : Modifiez votre program pour qu’il demande à l’utilisateur deux textes (l’un étant le texte non chiffré, et l’autre la clé), qui les convertit tous les deux (selon l’exercice 2), et qui chiffre le texte avec le chiffrement de Vigenère et la clé donnée.**


```python
import re

def encoded(msg, keyText):
    counter = 0
    conversion = ''
    for letter in msg:
        if counter == len(keyText):
            counter = 0

        conversion = conversion + chr(((ord(letter) + ord(keyText[counter])) - 2 * 97) % 26 + 97)
        counter += 1
    return conversion


keyText = input('Insert key\n>')
message = input('Insert text\n>')

newMessage = re.sub(r'[^a-z]', '', message.lower())
print('Coded message:')
print(encoded(newMessage, keyText))

```

    Insert key
    >ab
    Insert text
    >aazz
    Coded message:
    abza
    

**Exercice 5 : Implémentez un programme qui demande à l’utilisateur deux textes (l’un étant le texte chiffré, et l’autre la clé), et qui déchiffre le texte.**


```python
import re


def decoder(msg, keyText):
    counter = 0
    conversion = ''
    for letter in msg:
        if counter == len(keyText):
            counter = 0

        conversion = conversion + chr((ord(letter) - ord(keyText[counter]) + 26) % 26 + 97)
        counter += 1
    return conversion


keyText = input('Insert key\n>')
coded_message = input('Insert coded text\n>')

newMessage = re.sub(r'[^a-z]', '', coded_message.lower())
print('Decoded message:')
print(decoder(newMessage, keyText))

```

    Insert key
    >ab
    Insert coded text
    >abza
    Decoded message:
    aazz
    

## 2. Cryptanalyse par estimation de la longueur de la clé et analyse fréquentielle

Lorsque la longueur de la clé est connue, un chiffrement de Vigenère peut être cassé par une analyse fréquentielle.

### 1.1	Méthode de Babbage et Kasiki

**Exercice 6 : Implémentez un programme qui prend en paramètre un texte chiffré, et qui affiche toutes les occurrences de séquences de 3 lettres ou plus qui se répétent. **

Exemple : 
user$ prog6 
cipher: abcdefghijklmnopqrstuvwxyzabcdmnoabc 
abc trouvé 3 fois 
bcd trouvé 2 fois 
abcd trouvé 2 fois 
mno trouvé 2 fois



```python
def repeating_sequence(ciphertext):
    sequences = {}
    for sequence_length in range(3, len(ciphertext)):
        for scan in range(len(ciphertext) - sequence_length + 1):
            sequence = ciphertext[scan:scan+sequence_length]
            if sequence not in sequences:
                count = 0
                for look in range(len(ciphertext) - len(sequence) + 1):
                    is_match = ciphertext[look: look+len(sequence)]
                    if sequence == is_match:
                        count += 1
                if count >= 2:
                    sequences[sequence] = count

    for sequence, count in sequences.items():
        print(f"{sequence} found {count} times")


ciphertext = input("Enter your ciphertext here:\n")
repeating_sequence(ciphertext)
```

    Enter your ciphertext here:
    abcdefghijklmnopqrstuvwxyzabcdmnoabc
    abc found 3 times
    bcd found 2 times
    mno found 2 times
    abcd found 2 times
    

**Exercice 7 : Modifiez votre programme pour qu’il calcule la longueur de la clé à partir des distances entre les répétitions.**


```python
def sequence_distance(ciphertext):
    sequences = {}
    for sequence_length in range(3, len(ciphertext)):
        for scan in range(len(ciphertext) - sequence_length + 1):
            sequence = ciphertext[scan:scan+sequence_length]
            if sequence not in sequences:
                count = 0
                distance = []
                distance_1 = scan
                for look in range(len(ciphertext) - len(sequence) + 1):
                    is_match = ciphertext[look: look+len(sequence)]
                    if sequence == is_match:
                        count += 1
                        if count >= 2:
                            distance.append(look - distance_1)
                            distance_1 = look
                            sequences[sequence] = distance
    multiples = []
    for sequence, distance in sequences.items():
        for dist in distance:
            for number in range(2, dist//2 + 1):
                if dist % number == 0:
                    multiples.append(number)
            multiples.append(dist)
        print(f"{sequence} is separated by {distance} unit")
    print()
    print(f"possible lengths of key in order of frequency: {order_by_frequency(multiples)}")


def order_by_frequency(items):
    frequency = {}
    for item in items:
        if item in frequency:
            frequency[item] += 1
        else:
            frequency[item] = 1
    sorted_items = sorted(frequency.items(), key=lambda x: x[1], reverse=True)
    return [item[0] for item in sorted_items]


ciphertext = input("Enter your ciphertext here:\n")
sequence_distance(ciphertext)
```

    Enter your ciphertext here:
    abcdefghijklmnopqrstuvwxyzabcdmnoabc
    abc is separated by [26, 7] unit
    bcd is separated by [26] unit
    mno is separated by [18] unit
    abcd is separated by [26] unit
    
    possible lengths of key in order of frequency: [2, 13, 26, 7, 3, 6, 9, 18]
    

**Exercice 8 : Améliorez votre programme pour qu’il supprime les répétitions peu probables (par exemple, 10% des répétitions). Expliquez ce que vous considérez comme peu probable.**


```python
def sequence_distance(ciphertext):
    sequences = {}
    for sequence_length in range(3, len(ciphertext)):
        for scan in range(len(ciphertext) - sequence_length + 1):
            sequence = ciphertext[scan:scan+sequence_length]
            if sequence not in sequences:
                count = 0
                distance = []
                distance_1 = scan
                for look in range(len(ciphertext) - len(sequence) + 1):
                    is_match = ciphertext[look: look+len(sequence)]
                    if sequence == is_match:
                        count += 1
                        if count >= 2:
                            distance.append(look - distance_1)
                            distance_1 = look
                            sequences[sequence] = distance
    multiples = []
    for sequence, distance in sequences.items():
        for dist in distance:
            for number in range(2, dist//2 + 1):
                if dist % number == 0:
                    multiples.append(number)
            multiples.append(dist)
        print(f"{sequence} is separated by {distance} unit")
    print()
    frequency = order_by_frequency(multiples)
    print(f"possible lengths of key in order of frequency: {frequency[:3]}")


def order_by_frequency(items):
    frequency = {}
    for item in items:
        if item in frequency:
            frequency[item] += 1
        else:
            frequency[item] = 1
    sorted_items = sorted(frequency.items(), key=lambda x: x[1], reverse=True)
    return [item[0] for item in sorted_items]


ciphertext = input("Enter your ciphertext here:\n")
sequence_distance(ciphertext)
```

    Enter your ciphertext here:
    abcdefghijklmnopqrstuvwxyzabcdmnoabc
    abc is separated by [26, 7] unit
    bcd is separated by [26] unit
    mno is separated by [18] unit
    abcd is separated by [26] unit
    
    possible lengths of key in order of frequency: [2, 13, 26]
    

Il est peu probable que la clé soit de longueur qui ne soit pas un diviseur de la majorité des distances obtenu entre les répétitions.

## 2.2 Test de Friedman

**Exercice 9 : Soit Tr un grand texte, généré aléatoirement, en utilisant seulement des lettres minuscules. Quelle est la probabilité Kr que deux lettres choisies aléatoirement soient égales, dans Tr ?**

Polyalphabetic ciphers

Kr = ((1/26) * (1/26)) + ((1/26) * (1/26)) + .... + ((1/26) * (1/26)) = 1/26 = 0.038

**Exercice 10 : Soit T un texte utilisant uniquement des lettres minuscules. Écrivez un programme qui calcule la probabilité K que deux lettres choisies aléatoirement soient les mêmes dans T.**

**Remarque : Vous pouvez considérer les 26 événements indépendants consistant à choisir la lettre li d’abord. Ainsi, K devient la somme des probabilités Ki, où Ki est la probabilité que deux lettres choisies aléatoirement soient égales à li.**


```python
def calculate_probability(text):
    letter_counts = [0] * 26
    total_letters = 0
    for letter in text:
        letter_index = ord(letter) - ord('a')
        letter_counts[letter_index] += 1
        total_letters += 1
    max_letter_count = max(letter_counts)
    probability = max_letter_count * (max_letter_count - 1) / (total_letters * (total_letters - 1))
    return probability

text = input("Enter text:\n").lower()
probability = calculate_probability(text)
print(probability)
```

    Enter text:
    abcdefghijklmnopqrstuvwxyzabcdmnoabc
    0.004761904761904762
    

![image-2.png](attachment:image-2.png)

**Exercice 11 : Le test de Friedman estime la longueur de la clé L comme (Ke-Kr)/(K-Kr). Calculez L.**

Remarque : Quand L=1, on a Ke=K, puisque le chiffrement de Vigenère correspond alors au cas d’un chiffrement par substitution simple. For L>1, K est égal à la probabilité que li soit égale à lj, avec li et lj qui correspondent à la même position dans la clé, plus la probabilité que li soit égale à lj, avec li et lj qui correspondent à des positions différentes dans la clé. Ainsi, K est égal à Ke/L (car il y a une probabilité 1/L que li et lj correspondent à la même position de la clé) plus (L-1).Kr/L (car il y a une probabilité (L-1)/L que li et lj correspondent à des positions différentes de la clé). Dans ce cas, on a (Ke-Kr)/(K-Kr)=L.



```python
def calculate_probability(text):
    letter_counts = [0] * 26
    total_letters = 0
    for letter in text:
        letter_index = ord(letter) - ord('a')
        letter_counts[letter_index] += 1
        total_letters += 1
    max_letter_count = max(letter_counts)
    probability = max_letter_count * (max_letter_count - 1) / (total_letters * (total_letters - 1))
    return probability


text = input("Enter text:\n").lower()
probability = calculate_probability(text)
print(f"k = {probability}")
ke = 0.067
kr = 1/26
k = probability
l = (ke - kr) / (k - kr)
print(f"L = {l}")
```

    Enter text:
    abcdefghijklmnopqrstuvwxyzabcdmnoabc
    k = 0.004761904761904762
    L = -0.8468478260869564
    

## 2.3 Analyse fréquentielle

**Exercice 12 : Implémentez un programme qui prend en entrée un texte chiffré et une longueur de clé, et qui casse le chiffrement de Vigenère en utilisant une analyse fréquentielle. Le programme peut demander à l’utilisateur quel caractère chiffré correspond à quel caractère en clair, mais le programme doit fournir à l’utilisateur assez d’informations.**



```python
import re


def key_finder(msg, key_len):
    letter_position = [[] for i in range(key_len)]
    for position in range(key_len):
        for letter in msg[position::key_len]:
            letter_position[position].append(letter)
    word = ''
    for position in letter_position:
        move = max(position, key=position.count)
        if ord(move) > ord('e'):
            word += chr((ord(move) - ord('e')) % 26 + ord('a'))
        else:
            word += chr((ord('e') - ord(move)) % 26 + ord('a'))
    return word

    #     conversion = conversion + (chr(ord(letter) - ord(keyText[counter]) + 96))
    #     counter += 1
    # return conversion


def decoder(msg, keyText):
    counter = 0
    conversion = ''
    for letter in msg:
        if counter == len(keyText):
            counter = 0

        conversion = conversion + chr((ord(letter) - ord(keyText[counter]) + 26) % 26 + 97)
        counter += 1
    return conversion


key_length = input('Insert key length\n>')
coded_message = input('Insert coded text\n>')

newMessage = re.sub(r'[^a-z]', '', coded_message.lower())
print('key:')
key = key_finder(newMessage, int(key_length))
print(key)
print('Decoded:')
print(decoder(newMessage, key))
```

    Insert key length
    >3
    Insert coded text
    >abcdefghijklmnopqrstuvwxyzabcdmnoabc
    key:
    edc
    Decoded:
    wyazbdcegfhjikmlnpoqsrtvuwyxzbikmwya
    
