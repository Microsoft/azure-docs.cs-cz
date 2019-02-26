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
ms.openlocfilehash: 694491f3bec9fd1e62e65504968be9f06dda0c11
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56824249"
---
> [!NOTE]
> - Náhled ověřování Azure AD pro objekty BLOB a fronty je určeno pouze pro nevýrobní prostředí. Produkční smlouvy o úrovni služeb (SLA) nejsou aktuálně k dispozici. Pokud pro váš scénář zatím nepodporuje ověřování Azure AD, nadále používat povolení sdíleného klíče nebo tokeny SAS ve svých aplikacích.
>
> - Ve verzi preview přiřazení rolí pro RBAC může trvat až pět minut na dokončení propagace.
>
> - K autorizaci objektů blob a frontu operací pomocí tokenu OAuth, musí používat protokol HTTPS.
>
> - Na webu Azure portal teď podporuje použití přihlašovacích údajů Azure AD ke čtení a zápisu objektů blob a fronty data jako součást verze preview verzi. 
> 
> - [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) aktuálně používá klíč účtu úložiště pro přístup k datům objektu blob a fronty. Pro objekty BLOB se podporuje přístup OAuth.
>
> - Služba soubory Azure podporuje ověřování pomocí Azure AD prostřednictvím protokolu SMB pro připojené k doméně pouze pro virtuální počítače (preview). Další informace o používání služby Azure AD prostřednictvím protokolu SMB pro soubory Azure najdete v tématu [přehled o Azure Active Directory authentication přes protokol SMB pro soubory Azure (preview)](../articles/storage/files/storage-files-active-directory-overview.md).