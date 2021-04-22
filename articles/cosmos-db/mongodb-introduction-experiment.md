---
title: Úvod k rozhraní Azure Cosmos DB API pro MongoDB
description: Naučte se, jak můžete použít Azure Cosmos DB k ukládání a dotazování obrovských objemů dat pomocí rozhraní API Azure Cosmos DB pro MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 04/20/2021
author: gahl-levy
ms.author: gahllevy
robots: noindex
ms.openlocfilehash: 04763aaf91b077223b8c2c5b4f56dc7289a9dbb4
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107893047"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>Rozhraní API služby Azure Cosmos DB pro MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Rozhraní Azure Cosmos DB API pro MongoDB usnadňuje použití Cosmos DB, jako by šlo o databázi MongoDB. Můžete využít prostředí MongoDB a nadále používat oblíbené ovladače MongoDB, sady SDK a nástroje, a to tak, že přejdete na aplikaci rozhraní API pro připojovací řetězec účtu MongoDB.

Rozhraní API pro MongoDB přináší následující výhody, které jsou postavené na [Azure Cosmos DB](introduction.md):

* **Okamžitá škálovatelnost**: tím, že povolíte funkci [automatického škálování](provision-throughput-autoscale.md) , vaše databáze může škálovat nahoru/dolů s nulovým zahřívání obdobím. 
* **Plně spravovaná horizontálního dělení**: Správa databázové infrastruktury je náročná a časově náročná. Rozhraní API pro MongoDB spravuje infrastrukturu pro vás, což zahrnuje horizontálního dělení. Takže se nemusíte starat o správu a soustředit se na čas při sestavování aplikací pro vaše uživatele.
* **Až 5 9 dostupnosti**: [dostupnost 99,999%](high-availability.md) se dá snadno konfigurovat, aby se zajistilo, že vaše data jsou vždycky pro vás.  
* **Nákladově efektivní a neomezená škálovatelnost**: kolekce horizontálně dělené se můžou škálovat na libovolnou velikost, a to v poměru k malému množství jako 1/100th virtuálního počítače kvůli úsporám a zásadám správného řízení prostředků.
* **Upgrade trvá několik sekund**: všechny verze rozhraní API jsou obsaženy v rámci jednoho základu kódu, takže se změny verze mění tak jednoduché jako [převrácení přepínače](mongodb-version-upgrade.md), s nulovými výpadky.
* **Analýzy propojení synapse**: Analyzujte data v reálném čase pomocí plně izolovaného [úložiště Azure synapse](synapse-link.md) Analytics pro rychlé a levné analytické dotazy. Jednoduché zaškrtávací políčko zajistí, aby vaše data byla k dispozici v synapse s využitím No-ETL (extrakce, transformace-Load).

> [!NOTE]
> [Rozhraní Azure Cosmos DB API pro MongoDB můžete používat zdarma s úrovní Free](how-pricing-works.md). U Azure Cosmos DB úrovně Free získáte k bezplatnému účtu první 400 RU/s a 5 GB úložiště, které se použije na úrovni účtu.


## <a name="how-the-api-works"></a>Jak funguje rozhraní API

Rozhraní Azure Cosmos DB API pro MongoDB implementuje přenosový protokol pro MongoDB. Tato implementace umožňuje transparentní kompatibilitu s nativními sadami SDK, ovladači a nástroji klienta MongoDB. Azure Cosmos DB nehostuje databázový stroj MongoDB. Jakýkoli ovladač klienta MongoDB kompatibilní s verzí rozhraní API, kterou používáte, by měl být schopný se připojit bez zvláštní konfigurace.

Kompatibilita funkcí MongoDB:

Rozhraní Azure Cosmos DB API pro MongoDB je kompatibilní s následujícími verzemi serveru MongoDB:
- [Verze 4,0](mongodb-feature-support-40.md)
- [Verze 3,6](mongodb-feature-support-36.md)
- [Verze 3,2](mongodb-feature-support.md)

Všechna rozhraní API pro verze MongoDB běží na stejném základu kódu, což provede upgrade jednoduché úlohy, která může být dokončena během několika sekund s nulovými výpadky. Azure Cosmos DB jednoduše Překlopí několik příznaků funkcí k přechodu z jedné verze do druhé.  Příznaky funkce také umožňují nepřetržitou podporu starších verzí rozhraní API, jako jsou 3,2 a 3,6. Můžete zvolit verzi serveru, která vám bude vyhovovat.

:::image type="content" source="./media/mongodb-introduction/cosmosdb-mongodb.png" alt-text="Rozhraní API služby Azure Cosmos DB pro MongoDB" border="false":::

## <a name="what-you-need-to-know-to-get-started"></a>Co potřebujete znát, abyste mohli začít

* Neúčtují se vám virtuální počítače v clusteru. [Ceny](how-pricing-works.md) jsou založené na propustnosti v jednotkách žádosti (ru) nakonfigurovaných pro jednotlivé databáze nebo na jednotlivé kolekce. Prvních 400 ru za sekundu je zdarma s [úrovní Free](how-pricing-works.md).

* Existují tři způsoby, jak nasadit rozhraní API Azure Cosmos DB pro MongoDB:
     * [Zřízená propustnost](set-throughput.md): nastavte číslo ru/s a ručně ho změňte. Tento model nejlépe odpovídá konzistentním úlohám.
     * [Automatické škálování](provision-throughput-autoscale.md): nastavte horní mez propustnosti, kterou potřebujete. Propustnost se okamžitě škáluje podle vašich potřeb. Tento model nejlépe odpovídá úlohám, které se často mění a optimalizují jejich náklady.
     * Bez [serveru](serverless.md) (Preview): platíte jenom za propustnost, kterou používáte, a období. Tento model nejlépe vyhovuje vývojovým a testovacím úlohám. 

* Výkon clusteru horizontálně dělené závisí na klíči horizontálních oddílů, který jste zvolili při vytváření kolekce. Pečlivě si horizontálních oddílů klíč, abyste zajistili, že vaše data jsou rovnoměrně rozložená mezi horizontálních oddílů.

## <a name="quickstart"></a>Rychlé zprovoznění

* [Migrujte existující Node.js webovou aplikaci v MongoDB](create-mongodb-nodejs.md).
* [Sestavení webové aplikace pomocí rozhraní API Azure Cosmos DB pro MongoDB a .NET SDK](create-mongodb-dotnet.md)
* [Vytvoření konzolové aplikace pomocí rozhraní API Azure Cosmos DB pro MongoDB a Java SDK](create-mongodb-java.md)

## <a name="next-steps"></a>Další kroky

* Pomocí kurzu [připojení aplikace MongoDB k Azure Cosmos DB](connect-mongodb-account.md) se dozvíte, jak získat informace o připojovacím řetězci k účtu.
* Postupujte podle kurzu [použití studia 3T with Azure Cosmos DB](mongodb-mongochef.md) a Naučte se, jak vytvořit propojení mezi databází Cosmos a aplikací MongoDB v studiu 3T.
* Postupujte podle kurzu [Import MongoDB data do Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) a importujte data do databáze Cosmos.
* Připojte se k účtu Cosmos pomocí [Robo 3T](mongodb-robomongo.md).
* Naučte se [konfigurovat předvolby pro čtení globálně distribuovaných aplikací](../cosmos-db/tutorial-global-distribution-mongodb.md).
* V našem [Průvodci odstraňováním potíží](mongodb-troubleshoot.md) najdete řešení pro běžně nalezené chyby.
