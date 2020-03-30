---
title: Jak funguje Azure MFA – Azure Active Directory
description: Azure Multi-Factor Authentication pomáhá chránit přístup k datům a aplikacím a zároveň uspokojovat požadavky uživatelů na jednoduchý proces přihlášení.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39948214f5bd080be417ed515bea6bff87d3b303
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484056"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Jak to funguje: Azure Multi-Factor Authentication

Bezpečnost dvoustupňového ověřování spočívá v jeho vrstvené přístupu. Ohrožení více faktorů ověřování představuje pro útočníky významnou výzvu. I v případě, že útočník podaří naučit heslo uživatele, je k ničemu, aniž by také mít k dispozici další metodu ověřování. Funguje tak, že vyžaduje dvě nebo více z následujících metod ověřování:

* Něco, co znáte (obvykle heslo)
* Něco, co máte (důvěryhodné zařízení, které není snadno duplikovat, jako je telefon)
* Něco, co jste (biometrie)

<center>

![Obrázek metod konceptuálního ověřování](./media/concept-mfa-howitworks/methods.png)</center>

Azure Multi-Factor Authentication (MFA) pomáhá chránit přístup k datům a aplikacím při zachování jednoduchosti pro uživatele. Poskytuje další zabezpečení tím, že vyžaduje druhou formu ověřování a poskytuje silné ověřování prostřednictvím řady snadno použitelných [metod ověřování](concept-authentication-methods.md). Uživatelé mohou nebo nemusí být napadena pro vícefaktorové řízení na základě rozhodnutí o konfiguraci, které provede správce.

## <a name="how-to-get-multi-factor-authentication"></a>Jak vícefaktorové ověřování získat?

Vícefaktorové ověřování je součástí následujících nabídek:

* **Azure Active Directory Premium** nebo **Microsoft 365 Business** – plné doporučené využití Azure Multi-Factor Authentication pomocí zásad podmíněného přístupu, které vyžadují vícefaktorové ověřování.

* **Azure AD Free** nebo samostatné licence **Office 365** – použití [výchozích nastavení zabezpečení](../fundamentals/concept-fundamentals-security-defaults.md) k vyžadování vícefaktorového ověřování pro uživatele a správce.

* **Globální správci Azure Active Directory** – podmnožina možností služby Azure Multi-Factor Authentication je k dispozici jako způsob ochrany účtů globálních správců.

> [!NOTE]
> Noví zákazníci už nemusí nakupovat Azure Multi-Factor Authentication jako samostatnou nabídku s účinností od 1. Vícefaktorové ověřování bude i nadále dostupnou funkcí v licencích Azure AD Premium.

## <a name="supportability"></a>Možnosti podpory

Vzhledem k tomu, že většina uživatelů je zvyklá používat k ověřování pouze hesla, je důležité, aby vaše organizace komunikovala se všemi uživateli o tomto procesu. Povědomí může snížit pravděpodobnost, že uživatelé volají na váš help desk pro drobné problémy související s vícefaktorové pomoci. Existují však některé scénáře, kde je nutné dočasně zakázat vícefaktorové zabezpečení. Pomocí následujících pokynů pochopit, jak zpracovat tyto scénáře:

* Vyškolte pracovníky podpory tak, aby zpracovávali scénáře, kdy se uživatel nemůže přihlásit, protože nemá přístup ke svým metodám ověřování nebo nepracuje správně.
   * Pomocí zásad podmíněného přístupu pro službu Azure MFA můžete vaši pracovníci podpory přidat uživatele do skupiny, která je vyloučena ze zásady vyžadující vícefaktorové ověřování.
* Zvažte použití s názvem umístění podmíněného přístupu jako způsobu, jak minimalizovat výzvy k dvoustupňovému ověření. Pomocí této funkce mohou správci obejít dvoustupňové ověření pro uživatele, kteří se přihlašují ze zabezpečeného důvěryhodného síťového umístění, jako je například síťový segment používaný pro připojení nových uživatelů.
* Nasaďte [azure ad identity protection](../active-directory-identityprotection.md) a aktivujte dvoustupňové ověření na základě detekce rizik.

## <a name="next-steps"></a>Další kroky

- [Podrobné nasazení Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
