---
title: Úvod do rozhraní API Azure Cosmos DB Gremlin
description: Zjistěte, jak můžete Azure Cosmos DB použít k ukládání, dotazování a procházení ohromných grafů s nízkou latencí pomocí dotazovacího jazyka grafů Gremlin produktu Apache TinkerPop.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 07/18/2019
ms.author: lbosq
ms.openlocfilehash: 9151b54d7fa0b64a465aa8384cb4bfdb8e72c482
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75500000"
---
# <a name="introduction-to-azure-cosmos-db-gremlin-api"></a>Úvod do služby Azure Cosmos DB: rozhraní Gremlin API

[Azure Cosmos DB](introduction.md) je globálně distribuovaná databázová služba s více modely od Microsoftu pro klíčové aplikace. Jedná se o databázi s více modely a podporuje datové modely dokumentu, hodnoty klíče, grafu a rodiny sloupců. Rozhraní API Azure Cosmos DB Gremlin se používá k ukládání a provozu s daty grafu v plně spravované databázové službě navržené pro libovolné škálování.  

![Architektura grafu služby Azure Cosmos DB](./media/graph-introduction/cosmosdb-graph-architecture.png)

Tento článek obsahuje přehled rozhraní Gremlin API služby Azure Cosmos DB a vysvětluje, jak ho můžete použít k ukládání ohromných grafů s miliardami vrcholů a hran. Grafy můžete zadat pomocí milisekundové latence a snadno vyvíjet strukturu grafu. Rozhraní Gremlin rozhraní Api služby Azure Cosmos DB je založené na standardu databáze [grafů Apache TinkerPop](https://tinkerpop.apache.org) a používá dotazovací jazyk Gremlin. 

Rozhraní Gremlin API služby Azure Cosmos DB kombinuje výkon algoritmů databáze grafů s vysoce škálovatelnou spravovanou infrastrukturou a poskytuje jedinečné a flexibilní řešení nejběžnějších problémů s daty spojených s nedostatečnou flexibilitou a relačními přístupy. 

## <a name="features-of-azure-cosmos-db-graph-database"></a>Funkce databáze grafu Azure Cosmos DB
 
Azure Cosmos DB je plně spravovaná databáze grafu, která nabízí globální distribuci, elastické škálování úložiště a propustnosti, automatické indexování a dotazování, nastavitelné úrovně konzistence a podporu standardu TinkerPop. 

Níže jsou uvedeny diferencované funkce, které azure cosmos DB Gremlin ROZHRANÍ API nabízí:

* **Elasticky škálovatelná propustnost a úložiště**

  Grafy v reálném světě je potřeba škálovat nad kapacitu jednoho serveru. Azure Cosmos DB podporuje horizontálně škálovatelné databáze grafů, které mohou mít prakticky neomezenou velikost z hlediska úložiště a zřízené propustnosti. S růstem měřítka databáze grafu budou data automaticky distribuována pomocí [dělení grafů](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning).

* **Replikace mezi více oblastmi**

  Azure Cosmos DB můžete automaticky replikovat data grafu do libovolné oblasti Azure po celém světě. Globální replikace zjednodušuje vývoj aplikací, které vyžadují globální přístup k datům. Kromě minimalizace latence čtení a zápisu kdekoli na světě poskytuje Azure Cosmos DB automatický regionální mechanismus převzetí služeb při selhání, který může zajistit kontinuitu vaší aplikace ve výjimečných případech přerušení služby v oblasti. 

* **Rychlé dotazy a průchody s nejpoužívanějším standardem grafu**

  Ukládejte heterogenní vrcholy a hrany a dotazujte se na ně pomocí známé syntaxe Gremlin. Gremlin je imperativní, funkční dotazovací jazyk, který poskytuje bohaté rozhraní pro implementaci běžných algoritmů grafu. 
  
  Azure Cosmos DB umožňuje bohaté dotazy v reálném čase a průchody bez nutnosti zadávat rady při psaní schématu, sekundární indexy nebo zobrazení. Další informace najdete v článku o [dotazování grafů pomocí jazyka Gremlin](gremlin-support.md).

* **Plně spravovaná databáze grafů**

  Azure Cosmos DB eliminuje nutnost správy databáze a počítačových prostředků. Většina existujících platforem databáze grafů je vázána na omezení své infrastruktury a často vyžaduje vysoký stupeň údržby, aby byl zajištěn její provoz. 
  
  Jako plně spravovaná služba Cosmos DB odstraňuje potřebu spravovat virtuální počítače, aktualizovat software runtime, spravovat horizontál nebo replikaci nebo se zabývat komplexními upgrady datové vrstvy. Každý graf je automaticky zálohovaný a chráněný proti selháním v dané oblasti. Tyto záruky umožňují vývojářům zaměřit se na poskytování hodnoty aplikace namísto provozu a správy jejich grafových databází. 

* **Automatické indexování**

  Azure Cosmos DB standardně automaticky indexuje všechny vlastnosti v uzlech a hranách grafu a neočekává ani nevyžaduje žádné schéma nebo vytvoření sekundárních indexů. Další informace o [indexování v Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview). 

* **Kompatibilita s Apache TinkerPop**

  Azure Cosmos DB podporuje [open source standard Apache TinkerPop](https://tinkerpop.apache.org/). Standard Tinkerpop má rozsáhlý ekosystém aplikací a knihoven, které lze snadno integrovat s rozhraním Gremlin API služby Azure Cosmos DB. 

* **Laditelné úrovně konzistence**

  Azure Cosmos DB poskytuje pět dobře definované úrovně konzistence k dosažení správné kompromis mezi konzistence a výkonu pro vaši aplikaci. Pro dotazy a operace čtení nabízí služba Azure Cosmos DB pět různých úrovní konzistence: silná, omezená neaktuálnost, relace, konzistentní předpona a konečný výsledek. Tyto podrobné a jasně definované úrovně konzistence umožňují zvolit vhodný kompromis mezi konzistencí, dostupností a latencí. Další informace najdete v článku o [nastavitelných úrovních konzistence dat v Azure Cosmos DB](consistency-levels.md).

## <a name="scenarios-that-can-use-gremlin-api"></a>Scénáře použití rozhraní Gremlin API
Tady jsou některé scénáře, kde může být užitečná podpora grafů Azure Cosmos DB:

* **Sociální sítě/Zákazník 365**

  Zkombinováním dat o zákaznících a jejich interakcích s jinými lidmi můžete vytvořit individualizované prostředí, předvídat chování zákazníků nebo spojit osoby s lidmi, kteří mají podobné zájmy. Azure Cosmos DB lze použít ke správě sociálních sítí a sledování zákaznických preferencí a dat.

* **Generátory doporučení**

  Tento scénář se často používá v maloobchodě. Zkombinováním informací o produktech, uživatelích a interakcích uživatelů (jako je nákup, procházení nebo hodnocení položky) můžete vytvořit přizpůsobená doporučení. Pro tyto scénáře je ideální podpora Azure Cosmos DB s nízkou latencí, elastické škálování a nativní grafy.

* **Geoprostorové**

  V mnoha aplikacích v oboru telekomunikací, logistiky a plánování cest je potřeba najít místo zájmu v určité oblasti nebo vyhledat nejkratší/optimální trasu mezi dvěma místy. Azure Cosmos DB se k řešení těchto problémů skvěle hodí.

* **Internet věcí**

  Pomocí sítě a propojení mezi zařízeními IoT modelovanými ve formě grafu můžete zajistit lepší porozumění stavu vašich zařízení a majetku. Zároveň zjistíte, jak změny v jedné části sítě mohou potenciálně ovlivnit jinou část.

## <a name="introduction-to-graph-databases"></a>Úvod do grafových databází
Data, která existují v reálném světě, jsou přirozeně propojená. Tradiční modelování dat se zaměřuje na samostatné definování entit a výpočet jejich vztahů za běhu. Zatímco tento model má své výhody, vysoce propojená data mohou být náročná na správu pod jeho omezeními.  

Přístup databáze grafů závisí na trvalých relacích ve vrstvě úložiště, což vede k vysoce efektivním operacím načítání grafů. Rozhraní Gremlin rozhraní Api služby Azure Cosmos DB podporuje [model grafu vlastností](https://tinkerpop.apache.org/docs/current/reference/#intro).

### <a name="property-graph-objects"></a>Objekty grafu vlastností

[Graf](http://mathworld.wolfram.com/Graph.html) vlastností je struktura, která se skládá z [vrcholů](http://mathworld.wolfram.com/GraphVertex.html) a [hran](http://mathworld.wolfram.com/GraphEdge.html). Oba objekty mohou mít libovolný počet párů klíč hodnota jako vlastnosti. 

* **Vrcholy** – vrcholy označují diskrétní entity, například osobu, místo nebo událost.

* **Hrany** – znázorňují vztahy mezi vrcholy. Nějaká osoba například může znát jinou osobu, účastnit se nějaké události a nacházet se na určitém místě. 

* **Vlastnosti** vyjadřují informace o vrcholech a hranách. V vrcholech nebo hranách může být libovolný počet vlastností a lze je použít k popisu a filtrování objektů v dotazu. Mezi příkladové vlastnosti patří vrchol, který má název a stáří, nebo hranu, která může mít časové razítko nebo tloušťku. 

Databáze grafů jsou často zahrnuty do kategorie NoSQL nebo non-relační databáze, protože neexistuje žádná závislost na schématu nebo omezeném datovém modelu. Tento nedostatek schématu umožňuje modelování a ukládání propojených struktur přirozeně a efektivně. 

### <a name="gremlin-by-example"></a>Ukázka jazyka Gremlin
Ukázkový graf vám pomůže pochopit, jak lze v jazyce Gremlin vyjádřit dotazy. Na následujícím obrázku je znázorněna obchodní aplikace, která spravuje data o uživatelích, zájmech a zařízeních v podobě grafu.  

![Ukázková databáze znázorňující osoby, zařízení a zájmy](./media/gremlin-support/sample-graph.png) 

Tento graf má následující typy *vrcholů* (nazývanou "label" v Gremlinu):

- **Lidé**: Graf má tři lidi, Robin, Thomas a Ben
- **Zájmy**: Jejich zájmy, v tomto příkladu, hra fotbalu
- **Zařízení**: Zařízení, která lidé používají
- **Operační systémy**: Operační systémy, na kterých zařízení běží

Vztahy mezi těmito entitami představujeme prostřednictvím následujících typů/popisků *hran:*

- **Ví:** Například, "Thomas zná Robin"
- **Zájem**: Chcete-li reprezentovat zájmy lidí v našem grafu, například, "Ben má zájem o fotbal"
- **RunsOS**: Notebook běží operační systém Windows
- **Použití**: Chcete-li reprezentovat, které zařízení osoba používá. Robin například používá telefon Motorola se sériovým číslem 77.

Nyní pomocí [konzoly Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#gremlin-console) spustíme u tohoto grafu několik operací. K jejich provedení můžete také použít ovladače Gremlin na platformě dle vlastního výběru (Java, Node.js, Python nebo .NET).  Než se podíváme na to, co Azure Cosmos DB podporuje, projděme si několik příkladů, abychom se blíže seznámili se syntaxí.

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

Graf je užitečný zejména v případě, kdy potřebujete získat odpověď na otázky, jako je Které operační systémy přátelé Thomase používají? Můžete spustit tento Gremlin traversal získat tyto informace z grafu:

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```

## <a name="next-steps"></a>Další kroky
Další informace o podpoře grafů ve službě Azure Cosmos DB najdete zde:

* Začněte [kurzem věnovaným grafu služby Azure Cosmos DB](create-graph-dotnet.md).
* Naučte se, jak [dotazovat grafy ve službě Azure Cosmos DB pomocí jazyka Gremlin](gremlin-support.md).
