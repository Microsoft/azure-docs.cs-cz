---
title: Co je Azure Standard Load Balancer?
titleSuffix: Azure Load Balancer
description: Pomocí této cesty výukového programu začněte s přehledem funkcí Azure Standard Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: allensu
ms.openlocfilehash: 36035d844237115361ccb3c1e1bc0dcb91250881
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423869"
---
# <a name="azure-standard-load-balancer-overview"></a>Přehled služby Azure Standard Load Balancer

Azure Load Balancer vám umožní škálovat aplikace a vytvořit vysokou dostupnost pro vaše služby. Load Balancer lze použít pro příchozí i odchozí scénáře a poskytuje nízkou latenci, vysokou propustnost a škáluje až milionů toků pro všechny aplikace TCP a UDP. 

Tento článek se zaměřuje na Standard Load Balancer.  Obecnější přehled Azure Load Balancer najdete také v článku [Load Balancer Overview](load-balancer-overview.md) .

## <a name="what-is-standard-load-balancer"></a>Co je Standard Load Balancer?

Standard Load Balancer je nový Load Balancer produkt pro všechny aplikace TCP a UDP s rozšířenou a podrobnější funkcí nastavenou nad základní Load Balancer.  I když existuje hodně podobnosti, je důležité se seznámit s rozdíly, jak je uvedeno v tomto článku.

Standard Load Balancer můžete použít jako veřejný nebo interní Load Balancer. A virtuální počítač může být připojen k jednomu veřejnému a jednomu internímu prostředku Load Balancer.

Funkce prostředku Load Balancer se vždycky vyjadřují jako front-end, pravidlo, sonda stavu a definice fondu back-endu.  Prostředek může obsahovat více pravidel. Virtuální počítače můžete umístit do back-endu fondu tím, že zadáte back-end fond z prostředku síťové karty virtuálního počítače.  Tento parametr se předává přes síťový profil a při použití Virtual Machine Scale Sets se rozšíří.

Jedním z klíčových aspektů je obor virtuální sítě pro daný prostředek.  I když základní Load Balancer existují v oboru skupiny dostupnosti, Standard Load Balancer je plně integrovaná s oborem virtuální sítě a platí všechny koncepce virtuálních sítí.

Prostředky Load Balancer jsou objekty, ve kterých můžete vyjádřit, jak by měl Azure naprogramovat svoji víceklientské infrastrukturu, aby dosáhli scénáře, který chcete vytvořit.  Mezi prostředky Load Balancer a skutečnou infrastrukturou neexistuje žádný přímý vztah. vytvořením Load Balancer není vytvořena instance, kapacita je vždy k dispozici a neexistuje žádná prodleva při počátečním nebo škálování, která by bylo možné zvážit. 

>[!NOTE]
> Azure pro vaše scénáře poskytuje sadu plně spravovaných řešení pro vyrovnávání zatížení. Pokud hledáte žádost o ukončení protokolu TLS (Transport Layer Security) ("snižování zátěže SSL") nebo požadavek na protokol HTTP/HTTPS, zpracování aplikační vrstvy, přečtěte si téma [co je Azure Application Gateway?](../application-gateway/overview.md) Pokud hledáte globální vyrovnávání zatížení DNS, přečtěte si téma [co je Traffic Manager?](../traffic-manager/traffic-manager-overview.md) Vaše ucelené scénáře můžou využít kombinaci těchto řešení.
>
> Porovnání možností vyrovnávání zatížení Azure najdete v tématu [Přehled možností vyrovnávání zatížení v Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

## <a name="why-use-standard-load-balancer"></a>Proč použít Standard Load Balancer?

Standard Load Balancer umožňuje škálovat aplikace a zajistit vysokou dostupnost pro nasazení v malém měřítku až po rozsáhlé a složité architektury s více zónami.

Přehled rozdílů mezi Standard Load Balancer a základními Load Balancer najdete v následující tabulce:

>[!NOTE]
> V nových návrzích by se měl používat Load Balancer úrovně Standard. 

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Projděte si [omezení služby pro Load Balancer](https://aka.ms/lblimits)a [ceny](https://aka.ms/lbpricing)a [smlouvy SLA](https://aka.ms/lbsla).


### <a name="backend"></a>Back-end fond

Standard Load Balancer fondy back-endu se rozšiřují do libovolného prostředku virtuálního počítače ve virtuální síti.  Může obsahovat až 1000 back-end instancí.  Instance back-endu je konfigurace protokolu IP, což je vlastnost prostředku síťové karty.

Back-end fond může obsahovat samostatné virtuální počítače, skupiny dostupnosti nebo sady škálování virtuálních počítačů.  Prostředky můžete také prolnout ve fondu back-endu. Můžete zkombinovat až 150 prostředků ve fondu back-endu pro každý Load Balancer prostředek.

Při zvažování, jak navrhnout back-end fond, můžete navrhnout minimální počet jednotlivých prostředků fondu back-end serveru a dále tak optimalizovat dobu trvání operací správy.  Nedochází k žádnému rozdílu nad výkonem nebo škálováním roviny dat.

### <a name="probes"></a>Sondy stavu
  
Standard Load Balancer přidává podporu [sond stavu https](load-balancer-custom-probe-overview.md#httpprobe) (Test http s obálkou TLS (Transport Layer Security)), aby bylo možné přesně monitorovat aplikace https.  

Kromě toho, když se test celého fondu back-endu [vypíná](load-balancer-custom-probe-overview.md#probedown), Standard Load Balancer umožní pokračovat v navázání připojení TCP. (Základní Load Balancer ukončí všechna připojení TCP ke všem instancím).

Podrobnosti najdete v [Load Balancer sondy stavu](load-balancer-custom-probe-overview.md) .

### <a name="az"></a>Zóny dostupnosti

>[!IMPORTANT]
>V tématu [zóny dostupnosti](../availability-zones/az-overview.md) najdete související témata, včetně informací o konkrétní oblasti.

Standard Load Balancer podporuje další možnosti v oblastech, kde jsou dostupné Zóny dostupnosti.  Tyto funkce jsou přírůstkové na všechny Standard Load Balancer.  Zóny dostupnosti konfigurace jsou k dispozici pro veřejné a interní Standard Load Balancer.

Ve výchozím nastavení se při nasazení v oblasti s Zóny dostupnosti stanou frontami bez oblasti.   Zóna redundantní front-end zaznamená selhání zóny a obsluhuje vyhrazenou infrastrukturu ve všech zónách současně. 

Navíc můžete zaručit front-end pro konkrétní zónu. Oblast front-endu se podílí s příslušnou zónou a je obsluhována pouze pomocí vyhrazené infrastruktury v jedné zóně.

Pro back-end fond je k dispozici vyrovnávání zatížení mezi zónami a každý prostředek virtuálního počítače ve virtuální síti může být součástí fondu back-endu.

Přečtěte si [podrobnou diskuzi o schopnostech souvisejících s zóny dostupnosti](load-balancer-standard-availability-zones.md).

### <a name="diagnostics"></a>Diagnostiky

Standard Load Balancer poskytuje multidimenzionální metriky prostřednictvím Azure Monitor.  Tyto metriky je možné filtrovat, seskupovat a rozdělit pro danou dimenzi.  Poskytují aktuální a historické poznatky o výkonu a stavu vaší služby.  Podporuje se taky Resource Health.  Následuje stručný přehled podporované diagnostiky:

| Metrika | Popis |
| --- | --- |
| Dostupnost virtuální IP adresy | Standard Load Balancer nepřetržitě vykonává cestu k datům z oblasti do Load Balancer front-endu všech cest k sadě SDN, která podporuje váš virtuální počítač. Pokud zůstanou instance v pořádku, měření se řídí stejnou cestou jako provoz s vyrovnáváním zatížení vaší aplikace. Také se ověří cesta k datům, kterou používají vaši zákazníci. Měření je pro vaši aplikaci neviditelné a neovlivňuje jiné operace.|
| DIP – dostupnost | Standard Load Balancer používá distribuovanou službu pro zjišťování stavu, která monitoruje stav koncového bodu vaší aplikace podle nastavení konfigurace. Tato metrika poskytuje agregované nebo na koncové body filtrované – zobrazení každého koncového bodu jednotlivých instancí ve fondu Load Balancer.  Můžete zjistit, jak Load Balancer zobrazení stavu aplikace, jak je uvedeno v konfiguraci sondy stavu.
| Pakety SYN | Standard Load Balancer neukončuje připojení TCP nebo komunikují s toky paketů TCP a UDP. Toky a jejich metody handshake jsou vždy mezi zdrojem a instancí virtuálního počítače. K lepšímu řešení potíží se scénáři protokolu TCP můžete použít čítače paketů SYN, abyste pochopili, kolik pokusů o připojení TCP je prováděno. Metrika hlásí počet přijatých paketů TCP SYN.|
| Připojení SNAT | Standard Load Balancer hlásí počet odchozích toků, které jsou maskované na front-endu veřejné IP adresy. Porty SNAT jsou prostředek exhaustible. Tato metrika vám může poskytnout informace o tom, jak intenzivně se aplikace spoléhá na SNAT pro odchozí vzniklé toky.  Čítače pro úspěšné a neúspěšné odchozí toky SNAT jsou hlášeny a lze je použít k řešení potíží a pochopení stavu odchozích toků.|
| Čítače bajtů | Standard Load Balancer hlásí data zpracovaná za front-end.|
| Čítače paketů | Standard Load Balancer hlásí zpracované pakety za front-endu.|

Přečtěte si [podrobnou diskuzi o diagnostice Standard Load Balancer](load-balancer-standard-diagnostics.md).

### <a name="haports"></a>Porty HA

Standard Load Balancer podporuje nový typ pravidla.  

Můžete nakonfigurovat pravidla vyrovnávání zatížení, která zajistí škálování aplikace a bude vysoce spolehlivá. Když použijete pravidlo vyrovnávání zatížení s porty HA, Standard Load Balancer bude poskytovat vyrovnávání zatížení na každém dočasném portu IP adresy interního Standard Load Balancer front-endu.  Tato funkce je užitečná pro jiné scénáře, kdy je nepraktické nebo nežádoucí určení jednotlivých portů.

Pravidlo vyrovnávání zatížení portů HA vám umožní vytvářet aktivní – pasivní nebo aktivní – aktivní n + 1 scénáře pro síťová virtuální zařízení a všechny aplikace, které vyžadují velké rozsahy vstupních portů.  Sondu stavu lze použít k určení, které back-endy by měly dostávat nové toky.  Skupinu zabezpečení sítě můžete použít k emulaci scénáře rozsahu portů.

>[!IMPORTANT]
> Pokud plánujete použít síťové virtuální zařízení, obraťte se na dodavatele a Projděte si informace o tom, jestli se jejich produkt testuje pomocí portů HA, a postupujte podle svých specifických pokynů k implementaci. 

Přečtěte si [podrobnou diskuzi o portech ha](load-balancer-ha-ports-overview.md).

### <a name="securebydefault"></a>Zabezpečení ve výchozím nastavení

Standard Load Balancer je plně připojená k virtuální síti.  Virtuální síť je privátní, uzavřená síť.  Vzhledem k tomu, že standardní nástroje pro vyrovnávání zatížení a standardní veřejné IP adresy jsou navržené tak, aby se k této virtuální síti mělo přistup mimo virtuální síť, teď se tyto prostředky standardně zavřou, pokud je neotevřete. To znamená, že skupiny zabezpečení sítě (skupin zabezpečení sítě) se teď používají k explicitnímu povolení a povolenému provozu.  Můžete vytvořit celé virtuální datové centrum a rozhodnout NSG, co a kdy má být k dispozici.  Pokud nemáte NSG v podsíti nebo síťové kartě prostředku virtuálního počítače, přenosy nepovolují přístup k tomuto prostředku.

Další informace o skupin zabezpečení sítě a o tom, jak je použít pro váš scénář, najdete v tématu [skupiny zabezpečení sítě](../virtual-network/security-overview.md).

### <a name="outbound"></a>Odchozí připojení

Load Balancer podporuje scénáře příchozího a odchozího přenosu.  Standard Load Balancer se výrazně liší od základní Load Balancer s ohledem na odchozí připojení.

Ke mapování interních privátních IP adres ve virtuální síti na veřejné IP adresy na Load Balancer front-endu se používá zdrojový překlad adresy (SNAT).

Standard Load Balancer zavádí nový algoritmus pro robustnější [, škálovatelný a předvídatelný algoritmus SNAT](load-balancer-outbound-connections.md#snat) , který umožňuje nové možnosti, odebírá nejednoznačnost a spíše vynutí explicitní konfigurace namísto vedlejších účinků. Tyto změny jsou nezbytné k tomu, aby bylo možné vymezit nové funkce. 

Toto je klíčová principy, která se zapamatuje při práci s Standard Load Balancer:

- dokončení pravidla řídí prostředek Load Balancer.  veškeré programování Azure je odvozeno z jeho konfigurace.
- Pokud je k dispozici více front-endu, použijí se všechny front-endové a každý front-end vynásobí počet dostupných portů SNAT.
- můžete zvolit a řídit, jestli nechcete, aby se pro odchozí připojení používal konkrétní front-end.
- odchozí scénáře jsou explicitní a odchozí připojení neexistuje, dokud není zadané.
- pravidla vyrovnávání zatížení odvozují, jak je SNAT naprogramováno. Pravidla vyrovnávání zatížení jsou specifická pro protokol. SNAT je specifická pro protokol a konfigurace by se měla projevit místo vytvoření vedlejšího efektu.

#### <a name="multiple-frontends"></a>Několik front-endů
Pokud potřebujete další porty SNAT, protože očekáváte nebo už máte vysoký požadavek na odchozí připojení, můžete taky přidat přírůstkový inventář portů SNAT tím, že nakonfigurujete další front-endové, pravidla a back-end fondy na stejný virtuální počítač. prostředky.

#### <a name="control-which-frontend-is-used-for-outbound"></a>Řízení, který front-end používá pro odchozí připojení
Pokud chcete omezit odchozí připojení jenom na základě konkrétní IP adresy front-endu, můžete pro pravidlo, které vyjadřuje odchozí mapování, volitelně zakázat odchozí SNAT.

#### <a name="control-outbound-connectivity"></a>Řízení odchozího připojení
Standard Load Balancer existuje v kontextu virtuální sítě.  Virtuální síť je izolovaná privátní síť.  Pokud neexistuje přidružení s veřejnou IP adresou, není veřejné připojení povoleno.  Můžete získat přístup k [koncovým bodům služby virtuální](../virtual-network/virtual-network-service-endpoints-overview.md) sítě, protože jsou uvnitř a místní k vaší virtuální síti.  Pokud chcete vytvořit odchozí připojení k cíli mimo vaši virtuální síť, máte dvě možnosti:
- Přiřaďte veřejnou IP adresu standardní SKU jako veřejnou IP adresu na úrovni instance k prostředku virtuálního počítače nebo
- Umístěte prostředek virtuálního počítače do back-endu fondu veřejných Standard Load Balancer.

Oba umožní odchozí připojení z virtuální sítě k mimo virtuální síť. 

Pokud máte k back-endu, ve kterém je umístěný prostředek virtuálního počítače, přidružená _jenom_ interní standard Load Balancer, váš virtuální počítač může dosáhnout jenom virtuálních síťových prostředků a [koncových bodů služby](../virtual-network/virtual-network-service-endpoints-overview.md)virtuální sítě.  Pro vytvoření odchozího připojení můžete postupovat podle kroků popsaných v předchozím odstavci.

Odchozí připojení prostředku virtuálního počítače, které není přidružené ke standardním SKU, zůstane stejné jako předtím.

Přečtěte si [podrobné informace o odchozích připojeních](load-balancer-outbound-connections.md).

### <a name="multife"></a>Několik front-endu
Load Balancer podporuje více pravidel s více front-endu.  Standard Load Balancer tuto situaci rozšíří do odchozích scénářů.  Odchozí scénáře jsou v podstatě invertované příchozí pravidlo vyrovnávání zatížení.  Příchozí pravidlo vyrovnávání zatížení také vytvoří přidružení pro odchozí připojení. Standard Load Balancer používá všechny front-endové služby přidružené k prostředku virtuálního počítače prostřednictvím pravidla vyrovnávání zatížení.  Kromě toho parametr pravidla vyrovnávání zatížení, který umožňuje potlačit pravidlo vyrovnávání zatížení pro účely odchozího připojení, které umožňuje výběr určitých front-endu, včetně žádné.

Pro srovnání základní Load Balancer vybere jeden front-end s náhodným umístěním a neexistuje možnost řízení toho, která z nich byla vybrána.

Přečtěte si [podrobné informace o odchozích připojeních](load-balancer-outbound-connections.md).

### <a name="operations"></a>Operace správy

Standard Load Balancer prostředky existují na zcela nové platformě infrastruktury.  To umožňuje rychlejší operace správy pro standardní SKU a časy dokončení, obvykle méně než 30 sekund na jeden prostředek SKU Standard.  V případě zvýšení velikosti back-end fondů se také zvýší doba potřebná pro změny ve fondu back-endu.

Můžete změnit Standard Load Balancer prostředky a přesunout standardní veřejnou IP adresu z jednoho virtuálního počítače na další mnohem rychlejší.

## <a name="migration-between-skus"></a>Migrace mezi SKU

SKU nejsou proměnlivé. Postupujte podle kroků v této části, chcete-li přejít z jedné SKU prostředku do druhé.

>[!IMPORTANT]
>Projděte si tento dokument v celém rozsahu, abyste porozuměli rozdílům mezi SKU a pečlivě zkontrolovali váš scénář.  Je možné, že budete muset provést další změny, abyste mohli svůj scénář zarovnat.

### <a name="migrate-from-basic-to-standard-sku"></a>Migrace z úrovně Basic na standard SKU

1. V případě potřeby vytvořte nový standardní prostředek (Load Balancer a veřejné IP adresy). Znovu vytvořte pravidla a definice sondy.  Pokud jste dřív používali test TCP na 443/TCP, zvažte možnost změnit tento protokol testu na test HTTPS a přidat cestu.

2. Vytvořte nové nebo aktualizujte stávající NSG na síťové kartě nebo podsíti na seznam povolených přenosů s vyrovnáváním zatížení, sondu a všech ostatních přenosů, které chcete povolit.

3. Odeberte základní prostředky SKU (podle potřeby Load Balancer a veřejné IP adresy) ze všech instancí virtuálních počítačů. Nezapomeňte taky odebrat všechny instance virtuálních počítačů ve skupině dostupnosti.

4. Připojte všechny instance virtuálních počítačů k novým prostředkům standardní skladové položky.

### <a name="migrate-from-standard-to-basic-sku"></a>Migrace z úrovně Standard na Basic SKU

1. V případě potřeby vytvořte nový základní prostředek (Load Balancer a veřejné IP adresy). Znovu vytvořte pravidla a definice sondy.  Změňte test HTTPS na test TCP na 443/TCP. 

2. Odeberte standardní prostředky SKU (podle potřeby Load Balancer a veřejné IP adresy) ze všech instancí virtuálních počítačů. Nezapomeňte taky odebrat všechny instance virtuálních počítačů ve skupině dostupnosti.

3. Připojte všechny instance virtuálních počítačů k novým základním prostředkům SKU.

>[!IMPORTANT]
>
>Existují omezení týkající se použití základních a standardních SKU.
>
>Porty HA a diagnostika standardní SKU jsou k dispozici pouze v SKU Standard. Nemůžete migrovat ze standardní SKU na základní SKU a také tyto funkce uchovávat.
>
>Základní a standardní SKU mají několik rozdílů, jak je uvedeno v tomto článku.  Ujistěte se, že rozumíte a připravíte se na ně.
>
>Pro Load Balancer a veřejné IP prostředky se musí použít vyhovující skladové položky. Nemůžete mít kombinaci základních prostředků SKU a prostředků standardních SKU. Samostatné virtuální počítače, virtuální počítače v prostředku skupiny dostupnosti ani prostředky škálovacích sad virtuálních počítačů není možné připojit k oběma SKU zároveň.

## <a name="region-availability"></a>Dostupnost v oblastech

Standard Load Balancer je aktuálně k dispozici ve všech oblastech veřejného cloudu.

## <a name="sla"></a>SLA

Služby Vyrovnávání zatížení úrovně Standard jsou dostupné s 99,99% smlouvou SLA.  Podrobnosti najdete v [Standard Load Balancer smlouvě SLA](https://aka.ms/lbsla) .

## <a name="pricing"></a>Ceny

Účtuje se Standard Load Balancer využití.

- Počet konfigurovaných a odchozích pravidel vyrovnávání zatížení (příchozí pravidla překladu adres (NAT) se nepočítají na základě celkového počtu pravidel)
- Množství zpracovaných příchozích a odchozích dat bez ohledu na pravidlo 

Informace o cenách za Load Balancer úrovně Standard najdete na stránce s [cenami za Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="limitations"></a>Omezení

- SKU nejsou proměnlivé. Nemůžete změnit SKU existujícího prostředku.
- Samostatný prostředek virtuálního počítače, prostředek sady dostupnosti nebo prostředek sady škálování virtuálního počítače můžou odkazovat na jednu SKU, nikdy ne obojí.
- Pravidlo Load Balancer nemůže zasahovat do dvou virtuálních sítí.  Front-endové a jejich související instance back-end musí být umístěné ve stejné virtuální síti.  
- [Operace přesunu předplatného](../azure-resource-manager/resource-group-move-resources.md) se u prostředků Standard SKU a PIP nepodporují.
- Role webového pracovního procesu bez virtuální sítě a dalších služeb platformy Microsoft můžou být dostupné, když se k vedlejšímu účinku používá jenom vnitřní Standard Load Balancer, která se dá použít jenom pro služby předplatného a jiné služby platformy. Nesmíte spoléhat na to, že se jedná o samotnou službu nebo že se může změnit základní platforma bez předchozího upozornění. Vždy musíte předpokládat, že pokud budete chtít používat jenom interní Standard Load Balancer, musíte v případě potřeby explicitně vytvořit [odchozí připojení](load-balancer-outbound-connections.md) .
- Load Balancer je produkt TCP nebo UDP určený k vyrovnávání zatížení a přesměrování portů pro tyto konkrétní protokoly IP.  Pravidla vyrovnávání zatížení a příchozí pravidla překladu adres (NAT) se podporují pro protokoly TCP a UDP, ale nikoli pro ostatní protokoly IP včetně protokolu ICMP. Load Balancer neukončuje datovou část toku protokolu UDP ani TCP, nereaguje na ni, ani s ní neprovádí jiné interakce. Nejedná se o proxy server. Úspěšné ověření připojení k front-endu musí probíhat v pásmu se stejným protokolem, který se používá ve vyrovnávání zatížení nebo příchozím pravidlu NAT (TCP nebo UDP) _, a_ nejméně jeden z vašich virtuálních počítačů musí vygenerovat odpověď pro klienta, aby zobrazil odpověď z front-endu.  Nepříjem vložené odpovědi z Load Balancer front-endu indikuje, že žádné virtuální počítače nedokázaly odpovědět.  Není možné pracovat s Load Balancer front-endu bez toho, aby virtuální počítač mohl reagovat.  To platí i pro odchozí připojení, kde se [maskovací SNAT portů](load-balancer-outbound-connections.md#snat) podporuje pouze pro protokoly TCP a UDP – všechny ostatní protokoly IP včetně protokolu ICMP také selžou.  Pokud chcete tento problém zmírnit, přiřaďte veřejnou IP adresu na úrovni instance.
- Na rozdíl od veřejných nástrojů pro vyrovnávání zatížení, které poskytují [odchozí připojení](load-balancer-outbound-connections.md) při přechodu z privátních IP adres uvnitř virtuální sítě na veřejné IP adresy, interní nástroje pro vyrovnávání zatížení nepřevádějí odchozí nepřipojená připojení k front-endu interního Load Balancer, jak jsou v privátním adresním prostoru IP adres.  Tím se zabrání možnému vyčerpání SNAT v rámci jedinečného interního adresního prostoru IP adres, kde překlad není požadován.  Vedlejším účinkem je to, že pokud odchozí tok z virtuálního počítače v záložním fondu pokusy o tok do front-endu interního Load Balancer, ve kterém se nachází fond, _a_ namapuje se zpátky na sebe, obě ramena toku se neshodují a tok selže.  Pokud tok nemapoval zpátky na stejný virtuální počítač ve fondu back-end, který tok vytvořil pro front-end, tok bude úspěšný.   Když se tok mapuje zpátky sám na sebe, zdá se, že odchozí tok pochází z virtuálního počítače na front-end a odpovídající příchozí tok se zdá, že pochází z virtuálního počítače do sebe samé. Z pohledu hostovaného operačního systému se příchozí a odchozí části stejného toku na virtuálním počítači neshodují. Zásobník protokolu TCP nerozpozná, že jsou tyto poloviny stejného toku součástí stejného toku, protože se zdroj neshoduje s cílem.  Když se tok mapuje na jakýkoli jiný virtuální počítač ve fondu back-end, budou se tyto poloviny toku shodovat a virtuální počítač může úspěšně reagovat na tok.  Příznakem pro tento scénář jsou přerušované časové limity připojení. Existuje několik běžných alternativních řešení pro spolehlivé dosažení tohoto scénáře (pocházející z fondu back-end do fondů back-endu odpovídajících interním Load Balancer front-end), který zahrnuje vložení proxy serveru třetí strany za interní Load Balancer nebo [použití pravidel stylu DSR](load-balancer-multivip-overview.md).  Přestože ke zmírnění problému můžete použít veřejný Load Balancer, výsledný scénář je náchylný k [vyčerpání SNAT](load-balancer-outbound-connections.md#snat) a měli byste se mu vyhnout, pokud nebude pečlivě řízený.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o používání [Standard Load Balancer a zóny dostupnosti](load-balancer-standard-availability-zones.md).
- Seznamte se s [sondami stavu](load-balancer-custom-probe-overview.md).
- Přečtěte si další informace o [zóny dostupnosti](../availability-zones/az-overview.md).
- Další informace o [diagnostice Standard Load Balancer](load-balancer-standard-diagnostics.md).
- Přečtěte si o [podporovaných multidimenzionálních metrikách](../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) pro diagnostiku v [Azure monitor](../monitoring-and-diagnostics/monitoring-overview.md).
- Přečtěte si o použití [Load Balancer pro odchozí připojení](load-balancer-outbound-connections.md).
- Přečtěte si o [odchozích pravidlech](load-balancer-outbound-rules-overview.md).
- Přečtěte si o [resetování protokolu TCP při nečinnosti](load-balancer-tcp-reset.md).
- Přečtěte si o [Standard Load Balancer s pravidly pro vyrovnávání zatížení portů vysoké dostupnosti](load-balancer-ha-ports-overview.md).
- Přečtěte si o použití [Load Balancer s více front-endu](load-balancer-multivip-overview.md).
- Seznamte se s [virtuálními sítěmi](../virtual-network/virtual-networks-overview.md).
- Přečtěte si další informace o [skupinách zabezpečení sítě](../virtual-network/security-overview.md).
- Přečtěte si o [koncových bodech služby virtuální](../virtual-network/virtual-network-service-endpoints-overview.md)sítě.
- Přečtěte si o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.
- Přečtěte si další informace o [Load Balancer](load-balancer-overview.md).
