Zadanie 1
---------

1. Zaprojektuj oraz przygotuj prototyp rozwiązania z wykorzystaniem oprogramowania ``VirtualBox`` lub podobnego. 
Zaproponuj rozwiązanie spełniające poniższe wymagania:
   * Usługodawca zapewnia domunikację z siecią internet poprzez interfejs ``eth0`` ``PC0``
   * Zapewnij komunikację z siecią internet na poziomie ``LAN1`` oraz ``LAN2``
   * Dokonaj takiego podziału sieci o adresie ``172.22.128.0/17`` aby w ``LAN1`` można było zaadresować ``500`` adresów natomiast w LAN2 ``5000`` adresów    
   * Przygotuj dokumentację powyższej architektury w formie graficznej w programie ``DIA``
 
---------

**Rozwiązanie**
![network image](network.png)
Konfiguracja sieci:
1. LAN1 - ``172.22.160.0/23``
2. LAN2 - ``172.22.128.0/19``

Konfiguracja maszyn wirtualnych:
1. PC0
  * LAN1
    * enp0s3 - ``172.22.160.1/23``
  * LAN2
    * enp0s8 - ``172.22.128.1/19``
  * NAT
    * enp0s9 - ustalone przez DHCP (NAT)
2. PC1
  * LAN1
    * enp0s3 - ``172.22.160.100/23``
3. PC2
  * LAN2
    * enp0s3 - ``172.22.128.100/19``
  
Komendy:

**PC0**

  | Komenda                                 | Komentarz                                                 |
  |:---------------------------------------:|-----------------------------------------------------------|
  | ``ip a add 172.22.160.1/23 dev enp0s3`` | Przypisujemy adresy routerowi na sieciach LAN1 i LAN2     |
  | ``ip a add 172.22.128.1/19 dev enp0s8`` |                                                           |
  | ``ip link set <dev> up``                | Włączamy karty sieciowe                                   | 
  | ``dhclient -r``                         |                                                           |
  | ``dhclient``                            | Przypisujemy adres karcie sieciowej enp0s9 za pomocą DHCP |
  | ``ip route``                            | Wyświetla obecny routing                                  |
  | ``nano /etc/network/interfaces``        | Zmieniamy ostawienie sieci NAT z ``auto``/``static`` na ``DHCP`` |
  | ``nano /etc/sysctl.conf``               | Usuwamy komentarz z linii zaw. ``ip_forward``             |
  | ``echo 1 > /proc/sys/net/ipv4/ip_forward`` | |
  | ``iptables -t nat -A POSTROUTING -s 172.22.160.0/23 -o enp0s9 -j MASQUERADE`` | routuje dane clientów do interfejsu NAT |
  
  
  
**PC1** / **PC2**

  | Komenda                                 | Komentarz                                                 |
  |:---------------------------------------:|-----------------------------------------------------------|
  | ``ip a add {ip}``          | Przypisujemy adres |
  | ``ip link set enp0s3 up``               | |
  | ``ip route add default via {ip routera}`` | Routujemy ruch |
  | ``ping google.com``                     | Sprawdzamy czy dane przechodzą do internetu |
