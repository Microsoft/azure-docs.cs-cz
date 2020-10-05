---
title: Seznámení se službou Table Storage – Úložiště objektů v Azure | Microsoft Docs
description: Ukládejte si strukturovaná data v cloudu pomocí Azure Table Storage, úložiště dat typu NoSQL.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.devlang: dotnet
ms.topic: overview
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 2670d9ce568195fd97350bf678059ac89610422f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88236161"
---
# <a name="what-is-azure-table-storage-"></a>Co je Azure Table Storage? 

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Azure Table Storage je služba, která ukládá strukturovaná data NoSQL do cloudu a poskytuje úložiště klíčů/atributů s návrhem bez použití schématu. Vzhledem k tomu, že je Table Storage bez schématu, je snadné data přizpůsobovat měnícím se potřebám vaší aplikace. Přístup k datům Table Storage je pro mnoho typů aplikací rychlý a nákladově efektivní a pro podobné objemy dat obvykle znamená nižší náklady než tradiční SQL.

Table Storage můžete používat k ukládání flexibilních datových sad, například uživatelských dat pro webové aplikace, adresářů, informací o zařízení nebo dalších typů metadat, které vaše služba vyžaduje. V tabulce můžete uložit libovolný počet entit a účet úložiště může obsahovat libovolný počet tabulek, až do limitu kapacity účtu úložiště.

[!INCLUDE [storage-table-concepts-include](../../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>Další kroky

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.

* [Začněte se službou Azure Table Storage pomocí rozhraní .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)

* Projděte si referenční dokumentaci ke službě Table service, kde najdete úplné podrobnosti o dostupných rozhraních API:

    * [Klientská knihovna pro úložiště – referenční informace pro .NET](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)

    * [REST API – referenční informace](https://msdn.microsoft.com/library/azure/dd179355)
