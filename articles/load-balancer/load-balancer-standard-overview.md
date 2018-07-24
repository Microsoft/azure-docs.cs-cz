---
title: Přehled služby Azure Load balancer úrovně Standard | Dokumentace Microsoftu
description: Přehled funkcí Azure Load balancer úrovně Standard
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/20/2018
ms.author: kumud
ms.openlocfilehash: 1a7f37d3f95701779a16cf5dc6844fb67ee7f956
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215097"
---
# <a name="azure-load-balancer-standard-overview"></a>Přehled služby Azure Load balancer úrovně Standard

Nástroj Azure Load Balancer umožňuje škálovat svoje aplikace a poskytovat vysokou dostupnost vaší služby. Nástroj pro vyrovnávání zatížení lze použít pro příchozí i odchozí scénáře a poskytuje nízkou latenci a vysokou propustnost a škálování až na úrovni milionů toků pro všechny aplikace TCP a UDP. 

Tento článek se zaměřuje na Load balanceru úrovně Standard.  Obecnější přehled nástroje pro vyrovnávání zatížení Azure, najdete v tématu [Load Balancer – přehled](load-balancer-overview.md) také.

## <a name="what-is-standard-load-balancer"></a>Co je Load balancer úrovně Standard?

Load balancer úrovně Standard je nový produkt nástroje pro vyrovnávání zatížení pro všechny protokoly TCP a UDP aplikací pomocí funkce služby rozšíření a podrobnější nastavení přes Load balancer úrovně Basic.  I když existují mnoho podobností, je důležité se seznámit s rozdíly, jak je uvedeno v tomto článku.

Load balancer úrovně Standard můžete použít jako veřejný nebo interní Vyrovnávání zatížení. A virtuální počítač může být připojen k veřejné a jeden prostředek interní nástroj pro vyrovnávání zatížení.

Funkce prostředku nástroje pro vyrovnávání zatížení se vždy vyjádřený jako front-end, pravidla, sondu stavu a definice fondu back-endu.  Prostředek může obsahovat více pravidel. Virtuální počítače lze umístit do back-endového fondu tak, že zadáte back-endový fond z prostředku síťové karty virtuálního počítače.  Tento parametr je předat prostřednictvím profilu sítě a rozšířit při použití škálovacích sad virtuálních počítačů.

Jeden klíčovým aspektem je pro prostředek oboru ve virtuální síti.  Load balancer úrovně Basic existuje v rámci oboru skupiny dostupnosti, Load balanceru úrovně Standard je plně integrována s rozsahem virtuální sítě a použít všechny koncepty virtuální sítě.

Prostředků nástroje pro vyrovnávání zatížení jsou objekty, ve kterém můžete vyjádřit způsobu Azure by měla aplikace dosáhnout scénář, který chcete vytvořit svoji infrastrukturu více tenantů.  Není žádný přímý vztah mezi prostředky nástroje pro vyrovnávání zatížení a infrastruktury skutečný; Vytvoření nástroje pro vyrovnávání zatížení nelze vytvořit instanci, je vždy k dispozici Kapacita a neexistují žádné počáteční nebo škálování vezměte v úvahu docházet ke zpožděním. 

>[!NOTE]
> Azure poskytuje sadu plně spravované řešení pro vaše scénáře Vyrovnávání zatížení.  Pokud chcete pro ukončení protokolu TLS ("přesměrování zpracování SSL") nebo za zpracování vrstvy aplikace požadavku HTTP/HTTPS, přečtěte si [Application Gateway](../application-gateway/application-gateway-introduction.md).  Pokud chcete pro službu DNS globální Vyrovnávání zatížení, přečtěte si [Traffic Manageru](../traffic-manager/traffic-manager-overview.md).  Scénáře začátku do konce můžou mít užitek z kombinace těchto řešení podle potřeby.

## <a name="why-use-standard-load-balancer"></a>Proč používat Load balanceru úrovně Standard?

Standard Load Balancer umožňuje škálovat aplikace a zajistit vysokou dostupnost pro nasazení v malém měřítku až po rozsáhlé a složité architektury s více zónami.

Následující tabulka obsahuje přehled přehledné informace o rozdílech mezi Load balanceru úrovně Standard a Load balancer úrovně Basic:

>[!NOTE]
> Load balancer úrovně Standard by měl přijmout nové návrhy. 

| | Standardní SKU | Základní SKU |
| --- | --- | --- |
| Velikost fondu back-endu | až 1 000 instancí | až 100 instancí |
| Koncové body back-Endového fondu | jakýkoli virtuální počítač v jedné virtuální sítě, včetně kombinace virtuální počítače, skupiny dostupnosti, škálovacích sad virtuálních počítačů. | virtuální počítače ve škálovací sady nebo na virtuálním počítači jeden dostupnosti |
| Zóny dostupnosti | zónové a zónově redundantních front-endů pro příchozí a odchozí, odchozí toky mapování překonat selhání zóny Vyrovnávání zatížení mezi zónami | / |
| Diagnostika | Azure Monitor, vícedimenzionálních metrik, včetně bajtů a čítače paketů, stav testu stavu, pokusy o připojení (TCP SYN), stav odchozí připojení (SNAT úspěšné i neúspěšné toky), měření roviny aktivních dat. | Azure Log Analytics pro veřejného Load Balanceru úrovně pouze SNAT vyčerpání výstraha, počet stavu back-endového fondu |
| HA porty | interní nástroj pro vyrovnávání zatížení | / |
| Ve výchozím nastavení zabezpečení | Výchozí uzavřel za veřejné IP adresy a nástroje pro vyrovnávání zatížení koncové body a skupiny zabezpečení sítě musí použít na explicitní seznam povolených pro provoz tok | Výchozí otevřený, skupiny zabezpečení sítě volitelné |
| [Odchozí připojení](load-balancer-outbound-connections.md) | Několik front-endů s za odhlásit pravidlo Vyrovnávání zatížení. Odchozí scénář _musí_ explicitně vytvořit pro virtuální počítač, abyste mohli použít odchozí připojení.  [Koncové body služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md) dostupná bez odchozí připojení a ne započítávat zpracovaná data.  Všechny veřejné IP adresy, včetně služeb Azure PaaS, která není k dispozici jako koncové body služby virtuální sítě, musíte kontaktovat prostřednictvím odchozího připojení a počet směrem k zpracovaná data. Když virtuální počítač obsluhuje jenom interní nástroj pro vyrovnávání zatížení, nejsou k dispozici odchozí připojení přes výchozí SNAT. Přenosový protokol konkrétní založené na protokolu Příchozí pravidlo Vyrovnávání zatížení je výstupní programování SNAT. | Jeden front-endu, když jsou k dispozici několik front-endů vybraného náhodně.  Když virtuální počítač obsluhuje jenom interní nástroj pro vyrovnávání zatížení, použije se výchozí SNAT. |
| [Několik front-endů](load-balancer-multivip-overview.md) | Příchozí a [odchozí](load-balancer-outbound-connections.md) | Pouze příchozí |
| [Sonda stavu dolů chování](load-balancer-custom-probe-overview.md) | Připojení TCP zůstat naživu na instanci test dolů __a__ na všechny testy dolů | Připojení TCP zůstat naživu na instanci test mimo provoz. Ukončete všechna připojení TCP na všechny testy dolů |
| Operace správy | Většina operací < 30 sekund | 60 – 90 sekund typické |
| SLA | 99,99 % pro cestu k datům s dva virtuální počítače v dobrém stavu | Implicitní v SLA k virtuálním počítačům | 
| Ceny | Bude účtovat podle počtu pravidel, zpracování dat příchozí nebo odchozí přidruženého prostředku  | bez poplatků |

Kontrola [omezení služby nástroje pro vyrovnávání zatížení](https://aka.ms/lblimits), stejně jako [ceny](https://aka.ms/lbpricing), a [SLA](https://aka.ms/lbsla).


### <a name="backend"></a>Back-endový fond

Back-endové fondy úrovně standard pro vyrovnávání zatížení se rozšíří na prostředek virtuálního počítače ve virtuální síti.  Může obsahovat až 1000 instancí back-endu.  Instance back-end je konfiguraci IP, který je vlastnost prostředku síťové karty.

Back-endový fond může obsahovat samostatné virtuální počítače, skupiny dostupnosti nebo škálovací sady virtuálních počítačů.  Můžete také kombinovat prostředků v back-endový fond. Můžete kombinovat až 150 prostředků v back-endový fond pro každý prostředek nástroje pro vyrovnávání zatížení.

Při zvažování návrhu vaší back-endového fondu, můžete navrhnout pro nejmenší počet jednotlivých back-endový fond prostředků k optimalizaci trvání operace správy.  Není žádný rozdíl ve výkonu roviny dat nebo určený počet číslic.

## <a name="az"></a>Zóny dostupnosti

Load balancer úrovně standard podporuje další možnosti v oblastech, kde zóny dostupnosti jsou k dispozici.  Tyto funkce se přičítají k všechny Load balanceru úrovně Standard poskytuje.  Konfigurace zóny dostupnosti jsou k dispozici pro veřejné a vnitřní Load balanceru úrovně Standard.

Ve výchozím nastavení při nasazení v oblasti se zónami dostupnosti stát zónově redundantní non zónové front-endů.   Zónově redundantní front-endu odolává selhání zóny a obsluhují vyhrazená infrastruktura ve všech oblastí současně. 

Kromě toho může zaručit front-end ke konkrétní zóně. Oblastmi front-endu sdílí pracuje s příslušnými zóny a slouží pouze vyhrazená infrastruktura v jedné oblasti.

Vyrovnávání zatížení mezi zónami je k dispozici pro back-endového fondu a prostředek virtuálního počítače ve virtuální síti můžou být součástí back-endový fond.

Kontrola [podrobnou diskuzi o zónách dostupnosti související možnosti](load-balancer-standard-availability-zones.md).

### <a name="diagnostics"></a> Diagnostika

Load balancer úrovně standard poskytuje vícedimenzionálních metrik přes Azure Monitor.  Tyto metriky můžete filtrovat, seskupeny a rozdělují a směřují pro dané dimenzi.  Obsahují aktuální a historický přehled o výkonu a stavu služby.  Služba Resource Health je také podporována.  Tady je stručný přehled podporovaných diagnostiky:

| Metrika | Popis |
| --- | --- |
| Dostupnost virtuálních IP adres | Load balancer úrovně Standard vykonává nepřetržitě na cestu k datům z v rámci oblasti nástroji pro vyrovnávání zatížení front-endu k sadě komponent SDN, který podporuje váš virtuální počítač. Za předpokladu, zůstanou v dobrém stavu instance, měření se řídí stejnou cestu jako provoz s vyrovnáváním zatížení vaší aplikace. Na cestu k datům, která používají vaši zákazníci je také ověřován. Měření není viditelný pro vaši aplikaci a nebude v konfliktu s dalšími operacemi.|
| Dostupnost vyhrazené IP adresy | Load balancer úrovně Standard používá distribuované stavu testování služba, která monitoruje stav vašeho koncového bodu aplikace podle nastavení konfigurace. Tato metrika poskytuje agregace nebo za koncový bod filtrované zobrazení každé jednotlivé instance koncového bodu v nástroji pro vyrovnávání zatížení fondu.  Uvidíte jak nástroj pro vyrovnávání zatížení zobrazení stavu aplikace, jak je uvedeno ve vaší konfiguraci sondy stavu.
| SYN pakety | Load balancer úrovně Standard není ukončit připojení TCP nebo pracovat s TCP nebo UDP paketů toky. Toky a jejich počet metod handshake jsou vždy mezi zdrojem a instanci virtuálního počítače. Lepší řešení potíží s scénáře protokol TCP, které můžete využít SYN čítače paketů o tom, kolik připojení TCP pokusy. Metrika hlásí počet TCP SYN pakety, které byly přijaty.|
| Připojení SNAT | Load balancer úrovně Standard hlásí počet odchozích toků, které jsou masqueraded veřejné IP adresy front-endu. SNAT porty jsou vyčerpatelným prostředků. Tato metrika se mohou vyjádřit údaj o tom, jak často se aplikace spoléhá na SNAT pro odchozí toky s původem.  Čítače pro úspěšné i neúspěšné odchozích toků SNAT označené a slouží k odstranění potíží a porozumět stavu vašich odchozích toků.|
| Čítače bajtů | Load balancer úrovně Standard sestavy dat zpracovaných za front-endu.|
| Čítače paketů | Load balancer úrovně Standard sestavy pakety zpracovaných za front-endu.|

Kontrola [podrobné informace o standardního diagnostického nástroje pro vyrovnávání zatížení](load-balancer-standard-diagnostics.md).

### <a name="haports"></a>HA porty

Load balancer úrovně standard podporuje nový typ pravidla.  

Můžete nakonfigurovat pravidla být vysoce spolehlivé škálování vaší aplikace a vyrovnávání zatížení. Při použití porty s vysokou DOSTUPNOSTÍ pravidlo Vyrovnávání zatížení, Load balanceru úrovně Standard vám poskytne za tok zátěže na každý dočasných portů interní Load balanceru úrovně Standard pro front-endové IP adresy.  Tato funkce je užitečná pro jiné scénáře, kdy je nepraktické nebo nežádoucích k zadání jednotlivých portů.

Pravidlo Vyrovnávání zatížení porty s vysokou DOSTUPNOSTÍ umožňuje vytvořit aktivní pasivní nebo aktivní aktivní n + 1 scénáře pro síťová virtuální zařízení a jakékoli aplikace, která vyžaduje velké rozsahy příchozí porty.  Chcete-li zjistit, které back-EndY by měla přijímat nové toky je možné sondu stavu.  Skupina zabezpečení sítě můžete použít k emulaci scénář rozsah portů.

>[!IMPORTANT]
> Pokud máte v úmyslu používat síťové virtuální zařízení, obraťte se na dodavatele pokyny ohledně toho, jestli svůj produkt je testovaná s porty s vysokou DOSTUPNOSTÍ a postupujte podle jejich konkrétní pokyny pro implementaci. 

Kontrola [podrobné informace o porty s vysokou DOSTUPNOSTÍ](load-balancer-ha-ports-overview.md).

### <a name="securebydefault"></a>Ve výchozím nastavení zabezpečení

Load balancer úrovně Standard je plně připojení k virtuální síti.  Virtuální síť je privátní a uzavřené.  Vzhledem k tomu, že nástroje pro vyrovnávání zatížení a standardní veřejné IP adresy jsou navržené tak, aby této virtuální síti přístup z mimo virtuální síť, tyto prostředky teď ve výchozím nastavení uzavřeno, pokud je otevřete. To znamená, že skupiny zabezpečení sítě (Nsg) se teď používají tak, aby výslovně povolovala a seznamu povolených IP adres povolený provoz.  Můžete vytvořit celého virtuálního datového centra a rozhodnout, co a kdy by měla být k dispozici prostřednictvím skupiny zabezpečení sítě.  Pokud nemáte skupiny NSG v podsíti nebo NIC prostředku vašeho virtuálního počítače, přenos není povolen pro dosažení tohoto prostředku.

Další informace o skupinách Nsg a způsobu jejich použití pro váš scénář, naleznete v tématu [skupiny zabezpečení sítě](../virtual-network/security-overview.md).

### <a name="outbound"></a> Odchozí připojení

Nástroj pro vyrovnávání zatížení podporuje scénáře příchozí a odchozí.  Load balancer úrovně standard se značně liší základní nástroje pro vyrovnávání zatížení s ohledem na odchozí připojení.

Zdrojové síťové adresy překladu (SNAT) se používá k mapování interní a privátní IP adres ve virtuální síti na veřejné IP adresy na nástroj pro vyrovnávání zatížení front-endů.

Zavádí nový algoritmus pro Load balancer úrovně standard [robustní, škálovatelné a předvídatelné SNAT algoritmus](load-balancer-outbound-connections.md#snat) a umožňuje nové možnosti, odebere nejednoznačnosti a vynutí explicitní konfigurace spíše na straně účinky. Tyto změny jsou nezbytné k tomu pro nové funkce, které se objeví. 

Toto jsou klíčové principů pamatovat při práci se službou Load balancer úrovně Standard:

- dokončení pravidlo jednotek prostředku nástroje pro vyrovnávání zatížení.  všechny programování Azure je odvozena od její konfiguraci.
- Když jsou k dispozici několik front-endů, všechny front-endů se používají a počet dostupných portů SNAT vynásobí každý front-endu
- můžete vybrat a řízení, pokud nechcete, aby pro konkrétní front-endu má být použit pro odchozí připojení.
- odchozí scénáře jsou explicitní a odchozí připojení neexistuje, dokud je zadaná.
- pravidla Vyrovnávání zatížení odvození, jak programovat SNAT. Pravidla Vyrovnávání zatížení jsou specifická pro protokol. SNAT je specifická pro protokol a konfiguraci byste tyto změny projeví spíše než vytvořit vedlejší účinek.

#### <a name="multiple-frontends"></a>Několik front-endů
Pokud chcete další porty SNAT, protože se očekává nebo jsou již dochází k vysoké požadavky na odchozích připojení, můžete také přidat přírůstkové inventáře port SNAT nakonfigurováním dalších front-endů, pravidla a back-endové fondy na stejný virtuální počítač prostředky.

#### <a name="control-which-frontend-is-used-for-outbound"></a>Ovládací prvek, který front-endu se používá pro odchozí
Pokud chcete omezit odchozí připojení k pocházejí pouze z konkrétní front-endové IP adresy, Volitelně můžete zakázat odchozí SNAT na pravidlo, které vyjadřuje odchozí mapování.

#### <a name="control-outbound-connectivity"></a>Odchozí připojení ovládacího prvku
Load balancer úrovně Standard existuje v rámci virtuální sítě.  Virtuální síť je izolované a privátní sítě.  Pokud existuje přidružení s použitím veřejné IP adresy veřejného připojení se nepovoluje.  Můžete oslovit [koncové body služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md) protože jsou uvnitř a místní k virtuální síti.  Pokud chcete navázat odchozí připojení k cíli mimo virtuální síť, máte dvě možnosti:
- přiřadit veřejnou IP adresu standardních SKU jako adresu veřejnou IP adresu na úrovni Instance pro prostředek virtuálního počítače nebo
- Umístěte virtuální počítače ve fondu back-end veřejný Load balancer úrovně Standard.

Obě umožní odchozí připojení z virtuální sítě, abyste mimo virtuální síť. 

Pokud jste _pouze_ mají interní Load balanceru úrovně Standard přidružené k back-endový fond, ve kterém se nachází váš prostředek virtuálního počítače, virtuální počítač může dosáhnout jenom prostředky virtuální sítě a [služba virtuální sítě Koncové body](../virtual-network/virtual-network-service-endpoints-overview.md).  Můžete postupovat podle kroků popsaných v předchozím odstavci vytvořit odchozí připojení.

Odchozí připojení není přidružen standardní SKU zůstane jako před prostředku virtuálního počítače.

Kontrola [podrobné informace o odchozích připojení](load-balancer-outbound-connections.md).

### <a name="multife"></a>Několik front-endů
Nástroj pro vyrovnávání zatížení podporuje více pravidel s více front-endů.  Load balancer úrovně Standard to rozšíří na odchozí scénáře.  Odchozí scénáře jsou v podstatě inverzní funkce příchozí pravidlo Vyrovnávání zatížení.  Příchozí pravidla i Vyrovnávání zatížení vytvoří spolupracovníkovi pro odchozí připojení. Load balanceru úrovně Standard používá všech front-endů přidružený prostředek virtuálního počítače prostřednictvím pravidla Vyrovnávání zatížení.  Kromě toho parametr na Vyrovnávání zatížení pravidlo a umožňuje potlačit pravidlo Vyrovnávání zatížení pro účely odchozí připojení, které vám umožní vybrat konkrétní front-endů včetně none.

Pro porovnání Load balancer úrovně Basic náhodně vybere jeden front-endu a neexistuje žádná možnost řídit, které z nich byl vybrán.

Kontrola [podrobné informace o odchozích připojení](load-balancer-outbound-connections.md).

### <a name="operations"></a> Operace správy

Standardní prostředky nástroje pro vyrovnávání zatížení existovat na zcela novou platformu infrastruktury.  To umožňuje výrazně rychlejší operace správy pro SKU Standard a časy dokončení jsou obvykle méně než 30 sekund pro každý prostředek standardní SKU.  Všimněte si, že jako back-endové fondy zvětšují svou velikost, doba trvání vyžadované pro back-endového fondu se změní také zvýšení.

Můžete upravit prostředky Load balanceru úrovně Standard a přesunout standardní veřejnou IP adresu z jednoho virtuálního počítače do jiného mnohem rychlejší.

## <a name="migration-between-skus"></a>Migrace mezi skladovými položkami

Skladové jednotky se nedají mutable. Postupujte podle kroků v této části přesunout z jednoho prostředku skladovou Položku do jiného.

>[!IMPORTANT]
>Přečtěte si tento dokument v celém rozsahu pochopit rozdíly mezi skladovými položkami a pečlivě zkoumat, váš scénář.  Budete muset provést další změny zarovnání váš scénář.

### <a name="migrate-from-basic-to-standard-sku"></a>Migrace ze základní na standardní SKU

1. Vytvoření nových standardních prostředků (nástroj pro vyrovnávání zatížení a veřejné IP adresy, podle potřeby). Znovu vytvořte pravidla a sběru dat definice.

2. Vytvořit novou nebo aktualizovat existující skupině NSG na síťové karty nebo podsítě do seznamu povolených IP adres s vyrovnáváním zatížení provozu, sondy, jakož i ostatní, které chcete povolit provoz.

3. Odeberte prostředky základní SKU (nástroj pro vyrovnávání zatížení a veřejné IP adresy, podle vhodnosti) ze všech instancí virtuálních počítačů. Je potřeba taky odebrat všechny instance virtuálních počítačů sady dostupnosti.

4. Všechny instance virtuálních počítačů připojte k nové prostředky standardní SKU.

### <a name="migrate-from-standard-to-basic-sku"></a>Migrovat ze Standard na základní SKU

1. Vytvořte nový prostředek základní (nástroj pro vyrovnávání zatížení a veřejné IP adresy, podle potřeby). Znovu vytvořte pravidla a sběru dat definice. 

2. Odeberte prostředky standardní SKU (nástroj pro vyrovnávání zatížení a veřejné IP adresy, podle vhodnosti) ze všech instancí virtuálních počítačů. Je potřeba taky odebrat všechny instance virtuálních počítačů sady dostupnosti.

3. Všechny instance virtuálních počítačů připojte k nové prostředky základní SKU.

>[!IMPORTANT]
>
>Existují omezení týkající se použití Basic a Standard skladové položky.
>
>HA porty a Diagnostika standardních SKU jsou k dispozici pouze ve standardním SKU. Nelze migrovat ze standardních SKU pro základní SKU a také zachovat tyto funkce.
>
>Základní a standardní SKU mít několik rozdílů, jak je uvedeno v tomto článku.  Ujistěte se, že pochopení a příprava pro ně.
>
>Odpovídající SKU musí použít pro nástroj pro vyrovnávání zatížení a veřejnou IP adresu zdroje. Nemůžete mít směs základní SKU prostředky a prostředky standardní SKU. Samostatné virtuální počítače, virtuální počítače v prostředku skupiny dostupnosti ani prostředky škálovacích sad virtuálních počítačů není možné připojit k oběma SKU zároveň.

## <a name="region-availability"></a>Dostupnost v oblastech

Load balancer úrovně Standard je teď dostupná v všech oblastech veřejného cloudu.

## <a name="sla"></a>SLA

Nástroje pro vyrovnávání zatížení jsou k dispozici s 99,99 % smlouva SLA.  Zkontrolujte [standardní SLA nástroje pro vyrovnávání zatížení](https://aka.ms/lbsla) podrobnosti.

## <a name="pricing"></a>Ceny

Load balanceru úrovně standard se účtuje produkt na základě počtu nakonfigurovaných pravidel a všechny příchozí a odchozí objemu zpracovaných dat pro vyrovnávání zatížení. Load balancer úrovně Standard informace o cenách, najdete [ceny za nástroj pro vyrovnávání zatížení](https://aka.ms/lbpricing) stránky.

## <a name="limitations"></a>Omezení

- Skladové jednotky se nedají mutable. Nejde změnit skladovou Položku na existující prostředek.
- Prostředek virtuální počítač samostatné skupiny dostupnosti prostředků nebo prostředek škálovací sady virtuálních počítačů může odkazovat na jeden SKU, nikdy obojí.
- Pravidlo nástroje pro vyrovnávání zatížení nemůžou zahrnovat dvě virtuální sítě.  Front-Endů a jejich související back-endových instancí se musí nacházet ve stejné virtuální síti.  
- Front-endů nástroje pro vyrovnávání zatížení nejsou přístupné napříč globální vnet peering.
- [Přesunout předplatné operace](../azure-resource-manager/resource-group-move-resources.md) nejsou podporovány pro standardní SKU LB a PIP prostředky.
- Webových rolí pracovního procesu bez virtuální sítě a dalších služeb platformy Microsoft může být přístupný, když kvůli vedlejším účinkem z jak pre-VNet služeb a další platformy služby funkce se používá jenom interní Load balanceru úrovně Standard. Nesmí spoléhat na to, jak funkcím služby sebe samu ani na základní platformě můžete změnit bez předchozího upozornění. Musíte vždy předpokládat, je potřeba vytvořit [odchozí připojení](load-balancer-outbound-connections.md) explicitně potřeby při použití interní Load balanceru úrovně Standard pouze.
- Nástroj pro vyrovnávání zatížení je produkt TCP nebo UDP pro vyrovnávání zatížení a přesměrování portu pro tyto konkrétní protokoly IP.  Pravidla Vyrovnávání zatížení a pravidla příchozího překladu adres jsou podporované pro TCP a UDP a není podporována pro ostatní IP protokoly včetně protokolu ICMP. Nástroj pro vyrovnávání zatížení neukončí, reagovat nebo jinak interakci s datovou částí toku UDP nebo TCP. Nejedná se o proxy serveru. Úspěšné ověření připojení k front-endem přijme místo integrované s stejný protokol použitý v zatížení vyrovnávání nebo příchozí pravidlo NAT (TCP nebo UDP) _a_ alespoň jeden z vašich virtuálních počítačů musí generovat odpověď pro klienta Chcete-li zobrazit odpověď z front-endem.  Nepřijímá odpověď integrovaných z front-endu nástroje pro vyrovnávání zatížení Určuje, že žádné virtuální počítače nebyly schopné reagovat.  Není možné pracovat s nástroji pro vyrovnávání zatížení front-endu bez virtuální počítač, který je schopný reagovat.  To platí i pro odchozí připojení kde [port krycí SNAT](load-balancer-outbound-connections.md#snat) se podporuje jenom pro TCP a UDP; žádné další IP protokoly včetně protokolu ICMP se také nezdaří.  Přiřazení veřejné IP adresy na úrovni instance zmírnit.
- Na rozdíl od veřejné Vyrovnávání zatížení, který bude poskytovat [odchozí připojení](load-balancer-outbound-connections.md) při přesunu z privátních IP adres ve virtuální síti na veřejné IP adresy, interní nástroje pro vyrovnávání zatížení nepřekládat odchozí pochází připojení k front-endu interního nástroje Load Balancer obě jsou v privátní adresní prostor IP adres.  Tím se vyhnete potenciál pro SNAT vyčerpání uvnitř jedinečný interní adresní prostor IP adres ve kterém se nevyžaduje překlad.  Vedlejším účinkem je, že pokud odchozí tok z virtuálního počítače v back endového fondu pokouší toku pro front-endu interního nástroje pro vyrovnávání zatížení ve fondu, který se nachází _a_ je namapována na sebe sama, obě úsecích tok neodpovídají a tok se nezdaří .  Pokud tok nemapují zpět na stejný virtuální počítač v back endového fondu, který vytvoří tok front-endu, tok proběhne úspěšně.   Když tok mapuje sám na sebe odchozího toku se zobrazí na pocházejí z virtuálního počítače do front-endu a odpovídající příchozího toku se zobrazí pocházejí z virtuálního počítače na sebe sama. Z hostovaného operačního systému se příchozí a odchozí části stejný tok neshodují ve virtuálním počítači. Zásobník protokolu TCP nerozpozná tyto polovinami stejný tok jako součást stejný tok jako zdroje a cíle se neshodují.  Když tok mapuje na jakýkoli jiný virtuální počítač v back endového fondu, polovinami tok budou odpovídat a virtuálního počítače můžou úspěšně reagovat na tok.  Příznak pro tento scénář je přerušované připojení, vypršení časového limitu. Existuje několik běžných zástupná spolehlivě dosažení tohoto scénáře (pocházející toky z back endového fondu do back endu fondy příslušné interní nástroj pro vyrovnávání zatížení front-endu) mezi které patří buď vložení proxy třetích stran za interní zatížení Nástroje pro vyrovnávání nebo [pomocí pravidel stylu DSR](load-balancer-multivip-overview.md).  Zatímco veřejný Load Balancer můžete použít ke zmírnění, je výsledný scénář náchylná k [SNAT vyčerpání](load-balancer-outbound-connections.md#snat) a mělo by se vyhnout, pokud pečlivě spravované.

## <a name="next-steps"></a>Další postup

- Další informace o použití [Load balanceru úrovně Standard a zóny dostupnosti](load-balancer-standard-availability-zones.md)
- Další informace o [zóny dostupnosti](../availability-zones/az-overview.md).
- Další informace o [diagnostické nástroje pro vyrovnávání zatížení standardní](load-balancer-standard-diagnostics.md).
- Další informace o [podporované vícedimenzionálních metrik](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworkloadbalancers) pro diagnostiku v [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md).
- Další informace o použití [nástroje pro vyrovnávání zatížení pro odchozí připojení](load-balancer-outbound-connections.md)
- Další informace o [Load balanceru úrovně Standard s pravidla Vyrovnávání zatížení pro porty s vysokou DOSTUPNOSTÍ](load-balancer-ha-ports-overview.md)
- Další informace o použití [nástroje pro vyrovnávání zatížení s více front-Endů](load-balancer-multivip-overview.md)
- Další informace o [virtuálních sítí](../virtual-network/virtual-networks-overview.md).
- Další informace o [skupiny zabezpečení sítě](../virtual-network/security-overview.md).
- Další informace o [koncové body služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md)
- Přečtěte si o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.
- Další informace o [nástroje pro vyrovnávání zatížení](load-balancer-overview.md).
