---
title: Podpora Azure Table Storage ve službě Azure Cosmos DB
description: Zjistěte, jak rozhraní Table API služby Azure Cosmos DB spolupracuje s tabulkami úložišť v Azure.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 11/15/2017
author: wmengmsft
ms.author: wmeng
ms.reviewer: sngun
ms.openlocfilehash: 31072be9ae73791e6458b0aee4490ac90df44d94
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957959"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Vývoj pomocí rozhraní Table API služby Azure Cosmos DB a úložištěm tabulek Azure

Rozhraní Table API služby Azure Cosmos DB a úložiště tabulek Azure sdílejí stejný tabulkový datový model a zpřístupňují přes svoje sady SDK stejné vytvářecí, odstraňovací, aktualizační a dotazovací operace. 

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Vývoj pomocí rozhraní Table API služby Azure Cosmos DB

[Rozhraní Table API služby Azure Cosmos DB](table-introduction.md) má v současnosti pro vývoj dostupné čtyři sady SDK: 

* [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): sadu .NET SDK. Tato knihovna cílí na .NET Standard a má stejné podpisy tříd a metod jako veřejná [Windows Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage), ale má také možnost připojení k účtům Azure Cosmos DB pomocí rozhraní Table API. Alternativně je k dispozici jako předchozí verze této sady SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) která funguje pouze na rozhraní .NET Framework.

* [Python SDK](table-sdk-python.md): Nová sada Azure Cosmos DB Python SDK je jediná sada SDK, která podporuje úložiště tabulek Azure v jazyce Python. Tato sada SDK umožňuje připojení k úložišti tabulek Azure i k rozhraní Table API služby Azure Cosmos DB.

* [Java SDK](table-sdk-java.md): Tato sada Azure Storage SDK umožňuje připojení k účtům služby Azure Cosmos DB pomocí rozhraní Table API.

* [Node.js SDK](table-sdk-nodejs.md): Tato sada Azure Storage SDK umožňuje připojení k účtům služby Azure Cosmos DB pomocí rozhraní Table API.


Další informace o práci s rozhraním API tabulky je k dispozici v [– nejčastější dotazy: Vývoj s využitím rozhraní Table API](faq.md#table) článku.

## <a name="developing-with-azure-table-storage"></a>Vývoj pomocí úložiště tabulek Azure

Úložiště tabulek Azure má pro vývoj dostupné tyto sady SDK:

- [WindowsAzure.Storage .NET SDK](https://www.nuget.org/packages/WindowsAzure.Storage/). Tato knihovna umožňuje pracovat se službou tabulky úložiště.
- [Python SDK](table-sdk-python.md). Sada Azure Cosmos DB Table SDK pro Python také podporuje službu tabulky úložiště.
- [Azure Storage SDK pro jazyk Java](https://github.com/azure/azure-storage-java). Tato sada Azure Storage SDK poskytuje klientskou knihovnu v Javě pro využití úložiště tabulek Azure.
- [Node.js SDK](table-sdk-nodejs.md). Tato sada SDK nabízí balíček Node.js a javascriptovou klientskou knihovnu kompatibilní s prohlížečem pro využití služby tabulky úložiště.
- [Modul PowerShellu AzureRmStorageTable](https://www.powershellgallery.com/packages/AzureRmStorageTable/1.0.0.7). Tento modul PowerShellu obsahuje rutiny pro práci s tabulkami úložiště.
- [Klientská knihovna Azure Storage pro jazyk C++](https://github.com/Azure/azure-storage-cpp/). Tato knihovna umožňuje vývoj aplikací pro Azure Storage.
- [Klientská knihovna tabulky úložiště Azure pro jazyk Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Tento projekt poskytuje balíček Ruby, který usnadňuje přístup ke službám tabulky úložiště Azure.
- [Klientská knihovna PHP tabulky úložiště Azure](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Tento projekt poskytuje klientskou knihovnu PHP, která usnadňuje přístup ke službám tabulky úložiště Azure.


   





