---
title: Automatizovaný výkon, náklady, doporučení zabezpečení pro Azure Cosmos DB
description: Naučte se, jak zobrazit přizpůsobený výkon, náklady, zabezpečení a další doporučení pro Azure Cosmos DB na základě vašich vzorů úloh.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/28/2020
ms.reviewer: sngun
ms.openlocfilehash: 8fa2fdf23a0d71b854e043b66c0aed7e944c5f39
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87450336"
---
# <a name="automated-recommendations-for-azure-cosmos-db"></a>Automatizovaná doporučení pro Azure Cosmos DB

Všechny cloudové služby, včetně Azure Cosmos DB, poskytují časté aktualizace s novými funkcemi, funkcemi a vylepšeními. Je důležité, aby vaše aplikace zůstala v provozu s nejnovějšími aktualizacemi výkonu a zabezpečení. Azure Portal nabízí vlastní doporučení, která vám umožní maximalizovat výkon aplikace. Poradenský modul Azure Cosmos DB průběžně analyzuje historii využití vašich prostředků Azure Cosmos DB a poskytuje doporučení založená na vašich vzorcích úloh. Tato doporučení odpovídají oblastem, jako je vytváření oddílů, indexování, síť, zabezpečení atd. Tato vlastní doporučení vám pomůžou zlepšit výkon aplikace.

## <a name="view-recommendations"></a>Zobrazení doporučení

Doporučení pro Azure Cosmos DB můžete zobrazit následujícími způsoby:

- Na kartě oznámení je jeden způsob, jak zobrazit doporučení. Pokud jsou k dispozici nová doporučení, zobrazí se panel zpráv. Přihlaste se k vašemu [Azure Portal](https://portal.azure.com) a přejděte k účtu Azure Cosmos. V rámci účtu Azure Cosmos otevřete podokno **oznámení** a pak vyberte kartu **doporučení** . Můžete vybrat zprávu a zobrazit doporučení.  

   :::image type="content" source="./media/automated-recommendations/cosmos-db-pane-recommendations.png" alt-text="Zobrazit doporučení z Azure Cosmos DBho podokna":::

- Doporučení můžete také najít prostřednictvím [Azure Advisor](../advisor/advisor-overview.md) v kategoriích podle různých sad, jako jsou náklady, zabezpečení, spolehlivost, výkon a provozní kvalita. Můžete vybrat konkrétní předplatná a filtrovat podle typu prostředku, který je **Azure Cosmos DB účtů**.  Když vyberete konkrétní doporučení, zobrazí se akce, které můžete využít k výhodám vašich úloh.

   :::image type="content" source="./media/automated-recommendations/advisor-pane-recommendations.png" alt-text="Zobrazit doporučení z Azure Cosmos DBho podokna":::

Ne všechna doporučení zobrazená v podokně Azure Cosmos DB jsou k dispozici v Azure Advisor a naopak. To je proto, že na základě typu doporučení, které odpovídají, do podokna Azure Advisor, Azure Cosmos DBho podokna nebo obojího.

V současné době Azure Cosmos DB podporuje doporučení v následujících oblastech. Každé z těchto doporučení obsahuje odkaz na relevantní část dokumentace, takže je snadné provést další kroky.

## <a name="sdk-usage-recommendations"></a>Doporučení pro použití sady SDK

V této kategorii Advisor detekuje použití staré verze sad SDK a doporučuje upgradovat na novější verzi a využívat nejnovější opravy chyb a vylepšení výkonu. V současné době jsou k dispozici následující doporučení specifická pro sadu SDK:

|Název  |Popis  |
|---------|---------|
| Starý konektor Spark | Detekuje použití starých verzí konektoru Spark a doporučuje upgradovat. |
| Stará sada .NET SDK | Detekuje použití starších verzí sady .NET SDK a doporučuje upgrade. |
| Stará Java SDK | Detekuje použití starších verzí konektoru Java a doporučuje upgrade. |

## <a name="indexing-recommendations"></a>Doporučení indexování

V této kategorii Advisor detekuje režim indexování, zásadu indexování, indexované cesty a doporučuje změnu v případě, že aktuální konfigurace má dopad na výkon dotazů. V současné době jsou k dispozici následující doporučení specifická pro indexování:

|Název  |Popis  |
|---------|---------|
| Opožděné indexování | Detekuje využití režimu opožděného indexování a doporučuje použít místo toho konzistentní režim indexování. Účelem režimu opožděného indexování Azure Cosmos DB je omezené a může mít dopad na aktuálnost výsledků dotazů v některých situacích, takže se doporučuje konzistentní režim indexování. |
| Složené indexování| Zjistí účty, ve kterých můžou dotazy těžit ze složených indexů, a doporučuje je použít. Složené indexy můžou významně zlepšit výkon a využití propustnosti některých dotazů.|
| Výchozí zásada indexování s velkým počtem indexovaných cest | Detekuje kontejnery běžící ve výchozím indexování s velkým počtem indexovaných cest a doporučuje přizpůsobovat zásady indexování.|
| Seřadit podle dotazů s vysokou sazbou za RU/s| Zjišťuje pořadí vystavování kontejnerů dotazy s vysokými poplatky za RU/s a doporučuje prozkoumat složené indexy.|
| Účty MongoDB 3,6 bez využití indexu a vysokého počtu RU/s| Detekuje rozhraní API Azure Cosmos DB pro MongoDB s 3,6 verzí kontejnerů, které vydávají dotazy s vysokým RU/s a doporučuje přidat indexy.|

## <a name="cost-optimization-recommendations"></a>Doporučení pro optimalizaci nákladů

V této kategorii služba Advisor detekuje využití RU/s a zjistí, že můžete optimalizovat cenu tím, že provedete některé změny vašich prostředků nebo využijete jiný cenový model. V současné době jsou k dispozici následující doporučení týkající se optimalizace nákladů:

|Název  |Popis  |
|---------|---------|
| Rezervovaná kapacita | Detekuje využití RU/s a doporučuje rezervované instance pro uživatele, kteří jim můžou z něj těžit. |
| Neaktivní kontejnery | Detekuje kontejnery, které se nepoužily déle než 30 dní, a doporučuje snižovat propustnost takových kontejnerů nebo jejich odstranění.|
| Nová předplatná s vysokou propustností | Detekuje nové odběry s účty stráví neobvykle vysokou mírou RU/s za den a poskytuje jim oznámení. Toto oznámení je speciálně zaměřeno na nové zákazníky, kteří Azure Cosmos DB fungovat na základě zřízeného modelu založeného na propustnosti a nevyužívají model založený na spotřebě. |

## <a name="migration-recommendations"></a>Doporučení pro migraci

V této kategorii Advisor detekuje, že používáte starší funkce, doporučuje migraci, takže můžete využívat Azure Cosmos DB obrovské škálovatelnost a další výhody. V současné době jsou k dispozici následující doporučení specifická pro migraci:

|Název  |Popis  |
|---------|---------|
| Kontejnery bez oddílů | Detekuje kontejnery pevné velikosti, které se blíží maximálnímu limitu úložiště, a doporučuje je migrovat do dělených kontejnerů.|

## <a name="query-usage-recommendations"></a>Doporučení pro použití dotazů

V této kategorii Advisor detekuje provádění dotazů a určí, že výkon dotazů může být vyladěn pomocí některých změn. V současné době jsou k dispozici následující doporučení k používání dotazů:

|Název  |Popis  |
|---------|---------|
| Dotazy s pevnou velikostí stránky | Detekuje dotazy vydané s pevnou velikostí stránky a doporučuje použít hodnotu-1 (bez omezení velikosti stránky) místo definování konkrétní hodnoty. Tato možnost snižuje počet síťových přenosů potřebných k načtení všech výsledků. |

## <a name="next-steps"></a>Další kroky

* [Ladění výkonu dotazů v Azure Cosmos DB](sql-api-query-metrics.md)
* [Řešení potíží s dotazy](troubleshoot-query-performance.md) při použití Azure Cosmos DB
