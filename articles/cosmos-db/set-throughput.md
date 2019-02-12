---
title: Zřizování propustnosti na kontejnery Azure Cosmos a databází
description: Zjistěte, jak nastavit zřízená propustnost pro kontejnery Azure Cosmos a databáze.
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: andrl
ms.openlocfilehash: 439b48c271260e9744bb9c9ca0e2b21e61cf4687
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005059"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Zřízení propustnosti u kontejnerů a databází

Databáze Azure Cosmos je jednotka správy pro skupinu kontejnerů. Databáze obsahuje sadu kontejnerů dogmaticky na schématu. Kontejner služby Azure Cosmos je jednotka škálovatelnost pro propustnost a úložiště. Kontejner je horizontálně dělené do oddílů mezi sadu počítačů v rámci oblasti Azure a je distribuovaná napříč všemi oblastmi Azure spojené s vaším účtem Azure Cosmos.

Pomocí služby Azure Cosmos DB můžete nakonfigurovat propustnost v přírůstcích po dvou:
 
- Kontejnery služby Azure Cosmos
- Databáze Azure Cosmos

## <a name="set-throughput-on-a-container"></a>Nastavení propustnosti na kontejner  

Propustnost zřízený v kontejneru Azure Cosmos je rezervovaných exkluzivně pro kontejner. Kontejner obdrží zřízená propustnost celou dobu. Zřízenou propustnosti kontejneru finančně podložená smlouvami SLA. Ke konfiguraci propustnosti na kontejner, naleznete v tématu [zřizování propustnosti kontejneru Azure Cosmos](how-to-provision-container-throughput.md).

Nastavení zřízenou propustnost v kontejneru je často používaný možnost. Dá se pružně škálovat propustnost pro kontejner zřízením jakýkoli objem propustnost pomocí jednotek žádosti (ru). Nemůžete ale zadat selektivně propustnost pro logické oddíly. 

Pokud úlohu spuštěnou na logický oddíl spotřebovává více než propustnost, která byla přidělena konkrétní logického oddílu, vaše operace získat rychlost limited. Pokud dojde k omezení rychlosti, můžete zvýšit propustnost pro celý kontejner nebo opakujte operaci. Další informace o dělení najdete v tématu [logické oddíly](partition-data.md).

Doporučujeme, abyste konfiguraci propustnosti na kontejner členitosti při zaručený výkon pro kontejner.

V kontejneru Azure Cosmos zřízenou propustnost je rovnoměrně mezi všechny logické oddíly kontejneru. Protože jeden nebo více logické oddíly kontejneru jsou hostované fyzický oddíl, fyzické oddíly patřit pouze do kontejneru a podporu v kontejneru zřízené propustnosti. 

Následující obrázek ukazuje, jak fyzickým oddílem hostitelem jedné nebo více logické oddíly kontejneru:

![Fyzický oddíl](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Nastavení propustnosti na databázi

Když si zřídíte propustnost v databázi Azure Cosmos, propustnost je sdílen mezi všechny kontejnery v databázi. Výjimkou je, pokud jste zadali zřízenou propustnost na konkrétní kontejnery. Sdílení propustnosti databáze mezi jejím kontejnerům je obdobou hostující databázi na clusteru počítačů. Protože všechny kontejnery v rámci databáze sdílet prostředky, které jsou k dispozici na na počítači, přirozeně nelze získat předvídatelný výkon na žádný konkrétní kontejner. Ke konfiguraci propustnosti na databázi, naleznete v tématu [konfigurovat zřízenou propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).

Nastavení propustnosti na databázi Azure Cosmos zaručuje přijímat zřízená propustnost celou dobu. Protože všechny kontejnery v rámci databáze sdílejí zřízená propustnost, Azure Cosmos DB neposkytuje žádné hodnoty považují propustnost záruky pro konkrétní kontejner v této databázi. Část propustnost, které můžou přijímat konkrétní kontejner je závislá na:

* Počet kontejnerů.
* Volba klíče oddílu pro různé kontejnery.
* Rozdělení zatížení napříč různými logické oddíly kontejnerů. 

Doporučujeme nakonfigurovat propustnost v databázi, pokud chcete sdílet propustnost napříč více kontejnery, ale nechcete vyhradit propustnost pro jakékoli konkrétní kontejner. 

Následující příklady ukazují, kde má upřednostňované zřídit propustnost na úrovni databáze:

* Zřízená propustnost databázi pro sdílení obsahu mezi sadu kontejnerů je užitečná pro víceklientské aplikace. Každý uživatel může být reprezentována odlišné kontejneru Azure Cosmos.

* Zřízená propustnost databázi pro sdílení obsahu mezi sadu kontejnerů je užitečné, když migrace databáze NoSQL, jako je například MongoDB nebo Cassandru, hostovaná v clusteru virtuálních počítačů nebo z místních fyzických serverů do služby Azure Cosmos DB. Představte si zřízená propustnost nakonfigurovaný ve vaší databázi Azure Cosmos jako logický ekvivalent, ale nákladově efektivní a elastické na tento výpočetní kapacitu vašeho clusteru MongoDB nebo Cassandra.  

Všechny kontejnery, které vytvoří v databázi se zřízenou propustností musí být vytvořená s klíčem oddílu. V libovolném časovém okamžiku čas propustnost přidělené kontejneru v databázi je distribuován na všechny logické oddíly tohoto kontejneru. Až budete mít kontejnery, které sdílejí zřízenou propustnost v databázi, nelze selektivně použít propustnost pro konkrétní kontejner nebo logického oddílu. 

Pokud úlohy na logický oddíl spotřebovává více než propustnost, která je přidělena konkrétní logický oddíl, jsou vaše operace míra časově omezené. Pokud dojde k omezení rychlosti, můžete zvýšit propustnost pro celý kontejner nebo opakujte operaci. Další informace o dělení najdete v tématu [logické oddíly](partition-data.md).

Více logické oddíly, které sdílejí propustnosti zřízené databáze je možné hostovat na jeden fyzický oddíl. Během jednoho logického oddílu kontejneru vždy s rozsahem v rámci fyzický oddíl, "L" logické oddíly napříč kontejnery "C", které sdílejí zřízené propustnosti databáze můžete mapovat a hostované na fyzické oddíly "R". 

Následující obrázek ukazuje, jak fyzický oddíl můžete hostovat jeden nebo více logické oddíly, které patří do různých kontejnerů v databázi:

![Fyzický oddíl](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Nastavení propustnosti na databázi a kontejner

Můžete kombinovat dva modely. Zřizování propustnosti na databázi a kontejner je povolen. Následující příklad ukazuje, jak zřídit propustnost v databázi Azure Cosmos a kontejneru:

* Můžete vytvořit databázi Azure Cosmos se zřízenou propustností ru "K" s názvem Z. 
* Dále vytvořte pět kontejnery s názvem A, B, C, D a elektronické v databázi.
* Můžete explicitně nakonfigurovat "P" ru zřízenou propustnost na kontejner s názvem B.
* "K" rezervovaných jednotek propustnosti je sdílen mezi čtyři kontejnery A, C, D a E. Přesné množství propustnost k dispozici A C, D nebo elektronické liší. Neexistují žádné smlouvy SLA pro jednotlivých jednotlivých kontejnerů o propustnosti.
* Kontejner s názvem B je zaručeno, že získáte "P" rezervovaných jednotek propustnosti. Se zajištěním smlouvy o úrovni služeb.

## <a name="comparison-of-models"></a>Porovnání modelů

|**Kvóta**  |**V databázi zřízenou propustnost**  |**V kontejneru zřízenou propustnost**|
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

