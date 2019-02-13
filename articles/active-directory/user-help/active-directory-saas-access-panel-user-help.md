---
title: Získejte pomoc se sadou přístup a používání portálu MyApps ve službě Azure Active Directory | Dokumentace Microsoftu
description: Získejte pomoc s přihlašováním k a provádět běžné úkoly na přístupovém panelu.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: lizross
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64090e67c7bdc019498d35d7e9f5dfc8402ea57e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175758"
---
# <a name="troubleshoot-issues-with-accessing-and-using-the-myapps-portal"></a>Řešení potíží s přístupem k a používání portálu MyApps

Pokud je máte problémy s přihlašováním k nebo pomocí portálu MyApps, zkuste tyto tipy pro řešení potíží než požádáte o pomoc helpdesk nebo správce.

## <a name="i-am-having-trouble-signing-into-the-myapps-portal"></a>Mám potíže při přihlašování k portálu MyApps

Vyzkoušejte tyto Obecné tipy:

- První, kontrola zjistit, zda se používá správnou adresu URL [ https://myapps.microsoft.com ](https://myapps.microsoft.com).
- Zkuste přidat adresu URL důvěryhodných serverů v prohlížeči.
- Ujistěte se, že heslo je správný a že nevypršela platnost. Další informace najdete v tématu [obnovit heslo pracovního nebo školního](active-directory-passwords-update-your-own-password.md).
- Zkontrolujte, že je vaše kontaktní informace pro ověřování a neblokuje přístup. Další informace najdete v tématu [Azure Multi-Factor Authentication význam pro mě nejlepší?](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user).
- Vymazat soubory cookie v prohlížeči a potom to zkusit znovu přihlásit.

Pokud jsou stále dochází k problémům při pokusu o přihlášení, obraťte se na správce.

## <a name="i-seem-to-be-having-password-issues"></a>Zdá se, že se s problémy s heslem

Pokud zapomněli jste heslo nikdy nedostali některou z pracovníky IT, jsou uzamčené mimo váš účet nebo chcete změnit heslo, přečtěte si téma [Nápověda k zapomenutému heslu Azure AD](active-directory-passwords-update-your-own-password.md).

## <a name="i-need-to-register-for-password-reset"></a>Potřebuji k registraci pro resetování hesla

Můžete resetovat heslo nebo odemknout svůj účet bez nutnosti mluvit s někdo pomocí samoobslužného resetování hesla (SSPR). Tuto funkci mohli používat, musíte zaregistrovat metody ověřování nebo potvrdit předdefinované metody ověřování, které vyžaduje váš správce. Další informace najdete v tématu [registrace pro resetování hesla pomocí samoobslužné služby](active-directory-passwords-reset-register.md).

## <a name="i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Mám potíže s instalací rozšíření Moje aplikace zabezpečené přihlášení

Na portálu MyApps vyžaduje prohlížeč, který podporuje JavaScript a má povolený šablon stylů CSS. Pokud použijete heslo aplikace založené na jednotné přihlašování, doprovodné rozšíření musí být nainstalována také. Toto rozšíření se automaticky stáhne při spuštění aplikace, který je nakonfigurovaný pro heslo aplikace založené na jednotné přihlašování.

Zaškrtněte, pokud chcete zajistit, že jsou splňuje následující požadavky na prohlížeč:

- **Microsoft Edge**: Windows 10 Anniversary Edition nebo novější.
- **Chrome**: ve Windows 7 nebo novější a v Mac OS X nebo novější.
- **Firefox 26.0 nebo novější**: ve Windows XP SP2 nebo novější a Mac OS X 10.6 nebo novější.
- **Internet Explorer 11**: Windows 7 nebo novější (omezená podpora).

Rozšíření můžete také stáhnout přímo z následujících webů:

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

Pokud máte nainstalované rozšíření a i nadále dochází k problémům, zkuste následující:

- Zkontrolujte nastavení rozšíření prohlížeče a ujistěte se, že ho povolíte.
- Restartujte prohlížeč a přihlaste se k portálu MyApps.
- Vymazat soubory cookie prohlížeče a přihlaste se k portálu MyApps.
- Přístup k nástroji Diagnostika a podrobné pokyny ke konfiguraci rozšíření pro aplikaci Internet Explorer, naleznete v tématu [řešení potíží s rozšířením Panel přístupu pro aplikaci Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Použití Moje aplikace zabezpečené přihlašování rozšíření
* Pokud používáte Moje adresy URL aplikací jiných než `https://myapps.microsoft.com`, nakonfigurujte adresu URL svého výchozí následujícím způsobem:
   1. Když jste *není* přihlášení k rozšíření, klikněte pravým tlačítkem na ikonu rozšíření.
   2. V nabídce vyberte **Moje aplikace URL**.
   3. Vyberte výchozí adresu URL.
   4. Vyberte ikonu rozšíření.
   5. Přihlaste se k rozšíření, vyberte **Přihlaste se k Začínáme**.

* Přihlásit se přímo do aplikace v prohlížeči, postupujte takto:
   1. Po instalaci rozšíření, přihlaste se k němu tak, že vyberete **Přihlaste se k Začínáme**.
   2. Přihlaste se k aplikaci pomocí adresy URL přihlašování.  
       Adresa URL přihlašování je obvykle adresa URL aplikace, která se zobrazí přihlašovací formulář.
      Rozšíření by měl změny stavu a zjistíte, že heslo je k dispozici.
   3. Pro přihlášení, vyberte ikonu rozšíření.

* Chcete-li spustit aplikaci z rozšíření, postupujte takto:
   1. Po instalaci rozšíření, přihlaste se k němu tak, že vyberete **Přihlaste se k Začínáme**.
   2. Vyberte ikonu rozšíření a otevřete nabídku.
   3. Vyhledejte aplikaci, která je dostupná na portálu MyApps.
   4. V seznamu výsledků hledání vyberte aplikaci.  
       Poslední tři aplikace využili jste se zobrazují v **naposledy použité** zástupce seznamu.

> [!NOTE]
> Tyto možnosti jsou dostupné jenom pro Microsoft Edge, Chrome a Firefox.

## <a name="how-do-i-add-a-new-app"></a>Jak přidat novou aplikaci?

1.  Na **aplikace** stránce **přidat aplikaci**.
2.  Najděte aplikaci, kterou chcete přidat a pak vyberte **přidat**.

   > [!NOTE]
   > * Tuto možnost můžete přistupovat pouze v případě, že správce má povolený pro váš účet.
   > * Pokud aplikace vyžaduje oprávnění, může muset čekat na schválení správce.

## <a name="how-do-i-manage-my-group-memberships"></a>Jak můžu spravovat své členství ve skupinách?

Vyberte **skupiny** dlaždici, a poté proveďte jednu z následujících akcí:
* Chcete-li vytvořit skupinu, v části **skupiny moje**vyberte **vytvořit skupinu**a pak postupujte podle pokynů.
* Připojení ke skupině, v části **skupiny ve**vyberte **připojit se ke skupině**a pak postupujte podle pokynů.

   > [!NOTE]
   > * Tuto možnost můžete přistupovat pouze v případě, že správce má povolený pro váš účet.
   > * Pokud jste členem skupiny, můžete zobrazit podrobnosti a ponechat skupině.
   > * Pokud jste vlastníkem skupiny, můžete zobrazit podrobnosti, přidat nebo odebrat členy a opustit skupinu.
