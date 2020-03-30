---
title: Získání nápovědy k portálu Moje aplikace – Azure Active Directory| Dokumenty společnosti Microsoft
description: Získejte pomoc s přihlášením a prováděním běžných úloh na portálu Moje aplikace.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: f72dd5595d67ae989cec5681d22def9a2f929adf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253153"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>Poradce při potížích s portálem Moje aplikace

Pokud máte problémy s přihlášením k portálu **Moje aplikace** nebo s jeho používáním, vyzkoušejte tyto tipy pro řešení potíží dříve, než kontaktujete helpdesk nebo správce a požádejte o pomoc.

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Mám potíže s instalací rozšíření O bezpečné přihlašování aplikace

Pokud máte potíže s instalací rozšíření Zabezpečené přihlášení moje aplikace:

- Ujistěte se, že používáte podporovaný prohlížeč, včetně:

    - **Microsoft Edge.** Běží na Windows 10 Anniversary Edition nebo novější.

    - **Google Chrome.** Běží na Windows 7 nebo novější, a na Mac OS X nebo novější.

    - **Mozilla Firefox 26.0 nebo novější.** Běží v systému Windows XP SP2 nebo novějším a v systému Mac OS X 10.6 nebo novějším.

    - **Internet Explorer 11.** Běží na Windows 7 nebo novější (omezená podpora).

- Zkontrolujte, zda je zapnuté nastavení rozšíření prohlížeče.

- Zkuste restartovat prohlížeč a znovu se přihlásit k portálu **Moje aplikace.**

- Zkuste vymazat soubory cookie prohlížeče a restartujte počítač a znovu se přihlaste k portálu **Moje aplikace.**

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>Nemůžu se přihlásit k portálu **Moje aplikace**

Pokud máte potíže s přihlášením k portálu **Moje aplikace,** můžete zkusit následující:

- Ujistěte se, že používáte správnou adresu URL. Měla by https://myapps.microsoft.com to být přizpůsobená stránka pro https://myapps.microsoft.com/contoso.comvaši organizaci, například .

- Zkontrolujte, zda je heslo správné a jeho platnost nevypršela. Další informace najdete v [tématu Resetování pracovního nebo školního hesla](active-directory-passwords-update-your-own-password.md).

- Zkontrolujte, zda jsou ověřovací údaje aktuální a přesné. Další informace najdete v tématu Co pro mě [Changing your security info methods and information](security-info-add-update-methods-overview.md)znamená [azure multifaktorové ověřování?](multi-factor-authentication-end-user.md)

- Přidejte adresu URL portálu **Moje aplikace** do nastavení **Vlastnosti internetu > zabezpečení > důvěryhodné servery.**

- Vymažte mezipaměť prohlížeče a zkuste se znovu přihlásit.

## <a name="my-password-isnt-working"></a>Moje heslo nefunguje

Pokud jste zapomněli heslo, nikdy jste ho neobdrželi od vaší organizace, jste zablokováni z účtu nebo chcete změnit heslo, přečtěte si [nápovědu, zapomněl jsem heslo azure ad](active-directory-passwords-update-your-own-password.md).

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>Chci mít možnost obnovit své vlastní heslo

Aby bylo možné obnovit vlastní heslo, musí správce nejprve zapnout funkci pro vaši organizaci a potom je nutné aktualizovat a ověřit požadované metody ověření. Další informace o aktualizaci metod ověřování naleznete v [tématu Registrace samoobslužných resetování hesla](active-directory-passwords-reset-register.md).

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>Při spuštění aplikace se zobrazuje zpráva odepření přístupu

Pokud se vám po spuštění aplikace z portálu **Moje aplikace** zobrazuje zpráva o **odepření přístupu,** můžete zkusit následující:

- Zkontrolujte, zda jste nainstalovali [rozšíření Moje aplikace zabezpečeného přihlášení](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) a zda používáte podporovaný [prohlížeč](my-apps-portal-end-user-access.md#supported-browsers).

- Ujistěte se, že používáte správnou adresu URL aplikace a že adresa URL je na vašem **seznamu Vlastnosti > zabezpečení > > důvěryhodné servery.**

- Zkontrolujte, zda je heslo správné a jeho platnost nevypršela. Další informace najdete v [tématu Resetování pracovního nebo školního hesla](active-directory-passwords-update-your-own-password.md).

- Zkontrolujte, zda jsou ověřovací údaje aktuální a přesné. Další informace najdete v tématu Co pro mě [Changing your security info methods and information](security-info-add-update-methods-overview.md)znamená [azure multifaktorové ověřování?](multi-factor-authentication-end-user.md)

- Vymažte mezipaměť prohlížeče a zkuste se znovu přihlásit.

Pokud po vyzkoušení těchto věcí stále nemáte přístup k aplikaci, musíte se obrátit na oddělení technické podpory vaší organizace a požádat o pomoc.

## <a name="next-steps"></a>Další kroky

Po přihlášení na portál **Moje aplikace** můžete také aktualizovat informace o svém profilu a účtu, informace o skupině a přístup k informacím o recenzích (pokud máte oprávnění).

- [Přístup k aplikacím a jejich používání na portálu Moje aplikace](my-apps-portal-end-user-access.md).

- [Změňte informace o svém profilu](my-apps-portal-end-user-update-profile.md).

- [Zobrazení a aktualizace informací týkajících se skupin](my-apps-portal-end-user-groups.md).

- [Proveďte vlastní kontroly přístupu](my-apps-portal-end-user-access-reviews.md).
