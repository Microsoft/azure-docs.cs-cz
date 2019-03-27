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
ms.openlocfilehash: 4c66f35537cff87eb53a6cfe9bc0389f038f1a10
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449760"
---
Azure poskytuje následující předdefinované role RBAC pro přístup k datům úložiště:

- [Vlastník dat objektů Blob úložiště](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview): Slouží k nastavení vlastnictví a správě řízení přístupu POSIX pro Azure Data Lake Storage Gen2 (preview). Další informace najdete v tématu [řízení přístupu v Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Přispěvatel dat objektu Blob úložiště](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview): Slouží k udělení oprávnění ke čtení/zápis/delete k prostředkům úložiště objektů Blob.
- [Čtenář dat objektu Blob úložiště](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview): Slouží k udělení oprávnění jen pro čtení k prostředkům úložiště objektů Blob.
- [Přispěvatel dat fronty služby Storage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview): Slouží k udělení oprávnění pro čtení/zápisu a odstranění do fronty Azure.
- [Čtenář dat fronty služby Storage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview): Slouží k udělení oprávnění jen pro čtení do fronty Azure.
- [Data úložiště fronty zpráv procesoru](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor-preview): Slouží k udělení náhled, načíst a odstranit oprávnění pro zprávy ve frontách Azure Storage.
- [Data úložiště fronty zpráv odesílatele](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender-preview): Použít k udělení oprávnění přidat do zprávy ve frontách Azure Storage.

> [!IMPORTANT]
> Přiřazení rolí pro RBAC může trvat až pět minut na dokončení propagace.

Další informace o tom, předdefinované role jsou definované pro službu Azure Storage, najdete v článku [pochopení definic rolí](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview). Informace o vytváření vlastních rolí RBAC najdete v tématu [vytváření vlastních rolí pro řízení přístupu](../articles/role-based-access-control/custom-roles.md). 
