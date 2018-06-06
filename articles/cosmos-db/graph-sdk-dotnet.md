---
title: .NET SDK služby Azure CosmosDB rozhraní Graph API & prostředky | Microsoft Docs
description: Další informace o Azure CosmosDB Graph API, včetně data vydání, vyřazení dat a změny provedené mezi každou verzi.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: dotnet
ms.topic: reference
ms.date: 10/17/2017
ms.author: lbosq
ms.openlocfilehash: c5975809b0f196fe88a11504549b046e910fc93c
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34795920"
---
# <a name="azure-cosmos-db-graph-net-api-download-and-release-notes"></a>Azure Cosmos DB grafu .NET API: Stažení a poznámky k verzi

|   |   |
|---|---|
|**Stažení sady SDK**|[NuGet](https://aka.ms/acdbgraphnuget)|
|**Dokumentaci k rozhraní API**|[Referenční dokumentace rozhraní API .NET](https://aka.ms/acdbgraphapiref)|
|**Rychlý start**|[Azure Cosmos DB: Vytvoření grafu aplikace pomocí rozhraní .NET a rozhraní Graph API](create-graph-dotnet.md)|
|**Kurz**|[Azure CosmosDB: Vytvořte kontejner s rozhraní Graph API](tutorial-develop-graph-dotnet.md)|
|**Aktuální podporovaných prostředí**| [Rozhraní Microsoft .NET Framework 4.6.1](https://www.microsoft.com/en-us/download/details.aspx?id=49981)</br> [Rozhraní Microsoft .NET Core](https://www.microsoft.com/net/download/core) |


## <a name="release-notes"></a>Poznámky k verzi

Doporučujeme používat ovladač Gremlin open source pro platformu .NET, Gremlin.Net. Zjistěte, jak začít pracovat [Cosmos databázi Azure: vytvoření grafu aplikace pomocí rozhraní .NET a rozhraní Graph API](create-graph-dotnet.md).

### <a name="a-name031-preview031-preview"></a><a name="0.3.1-preview"/>0.3.1-Preview

#### <a name="bug-fixes"></a>Opravy chyb
* Opravte volitelně načíst `appsettings.json` (`netstandard1.6`)

#### <a name="whats-new"></a>Co je nového
* Přepněte Microsoft.Azure.Graphs Cílová platforma AnyCPU.
* Odebrat Mono sestavení z `net461` manifest balíčku.

### <a name="a-name030-preview030-preview"></a><a name="0.3.0-preview"/>0.3.0-Preview

#### <a name="whats-new"></a>Co je nového
* Přidaná podpora pro `.netstandard 1.6`
  * Vyžaduje `Microsoft.Azure.DocumentDB.Core >= 1.5.1`
* Přidat novou `gremlin-groovy` analyzátor, který má nahradit existující analyzátor. Tento analyzátor podporuje podmnožinu na Tinkerpop `gremlin-groovy` syntaxe a zahrnuje:
  * Vylepšené analýzy výkonu pomocí 2 x.
  * Vyřešit řadu problémů souvisejících s znak uvozovací znaky řetězce, nesprávnému zpracování literálových hodnot a jiné nesrovnalosti v původním analyzátor.
* Přidání optimalizace pro traversals s predikáty okraj.
  *  Procházení směrování s filtry by se měla zobrazit tomuto vylepšení, například: `g.V('1').outE().has('name', 'marko').inV()`.
* Přidat optimalizace pro traversals s `limit()` krok.

#### <a name="breaking-changes"></a>Zásadní změny
* Odebrané podpory pro rozhraní .NET Framework 4.5.1

* Nový analyzátor zarovnaná s `gremlin-groovy` gramatika. V důsledku toho jsou některé výrazy, které dříve fungovaly nejednoznačný pro nový analyzátor. Jeden případ Poznámka:
  * `in` a `as` jsou vyhrazená slova v `gremlin-groovy`, takže tyto kroky musí být kvalifikovaný pomocí `.in()` nebo `.as()` předejdete chyby syntaxe. Příklad: `g.V().repeat(in()).times(2)`  ->  _vyvolá chybu syntaxe_  
 `g.V().repeat(__.in()).times(2)` -> _úspěšné_

### <a name="a-name024-preview024-preview"></a><a name="0.2.4-preview"/>0.2.4-preview

### <a name="a-name022-preview022-preview"></a><a name="0.2.2-preview"/>0.2.2-preview

### <a name="a-name021-preview021-preview"></a><a name="0.2.1-preview"/>0.2.1-preview

### <a name="a-name020-preview020-preview"></a><a name="0.2.0-preview"/>0.2.0-preview

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-Preview
* Verze preview počáteční.

## <a name="release--retirement-dates"></a>Verze & vyřazení kalendářních dat
Microsoft bude poskytovat oznámení alespoň **dobu 12 měsíců** předem vyřazení sady SDK k funkce smooth přechodu na novější nebo podporované verzi.

Nové funkce a funkce a optimalizace, jsou přidány pouze v aktuální sadě SDK, jako takový se doporučuje, aby vždy upgradu na nejnovější verze sady SDK v míře. 

Každá žádost o Azure DB Cosmos pomocí vyřazeno sady SDK budou odmítnuty službou.

<br/>

| Verze | Datum vydání | Datum vyřazení |
| --- | --- | --- |
| [0.3.1-preview](#0.3.1-preview) |17 říjen 2017 |--- |
| [0.3.0-preview](#0.3.0-preview) |2. října 2017 |--- |
| [0.2.4-preview](#0.2.4-preview) |4 srpen 2017 |--- |
| [0.2.2-preview](#0.2.2-preview) |23 června 2017 |--- |
| [0.2.1-preview](#0.2.2-preview) |8 června 2017 |--- |
| [0.2.0-preview](#0.2.2-preview) |10. května 2017 |--- |
| [0.1.0-preview](#0.1.0-preview) |8 může 2017 |--- |

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o rozhraní API služby Azure DB Cosmos grafu naleznete v tématu [Úvod do Azure Cosmos DB: rozhraní Graph API](graph-introduction.md). 
