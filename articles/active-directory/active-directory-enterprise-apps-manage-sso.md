---
title: Jednotné přihlašování správy firemních aplikací ve službě Azure Active Directory | Microsoft Docs
description: Spravovat nastavení jednotného přihlašování pro podnikové aplikace v rámci vaší organizace z galerii aplikací Azure Active Directory
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: barbkess
ms.reviewer: asmalser
ms.openlocfilehash: ee8f30bc81d3c3bd0bf4659359a6ca3a9280f734
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="managing-single-sign-on-for-enterprise-apps"></a>Správa jednotného přihlašování pro podnikové aplikace

Tento článek popisuje postup použití [portál Azure](https://portal.azure.com) ke správě nastavení jednotného přihlašování pro podnikové aplikace. Podnikové aplikace jsou aplikace, které jsou nasazené a použít v rámci vaší organizace. Tento článek se týká hlavně na aplikace, které byly přidány z [galerii aplikací Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). 

## <a name="finding-your-apps-in-the-portal"></a>Vyhledání aplikace v portálu
Všechny podnikové aplikace, které jsou nastavené pro jednotné přihlašování lze zobrazit a spravovat na portálu Azure. Aplikace lze nalézt v **všechny služby** &gt; **podnikové aplikace, které** části portálu. 

![Okno podnikové aplikace][1]

Vyberte **všechny aplikace** Chcete-li zobrazit seznam všech aplikací, které byly nakonfigurovány. Výběr aplikace zobrazí prostředky pro tuto aplikaci, kde je možné zobrazit sestavy pro tuto aplikaci a lze je spravovat celou řadu nastavení.

Chcete-li spravovat nastavení jednotného přihlašování, vyberte **jednotného přihlašování**.

![Okna prostředků aplikace][2]

## <a name="single-sign-on-modes"></a>Režimy přihlášení
**Jednotné přihlašování** začíná **režimu** nabídce, která umožňuje jeden přihlašování v režimu nakonfigurovat. Mezi dostupné možnosti patří:

* **Na základě SAML přihlašování** – tato možnost je dostupná, pokud aplikace podporuje úplné federované jednotné přihlašování s Azure Active Directory pomocí protokolu SAML 2.0, WS-Federation, nebo OpenID connect protokoly.
* **Založené na heslech přihlašování** – tato možnost je dostupná, pokud Azure AD podporuje formulář hesla naplnění pro tuto aplikaci.
* **Propojené přihlášení** -dříve označované jako "Stávající single sign-on", tato možnost umožňuje správcům umístit odkaz na tuto aplikaci Spouštěč aplikace jejich uživatele přístupový Panel Azure AD nebo Office 365.

Další informace o těchto režimech najdete v tématu [jak funguje jednotné přihlašování pomocí služby Azure Active Directory pracovní](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="saml-based-sign-on"></a>Na základě SAML přihlášení
**Na základě SAML přihlašování** možnost rozdělí na čtyři části:

### <a name="domains-and-urls"></a>Domén a adres URL
Toto je, kde jsou přidány všechny podrobnosti o domény a adresy URL aplikace do svého adresáře Azure AD. Všechny vstupy potřebné k vytvoření aplikace pracovní přihlášení se zobrazí přímo na obrazovce, zatímco všechny volitelné vstupy můžete zobrazit výběrem **zobrazit upřesňující nastavení adresy URL** zaškrtávací políčko. Úplný seznam podporovaných vstupy zahrnuje:

* **Adresa URL přihlašování** – Pokud uživatel přejde k přihlášení do aplikace. Pokud aplikace je nakonfigurovaná k provedení služby spouštěná zprostředkovatele jednotné přihlašování, když uživatel otevře tato adresa URL, na poskytovatele služby přesměruje do služby Azure AD k ověření a přihlášení uživatele. 
  * Pokud toto pole se vyplní, Azure AD používá adresu URL pro spuštění aplikace z Office 365 a Azure AD přístupového panelu.
  * Pokud toto pole je vynechán, pak Azure AD místo toho provede identity spouštěná zprostředkovatele přihlášení, při spuštění aplikace z Office 365 přístupový Panel Azure AD, nebo z Azure AD jeden přihlašování adresy URL.
* **Identifikátor** -tento identifikátor URI musí jednoznačně identifikovat aplikace, pro který jednotné přihlašování je konfigurován. Toto je hodnota, která odešle Azure AD zpátky do aplikace jako parametr cílové skupiny tokenu SAML a aplikace se očekává ověřte ji. Tato hodnota se také zobrazí jako ID Entity v veškerá metadata SAML poskytuje aplikace.
* **Adresa URL odpovědi** – adresa URL odpovědi je, kde se předpokládá, že aplikace přijímat tokenu SAML. Tím se také označuje jako adresu URL Assertion příjemce Service (ACS). Po zadání těchto kliknutím na tlačítko Další pokračujte na další obrazovce. Tato obrazovka poskytuje informace o co je potřeba nakonfigurovat na straně aplikace povolit pro přijetí tokenu SAML z Azure AD.
* **Stav předávání** -stav předávání je volitelný parametr, který vám pomůže v aplikaci umožňují přesměruje uživatele po dokončení ověřování. Obvykle hodnota je platná adresa URL v aplikaci, ale některé aplikace používají toto pole jinak (podrobnosti najdete v aplikace jednoho přihlášení dokumentaci). Možnost nastavit stav předávání je nová funkce, které jsou jedinečné pro nový portál Azure.

### <a name="user-attributes"></a>Uživatelské atributy
Toto je, kde můžou správci zobrazit a upravit atributy, které se odesílají v tokenu SAML, která vydává Azure AD pro každou aplikaci, že přihlášení.

Podporováno pouze upravovat atribut je **uživatelský identifikátor** atribut. Hodnota tohoto atributu je pole ve službě Azure AD, která jednoznačně identifikuje každého uživatele v aplikaci. Například pokud byla aplikace nasazená pomocí "e-mailovou adresu" jako uživatelské jméno a jedinečný identifikátor, pak hodnota se nastavuje na pole "user.mail" ve službě Azure AD.

### <a name="saml-signing-certificate"></a>Certifikát pro podpis SAML
Tato část uvádí podrobnosti o certifikát používaný k podepisování tokenů SAML, které jsou vydány pokaždé, když se uživatel ověřuje aplikace Azure AD. To umožňuje vlastnosti aktuální certifikát, který má být prověřovány, včetně datum vypršení platnosti.

### <a name="application-configuration"></a>Konfigurace aplikace
Koncová část poskytuje dokumentaci a ovládací prvky, které jsou potřeba ke konfiguraci vlastní aplikace použít Azure Active Directory jako zprostředkovatele identity.

**Konfigurace aplikace** rozevírací nabídce poskytuje nové stručný a embedded pokyny ke konfiguraci aplikace. To je další novou funkcí, které jsou jedinečné pro nový portál Azure.

> [!NOTE]
> Úplný příklad embedded dokumentace najdete v části aplikace Salesforce.com. Průběžně je přidáván dokumentace pro další aplikace.
> 
> 

![Vložené dokumentace][3]

## <a name="password-based-sign-on"></a>Založené na heslech přihlášení
Pokud pro aplikaci podporován, vyberete režim jednotné přihlašování založené na heslech a vyberete **Uložit** okamžitě nakonfiguruje jejich jednotné přihlašování založené na heslech. Další informace o nasazení založené na heslech jednotného přihlašování najdete v tématu [jak funguje jednotné přihlašování pomocí služby Azure Active Directory pracovní](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Založené na heslech přihlášení][4]

## <a name="linked-sign-on"></a>Propojené přihlášení
Pokud podporován pro aplikaci, výběr propojené režimu jednotné přihlašování umožňuje zadat adresu URL, kterou chcete přístupový Panel Azure AD nebo Office 365 pro přesměrování na, když uživatelé kliknou na tuto aplikaci. Další informace o propojené přihlášení SSO (dříve označované jako "stávající SSO") najdete v tématu [jak funguje jednotné přihlašování pomocí služby Azure Active Directory pracovní](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Propojené přihlášení][5]

## <a name="feedback"></a>Váš názor

Věříme, že je jako vylepšenou prostředí Azure AD. Prosím udržovat zpětnou vazbu, než dorazí! POST vaše názory a návrhy pro zlepšení **portál pro správu** části našich [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Jsme se vzrušení o vytváření nástrojů nové vlastní položky každý den a použijte vaše pokyny na obrazec a definovat, co se máme zaměřit příště.

[1]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade.PNG
[2]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-sso-blade.PNG
[3]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-embedded-docs.PNG
[4]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-password-sso.PNG
[5]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-linked-sso.PNG
