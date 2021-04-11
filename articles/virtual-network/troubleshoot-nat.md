---
title: Řešení potíží s připojením služby Azure Virtual Network NAT
titleSuffix: Azure Virtual Network
description: Řešení potíží se službou Virtual Network NAT
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/20/2020
ms.author: allensu
ms.openlocfilehash: 51bc4233393be6d914578581597e8cce9d0373b0
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059254"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity"></a>Řešení potíží s připojením služby Azure Virtual Network NAT

Tento článek pomáhá správcům diagnostikovat a řešit problémy s připojením při použití Virtual Network NAT.

## <a name="problems"></a>Problémy

* [Vyčerpání SNAT](#snat-exhaustion)
* [Nepodaří se odeslat test ICMP](#icmp-ping-is-failing)
* [Selhání připojení](#connectivity-failures)
* [Koexistence protokolu IPv6](#ipv6-coexistence)
* [Připojení nepochází z IP adresy brány NAT.](#connection-doesnt-originate-from-nat-gateway-ips)

Chcete-li tyto problémy vyřešit, postupujte podle kroků v následující části.

## <a name="resolution"></a>Řešení

### <a name="snat-exhaustion"></a>Vyčerpání SNAT

Jeden [prostředek brány NAT](nat-gateway-resource.md) podporuje 64 000 až 1 000 000 souběžných toků.  Každá IP adresa poskytuje k dostupnému inventáři porty 64 000 SNAT. Můžete použít až 16 IP adres na jeden prostředek brány NAT.  Mechanizmus SNAT je [zde](nat-gateway-resource.md#source-network-address-translation) popsán podrobněji.

Hlavní příčinou vyčerpání SNAT je i anti-vzor pro způsob, jakým se u odchozího připojení naváže, spravuje nebo konfigurovatelné časovače, které se mění z výchozích hodnot.  Pečlivě si prostudujte tuto část.

#### <a name="steps"></a>Postup

1. Ověřte, zda jste změnili výchozí časový limit nečinnosti na hodnotu vyšší než 4 minuty.
2. Prozkoumejte, jak vaše aplikace vytváří odchozí připojení (například revize kódu nebo zachycení paketů). 
3. Určete, zda má tato aktivita očekávané chování nebo zda se aplikace nechová.  Využijte [metriky](nat-metrics.md) v Azure monitor k odůvodnění vašich zjištění. Pro metriku připojení SNAT použijte kategorii "neúspěšné".
4. Vyhodnotí, zda jsou následovány příslušné vzory.
5. Vyhodnoťte, jestli se má vyčerpání portů SNAT zmírnit pomocí dalších IP adres přiřazených k prostředku brány NAT.

#### <a name="design-patterns"></a>Vzory návrhu

Kdykoli je to možné, využijte výhod opětovného použití připojení a sdružování připojení.  Tyto vzory se vyhne problémům s vyčerpáním prostředků a mají za následek předvídatelné chování. Primitivní prvky pro tyto vzory se dají najít v řadě vývojových knihoven a architektur.

_**Řešení:**_ Použití vhodných vzorů a osvědčených postupů

- Prostředky brány NAT mají výchozí časový limit nečinnosti TCP 4 minuty.  Pokud se toto nastavení změní na vyšší hodnotu, překlad adres (NAT) bude mít na toky delší dobu a může způsobit [zbytečný tlak na inventáři portů SNAT](nat-gateway-resource.md#timers).
- Atomické žádosti (jedna žádost na připojení) jsou nekvalitní volbou. Taková omezení pro antipatterny se omezují na škálování, snižuje výkon a snižuje spolehlivost. Místo toho můžete znovu použít připojení HTTP/S a snížit tak počet připojení a přidružené porty SNAT. Škálování aplikace bude zvyšovat a zvyšovat výkon v důsledku snížení nákladů na handshake, režijních a kryptografických operací při použití protokolu TLS.
- Pokud klient neukládá do mezipaměti výsledek překladače DNS, může do svazku zavést mnoho jednotlivých toků. Použijte ukládání do mezipaměti.
- Toky UDP (například vyhledávání DNS) přidělují porty SNAT po dobu nečinnosti časového limitu. Čím delší časový limit nečinnosti, tím vyšší je tlak na portech SNAT. Použijte krátký časový limit nečinnosti (například 4 minuty).
- Pomocí fondů připojení natvarujte svazek připojení.
- Nikdy netiché opuštění toku TCP a spoléhá na časovače TCP, aby se vyčistil tok. Pokud nepovolíte, aby protokol TCP explicitně zavřel připojení, stav zůstane přidělený v zprostředkujících systémech a koncových bodech a zpřístupňuje porty SNAT pro další připojení. Tento model může aktivovat selhání aplikace a vyčerpání SNAT. 
- Neměňte hodnoty časovače související s ukončením protokolu TCP na úrovni operačního systému bez odborných znalostí o dopadu. I když dojde k obnovení zásobníku protokolu TCP, výkon vaší aplikace může mít negativní vliv na to, že koncové body připojení neodpovídají očekávání. Přáním změnit časovače je obvykle znaménkem základního problému s návrhem. Přečtěte si následující doporučení.

Vyčerpání SNAT je také možné doplnit dalšími antivzory v podkladové aplikaci. Projděte si tyto další vzory a osvědčené postupy, které vám pomůžou zlepšit škálovatelnost a spolehlivost vaší služby.

- Vyzkoumejte dopad snížení [časového limitu nečinnosti protokolu TCP](nat-gateway-resource.md#timers) na nižší hodnoty, včetně výchozího časového limitu nečinnosti 4 minut, aby se uvolnil inventář portů SNAT dříve.
- Zvažte [asynchronní vzorce cyklického dotazování](/azure/architecture/patterns/async-request-reply) pro dlouhotrvající operace pro uvolnění prostředků připojení pro jiné operace.
- Dlouhodobé toky (například opakovaně používané připojení TCP) by měly používat udržení protokolu TCP nebo udržení nedostatku aplikační vrstvy, aby se předešlo vypršení zprostředkujících systémů. Zvýšení časového limitu nečinnosti je poslední a nemusí vyhodnotit původní příčinu. Dlouhý časový limit může způsobit selhání s nízkou rychlostí, pokud vyprší časový limit a zavádí zpoždění a nepotřebná selhání.
- [Vzorce opakovaného opakování](/azure/architecture/patterns/retry) by se měly používat k tomu, aby se předešlo agresivním pokusům o opakování nebo selhání při přechodném selhání nebo obnovení.
Vytvoření nového připojení TCP pro každou operaci HTTP (označované také jako "atomická připojení") je anti-Pattern.  Atomická připojení zabrání vaší aplikaci v škálování prostředků na dobrém a odpadním zdroji.  Vždy prokanálů více operací do stejného připojení.  Vaše aplikace bude využívat rychlost transakcí a náklady na prostředky.  Když vaše aplikace používá šifrování transportní vrstvy (například TLS), jsou k dispozici značné náklady spojené se zpracováním nových připojení.  Další vzory osvědčených postupů najdete v [vzorech návrhu cloudu Azure](/azure/architecture/patterns/) .

#### <a name="additional-possible-mitigations"></a>Další možná omezení rizik

_**Řešení:**_ Škálování odchozího připojení je následující:

| Scenario | Kazování |Omezení rizik |
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
| Windows | [PsPing](/sysinternals/downloads/psping) | Vyvolání PowerShellu [– WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) | specifické pro aplikaci |

### <a name="connectivity-failures"></a>Selhání připojení

Problémy s připojením ke službě [Virtual Network NAT](nat-overview.md) můžou být způsobené několika různými problémy:

* trvalá selhání kvůli chybám konfigurace.
* přechodná nebo trvalá [vyčerpání SNAT](#snat-exhaustion) brány NAT
* Přechodné chyby v infrastruktuře Azure, 
* Přechodné chyby v cestě mezi Azure a veřejným internetovým cílem 
* přechodná nebo trvalá selhání ve veřejném internetovém cílovém umístění.

K ověření připojení použijte nástroje, jako jsou následující. Protokol [ICMP pro příkazy není podporován](#icmp-ping-is-failing).

| Operační systém | Test obecného připojení TCP | Test aplikační vrstvy TCP | UDP |
|---|---|---|---|
| Linux | NC (test obecného připojení) | kudrlinkou (test aplikační vrstvy TCP) | specifické pro aplikaci |
| Windows | [PsPing](/sysinternals/downloads/psping) | Vyvolání PowerShellu [– WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) | specifické pro aplikaci |

#### <a name="configuration"></a>Konfigurace

Ověřte svou konfiguraci:
1. Má prostředek služby NAT Gateway alespoň jeden prostředek veřejné IP adresy nebo jeden prostředek předpony veřejné IP adresy? Aby služba NAT Gateway mohla zajišťovat odchozí připojení, musí k ní být přidružená alespoň jedna IP adresa.
2. Je podsíť virtuální sítě nakonfigurovaná tak, aby používala bránu NAT?
3. Používáte UDR (uživatelem definovaná trasa) a přepisujete cíl?  Prostředky brány NAT se stanou výchozí trasou (0/0) v nakonfigurovaných podsítích.

#### <a name="snat-exhaustion"></a>Vyčerpání SNAT

Přečtěte si část o [vyčerpání SNAT](#snat-exhaustion) v tomto článku.

#### <a name="azure-infrastructure"></a>Infrastruktura Azure

Azure monitoruje a provozuje svoji infrastrukturu se špičkovou péčí. K přechodným chybám může dojít, není nijak zaručeno, že jsou přenosy bezeztrátové.  Použití vzorů návrhu umožňujících opětovné přenosy SYN pro aplikace TCP. Používejte dostatečně velký časový limit připojení, aby bylo možné povolit opakovaný přenos TCP SYN, aby se snížily přechodné dopady způsobené ztrátou paketu SYN.

_**Řešení**_

* Kontroluje [vyčerpání SNAT](#snat-exhaustion).
* Parametr konfigurace v zásobníku protokolu TCP, který řídí chování funkce SYN pro opakování přenosu, se nazývá RTO ([časový limit opětovného přenosu](https://tools.ietf.org/html/rfc793)). Hodnota RTO je ve výchozím nastavení přizpůsobitelná, ale obvykle 1 sekunda nebo vyšší s exponenciálním pozadím.  Pokud je časový limit připojení aplikace moc krátký (například 1 sekunda), můžete se setkat s časovými prodlevami připojení.  Zvyšte časový limit připojení aplikace.
* Pokud obdržíte delší dobu, neočekávaná časová prodleva s výchozím chováním aplikace, otevřete případ podpory pro další řešení potíží.

Nedoporučujeme uměle snižovat časový limit připojení TCP nebo vyladit parametr RTO.

#### <a name="public-internet-transit"></a>Veřejný internetový přenos

Šance na přechodná selhání se zvýší s delší cestou k cílovému a více zprostředkujícím systémům. Očekávalo se, že přechodné chyby můžou zvýšit četnost v rámci [infrastruktury Azure](#azure-infrastructure). 

Řiďte se stejnými pokyny jako v části předchozí [infrastruktura Azure](#azure-infrastructure) .

#### <a name="internet-endpoint"></a>Internetový koncový bod

Předchozí části se vztahují spolu s koncovým bodem Internetu, se kterým se naváže komunikace. Další faktory, které mohou mít dopad na úspěšnost připojení:

* řízení provozu na straně cíle, včetně
- Omezení rychlosti rozhraní API, které ukládá cílová strana
- Snižování rizik DDoS a vytváření přenosů transportní vrstvy
* Brána firewall nebo jiné součásti v cílovém umístění 

K určení toho, co se provádí, se obvykle vyžaduje zachycení paketů na zdrojovém a cílovém umístění (Pokud je k dispozici).

_**Řešení**_

* Kontroluje [vyčerpání SNAT](#snat-exhaustion). 
* Ověřte připojení ke koncovému bodu ve stejné oblasti nebo jinde pro porovnání.  
* Pokud vytváříte testování s vysokým objemem nebo propustností transakcí, prozkoumejte, pokud omezení rychlosti snižuje počet selhání.
* Pokud změna frekvence ovlivňuje četnost selhání, ověřte, jestli se dosáhlo limitů rychlosti rozhraní API nebo jiných omezení na straně cíle.
* Pokud je vaše šetření neprůkazné, otevřete případ podpory pro další řešení potíží.

#### <a name="tcp-resets-received"></a>Přijatá obnovení TCP

Brána NAT vygeneruje na zdrojovém virtuálním počítači resety TCP pro provoz, který se nerozpoznal jako probíhající.

Jedním z možných důvodů je, že připojení TCP má nečinné časový limit.  Časový limit nečinnosti můžete upravit ze 4 minut na až 120 minut.

Resety TCP se negenerují na veřejné straně prostředků brány NAT. Resetování TCP na straně cíle je vygenerováno zdrojovým virtuálním počítačem, nikoli prostředkem brány NAT.

_**Řešení**_

* Projděte si doporučení pro [vzory návrhu](#design-patterns) .  
* Otevřete případ podpory pro další řešení potíží, pokud je to potřeba.

### <a name="ipv6-coexistence"></a>Koexistence protokolu IPv6

[Virtual Network NAT](nat-overview.md) podporuje protokoly IPv4 UDP a TCP a nasazení v [podsíti s předponou IPv6 se nepodporuje](nat-overview.md#limitations).

_**Řešení:**_ Nasaďte bránu NAT v podsíti bez předpony IPv6.

Díky [Virtual Network překladu adres (NAT)](https://aka.ms/natuservoice)můžete v zájmu dalších možností indikovat další možnosti.

### <a name="connection-doesnt-originate-from-nat-gateway-ips"></a>Připojení nepochází z IP adresy brány NAT.

Nakonfigurujete bránu NAT, IP adresy, které se mají použít, a kterou podsíť má použít prostředek brány NAT. Připojení z instancí virtuálních počítačů, které existovaly před nasazením brány NAT, ale nepoužívají IP adresy.  Zdá se, že používají IP adresy, které se nepoužívají s prostředkem brány NAT.

_**Řešení**_

[Virtual Network NAT](nat-overview.md) nahrazuje odchozí připojení pro podsíť, ve které je nakonfigurované. Při přechodu z výchozí adresy SNAT nebo nástroje pro vyrovnávání zatížení na používání bran NAT budou nová připojení okamžitě začít používat IP adresy přidružené k prostředku brány NAT.  Pokud ale virtuální počítač stále během přepínání na prostředek brány NAT zavede připojení, bude připojení dál používat starou IP adresu SNAT, která byla přiřazena při navázání připojení.  Ujistěte se, že skutečně vytváříte nové připojení místo opětovného použití připojení, které již existovalo, protože operační systém nebo prohlížeč vytvořil do mezipaměti připojení ve fondu připojení.  Například při použití _kudrlinkou_ v prostředí PowerShell Nezapomeňte zadat parametr _-DisableKeepalive_ , který vynutí nové připojení.  Pokud používáte prohlížeč, můžou být taky připojení ve fondu.

Pro prostředek brány NAT není nutné restartovat virtuální počítač s konfigurací podsítě.  Pokud je však virtuální počítač restartován, bude stav připojení vyprázdněn.  Po vyprázdnění stavu připojení budou všechna připojení začínat použitím IP adres prostředku brány NAT.  Jedná se ale o vedlejší účinek restartování virtuálního počítače, který není indikátorem, který vyžaduje restart.

Pokud pořád máte potíže, otevřete případ podpory pro další řešení potíží.

## <a name="next-steps"></a>Další kroky

* Informace o [Virtual Network NAT](nat-overview.md)
* Další informace o [prostředku brány NAT](nat-gateway-resource.md)
* Seznamte [se s metrikami a upozorněními pro prostředky brány NAT](nat-metrics.md).
* [Řekněte nám, co se má sestavit příště pro Virtual Network překlad adres (NAT) ve službě UserVoice](https://aka.ms/natuservoice).