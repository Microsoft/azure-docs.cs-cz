---
title: Získejte pomoc se sadou na portálu Moje aplikace – Azure Active Directory | Dokumentace Microsoftu
description: Získejte pomoc s přihlašováním k a provádět běžné úkoly na portálu Moje aplikace.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e87ae0a8adf28176d9a97cbf1b78943179884a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60475023"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>Poradce při potížích s na portálu Moje aplikace
Pokud máte problémy s přihlašováním k nebo pomocí **Moje aplikace** portál, zkuste tyto tipy pro řešení potíží než požádáte o pomoc helpdesk nebo správce.

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Mám potíže s instalací rozšíření Moje aplikace zabezpečené přihlášení
Pokud máte potíže s instalací rozšíření Moje aplikace zabezpečené přihlášení:

- Ujistěte se, že používáte podporovaný prohlížeč, včetně:

    - **Microsoft Edge.** V systému Windows 10 Anniversary Edition nebo novějším.
    - **Google Chrome.** V systému Windows 7 nebo později a v Mac OS X nebo novějším.
    - **Mozilla Firefox 26.0 nebo novější.** V systému Windows XP SP2 nebo novější a na Mac OS X 10.6 nebo novějším.
    - **Internet Explorer 11.** Běží na Windows 7 nebo novější (omezená podpora).

- Zkontrolujte, zda že jsou zapnuté nastavení rozšíření prohlížeče.

- Akci restartování prohlížeče a přihlášením **Moje aplikace** portálu znovu.

- Vymažte soubory cookie v prohlížeči a potom restartujte a přihlaste se k **Moje aplikace** portálu znovu.

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>Nemůžu se přihlásit k **Moje aplikace** portálu
Pokud máte potíže s přihlášením **Moje aplikace** portálu, můžete vyzkoušet následující:

- Ujistěte se, že používáte správné adresy URL. Měla by být https://myapps.microsoft.com nebo upravené stránky pro vaši organizaci, jako například https://myapps.microsoft.com/contoso.com.

- Ujistěte se, že heslo je správné a nevypršela platnost. Další informace najdete v tématu [obnovit heslo pracovního nebo školního](active-directory-passwords-update-your-own-password.md).

- Ujistěte se, že vaše údaje ověření přesné a aktuální. Další informace najdete v tématu [Azure Multi-Factor Authentication význam pro mě nejlepší?](multi-factor-authentication-end-user.md) nebo [Změna metody informace o zabezpečení a informace o](security-info-add-update-methods-overview.md).

- Přidat **Moje aplikace** portál adresu URL **vlastnosti Internetu > zabezpečení > Důvěryhodné servery** nastavení.

- Vymazat mezipaměť prohlížeče a zkuste se přihlásit znovu.

## <a name="my-password-isnt-working"></a>Heslo není funkční.
Pokud zapomněli jste heslo nikdy nedostali některou z vaší organizace, jsou uzamčené z vašeho účtu nebo chcete změnit heslo, přečtěte si téma [Nápověda k zapomenutému heslu Azure AD](active-directory-passwords-update-your-own-password.md).

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>Chci, aby mohli resetovat vlastní heslo
Aby bylo možné resetovat vlastní heslo, správce musíte nejprve povolit funkce pro vaši organizaci, a pak musíte aktualizovat a ověřit svoje metody vyžaduje ověření. Další informace o tom, jak aktualizovat své metody ověřování najdete v tématu [registrace pro resetování hesla pomocí samoobslužné služby](active-directory-passwords-reset-register.md).

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>Při spuštění aplikace se zobrazuje zpráva o odepření přístupu
Pokud se vám **přístup odepřen** zpráv po spuštění aplikace z **Moje aplikace** portálu, můžete vyzkoušet následující:

- Ujistěte se, že jste nainstalovali [Moje aplikace zabezpečené přihlašování rozšíření](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) a, který používáte [podporovaného prohlížeče](my-apps-portal-end-user-access.md#supported-browsers).

- Ujistěte se, že používáte správné adresy URL pro aplikaci a že je adresa URL na vaše **vlastnosti Internetu > zabezpečení > Důvěryhodné servery** seznamu.

- Ujistěte se, že heslo je správné a nevypršela platnost. Další informace najdete v tématu [obnovit heslo pracovního nebo školního](active-directory-passwords-update-your-own-password.md).

- Ujistěte se, že vaše údaje ověření přesné a aktuální. Další informace najdete v tématu [Azure Multi-Factor Authentication význam pro mě nejlepší?](multi-factor-authentication-end-user.md) nebo [Změna metody informace o zabezpečení a informace o](security-info-add-update-methods-overview.md).

- Vymazat mezipaměť prohlížeče a zkuste se přihlásit znovu.

Pokud po pokusu o nich budete pořád nemáte přístup k vaší aplikace, musí vaše organizace helpdesku požádejte o pomoc.

## <a name="next-steps"></a>Další postup
Po přihlášení k **Moje aplikace** portálu, můžete také aktualizovat váš profil a informace o účtu, informace o skupinách a přístup zkontrolujte informace (Pokud máte oprávnění).

- [Přístup a používání aplikací na portálu Moje aplikace](my-apps-portal-end-user-access.md).

- [Změnit informace z vašeho profilu](my-apps-portal-end-user-update-profile.md).

- [Zobrazit a aktualizovat vaše informace týkající se skupiny](my-apps-portal-end-user-groups.md).

- [Provést vlastní kontroly přístupu](my-apps-portal-end-user-access-reviews.md).