---
title: Seznámení se službou Table Storage – Úložiště objektů v Azure | Microsoft Docs
description: Ukládejte si strukturovaná data v cloudu pomocí Azure Table Storage, úložiště dat typu NoSQL.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.devlang: dotnet
ms.topic: overview
ms.date: 01/07/2021
ms.subservice: tables
ms.openlocfilehash: 292104651c6bb9b63e9d8cbe26fea2bf8c6acbf1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98027736"
---
# <a name="what-is-azure-table-storage-"></a>Co je Azure Table Storage? 

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Azure Table Storage je služba, která ukládá nerelační strukturovaná data (označovaná také jako strukturovaná data NoSQL) v cloudu a poskytuje úložiště klíčů a atributů s návrhem bez schématu. Vzhledem k tomu, že je Table Storage bez schématu, je snadné data přizpůsobovat měnícím se potřebám vaší aplikace. Přístup k datům Table Storage je pro mnoho typů aplikací rychlý a nákladově efektivní a pro podobné objemy dat obvykle znamená nižší náklady než tradiční SQL.

Table Storage můžete používat k ukládání flexibilních datových sad, například uživatelských dat pro webové aplikace, adresářů, informací o zařízení nebo dalších typů metadat, které vaše služba vyžaduje. V tabulce můžete uložit libovolný počet entit a účet úložiště může obsahovat libovolný počet tabulek, až do limitu kapacity účtu úložiště.

[!INCLUDE [storage-table-concepts-include](../../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>Další kroky

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.

* [Začněte se službou Azure Table Storage pomocí rozhraní .NET](../../cosmos-db/tutorial-develop-table-dotnet.md)

* Projděte si referenční dokumentaci ke službě Table service, kde najdete úplné podrobnosti o dostupných rozhraních API:

    * [Klientská knihovna pro úložiště – referenční informace pro .NET](/dotnet/api/overview/azure/storage)

    * [REST API – referenční informace](/rest/api/storageservices/)