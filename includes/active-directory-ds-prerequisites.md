---
title: zahrnout soubor
description: Zahrnout soubor s požadavky
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
ms.openlocfilehash: 1fba8cc9ae40cf5539016bbd73de65f557a64136
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66111385"
---
> [!IMPORTANT]
> **Povolte synchronizaci hodnot hash hesel do služby Azure AD Domain Services, před provedením úkolů v tomto článku.**
>
> Postupujte podle pokynů níže, v závislosti na typu uživatele v adresáři služby Azure AD. Proveďte obě sady pokynů, pokud máte jak výhradně cloudové tak synchronizované uživatelské účty v adresáři služby Azure AD. Není možné provádět následující operace v případě, že se pokoušíte použít účet B2B Guest (například váš gmail nebo MSA z jiného zprostředkovatele Identity, která nastavení jsou povoleny) vzhledem k tomu, že nejsou dostupné žádné heslo pro tyto uživatele synchronizované do spravované domény jako tyto jsou účty hostů v adresáři. Úplné informace o těchto účtech, včetně jejich hesla by mimo Azure AD a jak tyto informace není ve službě Azure AD proto jej není i získat synchronizované do spravované domény. 
> - [Pokyny pro uživatelské účty jenom cloudu](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [Pokyny pro uživatelské účty synchronizované z místního adresáře](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
