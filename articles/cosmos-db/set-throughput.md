---
title: Zřizování propustnosti pro službu Azure Cosmos DB
description: Zjistěte, jak nastavit zřízená propustnost pro kontejnery služby Azure Cosmos DB a databáze.
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: andrl
ms.openlocfilehash: a97032344b904442ed3606c6297251578c3b4ff7
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263889"
---
# <a name="provision-throughput-on-azure-cosmos-containers-and-databases"></a>Zřizování propustnosti na kontejnery Azure Cosmos a databází

Databáze Azure Cosmos je jednotka správy pro skupinu kontejnerů. Databáze obsahuje sadu kontejnerů dogmaticky na schématu. Kontejner služby Azure Cosmos je jednotka škálovatelnost pro propustnost a úložiště. Kontejner je horizontálně dělené do oddílů mezi sadu počítačů v rámci oblasti Azure a je distribuovaná napříč všemi oblastmi Azure spojené s vaším účtem Azure Cosmos.

Azure Cosmos DB umožňuje nakonfigurovat propustnost v přírůstcích po dvou – **kontejnery Azure Cosmos** a **databáze Azure Cosmos**.

# <a name="setting-throughput-on-a-azure-cosmos-container"></a>Nastavení propustnosti kontejneru Azure Cosmos  

Propustnost zřízený v kontejneru Azure Cosmos je rezervovaných exkluzivně pro kontejner. Kontejner obdrží zřízená propustnost celou dobu. Zřízenou propustnosti kontejneru finančně podložená smlouvami SLA. Ke konfiguraci propustnosti na kontejner, naleznete v tématu [jak zřídit propustnosti kontejneru Azure Cosmos](how-to-provision-container-throughput.md).

Nastavení zřízenou propustnost v kontejneru je často používaný možnost. Přestože můžete Elasticky škálovat propustnost pro kontejner zřízením jakýkoli objem propustnosti (ru), nelze selektivně zadat propustnost pro logické oddíly. Až úlohu spuštěnou na logický oddíl spotřebovává více než propustnost, která byla přidělena konkrétní logický oddíl, zobrazí vaše operace míra limited. Pokud dojde k omezení rychlosti, můžete zvýšit propustnost pro celý kontejner nebo zkuste operaci zopakovat. Další informace o dělení najdete v tématu [logické oddíly](partition-data.md).

Doporučuje konfiguraci propustnosti na kontejner členitosti při zaručený výkon pro kontejner.

V kontejneru Azure Cosmos zřízenou propustnost je rovnoměrně mezi všechny logické oddíly kontejneru. Protože jeden nebo více logické oddíly kontejneru jsou hostitelem oddílu prostředků, fyzické oddíly patřit pouze do kontejneru a podporu ke kontejneru zřízené propustnosti. Následující obrázek ukazuje, jak oddíl prostředků hostitelem jedné nebo více logické oddíly kontejneru:

![Oddíl prostředků](./media/set-throughput/resource-partition.png)

# <a name="setting-throughput-on-a-azure-cosmos-database"></a>Nastavení propustnosti na databázi Azure Cosmos

Když si zřídíte propustnost v databázi Azure Cosmos, propustnost je sdílen mezi všechny kontejnery v databázi, pokud jste zadali zřízenou propustnost na konkrétní kontejnery. Sdílení propustnosti databáze mezi jejím kontejnerům je obdobou hostující databázi na clusteru počítačů. Protože všechny kontejnery v rámci databáze sdílet prostředky, které jsou k dispozici na na počítači, přirozeně nelze získat předvídatelný výkon na žádný konkrétní kontejner. Ke konfiguraci propustnosti na databázi, naleznete v tématu [konfigurace zřízenou propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).

Nastavení propustnosti na databázi Azure Cosmos zaručuje přijímat zřízená propustnost celou dobu. Od všech kontejnerů ve sdílené složce databáze zřízená propustnost služby Azure Cosmos DB neposkytuje žádné hodnoty považují propustnost záruky pro konkrétní kontejner v této databázi. Část propustnost, které můžou přijímat konkrétní kontejner je závislá na:

* Počet kontejnerů
* Volba klíče oddílu pro různé kontejnery a
* Rozdělení zatížení napříč různými logické oddíly kontejnerů. 

Je doporučeno nakonfigurovat propustnost v databázi, pokud chcete sdílet propustnost napříč více kontejnery, ale nechcete, aby vyhradit propustnost na žádný konkrétní kontejner. Toto jsou některé příklady, kde je upřednostňována před zřizování propustnosti na úrovni databáze:

* Zřízená propustnost databázi pro sdílení obsahu mezi sadu kontejnerů je užitečné pro aplikace s více tenanty. Každý uživatel může být reprezentována odlišné kontejneru Azure Cosmos.

* Zřízená propustnost databázi pro sdílení obsahu mezi sadu kontejnerů je užitečné, když provádíte migraci databáze NoSQL (třeba MongoDB, Cassandra) hostovaná v clusteru virtuálních počítačů nebo z místních fyzických serverů do služby Azure Cosmos DB. Zřízená propustnost nakonfigurovaný ve vaší databázi Azure Cosmos jako logický ekvivalent (ale více nákladově efektivní a flexibilních) na tento výpočetní kapacitu vašeho clusteru MongoDB nebo Cassandra si můžete představit.  

V libovolném časovém okamžiku čas propustnost přidělené kontejneru v databázi je distribuován na všechny logické oddíly tohoto kontejneru. Když máte zřízenou propustnost v databázi pro sdílení obsahu kontejnery, nelze selektivně použít propustnost pro konkrétní kontejner nebo logického oddílu. Pokud úlohy na logický oddíl spotřebovává více než propustnost, která je přidělena konkrétní logický oddíl, bude vaše operace míra limited. Pokud dojde k omezení rychlosti, můžete zvýšit propustnost pro celý kontejner nebo zkuste operaci zopakovat. Další informace o dělení najdete v tématu [logické oddíly](partition-data.md).

Více logické oddíly sdílení propustnosti zřízené databáze je možné hostovat na jeden prostředek oddílu. Během jednoho logického oddílu kontejneru vždy s rozsahem v rámci oddílu prostředků, "L" logické oddíly napříč "C" kontejnery zřízené propustnosti databáze pro sdílení obsahu můžete mapovat a hostované na fyzické oddíly "R". Následující obrázek ukazuje, jak oddíl prostředků můžete hostovat jeden nebo více logické oddíly, které patří do různých kontejnerů v databázi:

![Oddíl prostředků](./media/set-throughput/resource-partition2.png)

## <a name="setting-throughput-on-a-azure-cosmos-database-and-a-container"></a>Nastavení propustnosti na kontejner a databáze Azure Cosmos

Můžete kombinovat dva modely, zřizování propustnosti na databázi a kontejner je povolen. Následující příklad ukazuje, jak zřídit propustnost v databázi Azure Cosmos a kontejneru:

* Můžete vytvořit databázi Azure Cosmos se zřízenou propustností ru "K" s názvem "Z". 
* Dále vytvořte pět kontejnery s názvem A, B, C, D a elektronické v databázi.
* Můžete nakonfigurovat explicitně 'P' ru zřízenou propustnost v kontejneru "B".
* "K" rezervovaných jednotek propustnosti je sdílen mezi čtyři kontejnery – A, C, D a E. Přesné množství propustnost k dispozici A C, D nebo E se liší a neexistují žádné smlouvy SLA pro jednotlivých jednotlivých kontejnerů o propustnosti.
* Je zaručeno, že kontejner "B" zjištění propustnosti 'P' ru neustále a podložená smlouvami SLA.

## <a name="comparison-of-models"></a>Porovnání modelů

|**Kvóta**  |**V databázi zřízenou propustnost**  |**V kontejneru zřízenou propustnost**|
|---------|---------|---------|
|Minimální ru |400 |400|
|Minimální jednotek požadavku za kontejner|100|400|
|Minimální ru potřeba využívat 1 GB úložiště|40|40|
|Maximální ru|Neomezený počet v databázi|Neomezený počet v kontejneru|
|RU přiřazené/k dispozici pro konkrétní kontejner|Žádné záruky. RU přiřazen k danému kontejneru závisí na vlastnosti, jako je – volba klíče oddílu kontejnerů, které sdílejí propustnost, distribuci zatížení, počet kontejnerů. |Všechny jednotky ru nakonfigurované v kontejneru jsou rezervovaných exkluzivně pro kontejner.|
|Maximální velikost úložiště pro kontejner|Unlimited|Unlimited|
|Maximální propustnost na logický oddíl kontejneru|10 tisíc ru|10 tisíc ru|
|Maximální velikost úložiště (data a index) na logický oddíl kontejneru|10 GB|10 GB|

## <a name="next-steps"></a>Další postup

* Další informace o [logickými oddíly](partition-data.md)
* Přečtěte si [jak zřídit propustnosti kontejneru Azure Cosmos](how-to-provision-container-throughput.md)
* Přečtěte si [jak zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md)

