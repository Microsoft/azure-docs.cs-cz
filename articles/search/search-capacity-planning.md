---
title: Přidělit oddíly a replikami dotazů a indexování – Azure Search
description: Upravte oddílů a replik prostředky počítače ve službě Azure Search, kde každého prostředku se účtuje v jednotkách fakturovatelné vyhledávání.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/09/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: e2eff6c854dae48961700341a6db19dc7113901c
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316110"
---
# <a name="allocate-partitions-and-replicas-for-query-and-indexing-workloads-in-azure-search"></a>Přidělit oddílů a replik pro dotazy a indexování úloh ve službě Azure Search
Poté co [zvolte cenovou úroveň](search-sku-tier.md) a [při zřizování vyhledávací služby](search-create-service-portal.md), dalším krokem je Volitelně můžete zvýšit počet repliky nebo oddíly, které používá vaše služba. Každá úroveň nabízí pevný počet jednotek fakturace. Tento článek vysvětluje, jak přidělit jednotkách zajistit optimální konfiguraci, která vyrovnává vaše požadavky na spuštění dotazu, indexování a úložiště.

Konfigurace prostředků je k dispozici, když nastavujete službu na [úroveň Basic](https://aka.ms/azuresearchbasic) nebo jeden z [úrovně Standard](search-limits-quotas-capacity.md). Pro služby na těchto úrovních kapacity dokupovat v jednotkách po *hledání jednotky* (su) kde jednotlivých oddílů a replik se počítá jako jeden příkaz SU. 

Použití méně výsledků SUs v proporcionálně snížení nákladů. Účtování probíhá za platit tak dlouho, dokud je nastavení služby. Pokud nepoužíváte dočasně služby, je jediný způsob, jak se vyhnout fakturace odstraňování služby a potom vytvoříte ho znovu když je potřebujete.

> [!Note]
> Odstraněním služby odstraníte všechno, co na něj. Neexistuje žádné zařízení v rámci Azure Search pro zálohování a obnovení trvalých dat hledání. K opětovnému nasazení existujícího indexu na novou službu, když spustíte program použitý k vytvoření a načtení původně. 

## <a name="terminology-partitions-and-replicas"></a>Terminologie: oddílů a replik
Oddílů a replik jsou primární zdroje, které zálohují vyhledávací službu.

| Prostředek | Definice |
|----------|------------|
|*Oddíly* | Poskytuje index úložiště a vstupně-výstupních operací pro operace čtení a zápisu (například, když znovu sestavit nebo aktualizovat index).|
|*Repliky* | Instance z vyhledávací služby, které se používají především pro operace dotazů Vyrovnávání zatížení. Každá replika vždy hostuje jedna kopie indexu. Pokud máte 12 repliky, budete mít 12 zkopíruje každý index načíst služby.|

> [!NOTE]
> Neexistuje žádný způsob, jak přímo pracovat s nebo spravovat, které indexy spustit v replice. Jednu kopii každého index pro všemi replikami je součástí architektury služby.
>

## <a name="how-to-allocate-partitions-and-replicas"></a>Postup přidělení oddílů a replik
Ve službě Azure Search je služba původně přidělené minimální úroveň prostředků, který se skládá z jednoho oddílu a jednu repliku. Pro úrovně, které ho podporují můžou přírůstkově upravovat výpočetních prostředcích, které zvýšením počtu oddílů Pokud potřebujete další úložiště a vstupně-výstupní operace, nebo přidejte víc replik pro svazky větší dotazu nebo lepší výkon. Jediné služby musí mít dostatek prostředků ke zpracování všech úloh (indexování a dotazy). Nelze rozdělit zatížení mezi více službami.

Chcete-li zvýšit nebo změnit přidělení repliky a oddíly, doporučujeme pomocí webu Azure portal. Na portálu vynucuje omezení na povolené kombinace, které zůstávají pod mezní hodnoty:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) a vyberte vyhledávací službu.
2. V **nastavení**, otevřete **škálování** blade a pokud chcete zvýšit nebo snížit počet oddílů a replik pomocí posuvníků.

Pokud budete potřebovat založené na skriptu nebo kódu zřizování přístup [REST API pro správu](https://docs.microsoft.com/rest/api/searchmanagement/services) se o alternativu k portálu.

Obecně platí Hledat aplikace potřebovat víc replik, než má téma oddílů, zejména pokud operace služby jsou upřednostněno dotazu úlohy. V sekci [vysoké dostupnosti](#HA) vysvětluje, proč.

> [!NOTE]
> Po zřízení služby nejde upgradovat na vyšší skladovou Položku. Musíte vytvořit vyhledávací služby na novou úroveň a znovu načíst indexů. Zobrazit [vytvoření služby Azure Search na portálu](search-create-service-portal.md) nápovědu pro zřizování služby.
>
>

<a id="HA"></a>

## <a name="high-availability"></a>Vysoká dostupnost
Vzhledem k tomu je snadné a poměrně rychlé vertikální navýšení kapacity, doporučujeme obecně, že začnete s jedním oddílem a jednu nebo dvě repliky a potom vertikálního navýšení jako dotaz svazky sestavení. Především u replik spuštěné úlohy dotazu. Pokud potřebujete další propustnost nebo vysokou dostupnost, bude pravděpodobně vyžadovat další repliky.

Obecná doporučení pro zajištění vysoké dostupnosti jsou:

* Dvě repliky pro zajištění vysoké dostupnosti úloh jen pro čtení (dotazy)
* Tři nebo více replik pro zajištění vysoké dostupnosti pro čtení a zápis úloh (dotazů a indexování, jak přidat, aktualizovat ani odstranit jednotlivé dokumenty)

Smlouvy o úrovni služeb (SLA) pro službu Azure Search je určená v operacích dotazu a na aktualizace indexu, které se skládají z přidání, aktualizace nebo odstranění dokumentů.

Úroveň Basic zbarvení hlavy kořene navýšení kapacity na jeden oddíl a tři repliky. Pokud požadujete flexibilitu okamžitě reagovat na výkyvy poptávky pro indexování a dotazování propustnost, zvažte jednu z úrovně Standard.

### <a name="index-availability-during-a-rebuild"></a>Index dostupnost během opětovné sestavení

Vysoká dostupnost pro Azure Search se vztahují na dotazů a aktualizace indexu, které není zahrnují nové sestavení indexu. Je-li odstranit pole, změna datového typu nebo přejmenovat pole, je potřeba znovu sestavte index. Index znovu sestavit, musíte odstranit index, znovu vytvořte index a znovu načíst data.

> [!NOTE]
> Nová pole můžete přidat do indexu Azure Search bez nutnosti opětovného sestavení indexu. Hodnota nové pole bude mít hodnotu null pro všechny dokumenty již v indexu.

Pokud chcete zachovat dostupnost indexu během opětovné sestavení, musíte mít kopii index s jiným názvem ve stejné službě nebo její kopie index se stejným názvem v jiné službě a potom zadejte přesměrování nebo logika převzetí služeb při selhání ve vašem kódu.

## <a name="disaster-recovery"></a>Zotavení po havárii
V současné době neexistuje žádný předdefinovaný mechanismus pro zotavení po havárii. Přidání repliky nebo oddíly může být nesprávné strategie pro splnění cíle pro zotavení po havárii. Nejběžnější přístup je k zajištění redundance na úrovni služby nastavením druhý vyhledávací službě v jiné oblasti. Stejně jako u dostupnost během opětovné sestavení indexu, přesměrování nebo logika převzetí služeb při selhání musí pocházet z vašeho kódu.

## <a name="increase-query-performance-with-replicas"></a>Zvýšení výkonu dotazů s replikami.
Latence dotazu je indikátor, že jsou potřeba další repliky. Obecně platí je první krok směrem k zlepšení výkonu dotazů pro přidání dalších tohoto prostředku. Při přidávání repliky, další kopie, které index znovu online pro podporu úloh s větší dotazu a načtení vyvážit požadavky přes víc replik.

Nemůžeme poskytovat pevné odhady pro dotazy za sekundu (QPS): dotaz výkon závisí na složitosti dotazu a konkurenční úlohy. I když přidání repliky jasně výsledkem lepší výkon, výsledek není striktně lineární: přidání tří replik nezaručuje Trojitá propustnost.

Pokyny při odhadování QPS pro vaše úlohy najdete v tématu [Azure Search výkon a optimalizace aspekty](search-performance-optimization.md).

## <a name="increase-indexing-performance-with-partitions"></a>Zvyšte výkon indexování oddílů
Hledat aplikace, které vyžadují téměř aktualizace dat v reálném čase, bude nutné proporcionálně více oddílů než repliky. Přidání oddílů šíří operací čtení a zápisu napříč většího počtu výpočetních prostředků. Také poskytuje více místa na disku pro ukládání další indexům a dokumentům.

Indexy větší trvat delší dobu dotazu. V důsledku toho můžete zjistit, že každý další nárůst oddíly vyžaduje menší, ale proporcionální zvýšení replik. Do rychlost provádění dotazů bude převedena bude faktor složité dotazy a množství dotazů.

## <a name="basic-tier-partition-and-replica-combinations"></a>Úroveň Basic: Kombinace oddílů a replik
Základní služby může obsahovat právě jeden oddíl a až tři repliky, maximum omezit tři služby SUS. Pouze měnitelné prostředek je repliky. Budete potřebovat minimálně dvě repliky pro zajištění vysoké dostupnosti pro dotazy.

<a id="chart"></a>

## <a name="standard-tiers-partition-and-replica-combinations"></a>Úrovně Standard: Kombinace oddílů a replik
Tato tabulka ukazuje SUs nezbytném k podpoře kombinací repliky a oddíly, v souladu s 36 SU limitu pro všechny úrovně Standard.

|   | **oddíl 1** | **2 oddíly** | **3 oddíly** | **4 oddíly** | **6 oddíly** | **12 oddíly** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 repliky** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 repliky** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 repliky** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 repliky** |4 SU |8 SU |12 SU |16 SU |24 SU |neuvedeno |
| **5 repliky** |5 SU |10 SU |15 SU |20 SU |30 SU |neuvedeno |
| **6 replik** |6 SU |12 SU |18 SU |24 SU |36 SU |neuvedeno |
| **12 repliky** |12 SU |24 SU |36 SU |neuvedeno |neuvedeno |neuvedeno |

Su, ceny a kapacita je podrobně popsaný v na webu Azure. Další informace najdete v tématu [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Počet replik a oddíly, které jsou rovnoměrně rozděleny do 12 (konkrétně, 1, 2, 3, 4, 6 nebo 12). Je to proto, že každý index Azure Search předem rozdělí na 12 horizontálních oddílů, tak, aby bylo možné rozdělit do stejné části na všechny oddíly. Například pokud vaše služba má tři oddíly a vytvoření indexu, každý oddíl bude obsahovat čtyři horizontální oddíly indexu. Jak Azure Search horizontální oddíly, což je index je podrobnosti implementace, v budoucích vezích se můžou změnit. I když číslo 12 ještě dnes, by neměli očekávat, že jejich počet vždy být 12 v budoucnosti.
>
>

## <a name="billing-formula-for-replica-and-partition-resources"></a>Vzorec fakturace pro repliky a oddílu prostředků
Vzorec pro výpočet počtu jednotek su se používají pro konkrétní kombinace je produktem repliky a oddíly, nebo (R X P = SU). Například tři repliky vynásobené tři oddíly se účtuje jako devět su.

Náklady za SU se určuje podle úrovně, s nižšími fakturační sazba za jednotku pro základní než pro úroveň Standard. Sazby za pro každou vrstvu můžete najít na [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/search/).
