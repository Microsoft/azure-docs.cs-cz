---
title: Zásady pro základní ochranu podmíněného přístupu – Azure Active Directory
description: Základní zásady podmíněného přístupu pro ochranu organizací před běžnými útoky
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 819b2f3c54d97639eceae9aed60fdd5da2e7b3ef
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608180"
---
# <a name="what-are-baseline-policies"></a>Co jsou základní zásady?

Základní zásady představují sadu předdefinovaných zásad, které vám pomůžou chránit organizace před velkým počtem běžných útoků. Mezi tyto běžné útoky můžou patřit spreje, přehrávání a útoky prostřednictvím hesla. Základní zásady jsou k dispozici ve všech edicích služby Azure AD. Společnost Microsoft tyto zásady ochrany základní úrovně zpřístupňuje všem, protože útoky na základě identity byly na nárůstu za posledních několik let. Cílem těchto čtyř zásad je zajistit, aby u všech organizací byla povolená základní úroveň zabezpečení bez dalších poplatků.  

Správa vlastních zásad podmíněného přístupu vyžaduje licenci Azure AD Premium.

## <a name="baseline-policies"></a>Základní zásady

![Základní zásady podmíněného přístupu v Azure Portal](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

Existují čtyři základní zásady, které můžou organizace povolit:

* [Vyžadovat MFA pro správce (Preview)](howto-baseline-protect-administrators.md)
* [Ochrana koncového uživatele (Preview)](howto-baseline-protect-end-users.md)
* [Blokovat starší verze ověřování (Preview)](howto-baseline-protect-legacy-auth.md)
* [Vyžadovat MFA pro správu služby (Preview)](howto-baseline-protect-azure.md)

Všechny čtyři tyto zásady budou mít vliv na starší toky ověřování, jako jsou POP, IMAP a starší Office desktopových klientů.

### <a name="require-mfa-for-admins-preview"></a>Vyžadovat MFA pro správce (Preview)

S ohledem na výkon a přístup k účtům správců byste je měli považovat za zvláštní péči. Jednou z běžných metod, jak zlepšit ochranu privilegovaných účtů, je vyžadovat silnější formu ověření účtu, když se používají k přihlášení. V Azure Active Directory můžete získat silnější ověření účtu tím, že budete vyžadovat, aby se správci zaregistrovali a používali službu Azure Multi-Factor Authentication.

[Vyžadovat MFA pro správce (Preview)](howto-baseline-protect-administrators.md) je základní zásada, která vyžaduje vícefaktorové ověřování (MFA) pro následující role adresáře, které se považují za nejvíce privilegované role Azure AD:

* Globální správce
* Správce SharePointu
* Správce Exchange
* Správce podmíněného přístupu
* Správce zabezpečení
* Správce helpdesku/správce hesel
* Správce fakturace
* Správce uživatelů

Pokud má vaše organizace tyto účty používané ve skriptech nebo v kódu, zvažte jejich nahrazení pomocí [spravovaných identit](../managed-identities-azure-resources/overview.md).

### <a name="end-user-protection-preview"></a>Ochrana koncového uživatele (Preview)

Vysoce privilegovaní správci nejsou jediným cílem útoků. Chybné objekty actor mají za cíl normální uživatele. Po získání přístupu můžou tyto vadné aktéry požádat o přístup k privilegovaným informacím jménem původního držitele účtu nebo si stáhnout celý adresář a udělat útok útoku phishing na celou organizaci. Jednou z běžných metod, jak zlepšit ochranu pro všechny uživatele, je vyžadovat silnější formu ověření účtu, když se zjistí rizikové přihlášení.

**Ochrana koncového uživatele (Preview)** je základní zásada, která chrání všechny uživatele v adresáři. Povolení těchto zásad vyžaduje, aby se všichni uživatelé zaregistrovali pro Azure Multi-Factor Authentication do 14 dnů. Po registraci se uživatelům zobrazí výzva k MFA jenom během pokusů o rizikové přihlašování. Ohrožení uživatelských účtů se zablokuje, dokud se resetování hesla nevrátí a nehrozí. 

[!NOTE]
Všichni uživatelé, kteří byli dříve označeni příznakem rizika, budou zablokováni, dokud nevyberete heslo a nehrozí riziko při aktivaci zásad

### <a name="block-legacy-authentication-preview"></a>Blokovat starší verze ověřování (Preview)

Starší protokoly ověřování (např.: IMAP, SMTP, POP3) jsou protokoly, které obvykle používají starší e-mailové klienty k ověřování. Starší protokoly nepodporují službu Multi-Factor Authentication. I v případě, že máte zásadu vyžadující službu Multi-Factor Authentication pro váš adresář, může se špatný objekt actor ověřit pomocí některého z těchto starších protokolů a obejít službu Multi-Factor Authentication.

Nejlepším způsobem, jak ochránit váš účet před nebezpečnými požadavky na ověření, které udělaly starší protokoly, je jejich blokování.

Zásada směrného plánu **starší verze ověřování (Preview)** blokuje žádosti o ověření vytvořené pomocí starších protokolů. K úspěšnému přihlášení pro všechny uživatele se musí použít moderní ověřování. Při použití ve spojení s ostatními základními zásadami budou požadavky přicházející ze starších protokolů blokované. Všechny uživatele budou navíc vyžadovat MFA, kdykoli je to potřeba. Tato zásada neblokuje Exchange ActiveSync.

### <a name="require-mfa-for-service-management-preview"></a>Vyžadovat MFA pro správu služby (Preview)

Organizace využívají celou řadu služeb Azure a spravují je od Azure Resource Managerch nástrojů, jako jsou:

* portál Azure
* Azure PowerShell
* Azure CLI

Použití některého z těchto nástrojů k provedení správy prostředků je vysoce privilegované akce. Tyto nástroje můžou upravovat konfigurace v rámci předplatného, jako je třeba nastavení služby a fakturace předplatného.

Aby bylo možné chránit privilegované akce, bude vyžadovat vícefaktorové ověřování pro všechny uživatele, kteří přistupují k Azure Portal, Azure PowerShell nebo rozhraní příkazového řádku Azure CLI.

## <a name="enable-a-baseline-policy"></a>Povolit zásadu standardních hodnot

Postup při povolování standardních zásad:

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte na **Azure Active Directory** > **podmíněný přístup**.
1. V seznamu zásad vyberte zásadu standardních hodnot, kterou chcete povolit.
1. Nastavte **Povolit zásady** na **zapnuto**.
1. Klikněte na Uložit.

## <a name="next-steps"></a>Další postup

Další informace naleznete v tématu:

* [Pět kroků pro zabezpečení infrastruktury identity](../../security/fundamentals/steps-secure-identity.md)
* [Co je podmíněný přístup v Azure Active Directory?](overview.md)
* [Vyžadovat MFA pro správce (Preview)](howto-baseline-protect-administrators.md)
* [Ochrana koncového uživatele (Preview)](howto-baseline-protect-end-users.md)
* [Blokovat starší verze ověřování (Preview)](howto-baseline-protect-legacy-auth.md)
* [Vyžadovat MFA pro správu služby (Preview)](howto-baseline-protect-azure.md)
