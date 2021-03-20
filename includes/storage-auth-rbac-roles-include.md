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
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99213928"
---
Azure poskytuje následující předdefinované role Azure pro autorizaci přístupu k datům BLOB a front pomocí Azure AD a OAuth:

- [Vlastník dat v objektech blob služby Storage:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner) Pomocí této role můžete nastavit vlastnictví a spravovat řízení přístupu POSIX pro službu Azure Data Lake Storage Gen2. Další informace najdete v tématu [Řízení přístupu ve službě Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Přispěvatel dat v objektech blob služby Storage:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor) Pomocí této role můžete udělit oprávnění ke čtení, zápisu nebo odstranění prostředků služby Blob Storage.
- [Čtenář dat v objektech blob služby Storage:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader) Pomocí této role můžete udělit jen pro čtení prostředků služby Blob Storage.
- [Delegující objektů blob služby Storage:](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator) Pomocí této role můžete získat klíč delegování, který můžete použít k vytvoření sdíleného přístupového podpisu podepsaného přihlašovacími údaji Azure AD pro kontejner nebo objekt blob.
- [Přispěvatel dat ve frontách služby Storage:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor) Pomocí této role můžete udělit oprávnění ke čtení, zápisu nebo odstranění front Azure.
- [Čtenář dat ve frontách služby Storage:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader) Pomocí této role můžete udělit oprávnění jen pro čtení front Azure.
- [Procesor datových zpráv ve frontách služby Storage:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor) Pomocí této role můžete udělit oprávnění k náhledu, načtení a odstranění zpráv ve frontách služby Azure Storage.
- [Odesílatel datových zpráv ve frontách služby Storage:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender) Pomocí této role můžete udělit oprávnění k přidávání zpráv ve frontách služby Azure Storage.

Pouze role definované pro přístup k datům umožňují objektu zabezpečení přístup k datům objektů BLOB nebo front. Předdefinované role, jako je **vlastník**, **Přispěvatel** a **Přispěvatel účtu úložiště** , umožňují objektu zabezpečení Spravovat účet úložiště, ale neposkytují přístup k datům objektů BLOB nebo Queue v rámci tohoto účtu přes Azure AD. Pokud ale role obsahuje **Microsoft. Storage/storageAccounts/klíče listkey/Action**, bude mít uživatel, ke kterému je tato role přiřazená, přístup k datům v účtu úložiště prostřednictvím autorizace pomocí sdíleného klíče s přístupovými klíči účtu. Další informace najdete v tématu [použití Azure Portal k přístupu k datům objektů BLOB nebo Queue](../articles/storage/blobs/authorize-data-operations-portal.md).

Podrobné informace o předdefinovaných rolích Azure pro Azure Storage pro datové služby a službu pro správu najdete v části **úložiště** v [předdefinovaných rolích Azure pro službu Azure RBAC](../articles/role-based-access-control/built-in-roles.md#storage). Další informace o různých typech rolí, které poskytují oprávnění v Azure, najdete v tématu [role správců pro klasický odběr, role Azure a role Azure AD](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> Rozšiřování přiřazení rolí Azure může trvat až 30 minut.
