---
title: Stránkování v Azure Cosmos DB
description: Další informace o konceptech stránkování a tokenech pro pokračování
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.openlocfilehash: 5faff410fa18c5161d93f739f77eeb9c85d581a8
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430951"
---
# <a name="pagination-in-azure-cosmos-db"></a>Stránkování v Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

V Azure Cosmos DB mohou dotazy obsahovat více stránek výsledků. Tento dokument vysvětluje kritéria, která Azure Cosmos DB dotazovací modul používá k rozhodnutí, zda se mají rozdělit výsledky dotazu na více stránek. Volitelně můžete použít tokeny pro pokračování ke správě výsledků dotazu, které jsou rozloženy na více stránkách.

## <a name="understanding-query-executions"></a>Porozumění provádění dotazů

Někdy budou výsledky dotazu rozděleny na více stránek. Výsledky každé stránky jsou generovány pomocí samostatného spuštění dotazu. Pokud výsledky dotazu nelze vrátit v jednom jednom spuštění, Azure Cosmos DB budou automaticky rozděleny výsledky na více stránek.

Můžete zadat maximální počet položek vrácených dotazem nastavením `MaxItemCount` . `MaxItemCount`Je určen pro každý požadavek a oznamuje dotazovacímu stroji, aby vrátil tento počet položek nebo méně. Můžete nastavit `MaxItemCount` na, `-1` Pokud nechcete omezit počet výsledků na provedení dotazu.

Kromě toho existují další důvody, proč může dotazovací stroj potřebovat rozdělit výsledky dotazu na více stránek. Tady jsou některé z nich:

- Kontejner byl omezen a nebylo k dispozici ru k vrácení více výsledků dotazu.
- Odpověď na provedení dotazu byla příliš velká.
- Čas provedení dotazu byl příliš dlouhý.
- U dotazovacího stroje bylo efektivnější vracet výsledky dalších spuštění.

Počet položek vrácených na provedení dotazu bude vždy menší nebo roven `MaxItemCount` . Je však možné, že ostatní kritéria omezí počet výsledků, které může dotaz vracet. Pokud spustíte stejný dotaz několikrát, počet stránek nemusí být konstantní. Například pokud je dotaz omezený, může být na stránce méně dostupných výsledků, což znamená, že dotaz bude mít další stránky. V některých případech je také možné, že váš dotaz může vrátit prázdnou stránku výsledků.

## <a name="handling-multiple-pages-of-results"></a>Zpracování více stránek výsledků

Chcete-li zajistit přesné výsledky dotazu, měli byste postupovat na všech stránkách. Dokud nebudou žádné další stránky, měli byste pokračovat ve spouštění dotazů.

Tady je několik příkladů zpracování výsledků z dotazů s více stránkami:

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L280)
- [Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176)
- [Node.js SDK](https://github.com/Azure/azure-sdk-for-js/blob/83fcc44a23ad771128d6e0f49043656b3d1df990/sdk/cosmosdb/cosmos/samples/IndexManagement.ts#L128-L140)
- [Python SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/examples.py#L89)

## <a name="continuation-tokens"></a>Tokeny pro pokračování

V sadě .NET SDK a Java SDK můžete volitelně použít tokeny pro pokračování jako záložku pro průběh dotazu. Azure Cosmos DB provádění dotazů je bezstavové na straně serveru a lze je kdykoli obnovit pomocí tokenu pro pokračování. Tokeny pro pokračování nejsou v sadě Node.js SDK podporovány. V sadě Python SDK je podporováno pro dotazy s jedním oddílem a v objektu Options je nutné zadat PK, protože není dostačující pro vlastní dotaz.

Tady je příklad použití tokenů pro pokračování:

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L699-L734)
- [Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216)
- [Python SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/test/test_query.py#L533)

Pokud dotaz vrátí token pro pokračování, existují další výsledky dotazu.

V REST API Azure Cosmos DB můžete spravovat tokeny pokračování s `x-ms-continuation` hlavičkou. Pokud hlavička odpovědi není prázdná, stejně jako při dotazování na sadu .NET nebo Java SDK `x-ms-continuation` znamená, že dotaz má další výsledky.

Pokud používáte stejnou verzi sady SDK, tokeny pro pokračování nebudou nikdy vypršet. Volitelně můžete [omezit velikost tokenu pro pokračování](/dotnet/api/microsoft.azure.documents.client.feedoptions.responsecontinuationtokenlimitinkb#Microsoft_Azure_Documents_Client_FeedOptions_ResponseContinuationTokenLimitInKb). Bez ohledu na množství dat nebo počet fyzických oddílů ve vašem kontejneru vrátí dotazy jeden token pro pokračování.

Tokeny pro pokračování nelze použít pro dotazy s [Group by](sql-query-group-by.md) nebo [DISTINCT](sql-query-keywords.md#distinct) , protože tyto dotazy by vyžadovaly uložení významného stavu. Pro dotazy s `DISTINCT` můžete použít tokeny pro pokračování, pokud přidáte `ORDER BY` do dotazu.

Tady je příklad dotazu `DISTINCT` , který může používat token pro pokračování:

```sql
SELECT DISTINCT VALUE c.name
FROM c
ORDER BY c.name
```

## <a name="next-steps"></a>Další kroky

- [Úvod do služby Azure Cosmos DB](introduction.md)
- [Ukázky Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Klauzule ORDER BY](sql-query-order-by.md)
