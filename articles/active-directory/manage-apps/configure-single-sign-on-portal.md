---
title: Jednotné přihlašování – řízení podnikových aplikací v Azure Active Directory | Dokumentace Microsoftu
description: Správa nastavení jednotného přihlašování pro podnikové aplikace v rámci vaší organizace z Galerie aplikací Azure Active Directory
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: barbkess
ms.reviewer: asmalser
ms.openlocfilehash: 81b959a08f55f13fd0bcadce32b65ba64f9bb270
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365487"
---
# <a name="managing-single-sign-on-for-enterprise-apps"></a>Správa jednotného přihlašování pro podnikové aplikace

Tento článek popisuje způsob použití [webu Azure portal](https://portal.azure.com) ke správě nastavení jednotného přihlašování pro podnikové aplikace. Podnikové aplikace jsou aplikace, které se nasazují a používané ve vaší organizaci. Tento článek se týká hlavně aplikací, které byly přidány od [Galerie aplikací Azure Active Directory](what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery). 

## <a name="finding-your-apps-in-the-portal"></a>Vyhledání aplikace na portálu
Všechny podnikové aplikace, které jsou nastavené pro jednotné přihlašování je možné zobrazit a spravovat na webu Azure Portal. Aplikace lze nalézt v **všechny služby** &gt; **podnikové aplikace** části portálu. 

![Okno podnikových aplikací](./media/configure-single-sign-on-portal/enterprise-apps-blade.png)

Vyberte **všechny aplikace** zobrazíte seznam všech aplikací, které byly nakonfigurovány. Výběr aplikace zobrazí prostředky pro tuto aplikaci, ve kterém lze sestavy prohlížet pro tuto aplikaci a je možné spravovat řadu nastavení.

Chcete-li spravovat nastavení jednotného přihlašování, vyberte **jednotného přihlašování**.

![Okno prostředků aplikace](./media/configure-single-sign-on-portal/enterprise-apps-sso-blade.png)

## <a name="single-sign-on-modes"></a>Režimy jednotné přihlašování
**Jednotné přihlašování** začíná **režimu** nabídky, která umožňuje jednotné přihlašování – režim, který chcete nakonfigurovat. Mezi dostupné možnosti patří:

* **Přihlašování na základě SAML** – tato možnost je k dispozici, pokud aplikace podporuje úplné federované jednotné přihlašování s Azure Active Directory pomocí protokolu SAML 2.0, WS-Federation nebo OpenID connect protokoly.
* **Přihlašování na základě heslo** – tato možnost je k dispozici, pokud Azure AD podporuje pro tuto aplikaci vyplňování formulářů pomocí hesla.
* **Propojené přihlašování** -dříve označované jako "Stávající single sign-on", tato možnost správcům umožňuje umístit odkaz na tuto aplikaci ve Spouštěči aplikací své uživatelské přístupový Panel Azure AD nebo Office 365.

Další informace o těchto režimech najdete v tématu [jak funguje jednotné přihlašování pomocí Azure Active Directory](what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="saml-based-sign-on"></a>Přihlašování na základě SAML
**Přihlašování na základě SAML** možnost je rozdělen na čtyři části:

### <a name="domains-and-urls"></a>Domén a adres URL
To je, kde se přidají všechny podrobnosti o domény a adresy URL aplikace do adresáře služby Azure AD. Všechny vstupy, které jsou potřeba ke zpřístupnění aplikace pracovní jednotné přihlašování se zobrazí přímo na obrazovce, že všechny volitelné vstupy můžete zobrazit výběrem **zobrazit pokročilé nastavení URL** zaškrtávací políčko. Úplný seznam podporovaných vstupů zahrnuje:

* **Adresa URL přihlašování** – Pokud uživatel přejde k přihlášení do aplikace. Pokud je aplikace nakonfigurována k provedení zahájené poskytovatelem služby jednotného přihlašování, když uživatel otevře tato adresa URL, poskytovatele služeb se přesměruje do služby Azure AD k ověření a přihlášení uživatele. 
  * Pokud toto pole se vyplní, Azure AD používá adresu URL ke spuštění aplikace z Office 365 a Azure AD přístupového panelu.
  * Pokud toto pole je vynechán, pak Azure AD místo toho provádí zahájené poskytovatelem identity přihlašování při spuštění aplikace z Office 365, Azure AD přístupovém panelu, nebo ze služby Azure AD jednotné přihlašování – adresa URL.
* **Identifikátor** -tento identifikátor URI by měl jednoznačně identifikovat aplikace, pro kterou jednotného přihlašování je konfigurován. Jedná se o hodnotu, která se odešle službě Azure AD zpět do aplikace jako parametr cílové skupiny tokenu SAML a abyste ověřili, že se očekává aplikace. Tato hodnota se zobrazí také jako ID Entity v veškerá metadata SAML poskytovaný aplikací.
* **Adresa URL pro odpověď** – adresa URL odpovědi je, kde se očekává, že aplikace přijímat tokenu SAML. To se také označuje jako adresu URL Assertion Consumer Service (ACS). Po zadání těchto klikněte na tlačítko Další a pokračujte na další obrazovce. Tato obrazovka obsahuje informace o tom, co potřebuje musí nakonfigurovat na straně aplikace, aby je tak, aby přijímal tokenu SAML ze služby Azure AD.
* **Stav přenosu** – stav přenosu je volitelný parametr, který vám pomůže zjistit, kam po skončení ověřování přesměrovat uživatele aplikace. Obvykle je hodnota platná adresa URL na úrovni aplikace, ale některé aplikace používají toto pole jinak (viz aplikace jednotné přihlašování – podrobnosti naleznete v dokumentaci). Možnost nastavit stav přenosu je nová funkce, které jsou jedinečné na novém portálu Azure portal.

### <a name="user-attributes"></a>Atributy uživatele
To je, kde můžou správci zobrazit a upravit atributy, které se odesílají v tokenu SAML, která vydává Azure AD pro každou aplikaci, že přihlášení.

Je podporované jenom upravitelné atribut **identifikátor uživatele** atribut. Hodnota tohoto atributu je pole ve službě Azure AD, který jednoznačně identifikuje každý uživatel v rámci aplikace. Například pokud se aplikace nasadila, pomocí "e-mailovou adresu" jako uživatelské jméno a jedinečný identifikátor, pak hodnota se nastavuje do pole "user.mail" v Azure AD.

### <a name="saml-signing-certificate"></a>Podpisový certifikát SAML
Tato část ukazuje podrobnosti o certifikátu, který se používá k podepisování tokenů SAML, které jsou vydány pokaždé, když se uživatel ověřuje aplikace Azure AD. To umožňuje vlastnosti aktuální certifikát, který ho možné zkontrolovat, včetně datum vypršení platnosti.

### <a name="application-configuration"></a>Konfigurace aplikace
Koncová část poskytuje dokumentaci a/nebo ovládací prvky potřebné k konfigurovat vlastní aplikace použít Azure Active Directory jako zprostředkovatele identity.

**Konfigurovat aplikace** rozevírací nabídce poskytuje nové stručný a vložené pokyny ke konfiguraci aplikace. Toto je další novou funkcí, které jsou jedinečné pro nový Azure portal.

> [!NOTE]
> Úplný příklad vložený dokumentaci prohlédněte si aplikaci Salesforce.com. Neustále se přidávají dokumentaci i další aplikace.
> 
> 

![Vložená dokumentace](./media/configure-single-sign-on-portal/enterprise-apps-blade-embedded-docs.png)

## <a name="password-based-sign-on"></a>Přihlašování na základě hesla
Pokud se podporuje pro aplikaci, vyberte režim jednotného přihlašování pomocí hesla a vyberete **Uložit** okamžitě nakonfiguruje ho, aby se jednotné přihlašování pomocí hesla. Další informace o nasazení jednotného přihlašování pomocí hesla, naleznete v tématu [jak funguje jednotné přihlašování pomocí Azure Active Directory](what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work).

![Přihlašování na základě hesla](./media/configure-single-sign-on-portal/enterprise-apps-blade-password-sso.png)

## <a name="linked-sign-on"></a>Propojené přihlašování
Pokud se podporuje pro aplikaci, vyberete propojený režim jednotného přihlašování umožňuje zadejte adresu URL, kterou chcete přístupový Panel Azure AD nebo Office 365 pro přesměrování v případě kliknutí na tuto aplikaci. Další informace o propojené přihlášení SSO (dříve označované jako "existující jednotné přihlašování"), najdete v části [jak funguje jednotné přihlašování pomocí Azure Active Directory](what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work).

![Propojené přihlašování](./media/configure-single-sign-on-portal/enterprise-apps-blade-linked-sso.png)

## <a name="feedback"></a>Váš názor

Věříme, že jste například vylepšenou prostředí Azure AD. Nechte prosím už zpětnou vazbu! Publikovat vaše názory a návrhy pro zlepšení **portál pro správu** část naší [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Jsme už nadšený, vytváření nových co skvělého každý den a používat vaše pokyny na obrazec a definovat, co se máme zaměřit příště.

