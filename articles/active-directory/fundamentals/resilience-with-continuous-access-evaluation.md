---
title: Odolnost sestavení pomocí vyhodnocení průběžného přístupu v Azure Active Directory
description: Příručka pro architekty a správce IT na používání CAE
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ad36c2a7f47948d9362b85e78355e6046cda703
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919414"
---
# <a name="build-resilience-by-using-continuous-access-evaluation"></a>Odolnost sestavení pomocí vyhodnocení průběžného přístupu

[Vyhodnocování průběžného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation) (CAE) umožňuje aplikacím Azure AD přihlašovat se k odběru kritických událostí, které je pak možné vyhodnotit a vyhovět. To zahrnuje vyhodnocení následujících událostí:

* Uživatelský účet, který se má odstranit nebo zakázat

* Heslo pro uživatele se změnilo.

* Pro uživatele je povoleno vícefaktorové ověřování.

* Správce explicitně odvolává token.

* Bylo zjištěno zvýšené riziko uživatele.

V důsledku toho mohou aplikace odmítat neprošlé tokeny na základě událostí, které jsou znázorněny v rámci služby Azure AD, jak je znázorněno v následujícím diagramu.

![conceptualiagram z CAE](./media/resilience-with-cae/admin-resilience-continuous-access-evaluation.png)

## <a name="how-does-cae-help"></a>Jak CAE Help?

Tento mechanismus umožňuje, aby služba Azure AD vydávala dlouhodobé tokeny, a současně umožňuje aplikacím odvolat přístup a vynutit opakované ověřování, pouze pokud je to potřeba. Výsledek netto tohoto modelu je menší počet volání k získání tokenů, což znamená, že koncový tok je pružnější. 

Aby bylo možné používat CAE, musí být služba i klient podporující CAE. Služby Microsoft 365, jako jsou Exchange Online, teams a SharePoint Online, podporují CAE. Na straně klienta jsou prostředí založená na prohlížeči, které používají tyto služby Office 365 (například webová aplikace Outlook) a konkrétní verze nativních klientů Office 365, CAE. Další cloudové služby Microsoftu se stanou podporou CAE.

Microsoft spolupracuje s oborem vytváření [standardů](https://openid.net/wg/sse/) , které umožní používání této funkce pro aplikace třetích stran. Můžete také vyvíjet aplikace, které podporují CAE. Další informace najdete v tématu postup sestavení odolnosti ve vaší aplikaci.

## <a name="how-do-i-implement-cae"></a>Návody implementovat CAE?

* [Povolte CAE](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation) v konfiguraci zabezpečení Azure AD.

* Ujistěte se, že vaše organizace používá [kompatibilní verze](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation) systém Microsoft Office nativních aplikací.

* [Vyoptimalizujte výzvy k opakovanému ověření](https://docs.microsoft.com/azure/active-directory/authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime).

 
## <a name="next-steps"></a>Další kroky
Prostředky odolnosti pro správce a architekty
 
* [Odolnost sestavení se správou přihlašovacích údajů](resilience-in-credentials.md)

* [Odolnost sestavení se stavy zařízení](resilience-with-device-states.md)

* [Odolnost sestavení při ověřování externích uživatelů](resilience-b2b-authentication.md)

* [Odolnost sestavení v hybridním ověřování](resilience-in-hybrid.md)

* [Odolnost sestavení v přístupu aplikace s proxy aplikací](resilience-on-premises-access.md)

Prostředky odolnosti pro vývojáře

* [Sestavování odolnosti IAM ve vašich aplikacích](resilience-app-development-overview.md)

* [Odolnost sestavení v systémech CIAM](resilience-b2c.md)
