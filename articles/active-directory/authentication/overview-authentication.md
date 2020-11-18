---
title: Přehled ověřování Azure Active Directory
description: Přečtěte si o různých metodách ověřování a funkcích zabezpečení pro přihlašování uživatelů pomocí Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: overview
ms.date: 07/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92ac495381f63e01d64c9a3d02777dca37ebb343
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837987"
---
# <a name="what-is-azure-active-directory-authentication"></a>Co je Azure Active Directory ověřování?

Jednou z hlavních funkcí platformy identity je ověření, nebo *ověření* přihlašovacích údajů, když se uživatel přihlásí k zařízení, aplikaci nebo službě. V Azure Active Directory (Azure AD) ověřování zahrnuje víc než jenom ověření uživatelského jména a hesla. K vylepšení zabezpečení a omezení nutnosti pomoci helpdesku zahrnuje ověřování Azure AD následující komponenty:

* Samoobslužné resetování hesla
* Multi-Factor Authentication Azure AD
* Hybridní integrace pro zápis změn hesel zpátky do místního prostředí
* Hybridní integrace, která vynutila zásady ochrany hesel pro místní prostředí
* Ověřování bez hesla

## <a name="improve-the-end-user-experience"></a>Zlepšení činnosti koncového uživatele

Azure AD pomáhá chránit identitu uživatele a zjednodušuje jejich přihlašování. Funkce, jako je Samoobslužné resetování hesla, umožňují uživatelům aktualizovat nebo měnit hesla pomocí webového prohlížeče z libovolného zařízení. Tato funkce je užitečná hlavně v případě, že uživatel zapomněl heslo nebo je jeho účet uzamčený. Bez čekání na technickou podporu nebo správce o poskytnutí podpory může uživatel odblokovat sebe sama a pokračovat v práci.

Azure AD Multi-Factor Authentication umožňuje uživatelům při přihlašování zvolit další formu ověřování, jako je například telefonní hovor nebo oznámení o mobilní aplikaci. Tato schopnost snižuje požadavek na jednu pevnou formu sekundárního ověřování, jako je hardwarový token. Pokud uživatel aktuálně nemá jednu formu dalšího ověřování, může zvolit jinou metodu a pokračovat v práci.

![Metody ověřování používané na přihlašovací obrazovce](media/concept-authentication-methods/overview-login.png)

Ověřování bez hesla uživateli umožňuje vytvořit a zapamatovat si zabezpečené heslo vůbec. Funkce, jako jsou klíče zabezpečení Windows Hello pro firmy nebo FIDO2, umožňují uživatelům přihlásit se k zařízení nebo aplikaci bez hesla. Tato schopnost může snížit složitost správy hesel v různých prostředích.

## <a name="self-service-password-reset"></a>Samoobslužné resetování hesla

Samoobslužné resetování hesla umožňuje uživatelům změnit nebo resetovat svoje heslo bez zásahu správce nebo helpdesku. Pokud je účet uživatele uzamčený nebo zapomněl heslo, může postupovat podle výzev ke zrušení odblokování a opětovnému získání práce. Tato možnost omezuje volání helpdesku a ztrátu produktivity, když se uživatel nemůže přihlásit ke svému zařízení nebo aplikaci.

Samoobslužné resetování hesla funguje v následujících scénářích:

* **Změna hesla –** když uživatel zná heslo, ale chce ho změnit na něco nového.
* **Resetování hesla –** když se uživatel nemůže přihlásit, například když zapomněl heslo a chcete resetovat heslo.
* **Odemčení účtu –** když se uživatel nemůže přihlásit, protože je jeho účet uzamčený a chcete odemknout svůj účet.

Když uživatel aktualizuje nebo obnoví heslo pomocí samoobslužného resetování hesla, může se toto heslo také zapsat zpátky do místního prostředí Active Directory. Zpětný zápis hesla zajistí, že uživatel může ihned použít aktualizované přihlašovací údaje s místními zařízeními a aplikacemi.

## <a name="azure-ad-multi-factor-authentication"></a>Multi-Factor Authentication Azure AD

Vícefaktorové ověřování je postup, ve kterém se uživateli při přihlašování zobrazí výzva k další formě prokázání totožnosti, třeba k zadání kódu na svém mobilním telefonu nebo k naskenování otisku prstu.

Pokud k ověření uživatele použijete jenom heslo, opustí pro útok nezabezpečený vektor. Pokud je heslo slabé nebo je jinde vystavené, je to skutečně uživatel, který se přihlašuje pomocí uživatelského jména a hesla, nebo se jedná o útočníka? Pokud požadujete druhou formu ověřování, zabezpečení se zvyšuje, protože tento přídavný faktor není něco, co by mohlo útočník snadno získat nebo duplikovat.

![Koncepční obraz různých forem Multi-Factor Authentication](./media/concept-mfa-howitworks/methods.png)

Služba Azure AD Multi-Factor Authentication funguje tak, že vyžaduje dvě nebo více následujících metod ověřování:

* Něco, co znáte, obvykle heslo.
* Něco, co máte, jako je například důvěryhodné zařízení, které není snadno duplikováno, jako je telefonní nebo hardwarový klíč.
* Něco, co jste biometrika jako otisk prstu nebo vzhled obličeje.

Uživatelé se můžou sami zaregistrovat pro Samoobslužné resetování hesla i pro Azure AD Multi-Factor Authentication v jednom kroku, aby se zjednodušila činnost na zprovoznění. Správci mohou definovat, jaké formuláře sekundárního ověřování lze použít. Azure AD Multi-Factor Authentication se může vyžadovat i v případě, že uživatel provede Samoobslužné resetování hesla pro další zabezpečení tohoto procesu.

## <a name="password-protection"></a>Ochrana hesel

Ve výchozím nastavení Azure AD blokuje slabá hesla, například *Heslo1*. Globální seznam zakázaných hesel je automaticky aktualizován a vynucen, který obsahuje známá slabá hesla. Pokud se uživatel služby Azure AD pokusí zadat heslo k jednomu z těchto slabých hesel, obdrží oznámení, že má zvolit bezpečnější heslo.

Pro zvýšení zabezpečení můžete definovat vlastní zásady ochrany heslem. Tyto zásady můžou používat filtry k blokování jakékoli variace hesla, které obsahuje název, jako je například *Contoso* nebo umístění jako *Londýn*.

Pro hybridní zabezpečení můžete integrovat ochranu hesel Azure AD pomocí místního prostředí Active Directory. Komponenta nainstalovaná v prostředí on-Prem přijímá globální seznam zakázaných hesel a vlastní zásady ochrany heslem z Azure AD a řadiče domény je používají ke zpracování událostí změny hesla. Tento hybridní přístup zajišťuje, že bez ohledu na to, jak nebo kde uživatel změní své přihlašovací údaje, vynutili používání silných hesel.

## <a name="passwordless-authentication"></a>Ověřování bez hesla

Koncovým cílem mnoha prostředí je odebrat použití hesel jako součást přihlašovacích událostí. Funkce, jako je Azure heslem Protection nebo Azure AD Multi-Factor Authentication pomáhá zlepšit zabezpečení, ale uživatelské jméno a heslo mají slabý tvar ověřování, který může být vystavený nebo útok hrubou silou.

![Zabezpečení a pohodlí v rámci procesu ověřování, který vede k bez hesla](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

Když se přihlásíte pomocí metody bez hesla, přihlašovací údaje se poskytují pomocí metod, jako je biometrika s Windows Hello pro firmy, nebo pomocí bezpečnostního klíče FIDO2. Tyto metody ověřování nemůže útočník snadno duplikovat.

Azure AD poskytuje možnosti nativně ověřit pomocí metod nezaložených na neheslech a zjednodušit tak přihlašování uživatelů a snížit riziko útoků.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak začít, najdete v [kurzu samoobslužného resetování hesla (SSPR)][tutorial-sspr] a [Azure AD Multi-Factor Authentication][tutorial-azure-mfa].

Další informace o konceptech samoobslužného resetování hesla najdete v tématu [Jak funguje Samoobslužné resetování hesla služby Azure AD][concept-sspr].

Další informace o konceptech služby Multi-Factor Authentication najdete v tématu [Jak funguje Azure AD Multi-Factor Authentication][concept-mfa].

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
