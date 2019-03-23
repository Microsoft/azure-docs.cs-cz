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
ms.openlocfilehash: 50217829be7aff3bcdbf417f19fff9f1fd7c6901
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372660"
---
> [!NOTE]
> - Přiřazení rolí pro RBAC může trvat až dvě minuty rozšíření.
>
> K autorizaci objektů blob a frontu operací pomocí tokenu OAuth, musí používat protokol HTTPS.
>
> - Na webu Azure portal teď podporuje použití přihlašovacích údajů Azure AD ke čtení a zápisu objektů blob a fronty data jako součást verze preview verzi. Pro přístup k datům objektu blob a fronty na webu Azure Portal, uživatel musí mít přiřazenou Azure Resource Manageru **čtečky** role, kromě příslušné role pro přístup k objektu blob nebo fronty. Další informace najdete v tématu [udělit přístup k kontejnery služby Azure a fronty pomocí RBAC na webu Azure Portal](../articles/storage/common/storage-auth-aad-rbac.md). 
> 
> - [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) aktuálně používá klíč účtu úložiště pro přístup k datům objektu blob a fronty. Pokud klíč není k dispozici, použije se autorizace Azure AD pro přístup k objektům BLOB. U front se aktuálně nepodporuje ověřování Azure AD. 
>
> - Služba soubory Azure podporuje ověřování pomocí Azure AD prostřednictvím protokolu SMB pro připojené k doméně pouze pro virtuální počítače (preview). Další informace o používání služby Azure AD prostřednictvím protokolu SMB pro soubory Azure najdete v tématu [přehled o Azure Active Directory authentication přes protokol SMB pro soubory Azure (preview)](../articles/storage/files/storage-files-active-directory-overview.md).