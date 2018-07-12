---
title: Průvodce zabezpečení Azure AD Privileged Identity Management
description: Při prvním použití rozšíření Azure Active Directory Privileged Identity Management, zobrazí se Průvodce zabezpečením. Tento článek popisuje postup při použití průvodce.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 02/27/2017
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017
ms.openlocfilehash: 6d600afea67d2703c796b403b3e509383f7171a5
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38590286"
---
# <a name="using-the-security-wizard-in-azure-ad-privileged-identity-management"></a>Pomocí Průvodce zabezpečení v Azure AD Privileged Identity Management 
Pokud jste první, kdo ke spuštění Azure Privileged Identity Management (PIM) pro vaši organizaci, zobrazí se průvodce. Průvodce vám pomůže pochopit bezpečnostní rizika privilegované identity a jak používat PIM ke snížení rizika. Není nutné měnit stávající přiřazení rolí v průvodci, pokud chcete to udělat později.

## <a name="what-to-expect"></a>Co můžete očekávat
Před zahájením vaší organizace pomocí PIM jsou trvalé všechna přiřazení rolí: uživatelům se vždycky nacházejí v těchto rolích i v případě, že nejsou potřeba v současné době jejich oprávnění.  První krok průvodce zobrazí seznam rolí vysokými a kolik uživatelů je aktuálně v těchto rolích. Můžete přejít na konkrétní roli Další informace o uživatelích, pokud jeden nebo více z nich neznáte.

Druhý krok průvodce vám dává možnost změnit přiřazení rolí správce.  

> [!WARNING]
> Je důležité, abyste měli aspoň jeden globální správce a více než jeden správce privilegovaných rolí pomocí účtu organizace (ne účet Microsoft). Pokud existuje jen jeden správce privilegovaných rolí, organizaci nebude možné spravovat PIM, pokud tento účet je odstraněný.
> Také zachovat přiřazení rolí trvalé, pokud má uživatel účet Microsoft (účtu, které používají pro přihlášení ke službám Microsoftu, jako je Skype nebo Outlook.com). Pokud budete chtít vyžadovat vícefaktorové ověřování pro aktivaci pro tuto roli, tento uživatel bude uzamčena na.
> 
> 

Po provedení změny, průvodce se už zobrazí. Při příštím vy nebo jiný správce privilegovaných rolí používat PIM, zobrazí se řídicí panel PIM.  

* Pokud chcete přidat nebo odebrat uživatele z role nebo změnit přiřazení z trvalé na oprávněné. Další informace najdete v [postup přidání nebo odebrání role uživatele](pim-how-to-add-role-to-user.md).
* Pokud chcete poskytnout přístup ke správě PIM více uživatelů, další informace najdete v [jak udělit přístup ke správě v PIM](pim-how-to-give-access-to-pim.md).

## <a name="next-steps"></a>Další postup
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]

