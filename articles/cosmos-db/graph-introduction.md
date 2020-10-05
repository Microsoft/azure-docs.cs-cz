---
title: Seznámení s Azure Cosmos DB Gremlin API
description: Zjistěte, jak můžete Azure Cosmos DB použít k ukládání, dotazování a procházení ohromných grafů s nízkou latencí pomocí dotazovacího jazyka grafů Gremlin produktu Apache TinkerPop.
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 07/10/2020
ms.author: jasonh
ms.openlocfilehash: 67a2c9537851343e8e5dad4a3654b31082e83d11
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91409608"
---
# <a name="introduction-to-gremlin-api-in-azure-cosmos-db"></a>Úvod do rozhraní Gremlin API v Azure Cosmos DB

[Azure Cosmos DB](introduction.md)   je globálně distribuovaná databázová služba pro více modelů od Microsoftu pro klíčové aplikace. Je to databáze s více modely a podporuje datové modely typu dokument, klíč-hodnota, graf a řada sloupců. "Azure Cosmos DB poskytuje databázovou službu grafu prostřednictvím rozhraní API Gremlin pro plně spravovanou databázovou službu určenou pro libovolné škálování.  

:::image type="content" source="./media/graph-introduction/cosmosdb-graph-architecture.png" alt-text="Architektura grafu služby Azure Cosmos DB" border="false":::

Tento článek obsahuje přehled rozhraní Azure Cosmos DB API Gremlin a vysvětluje, jak je používat k ukládání obrovských grafů s miliardami vrcholů a hran. Můžete zadávat dotazy na grafy s latencí milisekund a snadno vyvíjet strukturu grafu. Rozhraní Gremlin API pro Azure Cosmos DB je postavené na základě rozhraní [Apache TinkerPop](https://tinkerpop.apache.org), architektury grafu computingu. Rozhraní Gremlin API v Azure Cosmos DB používá dotazovací jazyk Gremlin.

Rozhraní Gremlin API Azure Cosmos DB kombinuje sílu algoritmů databázové databáze s vysoce škálovatelnou a spravovanou infrastrukturou, aby poskytovala jedinečné a flexibilní řešení nejběžnějších problémů s daty, která souvisí s nedostatečnou flexibilitou a relačními přístupy.

## <a name="features-of-azure-cosmos-dbs-gremlin-api"></a>Funkce rozhraní Gremlin API pro Azure Cosmos DB
 
Azure Cosmos DB je plně spravovaná databáze grafu, která nabízí globální distribuci, elastické škálování úložiště a propustnosti, automatické indexování a dotazování, nastavitelné úrovně konzistence a podporu standardu TinkerPop.

Níže jsou uvedené odlišné funkce Azure Cosmos DB Gremlin API nabízí:

* **Elasticky škálovatelná propustnost a úložiště**

  Grafy v reálném světě je potřeba škálovat nad kapacitu jednoho serveru. Azure Cosmos DB podporuje horizontálně škálovatelné databáze grafů, které mohou mít prakticky neomezenou velikost z hlediska úložiště a zajištěné propustnosti. Při zvětšování měřítka databáze grafu budou data automaticky distribuována pomocí [dělení grafů](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning).

* **Replikace mezi více oblastmi**

  Azure Cosmos DB může automaticky replikovat data grafu do jakékoli oblasti Azure po celém světě. Globální replikace zjednodušuje vývoj aplikací, které vyžadují globální přístup k datům. Kromě minimalizace latence čtení a zápisu kdekoli po celém světě Azure Cosmos DB poskytuje automatický mechanismus pro místní převzetí služeb při selhání, který umožňuje zajistit kontinuitu aplikace ve výjimečných případech přerušení služby v určité oblasti.

* **Rychlé dotazy a procházení s nejběžnějším přijatým standardem dotazů na grafy**

  Uložte heterogenní vrcholy a hrany a Dotazujte je pomocí známé syntaxe Gremlin. Gremlin je imperativně funkční dotazovací jazyk, který poskytuje bohatá rozhraní pro implementaci běžných algoritmů grafů.
  
  Azure Cosmos DB povoluje rozsáhlé dotazy v reálném čase a procházení, aniž by bylo nutné zadávat parametry schématu, sekundární indexy nebo zobrazení. Další informace najdete v článku o [dotazování grafů pomocí jazyka Gremlin](gremlin-support.md).

* **Plně spravovaná databáze grafu**

  Azure Cosmos DB eliminuje nutnost správy databáze a počítačových prostředků. Většina stávajících databázových platforem grafu je vázána na omezení infrastruktury a často vyžaduje vysokou úroveň údržby, aby bylo zajištěno její fungování. 
  
  Jako plně spravovaná služba Cosmos DB odstraňuje nutnost spravovat virtuální počítače, aktualizovat běhový software, spravovat horizontálního dělení nebo replikaci nebo řešit komplexní upgrady na datové vrstvě. Každý graf je automaticky zálohovaný a chráněný proti selháním v dané oblasti. Tyto záruky umožňují vývojářům soustředit se na poskytování hodnoty aplikace namísto provozu a správy databází v grafu. 

* **Automatické indexování**

  Ve výchozím nastavení Azure Cosmos DB automaticky indexuje všechny vlastnosti v uzlech (označované také jako vrcholy) a hrany v grafu a neočekává ani nevyžaduje žádné schéma nebo vytváření sekundárních indexů. Přečtěte si další informace o [indexování v Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview).

* **Kompatibilita s Apache TinkerPop**

  Azure Cosmos DB podporuje [Open Source Standard Apache TinkerPop](https://tinkerpop.apache.org/). Tinkerpop Standard má rozsáhlou ekosystém aplikací a knihoven, které je možné snadno integrovat s Gremlin rozhraním API Azure Cosmos DB.

* **Přizpůsobitelné úrovně konzistence**

  Azure Cosmos DB poskytuje pět jasně definovaných úrovní konzistence pro dosažení správných kompromisů mezi konzistencí a výkonem vaší aplikace. Pro dotazy a operace čtení nabízí služba Azure Cosmos DB pět různých úrovní konzistence: silná, omezená neaktuálnost, relace, konzistentní předpona a konečný výsledek. Tyto podrobné a jasně definované úrovně konzistence umožňují zvolit vhodný kompromis mezi konzistencí, dostupností a latencí. Další informace najdete v článku o [nastavitelných úrovních konzistence dat v Azure Cosmos DB](consistency-levels.md).

## <a name="scenarios-that-use-gremlin-api"></a>Scénáře, které používají rozhraní Gremlin API

Tady je několik scénářů, kde může být užitečná podpora grafu Azure Cosmos DB:

* **Sociální sítě/zákazník 365**

  Zkombinováním dat o zákaznících a jejich interakcích s jinými lidmi můžete vytvořit individualizované prostředí, předvídat chování zákazníků nebo spojit osoby s lidmi, kteří mají podobné zájmy. Azure Cosmos DB lze použít ke správě sociálních sítí a sledování zákaznických preferencí a dat.

* **Generátory doporučení**

  Tento scénář se často používá v maloobchodě. Zkombinováním informací o produktech, uživatelích a interakcích uživatelů (jako je nákup, procházení nebo hodnocení položky) můžete vytvořit přizpůsobená doporučení. Minimální latence, elastické škálování a podpora nativního grafu Azure Cosmos DB jsou ideální pro tyto scénáře.

* **Geoprostorové**

  V mnoha aplikacích v oboru telekomunikací, logistiky a plánování cest je potřeba najít místo zájmu v určité oblasti nebo vyhledat nejkratší/optimální trasu mezi dvěma místy. Azure Cosmos DB se k řešení těchto problémů skvěle hodí.

* **Internet věcí**

  Pomocí sítě a propojení mezi zařízeními IoT modelovanými ve formě grafu můžete zajistit lepší porozumění stavu vašich zařízení a majetku. Zároveň zjistíte, jak změny v jedné části sítě mohou potenciálně ovlivnit jinou část.

## <a name="introduction-to-graph-databases"></a>Seznámení s databázemi grafů

Data, která existují v reálném světě, jsou přirozeně propojená. Tradiční modelování dat se zaměřuje na definování entit samostatně a výpočet jejich vztahů za běhu. I když tento model má své výhody, může být velmi propojená data nenáročná na správu v rámci svých omezení.  

Přístup k databázi grafu spoléhá na trvalé vztahy ve vrstvě úložiště, což vede k vysoce efektivním operacím načítání grafů. Rozhraní Gremlin API Azure Cosmos DB podporuje [model grafu vlastností](https://tinkerpop.apache.org/docs/current/reference/#intro).

### <a name="property-graph-objects"></a>Objekty grafu vlastností

[Graf](http://mathworld.wolfram.com/Graph.html) vlastností je struktura, která se skládá z [vrcholů](http://mathworld.wolfram.com/GraphVertex.html) a [hran](http://mathworld.wolfram.com/GraphEdge.html). Oba objekty mohou mít libovolný počet párů klíč-hodnota jako vlastnosti. 

* **Vrcholy/uzly** – vrcholy označují diskrétní entity, jako je osoba, místo nebo událost.

* **Hrany/vztahy** – okraje označují vztahy mezi vrcholy. Nějaká osoba například může znát jinou osobu, účastnit se nějaké události a nacházet se na určitém místě.

* **Vlastnosti** vyjadřují informace o vrcholech a hranách. V obou vrcholech nebo v okrajích může být libovolný počet vlastností, které lze použít k popisu a filtrování objektů v dotazu. Příkladem vlastností je vrchol, který má název a stáří nebo okraj, který může mít časové razítko nebo váhu.

* **Label** – popisek je název nebo identifikátor vrcholu nebo okraje. Popisky mohou seskupovat více vrcholů nebo hran tak, aby všechny vrcholy a hrany ve skupině měly určitý popisek. Například graf může mít více vrcholů typu Label "Person".

Databáze grafů jsou často zahrnuty v kategorii NoSQL nebo nerelační databáze, protože neexistuje žádná závislost na schématu nebo omezeném datovém modelu. Toto nedostatečné schéma umožňuje modelování a ukládání propojených struktur přirozeně a efektivně.

### <a name="graph-database-by-example"></a>Databáze grafu podle příkladu

Ukázkový graf vám pomůže pochopit, jak lze v jazyce Gremlin vyjádřit dotazy. Na následujícím obrázku je znázorněna obchodní aplikace, která spravuje data o uživatelích, zájmech a zařízeních v podobě grafu.  

:::image type="content" source="./media/gremlin-support/sample-graph.png" alt-text="Architektura grafu služby Azure Cosmos DB" border="false"::: 

Tento graf má následující typy *vrcholů* (v Gremlin se také nazývají "jmenovka"):

* **Lidé**: graf má tři lidi, Robin, Tomáš a Robert.
* **Zájmy**: jejich zájmy v tomto příkladu se hrou fotbalu
* **Zařízení**: zařízení, která uživatelé používají
* **Operační systémy**: operační systémy, ve kterých se zařízení spouštějí
* **Místo**: místa, ze kterých se mají zařízení přistupovat

Vztahy mezi těmito entitami zastupujeme prostřednictvím následujících typů *Edge* :

* **Ví**: například "Tomáš ví s dotazem"
* **Zúčastněte**se: k vyjádření zájmů lidí v našem grafu, například "Robert se zajímá o fotbal."
* **RunsOS**: laptop spouští operační systém Windows.
* **Používá**: k vyjádření zařízení, které osoba používá. Robin například používá telefon Motorola se sériovým číslem 77.
* **Umístění: představuje**umístění, ze kterého mají být zařízení k dispozici.

Konzola Gremlin je interaktivní terminál nabízený Apache TinkerPop a tento terminál slouží k interakci s daty grafu. Další informace najdete v dokumentu rychlý Start, [Jak používat konzolu Gremlin](create-graph-gremlin-console.md). K jejich provedení můžete také použít ovladače Gremlin na platformě dle vlastního výběru (Java, Node.js, Python nebo .NET). Následující příklady ukazují, jak spustit dotazy na data grafu pomocí konzoly Gremlin.

Nejprve se podívejme na CRUD. Následující příkaz Gremlinu vloží do grafu vrchol Thomas:

```java
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

Tento příkaz Gremlinu dále vloží do grafu mezi Thomase a Robina okraj knows (zná).

```java
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

Následující dotaz vrátí vrcholy person (osoba) seřazené podle jmen osob v sestupném pořadí:

```java
:> g.V().hasLabel('person').order().by('firstName', decr)
```

Graf je užitečný zejména v případě, kdy potřebujete získat odpověď na otázky, jako je Které operační systémy přátelé Thomase používají? Tento Gremlin průchod můžete spustit pro získání těchto informací z grafu:

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```

## <a name="next-steps"></a>Další kroky

Další informace o podpoře grafů ve službě Azure Cosmos DB najdete zde:

* Začněte [kurzem věnovaným grafu služby Azure Cosmos DB](create-graph-dotnet.md).
* Naučte se, jak [dotazovat grafy ve službě Azure Cosmos DB pomocí jazyka Gremlin](gremlin-support.md).
