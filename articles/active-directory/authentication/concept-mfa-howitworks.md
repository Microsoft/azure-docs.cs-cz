---
title: Azure Multi-Factor Authentication – jak to funguje – Azure Active Directory
description: Azure Multi-Factor Authentication pomáhá chránit přístup k datům a aplikacím a současně plní požadavky uživatelů na jednoduchý proces přihlašování.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57b441803a066b794d17b46afec4d930b94ebdea
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125136"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Jak to funguje: Azure Multi-Factor Authentication

Zabezpečení dvoustupňového ověřování spočívá v jeho vrstveném přístupu. Omezení více ověřovacích faktorů prezentuje závažné výzvy pro útočníky. I když se útočník pokusí zjistit heslo uživatele, není k dispozici bez ohledu na další metodu ověřování. Funguje tak, že vyžaduje dvě nebo více následujících metod ověřování:

* Něco, co znáte (obvykle heslo)
* Něco, co máte (důvěryhodné zařízení, které není jednoduše duplikováno, jako je telefon)
* Něco, co používáte (biometrika)

<center>

![Obrázek metod koncepčního ověřování](./media/concept-mfa-howitworks/methods.png)</center>

Azure Multi-Factor Authentication (MFA) pomáhá chránit přístup k datům a aplikacím a současně zachovává jednoduchost uživatelů. Poskytuje dodatečné zabezpečení tím, že vyžaduje druhou formu ověřování a zajišťuje silné ověřování prostřednictvím řady snadno použitelných [metod ověřování](concept-authentication-methods.md). Na základě rozhodnutí týkajících se konfigurace, která správce provede, se uživatelé můžou nebo nemusí vyvolávat na MFA.

## <a name="how-to-get-multi-factor-authentication"></a>Jak získat Multi-Factor Authentication?

Multi-Factor Authentication se dodává jako součást následujících nabídek:

* **Azure Active Directory Premium** nebo **Microsoft 365 Business** plně vybaveného použití Azure Multi-Factor Authentication pomocí zásad podmíněného přístupu, které vyžadují vícefaktorové ověřování.

* **Azure AD Free** nebo samostatné licence na **Office 365** – pomocí předem vytvořených [zásad ochrany základní úrovně přístupu](../conditional-access/concept-baseline-protection.md) pro uživatele a správce vyžadovat vícefaktorové ověřování.

* **Azure Active Directory globálních správců** – podmnožina možností Azure Multi-Factor Authentication je dostupná jako způsob ochrany globálních účtů správců.

> [!NOTE]
> Noví zákazníci už nemůžou koupit Azure Multi-Factor Authentication jako samostatnou nabídku platnou od 1. září 2018. Multi-Factor Authentication bude nadále dostupná funkce v Azure AD Premium licencích.

## <a name="supportability"></a>Možnosti podpory

Vzhledem k tomu, že většina uživatelů je zvyklá používat k ověřování jenom hesla, je důležité, aby vaše organizace komunikovala se všemi uživateli týkajícími se tohoto procesu. Povědomí může snížit pravděpodobnost, že uživatelé volají vaši technickou podporu s menšími problémy souvisejícími s MFA. Existují však situace, kdy je nutné dočasně zakázat MFA. Následující pokyny vám pomohou pochopit, jak tyto scénáře zvládnout:

* Školením pracovníků podpory můžete zvládnout scénáře, kdy se uživatel nemůže přihlásit, protože nemá přístup ke svým metodám ověřování nebo nepracuje správně.
   * Pomocí zásad podmíněného přístupu pro službu Azure MFA mohou pracovníci podpory přidat uživatele do skupiny, která je vyloučena ze zásady, která vyžaduje MFA.
* Zvažte použití podmíněného přístupu s názvem umístění jako způsob, jak minimalizovat výzvy ke dvěma krokům při ověřování. Díky této funkci můžou správci obejít dvoustupňové ověřování u uživatelů, kteří se přihlašují z zabezpečeného důvěryhodného síťového umístění, jako je například segment sítě, který se používá pro nové připojování uživatelů.
* Nasazení [Azure AD Identity Protection](../active-directory-identityprotection.md) a aktivace dvoustupňového ověřování na základě detekce rizik.

## <a name="next-steps"></a>Další postup

- [Podrobný postup nasazení Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
