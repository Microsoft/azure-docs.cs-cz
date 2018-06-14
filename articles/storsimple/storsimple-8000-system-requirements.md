---
title: Požadavky na systém řady StorSimple 8000 | Microsoft Docs
description: Popisuje softwaru, sítě a vysokou dostupnost požadavky a osvědčené postupy pro řešení Microsoft Azure StorSimple.
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
ms.openlocfilehash: 1a9cdf31c5924d22d968cd99383417ba371cd1c3
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2018
ms.locfileid: "28011057"
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>Software řady StorSimple 8000, vysokou dostupnost a požadavky na síť

## <a name="overview"></a>Přehled

Vítá vás Microsoft Azure StorSimple. Tento článek popisuje důležité systémové požadavky a osvědčené postupy pro zařízení StorSimple a klienty úložiště přístup k zařízení. Doporučujeme, abyste si prošli informace pečlivě před nasazením systému StorSimple a pak zpátky na ni odkazuje podle potřeby během nasazení a další operace.

Systémové požadavky:

* **Požadavky na software pro klienty úložiště** -popisuje podporované operační systémy a veškeré další požadavky pro tyto operační systémy.
* **Požadavky sítě pro zařízení StorSimple** – poskytuje informace o portech, které musí být otevřen v bráně firewall pro přenosy iSCSI, cloudu nebo správu povolit.
* **Požadavky na vysokou dostupnost pro StorSimple** – popisuje požadavky na vysokou dostupnost a osvědčených postupů pro StorSimple zařízení a hostitele počítače.

## <a name="software-requirements-for-storage-clients"></a>Požadavky na software pro klienty úložiště

Následující softwarové požadavky jsou pro klienty úložiště, které přístup k zařízení StorSimple.

| Podporované operační systémy | Požadovaná verze | Další požadavky a poznámky |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2, 2016 |Svazky zařízení StorSimple iSCSI jsou podporovány pro použití v pouze následující typy disků systému Windows:<ul><li>Jednoduchý svazek na základním disku</li><li>Jednoduché a zrcadlený svazek na dynamickém disku</li></ul>Jsou podporovány pouze softwaru iniciátory iSCSI nativně součástí operačního systému. Iniciátory iSCSI hardwaru nejsou podporovány.<br></br>Windows Server 2012 a dynamické zajišťování 2016 a ODX funkce jsou podporované, pokud používáte svazek StorSimple iSCSI.<br><br>StorSimple můžete vytvořit dynamicky zajištěné a zcela zřizované svazky. Ji nelze vytvářet částečně zřizované svazky.<br><br>Přeformátování dynamicky zajištěné svazku může trvat dlouhou dobu. Doporučujeme odstranit svazek a vytvořit novou místo přeformátování. Pokud stále však upřednostňuje přeformátujte svazku:<ul><li>Spusťte následující příkaz před přeformátovat aby se zabránilo zpožděním recyklace místa: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Po dokončení formátování, použijte následující příkaz znovu zapnout recyklace místa:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Opravy hotfix systému Windows Server 2012, jak je popsáno v [KB 2878635](https://support.microsoft.com/kb/2870270) do počítače serveru systému Windows.</li></ul></li></ul></ul> Pokud konfigurujete Snapshot Manager zařízení StorSimple nebo adaptér StorSimple pro službu SharePoint, přejděte na [požadavky na Software pro volitelné součásti](#software-requirements-for-optional-components). |
| VMware ESX |5.5 a 6.0 |U VMware vSphere podporovány jako klient iSCSI. Funkce VAAI-block je podporovaná s VMware vSphere na zařízení StorSimple. |
| Linux RHEL/CentOS |5, 6 a 7 |Podpora pro Linux iSCSI klienty s verzemi iniciátor iSCSI otevřete 5, 6 a 7. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> IBM AIX se aktuálně nepodporuje s StorSimple.


## <a name="software-requirements-for-optional-components"></a>Požadavky na software pro volitelné součásti

Následující softwarové požadavky jsou pro volitelné součásti zařízení StorSimple (Snapshot Manager zařízení StorSimple a StorSimple adaptéru pro službu SharePoint).

| Komponenta | Platforma hostitele | Další požadavky a poznámky |
| --- | --- | --- |
| StorSimple Snapshot Manager |Windows Server 2008 R2 SP1, 2012, 2012 R2 |Použití služby StorSimple Snapshot Manager v systému Windows Server je nutné pro zálohování a obnovení zrcadlené dynamických disků a všechny zálohování konzistentní s aplikací.<br> Snapshot Manager zařízení StorSimple je podporována pouze na Windows Server 2008 R2 SP1 (64 bitů), Windows Server 2012 R2 a Windows Server 2012.<ul><li>Pokud používáte okno Server 2012, musíte nainstalovat rozhraní .NET 3.5 – 4.5 před instalací StorSimple Snapshot Manager.</li><li>Pokud používáte Windows Server 2008 R2 SP1, musíte před instalací StorSimple Snapshot Manager nainstalovat Windows Management Framework 3.0.</li></ul> |
| StorSimple Adapter pro SharePoint |Windows Server 2008 R2 SP1, 2012, 2012 R2 |<ul><li>Adaptér StorSimple pro službu SharePoint je podporována pouze na serveru SharePoint 2010 a SharePoint 2013.</li><li>RBS vyžaduje SQL Server Enterprise Edition, verze 2008 R2 nebo 2012.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>Požadavky sítě pro zařízení StorSimple

Zařízení StorSimple je zařízení uzamčené. Však musí být otevřen v bráně firewall povolit pro iSCSI, cloud a přenosy správy porty. Následující tabulka uvádí porty, které je potřeba otevřít v bráně firewall. V této tabulce *v* nebo *příchozí* odkazuje na směru, ve kterém příchozí žádosti klientů, přístup k zařízení. *Out* nebo *odchozí* odkazuje na směru, ve kterém zařízení StorSimple odešle data externě, kromě nasazení: například odchozí k Internetu.

| Číslo portu<sup>1,2</sup> | Příchozí nebo odchozí | Rozsah portů | Požaduje se | Poznámky |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |na více systémů |WAN |Ne |<ul><li>Odchozí port se používá pro přístup k Internetu, abyste získali aktualizace.</li><li>Odchozí webový proxy server je konfigurovatelná uživatelem.</li><li>Povolit aktualizace systému, musí být tento port taky otevřený pro pevné IP adresy řadiče.</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |na více systémů |WAN |Ano |<ul><li>Odchozí port se používá pro přístup k datům v cloudu.</li><li>Odchozí webový proxy server je konfigurovatelná uživatelem.</li><li>Povolit aktualizace systému, musí být tento port taky otevřený pro pevné IP adresy řadiče.</li><li>Tento port se také používá na obou řadičích pro uvolňování paměti.</li></ul> |
| UDP 53 (DNS) |na více systémů |WAN |V některých případech; v části poznámky. |Tento port je povinný, jenom v případě, že používáte adresu serveru DNS pro internetový. |
| UDP 123 (NTP) |na více systémů |WAN |V některých případech; v části poznámky. |Tento port je povinný, jenom v případě, že používáte server NTP založené na Internetu. |
| TCP 9354 |na více systémů |WAN |Ano |Odchozí port se používá zařízení StorSimple ke komunikaci se službou Správce zařízení StorSimple. |
| 3260 (iSCSI) |V |LAN |Ne |Tento port se používá pro přístup k datům přes iSCSI. |
| 5985 |V |LAN |Ne |Příchozí port se používá ve Snapshot Manageru zařízení StorSimple ke komunikaci se zařízením StorSimple.<br>Tento port se používá také při vzdálené připojení k Windows Powershellu pro StorSimple přes protokol HTTP. |
| 5986 |V |LAN |Ne |Tento port se používá při vzdálené připojení k Windows Powershellu pro StorSimple přes protokol HTTPS. |

<sup>1</sup> žádné příchozí porty muset otevřít na veřejného Internetu.

<sup>2</sup> Pokud víc portů provedení konfigurace brány, pořadí odchozí provoz směrovaný určí na základě portů směrování pořadí, které jsou popsané v [Port směrování](#routing-metric), níže.

<sup>3</sup> zařízení StorSimple pevné IP adresy řadiče musí být směrovatelné a musí umožňovat připojení k Internetu přímo nebo prostřednictvím nakonfigurované webový proxy server. Pevné IP adresy se používají k doručování aktualizací do zařízení a pro uvolňování paměti. Pokud řadiče zařízení nemůže připojit k Internetu prostřednictvím pevné IP adresy, nebudete moci aktualizace zařízení StorSimple a uvolňování paměti nebude správně fungovat.

> [!IMPORTANT]
> Ujistěte se, zda brána firewall upravit nebo dešifrovat veškerou komunikaci SSL mezi zařízení StorSimple a Azure.


### <a name="url-patterns-for-firewall-rules"></a>Adresa URL vzory pro pravidla brány firewall

Správci sítě často můžete nakonfigurovat pravidla rozšířené brány firewall na základě vzorů adresy URL pro filtrování příchozí a odchozí provoz. Zařízení StorSimple a služby StorSimple Manager zařízení závisí na jiných aplikací společnosti Microsoft, například Azure Service Bus, Azure Active Directory řízení přístupu, účty úložiště a servery Microsoft Update. Adresa URL vzory související s těmito aplikacemi slouží ke konfiguraci pravidel brány firewall. Je důležité si uvědomit, že můžete změnit adresu URL vzory související s těmito aplikacemi. Správce sítě a monitorovat a aktualizovat pravidla brány firewall pro vaše zařízení StorSimple jako a v případě potřeby pak bude nutné.

Doporučujeme vám, že nastavíte vašich pravidlech brány firewall pro odchozí přenosy, založené na StorSimple liberally pevné IP adresy, ve většině případů. Můžete však použít níže uvedené informace pravidel rozšířené brány firewall, které jsou potřebné k vytvoření zabezpečeného prostředí.

> [!NOTE]
> Zařízení (zdroj) IP adresy musí být vždy nastavená na všechny povolené síťové rozhraní. Cílové IP adresy musí být nastavena na [rozsahy IP adres Azure datacenter](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


#### <a name="url-patterns-for-azure-portal"></a>Vzory adres URL pro portál Azure

| Vzor adresy URL | Komponenta nebo funkce | IP adresy zařízení |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |Služba Správce zařízení StorSimple<br>Access Control Service<br>Azure Service Bus<br>Služba ověřování |Povolenou podporu cloudu síťová rozhraní |
| `https://*.backup.windowsazure.com` |Registrace zařízení |Pouze DATA 0 |
| `http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*` |Odvolání certifikátu |Povolenou podporu cloudu síťová rozhraní |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Monitorování a účty úložiště Azure |Povolenou podporu cloudu síťová rozhraní |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com` |Microsoft Update servers<br> |Pevné IP adresy řadiče pouze |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Pevné IP adresy řadiče pouze |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Balíček pro podporu |Povolenou podporu cloudu síťová rozhraní |

#### <a name="url-patterns-for-azure-government-portal"></a>Adresa URL vzory pro portál Azure Government.

| Vzor adresy URL | Komponenta nebo funkce | IP adresy zařízení |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login.microsoftonline.us` |Služba Správce zařízení StorSimple<br>Access Control Service<br>Azure Service Bus<br>Služba ověřování |Povolenou podporu cloudu síťová rozhraní |
| `https://*.backup.windowsazure.us` |Registrace zařízení |Pouze DATA 0 |
| `http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*` |Odvolání certifikátu |Povolenou podporu cloudu síťová rozhraní |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Monitorování a účty úložiště Azure |Povolenou podporu cloudu síťová rozhraní |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com` |Microsoft Update servers<br> |Pevné IP adresy řadiče pouze |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Pevné IP adresy řadiče pouze |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Balíček pro podporu |Povolenou podporu cloudu síťová rozhraní |

### <a name="routing-metric"></a>Metrika směrování

Směrování metrika je přidružené k rozhraní a brány, kterou směrování dat na servery z uvedených sítí. Směrování metrika se používá ve směrovací protokol k výpočtu nejlepší cestu do určitého cíle, pokud se zjišťuje, že existuje více cest pro stejný cíl. Čím nižší metriky směrování, tím vyšší priorita.

V kontextu zařízení StorSimple Pokud jsou nakonfigurované víc síťových rozhraní a bran pro provoz kanál, bude směrování metriky vyskytnou se pro určení relativní pořadí, ve kterém se získat rozhraní používat. Metrika směrování nelze změnit uživatelem. Můžete ale použít `Get-HcsRoutingTable` rutiny vytiskněte směrovací tabulku (a metriky) v zařízení StorSimple. Další informace o rutině Get-HcsRoutingTable v [nasazení řešení potíží s StorSimple](storsimple-troubleshoot-deployment.md).

Takto lze vysvětlit směrování metriky algoritmem použitým pro Update 2 nebo novější.

* Sadu předem určený hodnoty byly přiřazeny síťových rozhraní.
* Vezměte v úvahu tabulkou aplikace příklad vidíte níže přiřazených k různým síťovým rozhraním, pokud je cloud povolen hodnot nebo cloudu zakázaný, ale nakonfigurované brány. Poznámka: hodnoty přiřazené tady jsou pouze ukázkové hodnoty.

    | Síťové rozhraní | Povolenou podporu cloudu | Cloud zakázaný s bránou |
    |-----|---------------|---------------------------|
    | Data 0  | 1            | -                        |
    | Data 1  | 2            | 20                       |
    | Data 2  | 3            | 30                       |
    | Data 3  | 4            | 40                       |
    | Data 4  | 5            | 50                       |
    | Data 5  | 6            | 60                       |


* Pořadí, ve kterém budou směrovány přenosy dat cloudové přes rozhraní sítě je:
  
    *Data 0 > Data 1 > datum 2 > dat 3 > dat 4 > Data 5*
  
    To lze vysvětlit v následujícím příkladu.
  
    Vezměte v úvahu zařízení StorSimple se dvěma povolenou podporu cloudu síťovými rozhraními, Data 0 a Data 5. Data 1 až 4 dat jsou cloudu zakázaný, ale mít nakonfigurované brány. Pořadí, ve kterém budou směrovány přenosy pro toto zařízení bude:
  
    *Data 0 (1) > Data 5 (6) > Data 1 (20) > Data 2 (30) > Data 3 (40) > Data 4 (50)*
  
    *Čísla v závorkách označují příslušných směrování metriky.*
  
    Pokud se Data 0 nezdaří, bude získat přenosy dat cloudové směrován až Data 5. Vzhledem k tomu, že brána je nakonfigurovaná v jiné síti, pokud Data 0 a Data 5 nezdaří, bude provoz cloudové projít Data 1.
* Pokud se nezdaří povolenou podporu cloudu síťové rozhraní, pak jsou 3 opakování s 30 druhý zpožděním pro připojení k rozhraní. Pokud selžou všechny opakované pokusy, provoz se směruje na další dostupný povolenou podporu cloudu rozhraní určeného do směrovací tabulky. Pokud všechny povolenou podporu cloudu síťové rozhraní služeb při selhání, pak zařízení bude převzetí služeb při selhání jiný řadič (bez restartování v tomto případě).
* Pokud dojde k selhání VIP pro rozhraní sítě iSCSI povolený, budou existovat 3 opakování s zpožděním 2 sekundy. Toto chování má zůstanou stejná z předchozích verzí. Pokud selžou všechny síťová rozhraní iSCSI, se provedou (připojí restartování) selhání řadiče.
* Výstrahu se také vyvolá zařízení StorSimple, když dojde k selhání VIP. Další informace, přejděte na [výstrahy Stručná referenční příručka](storsimple-8000-manage-alerts.md).
* Z hlediska opakovaných pokusů, které bude iSCSI mají přednost před cloudu.
  
    Podívejte se na následující příklad: StorSimple A zařízení má dvě rozhraní sítě povolené, Data 0 a Data 1. Rozhraní data 0 má povolenou podporu cloudu vzhledem k tomu Data 1 je i cloudu a podporou iSCSI. Žádné jiné síťová rozhraní na tomto zařízení jsou povolené pro cloud nebo iSCSI.
  
    Pokud Data 1 selže, je zadána poslední síťové rozhraní iSCSI, výsledkem bude selhání řadič 1 dat na jiný řadič.

### <a name="networking-best-practices"></a>Osvědčené postupy sítě

Kromě výše uvedených síťové požadavky pro optimální výkon vašeho řešení StorSimple prosím splňovat následující osvědčené postupy:

* Zajistěte, aby zařízení StorSimple vyhrazené šířky pásma 40 MB/s (nebo více) vždy k dispozici. Neměl by se sdílet tento šířky pásma (nebo přidělení by mělo být zaručeno prostřednictvím zásad QoS) s jinými aplikacemi.
* Zajistěte, aby po celou dobu je k dispozici síťové připojení k Internetu. Připojení k Internetu výskyt občasný, nebo nespolehlivé do zařízení, včetně jakkoli, bez připojení k Internetu bude mít za následek nepodporované konfigurace.
* Izolujte přenosy dat iSCSI a cloudu podle nutnosti mít vyhrazené síťových rozhraní na vašem zařízení pro přístup k iSCSI a cloudu. Další informace najdete v tématu Jak [upravit síťových rozhraní](storsimple-8000-modify-device-config.md#modify-network-interfaces) zařízení StorSimple.
* Nepoužívejte konfiguraci odkaz agregace řízení Protokol LACP () pro síťová rozhraní. Toto je Nepodporovaná konfigurace.

## <a name="high-availability-requirements-for-storsimple"></a>Požadavky na vysokou dostupnost pro StorSimple

Hardwarová platforma, která je součástí řešení StorSimple obsahuje dostupnost a spolehlivost funkcí, které tvoří základ pro vysoce dostupné a odolné proti chybám úložiště infrastruktury ve vašem datovém centru. Existují však požadavky a osvědčené postupy, které by měly splňovat k zajištění dostupnosti vašeho řešení StorSimple. Před nasazením zařízení StorSimple, pečlivě zkontrolujte následující požadavky a osvědčené postupy pro zařízení StorSimple a připojené hostitelských počítačích.

Další informace o monitorování a údržbu hardwarové součásti zařízení StorSimple, přejděte na [používat službu StorSimple Manager zařízení a stav monitorování hardwarové součásti](storsimple-8000-monitor-hardware-status.md) a [StorSimple výměna hardwaru součást](storsimple-8000-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Požadavky na vysokou dostupnost a postupy pro zařízení StorSimple

Zkontrolujte následující informace pečlivě pro zajištění vysoké dostupnosti zařízení StorSimple.

#### <a name="pcms"></a>PCMs

Zařízení StorSimple zahrnují za provozu, redundantní napájení a chlazení moduly (PCMs). Každý PCM má dostatečnou kapacitu k poskytování služby pro celé skříně. K zajištění vysoké dostupnosti, musí být nainstalován i PCMs.

* Vaše PCMs připojení ke zdrojům různých power k zajištění dostupnosti, pokud se nezdaří zdroji napájení.
* Pokud se nezdaří PCM, žádost náhradní okamžitě.
* Odeberte selhání PCM jenom v případě, že máte nahrazení a jsou připraveny k její instalaci.
* Současně neodebírejte obou PCMs. Modul PCM zahrnuje modul zálohování baterie. Odebrání i PCMs způsobí vypnutí bez ochrany baterie a stavu zařízení se neuloží. Další informace o baterie, přejděte na [Udržovat modul zálohování baterie](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Moduly řadiče

Zařízení StorSimple zahrnují moduly řadiče redundantní, za provozu. Moduly řadiče pracovat způsobem aktivní nebo pasivní. V každém okamžiku jeden modul řadiče je aktivní a poskytuje služby, zatímco ostatní řadiče modul je pasivní. Modul pasivní řadiče je zapnutý a lze použít, pokud modul active řadiče selže nebo je odebrat. Každý modul řadiče má dostatečnou kapacitu k poskytování služby pro celé skříně. Oba řadiče moduly musí být nainstalované k zajištění vysoké dostupnosti.

* Ověřte, že oba řadiče moduly jsou nainstalovány za všech okolností.
* Pokud modul řadiče selže, žádost náhradní okamžitě.
* Odeberte modul selhání řadiče jenom v případě, že máte nahrazení a jsou připraveny k její instalaci. Odebírání modulu pro delší dobu, bude mít vliv na vzduchu a chlazení v systému.
* Ujistěte se, že síťová připojení pro oba řadiče moduly jsou identické a mít konfigurace aplikace identických síťových rozhraní propojená síť.
* Pokud modul řadiče selže nebo potřebuje nahrazení, ujistěte se, že jiné řadiče modulu je v aktivním stavu před výměnou modul selhání řadiče. Chcete-li ověřit, zda je řadič active, přejděte na [identifikovat řadič active na vašem zařízení](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Neodebírejte oba řadiče modulů ve stejnou dobu. Pokud probíhá selhání řadiče, vypněte modul pohotovostní řadiče nebo ji odeberte z skříň.
* Po selhání řadiče počkejte alespoň pět minut před odebráním buď řadiče modulu.

#### <a name="network-interfaces"></a>Síťová rozhraní

StorSimple zařízení řadiče moduly každý mají čtyři 1 Gigabit a 10 dva adaptéry Gigabit Ethernet síťových rozhraní.

* Ujistěte se, že síťová připojení pro oba řadiče moduly jsou identické a rozhraní sítě, že rozhraní modulu řadiče připojeni tak, aby měl konfigurace aplikace identických síťových.
* Pokud je to možné, nasaďte připojení k síti přes různé přepínače, aby Zajistěte dostupnost služeb v případě selhání síťové zařízení.
* Při odpojování poslední zbývající rozhraní iSCSI povolený nebo jediný (s přiřazené IP adresy), nejdřív zakázat rozhraní a potom odpojte kabelů. Pokud rozhraní je odpojené nejprve, může to způsobit active kontroleru k převzetí služeb při selhání řadičem pasivní. Když má pasivní řadič také jeho odpovídající rozhraní byl odpojen, pak oběma řadičům restartuje více než jednou. před spuštěním na jednom řadiči.
* Aspoň dvě rozhraní dat připojte k síti z jednotlivých modulu řadiče.
* Pokud jste povolili dva 10 GbE rozhraní nasazení těch s jednosměrným různým přepínačům.
* Pokud je to možné, použijte funkci MPIO na serverech a ujistěte se, že servery budou tolerovat odkaz, sítě nebo selhání rozhraní.

Další informace o zařízení pro vysokou dostupnost a výkon sítě, přejděte na [instalace zařízení StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) nebo [instalace zařízení StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>SSD a HDD

Zařízení StorSimple obsahovat disky SSD (Solid-State Drive) a pevných disků (HDD) chráněné pomocí zrcadlení prostory. Použití zrcadlených prostorech zajišťuje, že zařízení dokáže odolat selhání jednotky SSD nebo pevné disky.

* Ujistěte se, zda jsou nainstalovány všechny moduly SSD a HDD.
* Pokud na SSD nebo HDD selže, žádost o nahrazení okamžitě.
* Pokud na SSD nebo HDD selže nebo vyžaduje nahrazení, ujistěte se, odstranit pouze SSD nebo pevný disk, který vyžaduje nahrazení.
* Neodebírejte víc než jeden SSD nebo HDD ze systému v libovolném bodě v čase.
  Selhání 2 nebo více disků určité typu (HDD, SSD) nebo po sobě jdoucích selhání v rámci krátkého času může dojít ke ztrátě dat systému selhání a potenciální. Pokud k tomu dojde, [kontaktovat Microsoft Support](storsimple-8000-contact-microsoft-support.md) o pomoc.
* Při nahrazení, monitorovat **sdílené součásti** v **stavu hardwaru** okno pro jednotky SSD a pevných disků. Zelená zkontrolujte stav označuje, že disky jsou v pořádku nebo OK zatímco červený vykřičník bodu indikuje selhání SSD nebo pevný disk.
* Doporučujeme nakonfigurovat cloudových snímků pro všechny svazky, které potřebujete k ochraně v případě selhání systému.

#### <a name="ebod-enclosure"></a>EBOD skříň

Model zařízení StorSimple 8600 obsahuje skříň rozšířené Bunch disky (EBOD) kromě primární skříň. EBOD obsahuje EBOD řadiče a pevných disků (HDD) chráněné pomocí zrcadlení prostory. Použití zrcadlených prostorech zajišťuje, že zařízení je schopen tolerovat selhání jednoho nebo více pevných disků. Skříň EBOD je připojená k primární skříň prostřednictvím redundantní SAS kabely.

* Ujistěte se, že oba EBOD skříň řadiče moduly SAS kabely i všechny pevné disky jsou nainstalovány za všech okolností.
* Pokud modul EBOD skříň řadiče selže, žádost o nahrazení okamžitě.
* Pokud modul EBOD skříň řadiče selže, ujistěte se, že jiné řadiče modulu je aktivní, před nahrazením modulu se nezdařilo. Chcete-li ověřit, zda je řadič active, přejděte na [identifikovat řadič active na vašem zařízení](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Během EBOD řadiče modulu nahrazení, neustále monitorovat stav součásti ve službě StorSimple Manager zařízení díky přístupu k **monitorování** > **stavu hardwaru**.
* Pokud je kabel SAS selže nebo vyžaduje nahrazení (Microsoft Support by podílejí aby toto zjištění), ujistěte se, odebrat pouze kabel SAS, která vyžaduje nahrazení.
* Neodebírejte současně oba SAS kabely ze systému v libovolném bodě v čase.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Vysoká dostupnost doporučení pro počítače hostitele

Pečlivě zkontrolujte tyto doporučené postupy pro zajištění vysoké dostupnosti hostitelů připojené k zařízení StorSimple.

* Konfigurace zařízení StorSimple s [dvěma uzly souborového serveru clusteru konfigurace][1]. Odebráním jediný bod selhání a sestavování v redundance na straně hostitele stane dostupným celé řešení.
* Použijte nepřetržitě dostupné složky (CA) k dispozici v systému Windows Server 2012 (SMB 3.0) pro zajištění vysoké dostupnosti během převzetí služeb při selhání řadičích úložiště. Další informace o konfiguraci clusterů souborových serverů a nepřetržitě dostupné sdílené složky v systému Windows Server 2012, najdete v části to [Videoukázka](http://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>Další postup

* [Další informace o omezení systému StorSimple](storsimple-8000-limits.md).
* [Informace o nasazení řešení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
