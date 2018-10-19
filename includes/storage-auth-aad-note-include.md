---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/09/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 2d641287bcf095f13719667a91b7f61295309b5d
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430353"
---
> [!NOTE]
> - Náhled ověřování Azure AD pro objekty BLOB a fronty je určeno pouze pro nevýrobní prostředí. Produkční smlouvy o úrovni služeb (SLA) nejsou aktuálně k dispozici. Pokud pro váš scénář zatím nepodporuje ověřování Azure AD, nadále používat povolení sdíleného klíče nebo tokeny SAS ve svých aplikacích.
>
> - Ve verzi preview přiřazení rolí pro RBAC může trvat až pět minut na dokončení propagace.
>
> - Musí používat protokol HTTPS k ověřování ve službě Azure AD při volání operace objektů blob a fronty.
>
> - Na webu Azure portal teď podporuje používání přihlašovacích údajů Azure AD ke čtení a zápis dat objektů blob jako součást verze preview.
> 
> - Na webu Azure portal nepodporuje aktuálně pomocí přihlašovacích údajů Azure AD. číst a zapisovat data ve frontě. Data ve frontě se přistupuje přes své klíče účtu úložiště.
>
> - [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) aktuálně používá klíč účtu úložiště pro přístup k datům objektu blob a fronty.
>
> - Služba soubory Azure podporuje ověřování pomocí Azure AD prostřednictvím protokolu SMB pro připojené k doméně pouze pro virtuální počítače (preview). Další informace o používání služby Azure AD prostřednictvím protokolu SMB pro soubory Azure najdete v tématu [přehled o Azure Active Directory authentication přes protokol SMB pro soubory Azure (preview)](../articles/storage/files/storage-files-active-directory-overview.md).



