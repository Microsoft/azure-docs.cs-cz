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
ms.openlocfilehash: d73bab76860abf3e9fa442dad44e1ddb66d147a3
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519544"
---
Azure poskytuje následující integrované role RBAC pro autorizaci přístupu k datům objektů blob a fronty pomocí Azure AD a OAuth:

- [Vlastník dat objektu blob úložiště:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner)Slouží k nastavení vlastnictví a spravovat řízení přístupu POSIX pro Azure Data Lake Storage Gen2. Další informace najdete [v tématu Řízení přístupu v Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Přispěvatel dat objektu blob úložiště:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor)Slouží k udělení oprávnění pro čtení, zápis a odstranění prostředkůúložiště objektů Blob.
- [Čtečka dat objektu blob úložiště](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Slouží k udělení oprávnění jen pro čtení prostředkůúložiště objektů Blob.
- [Přispěvatel dat fronty úložiště:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor)Slouží k udělení oprávnění ke čtení, zápisu a odstraňování frontám Azure.
- [Čtečka dat fronty úložiště:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader)Slouží k udělení oprávnění jen pro čtení frontám Azure.
- [Procesor zpráv o datových zprávách fronty úložiště:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor)Slouží k udělení oprávnění k náhledu, načtení a odstranění zpráv ve frontách úložiště Azure.
- [Odesílatel datových zpráv fronty úložiště](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Slouží k udělení oprávnění k přidání zpráv ve frontách úložiště Azure.

Podrobné informace o předdefinovaných rolích RBAC pro Azure Storage pro datové i prodministrované služby najdete v části **Úložiště** ve [integrovaných rolích Azure pro Azure RBAC](../articles/role-based-access-control/built-in-roles.md#storage). Další informace o různých typech rolí, které poskytují oprávnění v Azure, najdete v článku [Klasické role správce předplatného, role Azure RBAC a role Azure AD](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!NOTE]
> Šíření rolí RBAC může trvat až pět minut.
>
> Pouze role explicitně definované pro přístup k datům umožňují objektzabezpečení přístup k datům objektu blob nebo fronty. Role, jako je **vlastník**, **přispěvatel**a **přispěvatel účtu úložiště,** povolují objekt zabezpečení spravovat účet úložiště, ale neposkytují přístup k datům objektu blob nebo fronty v rámci tohoto účtu.
>
> Přístup k datům objektů blob nebo fronty na webu Azure Portal můžete autorizovat buď pomocí vašeho účtu Azure AD, nebo pomocí přístupového klíče účtu úložiště. Další informace najdete [v tématu Použití portálu Azure pro přístup k datům objektu blob nebo fronty](../articles/storage/common/storage-access-blobs-queues-portal.md).
