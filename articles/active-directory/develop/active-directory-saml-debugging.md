---
title: Ladění na základě SAML jednotné přihlašování – Azure Active Directory | Microsoft Docs
description: Ladění na základě SAML jednotného přihlašování k aplikacím v Azure Active Directory.
services: active-directory
author: CelesteDG
documentationcenter: na
manager: mtillman
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/15/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin, dastrock, smalser
ms.openlocfilehash: 8bb0df567fbac6e8f8a2e2f64f868b4f219e05ac
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751390"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Ladění na základě SAML jednotného přihlašování k aplikacím v Azure Active Directory

Zjistěte, jak najít a opravit [jednotného přihlašování](../manage-apps/what-is-single-sign-on.md) problémy pro aplikace v Azure Active Directory (Azure AD), které podporují [zabezpečení kontrolního výrazu SAML (Markup Language) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language). 

## <a name="before-you-begin"></a>Než začnete
Doporučujeme nainstalovat [Moje aplikace zabezpečené přihlašování rozšíření](../active-directory-saas-access-panel-user-help.md#i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension). Toto rozšíření prohlížeče lze snadno získat žádost SAML a SAML odpovědi informace, které potřebujete pro řešení problémů s jednotné přihlašování. V případě, že nemůžete instalovat rozšíření, tento článek ukazuje, jak řešit problémy s i bez rozšíření nainstalovat.

Stáhnout a nainstalovat rozšíření Moje aplikace zabezpečené přihlašování, použijte jednu z následujících odkazů.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)


## <a name="test-saml-based-single-sign-on"></a>Test na základě SAML jednotné přihlašování

K testování na základě SAML jednotné přihlašování mezi AAD a cílová aplikace:

1.  Přihlaste se k [portál Azure](https://portal.azure.com) jako globální správce nebo jiný správce, který má oprávnění ke správě aplikací.
2.  V levém okně klikněte na **Azure Active Directory**a potom klikněte na **podnikové aplikace, které**. 
3.  Ze seznamu podnikové aplikace, klikněte na aplikaci, pro který chcete testovat jednotné přihlašování a potom z možností na levém kliknutím **jednotného přihlašování**.
4.  Otevřete základě SAML jeden testování přihlašování, **domény a adresy URL** části klikněte na tlačítko **nastavení testu SAML**. Pokud je tlačítko Nastavení testu SAML šedá, musíte nejprve vyplňte a uložte povinné atributy.
5.  V **Test jednotného přihlašování** okně používat svoje podnikové přihlašovací údaje pro přihlášení do cílové aplikace. Můžete přihlásit jako aktuální uživatel, nebo jako jiný uživatel. Pokud se přihlásit jako jiný uživatel, výzva vás vyzve k ověření.

    ![Zkušební stránku SAML](media/active-directory-saml-debugging/testing.png)


Pokud jste úspěšně přihlášeni, test byla úspěšná. V takovém případě Azure AD vystaví token SAML odpovědi do aplikace. Aplikace používá SAML token se úspěšně vás přihlásit.

Pokud máte chybu na přihlašovací stránce společnosti nebo aplikace, použijte jednu z následujících částech vyřešit chyby.


## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Vyřešte chyby přihlášení na přihlašovací stránku společnosti

Při pokusu o přihlášení může se zobrazit chyba na přihlašovací stránku vaší společnosti. 

![Chyba přihlášení](media/active-directory-saml-debugging/error.png)

Ladění této chyby, budete potřebovat chybovou zprávu a žádost SAML. Rozšíření Moje aplikace zabezpečené přihlašování automaticky shromažďuje tyto informace a zobrazí pokyny řešení v Azure AD. 

Chcete-li vyřešit chyby přihlášení s MyApps zabezpečení nainstalované rozšíření pro přihlášení:

1.  Když dojde k chybě, rozšíření vás přesměruje zpět do Azure Ad **Test jednotného přihlašování** okno. 
2.  Na **Test jednotného přihlašování** okně klikněte na tlačítko **stáhnout žádost SAML**. 
3.  Měli byste vidět konkrétní řešení pokyny podle chyba a hodnotami ve žádost SAML. Přečtěte si pokyny.

Chcete-li vyřešit chyby bez instalace MyApps zabezpečené přihlašování rozšíření:

1. Zkopírujte chybová zpráva v pravém dolním rohu stránky. Obsahuje chybovou zprávu:
    - CorrelationID a časové razítko. Tyto hodnoty jsou důležité, když vytvoříte případu podpory se společností Microsoft, protože pomáhají technici identifikovat problém a následně přesné řešení vašeho problému.
    - Příkaz identifikace příčiny problému.
2.  Přejděte zpět na Azure AD a najděte **Test jednotného přihlašování** okno.
3.  V textovém poli výše **získat pokyny řešení**, vložte chybovou zprávu.
3.  Klikněte na tlačítko **získat pokyny řešení** zobrazíte kroky vedoucí k vyřešení problému. Pokyny mohou vyžadovat informace z SAML požadavek nebo odpověď SAML. Pokud nepoužíváte rozšíření MyApps zabezpečené přihlašování, bude pravděpodobně nutné nástroj, jako [Fiddler](http://www.telerik.com/fiddler) načíst SAML žádostí a odpovědí.
4.  Ověřte, že cíl v žádosti SAML odpovídá SAML jeden přihlašování adresa URL služby získané z Azure Active Directory
5.  Ověřte, že vystavitel v žádosti SAML je stejný identifikátor, který jste nakonfigurovali pro aplikaci v Azure Active Directory. Azure AD používá vystavitele najít aplikaci ve vašem adresáři.
6.  Ověřte, že AssertionConsumerServiceURL je, kde se předpokládá, že aplikace přijímat tokenu SAML z Azure Active Directory. Tuto hodnotu můžete nakonfigurovat v Azure Active Directory, ale není povinný, pokud je součástí žádost SAML.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Vyřešte chyby přihlášení na stránce aplikace

Můžete úspěšně přihlásit a pak na stránce aplikace najdete v části k chybě. K tomu dochází, když Azure AD vystaví token do aplikace, ale aplikace nepřijímá odpovědi.   

Chcete-li vyřešit chyby:

1. Pokud je aplikace v galerii Azure AD, ověřte, že jste provedli všechny kroky pro integraci aplikace s Azure AD. Pokyny k integraci pro vaši aplikaci, najdete v tématu [seznam kurzů integraci aplikací SaaS](../saas-apps/tutorial-list.md).
2. Načtěte odpověď SAML.
    - Pokud je nainstalovaná rozšíření Moje aplikace zabezpečené přihlašování, z **Test jednotného přihlašování** okně klikněte na tlačítko **stáhnout odpověď SAML**.
    - Pokud není nainstalovaná rozšíření, použijte nástroj, jako [Fiddler](http://www.telerik.com/fiddler) načíst odpověď SAML. 
3. Všimněte si tyto prvky v odpovědi tokenu SAML:
    - Jedinečný identifikátor uživatele NameID hodnotu a formátu
    - Deklarace identity vystavené v tokenu
    - Certifikát používaný k podepisování token. Informace o tom, jak zkontrolovat odpověď SAML najdete v tématu [protokolu SAML přihlašování](active-directory-single-sign-on-protocol-reference.md).
4. Další informace o odpověď SAML, najdete v části [protokolu SAML přihlašování](active-directory-single-sign-on-protocol-reference.md).
5. Teď, když jste si prohlédli odpověď SAML, najdete v části [chyby na stránce aplikace po přihlášení](../application-sign-in-problem-application-error.md) pokyny k vyřešení problému. 
6. Pokud jste stále nemůžete úspěšně přihlásit, požádejte dodavatele aplikace co chybí odpověď SAML.


## <a name="next-steps"></a>Další postup
Teď, když jednotné přihlašování funguje do vaší aplikace, může [automatizace zřizování uživatelů a rušení zajištění pro aplikace SaaS](../active-directory-saas-app-provisioning.md), nebo [Začínáme s podmíněným přístupem](../active-directory-conditional-access-azure-portal-get-started.md).


