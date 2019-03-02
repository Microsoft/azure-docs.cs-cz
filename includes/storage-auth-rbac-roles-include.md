---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/19/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 9402c4b24c9d64b4b69d750fbd19de40cda396f3
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2019
ms.locfileid: "57218086"
---
Azure poskytuje následující předdefinované role RBAC pro přístup k datům úložiště:

- [Vlastník dat úložiště objektů Blob (Preview)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview): Slouží k nastavení vlastnictví a správě řízení přístupu POSIX pro Azure Data Lake Storage Gen2 (preview). Další informace najdete v tématu [řízení přístupu v Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Přispěvatel dat objektu Blob služby Storage (Preview)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview): Slouží k udělení oprávnění ke čtení/zápis/delete k prostředkům úložiště objektů Blob.
- [Čtenář dat objektu Blob služby Storage (Preview)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview): Slouží k udělení oprávnění jen pro čtení k prostředkům úložiště objektů Blob.
- [Přispěvatel dat fronty služby Storage (Preview)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview): Slouží k udělení oprávnění pro čtení/zápisu a odstranění do fronty Azure.
- [Čtenář dat fronty služby Storage (Preview)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview): Slouží k udělení oprávnění jen pro čtení do fronty Azure.

Další informace o tom, předdefinované role jsou definované pro službu Azure Storage, najdete v článku [pochopení definic rolí](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview). Informace o vytváření vlastních rolí RBAC najdete v tématu [vytváření vlastních rolí pro řízení přístupu](../articles/role-based-access-control/custom-roles.md). 
