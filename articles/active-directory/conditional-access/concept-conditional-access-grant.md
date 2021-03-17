---
title: Udělení ovládacích prvků v zásadách podmíněného přístupu – Azure Active Directory
description: Co poskytuje řízení v zásadách podmíněného přístupu Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f0a84af0c7a4105327405cfb809f5101ab42931
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938387"
---
# <a name="conditional-access-grant"></a>Podmíněný přístup: udělení

V rámci zásad podmíněného přístupu může správce využít řízení přístupu k udělení nebo blokování přístupu k prostředkům.

![Zásada podmíněného přístupu s uděleným ovládacím prvkem, který vyžaduje vícefaktorové ověřování](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Blokování přístupu

Blok vezme v úvahu všechna přiřazení a zabraňuje přístupu na základě konfigurace zásad podmíněného přístupu.

Blok je výkonný ovládací prvek, který by měl být wielded s odpovídajícím vědomím. Zásady s příkazy Block mohou mít nezamýšlené vedlejší účinky. Před tím, než povolíte škálování, jsou správné testování a ověřování důležité. Při provádění změn by správci měli používat nástroje, jako je [režim pouze pro vytváření sestav podmíněného přístupu](concept-conditional-access-report-only.md) a [Nástroj what if v podmíněném přístupu](what-if-tool.md) .

## <a name="grant-access"></a>Udělení přístupu

Správci se můžou rozhodnout vyhovět jednomu nebo více ovládacím prvkům při udělení přístupu. Tyto ovládací prvky zahrnují následující možnosti: 

- [Vyžadovat Multi-Factor Authentication (Azure AD Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)
- [Vyžadovat, aby zařízení bylo označené jako vyhovující (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Vyžadovat zařízení připojené k hybridní službě Azure AD](../devices/concept-azure-ad-join-hybrid.md)
- [Vyžadovat klientskou aplikaci schválenou](app-based-conditional-access.md)
- [Vyžadování zásad ochrany aplikací](app-protection-based-conditional-access.md)
- [Vyžadovat změnu hesla](#require-password-change)

Když se správci rozhodnou tyto možnosti kombinovat, můžou zvolit následující metody:

- Vyžadovat všechny vybrané ovládací prvky (ovládací prvek **a** ovládací prvek)
- Vyžadovat jeden z vybraných ovládacích prvků (ovládací prvek **nebo** ovládací prvek)

Ve výchozím nastavení podmíněný přístup vyžaduje všechny vybrané ovládací prvky.

### <a name="require-multi-factor-authentication"></a>Vyžadovat Multi-Factor Authentication

Zaškrtnutí tohoto políčka bude vyžadovat, aby uživatelé prováděli Multi-Factor Authentication služby Azure AD. Další informace o nasazení služby Azure AD Multi-Factor Authentication najdete v článku [Plánování nasazení cloudových Multi-Factor Authentication Azure AD](../authentication/howto-mfa-getstarted.md).

### <a name="require-device-to-be-marked-as-compliant"></a>Vyžadovat, aby zařízení bylo označené jako vyhovující

Organizace, které nasadily Microsoft Intune, můžou použít informace vrácené ze svých zařízení k identifikaci zařízení, která splňují konkrétní požadavky na dodržování předpisů. Tyto informace o dodržování zásad se předávají z Intune do Azure AD, kde podmíněný přístup může učinit rozhodnutí udělit nebo blokovat přístup k prostředkům. Další informace o zásadách dodržování předpisů najdete v článku [Nastavení pravidel pro zařízení, která umožňují přístup k prostředkům ve vaší organizaci pomocí Intune](/intune/protect/device-compliance-get-started).

Zařízení může být označeno jako kompatibilní s Intune (pro libovolný operační systém zařízení) nebo systémem MDM jiného výrobce pro zařízení s Windows 10. Jamf pro je jediný podporovaný systém MDM třetí strany. Další informace o integraci najdete v článku integrace [Jamf pro s Intune pro dodržování předpisů](/intune/protect/conditional-access-integrate-jamf).

Zařízení musí být zaregistrovaná ve službě Azure AD, aby je bylo možné označit jako vyhovující. Další informace o registraci zařízení najdete v článku [co je identita zařízení](../devices/overview.md).

### <a name="require-hybrid-azure-ad-joined-device"></a>Vyžadovat zařízení připojené k hybridní službě Azure AD

Organizace se můžou rozhodnout používat identitu zařízení jako součást zásad podmíněného přístupu. Organizace můžou vyžadovat, aby se zařízení připojila k hybridní službě Azure AD pomocí tohoto zaškrtávacího políčka. Další informace o identitách zařízení najdete v článku [co je identita zařízení?](../devices/overview.md).

Při použití [toku OAuth pro kód zařízení](../develop/v2-oauth2-device-code.md)se nepodporuje stav vyžadovat řízení udělení spravovaného zařízení nebo stav zařízení. Důvodem je to, že zařízení, které provádí ověřování, nemůže poskytnout stav zařízení zařízení, které poskytuje kód, a stav zařízení je v tokenu zamčený k zařízení, které provádí ověřování. Místo toho použijte ovládací prvek vyžadovat službu Multi-Factor Authentication.

### <a name="require-approved-client-app"></a>Vyžadovat klientskou aplikaci schválenou

Organizace můžou vyžadovat, aby se pokus o přístup k vybraným cloudovým aplikacím nastavil ze schválené klientské aplikace. Tyto schválené klientské aplikace podporují [Zásady ochrany aplikací Intune](/intune/app-protection-policy) nezávisle na řešení správy mobilních zařízení (MDM).

Aby bylo možné tento ovládací prvek udělit, podmíněný přístup vyžaduje, aby zařízení bylo zaregistrované v Azure Active Directory, které vyžaduje použití aplikace zprostředkovatele. Aplikace zprostředkovatele může být Microsoft Authenticator pro iOS nebo buď Microsoft Authenticator nebo portál společnosti Microsoft pro zařízení s Androidem. Pokud při pokusu uživatele o ověření není v zařízení nainstalovaná aplikace zprostředkovatele, uživatel se přesměruje do příslušného obchodu s aplikacemi, aby se nainstalovala požadovaná aplikace zprostředkovatele.

Toto nastavení platí pro následující aplikace pro iOS a Android:

- Microsoft Azure Information Protection
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Power Automate
- Fakturace Microsoftu
- Microsoft Kaizala
- Spouštěč Microsoftu
- Microsoft Office
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
- Aplikace Microsoft Visio
- Microsoft Word
- Yammer Microsoftu
- Microsoft tabule
- Správce Microsoft 365

**Poznámky**

- Schválené klientské aplikace podporují funkci správy mobilních aplikací Intune.
- Požadavek na **vyžadování schválené aplikace klienta** :
   - Podporuje jenom podmínku platformy iOS a Android pro zařízení.
   - K registraci zařízení se vyžaduje aplikace zprostředkovatele. Aplikace zprostředkovatele může být Microsoft Authenticator pro iOS nebo buď Microsoft Authenticator nebo portál společnosti Microsoft pro zařízení s Androidem.
- Podmíněný přístup nemůže vzít v úvahu schválenou klientskou aplikaci Microsoft Edge v režimu InPrivate.
- Pomocí služby Azure Proxy aplikací služby AD povolíte, aby se mobilní aplikace Power BI připojovala k místním Server sestav Power BI není podporovaná pro zásady podmíněného přístupu, které vyžadují aplikaci Microsoft Power BI jako schválenou klientskou aplikaci.

Informace najdete v článku [How to: vyžadovat schválené klientské aplikace pro přístup k cloudovým aplikacím s podmíněným přístupem](app-based-conditional-access.md) pro příklady konfigurace.

### <a name="require-app-protection-policy"></a>Vyžadování zásad ochrany aplikací

V rámci zásad podmíněného přístupu můžete vyžadovat, aby v klientské aplikaci existovaly [Zásady ochrany aplikací Intune](/intune/app-protection-policy) , aby byl přístup k vybraným cloudovým aplikacím dostupný. 

Aby bylo možné tento ovládací prvek udělit, podmíněný přístup vyžaduje, aby zařízení bylo zaregistrované v Azure Active Directory, které vyžaduje použití aplikace zprostředkovatele. Zprostředkující aplikací může být buď Microsoft Authenticator pro zařízení s iOSem, nebo Portál společnosti Microsoft pro zařízení s Androidem. Pokud při pokusu uživatele o ověření není v zařízení nainstalovaná aplikace zprostředkovatele, uživatel se přesměruje do obchodu s aplikacemi a nainstaluje aplikaci zprostředkovatele.

Aby se aplikace **Intune SDK** s implementací **zásad zabezpečení** implementovala a splňovaly některé další požadavky na podporu tohoto nastavení, musí být aplikace. Vývojáři implementující aplikace pomocí sady Intune SDK můžou najít další informace v dokumentaci k sadě SDK týkající se těchto požadavků.

Následující klientské aplikace byly potvrzeny pro podporu tohoto nastavení:

- Microsoft Cortana
- Microsoft Edge
- Microsoft Excel
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Word
- Víceřádkové pro Intune
- Devět e-mailů & kalendář

> [!NOTE]
> Microsoft teams, Microsoft Kaizala, Microsoft Skype pro firmy a Microsoft Visio nepodporují udělení **zásad ochrany aplikací** . Pokud požadujete, aby tyto aplikace fungovaly, použijte výhradně udělení oprávnění **vyžadovat schválené aplikace** . Použití klauzule OR mezi dvěma granty nebude pro tyto tři aplikace fungovat.

**Poznámky**

- Aplikace pro zásady ochrany aplikací podporují funkci správy mobilních aplikací Intune s ochranou zásad.
- Požadavky na **zásady pro vyžadování zásad ochrany aplikací** :
    - Podporuje jenom podmínku platformy iOS a Android pro zařízení.
    - K registraci zařízení se vyžaduje aplikace zprostředkovatele. V systému iOS je aplikace zprostředkovatele Microsoft Authenticator a v Androidu je Portál společnosti Intune aplikace.

Informace najdete v článku [Postupy: vyžadování zásad ochrany aplikací a schválené klientské aplikace pro přístup k cloudovým aplikacím pomocí podmíněného přístupu](app-protection-based-conditional-access.md) pro příklady konfigurace.

### <a name="require-password-change"></a>Vyžadovat změnu hesla 

Když se zjistí riziko uživatele, použije se podmínky zásad rizika pro uživatele, kteří můžou správci bezpečně měnit heslo pomocí samoobslužného resetování hesla služby Azure AD. Pokud se zjistí riziko pro uživatele, můžou uživatelé provést Samoobslužné resetování hesla, aby mohli správci zabránit zbytečným hluku pro správce. 

Když se uživateli zobrazí výzva ke změně hesla, bude se nejdřív vyžadovat ověření Multi-Factor Authentication. Budete chtít zajistit, aby všichni vaši uživatelé zaregistrovali službu Multi-Factor Authentication, takže budou připravené na případ, že se pro svůj účet zjistí riziko.  

> [!WARNING]
> Před aktivací zásad rizika uživatele musí být uživatelé předtím registrováni pro Samoobslužné resetování hesla. 

Při konfiguraci zásady pomocí ovládacího prvku změny hesla existuje několik omezení.  

1. Zásada musí být přiřazená ke všem cloudovým aplikacím. Tím zabráníte útočníkovi v použití jiné aplikace ke změně uživatelského hesla a resetování rizika účtu, a to pouhým přihlášením do jiné aplikace. 
1. Vyžadování změny hesla nelze použít s jinými ovládacími prvky, jako je třeba vyhovující zařízení.  
1. Ovládací prvek změny hesla se dá použít jenom s podmínkou přiřazení uživatele a skupiny, podmínky přiřazení cloudové aplikace (která musí být nastavená na všechny) a na rizikové podmínky uživatele. 

### <a name="terms-of-use"></a>Podmínky použití

Pokud vaše organizace vytvořila podmínek použití, můžou být v rámci grant Controls viditelné další možnosti. Tyto možnosti umožňují správcům vyžadovat potvrzení podmínek použití jako podmínku přístupu k prostředkům chráněným pomocí zásad. Další informace o podmínek použití najdete v článku [Azure Active Directory podmínkami použití](terms-of-use.md).

## <a name="next-steps"></a>Další kroky

- [Podmíněný přístup: ovládací prvky relace](concept-conditional-access-session.md)

- [Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

- [Režim pouze sestav](concept-conditional-access-report-only.md)
