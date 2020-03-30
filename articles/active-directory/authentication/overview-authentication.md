---
title: Přehled ověřování služby Azure Active Directory
description: Seznamte se s různými metodami ověřování a funkcemi zabezpečení pro přihlášení uživatelů pomocí služby Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: overview
ms.date: 01/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f4c2fa8488490561e8f11746e8e737718ee9f37
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76261258"
---
# <a name="what-is-azure-active-directory-authentication"></a>Co je ověřování služby Azure Active Directory?

Jednou z hlavních funkcí platformy identit je ověření nebo *ověření*pověření při přihlášení uživatele k zařízení, aplikaci nebo službě. Ve službě Azure Active Directory (Azure AD) ověřování zahrnuje víc než jen ověření uživatelského jména a hesla. Chcete-li zlepšit zabezpečení a snížit potřebu pomoci help desk, azure ad ověřování zahrnuje následující součásti:

* Samoobslužné resetování hesla
* Azure Multi-Factor Authentication
* Hybridní integrace pro zápis změn hesla zpět do místního prostředí
* Hybridní integrace pro vynucení zásad ochrany heslem pro místní prostředí
* Ověřování bez hesla

## <a name="improve-the-end-user-experience"></a>Zlepšete prostředí pro koncové uživatele

Azure AD pomáhá chránit identitu uživatele a zjednodušit jejich přihlašovací prostředí. Funkce, jako je samoobslužné resetování hesla, umožňují uživatelům aktualizovat nebo měnit hesla pomocí webového prohlížeče z libovolného zařízení. Tato funkce je užitečná zejména v případě, že uživatel zapomněl své heslo nebo je jeho účet uzamčen. Bez čekání na helpdesk nebo správce poskytovat podporu, uživatel může odblokovat sebe a pokračovat v práci.

Azure Multi-Factor Authentication umožňuje uživatelům zvolit další formu ověřování během přihlášení, jako je telefonní hovor nebo oznámení mobilní aplikace. Tato možnost snižuje požadavek na jednu pevnou formu sekundárního ověřování, jako je hardwarový token. Pokud uživatel nemá aktuálně jednu formu dalšího ověřování, může zvolit jinou metodu a pokračovat v práci.

![Metody ověřování používané na přihlašovací obrazovce](media/concept-authentication-methods/overview-login.png)

Ověřování bez hesla odstraňuje potřebu uživatele vytvořit a zapamatovat si bezpečné heslo vůbec. Funkce, jako jsou klíče zabezpečení Windows Hello pro firmy nebo FIDO2, umožňují uživatelům přihlásit se k zařízení nebo aplikaci bez hesla. Tato možnost může snížit složitost správy hesel v různých prostředích.

## <a name="self-service-password-reset"></a>Samoobslužné resetování hesla

Samoobslužné resetování hesla umožňuje uživatelům změnit nebo resetovat své heslo bez zapojení správce nebo technické podpory. Pokud je uživatelský účet uzamčen nebo zapomene heslo, může sledovat výzvy k odblokování a vrácení se do práce. Tato možnost snižuje volání technické podpory a ztrátu produktivity, když se uživatel nemůže přihlásit ke svému zařízení nebo aplikaci.

Samoobslužné resetování hesla funguje v následujících scénářích:

* **Změna hesla -** když uživatel zná své heslo, ale chce ho změnit na něco nového.
* **Resetování hesla –** když se uživatel nemůže přihlásit, například když zapomněl heslo a chce obnovit své heslo.
* **Odemknutí účtu –** když se uživatel nemůže přihlásit, protože je jeho účet uzamčen a chce svůj účet odemknout.

Pokud uživatel aktualizuje nebo resetuje své heslo pomocí samoobslužné resetování hesla, může být toto heslo také zapsáno zpět do místního prostředí služby Active Directory. Zpětný zápis hesla zajišťuje, že uživatel může okamžitě používat jejich aktualizované přihlašovací údaje s místními zařízeními a aplikacemi.

## <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Vícefaktorové ověřování je proces, při kterém je uživatel během procesu přihlášení vyzván k zadání další formy identifikace, například k zadání kódu na svém mobilním telefonu nebo k provedení skenování otisků prstů.

Pokud používáte heslo pouze k ověření uživatele, ponechá nezabezpečený vektor pro útok. Pokud je heslo slabé nebo bylo vystaveno jinde, je to opravdu uživatel, který se přihlašuje pomocí uživatelského jména a hesla, nebo je to útočník? Pokud požadujete druhou formu ověřování, zabezpečení se zvýší, protože tento další faktor není něco, co je pro útočníka snadné získat nebo duplikovat.

![Koncepční obraz různých forem vícefaktorové autentizace](./media/concept-mfa-howitworks/methods.png)

Azure Multi-Factor Authentication funguje tak, že vyžaduje dvě nebo více z následujících metod ověřování:

* Něco, co znáte, obvykle heslo.
* Něco, co máte, například důvěryhodné zařízení, které není snadno duplikováno, například telefon nebo hardwarový klíč.
* Něco, co jste - biometrie jako otisk prstu nebo skenování obličeje.

Uživatelé se mohou zaregistrovat pro samoobslužné resetování hesla a azure multifaktorové ověřování v jednom kroku zjednodušit možnosti nástupu na palubu. Správci mohou definovat, jaké formy sekundárního ověřování lze použít. Azure Multi-Factor Authentication může být také vyžadováno, když uživatelé provádějí samoobslužné resetování hesla k dalšímu zabezpečení tohoto procesu.

## <a name="password-protection"></a>Ochrana heslem

Ve výchozím nastavení Azure AD blokuje slabá hesla, jako je *Password1*. Globální seznam zakázaných hesel je automaticky aktualizován a vynucen, který obsahuje známá slabá hesla. Pokud se uživatel Azure AD pokusí nastavit své heslo na jedno z těchto slabých hesel, obdrží oznámení o výběru bezpečnějšího hesla.

Chcete-li zvýšit zabezpečení, můžete definovat vlastní zásady ochrany heslem. Tyto zásady mohou pomocí filtrů blokovat libovolnou variantu hesla obsahujícího například název *Contoso* nebo umístění, jako je *Londýn*.

Pro hybridní zabezpečení můžete integrovat ochranu heslem Azure AD s místním prostředím služby Active Directory. Součást nainstalovaná v prostředí on-prem obdrží globální seznam zakázaných hesel a vlastní zásady ochrany heslem z Azure AD a řadiče domény je používají ke zpracování událostí změny hesla. Tento hybridní přístup zajišťuje, že bez ohledu na to, jak nebo kde uživatel změní svá pověření, vynucujete použití silných hesel.

## <a name="passwordless-authentication"></a>Ověřování bez hesla

Konečným cílem pro mnoho prostředí je odebrat použití hesel jako součást událostí přihlášení. Funkce, jako je ochrana heslem Azure nebo Azure Multi-Factor Authentication pomáhají zlepšit zabezpečení, ale uživatelské jméno a heslo zůstává slabou formou ověřování, které může být vystaveno nebo napadeno hrubou silou.

![Zabezpečení versus pohodlí s procesem ověřování, který vede k bezhelnám](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

Když se přihlásíte metodou bez hesla, přihlašovací údaje jsou k dispozici pomocí metod, jako je biometrie s Windows Hello pro firmy nebo bezpečnostní klíč FIDO2. Tyto metody ověřování nelze snadno duplikovat útočníkem.

Azure AD poskytuje způsoby, jak nativně ověřit pomocí metod bez hesla pro zjednodušení prostředí pro přihlášení pro uživatele a snížit riziko útoků.

## <a name="next-steps"></a>Další kroky

Chcete-li začít, podívejte se na [rychlý start pro samoobslužné resetování hesla][quickstart-sspr] a Azure [Multi-Factor Authentication tutorial][tutorial-mfa-applications].

Další informace o samoobslužných konceptech resetování hesla najdete v [tématu Jak funguje samoobslužné resetování hesla služby Azure AD][concept-sspr].

Další informace o konceptech vícefaktorového ověřování najdete v [tématu Jak funguje vícefaktorové ověřování Azure][concept-mfa].

<!-- INTERNAL LINKS -->
[quickstart-sspr]: quickstart-sspr.md
[tutorial-mfa-applications]: tutorial-mfa-applications.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
