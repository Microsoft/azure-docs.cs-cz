---
title: Udělení ovládacích prvků v zásadách podmíněného přístupu – Azure Active Directory
description: Co poskytuje řízení v zásadách podmíněného přístupu Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89063cc8131c28f20153c6fe9b4c71b58794e609
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192113"
---
# <a name="conditional-access-grant"></a>Podmíněný přístup: udělení

V rámci zásad podmíněného přístupu může správce využít řízení přístupu k udělení nebo blokování přístupu k prostředkům.

![Zásada podmíněného přístupu s uděleným ovládacím prvkem, který vyžaduje vícefaktorové ověřování](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Blokovat přístup

Blok vezme v úvahu všechna přiřazení a zabraňuje přístupu na základě konfigurace zásad podmíněného přístupu.

Blok je výkonný ovládací prvek, který by měl být wielded s odpovídajícím vědomím. Správce by měl použít [režim pouze pro sestavy](concept-conditional-access-report-only.md) k otestování před povolením.

## <a name="grant-access"></a>Udělení přístupu

Správci se můžou rozhodnout vyhovět jednomu nebo více ovládacím prvkům při udělení přístupu. Tyto ovládací prvky zahrnují následující možnosti: 

- [Vyžadovat Multi-Factor Authentication (Azure Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)
- [Vyžadovat, aby zařízení bylo označené jako vyhovující (Microsoft Intune)](https://docs.microsoft.com/intune/protect/device-compliance-get-started)
- [Vyžadovat zařízení připojené k hybridní službě Azure AD](../devices/concept-azure-ad-join-hybrid.md)
- [Vyžadovat klientskou aplikaci schválenou](app-based-conditional-access.md)
- [Vyžadovat zásady ochrany aplikací](app-protection-based-conditional-access.md)

Když se správci rozhodnou tyto možnosti kombinovat, můžou zvolit následující metody:

- Vyžadovat všechny vybrané ovládací prvky (ovládací prvek **a** ovládací prvek)
- Vyžadovat jeden z vybraných ovládacích prvků (ovládací prvek **nebo** ovládací prvek)

Ve výchozím nastavení podmíněný přístup vyžaduje všechny vybrané ovládací prvky.

### <a name="require-multi-factor-authentication"></a>Vyžadovat Multi-Factor Authentication

Zaškrtnutí tohoto políčka bude vyžadovat, aby uživatelé prováděli Multi-Factor Authentication Azure. Další informace o nasazení služby Azure Multi-Factor Authentication najdete v článku [plánování cloudového nasazení azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md).

### <a name="require-device-to-be-marked-as-compliant"></a>Vyžadovat, aby zařízení bylo označené jako vyhovující

Organizace, které nasadily Microsoft Intune, můžou použít informace vrácené ze svých zařízení k identifikaci zařízení, která splňují konkrétní požadavky na dodržování předpisů. Tyto informace o dodržování zásad se předávají z Intune do Azure AD, kde podmíněný přístup může učinit rozhodnutí udělit nebo blokovat přístup k prostředkům. Další informace o zásadách dodržování předpisů najdete v článku [Nastavení pravidel pro zařízení, která umožňují přístup k prostředkům ve vaší organizaci pomocí Intune](https://docs.microsoft.com/intune/protect/device-compliance-get-started).

### <a name="require-hybrid-azure-ad-joined-device"></a>Vyžadovat zařízení připojené k hybridní službě Azure AD

Organizace se můžou rozhodnout používat identitu zařízení jako součást zásad podmíněného přístupu. Organizace můžou vyžadovat, aby se zařízení připojila k hybridní službě Azure AD pomocí tohoto zaškrtávacího políčka. Další informace o identitách zařízení najdete v článku [co je identita zařízení?](../devices/overview.md).

### <a name="require-approved-client-app"></a>Vyžaduje se klientem schválená aplikace.

Organizace můžou vyžadovat, aby se pokus o přístup k vybraným cloudovým aplikacím nastavil ze schválené klientské aplikace.

Toto nastavení platí pro následující klientské aplikace:

- Microsoft Azure Information Protection
- Rezervace Microsoftu
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft fakturace
- Microsoft Kaizala
- Spouštěcí program společnosti Microsoft
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Aplikace Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype pro firmy
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer

**Poznámky**

- Schválené klientské aplikace podporují funkci správy mobilních aplikací Intune.
- Požadavek na **vyžadování schválené aplikace klienta** :
   - Podporuje jenom podmínku platformy iOS a Android pro zařízení.
- Podmíněný přístup nemůže vzít v úvahu schválenou klientskou aplikaci Microsoft Edge v režimu InPrivate.

### <a name="require-app-protection-policy"></a>Vyžadování zásad ochrany aplikací

V rámci zásad podmíněného přístupu můžete vyžadovat, aby se v klientské aplikaci nacházely zásady ochrany aplikací, než bude přístup k vybraným cloudovým aplikacím k dispozici. 

![Řízení přístupu pomocí zásad ochrany aplikací](./media/technical-reference/22.png)

Toto nastavení platí pro následující klientské aplikace:

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Aplikace Microsoft Planner

**Poznámky**

- Aplikace pro zásady ochrany aplikací podporují funkci správy mobilních aplikací Intune s ochranou zásad.
- Požadavky na **zásady pro vyžadování zásad ochrany aplikací** :
    - Podporuje jenom podmínku platformy iOS a Android pro zařízení.

## <a name="next-steps"></a>Další kroky

- [Podmíněný přístup: ovládací prvky relace](concept-conditional-access-session.md)

- [Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

- [Režim pouze sestav](concept-conditional-access-report-only.md)
