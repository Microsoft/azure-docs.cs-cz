      ---
Autor: alkohli ms.service: databox  
MS.topic: zahrnují ms.date: 03/21/2019 ms.author: alkohli
---

| Port č.| Snížení nebo navýšení kapacity | Rozsah portů| Požaduje se|   Poznámky |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|Výstup|Síť WAN |Ne|Odchozí port se používá pro přístup k Internetu pro načtení aktualizací. <br>Odchozí webový proxy server je konfigurovatelná uživatelem. |
| TCP 443 (HTTPS)|Výstup|Síť WAN|Ano|Odchozí port se používá pro přístup k datům v cloudu.<br>Odchozí webový proxy server je konfigurovatelná uživatelem.|
| UDP 123 (NTP)|Výstup|Síť WAN|V některých případech<br>Viz poznámky|Tento port je požadován, pouze v případě, že používáte NTP server s procesorem na Internetu.  |   
| UDP 53 (DNS)|Výstup|Síť WAN|V některých případech<br>Viz poznámky|Tento port je povinný, jenom v případě, že používáte DNS server s procesorem na Internetu.<br>Doporučujeme používat místní server DNS. |
| TCP 5985 (WinRM)|Out nebo přihlášení.|LAN|V některých případech<br>Viz poznámky|Tento port se nejde připojit k zařízení pomocí vzdáleného prostředí PowerShell přes protokol HTTP.  |
| UDP 67 (DHCP)|Výstup|LAN|V některých případech<br>Viz poznámky|Tento port je povinný, jenom v případě, že používáte místní server DHCP.  |
| TCP 80 (HTTP)|Out nebo přihlášení.|LAN|Ano|Toto je příchozí port pro místní uživatelské rozhraní na zařízení pro místní správu. <br>Přístup k místním uživatelského rozhraní pomocí protokolu HTTP bude automaticky přesměrovat na protokol HTTPS.  |
| TCP 443 (HTTPS)|Out nebo přihlášení.|LAN|Ano|Toto je příchozí port pro místní uživatelské rozhraní na zařízení pro místní správu. |
| TCP 445 (SMB)|V|LAN|V některých případech<br>Viz poznámky|Tento port je povinný, jenom v případě, že se připojujete přes SMB. |
| TCP 2049 (NFS)|V|LAN|V některých případech<br>Viz poznámky|Tento port je povinný, jenom v případě, že se připojujete pomocí systému souborů NFS. |

