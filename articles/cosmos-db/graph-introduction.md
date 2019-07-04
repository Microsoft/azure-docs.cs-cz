---
title: Úvod do služby Azure Cosmos DB Gremlin API
description: Zjistěte, jak můžete Azure Cosmos DB použít k ukládání, dotazování a procházení ohromných grafů s nízkou latencí pomocí dotazovacího jazyka grafů Gremlin produktu Apache TinkerPop.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 06/25/2019
ms.author: lbosq
ms.openlocfilehash: 126c825106b7844a5fc8a5a3cdbcc7aa6c273b5b
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502795"
---
# <a name="introduction-to-azure-cosmos-db-gremlin-api"></a>Úvod do služby Azure Cosmos DB: Rozhraní Gremlin API

[Azure Cosmos DB](introduction.md) je globálně distribuovaná a vícemodelová databázová služba od Microsoftu pro klíčové aplikace. Je vícemodelová databáze a podporuje dokument, klíč hodnota, graf a úložiště se sloupcovou strukturou datových modelů. Gremlin API služby Azure Cosmos DB se používá k ukládání a pracovat s daty grafu na prostředí je plně spravovaná databáze v libovolném měřítku.  

![Architektura grafu služby Azure Cosmos DB](./media/graph-introduction/cosmosdb-graph-architecture.png)

Tento článek obsahuje přehled rozhraní Gremlin API služby Azure Cosmos DB a vysvětluje, jak ho můžete použít k ukládání ohromných grafů s miliardami vrcholů a hran. Se můžete dotazovat grafy s latencí milisekund a snadnému rozšíření struktura. Rozhraní Gremlin API služby Azure Cosmos DB je založen na [Apache TinkerPop](https://tinkerpop.apache.org) standardní databáze grafů a používá konzola Gremlin dotazovací jazyk. 

Rozhraní Gremlin API služby Azure Cosmos DB v sobě kombinuje výkon algoritmy databáze grafu s vysoce škálovatelnou a spravovanou infrastrukturu a poskytovat jedinečné, flexibilní řešení většiny běžných problémů dat spojené s chybějící flexibilitu a relační přístupů. 

## <a name="features-of-azure-cosmos-db-graph-database"></a>Funkce databáze grafu Azure Cosmos DB
 
Azure Cosmos DB je plně spravovaná databáze grafu, která nabízí globální distribuci, elastické škálování úložiště a propustnosti, automatické indexování a dotazování, nastavitelné úrovně konzistence a podporu standardu TinkerPop. 

Tady jsou rozlišené funkce, které nabízí rozhraní Gremlin API služby Azure Cosmos DB:

* **Elasticky škálovatelná propustnost a úložiště**

  Grafy v reálném světě je potřeba škálovat nad kapacitu jednoho serveru. Azure Cosmos DB podporuje grafu horizontálně škálovatelná databáze, které může mít prakticky neomezené velikosti z hlediska a zřízené propustnosti. S růstem databáze měřítko grafu, data budou automaticky distribuována pomocí [dělení grafů](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning).

* **Replikace ve více oblastech**

  Azure Cosmos DB můžete automaticky replikovat data grafu do libovolné oblasti Azure. Replikace zjednodušuje vývoj aplikací, které vyžadují globální přístup k datům. Kromě minimalizaci latence pro čtení, Azure Cosmos DB poskytuje mechanismus regionální převzetí služeb při selhání, který můžete zajistit kontinuitu vaší aplikace ve výjimečných případech přerušení služeb v oblasti. 

* **Rychlé zpracování dotazů a procházení se standardem dotazu nejčastěji používaná grafu**

  Můžete ukládat heterogenní vrcholy a hrany a dotazovat tyto dokumenty pomocí známé syntaxe Gremlin. Gremlin je nezbytné, funkční dotaz jazyk, který poskytuje také bohaté rozhraní příkazů k implementaci běžných algoritmů grafu. 
  
  Azure Cosmos DB umožňuje bohaté dotazy v reálném čase a procházení bez potřeba specifikovat parametry schématu, sekundární indexy nebo zobrazení. Další informace najdete v článku o [dotazování grafů pomocí jazyka Gremlin](gremlin-support.md).

* **Plně spravovaná grafová databáze s**

  Azure Cosmos DB eliminuje nutnost správy databáze a počítačových prostředků. Většina platforem existující databáze grafu je vázána na omezení infrastruktury a často vyžadují vysokou míru údržby k zajištění jeho provozu. 
  
  Jako plně spravovanou službu Microsoft Azure není nutné spravovat virtuální počítače, aktualizace softwaru modulu runtime, Správa horizontálního dělení nebo replikace nebo řešit komplexní datové vrstvy upgradování. Každý graf je automaticky zálohovaný a chráněný proti selháním v dané oblasti. Tyto záruky umožňují vývojářům soustředit se na poskytování hodnoty aplikace, ne na provoz a správu svých databází. 

* **Automatické indexování**

  Azure Cosmos DB standardně automaticky indexuje všechny vlastnosti v uzlech a hranách grafu a neočekává ani nevyžaduje žádné schéma nebo vytvoření sekundárních indexů. Další informace o [indexování ve službě Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview). 

* **Kompatibilita s Apache TinkerPop**

  Azure Cosmos DB podporuje [open source Apache TinkerPop standard](http://tinkerpop.apache.org/). Standardní Tinkerpop má dostatek ekosystému aplikací a knihoven, které je možné snadno integrovat s Azure Cosmos DB Gremlin API nevztahují. 

* **Přizpůsobitelných úrovní konzistence**

  Pro dosažení optimálního kompromisu mezi konzistencí a výkonem si můžete vybrat z pěti jasně definovaných úrovní konzistence. Pro dotazy a operace čtení nabízí služba Azure Cosmos DB pět různých úrovní konzistence: silná, omezená neaktuálnost, relace, konzistentní předpona a konečný výsledek. Tyto podrobné a jasně definované úrovně konzistence umožňují zvolit vhodný kompromis mezi konzistencí, dostupností a latencí. Další informace najdete v článku o [nastavitelných úrovních konzistence dat v Azure Cosmos DB](consistency-levels.md).

## <a name="scenarios-that-can-use-gremlin-api"></a>Scénáře použití rozhraní Gremlin API
Tady je několik scénářů, ve kterých je možné využít podporu grafů služby Azure Cosmos DB:

* Sociální sítě

  Zkombinováním dat o zákaznících a jejich interakcích s jinými lidmi můžete vytvořit individualizované prostředí, předvídat chování zákazníků nebo spojit osoby s lidmi, kteří mají podobné zájmy. Azure Cosmos DB lze použít ke správě sociálních sítí a sledování zákaznických preferencí a dat.

* Generátory doporučení

  Tento scénář se často používá v maloobchodě. Zkombinováním informací o produktech, uživatelích a interakcích uživatelů (jako je nákup, procházení nebo hodnocení položky) můžete vytvořit přizpůsobená doporučení. Nízká latence, elastické škálování a nativní podpora grafů služby Azure Cosmos DB se k modelování těchto interakcí ideálně hodí.

* Geoprostorové

  V mnoha aplikacích v oboru telekomunikací, logistiky a plánování cest je potřeba najít místo zájmu v určité oblasti nebo vyhledat nejkratší/optimální trasu mezi dvěma místy. Azure Cosmos DB se k řešení těchto problémů skvěle hodí.

* Internet věcí

  Pomocí sítě a propojení mezi zařízeními IoT modelovanými ve formě grafu můžete zajistit lepší porozumění stavu vašich zařízení a majetku. Zároveň zjistíte, jak změny v jedné části sítě mohou potenciálně ovlivnit jinou část.

## <a name="introduction-to-graph-databases"></a>Úvod do databáze grafů
Data, která existují v reálném světě, jsou přirozeně propojená. Modelování dat tradiční se zaměřuje na definování entit samostatně a výpočetnímu jejich vztahy v době běhu. Přestože tento model má své výhody, může být náročné vysoce propojenými data spravovat pod jeho omezení.  

Přístup databáze grafu spoléhá na zachování vztahy ve vrstvě úložiště místo, což vede k operacemi načítání vysoce účinného grafu. Rozhraní Gremlin API služby Azure Cosmos DB podporuje [vlastnost graf modelu](https://tinkerpop.apache.org/docs/current/reference/#intro).

### <a name="property-graph-objects"></a>Vlastnosti grafu objektů

Vlastnost [grafu](http://mathworld.wolfram.com/Graph.html) je struktura, která se skládá z [vrcholy](http://mathworld.wolfram.com/GraphVertex.html) a [hrany](http://mathworld.wolfram.com/GraphEdge.html). Oba objekty může mít libovolný počet párů klíč hodnota jako vlastnosti. 

* **Vrcholy** -vrcholy označení samostatné entity, jako je například osoby, místa nebo události.

* **Hrany** – znázorňují vztahy mezi vrcholy. Nějaká osoba například může znát jinou osobu, účastnit se nějaké události a nacházet se na určitém místě. 

* **Vlastnosti** vyjadřují informace o vrcholech a hranách. Může být libovolný počet vlastností v vrcholy a okraje a slouží k popisu a filtrovat objekty v dotazu. Příklad vlastnosti zahrnují vrchol, který má název a věk nebo okraj, který může mít časové razítko a/nebo tloušťku. 

Databáze grafů jsou často zahrnuté do NoSQL nebo nerelačních, kategorie databáze, protože neexistuje žádná závislost na schématu nebo omezeného datového modelu. Tato nedostatečná schéma umožňuje modelování a ukládání propojených struktur přirozeně a efektivně. 

### <a name="gremlin-by-example"></a>Ukázka jazyka Gremlin
Ukázkový graf vám pomůže pochopit, jak lze v jazyce Gremlin vyjádřit dotazy. Na následujícím obrázku je znázorněna obchodní aplikace, která spravuje data o uživatelích, zájmech a zařízeních v podobě grafu.  

![Ukázková databáze znázorňující osoby, zařízení a zájmy](./media/gremlin-support/sample-graph.png) 

Tento graf má následující typy vrcholů (v jazyce Gremlin se jim říká popisky):

- Uživatelé: Graf má tři lidi, Robin Thomas a Petr
- Zájmy: Jejich zájmů, v tomto příkladu hru fotbalového
- Zařízení: Zařízení, která používá
- Operační systémy: Operační systémy, které zařízení běží v

Vztahy mezi těmito entitami vyjádříme pomocí následujících typů okrajů/popisků:

- Ví: Například "Thomas ví Robin"
- Chtěli byste: K reprezentaci zájmy uživatelů v našem grafu, například "Ben má zájem o Football"
- RunsOS: Přenosný počítač spustí operační systém Windows
- Používá: K reprezentaci zařízení uživatel používá. Robin například používá telefon Motorola se sériovým číslem 77.

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

Graf je užitečný zejména v případě, kdy potřebujete získat odpověď na otázky, jako je Které operační systémy přátelé Thomase používají? Můžete spustit tento Gremlin procházení k získání těchto informací z grafu:

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```
Nyní se podívejme na to, co Azure Cosmos DB může nabídnout vývojářům v Gremlinu.

## <a name="next-steps"></a>Další postup
Další informace o podpoře grafů ve službě Azure Cosmos DB najdete tady:

* Začněte [kurzem věnovaným grafu služby Azure Cosmos DB](create-graph-dotnet.md).
* Naučte se, jak [dotazovat grafy ve službě Azure Cosmos DB pomocí jazyka Gremlin](gremlin-support.md).
