---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: f014ce55dc40723faf1b60f908814f9fa0428b8e
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99213928"
---
Azure poskytuje následující předdefinované role Azure pro autorizaci přístupu k datům BLOB a front pomocí Azure AD a OAuth:

- [Vlastník dat objektu BLOB služby Storage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): pomocí tohoto nastavení můžete nastavit vlastnictví a spravovat řízení přístupu POSIX pro Azure Data Lake Storage Gen2. Další informace najdete v tématu [řízení přístupu v Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Přispěvatel dat objektu BLOB služby Storage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): slouží k udělení oprávnění ke čtení, zápisu a odstraňování prostředků BLOB Storage.
- [Čtečka dat objektů BLOB úložiště](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): slouží k udělení oprávnění jen pro čtení k prostředkům BLOB Storage.
- Delegovaný [objekt BLOB úložiště](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator): Získá klíč pro delegování uživatelů, který se použije k vytvoření sdíleného přístupového podpisu podepsaného pomocí přihlašovacích údajů Azure AD pro kontejner nebo objekt BLOB.
- [Přispěvatel dat fronty úložiště](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): slouží k udělení oprávnění ke čtení, zápisu a odstraňování front Azure.
- [Čtečka dat fronty úložiště](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): slouží k udělení oprávnění jen pro čtení ke frontám Azure.
- [Procesor zpráv s daty ve frontě úložiště](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): slouží k udělení oprávnění k prohlížení, načítání a odstraňování zpráv v Azure Storagech frontách.
- [Odesílatel zprávy s daty ve frontě úložiště](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): slouží k udělení oprávnění přidat zprávám ve frontě Azure Storage.

Pouze role definované pro přístup k datům umožňují objektu zabezpečení přístup k datům objektů BLOB nebo front. Předdefinované role, jako je **vlastník**, **Přispěvatel** a **Přispěvatel účtu úložiště** , umožňují objektu zabezpečení Spravovat účet úložiště, ale neposkytují přístup k datům objektů BLOB nebo Queue v rámci tohoto účtu přes Azure AD. Pokud ale role obsahuje **Microsoft. Storage/storageAccounts/klíče listkey/Action**, bude mít uživatel, ke kterému je tato role přiřazená, přístup k datům v účtu úložiště prostřednictvím autorizace pomocí sdíleného klíče s přístupovými klíči účtu. Další informace najdete v tématu [použití Azure Portal k přístupu k datům objektů BLOB nebo Queue](../articles/storage/blobs/authorize-data-operations-portal.md).

Podrobné informace o předdefinovaných rolích Azure pro Azure Storage pro datové služby a službu pro správu najdete v části **úložiště** v [předdefinovaných rolích Azure pro službu Azure RBAC](../articles/role-based-access-control/built-in-roles.md#storage). Další informace o různých typech rolí, které poskytují oprávnění v Azure, najdete v tématu [role správců pro klasický odběr, role Azure a role Azure AD](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> Rozšiřování přiřazení rolí Azure může trvat až 30 minut.
