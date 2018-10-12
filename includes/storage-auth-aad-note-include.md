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
ms.openlocfilehash: ac7aa294dee25a6dea4ec2ee614f2b2d34d3a6bc
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49117673"
---
> [!IMPORTANT]
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


