---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/17/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4b10955a1d3b85acbcae109836bebc03ec04c72c
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279317"
---
Azure poskytuje následující předdefinované role RBAC pro autorizaci přístupu k datům BLOB a front pomocí Azure AD a OAuth:

- [Vlastník dat objektu BLOB služby Storage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): pomocí tohoto nastavení můžete nastavit vlastnictví a spravovat řízení přístupu POSIX pro Azure Data Lake Storage Gen2. Další informace najdete v tématu [řízení přístupu v Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Přispěvatel dat objektu BLOB služby Storage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): slouží k udělení oprávnění ke čtení, zápisu a odstraňování prostředků BLOB Storage.
- [Čtečka dat objektů BLOB úložiště](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): slouží k udělení oprávnění jen pro čtení k prostředkům BLOB Storage.
- [Přispěvatel dat fronty úložiště](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): slouží k udělení oprávnění ke čtení, zápisu a odstraňování front Azure.
- [Čtečka dat fronty úložiště](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): slouží k udělení oprávnění jen pro čtení ke frontám Azure.
- [Procesor zpráv s daty ve frontě úložiště](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): slouží k udělení oprávnění k prohlížení, načítání a odstraňování zpráv v Azure Storagech frontách.
- [Odesílatel zprávy s daty ve frontě úložiště](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): slouží k udělení oprávnění přidat zprávám ve frontě Azure Storage.

> [!NOTE]
> Rozšiřování přiřazení rolí RBAC může trvat až pět minut.
>
> Pouze role definované pro přístup k datům umožňují objektu zabezpečení přístup k datům objektů BLOB nebo front. Role, jako je **vlastník**, **Přispěvatel**a **Přispěvatel účtu úložiště** , umožňují objektu zabezpečení Spravovat účet úložiště, ale neposkytují přístup k datům objektů BLOB ani Queue v rámci tohoto účtu.
>
> Přístup k datům objektů BLOB nebo front v Azure Portal může být autorizován pomocí účtu služby Azure AD nebo přístupového klíče účtu úložiště. Další informace najdete v tématu [použití Azure Portal k přístupu k datům objektů BLOB nebo Queue](../articles/storage/common/storage-access-blobs-queues-portal.md).
