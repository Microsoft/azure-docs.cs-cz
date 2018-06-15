---
title: Vyberte cenovou úroveň nebo SKU pro službu Azure Search | Microsoft Docs
description: 'Vyhledávání systému Azure se dá zřídit na tyto identifikátory SKU: volné, Basic a Standard, kde Standard je k dispozici v různých konfigurace prostředků a kapacity úrovně.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 05/12/2018
ms.author: heidist
ms.openlocfilehash: bbf535c5b446fd654331374d29c106b6e43d55f5
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34266876"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Zvolte cenovou úroveň pro službu Azure Search

Ve službě Azure Search [je služba zřízena](search-create-service-portal.md) na konkrétní cenová úroveň nebo SKU. Mezi možnosti patří **volné**, **základní**, nebo **standardní**, kde **standardní** je k dispozici v několika konfigurace a kapacity. 

Účelem tohoto článku je vám pomohou zvolit vrstvu. Doplňuje [stránce s cenami](https://azure.microsoft.com/pricing/details/search/) a [omezení služby](search-limits-quotas-capacity.md) stránka s hodnotou hash fakturace koncepty a vzory spotřeba přidružené různých vrstev. Doporučuje také iteračního postupu porozumět tomu, které vrstvě nejlepší vyhovuje vašim potřebám. 

Úrovně určit kapacitu, není funkce. Pokud kapacitu vrstvy jím je příliš malá, budete muset zřídit novou službu na vyšší úroveň a potom [znovu načíst vaše indexy](search-howto-reindex.md). Neexistuje žádné místní upgrade stejné služby z jednoho identifikátoru SKU do jiného.

Dostupnost funkcí není potřeba rozlišovat primární vrstvy. Všechny vrstvy, včetně **volné** vrstvy, nabízejí parity funkcí, s výjimkou podpora indexeru pro S3HD. Ale indexování a prostředků omezení efektivně můžete omezit rozsah používání funkcí. Například [kognitivní vyhledávání](cognitive-search-concept-intro.md) indexování má dlouho běžící dovednosti tento časový limit na bezplatná služba Pokud datová sada se stane jako velmi malé.

> [!TIP]
> Většina zákazníků začínat **volné** vrstvy zkušební verze a pak absolutoria k **standardní** pro vývoj. Po zvolte úroveň a [zřídit službu vyhledávání](search-create-service-portal.md), můžete [zvýšit počet replik a oddílu](search-capacity-planning.md) pro optimalizaci výkonu. Další informace o kdy a proč by upravit kapacity najdete v tématu [aspekty výkonu a optimalizace](search-performance-optimization.md).
>

## <a name="billing-concepts"></a>Koncepty fakturace

Koncepty, které je třeba porozumět pro výběr úrovně zahrnují definice kapacitu, omezení služby a služby jednotky. 

### <a name="capacity"></a>Kapacita

Kapacita strukturovaná jako *repliky* a *oddíly*. Repliky jsou instance Vyhledávací služby, kde každé repliky hostitelem jedné kopie indexu Vyrovnávání zatížení sítě. Například služby s replikami 6 má 6 kopie každý index načíst ve službě. Oddíly ukládat indexy a automaticky rozdělí prohledávatelná data: dva oddíly rozdělení indexu v poloviční, tři oddíly na třetiny a tak dále. Z hlediska kapacity *oddílu velikost* je funkce primární rozdílné mezi vrstvami.

> [!NOTE]
> Všechny **standardní** úrovně podpory [flexibilní kombinace repliky a oddíly, které](search-capacity-planning.md#chart) , aby bylo možné [vážené systému pro rychlost nebo úložiště](search-performance-optimization.md) změnou rovnováhu mezi. **Základní** nabízí tři repliky pro vysokou dostupnost, ale má pouze oddíl. **Volné** vrstev neposkytují vyhrazených prostředcích: výpočetní prostředky jsou sdíleny více bezplatné služby.

### <a name="limits"></a>Omezení

Služby hostitele prostředky, například indexy, indexery a tak dále. Každá úroveň ukládá [omezení služby](search-limits-quotas-capacity.md) počtu prostředků, můžete vytvořit. Zakončení na počet indexů (a dalších objektů) jako takový je druhý rozdílné funkci mezi vrstvami. Při revizi jednotlivé možnosti na portálu, Všimněte si, omezení pro počet indexů. Další prostředky, například skillsets, indexery a zdroje dat jsou propojený s limity index.

### <a name="search-units"></a>Jednotky vyhledávání

Je nejdůležitější fakturace koncept pochopit *jednotka vyhledávání* (SU), což je fakturace jednotky pro službu Azure Search. Protože Azure Search, závisí na repliky a oddíly, které mají funkce, nemá smysl k vyúčtování jeden nebo druhý. Místo toho fakturace je založena na složené obou. Formulaically, SU je produkt repliky a oddíly, které používá služba: (R X P = SU). Minimálně každých služby začíná 1 SU (jedna replika násobí hodnotou jeden oddíl), ale realističtější modelu může být repliky 3, 3oddíl služby účtován jako služby SUs 9. 

I když každá úroveň nabízí progresivně vyšší kapacity, můžete zahrnout část celkové kapacity online, která uchovává zbývající ve fondu. Z hlediska fakturační, je počet oddílů a uvést online, počítaný pomocí vzorce SU, která určuje, co ve skutečnosti platíte repliky.

Míra fakturace je za SU, s každou úroveň s jinou rychlost každou hodinu. Vyhodnotí pro každou vrstvu lze najít v [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/search/).

## <a name="consumption-patterns"></a>Vzory spotřeba

Většina zákazníků začínat **volné** služby, které udržují po neomezenou dobu a pak vyberte jednu z **standardní** vrstev závažné vývoj nebo produkční úlohy. 

![Služba Azure search úrovně](./media/search-sku-tier/tiers.png "cenové úrovně vyhledávání systému Azure")

Na obou koncích **základní** a **S3 HD** existovat v případě vzorů důležité ale netypických spotřeby. **Základní** je pro malé produkční zatížení: nabízí SLA, vyhrazené prostředky, vysokou dostupnost, ale mírné úložiště, přičemž na celkem 2 GB. Tato úroveň byl navržen s důrazem pro zákazníky, kteří konzistentně pod využívané dostupné kapacity. Na konci úplně **S3 HD** je pro zatížení typický ISV, partnery, [víceklientské řešení](search-modeling-multitenant-saas-applications.md), nebo žádnou konfiguraci volání pro velké množství malých indexy. Často je samozřejmé při **základní** nebo **S3 HD** vrstvy je vpravo fit, ale pokud chcete, aby potvrzení můžete odeslat do [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) nebo [obraťte se na Azure Podpora](https://azure.microsoft.com/support/options/) další pokyny.

Pro často používané standardní vrstvy, s posunem fokus **S1 S3** jsou rozšiřování zvyšující se úroveň kapacitu s body důraz na velikost oddílu a maximální hodnoty čísel indexy, indexery a corollary prostředků:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Velikost oddílu|  25 GB | 100 GB | 250 GB |  |  |  |  |
| index a indexer omezení| 50 | 200 | 200 |  |  |  |  |

**S1** je běžné použít v případě vyhrazených prostředcích a více oddílů, budou nezbytné. S oddíly 25 GB pro až 12 oddíly za službu limit **S1** je celkový počet 300 GB, pokud maximalizovat oddíly přes repliky (najdete v části [přidělení replik a oddíly](search-capacity-planning.md#chart) pro více vyrovnáváním složení.)

Stránky portálu a ceny zaměření na velikost oddílu a úložiště, ale pro každou vrstvu všechny výpočetní možnosti (kapacita disku, rychlosti procesorů) růst lineárně s ceny. **S2** replika je rychlejší než **S1**, a **S3** je rychlejší než **S2**. **S3** vrstev rozdělit obecně lineární výpočetní ceny vzor s nepřiměřeně rychlejší vstupně-výstupní operace. Pokud očekáváte, že vstupně-výstupních operací jako problémové místo, **S3** vám dává mnohem víc IOPS než nižší úrovně.

**S3** a **S3 HD** jsou zajišťované pomocí infrastruktury identické vysoké kapacity, ale každý jeden dosáhne své maximální limit různými způsoby. **S3** cílem menší počet velké indexy. Jako takový jeho maximální limit je vázán na prostředků (pro každou službu 2.4 TB). **S3 HD** cílem velký počet velmi malé indexy. Na 1000 indexy **S3 HD** dosaženo omezení ve formě indexu omezení. Pokud jste **S3 HD** zákazníkovi, který vyžaduje více než 1 000 indexy, obraťte se na Microsoft Support informace o tom, jak pokračovat.

> [!NOTE]
> Limity dřív, byly zabývat ale již nejsou použitelné pro většinu služby vyhledávání systému Azure zřízené po 2018 leden. Další informace o podmínkách, pro které se omezení dokumentů vztahuje pořád platí najdete v tématu [omezení služby: dokumentu omezení](search-limits-quotas-capacity.md#document-limits).
>

## <a name="evaluate-capacity"></a>Vyhodnocení kapacity

Kapacita a nákladů na běžící služba přejděte ruční v dolním. Úrovně uložit omezení na dvě úrovně (úložiště a prostředků), takže by měl přemýšlíte o obě protože kteréhokoli z nich nedostanete nejprve je účinné limit. 

Obchodní požadavky stanovují obvykle počet indexů, které budete potřebovat. Například globální index pro velké úložiště dokumentů nebo možná více indexů, na základě oblast, aplikace nebo volné místo na firmy.

Chcete-li určit velikost index, budete muset [jednu vytvořit](search-create-index-portal.md). Struktura dat ve službě Azure Search je primárně určen [obrácený index](https://en.wikipedia.org/wiki/Inverted_index), který má jiné vlastnosti než zdrojová data. Pro obráceným index velikost a složitost určuje obsah, nemusí nutně prokázat množství dat, které jste do ní kanálu. Zdroj velkých objemů dat s ohromnou redundance by mohla způsobit indexem menší než menší datové sady s vysoce proměnné obsahem.  Jako takový je zřídka možné odvození velikost indexu na základě velikosti původní datové sady.

### <a name="preliminary-estimates-using-the-free-tier"></a>Předběžné odhady pomocí úroveň Free

Jeden z přístupů k odhadování kapacity je začínat **volné** vrstvy. Odvolat, který **volné** služby nabízí až 3 indexy, 50 MB úložiště a indexování čas 2 minut. Může být náročné odhadnout velikost předpokládané indexu s těmito omezeními, ale následující příklad ilustruje přístup:

+ [Vytvoření bezplatné služby](search-create-service-portal.md)
+ Příprava malé, reprezentativní datové sady (předpokládá pět tisíc dokumentů a velikost vzorku 10 %)
+ [Sestavení indexu počáteční](search-create-index-portal.md) a poznamenejte si jeho velikost na portálu (předpokládá 30 MB)

Za předpokladu, že ukázku zástupce a 10 % celého datového zdroje, 30 MB index stane přibližně 300 MB Pokud jsou indexované všechny dokumenty. Díky této předběžné číslo, může double tato šířka do rozpočtu dva indexy (vývoj a produkční), celkem 600 MB v požadavky na úložiště. To je snadno uspokojit **základní** vrstvy, takže byste začali existuje.

### <a name="advanced-estimates-using-a-billable-tier"></a>Pokročilé odhadne pomocí vrstvu fakturovatelného času

Někteří zákazníci dávají přednost začínat vyhrazených prostředcích, které můžete pojmout větší vzorkování a časy zpracování a pak vytvářet realistické odhady množství index, velikost a svazky dotazu během vývoje. Na začátku služby je zřízený založené na nejlepší odhad odhad, a pak během existence vývojového projektu týmy obvykle zjistit, zda existující službu nad nebo pod kapacity pro předpokládané produkčním prostředí. 

1. [Zkontrolujte omezení služby v jednotlivých vrstvách](https://docs.microsoft.com/en-us/azure/search/search-limits-quotas-capacity#index-limits) k určení, zda nižší úrovně může podporovat objemu indexy, které potřebujete. Mezi **základní**-**S1**- **S2** vrstvy, limity indexu jsou 15 – 50-200, v uvedeném pořadí.

1. [Vytvoření služby v vrstvu fakturovatelný](search-create-service-portal.md):

    + Spustit na nízkou **základní** nebo **S1** Pokud jste na začátku křivku vaše.
    + Spustit na vysokou **S2** nebo i **S3**, pokud jsou ve velkém měřítku indexování a dotaz zatížení samozřejmé.

1. [Sestavení indexu počáteční](search-create-index-portal.md) k určení, jak zdrojová data přeloží na index. Toto je jediný způsob, jak odhadnout velikost indexu.

1. [Monitorování úložiště, omezení služby, dotaz svazku a latence](search-monitor-usage.md) na portálu. Na portálu se dozvíte, dotazy na druhou, omezenému dotazy a hledání latence; všechny z nich může pomoci při rozhodování, pokud jste na správné úrovně. Kromě zajištění dostatečného portálu metriky, můžete nakonfigurovat hloubkového monitorování, jako je například interaktivní analýzy, povolením [Analýza provozu vyhledávání](search-traffic-analytics.md). 

Index počtu a velikosti jsou rovnoměrně relevantní pro analýzy, protože maximální limitu prostřednictvím úplné využití úložiště (oddíly) nebo maximální limit počtu prostředků (indexy, indexery a tak dále), nastane dříve. Na portálu pomáhá udržovat přehled o obě, zobrazuje aktuální využití a maximální limit vedle sebe na stránce Přehled.

> [!NOTE]
> Požadavky na úložiště může být přepsání zvýšeným, pokud dokumenty obsahují nadbytečné data. V ideálním případě dokumenty obsahují pouze data, která potřebujete pro možnosti vyhledávání. Binární data je jiný prohledávat a je třeba uložit odděleně (třeba v tabulce nebo objekt blob úložiště Azure) s pole v indexu pro uložení adresy URL odkaz na externí data. Maximální velikost jednotlivých dokumentu je 16 MB (nebo méně Pokud jste hromadné odesílání více dokumentů v jedné žádosti). [Omezení ve službě Azure Search služby](search-limits-quotas-capacity.md) obsahuje další informace.
>

**Důležité informace o svazku dotazu**

Dotazy za sekundu (QPS) je metrika, který získá úroveň během optimalizace výkonu, ale není obecně zabývat vrstvě Pokud očekáváte, že svazek velmi vysoká dotazu na začátku.

Všechny standardní vrstvy vyrovnávání replik doručovat do oddílů, podpora dotazu určitý prostřednictvím další repliky pro načítání vyrovnávání a další oddíly pro paralelní zpracování. Abyste mohli vyladit výkon po zřízení služby.

Zákazník, který očekávat silné trvalejší dotazu, svazky od samého počátku zvažte vyšší úrovně, založenou na výkonnější hardwaru. Můžete pak offline oddíly a repliky nebo dokonce přejít nižší úrovně služby, pokud tyto svazky dotazu se nepodařilo vyhodnotit. Další informace o tom, jak vypočítat dotazu propustnost, najdete v části [výkonu Azure Search a optimalizace](search-performance-optimization.md).


**Smlouvy o úrovni služeb**

**Volné** vrstvy a preview funkce nejsou s [smlouvy o úrovni (SLA) služeb](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Pro všechny fakturovatelné úrovně SLA projeví při zřizování dostatečná redundance pro vaši službu. Dvě nebo více replik jsou požadovány pro SLA dotazu (načíst). Tři nebo více replik jsou požadovány pro dotazy a indexování smlouvy o úrovni služeb (pro čtení a zápis). Počet oddílů není k posouzení SLA. 

## <a name="tips-for-tier-evaluation"></a>Tipy pro vyhodnocení vrstvy

+ Zjistěte, jak vytvářet efektivní indexy a které metody aktualizace jsou minimálně impactful. Doporučujeme, abyste [Analýza provozu vyhledávání](search-traffic-analytics.md) pro získaným na dotaz aktivity.

+ Povolit metriky sestavení kolem dotazy a shromažďovat data kolem vzorce používání (dotazy v pracovní době indexování mimo špičku) a tato data použít k informování budoucí služby rozhodnutí o zřízení. Když není praktické úrovni hodinových nebo denních, můžete dynamicky upravit oddílů a prostředky pro uložení plánované změny ve svazcích dotazu, nebo neplánované ale dlouhodobě změny, pokud úrovně uložení dostatečně dlouhé, pro vlastní akce.

+ Mějte na paměti, že je pouze nevýhodou zřizování, možná budete muset službu přerušit, pokud jsou větší než je odhadované skutečné požadavky. Pokud chcete zabránit přerušení služby, by vytvořit novou službu ve stejném předplatném na vyšší úroveň a spusťte ji vedle sebe, dokud se všechny aplikace a požadavky cíle nový koncový bod.

## <a name="next-steps"></a>Další postup

Začněte **volné** vrstvy a sestavení počáteční index pomocí podmnožinu dat pochopit jeho vlastnosti. Struktura dat ve službě Azure Search je obráceným index, kde velikost a složitost obráceným index je určen podle obsahu. Mějte na paměti, že vysoce redundantní obsahu může vést indexem menší než vysoce nestandardní obsah k. Jako takový je obsahu charakteristiky spíše než velikost datové sady, který určuje požadavky na úložiště indexu.

Jakmile máte představu počáteční index velikosti [zřídit fakturovatelný službu](search-create-service-portal.md) v některém z vrstvy popsané v tomto článku, buď **základní** nebo **standardní** vrstvy. Uvolnění omezeními umělé u podmnožiny dat a [sestavit index](search-howto-reindex.md) zahrnout všechna data ve skutečnosti chcete vyhledávat.

[Přidělení replik a oddíly](search-capacity-planning.md) podle potřeby výkonu a škálování, budete potřebovat.

Pokud výkon a kapacitu dobře, jste hotovi. Jinak, znovu vytvořte službu vyhledávání na různé vrstvy, která více přesně zarovnaná s vašim potřebám.

> [!NOTE]
> Další pomoc s dotazy, odeslání na [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) nebo [kontaktujte podporu Azure](https://azure.microsoft.com/support/options/).