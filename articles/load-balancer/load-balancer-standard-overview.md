---
title: Přehled služby Azure nástroj pro vyrovnávání zatížení | Microsoft Docs
description: Přehled funkcí Azure standardní nástroj pro vyrovnávání zatížení
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
ms.date: 05/03/2018
ms.author: kumud
ms.openlocfilehash: 648d96bbb18186524cfad4d3df1f61e98062fcb4
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="azure-load-balancer-standard-overview"></a>Přehled služby Azure standardní nástroje pro vyrovnávání zatížení

Azure nástroj pro vyrovnávání zatížení umožňuje škálování aplikací a vytvořte vysokou dostupnost pro vaše služby. Nástroj pro vyrovnávání zatížení lze použít pro příchozí i odchozí scénáře a nabízí nízkou latencí a vysokou propustnost a škáluje až miliony toků pro všechny aplikace, TCP a UDP. 

Tento článek se zaměřuje na nástroj pro vyrovnávání zatížení.  Další obecné přehled nástroje pro vyrovnávání zatížení Azure, zkontrolujte [přehled nástroje pro vyrovnávání zatížení](load-balancer-overview.md) také.

## <a name="what-is-standard-load-balancer"></a>Co je nástroj pro vyrovnávání zatížení?

Nástroj pro vyrovnávání zatížení je nový produkt nástroj pro vyrovnávání zatížení pro všechny TCP a UDP aplikací pomocí funkce ze rozšířené a podrobnější nastavení přes základní nástroj pro vyrovnávání zatížení.  Existuje mnoho podobností, je důležité se seznámit s rozdíly, jak je uvedeno v tomto článku.

Nástroj pro vyrovnávání zatížení můžete použít jako veřejný nebo interní Vyrovnávání zatížení. A virtuální počítač může být připojený k veřejné a jeden interní nástroj pro vyrovnávání zatížení prostředků.

Funkce Vyrovnávání zatížení prostředků jsou vždy vyjádřené jako front-end, pravidlo, test stavu a definici fondu back-end.  Prostředek může obsahovat více pravidel. Virtuální počítače můžete umístit do fondu back-end zadáním fondu back-end z prostředku Síťový adaptér virtuálního počítače.  V případě škálovací sadu virtuálních počítačů je tento parametr předána profilu sítě a rozšířit.

Jeden aspekt klíče je pro prostředek oboru virtuální sítě.  Existuje základní nástroj pro vyrovnávání zatížení v rámci oboru skupiny dostupnosti, standardní Vyrovnávání zatížení jsou plně integrované s oboru virtuální sítě a použít všechny koncepce virtuální sítě.

Prostředky nástroje pro vyrovnávání zatížení jsou objekty v rámci kterých můžete express, jak by měla Azure programu její infrastruktuře víceklientské k dosažení scénáře, který chcete vytvořit.  Není žádný přímý vztah mezi prostředky pro vyrovnávání zatížení a skutečný infrastruktury; vytvoření služby Vyrovnávání zatížení nepodporuje vytvoření instance, kapacita je vždy k dispozici a neexistují žádné spuštění nebo škálování zpoždění vzít v úvahu. 

>[!NOTE]
> Azure poskytuje sada plně spravovaná řešení pro vaše scénáře Vyrovnávání zatížení.  Pokud hledáte pro ukončení protokolu TLS ("přesměrování zpracování SSL") nebo za zpracování vrstvy aplikace žádosti protokolu HTTP nebo HTTPS, přečtěte si [Application Gateway](../application-gateway/application-gateway-introduction.md).  Pokud hledáte pro globální DNS Vyrovnávání zatížení, přečtěte si [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Vaše scénáře začátku do konce může těžit z kombinace těchto řešení podle potřeby.

## <a name="why-use-standard-load-balancer"></a>Proč používat nástroj pro vyrovnávání zatížení?

Nástroj pro vyrovnávání zatížení umožňuje škálování aplikací a vytvoření vysoké dostupnosti pro nasazení v menším měřítku do zóny s více architekturami velké a komplexní.

Projděte si v následující tabulce základní informace o rozdílech mezi nástroj pro vyrovnávání zatížení a základní nástroj pro vyrovnávání zatížení:

>[!NOTE]
> Nové návrhy měli zvážit použití nástroj pro vyrovnávání zatížení. 

| | Standardní SKU | Základní SKU |
| --- | --- | --- |
| Velikost fondu back-end | až 1 000 instancí | až 100 instancí |
| Koncové body fond back-end | jakýkoli virtuální počítač v jedné virtuální sítě, včetně blend virtuálních počítačů, skupiny dostupnosti, sadách škálování virtuálních počítačů. | nastavení virtuálních počítačů v jedné dostupnost sady nebo virtuální počítač škálování |
| Zóny dostupnosti | zónově redundantní a oblastmi frontends pro příchozí a odchozí, odchozí toky mapování zůstanou platné i po selhání zóny cross zóny Vyrovnávání zatížení | / |
| Diagnostika | Azure monitorování vícerozměrných metriky, včetně bajtů a čítače paketů, stav testu stavu, pokusy o připojení (TCP SYN), stav odchozí připojení (úspěšné i neúspěšné překládat pomocí SNAT toky), aktivní data roviny měření | Azure Log Analytics pouze veřejné Vyrovnávání zatížení, překládat pomocí SNAT vyčerpání výstraha, počet stavu fondu back-end |
| HA porty | Interní zátěže. | / |
| Ve výchozím nastavení zabezpečení | Výchozí zavřít pro veřejné IP adresy a nástroj pro vyrovnávání zatížení koncové body a skupiny zabezpečení sítě musí použije k explicitně povolených pro přenosy na toku | Výchozí otevřená, skupinu zabezpečení sítě volitelné |
| Odchozí připojení | Více frontends s každé pravidlo výslovný nesouhlas s. Odchozí scénář _musí_ explicitně vytvořit pro virtuální počítač, abyste mohli použít odchozí připojení.  [Koncové body služby virtuální síť](../virtual-network/virtual-network-service-endpoints-overview.md) dostupný bez odchozí připojení a zpracování dat není započítávat.  Všechny veřejné IP adresy, včetně služeb Azure PaaS není k dispozici jako koncové body služby virtuální sítě, musí být dosaženo přes odchozí připojení a počtu ke zpracování dat. Když virtuální počítač je obsluhuje pouze k interním pro vyrovnávání zatížení, nejsou k dispozici odchozí připojení přes výchozí překládat pomocí SNAT. Odchozí překládat pomocí SNAT programování je transportní protokol konkrétní na základě protokolu Příchozí pravidlo Vyrovnávání zatížení. | Jeden front-endu, náhodně vybrané, pokud existuje více frontends.  Když virtuální počítač je obsluhuje pouze interní nástroj pro vyrovnávání zatížení, použije se výchozí překládat pomocí SNAT. |
| Několik front-endů | Příchozí a odchozí | Pouze příchozí |
| Operace správy | Většinu operací < 30 sekund | 60-90 sekund typické |
| SLA | 99,99 % pro cestu k datům s dva virtuální počítače v pořádku | Implicitní v SLA k Virtuálním počítačům | 
| Ceny | Účtovat na základě počtu pravidel, zpracování dat příchozí nebo odchozí přidružené prostředků  | Bez poplatků |

Zkontrolujte [omezení služby pro vyrovnávání zatížení](https://aka.ms/lblimits), a také [ceny](https://aka.ms/lbpricing), a [SLA](https://aka.ms/lbsla).


### <a name="backend"></a>Fond back-end

Standardní nástroj pro vyrovnávání zatížení back-endové fondy rozšíří k jakémukoli prostředku virtuálního počítače ve virtuální síti.  Může obsahovat až 1000 instance back-end.  Instance back-end je konfiguraci IP adresy, což je vlastnost prostředku Síťový adaptér.

Back-endový fond může obsahovat samostatné virtuální počítače, skupiny dostupnosti nebo sady škálování virtuálního počítače.  Můžete inovativně prostředky ve fondu back-end a může obsahovat libovolnou kombinaci těchto prostředků až 150 celkový.

Při zvažování návrhu back-endového fondu, můžete navrhnout pro nejmenší počet jednotlivých back-end fondu prostředků optimalizovat trvání operace správy.  Není žádný rozdíl ve výkonu roviny dat nebo určený počet číslic.

## <a name="az"></a>Dostupnost zóny

Nástroj pro vyrovnávání zatížení podporuje další dalo v oblastech, kde dostupnost zóny jsou k dispozici.  Tyto funkce jsou přírůstkové všechny standardní Vyrovnávání zatížení poskytuje.  Konfigurace zón dostupnosti jsou k dispozici pro veřejné a interní standardní Vyrovnávání zatížení.

Bez oblastmi frontends stát zónově redundantní ve výchozím nastavení při nasazení v oblasti dostupnosti zón.   Zónově redundantní front-endu odolává selhání zóny a je vyhovovat vyhrazená infrastruktura, kterou ve všech zónách současně. 

Kromě toho může zaručit front-end pro konkrétní zónu. Oblastmi front-endu sdílí rozklad s příslušnými zóny a je obsluhuje pouze vyhrazená infrastruktura, kterou v jedné oblasti.

Vyrovnávání zatížení mezi zóny je dostupná pro fond back-end a všechny prostředek virtuálního počítače ve virtuální síti můžou být součástí fondu back-end.

Zkontrolujte [podrobnou diskuzi o dostupnosti zóny související dalo](load-balancer-standard-availability-zones.md).

### <a name="diagnostics"></a> Diagnostika

Nástroj pro vyrovnávání zatížení poskytuje vícerozměrných metriky prostřednictvím Azure monitorování.  Tyto metriky lze filtrovat, seskupené a zadejte aktuální a historický přehled o výkonu a stavu služby.  Stav prostředku je také podporována.  Toto je stručný přehled podporovaných diagnostiky:

| Metrika | Popis |
| --- | --- |
| Dostupnost virtuálních IP adres | Standardní nástroje pro vyrovnávání zatížení vykonává nepřetržitě cesty data z v rámci oblasti na front-endu úplně se zásobníkem SDN, který podporuje virtuální počítač nástroje pro vyrovnávání zatížení. Také v pořádku instancí zůstanou, následuje měření stejnou cestu jako Vyrovnávání zatížení sítě provoz vaší aplikace. Také ověřuje cestu datům, která je používána zákazníky. Měření neviditelná do vaší aplikace a nebudou v konfliktu s dalšími operacemi.|
| Dostupnost vyhrazené IP adresy | Standardní nástroje pro vyrovnávání zatížení používá distribuované stavu zjišťování služby, která monitoruje stav váš koncový bod aplikace podle nastavení konfigurace. Tato metrika poskytuje agregaci nebo na koncový bod filtrované zobrazení každé jednotlivé instance koncového bodu nástroji pro vyrovnávání zatížení fondu.  Uvidíte, jak nástroj pro vyrovnávání zatížení zobrazení stavu aplikace podle vašeho Konfigurace testu stavu.
| SYN paketů | Standardní nástroje pro vyrovnávání zatížení nepodporuje ukončit připojení TCP nebo interakci s TCP nebo UDP paketů toky. Toky a jejich metodou handshake jsou vždy mezi zdrojovým a instance virtuálního počítače. Chcete-li lépe vyřešit vaše scénáře protokolu TCP, je nutné používat SYN čítače pakety zjistit, kolik připojení TCP se pokusy. Metrika hlásí počet paketů TCP SYN, které byly přijaty.|
| Překládat pomocí SNAT připojení | Standardní nástroje pro vyrovnávání zatížení hlásí počet odchozí toky, které jsou masqueraded front-end veřejné IP adresy. Překládat pomocí SNAT porty jsou vyčerpatelným prostředků. Tato metrika mohou poskytnout údaj o tom, jak výraznou je vaše aplikace spoléhat na překládat pomocí SNAT pro odchozí toky původu.  Čítače pro úspěšné i neúspěšné odchozí toky překládat pomocí SNAT hlášení a slouží k řešení potíží a pochopení stavu odchozích toků.|
| Čítače bajtů | Standardní nástroje pro vyrovnávání zatížení sestavy dat zpracovaných za front-endu.|
| Čítače paketů | Standardní nástroje pro vyrovnávání zatížení hlásí paketů zpracovaných za front-endu.|

Zkontrolujte [podrobné informace o standardního diagnostického nástroje pro vyrovnávání zatížení](load-balancer-standard-diagnostics.md).

### <a name="haports"></a>HA porty

Nástroj pro vyrovnávání zatížení podporuje nový typ pravidla.  

Můžete nakonfigurovat pravidla a ujistěte se, vaše aplikace škálování za vysoce spolehlivé Vyrovnávání zatížení. Když použijete pravidlo, standardní Vyrovnávání zatížení vyrovnávání zatížení HA porty poskytne za každých dočasných portů k interní standardní Vyrovnávání zatížení na front-endovou IP adresu pro vyrovnávání zatížení toku.  Tato funkce je užitečná pro scénáře, kde je nepraktické nebo žádoucí, aby zadat jednotlivé porty.

Pravidlo Vyrovnávání zatížení HA porty umožňuje vytvářet aktivní pasivní nebo aktivní aktivní n + 1 scénáře pro sítě virtuálních zařízení a všechny aplikace, které vyžaduje velkých oblastí příchozí porty.  Test stavu slouží k určení, které back-EndY by měl přijetí nové toky.  Skupina zabezpečení sítě můžete emulovat scénáři rozsah portů.

>[!IMPORTANT]
> Pokud máte v úmyslu použít virtuální zařízení sítě, poraďte s dodavatelem pro informace o tom, jestli byl testovaný svých produktech s HA porty a postupujte podle jejich konkrétní pokyny pro implementaci. 

Zkontrolujte [podrobné diskuzi o HA porty](load-balancer-ha-ports-overview.md).

### <a name="securebydefault"></a>Ve výchozím nastavení zabezpečení

Nástroj pro vyrovnávání zatížení je plně zařazený, nemá k virtuální síti.  Virtuální síť je uzavřené, privátní síť.  Protože nástroje pro vyrovnávání zatížení a veřejné IP adresy jsou navržená tak, aby této virtuální síti nelze přistupovat ze mimo virtuální síť, tyto prostředky teď výchozí uzavřen, pokud je otevřete. To znamená, že skupiny zabezpečení sítě (Nsg) se nyní používají tak, aby výslovně povolovala a seznam povolených adres povolené přenosy.  Můžete vytvořit vaše virtuální celého datového centra a rozhodnout prostřednictvím NSG, co a pokud by měl být k dispozici.  Pokud nemáte skupinu NSG na podsítě nebo síťová karta tohoto prostředku virtuálního počítače, jsme nebude povolit provoz k dosažení tohoto prostředku.

Další informace o skupiny Nsg a způsobu jejich použití pro váš scénář naleznete v tématu [skupin zabezpečení sítě](../virtual-network/virtual-networks-nsg.md).

### <a name="outbound"></a> Odchozí připojení

Nástroj pro vyrovnávání zatížení podporuje scénáře příchozí a odchozí.  Nástroj pro vyrovnávání zatížení se výrazně liší pro základní nástroj pro vyrovnávání zatížení s ohledem na odchozí připojení.

Zdroj síťové adresy překlad (SNAT) se používá k mapování interní a privátní IP adresa ve vaší virtuální síti na veřejné IP adresy na frontends nástroj pro vyrovnávání zatížení.

Zavádí nový algoritmus pro nástroj pro vyrovnávání zatížení [robustní, škálovatelnou a předvídatelný překládat pomocí SNAT algoritmus](load-balancer-outbound-connections.md#snat) a nové schopnosti povolí, odebere nejednoznačnosti a vynutí explicitní konfigurace místo straně účinky. Tyto změny jsou nezbytné pro povolení pro nové funkce, které vznikat. 

Toto jsou klíčové principů pamatovat při práci s nástroj pro vyrovnávání zatížení:

- dokončení pravidla jednotky prostředek pro vyrovnávání zatížení.  všechny programování Azure je odvozena z její konfiguraci.
- Když jsou k dispozici více frontends, se používají všechny frontends a každý front-endu vynásobí počet dostupných portů překládat pomocí SNAT
- můžete vybrat a řízení, pokud nechcete, aby pro konkrétní front-endu má být použit pro odchozí připojení.
- odchozí scénáře jsou explicitní a odchozí připojení neexistuje, dokud není zadán.
- pravidla Vyrovnávání zatížení odvození, jak programovat překládat pomocí SNAT. Pravidla Vyrovnávání zatížení jsou specifická pro protokol. Překládat pomocí SNAT je specifická pro protokol a konfigurace musí tuto skutečnost místo vytvoření vedlejším účinkem.

#### <a name="multiple-frontends"></a>Několik front-endů
Pokud chcete další překládat pomocí SNAT porty, protože je očekávána nebo se již vyskytuje vysokého zatížení pro odchozí připojení, můžete také přidat přírůstkové inventáře port překládat pomocí SNAT nakonfigurováním dalších frontends, pravidla a back-endové fondy stejný virtuální počítač prostředky.

#### <a name="control-which-frontend-is-used-for-outbound"></a>Ovládací prvek front-end, který se používá pro odchozí
Pokud chcete omezit odchozí připojení k provádět jenom na konkrétní front-endovou IP adresu, můžete volitelně můžete zakázat odchozí překládat pomocí SNAT na pravidlo, které vyjadřoval odchozí mapování.

#### <a name="control-outbound-connectivity"></a>Ovládací prvek odchozí připojení
Nástroj pro vyrovnávání zatížení v rámci virtuální sítě existuje.  Virtuální síť se izolované privátní síti.  Pokud existuje přidružení s veřejnou IP adresu, veřejné připojení není povoleno.  Můžete uživatele oslovit [koncové body služby virtuální síť](../virtual-network/virtual-network-service-endpoints-overview.md) vzhledem k tomu, že jsou uvnitř a místní k virtuální síti.  Pokud chcete navázat odchozí připojení do cílového umístění mimo virtuální síť, máte dvě možnosti:
- Přiřaďte standardní SKU veřejnou IP adresu jako veřejná IP adresa na úrovni Instance adresu pro prostředek virtuálního počítače nebo
- Umístěte prostředek virtuálního počítače ve fondu back-end veřejný Standard pro vyrovnávání zatížení.

Jak vám umožní odchozí připojení z virtuální sítě, abyste mimo virtuální síť. 

Pokud jste _pouze_ mít k interní standardní pro vyrovnávání zatížení přidružené k fondu back-end, ve kterém se nachází váš prostředek virtuálního počítače, virtuální počítač můžete mít pouze přístup k prostředkům virtuální sítě a [virtuální sítě služby Koncové body](../virtual-network/virtual-network-service-endpoints-overview.md).  Můžete postupovat podle kroků popsaných v předchozím odstavci vytvořit odchozí připojení.

Odchozí připojení není přidružený standardní SKU zůstanou jako před prostředku virtuálního počítače.

Zkontrolujte [podrobné informace o odchozí připojení](load-balancer-outbound-connections.md).

### <a name="multife"></a>Více frontends
Nástroj pro vyrovnávání zatížení podporuje více pravidel s více frontends.  Nástroj pro vyrovnávání zatížení rozšíří to odchozí scénářů.  Odchozí scénáře jsou v podstatě inverzní příchozí pravidlo Vyrovnávání zatížení.  Příchozí pravidlo také Vyrovnávání zatížení vytváří spolupracovníkovi pro odchozí připojení. Standardní Vyrovnávání zatížení používá všechny frontends přidružený prostředek virtuálního počítače prostřednictvím pravidlo Vyrovnávání zatížení.  Kromě toho parametr na Vyrovnávání zatížení pravidla a umožňuje potlačit pro účely odchozí připojení, která umožňuje výběr specifické frontends včetně žádné pravidlo Vyrovnávání zatížení.

Pro porovnání základní nástroje pro vyrovnávání zatížení náhodně vybere jeden front-endu a neexistuje žádná možnost řídit, která nebyla vybrána.

Zkontrolujte [podrobné informace o odchozí připojení](load-balancer-outbound-connections.md).

### <a name="operations"></a> Operace správy

Standardní prostředky aplikace pro vyrovnávání zatížení existovat na úplně novou infrastrukturu platformě.  To umožňuje výrazně rychlejší operace správy pro standardní SKU a dokončení časy jsou obvykle méně než 30 sekund na standardní SKU prostředků.  Upozorňujeme, že back-endové fondy zvýšit velikost, doba trvání požadované pro back-end fondu změní také zvýšení.

Můžete upravit prostředky nástroj pro vyrovnávání zatížení a přesunutí standardní veřejnou IP adresu z jednoho virtuálního počítače na jiný mnohem rychlejší.

## <a name="migration-between-skus"></a>Migrace mezi SKU

SKU nejsou měnitelný. Postupujte podle kroků v této části přesunuty z jednoho prostředku SKU do jiného.

>[!IMPORTANT]
>Zkontrolujte tento dokument a pochopit rozdíly mezi SKU a pečlivě zkoumat, váš scénář.  Budete muset udělat další změny, chcete-li zarovnat váš scénář.

### <a name="migrate-from-basic-to-standard-sku"></a>Migrovat ze základní standardní SKU

1. Vytvoření nového prostředku standardní (nástroj pro vyrovnávání zatížení a veřejné IP adresy, podle potřeby). Pravidla znovu vytvořit a sběru dat definice.

2. Vytvořit nový nebo aktualizovat existující skupina NSG na síťový adaptér nebo podsíť, do seznamu povolených IP adres skupinu s vyrovnáváním zatížení provozu, test, stejně jako všechny ostatní přenosy, který chcete povolit.

3. Základní SKU prostředků (nástroj pro vyrovnávání zatížení a veřejné IP adresy, podle vhodnosti) odeberte ze všech instancí virtuálních počítačů. Nezapomeňte také odebrat všechny instance virtuálních počítačů sady dostupnosti.

4. Připojte všechny instance virtuálních počítačů k nové prostředky standardní SKU.

### <a name="migrate-from-standard-to-basic-sku"></a>Migrace z Standard na základní SKU

1. Vytvoření nového prostředku základní (nástroj pro vyrovnávání zatížení a veřejné IP adresy, podle potřeby). Pravidla znovu vytvořit a sběru dat definice. 

2. Standardní SKU prostředků (nástroj pro vyrovnávání zatížení a veřejné IP adresy, podle vhodnosti) odeberte ze všech instancí virtuálních počítačů. Nezapomeňte také odebrat všechny instance virtuálních počítačů sady dostupnosti.

3. Připojte všechny instance virtuálních počítačů k nové prostředky základní SKU.

>[!IMPORTANT]
>
>Existují omezení týkající se použití Basic a standardní SKU.
>
>HA porty a diagnostiky standardní SKU jsou k dispozici pouze v standardní SKU. Nelze migrovat ze standardní SKU základní SKU a uchovávají taky tyto funkce.
>
>Základní a standardní SKU mít počet rozdíly, jak je uvedeno v tomto článku.  Zajistěte, aby pochopení a příprava pro ně.
>
>Odpovídající identifikátory SKU musí použít pro nástroj pro vyrovnávání zatížení a veřejnou IP adresu prostředky. Nemůžete mít směs základní SKU a standardní SKU prostředků. Nelze připojit samostatné virtuální počítače, virtuální počítače v prostředek sadu dostupnosti nebo škálování virtuálního počítače nastavte prostředky na obě položky současně.

## <a name="region-availability"></a>Dostupnost v oblastech

Standardní nástroje pro vyrovnávání zatížení je aktuálně k dispozici ve všech oblastech veřejného cloudu.

## <a name="sla"></a>SLA

Nástroje pro vyrovnávání zatížení jsou k dispozici SLA 99,99 %.  Zkontrolujte [standardní SLA nástroje pro vyrovnávání zatížení](https://aka.ms/lbsla) podrobnosti.

## <a name="pricing"></a>Ceny

Standardní Vyrovnávání zatížení je odečtena produkt na základě počtu Vyrovnávání zatížení nakonfigurovaná pravidla a všechny příchozí a odchozí data zpracování. Informace o cenách standardní Vyrovnávání zatížení, najdete v článku [ceny služby Vyrovnávání zatížení](https://aka.ms/lbpricing) stránky.

## <a name="limitations"></a>Omezení

- SKU nejsou měnitelný. Skladová položka existující prostředek nesmí změnit.
- Prostředek virtuálního počítače samostatnou sadu dostupnosti prostředků nebo prostředek sady škálování virtuálního počítače, můžete odkazovat jednoho identifikátoru SKU, nikdy obě.
- Pravidlo Vyrovnávání zatížení nemůžou zahrnovat dvě virtuální sítě.  Frontends a jejich související back-end instance se musí nacházet ve stejné virtuální síti.  
- Frontends nástroje pro vyrovnávání zatížení nejsou přístupné napříč vztahy globální virtuální sítě.
- [Přesunutí operací předplatné](../azure-resource-manager/resource-group-move-resources.md) nejsou podporovány pro standardní SKU LB a PIP prostředky.
- Webové role pracovního procesu bez virtuální sítě a další služby platformy Microsoft může být přístupné, pokud se používá pouze k interním standardní pro vyrovnávání zatížení z důvodu vedlejším účinkem z jak pre-VNet služby a jiné platformy služeb funkce. Nesmí spoléhají na to, jak příslušné služby sám sebe nebo základní platformy můžete změnit bez předchozího upozornění. Musí vždy předpokládat, budete muset vytvořit [odchozí připojení](load-balancer-outbound-connections.md) explicitně v případě, že při použití k interní standardní pro vyrovnávání zatížení jenom.
- Nástroj pro vyrovnávání zatížení je produkt TCP nebo UDP pro vyrovnávání zatížení a předávání port pro tyto konkrétní protokoly IP.  Pravidla Vyrovnávání zatížení a příchozího pravidla NAT jsou podporované pro TCP a UDP a není podporován pro jiné protokoly IP včetně protokolu ICMP. Nástroj pro vyrovnávání zatížení nezavře, reagovat nebo jinak interakci s datovou část toku UDP nebo TCP. Není proxy server. Úspěšné ověření připojení k front-endu vyžaduje místní integrované s stejný protokol použitý v vyrovnávání nebo příchozí pravidlo NAT zatížení (TCP nebo UDP) _a_ alespoň jeden z vašich virtuálních počítačů musíte vygenerovat odpověď pro klienta Chcete-li zobrazit na odpověď od front-endu.  Nepřijaté odpovědi integrované z front-endu nástroje pro vyrovnávání zatížení označuje, že žádné virtuální počítače nebyly schopné reagovat.  Není možné pracovat s front-endu bez schopné reagovat virtuálního počítače pro vyrovnávání zatížení.  To platí také pro odchozí připojení kde [port maskovaným překládat pomocí SNAT](load-balancer-outbound-connections.md#snat) je podporována pouze pro TCP a UDP; žádné jiné protokoly IP, včetně ICMP se také nezdaří.  Přiřaďte veřejná IP adresa úrovni instance zmírnit.
- Na rozdíl od veřejných Vyrovnávání zatížení, které poskytují [odchozí připojení](load-balancer-outbound-connections.md) při přechodu ze soukromých IP adres ve virtuální síti na veřejné IP adresy, interní nástroje pro vyrovnávání zatížení nepřevádět odchozí pochází připojení k front-endu interní pro vyrovnávání zatížení jako obě jsou prostor privátní IP adresy.  Tím je zabráněno případným překládat pomocí SNAT vyčerpání uvnitř jedinečné interní adresní prostor IP adres kde překlad se nevyžaduje.  Vedlejším účinkem je, že pokud odchozího toku z virtuálního počítače ve fondu back-end pokusů o toku do front-endu interní služby Vyrovnávání zatížení ve fondu, který se nachází _a_ je namapovaný na sebe, oba úsecích toku neshodují a tok se nezdaří. .  Pokud toku nemapují zpět do stejného virtuálního počítače ve fondu back-end, který vytvoří toku front-endu, bude úspěšné toku.   Když toku mapuje sám na sebe odchozího toku pravděpodobně pocházet z virtuálního počítače do front-endu a odpovídající příchozí tok, zobrazí se jenom z virtuálního počítače na sebe sama. Z hostovaného operačního systému se příchozí a odchozí součástí stejného toku neshodují ve virtuálním počítači. Zásobník protokolu TCP nerozpozná tyto polovina stejného toku jako součást stejného toku jako zdroj a cíl se neshodují.  Při toku mapuje k žádné virtuální počítače ve fondu back-end, bude odpovídat polovina toku a virtuální počítač může úspěšně reagovat na toku.  Tento příznak pro tento scénář je časové limity nepřerušované připojení. Existuje několik běžných dočasných řešení pro tento scénář spolehlivě dosažení (pocházející toky z back-end fondu k back-end fondu příslušné interní nástroj pro vyrovnávání zatížení front-endu) mezi které patří buď vložení proxy třetích stran za interní zatížení Nástroje pro vyrovnávání nebo [pomocí pravidel stylu DSR](load-balancer-multivip-overview.md).  Zatímco veřejný Vyrovnávání zatížení můžete použít ke zmírnění, výsledná scénář je náchylné k [vyčerpání překládat pomocí SNAT](load-balancer-outbound-connections.md#snat) a je nutno Pokud pečlivě spravované.

## <a name="next-steps"></a>Další postup

- Další informace o použití [dostupnost zóny a nástroj pro vyrovnávání zatížení](load-balancer-standard-availability-zones.md)
- Další informace o [dostupnost zóny](../availability-zones/az-overview.md).
- Další informace o [diagnostické nástroje pro vyrovnávání zatížení standardní](load-balancer-standard-diagnostics.md).
- Další informace o [podporované vícerozměrných metriky](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworkloadbalancers) pro diagnostiku v [Azure monitorování](../monitoring-and-diagnostics/monitoring-overview.md).
- Další informace o použití [Vyrovnávání zatížení pro odchozí připojení](load-balancer-outbound-connections.md)
- Další informace o [standardní Vyrovnávání zatížení s pravidla Vyrovnávání zatížení HA porty](load-balancer-ha-ports-overview.md)
- Další informace o použití [Vyrovnávání zatížení s více Frontends](load-balancer-multivip-overview.md)
- Další informace o [virtuální sítě](../virtual-network/virtual-networks-overview.md).
- Další informace o [skupin zabezpečení sítě](../virtual-network/virtual-networks-nsg.md).
- Další informace o [koncové body služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md)
- Přečtěte si o některých dalších klíče [sítě možnosti](../networking/networking-overview.md) v Azure.
- Další informace o [nástroj pro vyrovnávání zatížení](load-balancer-overview.md).
