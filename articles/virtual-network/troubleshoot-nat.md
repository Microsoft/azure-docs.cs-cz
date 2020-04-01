---
title: Poradce při potížích s připojením virtuální sítě Azure
titleSuffix: Azure Virtual Network
description: Řešení problémů s překladem síťových překladů virtuálních sítí
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to troubleshoot Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/30/2020
ms.author: allensu
ms.openlocfilehash: c012a8d83761b88cc59b62d11fd3d5542ca7f7a1
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396094"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity"></a>Poradce při potížích s připojením virtuální sítě Azure

Tento článek pomáhá správcům diagnostikovat a řešit problémy s připojením při použití překladu adres VIRTUÁLNÍ SÍTĚ.

## <a name="problems"></a>Problémy

* [Vyčerpání SNAT](#snat-exhaustion)
* [Příkaz ping ICMP selhává](#icmp-ping-is-failing)
* [Selhání připojení](#connectivity-failures)
* [Koexistence IPv6](#ipv6-coexistence)

Chcete-li tyto problémy vyřešit, postupujte podle pokynů v následující části.

## <a name="resolution"></a>Řešení

### <a name="snat-exhaustion"></a>Vyčerpání SNAT

Jeden [prostředek brány NAT](nat-gateway-resource.md) podporuje od 64 000 do 1 milionu souběžných toků.  Každá IP adresa poskytuje 64 000 portů SNAT do dostupných zásob. Na prostředek brány NAT můžete použít až 16 IP adres.  Mechanismus SNAT je [zde](nat-gateway-resource.md#source-network-address-translation) popsán podrobněji.

Často hlavní příčinou vyčerpání SNAT je anti-vzor pro způsob, jakým je odchozí připojení navázáno, spravováno nebo konfigurovatelné časovače změněny z jejich výchozí hodnoty.  Pečlivě si prostudujte tuto část.

#### <a name="steps"></a>Kroky

1. Zkontrolujte, zda jste změnili výchozí časový limit nečinnosti na hodnotu vyšší než 4 minuty.
2. Zjistěte, jak vaše aplikace vytváří odchozí připojení (například revize kódu nebo zachycení paketů). 
3. Zjistěte, zda je tato aktivita očekávané chování nebo zda se aplikace chová nesprávně.  Pomocí [metrik](nat-metrics.md) v Azure Monitoru doložit vaše zjištění. Pro metriku Připojení SNAT použijte kategorii "Nezdařilo se".
4. Vyhodnoťte, zda jsou dodrženy příslušné vzory.
5. Vyhodnoťte, zda by mělo být vyčerpání portu SNAT zmírněno dalšími IP adresami přiřazenými prostředku brány NAT.

#### <a name="design-patterns"></a>Způsoby návrhu

Vždy využijte opětovné použití připojení a sdružování připojení, kdykoli je to možné.  Tyto vzory zabrání problémům vyčerpání prostředků a za následek předvídatelné chování. Primitiva pro tyto vzory lze nalézt v mnoha vývojových knihoven a rámců.

_**Řešení:**_ Používejte vhodné vzory a osvědčené postupy

- Prostředky brány NAT mají výchozí časový limit nečinnosti protokolu TCP 4 minuty.  Pokud se toto nastavení změní na vyšší hodnotu, nat bude držet toky déle a může způsobit [zbytečný tlak na zásoby portů SNAT](nat-gateway-resource.md#timers).
- Atomické požadavky (jeden požadavek na připojení) jsou špatná volba návrhu. Takové anti-vzor omezuje měřítko, snižuje výkon a snižuje spolehlivost. Místo toho znovu použijte připojení HTTP/S ke snížení počtu připojení a přidružených portů SNAT. Škálování aplikace se zvýší a výkon se zlepší díky snížení nákladů na handshake, režii a kryptografické operace při použití TLS.
- Služba DNS může zavést mnoho jednotlivých toků na svazku, pokud klient neprovádí ukládání výsledků překladačů DNS do mezipaměti. Použijte ukládání do mezipaměti.
- Toky UDP (například vyhledávání DNS) přidělují porty SNAT po dobu trvání časového limitu nečinnosti. Čím delší časový limit nečinnosti, tím vyšší je tlak na porty SNAT. Použijte krátký časový limit nečinnosti (například 4 minuty).
- Pomocí fondů připojení můžete utvářet svazek připojení.
- Nikdy tiše opustit tok TCP a spoléhat se na časovače TCP vyčistit tok. Pokud nechcete nechat TCP explicitně zavřít připojení, stav zůstane přidělena na zprostředkující systémy a koncové body a znepřístupní porty SNAT pro jiná připojení. To může vyvolat selhání aplikace a vyčerpání SNAT. 
- Neměňte hodnoty časovače na úrovni operačního serveru TCP, aniž byste znali dopad. Zatímco zásobník TCP se obnoví, výkon aplikace může být negativně ovlivněn, když koncové body připojení mají neodpovídající očekávání. Touha změnit časovače je obvykle známkou základního problému návrhu. Projděte si následující doporučení.

Často krát SNAT vyčerpání může být také zesílen s jinými anti-vzory v základní aplikaci. Projděte si tyto další vzory a doporučené postupy, abyste zlepšili rozsah a spolehlivost vaší služby.

- Prozkoumejte dopad snížení [časového limitu nečinnosti protokolu TCP](nat-gateway-resource.md#timers) na nižší hodnoty, včetně výchozího časového limitu nečinnosti 4 minuty, abyste dříve uvolnili zásoby portů SNAT.
- Zvažte [asynchronní dotazování vzory](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply) pro dlouhotrvající operace uvolnit prostředky připojení pro jiné operace.
- Toky s dlouhou životností (například znovu použitá připojení TCP) by měly používat tcp keepalives nebo udržovaty vrstvy aplikace, aby se zabránilo vypršení časového limitu zprostředkujících systémů. Zvýšení časového limitu nečinnosti je poslední možnost a nemusí vyřešit hlavní příčinu. Dlouhý časový limit může způsobit selhání nízké sazby při vypršení časového limitu a zavést zpoždění a zbytečné selhání.
- Řádné opakování vzorky by měly být [použity,](https://docs.microsoft.com/azure/architecture/patterns/retry) aby se zabránilo agresivní opakování/shluky během přechodné selhání nebo zotavení po selhání.
Vytvoření nového připojení TCP pro každou operaci PROTOKOLU HTTP (označované také jako "atomová připojení") je anti-pattern.  Atomic connections zabrání vaší aplikaci škálování dobře a plýtvání prostředky.  Vždy potrubí více operací do stejného připojení.  Vaše aplikace bude mít prospěch z rychlosti transakcí a nákladů na prostředky.  Pokud vaše aplikace používá šifrování transportní vrstvy (například TLS), je značné náklady spojené se zpracováním nových připojení.  Zkontrolujte [vzory návrhu Azure Cloud](https://docs.microsoft.com/azure/architecture/patterns/) pro další vzory osvědčených postupů.

#### <a name="additional-possible-mitigations"></a>Další možné skutečnosti snižující závažnost rizika

_**Řešení:**_ Škálovat odchozí připojení následujícím způsobem:

| Scénář | Důkazy |Omezení rizik |
|---|---|---|
| Dochází k tvrzení o porty SNAT a vyčerpání portů SNAT během období vysoké využití. | Kategorie "Nezdařilo se" pro [metriku](nat-metrics.md) Připojení SNAT v Azure Monitoru zobrazuje přechodná nebo trvalá selhání v průběhu času a vysoký objem připojení.  | Zjistěte, zda můžete přidat další prostředky veřejných IP adres nebo veřejné prostředky předpony IP. Toto přidání umožní až 16 IP adres celkem do brány NAT. Toto přidání bude poskytovat další zásoby pro dostupné porty SNAT (64 000 na IP adresu) a umožní vám dále škálovat scénář.|
| Již jste poskytli 16 IP adres a stále dochází k vyčerpání portu SNAT. | Pokus o přidání další adresy IP se nezdaří. Celkový počet IP adres z veřejných prostředků IP adres nebo veřejných prostředků předpony IP přesahuje celkem 16. | Distribuujte aplikační prostředí mezi více podsítěmi a zajistěte prostředek brány NAT pro každou podsíť.  Přehodnoťte návrhový vzor (y) optimalizovat na základě předchozích [pokynů](#design-patterns). |

>[!NOTE]
>Je důležité pochopit, proč dochází k vyčerpání SNAT. Ujistěte se, že používáte správné vzory pro škálovatelné a spolehlivé scénáře.  Přidání dalších portů SNAT do scénáře bez pochopení příčiny poptávky by mělo být poslední možností. Pokud nepochopíte, proč váš scénář vyvíjí tlak na inventář portů SNAT, přidání dalších portů SNAT do inventáře přidáním dalších ADRES IP pouze zpozdí stejné selhání vyčerpání jako vaše aplikace.  Můžete maskovat jiné neefektivnosti a anti-vzory.

### <a name="icmp-ping-is-failing"></a>Příkaz ping ICMP selhává

[Virtuální síť NAT](nat-overview.md) podporuje protokoly IPv4 UDP a TCP. ICMP není podporována a očekává se selhání.  

_**Řešení:**_ Místo toho použijte testy připojení TCP (například "TCP ping") a testy aplikační vrstvy specifické pro UDP k ověření připojení typu end-to-end.

Následující tabulku lze použít jako výchozí bod, pro které nástroje použít ke spuštění testů.

| Operační systém | Obecný test připojení TCP | Test aplikační vrstvy TCP | UDP |
|---|---|---|---|
| Linux | nc (obecný test připojení) | zvlnění (test aplikační vrstvy TCP) | aplikace specifická |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [invoke-webrequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | aplikace specifická |

### <a name="connectivity-failures"></a>Selhání připojení

Problémy s připojením s [překladem nanepaměti virtuální sítě](nat-overview.md) mohou být způsobeny několika různými problémy:

* přechodné nebo trvalé [vyčerpání SNAT](#snat-exhaustion) brány NAT,
* přechodná selhání v infrastruktuře Azure, 
* přechodná selhání na cestě mezi Azure a veřejnou internetovou destinací, 
* přechodná nebo trvalá selhání v cílové destinaci veřejné sítě Internet.

K ověření připojení použijte nástroje, jako je následující. [Příkaz ping ICMP není podporován](#icmp-ping-is-failing).

| Operační systém | Obecný test připojení TCP | Test aplikační vrstvy TCP | UDP |
|---|---|---|---|
| Linux | nc (obecný test připojení) | zvlnění (test aplikační vrstvy TCP) | aplikace specifická |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [invoke-webrequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | aplikace specifická |

#### <a name="snat-exhaustion"></a>Vyčerpání SNAT

Projděte si oddíl o [vyčerpání SNAT](#snat-exhaustion) v tomto článku.

#### <a name="azure-infrastructure"></a>Infrastruktura Azure

Azure monitoruje a provozuje svou infrastrukturu s velkou péčí. Může dojít k přechodným poruchám, neexistuje žádná záruka, že přenosy jsou bezztrátové.  Používejte návrhové vzory, které umožňují opakované přenosy syn pro aplikace TCP. Použijte časové limity připojení dostatečně velké, aby umožnily opakovaný přenos Protokolu TCP SYN, aby se snížily přechodné dopady způsobené ztraceným paketem SYN.

_**Řešení:**_

* Zkontrolujte [vyčerpání SNAT](#snat-exhaustion).
* Parametr konfigurace v zásobníku Protokolu TCP, který řídí chování opakovaného přenosu SYN, se nazývá RTO ([Časový limit opakovaného přenosu).](https://tools.ietf.org/html/rfc793) Hodnota RTO je nastavitelná, ale obvykle 1 sekunda nebo vyšší ve výchozím nastavení s exponenciální back-off.  Pokud je časový limit připojení aplikace je příliš krátká (například 1 sekunda), může se zobrazit časové limity sporadické připojení.  Zvyšte časový limit připojení aplikace.
* Pokud pozorujete delší, neočekávané časové limity s výchozí chování aplikace, otevřete případ podpory pro další řešení potíží.

Nedoporučujeme uměle zkreslovat časový limit připojení Protokolu TCP nebo naladit parametr RTO.

#### <a name="public-internet-transit"></a>Veřejná internetová doprava

Pravděpodobnost přechodných selhání se zvyšuje s delší cestou k cíli a více mezilehlými systémy. Očekává se, že přechodná selhání může zvýšit četnost přes [infrastrukturu Azure](#azure-infrastructure). 

Postupujte podle stejných pokynů jako předchozí část [infrastruktury Azure.](#azure-infrastructure)

#### <a name="internet-endpoint"></a>Koncový bod Internetu

Platí předchozí části spolu s koncovým bodem Internetu, se kterým je komunikace navázána. Další faktory, které mohou mít vliv na úspěch připojení jsou:

* řízení dopravy na straně určení, včetně
- Omezení rychlosti rozhraní API uložené na straně určení
- Objemové ddos ové skutečnosti nebo tvarování transportní vrstvy
* brány firewall nebo jiné součásti v cílovém 

Obvykle paket zachytává ve zdroji a cíl (pokud je k dispozici) jsou nutné k určení, co se děje.

_**Řešení:**_

* Zkontrolujte [vyčerpání SNAT](#snat-exhaustion). 
* Ověřte připojení ke koncovému bodu ve stejné oblasti nebo jinde pro porovnání.  
* Pokud vytváříte testování s vysokou objemovou nebo transakční rychlostí, zjistěte, zda snížení rychlosti snižuje výskyt selhání.
* Pokud změna rychlosti ovlivňuje rychlost selhání, zkontrolujte, zda bylo dosaženo limitů rychlosti rozhraní API nebo jiných omezení na cílové straně.
* Pokud je vaše vyšetřování neprůkazné, otevřete případ podpory pro další řešení potíží.

#### <a name="tcp-resets-received"></a>Byla přijata protokol TCP Resetuje.

Brána NAT generuje protokol TCP resetuje na zdrojovém virtuálním počítači pro provoz, který není rozpoznán jako probíhající.

Jedním z možných důvodů je vypršel časový limit nečinnosti připojení TCP.  Časový limit nečinnosti můžete nastavit ze 4 minut až na 120 minut.

TCP Resety nejsou generovány na veřejné straně prostředků brány NAT. Obnovení protokolu TCP na cílové straně jsou generována zdrojovým virtuálním počítačem, nikoli prostředkem brány NAT.

_**Řešení:**_

* Zkontrolujte doporučení [návrhových vzorů.](#design-patterns)  
* V případě potřeby otevřete případ podpory pro další řešení potíží.

### <a name="ipv6-coexistence"></a>Koexistence IPv6

[Virtuální síť NAT](nat-overview.md) podporuje protokoly IPv4 UDP a TCP a nasazení v [podsíti s předponou IPv6 není podporováno](nat-overview.md#limitations).

_**Řešení:**_ Nasazení brány NAT v podsíti bez předpony IPv6.

Zájem o další funkce můžete uvést prostřednictvím [nástroje Virtual Network NAT UserVoice](https://aka.ms/natuservoice).

## <a name="next-steps"></a>Další kroky

* Další informace o [překladu virtuálních sítí](nat-overview.md)
* Informace o [prostředku brány NAT](nat-gateway-resource.md)
* Přečtěte si [o metrikách a výstrahách pro prostředky brány NAT](nat-metrics.md).
* [Řekněte nám, co se má stavět pro virtuální síť NAT v UserVoice](https://aka.ms/natuservoice).

