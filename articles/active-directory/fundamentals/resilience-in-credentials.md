---
title: Odolnost sestavení se správou přihlašovacích údajů v Azure Active Directory
description: Příručka pro architekty a správce IT při vytváření odolné strategie pro přihlašovací údaje.
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
ms.openlocfilehash: b5fd5cf419adb137df5c578e3b17b88749215be5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919619"
---
# <a name="build-resilience-with-credential-management"></a>Odolnost sestavení se správou přihlašovacích údajů

Pokud se přihlašovací údaje prezentují službě Azure AD v žádosti o token, existuje několik závislostí, které musí být pro ověření k dispozici. První faktor ověřování spoléhá na ověřování Azure AD a v některých případech na místní infrastrukturu. Další informace o architekturách hybridního ověřování najdete v tématu [odolnost sestavení v hybridní infrastruktuře](resilience-in-hybrid.md). 

Pokud implementujete druhý faktor, budou závislosti pro druhý faktor přidány do závislostí pro první. Například pokud váš první faktor je prostřednictvím PTA a váš druhý faktor je SMS, vaše závislosti jsou:

* Služby ověřování Azure AD

* Služba Azure MFA

* Místní infrastruktura

* Telefonní operátor

* Zařízení uživatele (bez obrázku)

 
![Obrázek metod ověřování a závislostí](./media/resilience-in-credentials/admin-resilience-credentials.png)

Vaše strategie vašich přihlašovacích údajů by měla vzít v úvahu závislosti každého typu ověřování a metody zřizování, které se vyhne jedinému bodu selhání. 

Vzhledem k tomu, že metody ověřování mají různé závislosti, je vhodné uživatelům povolit, aby si zaregistrovali tolik možností druhého faktoru, jak je to možné. Pokud je to možné, nezapomeňte zahrnout i druhý faktor s různými závislostmi. Například hlasové hovory a SMS jako druhý faktor sdílí stejné závislosti, takže mají stejné možnosti, protože nezmírňují riziko.

Nejkomplexnější strategii přihlašovacích údajů je použít ověřování pomocí hesla. Bezpečnostní klíče Windows Hello pro firmy a FIDO 2,0 mají méně závislostí než silné ověřování se dvěma samostatnými faktory. Microsoft Authenticator aplikace, klíče zabezpečení Windows Hello pro firmy a Fido 2,0 jsou nejbezpečnější. 

Pro druhé faktory má Microsoft Authenticator aplikace nebo jiné ověřovací aplikace s využitím TOTP (Time-to-based data) nebo tokenů OATH nejnižší závislosti a jsou proto pružnější.

## <a name="how-do-multiple-credentials-help-resilience"></a>Jak více přihlašovacích údajů pomůžou odolnost proti chybám?

Zřizování více typů přihlašovacích údajů poskytuje uživatelům možnosti, které přizpůsobí své preference a omezení prostředí. V důsledku toho interaktivní ověřování, kdy se uživatelům zobrazí výzva k ověření služby Multi-Factor Authentication, bude v době žádosti pružnější na konkrétní závislosti nedostupné. Můžete [optimalizovat výzvy k opakovanému ověření pro službu Multi-Factor Authentication](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

Kromě individuálních odolností uživatelů popsaných v tématu by podniky měly plánovat nepředvídané porušování, jako jsou provozní chyby, které zavádějí chybnou konfiguraci, přírodní havárie nebo výpadek podnikových prostředků do místní služby FS (zejména při použití pro Multi-Factor Authentication). 

## <a name="how-do-i-implement-resilient-credentials"></a>Návody implementovat odolné přihlašovací údaje?

* Nasaďte [přihlašovací údaje bez hesla](../authentication/howto-authentication-passwordless-deployment.md) , jako jsou Windows Hello pro firmy, telefonické ověřování a FIDO2 bezpečnostní klíče, abyste snížili závislosti.

* Nasaďte [aplikaci Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) jako druhý faktor.

* Zapněte [synchronizaci hodnot hash hesel](../hybrid/whatis-phs.md) u hybridních účtů synchronizovaných ze služby Windows Server Active Directory. Tato možnost se dá povolit souběžně s federačními službami, jako je AD FS, a poskytuje návrat k tomu, aby služba FS (Federation Service) nebyla úspěšná.

* [Analyzujte využití metod Multi-Factor Authentication](https://docs.microsoft.com/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/) pro zlepšení zkušeností uživatelů.

* [Implementace strategie odolného řízení přístupu](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>Další kroky
Prostředky odolnosti pro správce a architekty
 
* [Odolnost sestavení se stavy zařízení](resilience-with-device-states.md)

* [Odolnost sestavení pomocí vyhodnocení průběžného přístupu (CAE)](resilience-with-continuous-access-evaluation.md)

* [Odolnost sestavení při ověřování externích uživatelů](resilience-b2b-authentication.md)

* [Odolnost sestavení v hybridním ověřování](resilience-in-hybrid.md)

* [Odolnost sestavení v přístupu aplikace s proxy aplikací](resilience-on-premises-access.md)

Prostředky odolnosti pro vývojáře

* [Sestavování odolnosti IAM ve vašich aplikacích](resilience-app-development-overview.md)

* [Odolnost sestavení v systémech CIAM](resilience-b2c.md)
