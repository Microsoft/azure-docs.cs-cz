---
title: Úvod do Azure Cosmos DB Graph API | Microsoft Docs
description: Zjistěte, jak můžete pomocí Azure Cosmos DB uložení dotazu a procházení masivní grafy s nízkou latencí pomocí dotazovacího jazyka pro graf Gremlin systému Apache TinkerPop.
services: cosmos-db
author: LuisBosquez
documentationcenter: ''
ms.assetid: b916644c-4f28-4964-95fe-681faa6d6e08
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/05/2017
ms.author: lbosq
ms.openlocfilehash: 50030434f3b2e59ba97efca2946be1a5115bc145
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="introduction-to-azure-cosmos-db-graph-api"></a>Úvod do Azure Cosmos DB: Graph API

[Azure Cosmos DB](introduction.md) je služba databáze globálně distribuované, multimodel od společnosti Microsoft pro kritické aplikace. Azure Cosmos DB poskytuje následující funkce, které jsou zajišťované pomocí [špičkový SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/):

* [Globální distribuce na klíč](distribute-data-globally.md)
* [Elastické škálování propustnost a úložiště](partition-data.md) po celém světě
* Jednociferné milisekundu latence v 99th percentilu
* [Pět dobře definované úrovně konzistence](consistency-levels.md)
* Zaručit vysoká dostupnost 

Azure Cosmos DB [automaticky indexuje data](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), aniž by vyžadovala zapojení správy schémat a indexů. Je multimodel a podporuje dokumentu, klíč hodnota, grafu a sloupcová datové modely.

Doporučujeme vám, že jste v následujícím videu, kde Kirill Gavrylyuk vysvětluje, jak začít pracovat s grafy v Azure Cosmos DB:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Graphs-with-Azure-Cosmos-DB-Gremlin-API/player]
> 
> 

Poskytuje rozhraní API služby Azure DB Cosmos grafu:

- Graf modelování.
- Procházení rozhraní API.
- To globální distribuce.
- Elastické škálování úložiště a propustnost s méně než 10 ms, přečtěte si latenci a méně než 15 ms v 99th percentil.
- Automatické indexování s dostupností rychlých dotazu.
- Přizpůsobitelné úrovně konzistence.
- Komplexní SLA, včetně 99,99 % dostupnost SLA pro všechny účty jedné oblasti a všechny oblasti s více účty s volný konzistence a 99.999 % přečíst dostupnosti pro všechny účty databáze více oblast.

K dotazování databáze Cosmos Azure, můžete použít [Apache TinkerPop](http://tinkerpop.apache.org) graf traversal jazyk [Gremlin](http://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps), nebo jiné systémy TinkerPop kompatibilní grafu jako [Apache Spark GraphX](spark-connector-graph.md).

Tento článek obsahuje přehled Azure Cosmos DB Graph API a vysvětluje, jak můžete použít k ukládání velkých grafy s až miliardy vrcholy a okrajů. Se můžete dotazovat grafy s latencí milisekundu a snadno momentální grafu strukturu a schéma.

## <a name="graph-database"></a>Graf databáze
Data jak se objevuje v reálném světě se přirozeně připojen. Modelování tradiční dat se zaměřuje na entity. Pro mnoho aplikací je také zapotřebí model nebo model entity a vztahy přirozeně.

A [grafu](http://mathworld.wolfram.com/Graph.html) je struktura, která se skládá z [vrcholy](http://mathworld.wolfram.com/GraphVertex.html) a [okraje](http://mathworld.wolfram.com/GraphEdge.html). Vrcholy a okrajů může mít libovolný počet vlastností. Vrcholy označují diskrétní objekty, jako je osoba, místo nebo událost. Okraje označují vztahy mezi vrcholy. Například uživatel může vědět jinou osobou, být zahrnut v události a nedávno v umístění. Vlastnosti express informace o vrcholy a okrajů. Příklad vlastnosti zahrnují vrchol, který má název, stáří a okraj, který má časové razítko nebo váhou. Více oficiálně tento model se označuje jako [vlastnost grafu](http://tinkerpop.apache.org/docs/current/reference/#intro). Azure Cosmos DB podporuje model vlastnost grafu.

Například následující ukázkový graf znázorňuje vztahy mezi osoby, mobilní zařízení, zájmů a operační systémy:

![Zobrazuje osob, zařízení a zájmů ukázkové databáze](./media/graph-introduction/sample-graph.png)

Grafy jsou užitečné zjistit široké škály datových sad v vědecké účely, technologie a business. Graf databáze umožňují modelu a ukládání grafů přirozeně a efektivně, což je užitečné díky pro mnoho scénářů. Graf databáze jsou obvykle databáze NoSQL, protože tyto použití případů často také nutné flexibilitu schémat a rychlé iterací.

Grafy nabízejí nové a výkonné data modelování techniku. Ale tuto skutečnost sám o sobě není dostatečná důvod k použití databáze grafu. Pro mnoho případy použití a vzorů, které zahrnují traversals grafu grafy překonat tradiční databáze SQL a NoSQL podle pořadí podle velikosti. Tento rozdíl ve výkonu je rozšířena další při procházení více než jedna relace, jako je friend z friend.

Rychlé traversals, které poskytují grafu databází můžete kombinovat s algoritmy grafu jako první hloubka vyhledávání, první spektra vyhledávání a Dijkstra je algoritmus, k řešení problémů v různých doménách sociálních sítí, správy obsahu, geoprostorové, a doporučení.

## <a name="planet-scale-graphs-with-azure-cosmos-db"></a>Grafy planetu škálování s Azure Cosmos DB
Azure Cosmos DB je plně spravovaná grafu databáze, která nabízí globální distribuční elastické škálování úložiště a propustnost, automatické indexování a dotaz, přizpůsobitelné úrovně konzistence a podpora pro standardní TinkerPop.

![Azure Cosmos DB – architektura grafu](./media/graph-introduction/cosmosdb-graph-architecture.png)

Azure Cosmos DB nabízí následující možnosti odlišné ve srovnání s jiných databází grafu na trhu:

* Elasticky škálovatelná propustnost a úložiště

 Grafy ve skutečnosti muset škálování nad rámec kapacitu jeden server. S Azure Cosmos databáze je možné škálovat vaše grafy bezproblémově více serverů. Také je možné škálovat propustnost vaší grafu nezávisle podle vaší vzorů přístupu. Azure Cosmos DB podporuje grafu databáze, které je možné škálovat na prakticky neomezené velikosti úložiště a zřízené propustnosti.

* Replikace více oblast

 Azure Cosmos DB transparentně replikuje data grafu do všech oblastech, které jste spojené s vaším účtem. Replikace umožňuje vyvíjet aplikace, které vyžadují globální přístup k datům. V oblasti konzistence, dostupnost a výkon a odpovídající záruky jsou kompromisy. Azure Cosmos DB poskytuje transparentní regionální převzetí služeb při selhání s více funkci rozhraní API. Po celém světě můžete Elasticky škálovat propustnost a úložiště.

* Rychlé dotazy a traversals pomocí známé syntaxe pro Gremlin

 Ukládat heterogenní vrcholy a okrajů a dotazování pomocí známé syntaxe Gremlin těchto dokumentů. Azure Cosmos DB využívá vysoce souběžnou uvolnění zámku, strukturovaná protokolu technologii indexování automaticky indexuje všechny obsah. Tato funkce umožňuje bohaté dotazy v reálném čase a traversals bez potřeba specifikovat parametry schématu, sekundární indexy nebo zobrazení. Další informace v [dotazovat grafy pomocí Gremlin](gremlin-support.md).

* Plně spravovaná

 Azure Cosmos DB eliminuje potřeba spravovat prostředky databáze a počítačů. Jako plně spravovaná služba Microsoft Azure není nutné spravovat virtuální počítače, nasadit a konfigurovat software, spravovat škálování nebo řešit komplexní datové vrstvě upgrady. Každý grafu je automaticky zálohovat a chránit proti místní selhání. Můžete snadno přidat účet Azure Cosmos DB a zřídit kapacitu podle potřeby tak, aby se můžete soustředit na svou aplikaci, ne provoz a správu databáze.

* Automatické indexování

 Ve výchozím nastavení Azure Cosmos DB automaticky indexuje všechny vlastnosti v rámci uzlů a okraje v grafu a nemá očekávat nebo nevyžaduje žádné schéma nebo vytváření sekundárních indexů.

* Kompatibilita s Apache TinkerPop

 Azure Cosmos DB nativně podporuje standardní open source Apache TinkerPop a umožňuje integraci s jinými systémy povoleno TinkerPop grafu. Ano, můžete snadno migrovat z jiné databáze grafů, jako je Titan nebo Neo4j, nebo pomocí graf analýzy architektury, jako je Azure Cosmos DB [Apache Spark GraphX](spark-connector-graph.md).

* Přizpůsobitelné úrovně konzistence

 Vyberte z pěti dobře definované úrovně konzistence zajistit optimální kompromis mezi konzistencí a výkonem. Pro dotazy a operace čtení nabízí služba Azure Cosmos DB pět různých úrovní konzistence: silná, omezená neaktuálnost, relace, konzistentní předpona a konečný výsledek. Tyto úrovně konzistence podrobné, dobře definované umožňují zvukové kompromisy mezi konzistence, dostupností a latencí. Další informace v [úrovně konzistence přizpůsobitelné dat v Azure Cosmos DB](consistency-levels.md).

Azure Cosmos DB také můžete použít více modelů, jako je dokument a graf, v rámci stejné kontejnery nebo databáze. Kolekce dokumentů můžete použít k ukládání dat grafu node souběžně s dokumenty. Dotazy SQL za JSON a Gremlin dotazy můžete použít k dotazování stejná data jako graf.

## <a name="get-started"></a>Začínáme
Můžete rozhraní příkazového řádku Azure (CLI), Azure PowerShell nebo portálu Azure s podporou pro rozhraní graph API k vytváření účtů Azure Cosmos DB. Po vytvoření účtů, portál Azure poskytuje koncového bodu služby, jako je třeba `https://<youraccount>.gremlin.cosmosdb.azure.com`, která poskytuje front-end protokolu WebSocket pro Gremlin. Můžete nakonfigurovat vaše TinkerPop kompatibilní nástroje, jako je třeba [Gremlin konzoly](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console), k připojení k tomuto koncovému bodu a vytvářet aplikace v jazyce Java, Node.js nebo všechny Gremlin ovladače klienta.

V následující tabulce jsou uvedeny oblíbených Gremlin ovladače, které můžete použít pro Azure Cosmos DB:

| Ke stažení | Dokumentace | Začínáme |
| --- | --- | --- |
| [.NET](http://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-DotNet) | [Gremlin.NET na Githubu](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [Vytvoření grafu pomocí rozhraní .NET](create-graph-dotnet.md) |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Gremlin JavaDoc](http://tinkerpop.apache.org/javadocs/current/full/) | [Vytvoření grafu používá Java](create-graph-java.md) |
| [Node.js](https://www.npmjs.com/package/gremlin) | [Gremlin JavaScript na Githubu](https://github.com/jbmusso/gremlin-javascript) | [Vytvoření grafu pomocí Node.js](create-graph-nodejs.md) |
| [Python](http://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Gremlin Python na Githubu](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Vytvoření grafu používá Python](create-graph-python.md) |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Gremlin PHP na Githubu](https://github.com/PommeVerte/gremlin-php) | [Vytvoření grafu používá PHP](create-graph-php.md) |
| [Gremlin konzoly](https://tinkerpop.apache.org/downloads.html) | [TinkerPop dokumentace](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Vytvoření grafu pomocí konzoly Gremlin](create-graph-gremlin-console.md) |

## <a name="scenarios-for-graph-support-of-azure-cosmos-db"></a>Scénáře pro graf podpoře Azure Cosmos DB
Tady je několik scénářů, kde je možné grafu podpoře Azure Cosmos DB:

* Sociálních sítí

 Kombinací data o vašich zákazníků a jejich interakce s jinými lidmi, můžete vyvíjet přizpůsobené prostředí, předpovědi chování zákazníků nebo spojení uživatelů s ostatními s podobnými zájmy. Azure Cosmos DB slouží ke správě sociálních sítí a sledovat předvolby zákazníka a data.

* Generátory doporučení

 Tento scénář se často používá v prodejní odvětví. Kombinací informace o produktech, uživatelů a interakce s uživatelem, jako je nákup, procházení nebo hodnocení položku, můžete vytvořit vlastní doporučení. Nízká latence, elastické škálování a nativní podpora grafu Azure Cosmos databáze je ideální pro modelování tyto interakce.

* Geoprostorové

 Mnoho aplikací v telecommunications, logistiky a plánování cesty muset vyhledejte umístění, které vás zajímají v rámci oblasti nebo vyhledat nejkratší nebo optimální směrování mezi dvěma umístěními. Azure Cosmos DB je přirozené přizpůsobit pro tyto problémy.

* Internet věcí

 Sítě a připojení mezi modelován jako graf zařízení IoT můžete vytvořit lepší pochopení stavu zařízení a prostředky. Také můžete zjistíte, jak změny v jedné části sítě může potenciálně ovlivnit další části.

## <a name="next-steps"></a>Další postup
Další informace o podpoře grafu v Azure Cosmos DB najdete v tématu:

* Začínáme s [Azure Cosmos DB grafu kurzu](create-graph-dotnet.md).
* Další informace o tom, jak [dotazovat grafy v Azure Cosmos DB pomocí Gremlin](gremlin-support.md).
