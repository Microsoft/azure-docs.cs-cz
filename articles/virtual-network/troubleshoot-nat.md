---
title: Řešení potíží s připojením služby Azure Virtual Network NAT
titleSuffix: Azure Virtual Network NAT troubleshooting
description: Řešení potíží se službou Virtual Network NAT
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
ms.date: 03/05/2020
ms.author: allensu
ms.openlocfilehash: c629b3425cd095a6ac9d305b5cd6de58ed9d572a
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78674331"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity-problems"></a>Řešení potíží s připojením služby Azure Virtual Network NAT

Tento článek pomáhá správcům diagnostikovat a řešit problémy s připojením při použití Virtual Network NAT.

>[!NOTE] 
>Virtual Network překlad adres (NAT) je v tuto chvíli k dispozici jako Public Preview. V současné době je dostupná jenom v omezené sadě [oblastí](nat-overview.md#region-availability). Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="problems"></a>Problémy

* [Vyčerpání SNAT](#snat-exhaustion)
* [Nepodaří se odeslat test ICMP](#icmp-ping-is-failing)
* [Selhání připojení](#connectivity-failures)
* [Koexistence protokolu IPv6](#ipv6-coexistence)

Chcete-li tyto problémy vyřešit, postupujte podle kroků v následující části.

## <a name="resolution"></a>Řešení

### <a name="snat-exhaustion"></a>Vyčerpání SNAT

Jeden [prostředek brány NAT](nat-gateway-resource.md) podporuje 64 000 až 1 000 000 souběžných toků.  Každá IP adresa poskytuje k dostupnému inventáři porty 64 000 SNAT. Můžete použít až 16 IP adres na jeden prostředek brány NAT.  Mechanizmus SNAT je [zde](nat-gateway-resource.md#source-network-address-translation) popsán podrobněji.

Hlavní příčinou vyčerpání SNAT je i anti-vzor pro způsob, jakým se vytváří a spravuje odchozí připojení.  Pečlivě si prostudujte tuto část.

#### <a name="steps"></a>Kroky

1. Prozkoumejte, jak vaše aplikace vytváří odchozí připojení (například revize kódu nebo zachycení paketů). 
2. Určete, zda má tato aktivita očekávané chování nebo zda se aplikace nechová.  Využijte [metriky](nat-metrics.md) v Azure monitor k odůvodnění vašich zjištění. Pro metriku připojení SNAT použijte kategorii "neúspěšné".
3. Vyhodnotí, zda jsou následovány příslušné vzory.
4. Vyhodnoťte, jestli se má vyčerpání portů SNAT zmírnit pomocí dalších IP adres přiřazených k prostředku brány NAT.

#### <a name="design-patterns"></a>Způsoby návrhu

Kdykoli je to možné, využijte výhod opětovného použití připojení a sdružování připojení.  Tyto vzory se vyhnete problémům s vyčerpáním prostředků v nesprávném důsledku a jsou výsledkem předvídatelné, spolehlivé a škálovatelné chování. Primitivní prvky pro tyto vzory se dají najít v řadě vývojových knihoven a architektur.

_**Řešení:**_ Použití vhodných vzorů

- Zvažte [asynchronní vzorce cyklického dotazování](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply) pro dlouhotrvající operace pro uvolnění prostředků připojení pro jiné operace.
- Dlouhodobé toky (například opakovaně používané připojení TCP) by měly používat udržení protokolu TCP nebo udržení nedostatku aplikační vrstvy, aby se předešlo vypršení zprostředkujících systémů.
- [Vzorce opakovaného opakování](https://docs.microsoft.com/azure/architecture/patterns/retry) by se měly používat k tomu, aby se předešlo agresivním pokusům o opakování nebo selhání při přechodném selhání nebo obnovení.
Vytvoření nového připojení TCP pro každou operaci HTTP (označované také jako "atomická připojení") je anti-Pattern.  Atomická připojení zabrání vaší aplikaci v škálování prostředků na dobrém a odpadním zdroji.  Vždy prokanálů více operací do stejného připojení.  Vaše aplikace bude využívat rychlost transakcí a náklady na prostředky.  Když vaše aplikace používá šifrování transportní vrstvy (například TLS), jsou k dispozici značné náklady spojené se zpracováním nových připojení.  Další vzory osvědčených postupů najdete v [vzorech návrhu cloudu Azure](https://docs.microsoft.com/azure/architecture/patterns/) .

#### <a name="possible-mitigations"></a>Možná omezení rizik

_**Řešení:**_ Škálování odchozího připojení je následující:

| Scénář | Kazování |Omezení rizik |
|---|---|---|
| U portů SNAT a vyčerpání portů SNAT během období vysokého využití dochází ke sporu. | Kategorie "neúspěšné" v případě [metrik](nat-metrics.md) připojení SNAT v Azure monitor zobrazuje přechodné nebo trvalé chyby v čase a vysokém objemu připojení.  | Určete, jestli můžete přidat další prostředky veřejné IP adresy nebo prostředky předpony veřejných IP adres. Díky tomu bude pro vaši bránu NAT možné celkem až 16 IP adres. Toto přidání vám poskytne více inventářů pro dostupné porty SNAT (64 000 na IP adresu) a umožní vám ještě víc škálovat scénář.|
| Už jste zadali 16 IP adres a pořád dochází k vyčerpání portů SNAT. | Pokus o přidání další IP adresy se nezdařil. Celkový počet IP adres z prostředků veřejných IP adres nebo prostředků předpony veřejné IP adresy překračuje celkový počet 16. | Distribuujte aplikační prostředí napříč více podsítěmi a poskytněte prostředek brány NAT pro každou podsíť.  Přehodnoťte vzory návrhu pro optimalizaci na základě předchozích [pokynů](#design-patterns). |

>[!NOTE]
>Je důležité pochopit, proč dochází k vyčerpání SNAT. Ujistěte se, že používáte správné vzory pro škálovatelné a spolehlivé scénáře.  Přidání dalších portů SNAT do scénáře bez porozumění příčině požadavku by mělo být poslední možnost. Pokud nerozumíte tomu, proč váš scénář uplatňuje tlak na inventáři portů SNAT, přidání dalších IP adres do inventáře se dokončí tak, že se při škálování aplikace odloží jenom stejný stav vyčerpání.  Můžete maskovat jiné neefektivity a antipatterny.

### <a name="icmp-ping-is-failing"></a>Nepodaří se odeslat test ICMP

[Virtual Network NAT](nat-overview.md) podporuje protokoly IPv4 UDP a TCP. Protokol ICMP není podporován a očekává se selhání.  

_**Řešení:**_ Místo toho použijte testy připojení TCP (například "TCP test") a testy aplikační vrstvy specifické pro protokol UDP k ověření koncového a koncového připojení.

Následující tabulka slouží jako výchozí bod, ve kterém se nástroje použijí pro spuštění testů.

| Operační systém | Test obecného připojení TCP | Test aplikační vrstvy TCP | UDP |
|---|---|---|---|
| Linux | NC (test obecného připojení) | kudrlinkou (test aplikační vrstvy TCP) | specifické pro aplikaci |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | Vyvolání PowerShellu [– WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | specifické pro aplikaci |

### <a name="connectivity-failures"></a>Selhání připojení

Problémy s připojením ke službě [Virtual Network NAT](nat-overview.md) můžou být způsobeny několika různými problémy:

* přechodná nebo trvalá [vyčerpání SNAT](#snat-exhaustion) brány NAT
* Přechodné chyby v infrastruktuře Azure, 
* Přechodné chyby v cestě mezi Azure a veřejným internetovým cílem 
* přechodná nebo trvalá selhání ve veřejném internetovém cílovém umístění.

K ověření připojení použijte nástroje, jako jsou následující. [Příkazy protokolu ICMP pro protokol ICMP nejsou podporovány](#icmp-ping-is-failing).

| Operační systém | Test obecného připojení TCP | Test aplikační vrstvy TCP | UDP |
|---|---|---|---|
| Linux | NC (test obecného připojení) | kudrlinkou (test aplikační vrstvy TCP) | specifické pro aplikaci |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | Vyvolání PowerShellu [– WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | specifické pro aplikaci |

#### <a name="snat-exhaustion"></a>Vyčerpání SNAT

Přečtěte si část o [vyčerpání SNAT](#snat-exhaustion) v tomto článku.

#### <a name="azure-infrastructure"></a>Infrastruktura Azure

I když Azure monitoruje a provozuje svoji infrastrukturu se špičkovou péčí, může dojít k přechodným chybám, protože není nijak zaručeno, že jsou přenosy bezeztrátové.  Použití vzorů návrhu umožňujících opětovné přenosy SYN pro aplikace TCP. Používejte dostatečně velký časový limit připojení, aby bylo možné povolit opakovaný přenos TCP SYN, aby se snížily přechodné dopady způsobené ztrátou paketu SYN.

_**Řešení**_

* Kontroluje [vyčerpání SNAT](#snat-exhaustion).
* Parametr konfigurace v zásobníku protokolu TCP, který řídí chování funkce SYN pro opakování přenosu, se nazývá RTO ([časový limit opětovného přenosu](https://tools.ietf.org/html/rfc793)). Hodnota RTO je ve výchozím nastavení přizpůsobitelná, ale obvykle 1 sekunda nebo vyšší s exponenciálním pozadím.  Pokud je časový limit připojení aplikace moc krátký (například 1 sekunda), můžete se setkat s časovými prodlevami připojení.  Zvyšte časový limit připojení aplikace.
* Pokud obdržíte delší dobu, neočekávaná časová prodleva s výchozím chováním aplikace, otevřete případ podpory pro další řešení potíží.

Nedoporučujeme uměle snižovat časový limit připojení TCP nebo vyladit parametr RTO.

#### <a name="public-internet-transit"></a>veřejný internetový přenos

Pravděpodobnost přechodného selhání se zvyšuje s delší cestou k cílovému a více zprostředkujícím systémům. Očekává se, že přechodné chyby můžou zvýšit četnost v rámci [infrastruktury Azure](#azure-infrastructure). 

Řiďte se stejnými pokyny jako v části předchozí [infrastruktura Azure](#azure-infrastructure) .

#### <a name="internet-endpoint"></a>Internetový koncový bod

Předchozí části se vztahují i na požadavky týkající se internetového koncového bodu, ve kterém je vaše komunikace vytvořená. Další faktory, které mohou mít dopad na úspěšnost připojení:

* řízení provozu na straně cíle, včetně
- Omezení rychlosti rozhraní API, které ukládá cílová strana
- Snižování rizik DDoS a vytváření přenosů transportní vrstvy
* Brána firewall nebo jiné součásti v cílovém umístění 

K určení toho, co se provádí, se obvykle vyžaduje zachycení paketů ve zdroji i cíl (Pokud je k dispozici).

_**Řešení**_

* Kontroluje [vyčerpání SNAT](#snat-exhaustion). 
* Ověřte připojení ke koncovému bodu ve stejné oblasti nebo jinde pro porovnání.  
* Pokud vytváříte testování s vysokým objemem nebo propustností transakcí, prozkoumejte, pokud omezení rychlosti snižuje počet selhání.
* Pokud změna frekvence ovlivňuje četnost selhání, ověřte, jestli se dosáhlo limitů rychlosti rozhraní API nebo jiných omezení na straně cíle.
* Pokud je vaše šetření neprůkazné, otevřete případ podpory pro další řešení potíží.

#### <a name="tcp-resets-received"></a>Přijatá obnovení TCP

Pokud zjistíte, že se na zdrojovém virtuálním počítači přijmou obnovená nastavení TCP (TCP RST), můžou se vygenerovat bránou NAT na soukromé straně pro toky, které se nerozpoznají jako probíhající.  Jedním z možných důvodů je, že připojení TCP má nečinné časový limit.  Časový limit nečinnosti můžete upravit ze 4 minut na až 120 minut.

Resety TCP se negenerují na veřejné straně prostředků brány NAT. Pokud v cílové straně obdržíte resety TCP, vygenerují se v zásobníku zdrojového virtuálního počítače, a ne v prostředku brány NAT.

_**Řešení**_

* Projděte si doporučení pro [vzory návrhu](#design-patterns) .  
* Otevřete případ podpory pro další řešení potíží, pokud je to potřeba.

### <a name="ipv6-coexistence"></a>Koexistence protokolu IPv6

[Virtual Network NAT](nat-overview.md) podporuje protokoly IPv4 UDP a TCP a nasazení v [podsíti s předponou IPv6 se](nat-overview.md#limitations)nepodporuje.

_**Řešení:**_ Nasaďte bránu NAT v podsíti bez předpony IPv6.

Díky [Virtual Network překladu adres (NAT)](https://aka.ms/natuservoice)můžete v zájmu dalších možností indikovat další možnosti.

## <a name="next-steps"></a>Další kroky

* Informace o [Virtual Network NAT](nat-overview.md)
* Další informace o [prostředku brány NAT](nat-gateway-resource.md)
* Seznamte [se s metrikami a upozorněními pro prostředky brány NAT](nat-metrics.md).
* [Řekněte nám, co se má sestavit příště pro Virtual Network překlad adres (NAT) ve službě UserVoice](https://aka.ms/natuservoice).

