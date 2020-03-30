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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76279317"
---
Azure poskytuje následující integrované role RBAC pro autorizaci přístupu k datům objektů blob a fronty pomocí Azure AD a OAuth:

- [Vlastník dat objektu blob úložiště:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner)Slouží k nastavení vlastnictví a spravovat řízení přístupu POSIX pro Azure Data Lake Storage Gen2. Další informace najdete [v tématu Řízení přístupu v Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Přispěvatel dat objektu blob úložiště:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor)Slouží k udělení oprávnění pro čtení, zápis a odstranění prostředkůúložiště objektů Blob.
- [Čtečka dat objektu blob úložiště](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Slouží k udělení oprávnění jen pro čtení prostředkůúložiště objektů Blob.
- [Přispěvatel dat fronty úložiště:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor)Slouží k udělení oprávnění ke čtení, zápisu a odstraňování frontám Azure.
- [Čtečka dat fronty úložiště:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader)Slouží k udělení oprávnění jen pro čtení frontám Azure.
- [Procesor zpráv o datových zprávách fronty úložiště:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor)Slouží k udělení oprávnění k náhledu, načtení a odstranění zpráv ve frontách úložiště Azure.
- [Odesílatel datových zpráv fronty úložiště](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Slouží k udělení oprávnění k přidání zpráv ve frontách úložiště Azure.

> [!NOTE]
> Šíření rolí RBAC může trvat až pět minut.
>
> Pouze role explicitně definované pro přístup k datům umožňují objektzabezpečení přístup k datům objektu blob nebo fronty. Role, jako je **vlastník**, **přispěvatel**a **přispěvatel účtu úložiště,** povolují objekt zabezpečení spravovat účet úložiště, ale neposkytují přístup k datům objektu blob nebo fronty v rámci tohoto účtu.
>
> Přístup k datům objektů blob nebo fronty na webu Azure Portal můžete autorizovat buď pomocí vašeho účtu Azure AD, nebo pomocí přístupového klíče účtu úložiště. Další informace najdete [v tématu Použití portálu Azure pro přístup k datům objektu blob nebo fronty](../articles/storage/common/storage-access-blobs-queues-portal.md).
