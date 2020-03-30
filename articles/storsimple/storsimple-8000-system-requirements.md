---
title: Požadavky na systém řady StorSimple 8000 | Dokumenty společnosti Microsoft
description: Popisuje požadavky na software, sítě a vysokou dostupnost a doporučené postupy pro řešení Microsoft Azure StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 3032585c6f0a5cc6143eee06b12b6def50cd7cd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297708"
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>Software řady StorSimple 8000, vysoká dostupnost a požadavky na sítě

## <a name="overview"></a>Přehled

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Vítá vás Microsoft Azure StorSimple. Tento článek popisuje důležité systémové požadavky a osvědčené postupy pro vaše zařízení StorSimple a pro klienty úložiště, kteří přistupují k zařízení. Doporučujeme, abyste si tyto informace před nasazením systému StorSimple pečlivě prostudovali a pak se k němu vrátili podle potřeby během nasazení a následné operace.

Systémové požadavky zahrnují:

* **Požadavky na software pro klienty úložišť** – popisuje podporované operační systémy a všechny další požadavky pro tyto operační systémy.
* **Síťové požadavky pro zařízení StorSimple** – poskytuje informace o portech, které je třeba otevřít v bráně firewall, aby bylo možné provádět přenosy iSCSI, cloudu nebo správy.
* **Požadavky na vysokou dostupnost pro StorSimple** – popisuje vysoké požadavky na dostupnost a osvědčené postupy pro vaše zařízení StorSimple a hostitelský počítač.

## <a name="software-requirements-for-storage-clients"></a>Požadavky na software pro klienty úložišť

Následující požadavky na software jsou určeny pro klienty úložiště, kteří přistupují k vašemu zařízení StorSimple.

| Podporované operační systémy | Požadovaná verze | Další požadavky/poznámky |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2, 2016 |Svazky ISCSI jsou podporovány pro použití pouze u následujících typů disků systému Windows:<ul><li>Jednoduchý svazek na základním disku</li><li>Jednoduchý a zrcadlený svazek na dynamickém disku</li></ul>Podporovány jsou pouze iniciátory iSCSI softwaru, které se v operačním systému prezentují nativně. Hardwarové iniciátory iSCSI nejsou podporovány.<br></br>Pokud používáte svazek StorSimple iSCSI, jsou podporovány funkce thin provisioning systému Windows Server 2012 a 2016 a funkce ODX.<br><br>StorSimple můžete vytvořit tence zřízená a plně zřízené svazky. Nemůže vytvořit částečně zřízené svazky.<br><br>Přeformátování tence zřízeného svazku může trvat dlouhou dobu. Doporučujeme svazek smazat a místo přeformátování vytvořit novou. Pokud však stále dáváte přednost přeformátování svazku:<ul><li>Před přeformátováním spusťte následující příkaz, abyste se vyhnuli zpoždění rekultivace místa: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Po dokončení formátování znovu povolte rekultivaci místa pomocí následujícího příkazu:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>V počítači se systémem Windows Server použijte opravu hotfix systému Windows Server 2012 popsanou v [kb 2878635.](https://support.microsoft.com/kb/2870270)</li></ul></li></ul></ul> Pokud konfigurujete Správce snímků StorSimple nebo StorSimple Adapter pro službu SharePoint, přejděte k [požadavkům na software pro volitelné součásti](#software-requirements-for-optional-components). |
| VMware ESX |5,5 a 6,0 |Podporováno s VMware vSphere jako klientem iSCSI. Funkce VAAI-block je podporována vMware vSphere na zařízeních StorSimple. |
| Linux RHEL/CentOS |5, 6 a 7 |Podpora linuxových klientů iSCSI s iniciátorem open-iSCSI verze 5, 6 a 7. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> IBM AIX není aktuálně podporována službou StorSimple.


## <a name="software-requirements-for-optional-components"></a>Požadavky na software pro volitelné součásti

Následující požadavky na software jsou určeny pro volitelné komponenty StorSimple (StorSimple Snapshot Manager a StorSimple Adapter pro SharePoint).

| Komponenta | Hostitelská platforma | Další požadavky/poznámky |
| --- | --- | --- |
| StorSimple Snapshot Manager |Windows Server 2008 R2 SP1, 2012, 2012 R2 |Použití Správce snímků StorSimple v systému Windows Server je vyžadováno pro zálohování a obnovení zrcadlených dynamických disků a pro všechny zálohy konzistentní s aplikací.<br> Správce snímků StorSimple je podporován pouze v systémech Windows Server 2008 R2 SP1 (64bit), Windows Server 2012 R2 a Windows Server 2012.<ul><li>Pokud používáte Windows Server 2012, je nutné nainstalovat rozhraní .NET 3.5–4.5 před instalací Správce snímků StorSimple.</li><li>Používáte-li systém Windows Server 2008 R2 SP1, je před instalací Správce snímků StorSimple nutné nainstalovat rozhraní Windows Management Framework 3.0.</li></ul> |
| StorSimple Adapter pro SharePoint |Windows Server 2008 R2 SP1, 2012, 2012 R2 |<ul><li>StorSimple Adaptér pro SharePoint je podporovaný jenom na SharePointu 2010 a SharePointu 2013.</li><li>RBS vyžaduje SQL Server Enterprise Edition, verze 2008 R2 nebo 2012.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>Požadavky na síť pro zařízení StorSimple

Zařízení StorSimple je uzamčené zařízení. Porty je však třeba otevřít ve vaší bráně firewall, aby bylo možné pro provoz iSCSI, cloud a správa. V následující tabulce jsou uvedeny porty, které je třeba otevřít v bráně firewall. V této tabulce *in* nebo *příchozí* odkazuje na směr, ze kterého příchozí klient požaduje přístup k zařízení. *Odchozí* nebo *odchozí* odkazuje na směr, ve kterém vaše zařízení StorSimple odesílá data externě, mimo nasazení: například odchozí do Internetu.

| Přístav č.<sup>1,2</sup> | Dovnitř nebo ven | Rozsah portu | Požaduje se | Poznámky |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |Out |Síť WAN |Ne |<ul><li>Odchozí port se používá pro přístup k Internetu k načtení aktualizací.</li><li>Odchozí webový proxy server lze konfigurovat uživatelem.</li><li>Chcete-li povolit aktualizace systému, musí být tento port otevřen také pro pevné IP adresy řadiče.</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |Out |Síť WAN |Ano |<ul><li>Odchozí port se používá pro přístup k datům v cloudu.</li><li>Odchozí webový proxy server lze konfigurovat uživatelem.</li><li>Chcete-li povolit aktualizace systému, musí být tento port otevřen také pro pevné IP adresy řadiče.</li><li>Tento port se také používá na obou řadičích pro uvolňování paměti.</li></ul> |
| UDP 53 (DNS) |Out |Síť WAN |V některých případech; viz poznámky. |Tento port je vyžadován pouze v případě, že používáte internetový server DNS. |
| UDP 123 (NTP) |Out |Síť WAN |V některých případech; viz poznámky. |Tento port je vyžadován pouze v případě, že používáte internetový server NTP. |
| Protokol TCP 9354 |Out |Síť WAN |Ano |Odchozí port používá zařízení StorSimple ke komunikaci se službou StorSimple Device Manager. |
| 3260 (iSCSI) |V |LAN |Ne |Tento port se používá pro přístup k datům přes iSCSI. |
| 5985 |V |LAN |Ne |Příchozí port používá Správce snímků StorSimple ke komunikaci se zařízením StorSimple.<br>Tento port se také používá při vzdáleném připojení k prostředí Windows PowerShell pro StorSimple přes PROTOKOL HTTP. |
| 5986 |V |LAN |Ne |Tento port se používá při vzdáleném připojení k prostředí Windows PowerShell pro StorSimple přes protokol HTTPS. |

<sup>1</sup> Na veřejném Internetu není třeba otevírat žádné vstupní porty.

<sup>2</sup> Pokud konfiguraci brány provádí více portů, bude odchozí směrovaná provozní objednávka určena na základě pořadí směrování portů popsaného v [části Směrování portů](#routing-metric)níže.

<sup>3</sup> Řadič pevné IP adresy na vašem Zařízení StorSimple musí být směrovatelné a musí být schopen se připojit k Internetu přímo nebo prostřednictvím nakonfigurovaného webového proxy serveru. Pevné IP adresy se používají pro obsluhu aktualizací zařízení a pro uvolňování paměti. Pokud se řadiče zařízení nemohou připojit k Internetu prostřednictvím pevných IP adresy, nebudete moci aktualizovat zařízení StorSimple a uvolňování paměti nebude fungovat správně.

> [!IMPORTANT]
> Ujistěte se, že brána firewall neupravuje nebo nedešifruje žádný provoz TLS mezi zařízením StorSimple a Azure.


### <a name="url-patterns-for-firewall-rules"></a>Vzory adres URL pro pravidla brány firewall

Správci sítě mohou často konfigurovat pokročilá pravidla brány firewall na základě vzorů adres URL tak, aby filtrovali příchozí a odchozí přenosy. Vaše zařízení StorSimple a služba Správce zařízení StorSimple závisí na jiných aplikacích Microsoftu, jako je Azure Service Bus, Azure Active Directory Access Control, účty úložiště a servery Microsoft Update. Vzory adres URL přidružené k těmto aplikacím lze použít ke konfiguraci pravidel brány firewall. Je důležité si uvědomit, že vzory adres URL přidružené k těmto aplikacím se mohou změnit. To zase bude vyžadovat, aby správce sítě sledovat a aktualizovat pravidla brány firewall pro Vaše StorSimple podle potřeby.

Doporučujeme nastavit pravidla brány firewall pro odchozí provoz na základě Pevných IP adres StorSimple, ve většině případů liberálně. Níže uvedené informace však můžete použít k nastavení rozšířených pravidel brány firewall, která jsou potřebná k vytvoření zabezpečeného prostředí.

> [!NOTE]
> Ip adresy zařízení (zdroje) by měly být vždy nastaveny na všechna povolená síťová rozhraní. Cílové IP adresy by měly být nastaveny na [rozsahy IP adres datového centra Azure](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


#### <a name="url-patterns-for-azure-portal"></a>Vzory adres URL pro portál Azure

| Vzor adresy URL | Součást/funkce | Ip adresy zařízení |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |Služba Správce zařízení StorSimple<br>Služba řízení přístupu<br>Azure Service Bus<br>Ověřovací služba |Síťová rozhraní s podporou cloudu |
| `https://*.backup.windowsazure.com` |Registrace zařízení |POUZE DATA 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Odvolání certifikátu |Síťová rozhraní s podporou cloudu |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Účty úložiště Azure a monitorování |Síťová rozhraní s podporou cloudu |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Servery Microsoft Update<br> |Řadič pouze opravené IP adresy |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Řadič pouze opravené IP adresy |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Balíček podpory |Síťová rozhraní s podporou cloudu |

#### <a name="url-patterns-for-azure-government-portal"></a>Vzory adres URL pro portál Azure Government

| Vzor adresy URL | Součást/funkce | Ip adresy zařízení |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login.microsoftonline.us` |Služba Správce zařízení StorSimple<br>Služba řízení přístupu<br>Azure Service Bus<br>Ověřovací služba |Síťová rozhraní s podporou cloudu |
| `https://*.backup.windowsazure.us` |Registrace zařízení |POUZE DATA 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Odvolání certifikátu |Síťová rozhraní s podporou cloudu |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Účty úložiště Azure a monitorování |Síťová rozhraní s podporou cloudu |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Servery Microsoft Update<br> |Řadič pouze opravené IP adresy |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Řadič pouze opravené IP adresy |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Balíček podpory |Síťová rozhraní s podporou cloudu |

### <a name="routing-metric"></a>Metrika směrování

Metrika směrování je přidružena k rozhraním a bráně, které směrují data do zadaných sítí. Metrika směrování se používá protokolem směrování k výpočtu nejlepší cesty k danému cíli, pokud zjistí, že ke stejnému cíli existuje více cest. Čím nižší je metrika směrování, tím vyšší je předvolba.

V kontextu StorSimple, pokud více síťových rozhraní a brány jsou konfigurovány pro kanál provozu, metriky směrování vstoupí do hry k určení relativní pořadí, ve kterém rozhraní budou používat. Metriky směrování nelze změnit uživatelem. Rutinu `Get-HcsRoutingTable` však můžete použít k vytištění směrovací tabulky (a metrik) na zařízení StorSimple. Další informace o rutině Get-HcsRoutingTable v [řešení potíží s nasazením StorSimple](storsimple-troubleshoot-deployment.md).

Algoritmus metriky směrování použitý pro aktualizaci 2 a novější verze lze vysvětlit následujícím způsobem.

* Síťová rozhraní byla přiřazena sada předem určených hodnot.
* Vezměte v úvahu ukázkovou tabulku zobrazenou níže s hodnotami přiřazenými různým síťovým rozhraním, pokud jsou povolena v cloudu nebo bez cloudu, ale s nakonfigurovanou bránou. Všimněte si, že zde přiřazené hodnoty jsou pouze ukázkové hodnoty.

    | Síťové rozhraní | S podporou cloudu | Cloud-zakázáno s bránou |
    |-----|---------------|---------------------------|
    | Údaje 0  | 1            | -                        |
    | Údaje 1  | 2            | 20                       |
    | Údaje 2  | 3            | 30                       |
    | Údaje 3  | 4            | 40                       |
    | Údaje 4  | 5            | 50                       |
    | Údaje 5  | 6            | 60                       |


* Pořadí, ve kterém bude cloudový provoz směrován přes síťová rozhraní, je:
  
    *Údaje 0 > Údaje 1 > Datum 2 > údaje 3 > údaje 4 > údaje 5*
  
    To lze vysvětlit v následujícím příkladu.
  
    Zvažte zařízení StorSimple se dvěma síťovými rozhraními s podporou cloudu, data 0 a data 5. Data 1 až Data 4 jsou zakázána v cloudu, ale mají nakonfigurovanou bránu. Pořadí, ve kterém bude provoz směrován pro toto zařízení, bude:
  
    *Údaje 0 (1) > údaje 5 (6) > údaje 1 (20) > údaje 2 (30) > údaje 3 (40) > údaje 4 (50)*
  
    *Čísla v závorce označují příslušné metriky směrování.*
  
    Pokud se data 0 nezdaří, cloudový provoz se přesměruje přes data 5. Vzhledem k tomu, že brána je nakonfigurována ve všech ostatních sítích, pokud by se data 0 a data 5 nezdaří, cloudový provoz projde datem 1.
* Pokud síťové rozhraní s podporou cloudu selže, pak jsou 3 opakování s 30 sekund zpoždění pro připojení k rozhraní. Pokud všechny pokusy nezdaří, provoz je směrován do další dostupné rozhraní s podporou cloudu, jak je určeno směrovací tabulky. Pokud všechna síťová rozhraní s podporou cloudu senezdaří, zařízení převezme služby při selhání na jiný řadič (v tomto případě není restartováno).
* Pokud dojde k selhání programu VIP pro síťové rozhraní s podporou iSCSI, budou k dispozici 3 opakování se zpožděním 2 sekund. Toto chování zůstalo stejné z předchozích verzí. Pokud všechna síťová rozhraní iSCSI selžou, dojde k převzetí služeb při selhání řadiče (spolu s restartováním).
* Výstraha je také aktivována na zařízení StorSimple, pokud dojde k selhání virtuální ip. Další informace naleznete v [rychlém zobrazení výstrahy](storsimple-8000-manage-alerts.md).
* Pokud jde o opakování, iSCSI bude mít přednost před cloudem.
  
    Vezměme si následující příklad: Zařízení StorSimple má dvě povolená síťová rozhraní, data 0 a data 1. Data 0 je povolena v cloudu, zatímco data 1 jsou povolená v cloudu i iSCSI. Pro cloud nebo iSCSI nejsou povolena žádná jiná síťová rozhraní v tomto zařízení.
  
    Pokud se data 1 nezdaří, vzhledem k tomu, že se jedná o poslední síťové rozhraní iSCSI, bude výsledkem převzetí služeb při selhání řadiče na data 1 na druhém řadiči.

### <a name="networking-best-practices"></a>Doporučené postupy pro vytváření sítí

Kromě výše uvedených požadavků na sítě, pro optimální výkon vašeho řešení StorSimple, prosím, dodržovat následující osvědčené postupy:

* Ujistěte se, že vaše zařízení StorSimple má vždy k dispozici vyhrazenou šířku pásma 40 Mb/s (nebo více). Tato šířka pásma by neměla být sdílena (nebo přidělení by mělo být zaručeno pomocí zásad QoS) s jinými aplikacemi.
* Ujistěte se, že připojení k síti k Internetu je k dispozici po celou dobu. Sporadické nebo nespolehlivé připojení k Internetu k zařízením, včetně žádného připojení k Internetu, bude mít za následek nepodporovanou konfiguraci.
* Izolujte provoz iSCSI a cloud tím, že máte vyhrazená síťová rozhraní v zařízení pro iSCSI a přístup ke cloudu. Další informace naleznete v tématu [jak upravit síťová rozhraní](storsimple-8000-modify-device-config.md#modify-network-interfaces) na zařízení StorSimple.
* Nepoužívejte konfiguraci protokolu LACP (Link Aggregation Control Protocol) pro síťová rozhraní. Tato konfigurace se nepodporuje.

## <a name="high-availability-requirements-for-storsimple"></a>Vysoké požadavky na dostupnost pro StorSimple

Hardwarová platforma, která je součástí řešení StorSimple, má funkce dostupnosti a spolehlivosti, které poskytují základ pro vysoce dostupnou infrastrukturu úložiště odolnou proti chybám ve vašem datovém centru. Existují však požadavky a osvědčené postupy, které byste měli dodržovat, abyste zajistili dostupnost vašeho řešení StorSimple. Před nasazením StorSimple, pečlivě zkontrolujte následující požadavky a osvědčené postupy pro zařízení StorSimple a připojené hostitelské počítače.

Další informace o monitorování a údržbě hardwarových součástí zařízení StorSimple naleznete v části [Použití služby StorSimple Device Manager ke sledování hardwarových součástí a stavu](storsimple-8000-monitor-hardware-status.md) a nahrazení [hardwarové součásti StorSimple](storsimple-8000-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Vysoké požadavky na dostupnost a postupy pro vaše zařízení StorSimple

Pečlivě si přečtěte následující informace, abyste zajistili vysokou dostupnost zařízení StorSimple.

#### <a name="pcms"></a>PCM

StorSimple zařízení zahrnují redundantní, hot-swappable napájení a chlazení moduly (PCMs). Každý PCM má dostatečnou kapacitu pro poskytování služeb pro celé šasi. Chcete-li zajistit vysokou dostupnost, musí být nainstalovány oba počítače PCM.

* Připojte počítače PCM k různým zdrojům napájení a zajistěte tak dostupnost v případě selhání zdroje napájení.
* Pokud pcm selže, okamžitě požádejte o výměnu.
* Neúspěšný počítač PCM odebere teprve v případě, že máte náhradní počítač a jste připraveni jej nainstalovat.
* Neodstraňujte oba PCM současně. Modul PCM obsahuje záložní bateriový modul. Odebrání obou pcm ů bude mít za následek vypnutí bez ochrany baterie a stav zařízení nebude uložen. Další informace o baterii naleznete v části [Údržba záložního bateriového modulu](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Moduly regulátorů

StorSimple zařízení zahrnují redundantní moduly řadiče vyměnitelné za provozu. Moduly regulátoru pracují aktivním/pasivním způsobem. V každém okamžiku je jeden modul řadiče aktivní a poskytuje službu, zatímco druhý modul řadiče je pasivní. Modul pasivního regulátoru je zapnutý a zprovozní se, pokud aktivní modul regulátoru selže nebo je odebrán. Každý modul regulátoru má dostatečnou kapacitu pro poskytování služeb pro celý podvozek. Aby byla zajištěna vysoká dostupnost, musí být nainstalovány oba moduly řadiče.

* Ujistěte se, že oba moduly řadiče jsou nainstalovány po celou dobu.
* Pokud modul řadiče selže, okamžitě požádejte o výměnu.
* Modul řadiče se nezdařilo pouze v případě, že máte náhradní a jste připraveni jej nainstalovat. Odstranění modulu na delší dobu ovlivní proudění vzduchu a tím i chlazení systému.
* Ujistěte se, že síťová připojení k oběma modulům řadiče jsou shodná a že připojená síťová rozhraní mají identickou konfiguraci sítě.
* Pokud modul řadiče selže nebo potřebuje výměnu, ujistěte se, že druhý modul řadiče je v aktivním stavu před výměnou modulu řadiče se nezdařilo. Chcete-li ověřit, zda je ovladač aktivní, přejděte [na informace o identifikaci aktivního ovladače v zařízení](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Neodstraňujte oba moduly regulátoru současně. Pokud probíhá převzetí služeb při selhání řadiče, nevypínejte modul pohotovostního řadiče ani jej neodstraňujte z šasi.
* Po převzetí služeb při selhání řadiče počkejte alespoň pět minut před odebráním obou modulů řadiče.

#### <a name="network-interfaces"></a>Síťová rozhraní

Moduly řadičů zařízení StorSimple mají čtyři 1 gigabitová a dvě 10gigabitová ethernetová síťová rozhraní.

* Ujistěte se, že síťová připojení k oběma modulům řadiče jsou shodná a že síťová rozhraní, která jsou připojena k rozhraní modulu řadiče, mají identickou konfiguraci sítě.
* Pokud je to možné, nasaďte síťová připojení mezi různými přepínači, abyste zajistili dostupnost služby v případě selhání síťového zařízení.
* Při odpojování jediného nebo posledního zbývajícího rozhraní s podporou rozhraní iSCSI (s přiřazenými IP adresy) nejprve vypněte rozhraní a potom kabely odpojte. Pokud je rozhraní odpojeno jako první, způsobí, že aktivní řadič převezme služby při selhání pasivního řadiče. Pokud má pasivní řadič také odpojené odpovídající rozhraní, oba řadiče se před usazením na jednom řadiči několikrát restartují.
* Připojte alespoň dvě datová rozhraní k síti z každého modulu řadiče.
* Pokud jste povolili dvě rozhraní 10 GbE, nasaďte je na různých přepínačích.
* Pokud je to možné, použijte mpio na serverech, abyste zajistili, že servery mohou tolerovat selhání připojení, sítě nebo rozhraní.

Další informace o vysoké dostupnosti a výkonu zařízení v síti naleznete v [části Instalace zařízení StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) nebo [Instalace zařízení StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>SSD disky a pevné disky

Zařízení StorSimple zahrnují disky SSD (SSD) a pevné disky (HDD), které jsou chráněny zrcadlením prostorů. Použití zrcadlených prostorů zajišťuje, že zařízení je schopno tolerovat selhání jednoho nebo více disků SSD nebo pevných disků.

* Ujistěte se, že jsou nainstalovány všechny moduly SSD a HDD.
* Pokud ssd disk nebo pevný disk selže, okamžitě požádejte o výměnu.
* Pokud disk SSD nebo pevný disk selže nebo vyžaduje výměnu, nezapomeňte odebrat pouze disk SSD nebo pevný disk, který vyžaduje výměnu.
* Neodstraňujte ze systému více než jeden disk SSD nebo HDD.
  Selhání 2 nebo více disků určitého typu (HDD, SSD) nebo po sobě jdoucí selhání v krátkém časovém rámci může mít za následek selhání systému a potenciální ztrátu dat. Pokud k tomu dojde, [obraťte se](storsimple-8000-contact-microsoft-support.md) na podporu společnosti Microsoft o pomoc.
* Během výměny sledujte **sdílené součásti** v okně **Stavu hardwaru** pro jednotky na ssd diskech a pevných discích. Stav zelené kontroly označuje, že disky jsou v pořádku nebo OK, zatímco červený vykřičník označuje neúspěšný disk SSD nebo PEVNÝ DISK.
* Doporučujeme nakonfigurovat cloudové snímky pro všechny svazky, které je třeba chránit v případě selhání systému.

#### <a name="ebod-enclosure"></a>Skříň EBOD

StorSimple zařízení model 8600 obsahuje rozšířené bunch disků (EBOD) skříň kromě primární skříně. EBOD obsahuje řadiče EBOD a pevné disky (HDD), které jsou chráněny pomocí zrcadlených prostorů. Použití zrcadlených prostorů zajišťuje, že zařízení je schopno tolerovat selhání jednoho nebo více pevných disků. Skříň EBOD je připojena k primární skříni pomocí redundantních kabelů SAS.

* Ujistěte se, že oba moduly řadiče skříně EBOD, jak kabely SAS, tak všechny pevné disky jsou nainstalovány po celou dobu.
* Pokud modul řadiče skříně EBOD selže, okamžitě požádejte o výměnu.
* Pokud modul řadiče skříně EBOD selže, ujistěte se, že druhý modul řadiče je aktivní před výměnou modulu se nezdařilo. Chcete-li ověřit, zda je ovladač aktivní, přejděte [na informace o identifikaci aktivního ovladače v zařízení](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Během výměny modulu řadiče EBOD průběžně sledujte stav součásti ve službě StorSimple Device Manager přístupem ke > **stavu hardwaru** **monitoru**.
* Pokud kabel SAS selže nebo vyžaduje výměnu (K takovému určení by měla být zapojena podpora společnosti Microsoft), ujistěte se, že odeberete pouze kabel SAS, který vyžaduje výměnu.
* Neodstraňujte současně oba kabely SAS ze systému v žádném okamžiku.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Doporučení pro vysokou dostupnost pro hostitelské počítače

Pečlivě zkontrolujte tyto doporučené postupy, abyste zajistili vysokou dostupnost hostitelů připojených k vašemu zařízení StorSimple.

* Konfigurace storsimple s [konfiguracemi clusteru souborového serveru se dvěma uznami][1]. Odebráním jednotlivých bodů selhání a vytváření redundance na straně hostitele se stane vysoce dostupným celé řešení.
* Používejte nepřetržitě dostupné (CA) sdílené složky, které jsou k dispozici v systému Windows Server 2012 (SMB 3.0) pro vysokou dostupnost při převzetí služeb při selhání řadičů úložiště. Další informace o konfiguraci clusterů souborových serverů a průběžně dostupných sdílených složek se systémem Windows Server 2012 naleznete v této [ukázce videa](https://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>Další kroky

* [Další informace o omezení systému StorSimple](storsimple-8000-limits.md).
* [Přečtěte si, jak nasadit řešení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
