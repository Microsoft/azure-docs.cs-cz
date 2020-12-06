---
title: Přehled služby Azure AD Multi-Factor Authentication
description: Přečtěte si, jak Multi-Factor Authentication Azure AD pomáhá chránit přístup k datům a aplikacím a současně plnit požadavky uživatelů na jednoduchý proces přihlašování.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cc5227cb4b5de02cba65a60dd469da93d3767e5
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744051"
---
# <a name="how-it-works-azure-ad-multi-factor-authentication"></a>Jak to funguje: Multi-Factor Authentication Azure AD

Vícefaktorové ověřování je postup, ve kterém se uživateli při přihlašování zobrazí výzva k další formě prokázání totožnosti, třeba k zadání kódu na svém mobilním telefonu nebo k naskenování otisku prstu.

Pokud k ověření uživatele použijete jenom heslo, opustí pro útok nezabezpečený vektor. Pokud je heslo slabé nebo je jinde vystavené, je to skutečně uživatel, který se přihlašuje pomocí uživatelského jména a hesla, nebo se jedná o útočníka? Pokud požadujete druhou formu ověřování, zabezpečení se zvyšuje, protože tento přídavný faktor není něco, co by mohlo útočník snadno získat nebo duplikovat.

![Koncepční obraz různých forem Multi-Factor Authentication](./media/concept-mfa-howitworks/methods.png)

Služba Azure AD Multi-Factor Authentication funguje tak, že vyžaduje dvě nebo více následujících metod ověřování:

* Něco, co znáte, obvykle heslo.
* Něco, co máte, jako je například důvěryhodné zařízení, které není snadno duplikováno, jako je telefonní nebo hardwarový klíč.
* Něco, co jste biometrika jako otisk prstu nebo vzhled obličeje.

Uživatelé se můžou sami zaregistrovat pro Samoobslužné resetování hesla i pro Azure AD Multi-Factor Authentication v jednom kroku, aby se zjednodušila činnost na zprovoznění. Správci mohou definovat, jaké formuláře sekundárního ověřování lze použít. Azure AD Multi-Factor Authentication se může vyžadovat i v případě, že uživatel provede Samoobslužné resetování hesla pro další zabezpečení tohoto procesu.

![Metody ověřování používané na přihlašovací obrazovce](media/concept-authentication-methods/overview-login.png)

Azure AD Multi-Factor Authentication pomáhá chránit přístup k datům a aplikacím a současně zachovává jednoduchost uživatelů. Poskytuje dodatečné zabezpečení tím, že vyžaduje druhou formu ověřování a zajišťuje silné ověřování prostřednictvím řady snadno použitelných [metod ověřování](concept-authentication-methods.md). Na základě rozhodnutí týkajících se konfigurace, která správce provede, se uživatelé můžou nebo nemusí vyvolávat na MFA.

Vaše aplikace nebo služby nemusí dělat žádné změny pro použití Multi-Factor Authentication Azure AD. Výzvy k ověření jsou součástí události přihlášení služby Azure AD, která automaticky vyžádá a zpracuje výzvu MFA v případě potřeby.

## <a name="available-verification-methods"></a>Dostupné metody ověření

Když se uživatel přihlásí k aplikaci nebo službě a zobrazí se výzva MFA, může zvolit jednu z registrovaných forem dodatečného ověření. Správce může vyžadovat registraci těchto metod ověřování Azure AD Multi-Factor Authentication, nebo může uživatel přistupovat ke svému vlastnímu [profilu](https://myprofile.microsoft.com) , aby mohl upravovat nebo přidávat metody ověřování.

S Azure AD Multi-Factor Authentication můžete použít následující další formy ověřování:

* Aplikace Microsoft Authenticator
* Hardwarový token OATH
* SMS
* Hlasový hovor

## <a name="how-to-enable-and-use-azure-ad-multi-factor-authentication"></a>Jak povolit a používat Multi-Factor Authentication Azure AD

U uživatelů a skupin se dá povolit Multi-Factor Authentication Azure AD, aby se během přihlašovací události zobrazila výzva k dodatečnému ověření. [Výchozí nastavení zabezpečení](../fundamentals/concept-fundamentals-security-defaults.md) jsou dostupná pro všechny klienty Azure AD, aby bylo možné rychle povolit používání aplikace Microsoft Authenticator pro všechny uživatele.

Pro podrobnější ovládací prvky lze pomocí zásad [podmíněného přístupu](../conditional-access/overview.md) definovat události nebo aplikace, které vyžadují MFA. Tyto zásady mohou umožňovat běžné přihlašování, když se uživatel nachází v podnikové síti nebo registrovaném zařízení, ale při vzdáleném nebo osobním zařízení vyzve k zadání dalších faktorů ověřování.

![Diagram s přehledem toho, jak podmíněný přístup funguje k zabezpečení procesu přihlašování](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>Další kroky

Další informace o licencování najdete v tématu [funkce a licence pro Azure AD Multi-Factor Authentication](concept-mfa-licensing.md).

Pokud chcete zobrazit MFA v akci, povolte v následujícím kurzu Multi-Factor Authentication služby Azure AD pro skupinu testovacích uživatelů:

> [!div class="nextstepaction"]
> [Povolení vícefaktorového ověřování Azure AD](./tutorial-enable-azure-mfa.md)