---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 026717dff2b6883eb643497dec91226e4afe8133
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "60150213"
---
Azure poskytuje následující předdefinované role RBAC pro autorizaci přístupu k datům objektu blob a fronty pomocí služby Azure AD a protokolem OAuth:

- [Vlastník dat objektů Blob úložiště](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Slouží k nastavení vlastnictví a správě řízení přístupu POSIX pro Azure Data Lake Storage Gen2 (preview). Další informace najdete v tématu [řízení přístupu v Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Přispěvatel dat objektu Blob úložiště](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Slouží k udělení oprávnění ke čtení/zápis/delete k prostředkům úložiště objektů Blob.
- [Čtenář dat objektu Blob úložiště](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Slouží k udělení oprávnění jen pro čtení k prostředkům úložiště objektů Blob.
- [Přispěvatel dat fronty služby Storage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Slouží k udělení oprávnění pro čtení/zápisu a odstranění do fronty Azure.
- [Čtenář dat fronty služby Storage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Slouží k udělení oprávnění jen pro čtení do fronty Azure.
- [Data úložiště fronty zpráv procesoru](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Slouží k udělení náhled, načíst a odstranit oprávnění pro zprávy ve frontách Azure Storage.
- [Data úložiště fronty zpráv odesílatele](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Použít k udělení oprávnění přidat do zprávy ve frontách Azure Storage.

> [!NOTE]
> Mějte na paměti, která přiřazení rolí pro RBAC může trvat až pět minut na dokončení propagace.
