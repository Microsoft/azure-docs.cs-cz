---
title: Podpora Azure Table Storage v Azure Cosmos DB
description: Přečtěte si, jak Azure Cosmos DB tabulek rozhraní API pro tabulky a Azure Storage vzájemně spolupracují, sdílením stejného datového modelu tabulky a operací.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 05/21/2020
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: 28c58251d9a30b3bae9d958c32c4d6a71f86aaae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85263207"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Vývoj pomocí rozhraní Table API služby Azure Cosmos DB a úložištěm tabulek Azure

Rozhraní Table API služby Azure Cosmos DB a úložiště tabulek Azure sdílejí stejný tabulkový datový model a zpřístupňují přes svoje sady SDK stejné vytvářecí, odstraňovací, aktualizační a dotazovací operace.

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Vývoj pomocí rozhraní Table API služby Azure Cosmos DB

[Rozhraní Table API služby Azure Cosmos DB](table-introduction.md) má v současnosti pro vývoj dostupné čtyři sady SDK: 

* [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): .NET SDK. Tato knihovna cílí na .NET Standard a má stejné signatury tříd a metod jako veřejná [Sada Windows Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage), ale také umožňuje připojení k Azure Cosmos DB účtům pomocí rozhraní API pro tabulky. Uživatelé knihovny .NET Framework [Microsoft. Azure. CosmosDB. Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) se doporučují upgradovat na [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) , protože jsou v režimu údržby a budou brzy zastaralí.

* [Python SDK](table-sdk-python.md): nová sada SDK Azure Cosmos DB Pythonu je jediná sada SDK, která podporuje úložiště Azure Table v Pythonu. Tato sada SDK umožňuje připojení k úložišti tabulek Azure i k rozhraní Table API služby Azure Cosmos DB.

* [Java SDK](table-sdk-java.md): tato sada Azure Storage SDK umožňuje připojení k účtům Azure Cosmos DB pomocí rozhraní API pro tabulky.

* [Node.js SDK](table-sdk-nodejs.md): tato sada Azure Storage SDK umožňuje připojení k Azure Cosmos DB účtům pomocí rozhraní API pro tabulky.


Další informace o práci s rozhraním Table API jsou dostupné v článku s [častými dotazy o vývoji pomocí rozhraní Table API](table-api-faq.md).

## <a name="developing-with-azure-table-storage"></a>Vývoj pomocí úložiště tabulek Azure

Úložiště tabulek Azure má pro vývoj dostupné tyto sady SDK:

- [Microsoft. Azure. Storage. blob](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/), [Microsoft. Azure. Storage. File](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/), [Microsoft. Azure. Storage. Queue](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)a [Microsoft. Azure. Storage. Common](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) Library vám umožní pracovat se službou Azure Table Storage. Pokud používáte rozhraní API pro tabulky v Azure Cosmos DB, můžete místo toho použít knihovnu [Microsoft. Azure. CosmosDB. Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) .
- [Python SDK](https://github.com/Azure/azure-cosmos-table-python). Sada SDK Azure Cosmos DB SDK pro Python podporuje službu Table Storage (protože rozhraní API pro tabulky Azure Table Storage a Cosmos DB mají stejné funkce, které se dají využít, a s úsilím factorize naší vývojové prostředí SDK doporučujeme použít tuto sadu SDK).
- [Sada Azure Storage SDK pro jazyk Java](https://github.com/azure/azure-storage-java). Tato sada Azure Storage SDK poskytuje klientskou knihovnu v Javě pro využití úložiště tabulek Azure.
- [Node.js SDK](https://github.com/Azure/azure-storage-node). Tato sada SDK nabízí balíček Node.js a javascriptovou klientskou knihovnu kompatibilní s prohlížečem pro využití služby tabulky úložiště.
- [Modul PowerShellu AzureRmStorageTable](https://www.powershellgallery.com/packages/AzureRmStorageTable). Tento modul PowerShellu obsahuje rutiny pro práci s tabulkami úložiště.
- [Klientská knihovna Azure Storage pro jazyk C++](https://github.com/Azure/azure-storage-cpp/). Tato knihovna umožňuje vývoj aplikací pro Azure Storage.
- [Klientská knihovna tabulky úložiště Azure pro jazyk Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Tento projekt poskytuje balíček Ruby, který usnadňuje přístup ke službám tabulky úložiště Azure.
- [Klientská knihovna PHP tabulky úložiště Azure](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Tento projekt poskytuje klientskou knihovnu PHP, která usnadňuje přístup ke službám tabulky úložiště Azure.


   





