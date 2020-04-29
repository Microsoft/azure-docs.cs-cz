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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/26/2020
ms.locfileid: "68426867"
---
> [!IMPORTANT]
> **Před dokončením úkolů v tomto článku povolte synchronizaci hodnot hash hesel do Azure AD Domain Services.**
>
> Postupujte podle pokynů níže v závislosti na typu uživatelů v adresáři služby Azure AD. V případě, že máte v adresáři Azure AD kombinaci pouze cloudových a synchronizovaných uživatelských účtů, dokončete obě sady instrukcí. V případě, že se pokoušíte použít účet Guest B2B (například vaše Gmail nebo MSA od jiného poskytovatele identity, kterého povolujeme), nemusíte mít možnost provádět následující operace, protože pro tyto uživatele není k dispozici heslo, které by bylo možné synchronizovat se spravovanou doménou, protože se jedná o účty hostů v adresáři. Úplné informace o těchto účtech, včetně hesel, by byly mimo službu Azure AD a že tyto informace nejsou v Azure AD, takže se ani nesynchronizují do spravované domény. 
> - [Pokyny pro uživatelské účty jenom pro Cloud](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [Pokyny pro uživatelské účty synchronizované z místního adresáře](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
