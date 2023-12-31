# Εκτέλεση από LTSP client

!!! warning ""
    Αυτή η σελίδα προϋποθέτει ότι έχετε εγκαταστήσει τη [Διαχείριση
    ΣΕΠΕΗΥ](../ltsp/index.md).

Συνήθως ο Επόπτης εκτελείται από τον καθηγητή που συνδέεται στον LTSP server.
Όμως, σε ορισμένες περιπτώσεις είναι επιθυμητή η εκτέλεσή του από LTSP client,
για παράδειγμα όταν ένας server εξυπηρετεί πολλά εργαστήρια, ή όταν ο server
είναι τοποθετημένος μακρυά από την έδρα του καθηγητή.

## Out of the box (Προτεινόμενη λύση) {#out-of-the-box}

Ουσιαστικά το μόνο που χρειάζεται, είναι να κάνετε από οποιοδήποτε fat client,
οποιουδήποτε εργαστηρίου, login ως χρήστης με ρόλο καθηγητή. Δηλαδή ως χρήστης
που ανήκει στην ομάδα
`teachers` ([Ρόλος χρήστη: καθηγητή](../ltsp/users.md#create-new-user)).
Αυτοί οι χρήστες ανήκουν αυτόματα και στην ομάδα `epoptes`, που είναι η μόνη
προϋπόθεση, για να μπορούν να εκτελούν την εφαρμογή `Επόπτης` από οποιοδήποτε
fat client.

!!! tip "Χρήσιμο"
    Επειδή όλοι οι καθηγητές θα βλέπουν όλους τους χρήστες, θα πρέπει να
    δημιουργηθούν [Ομάδες](../epoptes/groups.md).

## Ειδικές περιπτώσεις {#special-cases}

Σε ειδικές περιπτώσεις που δεν είναι δυνατή η [Out of the box](#out-of-the-box)
λύση, υπάρχουν εναλλακτικά οι ακόλουθες:

- [Απομακρυσμένα](#remotely)
- [Τοπικά](#locally)

Ο παρακάτω πίνακας τις συγκρίνει:

| Ερώτηση                     | Απομακρυσμένα                          | Τοπικά                                      |
|-----------------------------|----------------------------------------|---------------------------------------------|
| Που "εκτελείται" ο Επόπτης; | Στον LTSP server                       | Σε client εργαστηρίου                       |
| Ποιους "βλέπει" ο Επόπτης;  | Όλα τα clients σε όλα τα εργαστήρια    | Μόνο τα clients του εργαστήριου             |
| Το broadcast είναι γρήγορο; | Όχι γιατί γίνεται μεσω του LTSP server | Ναι, χρησιμοποιούνται απ' ευθείας συνδέσεις |
| Είναι ασφαλές;              | Ναι                                    | Όχι τόσο πολύ                               |

Επομένως, σε όλες τις περιπτώσεις η λύση «Τοπικά» είναι καλύτερη, εκτός από την
ασφάλεια, καθώς τότε το ιδιωτικό κλειδί του Επόπτη εκτίθεται στο τοπικό δίκτυο,
επιτρέποντας επιθέσεις
[man-in-the-middle](https://el.wikipedia.org/wiki/Επίθεση_man-in-the-middle)
αλλά **όχι** [network sniffing](https://el.wikipedia.org/wiki/Packet_sniffer).

### Απομακρυσμένα {#remotely}

«Απομακρυσμένα» σημαίνει ότι όταν οι καθηγητές συνδέονται σε LTSP client και
επιλέγουν το μενού ***Εφαρμογές*** → ***Διαδίκτυο*** → ***Επόπτης***, τότε ο
Επόπτης εκτελείται στον server χρησιμοποιώνας την τεχνολογία [ltsp
remoteapps](https://ltsp.org/man/ltsp-remoteapps/). Για την υλοποίηση αυτής της
λύσης, ανοίξτε τη [Διαχείριση ΣΕΠΕΗΥ](../glossary/index.md#sch-scripts) και
επιλέξτε το μενού ***Εξυπηρετητής*** ▸ ***Αρχεία ρυθμίσεων*** ▸
***ltsp.conf***. Κάτω από την ενότητα `[clients]`, τοποθετήστε την ακόλουθη
παράμετρο:

```text title="/etc/ltsp/ltsp.conf"
[clients]
REMOTEAPPS="epoptes"
```

Στη συνέχεια επιλέξτε το μενού ***Εξυπηρετητής*** ▸ ***Εντολές LTSP*** ▸
***ltsp initrd*** και επανεκκινήστε τους clients.

### Τοπικά {#locally}

Σε αυτήν την λύση, ένας LTSP client ανά εργαστήριο θεωρείται ότι είναι ο
υπολογιστής του καθηγητή. Σε αυτούς τους clients δίνουμε συγκεκριμένα ονόματα
(hostnames), για παράδειγμα `a00` για το `lab-a`, ώστε να τους επιτρέπεται να
εκτελούν και την υπηρεσία αλλά και το γραφικό περιβάλλον του Επόπτη. Τα
υπόλοιπα LTSP clients του εργαστηρίου λαμβάνουν οδηγίες ώστε να συνδεθούν στον
υπολογιστή του καθηγητή και όχι στον LTSP server. Για να γίνουν όλα αυτά,
χρειάζονται οι ακόλουθες ρυθμίσεις στο `ltsp.conf`. **Συγχωνεύστε** την ενότητα
`[server]` με αυτήν που ήδη έχετε χωρίς να δημιουργήσετε νέα:

```text title="ltsp.conf"
[server]
# Διατήρηση του ιδιωτικού κλειδιού (private key) του Επόπτη στον εικονικού δίσκο LTSP.
OMIT_IMAGE_EXCLUDES="etc/epoptes/server.key"

[lab-a]
# Οδηγία ώστε οι `lab-a` clients να συνδεθούν στο `a00.local`.
# Οι καταλήξεις `.local` προσθέτονται αυτόματα στα hostnames από την υπηρεσία `avahi`.
POST_INIT_EPOPTES="sed 's/.*SERVER=.*/SERVER=a00.local/' -i /etc/default/epoptes-client"

[mac:address:of:lab-a-teacher-pc]
HOSTNAME=a00
# Στον υπολογιστή του καθηγητή να γίνεται εκκίνηση της υπηρεσίας `epoptes`
KEEP_SYSTEM_SERVICES="epoptes"
IGNORE_EPOPTES=1

[mac:address:of:lab-a-client01]
HOSTNAME=a01
# Χρήση οδηγίας `INCLUDE` για αντιστοίχιση clients σε όνομα εργαστηρίου.
INCLUDE=lab-a
```

Τέλος, από τη [Διαχείριση ΣΕΠΕΗΥ](../glossary/index.md#sch-scripts) επιλέξτε
το μενού ***Εξυπηρετητής*** ▸ ***Εντολές LTSP*** ▸ ***ltsp image
(δημοσίευση)***.
