---
title: Ladění jednotného přihlašování saml - Azure Active Directory | Dokumenty společnosti Microsoft
description: Ladění jednotného přihlašování saml do aplikací ve službě Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 02/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: luleon, hirsin, paulgarn
ROBOTS: NOINDEX
ms.openlocfilehash: 5d92b43b47a20a75d2c8b6becb69cfee5829e80f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154844"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Ladění jednotného přihlašování saml k aplikacím ve službě Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Zjistěte, jak najít a opravit problémy [s jedním přihlášením](../manage-apps/what-is-single-sign-on.md) pro aplikace ve službě Azure Active Directory (Azure AD), které podporují [jazyk značek zabezpečení Markup Language (SAML) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language). 

## <a name="before-you-begin"></a>Než začnete

Doporučujeme nainstalovat [rozšíření Zabezpečené přihlášení moje aplikace](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension). Toto rozšíření prohlížeče usnadňuje shromažďování informací o požadavku SAML a odpovědi SAML, které potřebujete k řešení problémů s jediným přihlášením. V případě, že rozšíření nelze nainstalovat, tento článek ukazuje, jak vyřešit problémy s nainstalovaným rozšířením i bez něj.

Chcete-li stáhnout a nainstalovat rozšíření Zabezpečení přihlašování moje aplikace, použijte jeden z následujících odkazů.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>Testování jednotného přihlašování na saml

Testování jednotného přihlašování na saml mezi Azure AD a cílovou aplikací:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako globální správce nebo jiný správce, který je oprávněný spravovat aplikace.
1. V levém okně vyberte **Azure Active Directory**a pak vyberte Podnikové **aplikace**. 
1. Ze seznamu podnikových aplikací vyberte aplikaci, pro kterou chcete otestovat jednotné přihlašování, a potom z možností vlevo vyberte **Jedno přihlášení**.
1. Chcete-li otevřít prostředí testování jednotného přihlašování založené na saml, přejděte na **test jednotného přihlášení** (krok 5). Pokud je tlačítko **Test** zašedlé, musíte nejprve vyplnit a uložit požadované atributy v části **Základní konfigurace SAML.**
1. V okně **Testování jednotného přihlašování** použijte k přihlášení k cílové aplikaci vaše podniková pověření. Můžete se přihlásit jako aktuální uživatel nebo jako jiný uživatel. Pokud se přihlásíte jako jiný uživatel, zobrazí se výzva s výzvou k ověření.

    ![Snímek obrazovky s testovací stránkou přiřazování webových stránek SAML](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)

Pokud jste úspěšně přihlášeni, test prošel. V tomto případě Azure AD vydal token odpovědi SAML do aplikace. Aplikace použila token SAML k úspěšnému přihlášení.

Pokud máte chybu na přihlašovací stránce společnosti nebo na stránce aplikace, použijte k vyřešení chyby jednu z dalších částí.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Řešení chyby přihlášení na přihlašovací stránce společnosti

Při pokusu o přihlášení se může zobrazit chyba na přihlašovací stránce společnosti, která je podobná následujícímu příkladu.

![Příklad zobrazení chyby na přihlašovací stránce společnosti](./media/howto-v1-debug-saml-sso-issues/error.png)

Chcete-li ladit tuto chybu, potřebujete chybovou zprávu a požadavek SAML. Rozšíření zabezpečení přihlašování moje aplikace automaticky shromažďuje tyto informace a zobrazuje pokyny k rozlišení ve službě Azure AD.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>Vyřešení chyby přihlášení s nainstalovaným rozšířením Zabezpečení přihlašování moje aplikace

1. Když dojde k chybě, rozšíření přesměruje zpět do okna jednotnépřihlášení Azure AD **Test.**
1. V okně **Test single sign-on** vyberte **Stáhnout požadavek SAML**.
1. Měli byste vidět konkrétní pokyny k řešení na základě chyby a hodnoty v požadavku SAML.
1. Zobrazí se tlačítko **Opravit** automaticky aktualizovat konfiguraci ve službě Azure AD k vyřešení problému. Pokud toto tlačítko nevidíte, pak problém s přihlášením není z důvodu chybné konfigurace ve službě Azure AD.

Pokud není k dispozici žádné řešení chyby přihlášení, doporučujeme, abyste nás informovali pomocí textového pole pro zpětnou vazbu.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>Vyřešení chyby bez instalace rozšíření Zabezpečení přihlašování aplikací

1. Zkopírujte chybovou zprávu v pravém dolním rohu stránky. Chybová zpráva obsahuje:
    - A CorrelationID a časové razítko. Tyto hodnoty jsou důležité při vytváření případu podpory se společností Microsoft, protože pomáhají technikům identifikovat váš problém a poskytnout přesné řešení vašeho problému.
    - Prohlášení identifikující hlavní příčinu problému.
1. Vraťte se do služby Azure AD a najděte okno **testování jednotného přihlašování.**
1. Do textového pole nad **pokyny k řešení**vložte chybovou zprávu.
1. Kliknutím na **tlačítko Získat pokyny k řešení** zobrazíte kroky k vyřešení problému. Pokyny mohou vyžadovat informace z požadavku SAML nebo odpovědi SAML. Pokud nepoužíváte rozšíření Moje aplikace zabezpečené přihlášení, možná budete potřebovat nástroj, jako je [Šumař](https://www.telerik.com/fiddler) načíst požadavek SAML a odpověď.
1. Ověřte, zda cíl v požadavku SAML odpovídá adrese URL služby jednotného přihlášení SAML získanou ze služby Azure AD.
1. Ověřte, že vystavitel v požadavku SAML je stejný identifikátor, který jste nakonfigurovali pro aplikaci ve službě Azure AD. Azure AD používá vystavittele k vyhledání aplikace ve vašem adresáři.
1. Ověřte AssertionConsumerServiceURL je místo, kde aplikace očekává, že obdrží token SAML z Azure AD. Tuto hodnotu můžete nakonfigurovat ve službě Azure AD, ale není povinná, pokud je součástí požadavku SAML.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Řešení chyby přihlášení na stránce aplikace

Můžete se úspěšně přihlásit a pak se zobrazí chyba na stránce aplikace. K tomu dochází, když Azure AD vydal token pro aplikaci, ale aplikace nepřijímá odpověď.

Chcete-li chybu vyřešit, postupujte takto:

1. Pokud je aplikace v Galerii Azure AD, ověřte, že jste postupovali podle všech kroků pro integraci aplikace s Azure AD. Pokyny k integraci pro vaši aplikaci najdete v [seznamu výukových programů pro integraci aplikací SaaS](../saas-apps/tutorial-list.md).
1. Načíst odpověď SAML.
    - Pokud je nainstalováno rozšíření Moje aplikace zabezpečenépřihlášení, klepněte na tlačítko **Otestovat jednotné přihlášení** **na tlačítko Stáhnout odpověď SAML**.
    - Pokud rozšíření není nainstalován, použijte nástroj, jako je [Šumař](https://www.telerik.com/fiddler) načíst odpověď SAML.
1. Všimněte si těchto prvků v tokenu odpovědi SAML:
   - Jedinečný identifikátor uživatele hodnoty a formátu NameID
   - Nároky vydané v tokenu
   - Certifikát používaný k podepsání tokenu.

     Další informace o odpovědi SAML naleznete v [tématu Single Sign-on SAML protocol](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

1. Nyní, když jste zkontrolovali odpověď SAML, naleznete [v tématu Chyba na stránce aplikace po přihlášení](../manage-apps/application-sign-in-problem-application-error.md) pokyny, jak problém vyřešit. 
1. Pokud se stále nemůžete úspěšně přihlásit, můžete se zeptat dodavatele aplikace, co chybí v odpovědi SAML.

## <a name="next-steps"></a>Další kroky

Teď, když jednotné přihlašování pracuje na vaší aplikaci, můžete [automatizovat zřizování uživatelů a zrušení zřizování pro aplikace SaaS](../manage-apps/user-provisioning.md) nebo [začít s podmíněným přístupem](../conditional-access/app-based-conditional-access.md).
