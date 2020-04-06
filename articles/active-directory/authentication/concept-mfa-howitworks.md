---
title: Přehled azure multifaktorového ověřování
description: Zjistěte, jak Azure Multi-Factor Authentication pomáhá chránit přístup k datům a aplikacím a zároveň uspokojuje požadavky uživatelů na jednoduchý proces přihlášení.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c50232abd12c8c0390409bd7bf72833b4f153e02
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667366"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Jak to funguje: Azure Multi-Factor Authentication

Vícefaktorové ověřování je proces, při kterém je uživatel během procesu přihlášení vyzván k zadání další formy identifikace, například k zadání kódu na svém mobilním telefonu nebo k provedení skenování otisků prstů.

Pokud používáte heslo pouze k ověření uživatele, ponechá nezabezpečený vektor pro útok. Pokud je heslo slabé nebo bylo vystaveno jinde, je to opravdu uživatel, který se přihlašuje pomocí uživatelského jména a hesla, nebo je to útočník? Pokud požadujete druhou formu ověřování, zabezpečení se zvýší, protože tento další faktor není něco, co je pro útočníka snadné získat nebo duplikovat.

![Koncepční obraz různých forem vícefaktorové autentizace](./media/concept-mfa-howitworks/methods.png)

Azure Multi-Factor Authentication funguje tak, že vyžaduje dvě nebo více z následujících metod ověřování:

* Něco, co znáte, obvykle heslo.
* Něco, co máte, například důvěryhodné zařízení, které není snadno duplikováno, například telefon nebo hardwarový klíč.
* Něco, co jste - biometrie jako otisk prstu nebo skenování obličeje.

Uživatelé se mohou zaregistrovat pro samoobslužné resetování hesla a azure multifaktorové ověřování v jednom kroku zjednodušit možnosti nástupu na palubu. Správci mohou definovat, jaké formy sekundárního ověřování lze použít. Azure Multi-Factor Authentication může být také vyžadováno, když uživatelé provádějí samoobslužné resetování hesla k dalšímu zabezpečení tohoto procesu.

![Metody ověřování používané na přihlašovací obrazovce](media/concept-authentication-methods/overview-login.png)

Azure Multi-Factor Authentication pomáhá chránit přístup k datům a aplikacím při zachování jednoduchosti pro uživatele. Poskytuje další zabezpečení tím, že vyžaduje druhou formu ověřování a poskytuje silné ověřování prostřednictvím řady snadno použitelných [metod ověřování](concept-authentication-methods.md). Uživatelé mohou nebo nemusí být napadena pro vícefaktorové řízení na základě rozhodnutí o konfiguraci, které provede správce.

Vaše aplikace nebo služby není nutné provádět žádné změny používat Azure Multi-Factor Authentication. Výzvy k ověření jsou součástí události přihlášení služby Azure AD, která automaticky požaduje a zpracovává výzvu mfa v případě potřeby.

## <a name="available-verification-methods"></a>Dostupné metody ověřování

Když se uživatel přihlásí k aplikaci nebo službě a obdrží výzvu vícefaktorové ověřování, může si vybrat z jedné ze svých registrovaných forem dodatečného ověření. Správce může vyžadovat registraci těchto metod ověřování vícefaktorovým ověřováním Azure nebo uživatel může přistupovat ke svému [vlastnímu profilu](https://myprofile.microsoft.com) a upravovat nebo přidávat metody ověřování.

S azure multifaktorovým ověřováním lze použít následující další formy ověřování:

* Aplikace Microsoft Authenticator
* Token hardwaru OATH
* SMS
* Hlasový hovor

## <a name="how-to-enable-and-use-azure-multi-factor-authentication"></a>Jak povolit a používat azure multi-factor authentication

Uživatelům a skupinám lze povolit vícefaktorové ověřování Azure a vyzvat k dalšímu ověření během události přihlášení. [Výchozí nastavení zabezpečení](../fundamentals/concept-fundamentals-security-defaults.md) jsou k dispozici pro všechny klienty Azure AD rychle povolit použití aplikace Microsoft Authenticator pro všechny uživatele.

Pro podrobnější ovládací prvky zásady [podmíněného přístupu](../conditional-access/overview.md) lze definovat události nebo aplikace, které vyžadují vícefaktorové povolení. Tyto zásady mohou povolit pravidelné události přihlášení, když je uživatel v podnikové síti nebo registrovaném zařízení, ale vyzve k zadání dalších ověřovacích faktorů, když je vzdálený nebo na osobním zařízení.

![Přehledový diagram fungování podmíněného přístupu k zabezpečení procesu přihlášení](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>Další kroky

Další informace o licencování najdete [v tématu Funkce a licence pro Azure Multi-Factor Authentication](concept-mfa-licensing.md).

Chcete-li zobrazit vícefaktorové ověřování v akci, povolte Azure Multi-Factor Authentication pro sadu testovacích uživatelů v následujícím kurzu:

> [!div class="nextstepaction"]
> [Povolení služby Azure Multi-Factor Authentication](tutorial-mfa-applications.md)
