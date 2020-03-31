---
title: Zřizování propustnost na kontejnerech a databázích Azure Cosmos
description: Zjistěte, jak nastavit zřízenou propustnost pro vaše kontejnery a databáze Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2019
ms.openlocfilehash: e7a64776cba00a6840af70cecad5bf9c02b3f38e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251970"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Zřízení propustnosti u kontejnerů a databází

Databáze Azure Cosmos je jednotka správy pro sadu kontejnerů. Databáze se skládá ze sady kontejnerů bez schématu. Kontejner Azure Cosmos je jednotka škálovatelnosti pro propustnost i úložiště. Kontejner je horizontálně rozdělený napříč sadou počítačů v rámci oblasti Azure a distribuuje se ve všech oblastech Azure přidružených k vašemu účtu Azure Cosmos.

S Azure Cosmos DB můžete zřídit propustnost na dvě granularities:
 
- Kontejnery Azure Cosmos DB
- Databáze Azure Cosmos DB

## <a name="set-throughput-on-a-container"></a>Nastavení propustnostna kontejneru  

Propustnost zřízená v kontejneru Azure Cosmos je vyhrazena výhradně pro tento kontejner. Kontejner obdrží zřízenou propustnost po celou dobu. Zřízená propustnost v kontejneru je finančně zálohována sla. Informace o konfiguraci propustnost v kontejneru najdete v tématu [Provision propustnost na kontejneru Azure Cosmos](how-to-provision-container-throughput.md).

Nastavení zřízená propustnost v kontejneru je nejčastěji používanou možností. Propustnost kontejneru můžete elasticky škálovat zřízením libovolného množství propustky pomocí [jednotek požadavku (RU).](request-units.md) 

Propustnost zřízená pro kontejner je rovnoměrně rozdělena mezi jeho fyzické oddíly a za předpokladu, že dobrý klíč oddílu, který rovnoměrně rozděluje logické oddíly mezi fyzické oddíly, je propustnost také rovnoměrně rozložena napříč všemi logické oddíly kontejneru. Nelze selektivně určit propustnost pro logické oddíly. Vzhledem k tomu, že jeden nebo více logických oddílů kontejneru jsou hostovány fyzickým oddílem, fyzické oddíly patří výhradně do kontejneru a podporují propustnost zřízenou v kontejneru. 

Pokud zatížení spuštěné na logickém oddílu spotřebovává více než propustnost, která byla přidělena tomuto logickému oddílu, vaše operace získat omezenou míru. Pokud dojde k omezení rychlosti, můžete buď zvýšit zřízenou propustnost pro celý kontejner, nebo opakovat operace. Další informace o dělení naleznete v [tématu Logické oddíly](partition-data.md).

Doporučujeme nakonfigurovat propustnost na rozlišovací schopnost kontejneru, pokud chcete zaručený výkon pro kontejner.

Následující obrázek ukazuje, jak fyzický oddíl hostuje jeden nebo více logických oddílů kontejneru:

![Fyzický oddíl](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Nastavení propustnosti v databázi

Když zřídíte propustnost v databázi Azure Cosmos, propustnost se sdílí napříč všemi kontejnery (nazývanými sdílené databázové kontejnery) v databázi. Výjimkou je, pokud jste zadali zřízenou propustnost na konkrétní kontejnery v databázi. Sdílení zřízené propustnosti na úrovni databáze mezi jeho kontejnery je obdobou hostování databáze na clusteru počítačů. Vzhledem k tomu, že všechny kontejnery v rámci databáze sdílet prostředky, které jsou k dispozici v počítači, přirozeně nezískáte předvídatelný výkon na konkrétní kontejner. Informace o konfiguraci zřízené propustnosti v databázi najdete v [tématu Konfigurace zřízené propustnosti v databázi Azure Cosmos](how-to-provision-database-throughput.md).

Nastavení propustnost v databázi Azure Cosmos zaručuje, že obdržíte zřízenou propustnost pro tuto databázi po celou dobu. Vzhledem k tomu, že všechny kontejnery v rámci databáze sdílejí zřízené propustnost, Azure Cosmos DB neposkytuje žádné předvídatelné záruky propustnost pro konkrétní kontejner v této databázi. Část propustnost, kterou může přijímat konkrétní kontejner, závisí na:

* Počet kontejnerů.
* Volba klíče oddílu pro různé kontejnery.
* Distribuce zatížení mezi různé logické oddíly kontejnerů. 

Doporučujeme nakonfigurovat propustnost v databázi, pokud chcete sdílet propustnost mezi více kontejnery, ale nechcete vyhradit propustnost pro konkrétní kontejner. 

Následující příklady ukazují, kde je upřednostňováno zřídit propustnost na úrovni databáze:

* Sdílení zřízené propustnost databáze v sadě kontejnerů je užitečné pro víceklientské aplikace. Každý uživatel může být reprezentován odlišným kontejnerem Azure Cosmos.

* Sdílení zřízené propustnosti databáze napříč sadou kontejnerů je užitečné při migraci databáze NoSQL, jako je MongoDB nebo Cassandra, hostované v clusteru virtuálních počítačů nebo z místních fyzických serverů do Azure Cosmos DB. Zřizování propustnosti nakonfigurované v databázi Azure Cosmos jako logický ekvivalent, ale nákladově efektivnější a elastičtější, aby výpočetní kapacity vašeho clusteru MongoDB nebo Cassandra clusteru.  

Všechny kontejnery vytvořené uvnitř databáze s zřízenou propustností musí být vytvořeny pomocí [klíče oddílu](partition-data.md). V libovolném okamžiku propustnost přidělené kontejneru v rámci databáze je distribuován a všechny logické oddíly tohoto kontejneru. Pokud máte kontejnery, které sdílejí zřízenou propustnost nakonfigurovanou v databázi, nelze selektivně použít propustnost na konkrétní kontejner nebo logický oddíl. 

Pokud zatížení na logickém oddílu spotřebovává více než propustnost, která je přidělena na konkrétní logický oddíl, vaše operace jsou omezeny sazby. Dojde-li k omezení rychlosti, můžete buď zvýšit propustnost pro celou databázi, nebo opakovat operace. Další informace o dělení naleznete v [tématu Logické oddíly](partition-data.md).

Kontejnery v databázi sdílené propustnost i sdílet propustnost (RU/s) přidělené do této databáze. V databázi můžete mít až čtyři kontejnery s minimálně 400 RU/s. Každý nový kontejner po prvních čtyřech bude vyžadovat další chod 100 RU/s. Například pokud máte sdílenou propustnost databáze s osmi kontejnery, minimální RU/s v databázi bude 800 RU/s.

> [!NOTE]
> V únoru 2020 jsme zavedli změnu, která umožňuje mít maximálně 25 kontejnerů v databázi sdílené propustnosti, což lépe umožňuje sdílení propustnosti mezi kontejnery. Po prvních 25 kontejnerů, můžete přidat další kontejnery do databáze pouze v případě, že jsou [zřízeny s vyhrazenou propustnost](#set-throughput-on-a-database-and-a-container), která je oddělena od sdílené propustnost databáze.<br>
Pokud váš účet Azure Cosmos DB už obsahuje sdílenou databázi propustností s >= 25 kontejnerů, účet a všechny ostatní účty ve stejném předplatném Azure jsou z této změny osvobozeny. Pokud máte zpětnou vazbu nebo dotazy, obraťte se na [odbornou pomoc.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 

Pokud vaše úlohy zahrnují odstranění a opětovné vytvoření všech kolekcí v databázi, doporučujeme přetáhnout prázdnou databázi a znovu vytvořit novou databázi před vytvořením kolekce. Následující obrázek ukazuje, jak může fyzický oddíl hostovat jeden nebo více logických oddílů, které patří do různých kontejnerů v databázi:

![Fyzický oddíl](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Nastavení propustnosti databáze a kontejneru

Můžete kombinovat dva modely. Zřizování propustnost na databázi a kontejneru je povoleno. Následující příklad ukazuje, jak zřídit propustnost v databázi Azure Cosmos a kontejneru:

* Můžete vytvořit databázi Azure Cosmos s názvem *Z* s zřízenou propustností *"K"* RU. 
* Dále vytvořte pět kontejnerů s názvem *A*, *B*, *C*, *D*a *E* v rámci databáze. Při vytváření kontejneru B, ujistěte se, že povolit **provision vyhrazené propustnost pro tuto** možnost kontejneru a explicitně nakonfigurovat *"P"* RU zřízená propustnost na tomto kontejneru. Všimněte si, že můžete nakonfigurovat sdílené a vyhrazené propustnost pouze při vytváření databáze a kontejneru. 

   ![Nastavení propustnostna na úrovni kontejneru](./media/set-throughput/coll-level-throughput.png)

* Propustnost *"K"* ru je sdílena mezi čtyřmi kontejnery *A*, *C*, *D*a *E*. Přesné množství propustnosti dostupné pro *A*, *C*, *D*nebo *E* se liší. Neexistují žádné sla pro propustnost každého jednotlivého kontejneru.
* Kontejner s názvem *B* je zaručeno, že získat propustnost *"P"* RU po celou dobu. Je podporován a SLA.

> [!NOTE]
> Kontejner s zřízenou propustností nelze převést na kontejner sdílené databáze. Naopak kontejner sdílené databáze nelze převést na vyhrazenou propustnost.

## <a name="update-throughput-on-a-database-or-a-container"></a>Aktualizace propustnost v databázi nebo kontejneru

Po vytvoření kontejneru Azure Cosmos nebo databáze, můžete aktualizovat zřízené propustnost. Neexistuje žádné omezení maximální zřízené propustnost, kterou můžete nakonfigurovat v databázi nebo kontejneru. [Minimální zřízená propustnost](concepts-limits.md#storage-and-throughput) závisí na následujících faktorech: 

* Maximální velikost dat, kterou jste kdy uložili do kontejneru
* Maximální propustnost, kterou jste kdy do kontejneru zadali
* Aktuální počet kontejnerů Azure Cosmos, které máte v databázi se sdílenou propustností. 

Můžete načíst minimální propustnost kontejneru nebo databáze programově pomocí sad SDK nebo zobrazit hodnotu na webu Azure Portal. Při použití sady .NET SDK umožňuje metoda [DocumentClient.ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) škálovat zřízené hodnoty propustnosti. Při použití java sdk, [RequestOptions.setOfferThroughput](sql-api-java-samples.md#offer-examples) metoda umožňuje škálovat zřízené hodnoty propustnost. 

Při použití sady .NET SDK umožňuje metoda [DocumentClient.ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) načíst minimální propustnost kontejneru nebo databáze. 

Zřízenou propustnost kontejneru nebo databáze můžete kdykoli škálovat. Při operaci škálování ke zvýšení propustnosti, může trvat delší dobu z důvodu systémové úkoly zřídit požadované zdroje. Můžete zkontrolovat stav operace škálování na webu Azure Portal nebo programově pomocí sad SDK. Při použití sady .Net SDK můžete získat stav operace `DocumentClient.ReadOfferAsync` škálování pomocí metody.

## <a name="comparison-of-models"></a>Porovnání modelů

|**Parametr**  |**Propustnost zřízená v databázi**  |**Propustnost zřízená na kontejneru**|
|---------|---------|---------|
|Minimální ru |400 (Po prvních čtyřech kontejnerech vyžaduje každý další kontejner minimálně 100 ru za sekundu.) |400|
|Minimální ru na kontejner|100|400|
|Maximální ru|Neomezené, v databázi.|Neomezené, na kontejneru.|
|Ru přiřazené nebo dostupné pro konkrétní kontejner|Žádné záruky. Ru přiřazené k danému kontejneru závisí na vlastnostech. Vlastnosti může být volba klíče oddílů kontejnerů, které sdílejí propustnost, distribuce úlohy a počet kontejnerů. |Všechny ru nakonfigurované v kontejneru jsou vyhrazeny výhradně pro kontejner.|
|Maximální skladování kontejneru|Neomezené.|Neomezené.|
|Maximální propustnost na logický oddíl kontejneru|10K RU|10K RU|
|Maximální úložiště (data + index) na logický oddíl kontejneru|20 GB|20 GB|

## <a name="next-steps"></a>Další kroky

* Další informace o [logických oddílech](partition-data.md).
* Zjistěte, jak [zřídit propustnost v kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* Zjistěte, jak [zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).

