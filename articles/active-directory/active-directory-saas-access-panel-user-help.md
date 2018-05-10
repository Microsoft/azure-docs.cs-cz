---
title: Zobrazit nápovědu k přístupu a použití portálu Moje aplikace v Azure Active Directory | Microsoft Docs
description: Získáte pomoc s přihlášením a provádět běžné úkoly na přístupovém panelu.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: japere
ms.openlocfilehash: 681831dcc65fa74d8d2e26f58849140498843c49
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="troubleshoot-issues-with-accessing-and-using-the-my-apps-portal"></a>Řešení problémů s přístupu a použití portálu Moje aplikace

Pokud dojde k problémům s přihlášením nebo pomocí portálu Moje aplikace, zkuste tyto tipy k řešení potíží se předtím, než se obrátíte pomoc helpdesk nebo na správce.

## <a name="i-am-having-trouble-signing-into-the-my-apps-portal"></a>Mám problémy při přihlášení k portálu Moje aplikace

Zkuste tyto Obecné tipy pro:

- První, kontrola, pokud chcete zobrazit, zda používáte správnou adresu URL, [ https://myapps.microsoft.com ](https://myapps.microsoft.com).
- Zkuste přidat adresu URL do prohlížeče důvěryhodných serverů.
- Ujistěte se, že vaše heslo je správný a jestli nevypršela platnost. Další informace najdete v tématu [resetovat heslo pracovního nebo školního](active-directory-passwords-update-your-own-password.md).
- Zkontrolujte, že vaše kontaktní údaje ověřování je aktuální a není blokuje přístup. Další informace najdete v tématu [co Azure Multi-Factor Authentication znamená pro mě nejlepší?](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user).
- Vymažte soubory cookie v prohlížeči a zkuste se znovu přihlásit.

Pokud jsou stále dochází k problémům při pokusu o přihlášení, obraťte se na správce.


## <a name="i-seem-to-be-having-password-issues"></a>I se zdá, že se heslo problémy s

Pokud zapomněli jste heslo nikdy přijaté z pracovníci IT, váš účet uzamčen nebo chcete změnit heslo, najdete v části [nápovědy, zapomenuté heslo Azure AD](active-directory-passwords-update-your-own-password.md).

## <a name="i-need-to-register-for-password-reset"></a>Potřebuji zaregistrovat pro resetování hesla

Můžete resetovat heslo nebo odemknout účet bez nutnosti s někým mluvit pomocí samoobslužné resetování hesla (SSPR). Před použitím této funkce, musí zaregistrovat vaše metody ověřování nebo potvrďte metody předdefinované ověřování, které váš správce vyžaduje. Další informace najdete v tématu [registrace pro resetování hesla pomocí samoobslužné služby](active-directory-passwords-reset-register.md).


## <a name="i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Mám problémy při instalaci rozšíření Moje aplikace zabezpečené přihlašování

Moje aplikace portál vyžaduje prohlížeč, který podporuje jazyk JavaScript a aktivoval šablon stylů CSS. Používáte-li heslo aplikace založené na jednom přihlášení, doprovodné rozšíření musí být nainstalována také. Toto rozšíření je stažen automaticky při spuštění aplikace, který je nakonfigurován pro heslo aplikace založené na jednom přihlášení.

Zkontrolujte, zda byly splněny následující požadavky na prohlížeč:
- **Hraniční**: v systému Windows 10 Anniversary Edition nebo novějším.
- **Chrome**: v systému Windows 7 nebo novější a Mac OS X nebo novější.
- **Firefox 26.0 nebo novější**: v systému Windows XP SP2 nebo novější a Mac OS X 10.6 nebo novější.
- **Internet Explorer 11**: na Windows 7 nebo novější (omezená podpora).

Můžete také stáhnout rozšíření pro Chrome a hraniční přímo z následujících lokalit:

- [Rozšíření Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Rozšíření Edge](https://www.microsoft.com/store/apps/9pc9sckkzk84)

Pokud jste nainstalovali rozšíření ale stále má problémy, zkuste následující postup:

- Zkontrolujte nastavení rozšíření prohlížeče a ujistěte se, že je povolena rozšíření.
- Restartujte prohlížeč a přihlaste se k portálu Moje aplikace.
- Vymažte soubory cookie prohlížeče a přihlaste se k portálu Moje aplikace.
- Pro přístup k nástroj pro diagnostiku a podrobné pokyny ke konfiguraci rozšíření pro Internet Explorer, najdete v části [řešení potíží s příponou Panel přístupu pro Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Použití v mé aplikace zabezpečené přihlašování rozšíření
* Pokud používáte Moje adresa URL aplikace jiné než `https://myapps.microsoft.com`, nakonfigurujte výchozí adresu URL pomocí těchto kroků:
   1. Když jste *není* přihlášení k rozšíření, klikněte pravým tlačítkem na ikonu rozšíření.
   2. V nabídce vyberte **Moje adresa URL aplikace**.
   3. Vyberte výchozí adresa URL.
   4. Vyberte ikonu pro rozšíření.
   5. Přihlaste se k rozšíření, vyberte **Přihlaste se k Začínáme**.

* Přihlaste se k aplikaci přímo z prohlížeče, takto:
   1. Po instalaci rozšíření, přihlaste se k němu výběrem **Přihlaste se k Začínáme**.
   2. Přihlaste se k aplikaci pomocí adresy URL přihlašování.  
       Adresa URL přihlašování je obvykle adresu URL aplikace, které zobrazí formulář přihlášení.
      Rozšíření by měl změnit stav a vám oznamuje, že heslo je k dispozici.
   3. K přihlášení, vyberte ikonu rozšíření.

* Spuštění aplikace z rozšíření, postupujte takto:
   1. Po instalaci rozšíření, přihlaste se k němu výběrem **Přihlaste se k Začínáme**.
   2. Vyberte ikonu rozšíření otevřete nabídku.
   3. Vyhledejte aplikaci, která je k dispozici na portálu Moje aplikace.
   4. V seznamu výsledků hledání vyberte aplikaci.  
       Poslední tři aplikace poprvé, zobrazí se zobrazují v **nedávno použité** zástupce seznamu.

> [!NOTE]
> Tyto možnosti jsou k dispozici pouze pro okraj a Chrome, Firefox.

## <a name="how-do-i-add-a-new-app"></a>Jak lze přidat novou aplikaci?

1.  Na **aplikace** vyberte **přidat aplikaci**.
2.  Vyhledejte aplikaci, kterou chcete přidat a potom vyberte **přidat**.

   > [!NOTE]
   > * Tuto možnost můžete přistupovat pouze v případě, že správce má povolené pro váš účet.
   > * Pokud aplikace vyžaduje oprávnění, možná budete muset čekat na schválení správce.
   > 

## <a name="how-do-i-manage-my-group-memberships"></a>Jak lze spravovat Moje členství ve skupině?

Vyberte **skupiny** dlaždici, a poté proveďte jednu z následujících akcí: 
* Chcete-li vytvořit skupinu, v části **skupiny I vlastní**, vyberte **vytvořit skupinu**a pak postupujte podle pokynů.
* O připojení ke skupině, v části **skupiny jsem v**, vyberte **připojit se ke skupině**a pak postupujte podle pokynů.

   > [!NOTE]
   > * Tuto možnost můžete přistupovat pouze v případě, že správce má povolené pro váš účet.
   > * Pokud jste členem skupiny, můžete zobrazit podrobnosti a nechte skupinu.
   > * Pokud jste vlastníkem skupiny, můžete zobrazit podrobnosti, přidat nebo odebrat členy a nechte skupinu.
   >


## <a name="next-steps"></a>Další postup

Další informace o odstraňování potíží naleznete v části [problémů pomocí aplikace přístup k panelu webu nebo mobilní aplikace](active-directory-application-access-panel-content-map.md).

