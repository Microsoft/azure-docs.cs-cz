|Název parametru| Typ | Popis| Možné hodnoty|
|-|-|-|-|
| /ServerMode|Povinné|Určuje, jestli se má nainstalovat konfigurační i procesový server, nebo jenom procesový server.|CS<br>PS|
|/InstallLocation|Povinné|Složka, ve které jsou nainstalované komponenty| Libovolná složka v počítači|
|/MySQLCredsFilePath|Povinné|Cesta k souboru, ve kterém jsou uložené přihlašovací údaje serveru MySQL|Soubor by měl být v níže uvedeném formátu.|
|/VaultCredsFilePath|Povinné|Cesta k souboru s přihlašovacími údaji trezoru|Platná cesta k souboru|
|/EnvType|Povinné|Typ prostředí, které chcete chránit |VMware<br>NonVMware|
|/PSIP|Povinné|IP adresa NIC, která se použije pro přenos dat replikace| Libovolná platná IP adresa|
|/CSIP|Povinné|IP adresa NIC, na které konfigurační server naslouchá| Libovolná platná IP adresa|
|/PassphraseFilePath|Povinné|Úplná cesta k umístění souboru s heslem|Platná cesta k souboru|
|/BypassProxy|Nepovinné|Určuje, že se konfigurační server připojí k Azure bez proxy serveru.|Tuto hodnotu získejte z Venu.|
|/ProxySettingsFilePath|Nepovinné|Nastavení proxy serveru (výchozí proxy server vyžaduje ověření, nebo vlastní proxy server)|Soubor by měl být v níže uvedeném formátu.|
|DataTransferSecurePort|Nepovinné|Číslo portu na PSIP, které se má použít pro data replikace| Platné číslo portu (výchozí hodnota je 9433)|
|/SkipSpaceCheck|Nepovinné|Přeskočí kontrolu místa na disku mezipaměti.| |
|/AcceptThirdpartyEULA|Povinné|Příznak značí přijetí smlouvy EULA třetích stran| |
|/ShowThirdpartyEULA|Nepovinné|Zobrazí smlouvy EULA třetích stran. Pokud je zadán jako vstup, všechny ostatní parametry budou ignorovány| |
