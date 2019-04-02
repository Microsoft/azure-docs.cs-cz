---
title: Zřizování propustnosti na kontejnery Azure Cosmos a databází
description: Zjistěte, jak nastavit zřízená propustnost pro kontejnery Azure Cosmos a databáze.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.openlocfilehash: 1f8bec6fbf0bce9a3ac272231058a96a5d9e84cc
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762493"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Zřízení propustnosti u kontejnerů a databází

Databáze Azure Cosmos je jednotka správy pro skupinu kontejnerů. Databáze obsahuje sadu kontejnerů dogmaticky na schématu. Kontejner služby Azure Cosmos je jednotka škálovatelnost pro propustnost a úložiště. Kontejner je horizontálně dělené do oddílů mezi sadu počítačů v rámci oblasti Azure a je distribuovaná napříč všemi oblastmi Azure spojené s vaším účtem Azure Cosmos.

Pomocí služby Azure Cosmos DB můžete zřídit propustnost v přírůstcích po dvou:
 
- Kontejnery služby Azure Cosmos
- Databáze Azure Cosmos

## <a name="set-throughput-on-a-container"></a>Nastavení propustnosti na kontejner  

Propustnost zřízený v kontejneru Azure Cosmos je rezervovaných exkluzivně pro tento kontejner. Kontejner obdrží zřízená propustnost celou dobu. Zřízenou propustnosti kontejneru finančně podložená smlouvami SLA. Další informace o konfiguraci propustnosti na kontejner, najdete v článku [zřizování propustnosti kontejneru Azure Cosmos](how-to-provision-container-throughput.md).

Nastavení zřízenou propustnosti kontejneru je nejčastěji používaných možností. Dá se pružně škálovat propustnost pro kontejner zřízením jakýkoli objem propustnost pomocí [jednotek žádosti (ru)](request-units.md). 

V kontejneru Azure Cosmos zřízenou propustnost je rovnoměrně mezi všechny logické oddíly kontejneru. Nelze zadat selektivně propustnost pro logické oddíly. Protože jeden nebo více logické oddíly kontejneru jsou hostované fyzický oddíl, fyzické oddíly patřit pouze do kontejneru a podporu v kontejneru zřízené propustnosti. 

Pokud úlohu spuštěnou na logický oddíl spotřebovává více než propustnost, která byla přidělena do tohoto logického oddílu, vaše operace získat rychlost limited. Pokud dojde k omezení rychlosti, můžete zvýšit zřízená propustnost pro celý kontejner nebo opakujte operaci. Další informace o dělení najdete v tématu [logické oddíly](partition-data.md).

Doporučujeme, abyste konfiguraci propustnosti na kontejner členitosti při zaručený výkon pro kontejner.

Následující obrázek ukazuje, jak fyzickým oddílem hostitelem jedné nebo více logické oddíly kontejneru:

![Fyzický oddíl](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Nastavení propustnosti na databázi

Když si zřídíte propustnost v databázi Azure Cosmos, propustnost je sdílen mezi všechny kontejnery v databázi. Výjimkou je, pokud jste zadali zřízenou propustnost na konkrétní kontejnery v databázi. Úrovni databáze zřízená propustnost mezi jeho kontejnery pro sdílení obsahu je obdobou hostující databázi na clusteru počítačů. Protože všechny kontejnery v rámci databáze sdílet prostředky, které jsou k dispozici na na počítači, přirozeně nelze získat předvídatelný výkon na žádný konkrétní kontejner. Další informace o konfiguraci zřízenou propustnost v databázi, naleznete v tématu [konfigurovat zřízenou propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).

Nastavení propustnosti na databázi Azure Cosmos zaručuje přijímat zřízená propustnost pro tuto databázi celou dobu. Protože všechny kontejnery v rámci databáze sdílejí zřízená propustnost, Azure Cosmos DB neposkytuje žádné hodnoty považují propustnost záruky pro konkrétní kontejner v této databázi. Část propustnost, které můžou přijímat konkrétní kontejner je závislá na:

* Počet kontejnerů.
* Volba klíče oddílu pro různé kontejnery.
* Rozdělení zatížení napříč různými logické oddíly kontejnerů. 

Doporučujeme nakonfigurovat propustnost v databázi, pokud chcete sdílet propustnost napříč více kontejnery, ale nechcete vyhradit propustnost pro jakékoli konkrétní kontejner. 

Následující příklady ukazují, kde má upřednostňované zřídit propustnost na úrovni databáze:

* Zřízená propustnost databázi pro sdílení obsahu mezi sadu kontejnerů je užitečná pro víceklientské aplikace. Každý uživatel může být reprezentována odlišné kontejneru Azure Cosmos.

* Zřízená propustnost databázi pro sdílení obsahu mezi sadu kontejnerů je užitečné, když migrace databáze NoSQL, jako je například MongoDB nebo Cassandru, hostovaná v clusteru virtuálních počítačů nebo z místních fyzických serverů do služby Azure Cosmos DB. Představte si zřízená propustnost nakonfigurovaný ve vaší databázi Azure Cosmos jako logický ekvivalent, ale nákladově efektivní a elastické na tento výpočetní kapacitu vašeho clusteru MongoDB nebo Cassandra.  

Všechny kontejnery, které vytvoří v databázi s zřízená propustnost je potřeba vytvořit s [klíč oddílu](partition-data.md). V libovolném časovém okamžiku čas propustnost přidělené kontejneru v databázi je distribuován na všechny logické oddíly tohoto kontejneru. Až budete mít kontejnery, které sdílejí zřízená propustnost, které jsou nakonfigurované na databázi, nelze selektivně použít propustnost pro konkrétní kontejner nebo logického oddílu. 

Pokud úlohy na logický oddíl spotřebovává více než propustnost, která je přidělena konkrétní logický oddíl, jsou vaše operace míra časově omezené. Pokud dojde k omezení rychlosti, můžete zvýšit propustnost pro celou databázi nebo opakujte operaci. Další informace o dělení najdete v tématu [logické oddíly](partition-data.md).

Více logické oddíly, které patří do různých kontejnerů, které sdílejí propustnosti zřízené databáze je možné hostovat na jeden fyzický oddíl. Během jednoho logického oddílu kontejneru vždy s rozsahem v rámci fyzický oddíl *"L"* logické oddíly napříč *"C"* kontejnery, které sdílejí zřízené propustnosti databáze může být mapovat a hostuje ho na *"R"* fyzické oddíly. 

Následující obrázek ukazuje, jak fyzický oddíl můžete hostovat jeden nebo více logické oddíly, které patří do různých kontejnerů v databázi:

![Fyzický oddíl](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Nastavení propustnosti na databázi a kontejner

Můžete kombinovat dva modely. Zřizování propustnosti na databázi a kontejner je povolen. Následující příklad ukazuje, jak zřídit propustnost v databázi Azure Cosmos a kontejneru:

* Můžete vytvořit databázi Azure Cosmos, s názvem *Z* se zřízenou propustností *"K"* RU. 
* Dále vytvořte pět kontejnery s názvem *A*, *B*, *C*, *D*, a *E* v databázi.
* Můžete explicitně nakonfigurovat *"P"* ru zřízenou propustnosti kontejneru s názvem *B*.
* *"K"* rezervovaných jednotek propustnosti je sdílen mezi čtyři kontejnery *A*, *C*, *D*, a *E*. Přesné množství propustnost, které jsou k dispozici *A*, *C*, *D*, nebo *E* se liší. Neexistují žádné smlouvy SLA pro jednotlivých jednotlivých kontejnerů o propustnosti.
* Kontejner s názvem *B* je zaručeno, že zobrazíte *"P"* rezervovaných jednotek propustnosti celou dobu. Se zajištěním smlouvy o úrovni služeb.

## <a name="update-throughput-on-a-database-or-a-container"></a>Aktualizace propustnosti na databázi nebo kontejneru

Po vytvoření kontejneru Azure Cosmos nebo databázi, můžete aktualizovat zřízenou propustnost. Maximální propustnosti, můžete nakonfigurovat na databázi nebo kontejner neexistuje žádné omezení. Minimální zřízená propustnost závisí na následujících faktorech: 

* Maximální velikost dat, ve kterém dříve uložený v kontejneru
* Maximální propustnost, které někdy zřídíte v kontejneru
* Maximální počet kontejnery Azure Cosmos, které můžete vytvářet v databázi s sdílené propustnost. 

Můžete programově načíst minimální propustnosti kontejneru nebo databáze pomocí sad SDK nebo zobrazit hodnotu na webu Azure Portal. Při použití sady .NET SDK [DocumentClient.ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) metoda umožňuje škálovat hodnoty zřízené propustnosti. Při použití sady Java SDK [RequestOptions.setOfferThroughput](sql-api-java-samples.md#offer-examples) metoda umožňuje škálovat hodnoty zřízené propustnosti. 

Při použití sady .NET SDK [DocumentClient.ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) metoda vám umožňuje načíst minimální propustnosti kontejneru nebo databáze. 

Zřízená propustnost kontejneru nebo databázi můžete škálovat v každém okamžiku. Můžete spustit operaci vertikální snížení kapacity po nečinnosti období 4 hodiny. Doba nečinnosti je definován jako čas období, kdy se žádné nabídky nahradit operací (to zahrnuje i horizontálního navýšení nebo snížení) na kontejner nebo databáze. 

## <a name="comparison-of-models"></a>Porovnání modelů

|**Parametr**  |**V databázi zřízenou propustnost**  |**V kontejneru zřízenou propustnost**|
|---------|---------|---------|
|Minimální ru |400 (po první čtyři kontejnery, každý další kontejner vyžaduje minimálně 100 ru za sekundu.) |400|
|Minimální jednotek požadavku za kontejner|100|400|
|Minimální ru potřeba využívat 1 GB úložiště|40|40|
|Maximální ru|Neomezeno na databázi.|Neomezené v kontejneru.|
|RU přiřazené nebo na konkrétní kontejner|Žádné záruky. RU přiřazen k danému kontejneru závisí na vlastnosti. Vlastnosti mohou být volba klíče oddílu kontejnerů, které sdílejí propustnost, distribuci zatížení a počet kontejnerů. |Všechny jednotky ru nakonfigurované v kontejneru jsou rezervovaných exkluzivně pro kontejner.|
|Maximální velikost úložiště pro kontejner|Neomezený počet.|Neomezený počet.|
|Maximální propustnost na logický oddíl kontejneru|10 tisíc ru|10 tisíc ru|
|Maximální velikost úložiště (data a index) na logický oddíl kontejneru|10 GB|10 GB|

## <a name="next-steps"></a>Další postup

* Další informace o [logické oddíly](partition-data.md).
* Zjistěte, jak [zřizování propustnosti kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* Zjistěte, jak [zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).

