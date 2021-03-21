---
title: Požadavky na systém řady StorSimple 8000 | Microsoft Docs
description: Popisuje požadavky na software, sítě a vysokou dostupnost a osvědčené postupy pro řešení Microsoft Azure StorSimple.
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
ms.date: 02/11/2021
ms.author: alkohli
ms.openlocfilehash: fa7616a740e8246fa08e950494428095f41ee404
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382850"
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>StorSimple 8000 series software, vysoká dostupnost a síťové požadavky

## <a name="overview"></a>Přehled

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Vítá vás Microsoft Azure StorSimple. Tento článek popisuje důležité požadavky na systém a osvědčené postupy pro zařízení StorSimple a pro klienty úložiště, kteří přistupují k zařízení. Doporučujeme, abyste pečlivě prostudovali informace před nasazením systému StorSimple a pak se na něj v průběhu nasazení a následné operace znovu odkazovali.

Požadavky na systém zahrnují:

* **Požadavky na software pro klienty úložiště** – popisuje podporované operační systémy a všechny další požadavky pro tyto operační systémy.
* **Požadavky na síť pro zařízení StorSimple** – poskytuje informace o portech, které je potřeba v bráně firewall otevřít, aby se povolily přenosy z iSCSI, cloudu nebo správy.
* **Požadavky na vysokou dostupnost pro StorSimple** – popisuje požadavky na vysokou dostupnost a osvědčené postupy pro vaše zařízení StorSimple a hostitelský počítač.

## <a name="software-requirements-for-storage-clients"></a>Požadavky na software pro klienty úložiště

Následující požadavky na software jsou pro klienty úložiště, kteří přistupují k vašemu zařízení StorSimple.

| Podporované operační systémy | Vyžaduje se verze | Další požadavky/poznámky |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2, 2016 |StorSimple svazky iSCSI se podporují jenom na následujících typech disků Windows:<ul><li>Jednoduchý svazek na základním disku</li><li>Jednoduchý a zrcadlený svazek na dynamickém disku</li></ul>Podporují se jenom ty iniciátory iSCSI softwaru, které jsou v operačním systému nativně přítomné. Nejsou podporovány hardwarové iniciátory iSCSI.<br></br>Pokud používáte svazek iSCSI StorSimple, podporují se dynamické zřizování a funkce ODX pro Windows Server 2012 a 2016.<br><br>StorSimple může vytvářet dynamicky zřízené a plně zřízené svazky. Nemůže vytvořit částečně zřízené svazky.<br><br>Přeformátování dynamicky zřízeného svazku může trvat dlouhou dobu. Místo přeformátování proto doporučujeme odstranit tento svazek a potom vytvořit nový. Pokud ale přesto chcete svazek přeformátovat:<ul><li>Před vlastním přeformátováním spusťte následující příkaz, abyste se vyhnuli prodlevám souvisejícím s recyklací místa: . <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Jakmile formátování skončí, použijte následující příkaz, který recyklaci místa znovu povolí: .<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Použijte Windows Server 2012 hotfix, jak je popsáno v [článku KB 2878635](https://support.microsoft.com/kb/2870270) pro počítač se systémem Windows Server.</li></ul></li></ul></ul> Pokud konfigurujete StorSimple Snapshot Manager nebo adaptér StorSimple pro službu SharePoint, pokračujte na [požadavky na software u volitelných součástí](#software-requirements-for-optional-components). <br> Pokud klient Windows serveru používá protokol SMB pro přístup k zařízení StorSimple, přejděte k části [ladění výkonu pro souborové servery SMB](/windows-server/administration/performance-tuning/role/file-server/smb-file-server) , kde najdete pokyny ke zvýšení paralelního zpracování.|
| VMware ESX |5,5 a 6,0 |Podporováno u VMware vSphere jako klienta iSCSI. Funkce VAAI-Block je podporovaná u VMware vSphere na zařízeních StorSimple. |
| Linux RHEL/CentOS |5, 6 a 7 |Podpora klientů iSCSI pro Linux s verzemi verze 5, 6 a 7 pro Linux. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> IBM AIX se v tuto chvíli v StorSimple nepodporuje.


## <a name="software-requirements-for-optional-components"></a>Požadavky na software pro volitelné součásti

Následující požadavky na software jsou pro volitelné součásti StorSimple (StorSimple Snapshot Manager a adaptér StorSimple pro SharePoint).

| Součást | Platforma hostitele | Další požadavky/poznámky |
| --- | --- | --- |
| StorSimple Snapshot Manager |Windows Server 2008 R2 SP1, 2012, 2012 R2 |Pro zálohování a obnovení zrcadlených dynamických disků a pro všechny zálohy konzistentní s aplikacemi se vyžaduje použití Snapshot Manager StorSimple na Windows serveru.<br> StorSimple Snapshot Manager se podporuje jenom v systému Windows Server 2008 R2 SP1 (64), Windows Server 2012 R2 a Windows Server 2012.<ul><li>Pokud používáte systém Windows Server 2012, je nutné před instalací StorSimple Snapshot Manager nainstalovat rozhraní .NET 3.5 – 4.5.</li><li>Pokud používáte systém Windows Server 2008 R2 SP1, je nutné před instalací StorSimple Snapshot Manager nainstalovat rozhraní Windows Management Framework 3,0.</li></ul> |
| StorSimple Adapter pro SharePoint |Windows Server 2008 R2 SP1, 2012, 2012 R2 |<ul><li>Adaptér StorSimple pro SharePoint je podporován pouze na SharePointu 2010 a SharePoint 2013.</li><li>KÓD RBS vyžaduje edici SQL Server Enterprise, verze 2008 R2 nebo 2012.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>Požadavky na síť pro zařízení StorSimple

Zařízení StorSimple je uzamčené. Porty ale musí být v bráně firewall otevřené, aby bylo možné provozovat přenosy z iSCSI, cloudu a správy. Následující tabulka uvádí porty, které je třeba otevřít v bráně firewall. V této tabulce se *v* nebo *příchozí* odkazuje na směr, ze kterého příchozí klient žádá o přístup k vašemu zařízení. *Výstupní* nebo *odchozí* odkazuje na směr, ve kterém vaše zařízení StorSimple odesílá data externě, mimo nasazení: například odchozí na Internet.

| Číslo portu.<sup>1, 2</sup> | V nebo ven | Rozsah portů | Vyžadováno | Poznámky |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |Out |Síť WAN |No |<ul><li>Odchozí port se používá pro přístup k Internetu k načtení aktualizací.</li><li>Odchozí webový proxy server je uživatelsky konfigurovatelné.</li><li>Aby se mohly aktualizovat systémové aktualizace, musí být tento port taky otevřený pro pevné IP adresy řadiče.</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |Out |Síť WAN |Yes |<ul><li>Odchozí port se používá pro přístup k datům v cloudu.</li><li>Odchozí webový proxy server je uživatelsky konfigurovatelné.</li><li>Aby se mohly aktualizovat systémové aktualizace, musí být tento port taky otevřený pro pevné IP adresy řadiče.</li><li>Tento port se používá také na obou řadičích pro uvolňování paměti.</li></ul> |
| UDP 53 (DNS) |Out |Síť WAN |V některých případech; viz poznámky. |Tento port je vyžadován pouze v případě, že používáte internetový server DNS. |
| UDP 123 (NTP) |Out |Síť WAN |V některých případech; viz poznámky. |Tento port je vyžadován pouze v případě, že používáte internetový server NTP. |
| TCP 9354 |Out |Síť WAN |Yes |Odchozí port používá zařízení StorSimple ke komunikaci se službou StorSimple Správce zařízení. |
| 3260 (iSCSI) |V |Síť LAN |No |Tento port se používá pro přístup k datům přes iSCSI. |
| 5985 |V |Síť LAN |No |Port pro příchozí spojení StorSimple Snapshot Manager slouží ke komunikaci se zařízením StorSimple.<br>Tento port se používá také při vzdáleném připojení k Windows PowerShell pro StorSimple přes protokol HTTP. |
| 5986 |V |Síť LAN |No |Tento port se používá, když se vzdáleně připojujete k Windows PowerShell pro StorSimple přes protokol HTTPS. |

<sup>1</sup> na veřejném Internetu není třeba otevřít žádné příchozí porty.

<sup>2</sup> Pokud konfigurace brány přenáší několik portů, určí se pořadí odchozího přenosu směrování na základě pořadí směrování portů popsaného v tématu [směrování portů](#routing-metric)níže.

<sup>3</sup> pevné IP adresy řadiče na zařízení StorSimple musí být směrovatelné a schopné se připojit k Internetu přímo nebo prostřednictvím nakonfigurovaného webového proxy serveru. Pevné IP adresy se používají pro obsluhu aktualizací zařízení a pro uvolňování paměti. Pokud se řadiče zařízení nemůžou připojit k internetu přes pevné IP adresy, nebudete moct aktualizovat zařízení StorSimple a uvolňování paměti nebude správně fungovat.

> [!IMPORTANT]
> Ujistěte se, že brána firewall nemění ani nešifruje žádný přenos TLS mezi zařízením StorSimple a Azure.


### <a name="url-patterns-for-firewall-rules"></a>Vzory adres URL pro pravidla brány firewall

Správci sítě mohou často konfigurovat Rozšířená pravidla brány firewall na základě vzorů adres URL pro filtrování příchozího a odchozího provozu. Vaše zařízení StorSimple a Služba StorSimple Správce zařízení jsou závislé na dalších aplikacích Microsoftu, jako jsou Azure Service Bus, Azure Active Directory Access Control, účty úložiště a Microsoft Update servery. Ke konfiguraci pravidel brány firewall lze použít vzory adres URL přidružené k těmto aplikacím. Je důležité pochopit, že se vzory adres URL přidružené k těmto aplikacím můžou změnit. To zase vyžaduje, aby správce sítě sledoval a aktualizoval pravidla brány firewall pro StorSimple jako a v případě potřeby.

Doporučujeme, abyste nastavili pravidla brány firewall pro odchozí přenosy na základě pevných IP adres StorSimple, a to ve většině případů. Pomocí níže uvedených informací ale můžete nastavit Rozšířená pravidla brány firewall, která jsou potřeba k vytváření zabezpečených prostředí.

> [!NOTE]
> Zařízení (zdrojové) IP adresy musí být vždycky nastavená na všechna povolená síťová rozhraní. Cílové IP adresy by měly být nastavené na [rozsahy IP adres datacentra Azure](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


#### <a name="url-patterns-for-azure-portal"></a>Vzory adres URL pro Azure Portal

| Vzor adresy URL | Součást/funkce | IP adresy zařízení |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |Služba Správce zařízení StorSimple<br>Access Control Service<br>Azure Service Bus<br>Ověřovací služba |Síťová rozhraní s povoleným cloudem |
| `https://*.backup.windowsazure.com` |Registrace zařízení |Jenom DATA 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Odvolání certifikátu |Síťová rozhraní s povoleným cloudem |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Účty a monitorování Azure Storage |Síťová rozhraní s povoleným cloudem |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update servery<br> |Pouze pevné IP adresy řadiče |
| `http://*.deploy.akamaitechnologies.com` |CDN Akamai |Pouze pevné IP adresy řadiče |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Balíček pro podporu |Síťová rozhraní s povoleným cloudem |

#### <a name="url-patterns-for-azure-government-portal"></a>Vzory adres URL pro portál Azure Government

| Vzor adresy URL | Součást/funkce | IP adresy zařízení |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login.microsoftonline.us` |Služba Správce zařízení StorSimple<br>Access Control Service<br>Azure Service Bus<br>Ověřovací služba |Síťová rozhraní s povoleným cloudem |
| `https://*.backup.windowsazure.us` |Registrace zařízení |Jenom DATA 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Odvolání certifikátu |Síťová rozhraní s povoleným cloudem |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Účty a monitorování Azure Storage |Síťová rozhraní s povoleným cloudem |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update servery<br> |Pouze pevné IP adresy řadiče |
| `http://*.deploy.akamaitechnologies.com` |CDN Akamai |Pouze pevné IP adresy řadiče |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Balíček pro podporu |Síťová rozhraní s povoleným cloudem |

### <a name="routing-metric"></a>Metrika směrování

Metrika směrování je přidružená k rozhraním a bráně, která směruje data do zadaných sítí. Metriky směrování používá směrovací protokol k výpočtu nejlepší cesty k danému cíli, pokud se zjistí, že existuje více cest ke stejnému cíli. Čím nižší je metrika směrování, tím vyšší je priorita.

Pokud je v kontextu StorSimple více síťových rozhraní a bran pro přenos provozu, budou metriky směrování odesílány do hry, aby určily relativní pořadí, ve kterém se rozhraní budou používat. Metriky směrování nemůže změnit uživatel. `Get-HcsRoutingTable`K vytištění směrovací tabulky (a metrik) na zařízení StorSimple ale můžete použít rutinu. Další informace o rutině Get-HcsRoutingTable při [řešení potíží s nasazením StorSimple](./storsimple-8000-troubleshoot-deployment.md)

Algoritmus metriky směrování použitý pro aktualizaci 2 a novější verze se dá vysvětlit následujícím způsobem.

* K síťovým rozhraním byla přiřazena sada předem určených hodnot.
* Vezměte níže uvedenou ukázkovou tabulku s hodnotami přiřazenými různým síťovým rozhraním v případě, že jsou povolené cloudové nebo cloudové zakázané, ale s nakonfigurovanou bránou. Všimněte si, že zde přiřazené hodnoty jsou pouze příklady hodnot.

    | Síťové rozhraní | Cloud – povoleno | Cloud – zakázaný s bránou |
    |-----|---------------|---------------------------|
    | Data 0  | 1            | -                        |
    | Data 1  | 2            | 20                       |
    | Data 2  | 3            | 30                       |
    | Data 3  | 4            | 40                       |
    | Data 4  | 5            | 50                       |
    | Data 5  | 6            | 60                       |


* Pořadí, ve kterém se cloudové přenosy budou směrovat přes síťová rozhraní:
  
    *Data 0 > data 1 > datum 2 > data 3 > data 4 > data 5*
  
    To může být vysvětleno v následujícím příkladu.
  
    Vezměte v úvahu zařízení StorSimple se dvěma síťovými rozhraními s podporou cloudu, daty 0 a daty 5. Data 1 až data 4 jsou zakázaná v cloudu, ale mají nakonfigurovanou bránu. Pořadí, ve kterém budou přenosy směrovány pro toto zařízení, bude:
  
    *Data 0 (1) > data 5 (6) > data 1 (20) > data 2 (30) > data 3 (40) > data 4 (50)*
  
    *Čísla v závorkách označují odpovídající metriky směrování.*
  
    Pokud data 0 selže, cloudový provoz se bude směrovat přes data 5. Vzhledem k tomu, že je brána nakonfigurovaná na všech ostatních sítích, v případě, že data 0 i data 5 selžou, přenos dat do cloudu prochází daty 1.
* Pokud se síťové rozhraní s povoleným cloudem nezdařilo, pak se pro připojení k rozhraní dokončí tři pokusy s prodlevou 30 sekund. Pokud se všechny opakované pokusy nezdaří, provoz se směruje do dalšího dostupného cloudového rozhraní, které je určeno směrovací tabulkou. Pokud všechna síťová rozhraní s povoleným cloudem selžou, zařízení převezme služby při selhání na jiný kontroler (v tomto případě se nerestartuje).
* Pokud u síťového rozhraní s podporou iSCSI dojde k selhání virtuální IP adresy, dojde k 3 opakovaným pokusům o zpoždění 2 sekund. Toto chování se nechalio stejně jako v předchozích verzích. Pokud všechna síťová rozhraní iSCSI selžou, dojde k převzetí služeb při selhání řadiče (doplněné restartováním).
* Když dojde k selhání virtuální IP adresy, na zařízení StorSimple se taky vygeneruje výstraha. Další informace najdete v [rychlém odkazu na výstrahu](storsimple-8000-manage-alerts.md).
* V rámci opakovaných pokusů bude mít iSCSI přednost před cloudem.
  
    Vezměte v úvahu následující příklad: zařízení StorSimple má dvě síťová rozhraní povolena, data 0 a data 1. Data 0 jsou zapnutá v cloudu, zatímco data 1 jsou Cloud i s povoleným iSCSI. V cloudu nebo iSCSI nejsou povolena žádná další síťová rozhraní v tomto zařízení.
  
    Pokud se data 1 nezdaří, protože se jedná o poslední síťové rozhraní iSCSI, způsobí to, že dojde k převzetí služeb při selhání řadiče na data 1 na druhém řadiči.

### <a name="networking-best-practices"></a>Osvědčené postupy pro sítě

Kromě výše uvedených požadavků na síť pro optimální výkon řešení StorSimple prosím dodržujte následující osvědčené postupy:

* Ujistěte se, že vaše zařízení StorSimple má po celou dobu vyhrazenou šířku pásma 40 MB/s (nebo více). Tato šířka pásma by se neměla sdílet (nebo by mělo být přidělování zaručeno prostřednictvím použití zásad QoS) pro všechny ostatní aplikace.
* Ujistěte se, že síťové připojení k Internetu je dostupné kdykoli. Občasná nebo nespolehlivá připojení k Internetu zařízením, včetně žádného připojení k Internetu, budou mít za následek nepodporovanou konfiguraci.
* Izolujte provoz iSCSI a cloudu tím, že ve svém zařízení máte vyhrazená síťová rozhraní pro iSCSI a cloudový přístup. Další informace najdete v tématu Postup [Úpravy síťových rozhraní](storsimple-8000-modify-device-config.md#modify-network-interfaces) na zařízení StorSimple.
* Nepoužívejte pro vaše síťová rozhraní konfiguraci LACP (Link Control Protocol). Tato konfigurace se nepodporuje.

## <a name="high-availability-requirements-for-storsimple"></a>Požadavky na vysokou dostupnost pro StorSimple

Hardwarová platforma, která je součástí řešení StorSimple, má funkce dostupnosti a spolehlivosti, které poskytují základ pro vysoce dostupnou a odolnou infrastrukturu úložiště ve vašem datovém centru. Existují však požadavky a osvědčené postupy, které byste měli dodržovat, abyste zajistili dostupnost vašeho řešení StorSimple. Před nasazením StorSimple pečlivě zkontrolujte následující požadavky a osvědčené postupy pro zařízení StorSimple a připojené hostitelské počítače.

Další informace o monitorování a údržbě hardwarových komponent zařízení StorSimple najdete [v části použití služby StorSimple Správce zařízení k monitorování hardwarových komponent a stavu](storsimple-8000-monitor-hardware-status.md) [StorSimple hardwarové](storsimple-8000-hardware-component-replacement.md)součásti.

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Požadavky a postupy vysoké dostupnosti pro zařízení StorSimple

Pečlivě si přečtěte následující informace, abyste zajistili vysokou dostupnost zařízení StorSimple.

#### <a name="pcms"></a>PCMs

Mezi zařízení StorSimple patří redundantní, Hot-swapové moduly napájení a chlazení (PCMs). Každý PCM má dostatečnou kapacitu pro poskytování služby pro celý skříň. Aby se zajistila vysoká dostupnost, musí být nainstalovaná obě PCMs.

* Připojte své PCMs k různým zdrojům napájení, abyste zajistili dostupnost, pokud se zdroj napájení nepovede.
* Pokud se PCM nezdařil, vyžádejte ihned náhradu.
* Odebrání neúspěšného PCM jenom v případě, že máte náhradu a máte připravenou ji nainstalovat.
* Neodstraňujte oba současně PCMs současně. Modul PCM obsahuje modul pro záložní baterii. Odebrání obou PCMs bude mít za následek vypnutí bez ochrany baterií a stav zařízení nebude uloženo. Další informace o baterii získáte tak, že přejdete na [udržovat modul záložní baterie](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Moduly kontroleru

Zařízení StorSimple obsahují nadbytečné a horké moduly odkládacího zařízení. Moduly řadiče pracují aktivním/pasivním způsobem. V jednom okamžiku je aktivní jeden modul kontroleru a poskytuje službu, zatímco druhý modul kontroleru je pasivní. Modul pasivního kontroleru je zapnutý a přestane fungovat, pokud dojde k chybě aktivního modulu řadiče nebo odebrání. Každý modul kontroleru má dostatečnou kapacitu pro poskytování služby pro celý skříň. Aby se zajistila vysoká dostupnost, musí se nainstalovat oba moduly řadiče.

* Ujistěte se, že jsou oba moduly řadiče nainstalovány současně.
* Pokud dojde k chybě modulu kontroleru, vyžádejte si okamžitě náhradu.
* Odeberte modul neúspěšného kontroleru pouze v případě, že máte náhradu a jste připraveni ho nainstalovat. Odebráním modulu pro rozšířené tečky se ovlivní tok a tím se dokončí chlazení systému.
* Ujistěte se, že síťová připojení k oběma modulům kontroléru jsou shodná a že připojená síťová rozhraní mají stejnou konfiguraci sítě.
* Pokud modul kontroléru selhává nebo vyžaduje náhradu, ujistěte se, že je modul druhého kontroleru v aktivním stavu před nahrazením modulu neúspěšného kontroléru. Pokud chcete ověřit, jestli je kontroler aktivní, použijte k [identifikaci aktivního řadiče na zařízení](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Neodstraňujte současně oba moduly řadiče. Pokud probíhá převzetí služeb při selhání, nevypněte modul pohotovostního kontroleru ani ho odeberte ze skříně.
* Po převzetí služeb při selhání řadiče počkejte aspoň pět minut, než odeberte kterýkoli modul kontroleru.

#### <a name="network-interfaces"></a>Síťová rozhraní

StorSimple moduly řadiče zařízení mají 4 1 Gigabit a 2 10 síťových rozhraní Gigabit Ethernet.

* Ujistěte se, že síťová připojení k oběma modulům kontroleru jsou shodná a že síťová rozhraní, ke kterým jsou připojena rozhraní modulů řadiče, mají stejnou konfiguraci sítě.
* Pokud je to možné, nasaďte síťová připojení mezi různými přepínači, aby se zajistila dostupnost služby v případě selhání síťového zařízení.
* Pokud odpojíte pouze nebo poslední zbývající rozhraní podporující technologii iSCSI (s přiřazenými IP adresami), zakažte nejprve rozhraní a potom kabely odpojte. Pokud se nejdřív odpojí rozhraní, pak dojde k převzetí služeb aktivního řadiče při selhání v pasivním kontroleru. Pokud má pasivní kontroler taky svá odpovídající rozhraní odpojená, pak se oba řadiče restartují několikrát, než se na jednom řadiči vyrovnává.
* Připojte alespoň dvě datová rozhraní k síti z každého modulu řadiče.
* Pokud jste povolili rozhraní 2 10 GbE, nasaďte je napříč různými přepínači.
* Pokud je to možné, použijte funkci MPIO na serverech, abyste zajistili, že servery můžou tolerovat selhání propojení, sítě nebo rozhraní.

Další informace o tom, jak vaše zařízení má vysokou dostupnost a výkon, najdete v článku [instalace zařízení StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) nebo [instalace zařízení StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>SSD a HDD

Zařízení StorSimple obsahují disky Solid State (SSD) a jednotky pevného disku (HDD), které jsou chráněné pomocí zrcadlených prostorů. Použití zrcadlených prostorů zajišťuje, že zařízení dokáže tolerovat selhání jednoho nebo více SSD nebo HDD.

* Ujistěte se, že jsou nainstalované všechny moduly SSD a HDD.
* Pokud jednotka SSD nebo HDD selhává, požádejte o náhradu hned.
* Pokud jednotka SSD nebo HDD selhává nebo vyžaduje náhradu, ujistěte se, že jste odebrali jenom jednotku SSD nebo HDD, která vyžaduje nahrazení.
* Neodstraňujte více než jednu jednotku SSD nebo HDD ze systému v jakémkoli bodě v čase.
  Selhání při 2 nebo více discích určitého typu (HDD, SSD) nebo po sobě jdoucích selhání v krátkém časovém rámci může způsobit selhání systému a potenciální ztrátu dat. Pokud k tomu dojde, [požádejte](storsimple-8000-contact-microsoft-support.md) o pomoc podpora Microsoftu.
* Během nahrazování monitorujte **sdílené komponenty** v okně **stav hardwaru** pro jednotky v SSD a HDD. Zelený stav kontroly znamená, že jsou disky v pořádku nebo v pořádku, zatímco červený vykřičník indikuje, že se nezdařila jednotka SSD nebo HDD.
* Doporučujeme, abyste nakonfigurovali cloudové snímky pro všechny svazky, které potřebujete chránit v případě selhání systému.

#### <a name="ebod-enclosure"></a>EBOD skříň

StorSimple Device model 8600 obsahuje kromě primárního skříňku i skříň EBOD (Extended of disks). EBOD obsahuje řadiče EBOD a jednotky pevného disku (HDD), které jsou chráněny pomocí zrcadlených prostorů. Používání zrcadlených prostorů zajišťuje, že zařízení dokáže tolerovat selhání jednoho nebo více HDD. EBOD skříň je připojená k primární skříni prostřednictvím redundantních kabelů SAS.

* Zajistěte, aby byly současně nainstalovány oba moduly EBOD skříně, jak kabely SAS, tak i všechny jednotky pevného disku.
* Pokud dojde k chybě modulu EBOD skříni, vyžádejte ihned náhradu.
* Pokud dojde k selhání modulu EBOD skříni, ujistěte se, že je modul druhého kontroleru aktivní, než nahradíte neúspěšný modul. Pokud chcete ověřit, jestli je kontroler aktivní, použijte k [identifikaci aktivního řadiče na zařízení](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Při nahrazení modulu eBOD Controller nepřetržitě Sledujte stav součásti ve službě StorSimple Správce zařízení, a to tak, že se přihlásíte ke **sledování**  >  **stavu hardwaru**.
* Pokud kabel SAS dojde k chybě nebo pokud vyžaduje nahrazení (podpora Microsoftu by se mělo zapojit do takového rozhodnutí), nezapomeňte odebrat jenom kabel SAS, který vyžaduje nahrazení.
* Neprovádějte souběžné odebrání obou kabelů SAS ze systému v libovolném časovém okamžiku.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Doporučení pro vysokou dostupnost vašich hostitelských počítačů

Pečlivě si prostudujte tyto osvědčené postupy, abyste zajistili vysokou dostupnost hostitelů připojených k vašemu zařízení StorSimple.

* Nakonfigurujte StorSimple s [konfiguracemi clusteru souborových serverů se dvěma uzly][1]. Když odeberete jednotlivé body selhání a sestavíte redundanci na straně hostitele, bude celé řešení vysoce dostupné.
* Používejte nepřetržitě dostupné sdílené složky (CA) dostupné s Windows Serverem 2012 (SMB 3,0) pro zajištění vysoké dostupnosti při převzetí služeb při selhání řadičů úložiště. Další informace o tom, jak nakonfigurovat clustery souborových serverů a nepřetržitě dostupné sdílené složky s Windows Serverem 2012, najdete v této [ukázce videa](https://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>Další kroky

* [Přečtěte si o omezeních systému StorSimple](storsimple-8000-limits.md).
* [Přečtěte si, jak nasadit řešení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731844(v=ws.10)