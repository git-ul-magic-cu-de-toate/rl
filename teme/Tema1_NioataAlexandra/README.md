Nioata Alexandra, 332CA

Switch - Implementare

Task 1:
Pentru primul task, se initializeaza MAC_Table, pentru a retine adresele MAC, iar apoi cu ajutorul pseudocodului
din enunt, se face tabela de comutare. La acest task, nu este nevoie de VLAN-uri.

Functia primeste o interfata, informatii despre pachet, lungime, adresele sursa respectiv destinatie MAC, lista de interfete si lista de adrese MAC_Table
Cand primeste un pachet, se actualizeaza in tabela MAC, adresa sursa si i se asociaza interfata pe care a fost primit pachetul. 
Atunci când primeste un pachet, functia actualizeaza adresa sursă (src_mac) in MAC_Table, si asociaza interfata pe care a fost primit pachetul.
Se verifica daca adresa e de tip unicast, caz in care se verifica daca adresa destinatie e deja in MAC_Table sau nu. Daca e atunci se trimite pachetul pe interfata cu adresa destinatie, daca nu se trimite pe restul interfetelor, destinatia fiind necunoscuta. Daca adresa este multicast, face acelasi lucru, trimite pe toate interfetele, mai putin interfata "principala"

def forward_with_learning(interface, data, length, src_mac, dest_mac, interfaces, MAC_Table):
    MAC_Table[src_mac] = interface
    
    if is_unicast(dest_mac):
        if dest_mac in MAC_Table:
            send_to_link(MAC_Table[dest_mac], data, length)
        else:
            for o in interfaces:
                if o != interface:
                    send_to_link(o, data, length)
    else:
        for o in interfaces:
            if o != interface:
                send_to_link(o, data, length)

De asemenea, s-a folosit o functie is_unicast, care verifica daca adresa de destinatie e de tip unicast si functia send_to_link din 
wrapper.py, care se ocupa de trimiterea datelor printr-o anumita interfata

Task 2:

Pentru task-ul 2, s-au adaugat functionalitati suplimentare la codul scris mai sus. Se initializeaza o tabela de VLAN-uri si se citeste din fisierele aflate in configs.

Pentru citire se foloseste o functie read_configs care primeste switch_id(numarul switch-ului 0, 1, 2) si o tabela VLAN. Trebuie citit din fisierul de forma 
"configs/switch{switch_id}.cfg", informatii legate de prioritate, interfete si VLAN-uri. Pe prima linie se citeste prioritatea, dar cum aceasta e folosita doar la task-ul 3,nu este nevoie de ea, citesc restul liniilor, care contin informatiile necesare. Se ia fiecare linie in parte, iar informatia de care e nevoie e VLAN-ul, 
daca linia nu se termina in T, inseamna ca se poate adauga in tabela de VLAN-uri, valoarea data (un string pe care eu il transform in intreg), daca linia se termina in T, inseamna ca avem interfete in Mod Trunk si le vom da o valoare negativa -1, apoi adaugam in tabela.

In plus, a fost necesara actualizarea codului pentru functia forward_with_learning, pentru a putea lua in considerare si faptul ca exista porturi cu VLAN-uri diferite. Switch-ul poate suporta mai multe VLAN-uri, de aceea cand primeste un cadru, el il transmite pe porturile cu acelasi VLAN/trunk, dar nu si pe porturile care apartin altor VLAN-uri
Fata de codul de la task-ul 1, aici se verifica de fiecare data daca VLAN-ul interfetei corespunde VLAN-ului dinaintea aparitiei tag-ului.

Inaintea apelarii efective a functiei in main, se verifica daca pachetul va veni de pe o interfata de tip acces/trunk. Pentru cele provenite din trunk, folosim id-ul vlan-ului din header, iar pentru celelalte utilizam tabela pentru a identifica VLAN-ul interfetei pe care a fost trimis pachetul
