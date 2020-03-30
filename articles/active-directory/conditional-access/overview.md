---
title: Co je podmíněný přístup ve službě Azure Active Directory?
description: Zjistěte, jak je podmíněný přístup jádrem nové řídicí roviny řízené identitou.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240909"
---
# <a name="what-is-conditional-access"></a>Co je podmíněný přístup?

Moderní bezpečnostní obvod nyní přesahuje síť organizace a zahrnuje identitu uživatele a zařízení. Organizace mohou tyto signály identity využívat jako součást svých rozhodnutí o řízení přístupu. 

Podmíněný přístup je nástroj používaný službou Azure Active Directory k sbližování signálů, rozhodování a vynucování zásad organizace. Podmíněný přístup je jádrem nové řídicí roviny řízené identitou.

![Koncepční podmíněný signál plus rozhodnutí o výkonu rozhodnutí](./media/overview/conditional-access-signal-decision-enforcement.png)

Zásady podmíněného přístupu v nejjednodušší jsou příkazy if-then, pokud uživatel chce získat přístup k prostředku, musí provést akci. Příklad: Mzdový manažer chce získat přístup k mzdové aplikaci a je povinen k přístupu k ní provést vícefaktorové ověřování.

Správci čelí dvěma hlavním cílům:

- Umožnit uživatelům být produktivní kdykoli a kdekoli
- Ochrana majetku organizace

Pomocí zásad podmíněného přístupu můžete v případě potřeby použít správné ovládací prvky přístupu, abyste udrželi vaši organizaci v bezpečí a zůstali mimo cestu uživatele, když je nepotřebujete.

![Tok procesu koncepčního podmíněného přístupu](./media/overview/conditional-access-overview-how-it-works.png)

Zásady podmíněného přístupu jsou vynuceny po dokončení ověřování prvního faktoru. Podmíněný přístup není určen jako první obranná linie organizace pro scénáře, jako jsou útoky dos (denial of service), ale může používat signály z těchto událostí k určení přístupu.

## <a name="common-signals"></a>Běžné signály

Běžné signály, které podmíněný přístup může vzít v úvahu při rozhodování o zásadách, zahrnují následující signály:

- Členství uživatele nebo skupiny
   - Zásady mohou být zaměřeny na konkrétní uživatele a skupiny, které správcům poskytují jemně odstupňovanou kontrolu nad přístupem.
- Informace o umístění IP
   - Organizace mohou vytvářet důvěryhodné rozsahy adres IP, které lze použít při rozhodování o zásadách. 
   - Správci mohou určit rozsahy IP adres celé země, ze kterých mají být blokovat nebo povolovat provoz.
- Zařízení
   - Uživatelé se zařízeními konkrétních platforem nebo označeni určitým stavem lze použít při vynucování zásad podmíněného přístupu.
- Aplikace
   - Uživatelé, kteří se pokoušejí o přístup k určitým aplikacím, mohou aktivovat různé zásady podmíněného přístupu. 
- Detekce rizik v reálném čase a vypočtená detekce rizik
   - Integrace signálů s Azure AD Identity Protection umožňuje zásady podmíněného přístupu k identifikaci rizikového chování při přihlašování. Zásady pak mohou přinutit uživatele, aby provedli změny hesla nebo vícefaktorové ověřování, aby se snížila jejich úroveň rizika, nebo může být zablokován přístup, dokud správce neprovede ruční akci.
- Zabezpečení aplikací Microsoft Cloud (MCAS)
   - Umožňuje sledování a řízení uživatelských aplikací a relací v reálném čase, což zvyšuje viditelnost a kontrolu nad přístupem a aktivitami prováděnými v cloudovém prostředí.

## <a name="common-decisions"></a>Společná rozhodnutí

- Blokovat přístup
   - Nejrestriktivnější rozhodnutí
- Udělení přístupu
   - Nejméně omezující rozhodnutí, může stále vyžadovat jednu nebo více z následujících možností:
      - Vyžadovat vícefaktorové ověřování
      - Vyžadovat, aby zařízení bylo označeno jako vyhovující
      - Vyžadovat hybridní zařízení azure ad připojeno
      - Vyžadovat schválenou klientskou aplikaci
      - Vyžadovat zásady ochrany aplikací (preview)

## <a name="commonly-applied-policies"></a>Běžně používané zásady

Mnoho organizací má společné obavy o přístup, které zásady podmíněného přístupu mohou pomoci například:

- Vyžadování vícefaktorového ověřování pro uživatele s rolemi pro správu
- Vyžadování vícefaktorového ověřování pro úlohy správy Azure
- Blokování přihlášení pro uživatele, kteří se pokoušejí používat starší ověřovací protokoly
- Vyžadování důvěryhodných umístění pro registraci azure vícefaktorového ověřování
- Blokování nebo udělení přístupu z konkrétních míst
- Blokování rizikového chování při hlásek
- Vyžadování zařízení spravovaných organizací pro konkrétní aplikace

## <a name="customer-case-studies"></a>Zákaznické případové studie

Zjistěte, jak ostatní organizace používají podmíněný přístup Azure AD k definování a implementaci rozhodnutí o automatizovaném řízení přístupu. Následující doporučené příběhy ukazují, jak jsou tyto potřeby zákazníků uspokojovány.

* [Wipro podporuje mobilní produktivitu pomocí cloudových bezpečnostních nástrojů Microsoftu, které zlepšují zapojení zákazníků.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Zásady podmíněného přístupu ve službě Azure AD umožnily společnosti sdílet dokumenty, prostředky a aplikace s důvěryhodnými externími entitami---, kteří můžou používat vlastní přihlašovací údaje--- při zachování kontroly nad vlastními podnikovými daty.
* [Aramex delivery limited - Globální logistická a dopravní společnost vytváří kancelář propojenou s cloudem s řešením pro správu identit a přístupu](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en). Zajištění bezpečného přístupu bylo obzvláště obtížné u vzdálených zaměstnanců společnosti Aramex. Společnost nyní používá podmíněný přístup, aby těmto vzdáleným zaměstnancům přístup ke svým aplikacím SaaS přistupovala mimo síť. Pravidlo podmíněného přístupu rozhodne, zda vynucuje vícefaktorové ověřování, což poskytuje správnému přístupu pouze správným lidem.

## <a name="license-requirements"></a>Licenční požadavky

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

Zákazníci s [licencemi Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) mají také přístup k funkcím podmíněného přístupu. 

## <a name="next-steps"></a>Další kroky

[Vytváření zásad podmíněného přístupu kousek po kousku](concept-conditional-access-policies.md)

Informace o implementaci podmíněného přístupu ve vašem prostředí najdete v [tématu Plánování nasazení podmíněného přístupu ve službě Azure Active Directory](plan-conditional-access.md).

[Další informace o ochraně identity](../identity-protection/overview-v2.md)

[Další informace o zabezpečení aplikací Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security)

[Další informace o Microsoft Intune](/intune/index)