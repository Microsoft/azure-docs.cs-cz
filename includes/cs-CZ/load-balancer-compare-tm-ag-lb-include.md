## <a name="load-balancer-differences"></a>Rozdíly mezi nástroji pro vyrovnávání zatížení

Existují různé možnosti distribuce síťového provozu pomocí systému Microsoft Azure. Tyto možnosti fungují různě, obsahují jiné sady funkcí a podporují různé scénáře. Lze je používat jednotlivě nebo je kombinovat.

* **Azure Load Balancer** funguje na přenosové vrstvě (Vrstva 4 v balíčku referenčních informací k síti modelu OSI). Poskytuje distribuci provozu na úrovni sítě mezi instancemi aplikace, které běží ve stejném datovém centru Azure.
* **Application Gateway** funguje na aplikační vrstvě (Vrstva 7 v balíčku referenčních informací k síti modelu OSI). Funguje jako služba reverzních proxy serverů, ukončuje připojení klienta a předává požadavky do back-endových koncových bodů.
* **Traffic Manager** funguje na úrovni DNS.  Pomocí odpovědí DNS směruje provoz koncového uživatele do globálně distribuovaných koncových bodů. Klienti se pak připojují přímo k těmto koncovým bodům.

Následující tabulka shrnuje funkce, které jednotlivé služby nabízí:

| Služba | Nástroj pro vyrovnávání zatížení Azure | Application Gateway | Traffic Manager |
| --- | --- | --- | --- |
| Technologie |Úroveň přenosu (Vrstva 4) |Úroveň aplikace (Vrstva 7) |Úroveň DNS |
| Podporované protokoly aplikací |Všechny |HTTP, HTTPS a protokoly WebSocket |Všechny (k monitorování koncového bodu je vyžadován koncový bod HTTP) |
| Koncové body |Virtuální počítače Azure a instance rolí služby Cloud Services. |Všechny interní IP adresy Azure, veřejné internetové IP adresy, virtuální počítače Azure nebo cloudová služba Azure |Virtuální počítače Azure, služba Cloud Services, webové aplikace Azure a externí koncové body. |
| Podpora virtuálních sítí |Lze použít pro internetové i interní (VNet) aplikace. |Lze použít pro internetové i interní (VNet) aplikace. |Podporuje pouze internetové aplikace. |
| Monitorování koncového bodu |Podporováno prostřednictvím sond. |Podporováno prostřednictvím sond. |Podporováno prostřednictvím HTTP/HTTPS GET. |

Azure Load Balancer i služba Application Gateway směrují síťový provoz do koncových bodů, ale mají jiné scénáře použití podle toho, jaký provoz mají zpracovávat. Následující tabulka vám pomůže pochopit rozdíly mezi těmito dvěma nástroji pro vyrovnávání zatížení:

| Typ | Nástroj pro vyrovnávání zatížení Azure | Application Gateway |
| --- | --- | --- |
| Protokoly |UDP/TCP |HTTP, HTTPS a protokoly WebSocket |
| Vyhrazení IP adres |Podporuje se |Nepodporuje se |
| Režim vyrovnávání zatížení |Pětinásobné (zdrojová IP adresa, zdrojový port, cílová IP adresa, cílový port, typ protokolu). |Kruhové dotazování.<br>Směrování na základě adresy URL. |
| Režim vyrovnávání zatížení (zdrojová IP adresa/spřažené relace). |Dvojnásobné (zdrojová IP adresa a cílová IP adresa), trojnásobné (zdrojová IP adresa, cílová IP adresa a port). Lze vertikálně zvýšit nebo snížit kapacitu podle počtu virtuálních počítačů. |Spřažení na základě souborů cookie.<br>Směrování na základě adresy URL. |
| Sondy stavu |Výchozí hodnota: interval sondování – 15 sekund. Vyřazení ze smyčky: po dvou selháních za sebou. Podporuje uživatelem definované sondy. |Interval nečinnosti sondy – 30 sekund. Vyřazena ze smyčky po pěti selháních živého provozu za sebou nebo po jediném selhání sondy v režimu nečinnosti. Podporuje uživatelem definované sondy. |
| Přesměrování zpracování SSL |Nepodporuje se |Podporuje se |
| Směrování na základě adresy URL | Nepodporuje se | Podporuje se|
| Zásady protokolu SSL | Nepodporuje se | Podporuje se|
