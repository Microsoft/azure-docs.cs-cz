---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/25/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: c2b409f0eefe5efa389432cbb007cc08e0c6ae1e
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71078236"
---
Azure poskytuje následující předdefinované role RBAC pro autorizaci přístupu k datům BLOB a front pomocí Azure AD a OAuth:

- [Vlastník dat objektu BLOB služby Storage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Slouží k nastavení vlastnictví a správě řízení přístupu POSIX pro Azure Data Lake Storage Gen2. Další informace najdete v tématu [řízení přístupu v Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Přispěvatel dat objektu BLOB služby Storage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Slouží k udělení oprávnění ke čtení, zápisu a odstraňování prostředků BLOB Storage.
- [Čtečka dat objektů BLOB úložiště](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Slouží k udělení oprávnění jen pro čtení k prostředkům BLOB Storage.
- [Přispěvatel dat fronty úložiště](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Slouží k udělení oprávnění ke čtení, zápisu a odstraňování front Azure.
- [Čtečka dat fronty úložiště](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Slouží k udělení oprávnění jen pro čtení ke frontám Azure.
- [Procesor zpráv s daty ve frontě úložiště](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Slouží k udělení oprávnění k prohlížení, načítání a odstraňování zpráv v Azure Storagech frontách.
- [Odesílatel zprávy s daty ve frontě úložiště](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Slouží k udělení oprávnění přidat zprávám ve frontě Azure Storage.

> [!NOTE]
> Rozšiřování přiřazení rolí RBAC může trvat až pět minut.
>
> Pouze role definované pro přístup k datům umožňují objektu zabezpečení přístup k datům objektů BLOB nebo front. Role, jako je **vlastník**, **Přispěvatel**a **Přispěvatel účtu úložiště** , umožňují objektu zabezpečení Spravovat účet úložiště, ale neposkytují přístup k datům objektů BLOB ani Queue v rámci tohoto účtu.
