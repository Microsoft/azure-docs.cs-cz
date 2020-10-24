---
title: Přehled služby Azure Table Storage
description: Naučte se používat úložiště tabulek Azure k ukládání flexibilních datových sad, jako jsou uživatelská data pro webové aplikace, adresáře, informace o zařízeních nebo jiné typy metadat.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: overview
ms.date: 05/20/2019
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: 7727dd2ae69fab75219c5d6d733db3f150061df3
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92477228"
---
# <a name="azure-table-storage-overview"></a>Přehled služby Azure Table Storage

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Azure Table Storage je služba, která ukládá strukturovaná data NoSQL do cloudu a poskytuje úložiště klíčů/atributů s návrhem bez použití schématu. Vzhledem k tomu, že je Table Storage bez schématu, je snadné data přizpůsobovat měnícím se potřebám vaší aplikace. Přístup k datům Table Storage je pro mnoho typů aplikací rychlý a nákladově efektivní a pro podobné objemy dat obvykle znamená nižší náklady než tradiční SQL.

Table Storage můžete používat k ukládání flexibilních datových sad, například uživatelských dat pro webové aplikace, adresářů, informací o zařízení nebo dalších typů metadat, které vaše služba vyžaduje. V tabulce můžete uložit libovolný počet entit a účet úložiště může obsahovat libovolný počet tabulek, až do limitu kapacity účtu úložiště.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>Další kroky

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.

* [Začínáme s rozhraním Azure Cosmos DB API pro tabulky a Azure Table Storage s využitím .NET SDK](./tutorial-develop-table-dotnet.md)

* Projděte si referenční dokumentaci ke službě Table service, kde najdete úplné podrobnosti o dostupných rozhraních API:

    * [Klientská knihovna pro úložiště – referenční informace pro .NET](/dotnet/api/overview/azure/storage)

    * [REST API – referenční informace](/rest/api/storageservices/)