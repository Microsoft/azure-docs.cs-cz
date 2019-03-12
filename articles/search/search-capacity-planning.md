---
title: Škálování oddílů a replik pro dotazy a indexování – vyhledávání Azure
description: Upravte oddílů a replik prostředky počítače ve službě Azure Search, kde každého prostředku se účtuje v jednotkách fakturovatelné vyhledávání.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 69fce34c55007daff48b2463da590ffb9cd59926
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775318"
---
# <a name="scale-partitions-and-replicas-for-query-and-indexing-workloads-in-azure-search"></a>Škálování oddílů a replik pro dotazy a indexování úloh ve službě Azure Search
Poté co [zvolte cenovou úroveň](search-sku-tier.md) a [při zřizování vyhledávací služby](search-create-service-portal.md), dalším krokem je Volitelně můžete zvýšit počet repliky nebo oddíly, které používá vaše služba. Každá úroveň nabízí pevný počet jednotek fakturace. Tento článek vysvětluje, jak přidělit jednotkách zajistit optimální konfiguraci, která vyrovnává vaše požadavky na spuštění dotazu, indexování a úložiště.

Konfigurace prostředků je k dispozici, když nastavujete službu na [úroveň Basic](https://aka.ms/azuresearchbasic) nebo jeden z [úrovně Standard](search-limits-quotas-capacity.md). Pro služby na těchto úrovních kapacity dokupovat v jednotkách po *hledání jednotky* (su) kde jednotlivých oddílů a replik se počítá jako jeden příkaz SU. 

Použití méně výsledků SUs v proporcionálně snížení nákladů. Účtování probíhá za platit tak dlouho, dokud je nastavení služby. Pokud nepoužíváte dočasně služby, je jediný způsob, jak se vyhnout fakturace odstraňování služby a potom vytvoříte ho znovu když je potřebujete.

> [!Note]
> Odstraněním služby odstraníte všechno, co na něj. Neexistuje žádné zařízení v rámci Azure Search pro zálohování a obnovení trvalých dat hledání. K opětovnému nasazení existujícího indexu na novou službu, když spustíte program použitý k vytvoření a načtení původně. 

## <a name="terminology-replicas-and-partitions"></a>Terminologie: repliky a oddíly
Repliky a oddíly, které jsou primární zdroje, které zálohují vyhledávací službu.

| Prostředek | Definice |
|----------|------------|
|*Oddíly* | Poskytuje index úložiště a vstupně-výstupních operací pro operace čtení a zápisu (například, když znovu sestavit nebo aktualizovat index).|
|*Repliky* | Instance z vyhledávací služby, které se používají především pro operace dotazů Vyrovnávání zatížení. Každá replika vždy hostuje jedna kopie indexu. Pokud máte 12 repliky, budete mít 12 zkopíruje každý index načíst služby.|

> [!NOTE]
> Neexistuje žádný způsob, jak přímo pracovat s nebo spravovat, které indexy spustit v replice. Jednu kopii každého index pro všemi replikami je součástí architektury služby.
>


## <a name="how-to-allocate-replicas-and-partitions"></a>Postup přidělení repliky a oddíly
Ve službě Azure Search je služba původně přidělené minimální úroveň prostředků, který se skládá z jednoho oddílu a jednu repliku. Pro úrovně, které ho podporují můžou přírůstkově upravovat výpočetních prostředcích, které zvýšením počtu oddílů Pokud potřebujete další úložiště a vstupně-výstupní operace, nebo přidejte víc replik pro svazky větší dotazu nebo lepší výkon. Jediné služby musí mít dostatek prostředků ke zpracování všech úloh (indexování a dotazy). Nelze rozdělit zatížení mezi více službami.

Chcete-li zvýšit nebo změnit přidělení repliky a oddíly, doporučujeme pomocí webu Azure portal. Na portálu vynucuje omezení na povolené kombinace, které zůstávají pod mezní hodnoty. Pokud budete potřebovat založené na skriptu nebo kódu zřizování přístup [prostředí Azure PowerShell](search-manage-powershell.md) nebo [REST API pro správu](https://docs.microsoft.com/rest/api/searchmanagement/services) alternativní řešení.

Obecně platí Hledat aplikace potřebovat víc replik, než má téma oddílů, zejména pokud operace služby jsou upřednostněno dotazu úlohy. V sekci [vysoké dostupnosti](#HA) vysvětluje, proč.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) a vyberte vyhledávací službu.
2. V **nastavení**, otevřete **škálování** stránky pro úpravu repliky a oddíly. 

   Následující snímek obrazovky ukazuje standardní službu opatřena jednu repliku a oddílu. Vzorce v dolní části označuje, kolik jednotek vyhledávání se používá (1). Pokud je cena ze jednotku se 100 USD (ne skutečná cena), bude měsíční náklady na spuštění této služby 100 USD v průměru.

   ![Měřítko stránky zobrazující aktuální hodnoty](media/search-capacity-planning/1-initial-values.png "měřítko stránky zobrazující aktuální hodnoty")

3. Pomocí posuvníku zvyšte nebo snižte počet oddílů. Vzorce v dolní části označuje, kolik jednotek vyhledávání se používají.

   V tomto příkladu zdvojnásobuje kapacitu se dvě repliky a jednotlivých oddílů. Všimněte si, že počet jednotek vyhledávání; je teď čtyři protože fakturační vzorec je repliky vynásobené oddíly (2 x 2). Zvýší kapacitu zdvojnásobuje více než náklady na provoz služby. Pokud hledání cena jednotky se 100 USD, nový měsíční faktuře bude nyní 400 dolarů.

   Aktuální náklady za jednotku jednotlivé vrstvy, najdete [stránce s cenami](https://azure.microsoft.com/pricing/details/search/).

   ![Přidat repliky a oddíly](media/search-capacity-planning/2-add-2-each.png "přidat repliky a oddíly")

3. Klikněte na tlačítko **Uložit** potvrďte provedené změny.

   ![Potvrzení změn škálování a fakturace](media/search-capacity-planning/3-save-confirm.png "potvrzení změn škálování a fakturace")

   Změny v kapacitě trvat několik hodin. Po spuštění procesu a neexistuje žádná monitorování v reálném čase pro repliky a oddíl úpravy nedá zrušit. Ale tato zpráva zůstává viditelná, zatímco probíhají změny.

   ![Stavová zpráva na portálu](media/search-capacity-planning/4-updating.png "stavová zpráva na portálu")


> [!NOTE]
> Po zřízení služby nejde upgradovat na vyšší skladovou Položku. Musíte vytvořit vyhledávací služby na novou úroveň a znovu načíst indexů. Zobrazit [vytvoření služby Azure Search na portálu](search-create-service-portal.md) nápovědu pro zřizování služby.
>
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Kombinace oddílů a replik

Základní služby může obsahovat právě jeden oddíl a až tři repliky, maximum omezit tři služby SUS. Pouze měnitelné prostředek je repliky. Budete potřebovat minimálně dvě repliky pro zajištění vysoké dostupnosti pro dotazy.

Všechny standardní služby, můžete předpokládat následující kombinace repliky a oddíly, v souladu s limit 36 SU. 

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


## <a name="next-steps"></a>Další postup

[Zvolte cenovou úroveň pro službu Azure Search](search-sku-tier.md)
