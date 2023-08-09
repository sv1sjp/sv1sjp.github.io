+++
title = "Self-Hosted SMTP Server για δοκιμές με emails"
date = "2023-04-28"
author = "Dimitris Vagiakakos"
+++

[Σημαντική Ανακοίνωση: Το παρών άρθρο έχει δημοσιτευτεί επίσημα στο Linux-User.gr και διατηρείται εδώ για καθαρά λόγους αρχειοθέτησης.](https://linux-user.gr/t/self-hosted-smtp-server-gia-dokimes-me-emails/4920/5)

Στην προσπάθεια μου να διοργανώσω ένα Security Awareness Campaign, χρειάστηκα να δοκιμάσω αποστολές μαζικών email σε ένα λογισμικό που φτιάξαμε.

Το θέμα είναι ότι αφού προσωπικά το χρειαζόμουν για να το δοκιμάσω τοπικά, πριν γίνει deploy εκεί οπού έπρεπε, ήθελα να δοκιμάσω ότι η υποδομή λειτουργεί και στέλνει κανονικά email.

Και εκεί βρήκα το [Mailhog](https://github.com/mailhog/MailHog) το οποίο μου έλυσε τα χέρια.

Το MaiHog τρέχει σε Go και είναι εξαιρετικά απλό στην χρήση ώστε να έχουμε έναν basic smtp server για δοκιμαστικούς σκοπούς στο σύστημα μας, ο οποίος μπορεί να λάβει emails από άλλες εφαρμογές.

## Deploy μέσω Docker:

Πολύ εύκολα και απλά δίνουμε σε ένα τερματικό:
```
docker run -d --name mailhog -p 1025:1025 -p 8025:8025 mailhog/mailhog
```
Και είμαστε έτοιμοι!! :slight_smile: 

## Web Client
Στην πόρτα 8025 μπορούμε να βρούμε ένα βασικό mail client ώστε να λαμβάνουμε τα τοπικά mail οπού στέλνονται:

![image|690x407](https://linux-user.gr/uploads/default/original/2X/c/cbb5d6389561223a2a1f3df23a31857bf6674845.png)

## Πώς στέλνουμε emails;

Το MailHog θα εμφανίζει οποιοδήποτε mail (ανεξαρτήτου domain name - dimitris@linux.gr, michaeljackson@greece.eu , fosscomm@earth.gov) σταλθεί με "στόχο" την ΙΡ και πόρτα 1025 (π.χ. localhost:1025)


Σε αυτό το σημείο αξίζει να επισημανθεί ξανά πως η λογική του MailHog είναι καθαρά για development για δοκιμή αποστολής email και όχι για πραγματική χρήση σε περιβάλλοντα παραγωγής.
