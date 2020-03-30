---
title: zahrnout soubor
description: zahrnout soubor s požadavky
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 66b5f8e2-e08d-43c8-b460-6204aecda8f7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.custom: include file
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: f7b1b294e9500ef9e0aadd24cfe3cd4e61fddda1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68426867"
---
> [!IMPORTANT]
> **Povolte synchronizaci hodnot hash hesel do služby Azure AD Domain Services před dokončením úkolů v tomto článku.**
>
> Postupujte podle pokynů níže, v závislosti na typu uživatelů v adresáři Azure AD. Pokud máte v adresáři Azure AD kombinaci uživatelských účtů pouze pro cloud a synchronizované, dokončete obě sady pokynů. V případě, že se pokoušíte použít účet B2B Guest (například váš gmail nebo MSA od jiného poskytovatele identity, který povolujeme), nemusí být možné provádět následující operace, protože nemáme heslo pro tyto uživatele synchronizované se spravovanou doménou, protože se jedná o účty host v adresáři. Úplné informace o těchto účtech, včetně jejich hesla by mimo Azure AD a jako tyto informace není ve službě Azure AD proto není ani získat synchronizovány do spravované domény. 
> - [Pokyny pro uživatelské účty pouze pro cloud](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [Pokyny pro uživatelské účty synchronizované z místního adresáře](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
