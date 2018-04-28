---
title: Plánování kapacity pro Azure Search | Microsoft Docs
description: Upravte oddíl a repliky prostředky počítače ve službě Azure Search, kde je každý prostředek za cenu v jednotkách fakturovatelný vyhledávání.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/09/2017
ms.author: heidist
ms.openlocfilehash: 08ae64aa92d7262b462ad105aa8e776bdaef15c0
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="scale-resource-levels-for-query-and-indexing-workloads-in-azure-search"></a>Škálování prostředku úrovně pro dotaz a indexování úlohy ve službě Azure Search
Po jste [zvolte cenovou úroveň](search-sku-tier.md) a [zřídit službu vyhledávání](search-create-service-portal.md), dalším krokem je volitelně zvýšit počet replik nebo oddíly, které používá vaše služba. Každá úroveň nabízí pevný počet fakturace jednotky. Tento článek vysvětluje, jak přidělit tyto jednotky k dosažení optimálního konfigurace, který vyrovnává vaše požadavky na spuštění dotazu, indexování a úložiště.

Konfigurace prostředků je k dispozici při nastavování služby v [úroveň Basic](http://aka.ms/azuresearchbasic) nebo jeden z [standardní úrovně](search-limits-quotas-capacity.md). Pro služby na tyto úrovně, je kapacity dokupovat v jednotkách po *jednotek vyhledávání* (SUs) kde každý oddíl a repliky počítá jako jedna SU. 

Použití méně služby SUs výsledky v úměrně nižší faktury. Fakturace pro platí, dokud je nastavení služby. Pokud nepoužíváte dočasně služby, je jediným způsobem, jak se vyhnout fakturace odstraněním služby a znovu vytvořit ji v případě potřeby.

> [!Note]
> Odstraněním služby odstraníte všechno, co na něm. Neexistuje žádné zařízení v rámci Azure Search pro zálohování a obnovení trvalá data vyhledávání. K opětovnému nasazení stávajícího indexu na novou službu, byste měli spustit program použitý k vytvoření a načtení ho původně. 

## <a name="terminology-partitions-and-replicas"></a>Terminologie: oddíly a repliky
Oddíly a repliky jsou primární prostředky, které zpět vyhledávací službu.

| Prostředek | Definice |
|----------|------------|
|*Oddíly* | Poskytuje index úložiště a vstupně-výstupních operací pro operace čtení a zápisu (například když znovu sestavit nebo aktualizovat index).|
|*Repliky* | Instance služby vyhledávání, používají primárně k operace dotazů Vyrovnávání zatížení. Každou repliku vždy hostuje jednu kopii indexu. Pokud máte 12 repliky, budete mít 12 kopie každý index načíst služby.|

> [!NOTE]
> Neexistuje žádný způsob, jak přímo pracovat s nebo spravovat, které indexy spustit v replice. Jednu kopii každého index v každé repliky je součástí architektury služby.
>

## <a name="how-to-allocate-partitions-and-replicas"></a>Postup přidělení replik a oddíly
Ve službě Azure Search je služba původně přidělena minimální úroveň prostředků, který se skládá z jednoho oddílu a jednu repliku. Pro vrstvy, které ji podporují můžete upravit přírůstkově výpočetní prostředky zvýšením oddílů, pokud potřebujete další úložiště a vstupně-výstupních operací, nebo přidat další repliky větší svazky dotazu nebo lepší výkon. Služba jednotného musí mít dostatek prostředků ke zpracování všech úloh (indexování a dotazy). Nelze rozdělit zatížení mezi více služeb.

Chcete-li zvýšit nebo změnit přidělení repliky a oddíly, doporučujeme pomocí portálu Azure. Na portálu vynucuje omezení povolené kombinace, které zůstat nižší než maximální limit:

1. Přihlaste se k [portál Azure](https://portal.azure.com/) a vyberte službu vyhledávání.
2. V **nastavení**, otevřete **škálování** okno a pomocí posuvníků zvýšení nebo snížení počtu oddílů a repliky.

Pokud budete potřebovat založených na skriptech nebo založené na kódu zřizování přístup, [REST API pro správu](https://docs.microsoft.com/rest/api/searchmanagement/services) je alternativa k portálu.

Obecně platí hledání aplikace potřebují další repliky než oddíly, zvláště pokud operací služby jsou upřednostněno dotazu úlohy. V části na [vysokou dostupnost](#HA) vysvětluje, proč.

> [!NOTE]
> Po zřízení služby, nelze upgradovat na vyšší skladová položka. Musíte vytvořit vyhledávací službu na novou vrstvu a znovu načíst vaše indexy. V tématu [vytvoření služby Azure Search na portálu](search-create-service-portal.md) o pomoc se zřizováním služby.
>
>

<a id="HA"></a>

## <a name="high-availability"></a>Vysoká dostupnost
Protože je snadné a relativně rychlé škálování, doporučujeme obvykle začínáte s jedním oddílem a jednu nebo dvě repliky a pak rozšiřování škálování využívajících jako dotaz svazky sestavení. Spuštěné úlohy dotazu především na replikách. Pokud potřebujete další propustnost nebo vysokou dostupnost, bude pravděpodobně vyžadovat další repliky.

Obecná doporučení pro zajištění vysoké dostupnosti jsou:

* Dvě repliky pro vysokou dostupnost úloh jen pro čtení (dotazy)
* Tři nebo více replik pro vysokou dostupnost úloh pro čtení a zápis (dotazy a indexování přidat, aktualizovat ani odstranit jednotlivé dokumenty)

Smlouvy o úrovni služeb (SLA) pro službu Azure Search cílí v operacích dotazu a aktualizace indexu, které se skládají z přidání, aktualizace nebo odstranění dokumentů.

Základní úroveň zbarvení hlavy kořene na jeden oddíl a tři repliky. Pokud chcete flexibilitu okamžitě reagovat na kolísání poptávka po propustnost indexování a dotazů, zvažte jednu standardní vrstvy.

### <a name="index-availability-during-a-rebuild"></a>Index dostupnosti při opětovném sestavení

Vysoké dostupnosti pro službu Azure Search se vztahují na dotazy a index aktualizace, které není zahrnují nové sestavení indexu. Pokud odstraníte pole, změnit datový typ nebo název pole, musíte znovu sestavte index. Nové sestavení indexu, musíte odstranit index, znovu vytvořte index a znovu načíst data.

> [!NOTE]
> Můžete přidat nové pole do indexu Azure Search bez znovu sestavit index. Hodnota nové pole bude mít hodnotu null pro všechny dokumenty již v indexu.

Údržbu index dostupnosti při opětovném sestavení, musí mít kopii index s jiným názvem na stejnou službu nebo kopii index se stejným názvem na jinou službu a pak zadejte přesměrování nebo logika převzetí služeb při selhání ve vašem kódu.

## <a name="disaster-recovery"></a>Zotavení po havárii
V současné době není k dispozici žádné předdefinované mechanismus pro zotavení po havárii. Přidání oddílů nebo repliky může být nesprávný strategie pro splnění cílů obnovení po havárii. Nejběžnějším přístupem je přidání redundance na úrovni služby nastavením druhý vyhledávací službu v jiné oblasti. Stejně jako u dostupnosti během opětovné sestavení indexu přesměrování nebo logika převzetí služeb při selhání musí pocházet z vašeho kódu.

## <a name="increase-query-performance-with-replicas"></a>Zvýšení výkonu dotazů s replikami
Latence dotazu je indikátorem, jsou potřeba další repliky. Obecně platí je prvním krokem k zlepšení výkonu dotazů, chcete-li přidat více tento prostředek. Při přidávání repliky, další kopie index znovu online podporujících větší zatížení dotazu a načíst vyvážit požadavky přes víc replik.

Nemůžeme poskytovat pevný odhady na dotazy za sekundu (QPS): dotaz výkon závisí na složitosti dotazu a konkurenční úlohy. I když přidání repliky jasně výsledkem lepší výkon, výsledek není výhradně lineární: Přidání tři repliky nezaručuje Trojitá propustnost.

Pokyny v odhadnout QPS pro zatížení najdete v tématu [aspekty výkonu a optimalizace Azure Search](search-performance-optimization.md).

## <a name="increase-indexing-performance-with-partitions"></a>Zvýšení výkonu indexování s oddíly
Vyhledávání aplikací, které vyžadují téměř aktualizace dat v reálném čase, bude nutné úměrně další oddíly než repliky. Přidání oddílů šíří operace čtení a zápisu napříč větší počet výpočetní prostředky. Nabízí také více místa na disku pro uložení další indexy a dokumenty.

Indexy větší trvat déle dotazu. Jako takový je možné, že každý další nárůst oddíly vyžaduje menší, ale přímo úměrná zvýšení replik. Složitost dotazy a dotaz svazek bude dvoufaktorového do jak rychle provádění dotazu zapnutý,.

## <a name="basic-tier-partition-and-replica-combinations"></a>Základní úroveň: kombinace parametrů oddílu a repliky
Základní služby může mít přesně jeden oddíl a až tři repliky, maximum omezit tři služby SUS. Pouze upravit prostředek je repliky. Pro zajištění vysoké dostupnosti u dotazů na potřebujete minimálně dvě repliky.

<a id="chart"></a>

## <a name="standard-tiers-partition-and-replica-combinations"></a>Standardní vrstev: kombinace parametrů oddílu a repliky
Tato tabulka ukazuje služby SUs potřebné k podpoře kombinace repliky a oddíly, závislosti limitu 36 SU pro všechny standardní úrovně.

|   | **oddíl 1** | **2 oddíly** | **3 oddíly** | **4 oddíly** | **6 oddíly** | **12 oddíly** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 repliky** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 repliky** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 repliky** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 repliky** |4 SU |8 SU |12 SU |16 SU |24 SU |neuvedeno |
| **5 repliky** |5 SU |10 SU |15 SU |20 SU |30 SU |neuvedeno |
| **6 repliky** |6 SU |12 SU |18 SU |24 SU |36 SU |neuvedeno |
| **12 repliky** |12 SU |24 SU |36 SU |neuvedeno |neuvedeno |neuvedeno |

Služba SUs, ceny a kapacity jsou podrobně vysvětleny na webu Azure. Další informace najdete v tématu [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Počet replik a oddíly, které jsou rovnoměrně rozděleny do 12 (konkrétně 1, 2, 3, 4, 6, 12). Je to proto, že každý index Azure Search předem rozdělí na 12 horizontálních oddílů, takže je možné rozdělit do stejné části pro všechny oddíly. Například pokud vaše služba má tři oddíly a vytvoření indexu, každý oddíl bude obsahovat čtyři horizontálních oddílů indexu. Jak Azure Search horizontálních oddílů na index je podrobností implementace, v budoucích vezích se můžou změnit. I když číslo 12 dnes, není pravděpodobné, že toto číslo vždycky být 12 v budoucnu.
>
>

## <a name="billing-formula-for-replica-and-partition-resources"></a>Vzorec fakturace pro repliky a oddíl prostředky
Vzorec pro výpočet, kolik služby SUs se používají pro konkrétní kombinace je produkt repliky a oddíly, nebo (R X P = SU). Například se jako devět služby SUs fakturuje tři repliky násobí hodnotou tři oddíly.

Cena za SU, je dáno úroveň, s nižší sazbou za jednotku fakturace pro základní než pro Standard. Vyhodnotí pro každou vrstvu lze najít v [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/search/).
