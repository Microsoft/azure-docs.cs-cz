---
title: Ladění založené na SAML jednotného přihlašování – Azure Active Directory | Dokumentace Microsoftu
description: Ladění založené na SAML jednotného přihlašování k aplikacím v Azure Active Directory.
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
ms.openlocfilehash: 0192b2d89b5e97c7f23b5394106853ddf83ddaf4
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044005"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Ladění založené na SAML jednotného přihlašování k aplikacím v Azure Active Directory

Zjistěte, jak najít a opravit [jednotného přihlašování](../manage-apps/what-is-single-sign-on.md) problémy pro aplikace v Azure Active Directory (Azure AD), které podporují [zabezpečení kontrolního výrazu SAML (Markup Language) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language). 

## <a name="before-you-begin"></a>Než začnete
Doporučujeme vám však nainstalovat [Moje aplikace zabezpečené přihlašování rozšíření](../user-help/active-directory-saas-access-panel-user-help.md#i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension). Toto rozšíření prohlížeče umožňuje snadno získat požadavek SAML a informace o SAML odpovědi, které potřebujete pro řešení problémů s jednotným přihlašováním. V případě, že nemůžete instalovat rozšíření, tento článek ukazuje, jak vyřešit problémy s i bez nainstalované rozšíření.

Stáhnout a nainstalovat rozšíření Moje aplikace zabezpečené přihlašování, použijte jednu z následujících odkazů.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)


## <a name="test-saml-based-single-sign-on"></a>Testování založené na SAML jednotného přihlašování

Testování založené na SAML jednotného přihlašování mezi AAD a cílová aplikace:

1.  Přihlaste se k [webu Azure portal](https://portal.azure.com) jako globální správce nebo jiný správce, který má oprávnění ke správě aplikací.
2.  V levém okně klikněte na tlačítko **Azure Active Directory**a potom klikněte na tlačítko **podnikové aplikace**. 
3.  Ze seznamu podnikové aplikace, klikněte na aplikaci, pro který chcete otestovat jednotné přihlašování a potom z možností v levém kliknutím na **jednotného přihlašování**.
4.  Chcete-li otevřít založené na SAML jednotné přihlašování – testovací prostředí, v **domény a adresy URL** klikněte na **nastavení testu SAML**. Pokud na nastavení testu SAML tlačítko zašedlé, budete muset nejprve vyplňte a uložte povinné atributy.
5.  V **otestovat jednotné přihlašování** okně používat své podnikové přihlašovací údaje pro přihlášení k cílové aplikaci. Můžete přihlásit jako aktuální uživatel nebo jako jiný uživatel. Pokud se přihlásit jako jiný uživatel výzvu vás vyzve k ověření.

    ![Zkušební stránku SAML](media/active-directory-saml-debugging/testing.png)


Pokud jste úspěšně přihlášení, má test byl dokončen. V tomto případě Azure AD vystaví token SAML odpovědi do aplikace. Aplikace používá pro úspěšné přihlášení tokenu SAML.

Pokud máte chybu na přihlašovací stránce společnosti nebo aplikace, použijte jednu z následujících částech tuto chybu napravíme.


## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Vyřešte chyby přihlášení na přihlašovací stránku vaší společnosti

Při pokusu o přihlášení se může zobrazit chyba na přihlašovací stránku vaší společnosti. 

![Chyby přihlášení](media/active-directory-saml-debugging/error.png)

Chcete-li ladit tuto chybu, budete potřebovat chybovou zprávu a požadavku SAML. Rozšíření Moje aplikace zabezpečené přihlášení automaticky shromažďuje tyto informace a zobrazí pokyny k řešení v Azure AD. 

Chcete-li vyřešit chybu přihlášení pomocí MyApps zabezpečení přihlášení rozšíření nainstalovat:

1.  Pokud dojde k chybě, rozšíření vás přesměruje zpátky do služby Azure Ad **otestovat jednotné přihlašování** okno. 
2.  Na **otestovat jednotné přihlašování** okna, klikněte na tlačítko **stáhnout si požadavek SAML**. 
3.  Měli byste vidět konkrétní řešení pokyny na základě chybu a hodnoty v požadavku SAML. Přečtěte si pokyny.

Chcete-li vyřešit chybu bez instalace MyApps zabezpečené přihlašování rozšíření:

1. Zkopírujte chybovou zprávu v pravém dolním rohu stránky. Chybová zpráva obsahuje:
    - ID korelace a časové razítko. Tyto hodnoty jsou důležité při vytváření případu podpory s Microsoftem, protože pomáhají rychle, aby váš problém identifikovat a zadat přesné řešení vašeho problému.
    - Příkaz, který identifikuje původní příčinu problému.
2.  Vraťte se do služby Azure AD a najít **otestovat jednotné přihlašování** okno.
3.  V textovém poli nahoře **pomoc rozlišení**, vložte chybové zprávě.
3.  Klikněte na tlačítko **pomoc rozlišení** zobrazíte kroky vedoucí k vyřešení problému. Pokyny mohou vyžadovat informace z požadavku SAML nebo odpověď SAML. Pokud nepoužíváte rozšíření MyApps zabezpečené přihlašování, budete nejspíš muset nástroj, jako [Fiddler](http://www.telerik.com/fiddler) získat požadavek SAML a odpovědi.
4.  Ověřte, že cíl v požadavku SAML odpovídá SAML jednotné přihlašování služby Adresa URL získaná z Azure Active Directory
5.  Ověřte, že issuer v požadavku SAML je stejný identifikátor, který jste nakonfigurovali pro aplikaci v Azure Active Directory. K vyhledání aplikace ve vašem adresáři používá Azure AD vystavitele.
6.  Ověřte, že AssertionConsumerServiceURL je, kde se očekává, že aplikace přijímat tokenu SAML ze služby Azure Active Directory. Tuto hodnotu můžete nakonfigurovat ve službě Azure Active Directory, ale není povinné, pokud se jedná o požadavek SAML.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Vyřešte chyby přihlášení na stránce aplikace

Může úspěšně přihlásit a pak na stránce aplikace zobrazí chyba. K tomu dojde, když Azure AD vystaví token do aplikace, ale aplikace nepřijímá žádné odpovědi.   

Chcete-li vyřešit chybu:

1. Pokud je aplikace v galerii Azure AD, ověřte, zda že jste provedli všechny kroky pro integraci aplikace s Azure AD. Pokyny k integraci vaší aplikace najdete v tématu [seznamu kurzů integrace aplikací SaaS](../saas-apps/tutorial-list.md).
2. Načte odpověď SAML.
    - Pokud je nainstalovaná rozšíření Moje aplikace zabezpečené přihlašování, z **otestovat jednotné přihlašování** okna, klikněte na tlačítko **stáhnout odpověď SAML**.
    - Pokud není nainstalovaná rozšíření, použijte nástroj, jako [Fiddler](http://www.telerik.com/fiddler) načíst odpověď SAML. 
3. Všimněte si, že tyto prvky v tokenu SAML odpovědi:
    - Jedinečný identifikátor uživatele NameID hodnota a formátu
    - Deklarací identity vystavených v tokenu
    - Certifikát použitý k podpisu tokenu. Informace o tom, jak zkontrolovat odpověď SAML, naleznete v tématu [protokol jednotné přihlašování SAML](active-directory-single-sign-on-protocol-reference.md).
4. Další informace o odpověď SAML, naleznete v tématu [protokol jednotné přihlašování SAML](active-directory-single-sign-on-protocol-reference.md).
5. Teď, když si přečtete odpověď SAML, naleznete v tématu [chyba na stránce aplikace po přihlášení](../application-sign-in-problem-application-error.md) pokyny k vyřešení problému. 
6. Pokud pořád nejste schopni úspěšně se přihlásit, požádejte dodavatele aplikace co chybí odpověď SAML.


## <a name="next-steps"></a>Další postup
Teď, když jednotné přihlašování funguje pro aplikace, může [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS](../active-directory-saas-app-provisioning.md), nebo [Začínáme s podmíněným přístupem](../active-directory-conditional-access-azure-portal-get-started.md).


