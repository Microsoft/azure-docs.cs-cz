---
title: StorSimple 8000 series systémové požadavky | Dokumentace Microsoftu
description: Popisuje softwaru, sítě a požadavky na vysokou dostupnost a osvědčené postupy pro řešení Microsoft Azure StorSimple.
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
ms.openlocfilehash: f05e3e85d36ffc23a193a6771a0271c71b2f8544
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60631902"
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>Software řady StorSimple 8000, vysokou dostupnost a požadavky na síť

## <a name="overview"></a>Přehled

Vítá vás Microsoft Azure StorSimple. Tento článek popisuje důležité systémové požadavky a osvědčené postupy pro zařízení StorSimple a úložiště klientům přístup k zařízení. Doporučujeme, abyste si informace o pečlivě před nasazení systému StorSimple a pak zpátky na ni odkazovat podle potřeby během nasazení a následná operace.

Systémové požadavky:

* **Požadavky na software pro klienty úložiště** -popisuje podporované operační systémy a veškeré další požadavky pro tyto operační systémy.
* **Požadavky na síť pro zařízení StorSimple** – poskytuje informace o portech, které musí být otevřené v bráně firewall povolit pro přenosy iSCSI, cloud nebo správy.
* **Požadavky na vysokou dostupnost pro StorSimple** – popisuje požadavky na vysokou dostupnost a osvědčených postupech pro StorSimple zařízení a hostitelského počítače.

## <a name="software-requirements-for-storage-clients"></a>Požadavky na software pro klienty úložiště

Následující softwarové požadavky jsou pro klienty úložiště, které přistupují k zařízení StorSimple.

| Podporované operační systémy | Požadovaná verze | Další požadavky a poznámky |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2, 2016 |ISCSI svazky zařízení StorSimple se podporují pro použití na pouze následující typy disků Windows:<ul><li>Jednoduchý svazek na základním disku</li><li>Jednoduchých a zrcadlených svazek na dynamickém disku</li></ul>Jsou podporovány pouze software iniciátory iSCSI nativně v operačním systému k dispozici. Nepodporují se iniciátory iSCSI hardwaru.<br></br>Pokud používáte iSCSI svazek StorSimple, jsou podporovány systému Windows Server 2012 a 2016 dynamické zajišťování a funkce ODX.<br><br>StorSimple můžete vytvářet dynamicky zajištěné a zcela zřizované svazky. Ho nelze vytvářet částečně zřizované svazky.<br><br>Přeformátování dynamicky zajištěné svazku může trvat dlouhou dobu. Doporučujeme odstranění svazku a pak vytvořit nový místo přeformátování. Pokud stále však preferovat u vydavatelských svazku:<ul><li>Spusťte následující příkaz před přeformátovat, aby se vyhnuli prodlevám recyklace místa: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Po dokončení formátování opětovné povolení recyklace místa použijte následující příkaz:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Použít opravu hotfix systému Windows Server 2012, jak je popsáno v [KB 2878635](https://support.microsoft.com/kb/2870270) do počítače serveru systému Windows.</li></ul></li></ul></ul> Pokud konfigurujete StorSimple Snapshot Manageru nebo StorSimple Adapter pro SharePoint, přejděte na [softwarové požadavky pro volitelné součásti](#software-requirements-for-optional-components). |
| VMware ESX |5.5 a 6.0 |Podporované s VMware vSphere jako klient iSCSI. VAAI bloku funkce je podporovaná s VMware vSphere na zařízení StorSimple. |
| Linux RHEL nebo CentOS |5, 6 a 7 |Podpora pro Linux iSCSI klienty s verzemi iniciátoru open iSCSI 5, 6 a 7. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> Storsimple se aktuálně nepodporuje IBM AIX.


## <a name="software-requirements-for-optional-components"></a>Požadavky na software pro volitelné součásti

Jsou následující softwarové požadavky pro volitelné součásti StorSimple (Snapshot Manageru zařízení StorSimple a StorSimple Adapter pro SharePoint).

| Komponenta | Hostitelskou platformu | Další požadavky a poznámky |
| --- | --- | --- |
| StorSimple Snapshot Manager |Windows Server 2008 R2 SP1, 2012, 2012 R2 |Použití nástroje Snapshot Manageru zařízení StorSimple ve Windows serveru se vyžaduje pro zálohování a obnovení zrcadlené dynamických disků a pro jakékoli zálohy konzistentní s aplikací.<br> StorSimple Snapshot Manageru je podporován pouze v systému Windows Server 2008 R2 SP1 (64-bit), Windows Server 2012 R2 a Windows Server 2012.<ul><li>Pokud používáte okno Server 2012, je nutné nainstalovat rozhraní .NET 3.5 – 4.5 před instalací StorSimple Snapshot Manageru.</li><li>Pokud používáte Windows Server 2008 R2 SP1, je nutné nainstalovat Windows Management Framework 3.0 před instalací StorSimple Snapshot Manageru.</li></ul> |
| StorSimple Adapter pro SharePoint |Windows Server 2008 R2 SP1, 2012, 2012 R2 |<ul><li>StorSimple Adapter pro SharePoint je podporován pouze na SharePoint 2010 a SharePoint 2013.</li><li>RBS vyžaduje SQL Server Enterprise Edition, verze 2008 R2 nebo 2012.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>Požadavky na síť pro zařízení StorSimple

Zařízení StorSimple je zařízení uzamknuté. Porty je však nutné otevřít v bráně firewall povolit pro iSCSI, cloud a správy provozu. Následující tabulka uvádí porty, které je potřeba otevřít v bráně firewall. V této tabulce *v* nebo *příchozí* odkazuje na směru, ze kterého příchozí požadavky klientů, přístup k vašemu zařízení. *Navýšení kapacity* nebo *odchozí* odkazuje na směru, ve kterém zařízení StorSimple, odesílá data externě, nad rámec nasazení: pro příklad, odchozí k Internetu.

| Číslo portu<sup>1,2</sup> | Snížení nebo navýšení kapacity | Rozsah portů | Požaduje se | Poznámky |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |navýšení kapacity |WAN |Ne |<ul><li>Odchozí port se používá pro přístup k Internetu pro načtení aktualizací.</li><li>Odchozí webový proxy server je konfigurovatelná uživatelem.</li><li>Povolit aktualizace systému, musí být tento port taky otevřený pro pevné IP adresy kontroleru.</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |navýšení kapacity |WAN |Ano |<ul><li>Odchozí port se používá pro přístup k datům v cloudu.</li><li>Odchozí webový proxy server je konfigurovatelná uživatelem.</li><li>Povolit aktualizace systému, musí být tento port taky otevřený pro pevné IP adresy kontroleru.</li><li>Tento port se také používá na obou řadičích pro uvolnění paměti.</li></ul> |
| UDP 53 (DNS) |navýšení kapacity |WAN |V některých případech; v části poznámky. |Tento port je povinný, jenom v případě, že používáte server služby Internetová DNS. |
| UDP 123 (NTP) |navýšení kapacity |WAN |V některých případech; v části poznámky. |Tento port je povinný, jenom v případě, že používáte server služby Internetová NTP. |
| TCP 9354 |navýšení kapacity |WAN |Ano |Odchozí port používá ke komunikaci se službou StorSimple Device Manager zařízení StorSimple. |
| 3260 (iSCSI) |V |LAN |Ne |Tento port se používá pro přístup k datům přes iSCSI. |
| 5985 |V |LAN |Ne |Příchozí port slouží ke komunikaci se zařízením StorSimple ve StorSimple Snapshot Manageru.<br>Tento port se používá také při vzdálené připojení k prostředí Windows PowerShell pro StorSimple přes protokol HTTP. |
| 5986 |V |LAN |Ne |Tento port se používá při vzdálené připojení k prostředí Windows PowerShell pro StorSimple přes protokol HTTPS. |

<sup>1</sup> žádné příchozí porty je potřeba otevřít na veřejném Internetu.

<sup>2</sup> Pokud více portů provést konfiguraci brány, odchozí provoz směrovaný pořadí se určí podle pořadí port směrování podle [Port směrování](#routing-metric)níže.

<sup>3</sup> pevné IP adresy na vašem zařízení StorSimple kontroleru musí být směrovatelné a schopné připojení k Internetu přímo nebo prostřednictvím proxy serveru nakonfigurovaný web. Pevné IP adresy se používají k doručování aktualizací do zařízení a pro uvolňování paměti. Pokud řadiče zařízení nemůže připojit k Internetu prostřednictvím pevné IP adresy, nebude moci aktualizace zařízení StorSimple a uvolňování paměti nebude správně fungovat.

> [!IMPORTANT]
> Ujistěte se, že brána firewall neupravuje nebo dešifrovat veškerý provoz protokolu SSL mezi zařízení StorSimple a Azure.


### <a name="url-patterns-for-firewall-rules"></a>Vzory adres URL pro pravidla brány firewall

Správci sítě můžou často konfigurace pravidla brány firewall na Upřesnit na základě vzory adres URL pro filtrování příchozího a odchozího provozu. Zařízení StorSimple a služby Správce zařízení StorSimple závisí na jiné aplikace od Microsoftu, jako je Azure Service Bus, Azure Active Directory Access Control, účty úložiště a serverům Microsoft Update. Vzory adres URL, které jsou přidružené k těmto aplikacím je možné nakonfigurovat pravidla brány firewall. Je důležité pochopit, že můžete změnit vzory adres URL, které jsou přidružené k těmto aplikacím. To zase vyžaduje správce sítě, monitorovat a aktualizovat pravidla brány firewall pro StorSimple jako a v případě potřeby.

Doporučujeme nastavit pravidla brány firewall pro odchozí provoz, podle StorSimple liberally pevné IP adresy, ve většině případů. Ale můžete použít následující informace pro nastavení pravidla brány firewall na pokročilé, které budete potřebovat pro vytvoření zabezpečeného prostředí.

> [!NOTE]
> Zařízení (zdroj) IP adresy musí být vždy nastavená na všechny povolené síťové rozhraní. Cílové IP adresy musí být nastavená na [rozsahy IP adres datacentra Azure](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


#### <a name="url-patterns-for-azure-portal"></a>Vzory adres URL pro web Azure portal

| Vzor adresy URL | Komponenta nebo funkce | IP adresy zařízení |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |Služba Správce zařízení StorSimple<br>Access Control Service<br>Azure Service Bus<br>Ověřovací službu |Povolenou podporu cloudu síťová rozhraní |
| `https://*.backup.windowsazure.com` |Registrace zařízení |Pouze DATA 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Odvolání certifikátu |Povolenou podporu cloudu síťová rozhraní |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Účty úložiště Azure a monitorování |Povolenou podporu cloudu síťová rozhraní |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Servery Microsoft Update<br> |Pevné IP adresy jenom kontroleru |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Pevné IP adresy jenom kontroleru |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Balíček pro podporu. |Povolenou podporu cloudu síťová rozhraní |

#### <a name="url-patterns-for-azure-government-portal"></a>Vzory adres URL pro portál Azure Government

| Vzor adresy URL | Komponenta nebo funkce | IP adresy zařízení |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login.microsoftonline.us` |Služba Správce zařízení StorSimple<br>Access Control Service<br>Azure Service Bus<br>Ověřovací službu |Povolenou podporu cloudu síťová rozhraní |
| `https://*.backup.windowsazure.us` |Registrace zařízení |Pouze DATA 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Odvolání certifikátu |Povolenou podporu cloudu síťová rozhraní |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Účty úložiště Azure a monitorování |Povolenou podporu cloudu síťová rozhraní |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Servery Microsoft Update<br> |Pevné IP adresy jenom kontroleru |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Pevné IP adresy jenom kontroleru |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Balíček pro podporu. |Povolenou podporu cloudu síťová rozhraní |

### <a name="routing-metric"></a>Metriky

Metriky směrování je přidružen rozhraní a brána, který směrovat data do vybraných sítích. Metriky se používá směrovací protokol k výpočtu nejlepší cestu do určitého cíle, pokud zjistí, že existuje více cest do stejného cíle. Nižší metriky, tím vyšší priorita.

V rámci StorSimple Pokud více síťových rozhraní a brány jsou nakonfigurované k provozu kanál, směrování metriky přijde do hry pro určení relativního pořadí, ve kterém se získat rozhraní používat. Směrování metriky nelze změnit tímto uživatelem. Můžete ale použít `Get-HcsRoutingTable` rutiny vytiskne směrovací tabulka (a metriky) na zařízení StorSimple. Další informace o rutině Get-HcsRoutingTable v [nasazení řešení potíží s StorSimple](storsimple-troubleshoot-deployment.md).

Takto lze vysvětlit směrování metriky algoritmus použitý pro aktualizaci Update 2 a novějších verzích.

* Sada předdefinovaných hodnot se přiřadily síťová rozhraní.
* Vezměte v úvahu tabulku příkladu níže se hodnoty přiřazené k různým síťovým rozhraním, pokud je cloud – povolen nebo cloudových – zakázáno, ale s nakonfigurovanou bránu. Mějte na paměti, že hodnoty přiřazené Zde jsou pouze ukázkové hodnoty.

    | Síťové rozhraní | Cloud-enabled | Cloud – zakázáno s bránou |
    |-----|---------------|---------------------------|
    | Data 0  | 1            | -                        |
    | Data 1  | 2            | 20                       |
    | Data 2  | 3            | 30                       |
    | Data 3  | 4            | 40                       |
    | Data 4  | 5            | 50                       |
    | Data 5  | 6            | 60                       |


* Pořadí, ve kterém se budou směrovat přenosy v cloudu přes síťová rozhraní, je:
  
    *Data 0 > Data 1 > datum 2 > Data 3 > Data 4 > Data 5*
  
    To lze vysvětlit v následujícím příkladu.
  
    Vezměte v úvahu zařízení StorSimple se dvěma povolenou podporu cloudu síťovými rozhraními, Data 0 a Data 5. Data 1 až Data 4 jsou cloudu – zakázáno, ale mít nakonfigurovanou bránu. Pořadí, ve kterém se budou směrovat provoz pro toto zařízení bude:
  
    *Data 0 (1) > Data 5 (6) > Data 1 (20) > Data 2 (30) > Data 3 (40) > Data 4 (50)*
  
    *Čísla v závorkách označují příslušných směrování metriky.*
  
    Pokud se Data 0 nezdaří, bude získat cloud provoz směrován přes Data 5. Vzhledem k tomu, že brána je nakonfigurovaná na jinou síť, Data 0 a Data 5 byly selhání, provoz cloudové projdou Data 1.
* Pokud povolenou podporu cloudu síťové rozhraní se nezdaří, pak jsou 3 opakování pomocí 30 druhý zpoždění pro připojení k rozhraní. Pokud selžou i všechny pokusy, provoz je směrován na další dostupný povolenou podporu cloudu rozhraní určenému do směrovací tabulky. Pokud všechny povolenou podporu cloudu síťová rozhraní selhání, pak zařízení převezme druhý kontroler (bez restartování v tomto případě).
* Pokud dojde k selhání virtuální IP adresu pro rozhraní sítě iSCSI povolený, nebudou 3 pokusy s prodlevou 2 sekundy. Toto chování má nechali stejné z předchozích verzí. Pokud selžou i všechna síťová rozhraní iSCSI, převzetí služeb kontroleru při dojde (připojí restartování).
* Výstrahu se také vyvolá zařízení StorSimple, když dojde k selhání virtuální IP adresy. Další informace najdete v části [upozornění Stručná referenční příručka](storsimple-8000-manage-alerts.md).
* Z hlediska opakovaných pokusů iSCSI bude mít přednost přes cloud.
  
    Vezměte v úvahu v následujícím příkladu: Má dvě síťová rozhraní povolená zařízení StorSimple, Data 0 a Data 1. Rozhraní data 0 má povolenou podporu cloudu že Data 1 je cloud určený pro obě a podporou iSCSI. Žádná další síťová rozhraní na tomto zařízení jsou povolené pro cloud nebo iSCSI.
  
    Pokud Data 1 selže, je uveden je poslední síťové rozhraní iSCSI, výsledkem bude převzetí služeb kontroleru při 1 dat na druhý kontroler.

### <a name="networking-best-practices"></a>Doporučené postupy sítě

Kromě výše uvedených požadavků sítě pro zajištění optimálního výkonu řešení StorSimple dodržujte následující osvědčené postupy:

* Zajistěte, aby zařízení StorSimple vyhrazené šířky pásma 40 MB/s (nebo více) vždy k dispozici. Tuto šířku pásma by se neměly sdílet (nebo přidělení by měl být zaručeno, že prostřednictvím zásad QoS) s ostatními aplikacemi.
* Zajistěte, aby za všech okolností je k dispozici síťové připojení k Internetu. Výskyt občasný, nebo nespolehlivé internetové připojení k zařízení, včetně jakýmkoli způsobem, bez připojení k Internetu bude mít za následek má nepodporovanou konfiguraci.
* Vyhrazené síťové rozhraní na zařízení pro přístup k iSCSI a cloudu izolujte přenosy iSCSI a cloudu. Další informace najdete v tématu Jak [změnit síťová rozhraní](storsimple-8000-modify-device-config.md#modify-network-interfaces) zařízení StorSimple.
* Nepoužívejte konfiguraci Protokol LACP (Link Aggregation ovládací prvek Protocol) pro síťová rozhraní. Toto je Nepodporovaná konfigurace.

## <a name="high-availability-requirements-for-storsimple"></a>Požadavky na vysokou dostupnost pro StorSimple

Hardwarová platforma, která je součástí řešení StorSimple se dostupnost a spolehlivost funkce, které poskytují základ pro vysoce dostupné a odolné úložiště infrastruktury ve vašem datovém centru. Existují však požadavky a osvědčené postupy, které by měly splňovat k zajištění dostupnosti vašeho řešení StorSimple. Před nasazením StorSimple, pečlivě si prostudujte následující požadavky a osvědčené postupy pro zařízení StorSimple a počítač připojený hostitel.

Další informace o sledování a údržbu hardwarové součásti zařízení StorSimple, přejděte na [použít službu Správce zařízení StorSimple pro monitorování hardwarových součástí a stav](storsimple-8000-monitor-hardware-status.md) a [StorSimple výměna hardwaru součástí](storsimple-8000-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Požadavky na vysokou dostupnost a postupy pro zařízení StorSimple

Přečtěte si následující informace pečlivě k zajištění vysoké dostupnosti vašeho zařízení StorSimple.

#### <a name="pcms"></a>PCMs

Zařízení StorSimple zahrnují za provozu, redundantní napájení, chlazení moduly (PCMs). Každý PCM má dostatečnou kapacitu k zajištění služeb pro celý skříně. K zajištění vysoké dostupnosti, musí být obě PCMs nainstalován.

* Vaše PCMs se připojte k různým zdrojům napájení pro zajištění dostupnosti, pokud se nezdaří zdroji napájení.
* Pokud se nezdaří PCM, požádat o výměnu okamžitě.
* Neúspěšné PCM odeberte pouze v případě, že máte nahrazení a je připraveno k jeho instalaci.
* Souběžně neodebírejte obou PCMs. Modul PCM zahrnuje modul záložní baterie. Odebírá se i PCMs způsobí vypnutí bez ochrany baterie a stav zařízení se neuloží. Další informace o baterie, přejděte na [udržovat modulu záložní baterie](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Moduly kontroleru

Zařízení StorSimple zahrnují moduly kontroleru redundantní, za provozu. Moduly kontroleru pracovat způsobem aktivní/pasivní vysokou dostupnost. V daném okamžiku jeden modul kontroleru je aktivní a poskytuje službu, zatímco ostatní řadiče modul je pasivní. Pasivní kontroler modul zapne a stane provozní, pokud modul aktivní kontroler selže nebo je odebrat. Každý kontroler modul nemá dostatečnou kapacitu k zajištění služeb pro celý skříně. Oba řadiče moduly musí být nainstalovaná a zajistit tak vysokou dostupnost.

* Ujistěte se, že oba řadiče moduly nainstalují za všech okolností.
* Pokud modul řadiče selže, požádat o náhradu okamžitě.
* Odebrání modulu kontroleru, který selhal, jenom Pokud jste nahrazení a je připraveno k jeho instalaci. Odebrání modulu pro delší dobu bude mít vliv na vzduchu a chlazení systému.
* Ujistěte se, že síťová připojení k modulům oba řadiče jsou identické a připojená síťová rozhraní mají konfiguraci služby stejné sítě.
* Pokud modul řadiče selže nebo je nutné vyměnit, ujistěte se, že jiné řadiče modul je v aktivním stavu před nahrazením modulu kontroleru, který selhal. Pokud chcete ověřit, že je aktivní kontroler, přejděte na [identifikace aktivního kontroleru na vašem zařízení](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Neodebírejte oba moduly řadiče ve stejnou dobu. Pokud probíhá převzetí služeb kontroleru při vypnutí modulu kontroler v pohotovostním režimu nebo ji odeberte z skříni.
* Po převzetí služeb kontroleru při počkejte alespoň pět minut před odebráním buď modul řadiče.

#### <a name="network-interfaces"></a>Síťová rozhraní

StorSimple zařízení řadič modulů každý se čtyřmi 1 Gigabit a dvě 10 gigabitová ethernetová síťová rozhraní.

* Ujistěte se, že síťová připojení k modulům oba řadiče jsou identické a síťová rozhraní, že kontroler rozhraní modulů připojeni k identických síťových nastavení.
* Pokud je to možné, nasaďte připojení k síti přes různé přepínače k zajištění dostupnosti služby selhání některé síťové zařízení.
* Při odpojování pouze nebo poslední zbývající rozhraní iSCSI povolený (pomocí IP adresy přiřazené), nejprve zakázat rozhraní a potom odpojte kabely. Pokud rozhraní je odpojen, poté způsobí aktivním řadiči – tím převzetí služeb při selhání na pasivním kontroleru. Pokud pasivní kontroler má také jeho odpovídající rozhraní odpojit, pak oba kontrolery se restartuje více než jednou před spuštěním na jeden kontroler.
* Aspoň dvě rozhraní datového připojení k síti z každého modulu kontroleru.
* Pokud jste povolili dvě rozhraní 10 GbE, nasadit napříč různým přepínačům.
* Pokud je to možné, ujistěte se, že servery budou tolerovat chyby odkaz, sítě nebo selhání rozhraní pomocí funkce MPIO na serverech.

Další informace o zařízení pro vysokou dostupnost a výkon sítě, přejděte na [instalaci zařízení StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) nebo [instalaci zařízení StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>Vlastnosti jednotek SSD a pevných disků

Zařízení StorSimple obsahovat disky SSD (Solid-State Drive) a pevných disků (HDD), které jsou chráněné pomocí zrcadlit mezery. Použití zrcadlených prostorech zajišťuje, že zařízení je schopné tolerovat selhání jednoho nebo více disků SSD nebo HDD.

* Ujistěte se, že jsou nainstalované všechny moduly SSD a HDD.
* Pokud SSD nebo pevný disk selže, žádost o náhradu okamžitě.
* Pokud SSD nebo pevný disk selže nebo vyžaduje nahrazení, ujistěte se, že odeberete jenom SSD nebo pevný disk, který vyžaduje nahrazení.
* Neodebírejte více než jeden SSD nebo pevný disk ze systému v libovolném bodě v čase.
  Selhání 2 nebo více disků určitého typu (HDD, SSD) nebo po sobě jdoucích selhání v rámci krátkého formátu času může dojít ke ztrátě dat systému selhání a možnosti. Pokud k tomu dojde, [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md) žádostí o pomoc.
* Při nahrazení, monitorovat **sdílené komponenty** v **stav hardwaru** okno jednotek SSD a HDD. Stav zelená značka zaškrtnutí označuje, že disky jsou v pořádku nebo OK, zatímco bodu červený vykřičník označuje neúspěšné SSD nebo pevný disk.
* Doporučujeme, abyste nakonfigurovali cloudové snímky pro všechny svazky, které potřebujete k ochraně v případě selhání systému.

#### <a name="ebod-enclosure"></a>Skříň EBOD

Model zařízení StorSimple 8600 obsahuje přílohu rozšířené Bunch disky (EBOD) kromě primární skříň. EBOD obsahuje řadiče EBOD a pevných disků (HDD), které jsou chráněné pomocí zrcadlit mezery. Použití zrcadlených prostorech zajišťuje, že zařízení je schopné tolerovat selhání jednoho nebo více pevných disků. Skříň EBOD je připojen k primární skříň prostřednictvím redundantní SAS kabely.

* Ujistěte se, že oba moduly řadiče EBOD skříň SAS kabely i všechny pevné disky jsou nainstalovány za všech okolností.
* Pokud modul řadiče EBOD skříň selže, žádost o náhradu okamžitě.
* Pokud se modul řadiče EBOD skříň nezdaří, ujistěte se, že ostatní moduly kontroleru je aktivní, před nahrazením modulu se nezdařilo. Pokud chcete ověřit, že je aktivní kontroler, přejděte na [identifikace aktivního kontroleru na vašem zařízení](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Během nahrazení modulu řadiče EBOD neustále monitorovat stav komponenty ve službě Správce zařízení StorSimple díky přístupu do **monitorování** > **stav hardwaru**.
* Pokud kabelu SAS selže nebo vyžaduje nahrazení (Microsoft Support by měl zapojí aby toto rozhodnutí), ujistěte se, že odeberete pouze kabel SAS, který vyžaduje nahrazení.
* Neodebírejte současně i SAS kabely ze systému v libovolném bodě v čase.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Doporučení k vysoké dostupnosti pro hostitelské počítače

Pečlivě si prostudujte tyto osvědčené postupy k zajištění vysoké dostupnosti hostitelů připojených k zařízení StorSimple.

* Konfigurace StorSimple s [konfigurace clusteru dva uzly souborového serveru][1]. Odebráním jediné body selhání a sestavování v redundance na straně hostitele, bude celé řešení s vysokou dostupností.
* Použijte nepřetržitě dostupné složky (CA) k dispozici v systému Windows Server 2012 (protokolu SMB 3.0) pro zajištění vysoké dostupnosti během převzetí služeb při selhání řadičů úložiště. Další informace o konfiguraci clusterů souborových serverů a nepřetržitě dostupné sdílené složky v systému Windows Server 2012, najdete to [Videoukázka](https://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>Další postup

* [Další informace o omezení systému StorSimple](storsimple-8000-limits.md).
* [Informace o nasazení řešení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
