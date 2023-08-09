+++
title = "Τραινάκι στο Τερματικό του Linux"
date = "2022-12-12"
author = "Dimitris Vagiakakos"
+++

[Σημαντική Ανακοίνωση: Το παρών άρθρο έχει δημοσιτευτεί επίσημα στο Linux-User.gr και διατηρείται εδώ για καθαρά λόγους αρχειοθέτησης.](https://linux-user.gr/t/trainaki-sto-termatiko-toy-linux/4658)
Αφού είδαμε το [iSemedaki](https://linux-user.gr/t/isemedaki-h-epektash-gnome-poy-den-prepei-na-leipei-apo-kanena-ellhniko-spiti/3590) για το GNOME, ήρθε η ώρα να μάθουμε πώς να εμφανίσουμε ένα μαγικό τραινάκι στο τερματικό του Linux.

![image|690x465](upload://2kkTXf8gO5WtEZZnbZldEIxE8Kd.jpeg)

Η εντολή ls αποτελεί μία εντολή οπού εμφανίζει τα αρχεία & φακέλους όπου εμπεριέχονται σε έναν φάκελο. [(Μάθημα στο Ultimate Linux Terminal Tutorial σχετικά με αυτά)](https://www.youtube.com/playlist?list=PLZa7COjIxKWzfu1kLBWBbj-3wdKSzDVl4).
 

Το sl από την άλλη, αποτελεί την εντολή "τιμωρία" σε περίπτωση που ο χρήστης πληκτρολογήσει ανάποδα το ls.

Για τιμωρία, το sl θα εμφανίσει ένα τραινάκι να τρέχει εντός του τερματικού μας.

Εκτός βέβαια από το παραδοσιακό τραινάκι, χρησιμοποιώντας παραμέτρους θα δούμε και διαφορετικά κόλπα με το τραινάκι:

```
sl -a : Accident mode με ανθρώπους να ζητούν βοήθεια
sl -l : Εμφανίζει ένα μικρότερο τραίνο
sl -F : Το τραίνο αρχίζει και πετάει
```
Για να κάνουμε εγκατάσταση το sl, δίνουμε από γραμμή εντολών:

Για Debian Based (Ubuntu,Linux Mint):
```
sudo apt install sl
```
Για Fedora:
```
sudo dnf install sl
```

Για Android Termux:
```
pkg install sl
```

Ενδεικτικό short Video:
https://youtube.com/shorts/jbbRa-lYQoc?feature=share
