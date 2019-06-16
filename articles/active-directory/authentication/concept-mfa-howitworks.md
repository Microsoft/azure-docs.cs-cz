---
title: Azure Multi-Factor Authentication – jak funguje – Azure Active Directory
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
ms.openlocfilehash: ff8f27d727768f3f1135f8929310d5cfeffe874b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113524"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Jak to funguje: Azure Multi-Factor Authentication

Zabezpečení dvoustupňové ověřování spočívá v jeho vrstveného přístupu. Tím bylo narušeno několika faktory ověřování představuje velkou výzvou pro útočníky. I v případě, že útočník dokázal další heslo uživatele, je zbytečné bez nutnosti vlastnictví dodatečnou metodu ověřování. Funguje tak, že vyžaduje dva nebo více z následujících metod ověřování:

* Něco víte (obvykle heslo)
* Něco, co máte (důvěryhodné zařízení, které není lehké duplikovat, jako je telefon)
* Něco, co že je (vaše biometrika)

<center>

![Koncepční ověřovací metody image](./media/concept-mfa-howitworks/methods.png)</center>

Azure Multi-Factor Authentication (MFA) pomáhá chránit přístup k datům a aplikacím při zachování jednoduchosti pro uživatele. Poskytuje dodatečné zabezpečení vyžadováním druhou formu ověřování a poskytuje silné ověřování přes celou řadu snadno použitelné [metody ověřování](concept-authentication-methods.md). Uživatelé můžou nebo nemusí být ovlivňuje vícefaktorové ověřování na základě konfigurace rozhodnutí, které provádí správce.

## <a name="how-to-get-multi-factor-authentication"></a>Jak získat ověření službou Multi-Factor Authentication?

Ověřování službou Multi-Factor Authentication je součástí následujících nabídek:

* **Azure Active Directory Premium** nebo **Microsoft 365 Business** -plně vybavené používání ověřování Azure Multi-Factor Authentication pomocí zásad podmíněného přístupu tak, aby vyžadovala vícefaktorové ověřování.

* **Azure AD Free**, **Azure AD Basic**, nebo samostatné **Office 365** licence – použití předem vytvořené [zásady podmíněného přístupu směrný plán ochrany](../conditional-access/concept-baseline-protection.md) tak, aby vyžadovala ověřování Multi-Factor Authentication pro uživatele a správce.

* **Azure Active Directory globální správci** -podmnožinu funkcí Azure Multi-Factor Authentication jsou k dispozici jako prostředek k ochraně účty globálních správců.

> [!NOTE]
> Noví zákazníci už koupit Azure Multi-Factor Authentication jako samostatná nabízí efektivní dne 1. května 2018. Ověřování službou Multi-Factor Authentication bude i nadále k dispozici funkce v licence Azure AD Premium.

## <a name="supportability"></a>Možnosti podpory

Protože většina uživatelů jsou zvyklí používat jenom hesla pro ověření, je důležité, že vaše organizace komunikuje se všichni uživatelé týkající se tohoto procesu. Sledování může snížit pravděpodobnost, že uživatelé volat vaše oddělení technické podpory pro menší problémy související s MFA. Existují však některé scénáře, kde dočasným zákazem MFA je nezbytné. Chcete-li pochopit, jak zvládnout tyto scénáře pomocí následujících pokynů:

* Trénování pracovníci podpory pro zpracování scénářů, kde uživatel nemůže přihlásit, protože nemají přístup ke své metody ověřování, nebo nejsou správně funguje.
   * Pomocí zásad podmíněného přístupu pro službu Azure MFA, pracovníci podpory můžete přidat uživatele do skupiny, která je vyloučena ze zásad, které vyžadují vícefaktorové ověřování.
* Zvažte možnost použít podmíněný přístup – pojmenovaná umístění jako způsob, jak minimalizovat dvoustupňové ověřování výzvy. Pomocí této funkce lze správcům obejít dvoustupňové ověřování pro uživatele, kteří se přihlašují ze bezpečné důvěryhodné síťové umístění, jako je například síť segment použitý pro nové registrace uživatele.
* Nasazení [Azure AD Identity Protection](../active-directory-identityprotection.md) a aktivovat dvoustupňové ověřování založené na rizikové události.

## <a name="next-steps"></a>Další postup

- [Podrobný postup nasazení Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
