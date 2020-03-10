---
title: Co je podmíněný přístup v Azure Active Directory?
description: Přečtěte si, jak podmíněný přístup je srdcem nové roviny ovládacího prvku řízená identitami.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b044a4fd4e29bfe35abff7a4b36e5bae783328b
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671871"
---
# <a name="what-is-conditional-access"></a>Co je podmíněný přístup?

Moderní hraniční zabezpečení teď přesahuje síť organizace, aby zahrnovala identitu uživatelů a zařízení. Organizace můžou tyto signály identity využít jako součást jejich rozhodnutí o řízení přístupu. 

Podmíněný přístup je nástroj používaný Azure Active Directory k spojování signálů, k rozhodování a vymáhání zásad organizace. Podmíněný přístup je srdcem nové roviny ovládacího prvku na základě identity.

![Koncepční podmíněný signál plus rozhodnutí pro získání vynucování](./media/overview/conditional-access-signal-decision-enforcement.png)

Zásady podmíněného přístupu jsou v nejjednodušším případě příkazy if a then, pokud chce uživatel získat přístup k prostředku, musí dokončit akci. Příklad: správce mezd chce získat přístup k aplikaci mzdy a vyžaduje k přístupu k němu službu Multi-Factor Authentication.

Správcům se čelí dva primární cíle:

- Umožnit uživatelům být produktivní kdykoli a kdekoli
- Ochrana prostředků organizace

Pomocí zásad podmíněného přístupu můžete použít správné řízení přístupu, pokud je to nutné, aby vaše organizace byla zabezpečená a zůstala v případě potřeby zachována uživateli.

![Tok procesu koncepčního podmíněného přístupu](./media/overview/conditional-access-overview-how-it-works.png)

Zásady podmíněného přístupu se vynutily po dokončení prvního faktoru ověřování. Podmíněný přístup není určený jako první linie obrany organizace pro scénáře, jako jsou útoky DoS (Denial-of-Service), ale může k určení přístupu použít signály z těchto událostí.

## <a name="common-signals"></a>Běžné signály

Běžné signály, které může podmíněný přístup vzít v úvahu při rozhodování o zásadách, zahrnuje tyto signály:

- Členství uživatele nebo skupiny
   - Zásady je možné cílit na konkrétní uživatele a skupiny, které správcům poskytují jemně odstupňovanou kontrolu nad přístupem.
- Informace o umístění IP adresy
   - Organizace můžou vytvářet důvěryhodné rozsahy IP adres, které se dají použít při rozhodování o zásadách. 
   - Správci můžou určit rozsahy IP adres celé země pro blokování nebo povolení provozu.
- Zařízení
   - Uživatelé se zařízeními konkrétních platforem nebo označenými určitým stavem lze použít při vynucování zásad podmíněného přístupu.
- Aplikace
   - Uživatelé, kteří se pokoušejí o přístup ke konkrétním aplikacím, můžou aktivovat různé zásady podmíněného přístupu. 
- Zjišťování rizik v reálném čase a vypočtené riziko
   - Signály pro integraci s Azure AD Identity Protection umožňují zásadám podmíněného přístupu identifikovat rizikové chování při přihlašování. Zásady potom můžou vynutit, aby uživatelé prováděli změny hesla nebo službu Multi-Factor Authentication, aby omezili jejich úroveň rizika nebo aby měli přístup blokovaný, dokud správce neprovede ruční akci.
- Microsoft Cloud App Security (MCAS)
   - Umožňuje monitorovat a kontrolovat přístup k uživatelským aplikacím v reálném čase, což zvyšuje viditelnost a kontrolu nad přístupem a aktivitami provedenými v rámci vašeho cloudového prostředí.

## <a name="common-decisions"></a>Společná rozhodnutí

- Blokovat přístup
   - Nejvíce omezující rozhodnutí
- Udělení přístupu
   - Minimální omezující rozhodnutí může stále vyžadovat jednu nebo více z následujících možností:
      - Vyžadovat Multi-Factor Authentication
      - Vyžadovat, aby zařízení bylo označené jako vyhovující
      - Vyžadovat zařízení připojené k hybridní službě Azure AD
      - Vyžaduje se klientem schválená aplikace.
      - Vyžadovat zásady ochrany aplikací (Preview)

## <a name="commonly-applied-policies"></a>Běžně používané zásady

Mnoho organizací má společný přístup, ke kterým může pomáhat zásada podmíněného přístupu, například:

- Vyžadování služby Multi-Factor Authentication pro uživatele s administrativními rolemi
- Vyžadování služby Multi-Factor Authentication pro úlohy správy Azure
- Blokování přihlášení pro uživatele, kteří se pokoušejí používat starší protokoly ověřování
- Vyžadování důvěryhodných umístění pro registraci služby Azure Multi-Factor Authentication
- Blokování a udělení přístupu z určitých umístění
- Blokování rizikových přihlašovacích chování
- Vyžadování zařízení spravovaných organizací pro konkrétní aplikace

## <a name="customer-case-studies"></a>Zákaznické případové studie

Zjistěte, jak jiné organizace používají podmíněný přístup Azure AD k definování a implementaci automatického rozhodování o řízení přístupu. Následující doporučené příběhy ukazují, jak se tyto požadavky zákazníků splní.

* [Společnost Wipro zabezpečí mobilní produktivitu pomocí nástrojů Microsoft Cloud Security Tools a vylepšuje zapojení zákazníků.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Zásady podmíněného přístupu ve službě Azure AD povolily společnosti sdílet dokumenty, prostředky a aplikace s důvěryhodnými externími entitami---, kdo může používat vlastní přihlašovací údaje---a přitom zachovat kontrolu nad svými vlastními podnikovými daty.
* [Aramex Delivery s omezeným přístupem – globální logistika a dopravní společnost vytváří Office propojené s cloudem a řešení pro správu identit a přístupu](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en). Zajištění bezpečného přístupu bylo obzvláště obtížné u vzdálených zaměstnanců Aramex. Společnost teď používá podmíněný přístup, aby tyto vzdálené zaměstnance měli přístup k jejich SaaS aplikacím mimo síť. Pravidlo podmíněného přístupu určí, jestli se má vyhodnotit Multi-Factor Authentication a že jenom ta, která mají správný přístup.

## <a name="license-requirements"></a>Licenční požadavky

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

Zákazníci s [licencemi Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) mají taky přístup k funkcím podmíněného přístupu. 

## <a name="next-steps"></a>Další kroky

[Vytvoření zásad podmíněného přístupu podle kusu](concept-conditional-access-policies.md)

Informace o tom, jak implementovat podmíněný přístup ve vašem prostředí, najdete v tématu [Plánování nasazení podmíněného přístupu v Azure Active Directory](plan-conditional-access.md).

[Další informace o Identity Protection](../identity-protection/overview-v2.md)

[Informace o Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security)

[Informace o Microsoft Intune](/intune/index)