---
title: Přehled služby Azure Table storage
description: Ukládejte si strukturovaná data v cloudu pomocí Azure Table Storage, úložiště dat typu NoSQL.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: dotnet
ms.topic: overview
ms.date: 11/03/2017
ms.author: sngun
ms.openlocfilehash: 5f07d041e7674cb1579247ca2b444017762c5be0
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52867405"
---
# <a name="azure-table-storage-overview"></a>Přehled služby Azure Table Storage

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Azure Table Storage je služba, která ukládá strukturovaná data NoSQL do cloudu a poskytuje úložiště klíčů/atributů s návrhem bez použití schématu. Vzhledem k tomu, že je Table Storage bez schématu, je snadné data přizpůsobovat měnícím se potřebám vaší aplikace. Přístup k datům Table Storage je pro mnoho typů aplikací rychlý a nákladově efektivní a pro podobné objemy dat obvykle znamená nižší náklady než tradiční SQL.

Table Storage můžete používat k ukládání flexibilních datových sad, například uživatelských dat pro webové aplikace, adresářů, informací o zařízení nebo dalších typů metadat, které vaše služba vyžaduje. V tabulce můžete uložit libovolný počet entit a účet úložiště může obsahovat libovolný počet tabulek, až do limitu kapacity účtu úložiště.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>Další postup

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.

* [Začínáme se službou Azure Table Storage pomocí rozhraní .NET](table-storage-how-to-use-dotnet.md)

* Projděte si referenční dokumentaci ke službě Table service, kde najdete úplné podrobnosti o dostupných rozhraních API:

    * [Klientská knihovna Storage pro .NET – referenční informace](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)

    * [REST API – referenční informace](https://msdn.microsoft.com/library/azure/dd179355)
