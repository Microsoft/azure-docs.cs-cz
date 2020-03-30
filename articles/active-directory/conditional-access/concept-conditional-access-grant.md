---
title: Udělení ovládacích prvků v zásadách podmíněného přístupu – Azure Active Directory
description: Co jsou grantové ovládací prvky v zásadách podmíněného přístupu Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02ec8dace971cd4dc1407c9e8d20839504c9ecc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331843"
---
# <a name="conditional-access-grant"></a>Podmíněný přístup: Udělení

V rámci zásad podmíněného přístupu může správce využít ovládací prvky přístupu k udělení nebo zablokování přístupu k prostředkům.

![Zásady podmíněného přístupu s ovládacím prvkem udělení, který vyžaduje vícefaktorové ověřování](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Blokovat přístup

Blok bere v úvahu všechna přiřazení a zabraňuje přístupu na základě konfigurace zásad podmíněného přístupu.

Blok je silný ovládací prvek, který by měl být ovládán s odpovídajícími znalostmi. Je to něco, co správci by měli použít [režim pouze pro sestavu](concept-conditional-access-report-only.md) k testování před povolením.

## <a name="grant-access"></a>Udělení přístupu

Správci se mohou rozhodnout vynutit jeden nebo více ovládacích prvků při udělování přístupu. Mezi tyto ovládací prvky patří následující možnosti: 

- [Vyžadovat vícefaktorové ověřování (Azure Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)
- [Vyžadovat, aby zařízení bylo označeno jako vyhovující (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Vyžadovat hybridní zařízení spojené s Azure AD](../devices/concept-azure-ad-join-hybrid.md)
- [Vyžadovat schválenou klientskou aplikaci](app-based-conditional-access.md)
- [Vyžadování zásad ochrany aplikací](app-protection-based-conditional-access.md)

Pokud se správci rozhodnou tyto možnosti kombinovat, mohou zvolit následující metody:

- Vyžadovat všechny vybrané ovládací prvky (řízení **a** řízení)
- Vyžadovat jeden z vybraných ovládacích prvků (ovládací prvek **OR)**

Ve výchozím nastavení vyžaduje podmíněný přístup všechny vybrané ovládací prvky.

### <a name="require-multi-factor-authentication"></a>Vyžadovat vícefaktorové ověřování

Zaškrtnutítohoto políčka bude vyžadovat, aby uživatelé provedli azure multifaktorové ověřování. Další informace o nasazení azure multifaktorové ověřování najdete v článku [Plánování cloudového nasazení Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md).

### <a name="require-device-to-be-marked-as-compliant"></a>Vyžadovat, aby zařízení bylo označeno jako vyhovující

Organizace, které nasadily Microsoft Intune, můžou informace vrácené ze svých zařízení použít k identifikaci zařízení, která splňují specifické požadavky na dodržování předpisů. Tyto informace o dodržování zásad se předávají z Intune do Azure AD, kde podmíněný přístup může rozhodovat o udělení nebo zablokování přístupu k prostředkům. Další informace o zásadách dodržování předpisů najdete v článku [Nastavení pravidel pro zařízení, která umožňují přístup k prostředkům ve vaší organizaci pomocí Intune](/intune/protect/device-compliance-get-started).

Zařízení může být označeno jako vyhovující intune (pro libovolné operační systémy zařízení) nebo systémem MDM jiného výrobce pro zařízení s Windows 10. Jamf pro je jediný podporovaný mdm systém třetí strany. Další informace o integraci najdete v článku [Integrace Jamf Pro s Intune pro dodržování předpisů](/intune/protect/conditional-access-integrate-jamf).

Zařízení musí být registrovaná ve službě Azure AD, aby je bylo možné označit jako kompatibilní. Další informace o registraci zařízení naleznete v článku [Co je identita zařízení](../devices/overview.md).

### <a name="require-hybrid-azure-ad-joined-device"></a>Vyžadovat hybridní zařízení spojené s Azure AD

Organizace se mohou rozhodnout používat identitu zařízení jako součást zásad podmíněného přístupu. Organizace mohou vyžadovat, aby zařízení byla hybridní Azure AD připojena pomocí tohoto políčka. Další informace o identitách zařízení najdete v článku [Co je identita zařízení?](../devices/overview.md).

### <a name="require-approved-client-app"></a>Vyžadovat schválenou klientskou aplikaci

Organizace mohou vyžadovat, aby se pokus o přístup k vybraným cloudovým aplikacím uskutečnil ze schválené klientské aplikace. Tyto schválené klientské aplikace podporují [zásady ochrany aplikací Intune](/intune/app-protection-policy) nezávisle na jakémkoli řešení pro správu mobilních zařízení (MDM).

Aby bylo možné využít tento grant řízení podmíněného přístupu vyžaduje, aby zařízení se zaregistrovali ve službě Azure Active Directory, která vyžaduje použití aplikace broker. Zprostředkující aplikací může být buď Microsoft Authenticator pro zařízení s iOSem, nebo Portál společnosti Microsoft pro zařízení s Androidem. Pokud aplikace broker není nainstalována na zařízení, když se uživatel pokusí o ověření, uživatel získá přesměrovándo obchodu s aplikacemi k instalaci aplikace broker.

Toto nastavení platí pro následující aplikace pro iOS a Android:

- Ochrana informací Microsoft Azure
- Rezervace společnosti Microsoft
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Fakturace společnosti Microsoft
- Microsoft Kaizala
- Spouštěč Microsoftu
- Microsoft Office
- Microsoft Office Hub
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype for Business
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer
- Microsoft Whiteboard

**Poznámky**

- Schválené klientské aplikace podporují funkci správy mobilních aplikací Intune.
- **Požadavek na schválenou klientskou aplikaci:**
   - Podporuje pouze iOS a Android pro stav platformy zařízení.
   - K registraci zařízení je vyžadována aplikace zprostředkovatele. V systému iOS je makléřská aplikace Microsoft Authenticator a v systému Android je aplikace Intune Company Portal.
- Podmíněný přístup nemůže považovat Microsoft Edge v režimu InPrivate za schválenou klientskou aplikaci.

Příklady konfigurace najdete v článku [Postup: Vyžadovat schválené klientské aplikace pro přístup ke cloudovým aplikacím s podmíněným přístupem.](app-based-conditional-access.md)

### <a name="require-app-protection-policy"></a>Vyžadování zásad ochrany aplikací

V zásadách podmíněného přístupu můžete vyžadovat, aby v klientské aplikaci byly k dispozici [zásady ochrany aplikací Intune,](/intune/app-protection-policy) než bude k dispozici přístup k vybraným cloudovým aplikacím. 

Aby bylo možné využít tento grant řízení podmíněného přístupu vyžaduje, aby zařízení se zaregistrovali ve službě Azure Active Directory, která vyžaduje použití aplikace broker. Zprostředkující aplikací může být buď Microsoft Authenticator pro zařízení s iOSem, nebo Portál společnosti Microsoft pro zařízení s Androidem. Pokud aplikace broker není nainstalována na zařízení, když se uživatel pokusí o ověření, uživatel získá přesměrovándo obchodu s aplikacemi k instalaci aplikace broker.

Toto nastavení platí pro následující klientské aplikace:

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**Poznámky**

- Zásady ochrany aplikací aplikace podporují funkci správy mobilních aplikací Intune s ochranou zásad.
- **Požadavky na zásady ochrany aplikace Vyžadovat:**
    - Podporuje pouze iOS a Android pro stav platformy zařízení.
    - K registraci zařízení je vyžadována aplikace zprostředkovatele. V systému iOS je makléřská aplikace Microsoft Authenticator a v systému Android je aplikace Intune Company Portal.

Příklady konfigurace najdete v článku [Postup: Vyžadovat zásady ochrany aplikací a schválenou klientskou aplikaci pro přístup ke cloudovým aplikacím s podmíněným přístupem.](app-protection-based-conditional-access.md)

### <a name="terms-of-use"></a>Podmínky použití

Pokud vaše organizace vytvořila podmínky použití, mohou být v rámci ovládacích prvků grantu viditelné další možnosti. Tyto možnosti umožňují správcům vyžadovat potvrzení podmínek použití jako podmínku přístupu k prostředkům chráněným zásadami. Další informace o podmínkách použití najdete v článku [Podmínky použití služby Azure Active Directory](terms-of-use.md).

## <a name="next-steps"></a>Další kroky

- [Podmíněný přístup: Ovládací prvky relace](concept-conditional-access-session.md)

- [Podmíněné přístupové běžné zásady](concept-conditional-access-policy-common.md)

- [Režim pouze sestav](concept-conditional-access-report-only.md)
