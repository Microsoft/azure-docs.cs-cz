---
title: Konfigurace jednotného přihlašování k Azure AD pro aplikace | Dokumentace Microsoftu
description: Zjistěte, jak připojit aplikace do Azure Active Directory pomocí SAML a jednotné přihlašování pomocí hesla k samoobslužné funkce
services: active-directory
author: barbkess
documentationcenter: na
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/12/2018
ms.author: barbkess
ms.reviewer: asmalser,luleon
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 29a2be1735561d31f934f1353a336b5e4d198227
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622189"
---
# <a name="configure-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory

Tento článek se týká funkce, která umožňuje správci nakonfigurovat jednotné přihlašování k aplikacím není k dispozici v galerii aplikací Azure Active Directory *bez psaní kódu*. Tato funkce byla vydána z 18. listopadu 2015 technical preview a je součástí [Azure Active Directory Premium](../fundamentals/active-directory-whatis.md). Pokud místo toho hledáte informace pro vývojáře o tom, jak integrace vlastních aplikací s Azure AD prostřednictvím kódu, přečtěte si téma [scénáře ověřování pro službu Azure AD](../develop/authentication-scenarios.md).

Galerie aplikací Azure Active Directory poskytuje seznam aplikací, které jsou známé pro podporu formou jednotné přihlašování s Azure Active Directory, jak je popsáno v [v tomto článku](what-is-single-sign-on.md). Jakmile (jako IT specialista nebo systémový integrátor ve vaší organizaci) najdete aplikace, kterou chcete připojit, můžete začít podle podrobných pokynů uvedené na webu Azure Portal můžete povolit jednotné přihlašování.

Zákazníci s [Azure Active Directory Premium](../fundamentals/active-directory-whatis.md) licenci získáte také tyto další funkce:

* Samoobslužná integrace každou aplikaci, která podporuje zprostředkovatelů identity SAML 2.0 (iniciovaného Zprostředkovatelem přihlašování nebo zahájené pomocí IdP)
* Samoobslužná integrace webové aplikace, který je založený na jazyce HTML přihlašovací stránky pomocí [jednotného přihlašování pomocí hesla](what-is-single-sign-on.md#password-based-sso)
* Připojení aplikace, které používají protokol SCIM pro zřizování uživatelů samoobslužné služby ([je zde popsáno,](use-scim-to-provision-users-and-groups.md))
* Možnost přidání odkazů do libovolné aplikace v [Spouštěči aplikací Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) nebo [přístupový panel služby Azure AD](what-is-single-sign-on.md#linked-sso)

To může zahrnovat jenom aplikace SaaS, které můžete použít, ale nebyly dosud byl zprovozněná v galerii aplikací Azure AD, ale třetí strany webových aplikací, které vaše organizace má nasazené na servery, které sami kontrolujete, buď v cloudu nebo místně.

Tyto funkce, označované také jako *šablony pro integraci aplikací*zadejte založené na standardech spojovací body pro aplikace, které podporují ověřování pomocí formulářů, SAML nebo SCIM a flexibilní možnosti a nastavení pro Kompatibilita s širokou řadu aplikací. 

## <a name="adding-an-unlisted-application"></a>Přidání neuvedené aplikace
K připojení aplikace pomocí šablony integrace aplikace, přihlaste se k webu Azure portal pomocí účtu správce Azure Active Directory. Přejděte **služby Active Directory > podnikové aplikace > nové aplikace > aplikace mimo galerii** vyberte **přidat**a potom **přidat aplikaci z Galerie** .

  ![Přidání aplikace](./media/configure-single-sign-on-non-gallery-applications/customapp1.png)

V galerii aplikací můžete přidat neuvedené aplikace tak, že vyberete **aplikace mimo galerii** dlaždici, která je zobrazena ve výsledcích hledání, pokud požadované aplikace se nenašla. Jakmile zadáte název vaší aplikace, můžete nakonfigurovat, volby jednotného přihlašování a chování. 

**Rychlý tip**: jako osvědčený postup, můžete zkontrolovat, jestli aplikace již existuje v galerii aplikací pomocí funkce vyhledávání. Pokud je nalezen aplikace a její popis uvádí jednotného přihlašování, pak aplikaci již podporují pro federované jednotné přihlašování.

  ![Search](./media/configure-single-sign-on-non-gallery-applications/customapp2.png)

Přidání aplikace tímto způsobem poskytuje podobné možnosti jako na jeden k dispozici pro předběžně integrované aplikace. Pokud chcete začít, vyberte **konfigurovat Single Sign-On** nebo klikněte na **jednotného přihlašování** levé navigační nabídce aplikace. Na další obrazovce zobrazí možnosti pro konfiguraci jednotného přihlašování. Tyto možnosti jsou popsány v následujících částech tohoto článku.
  
![Možnosti konfigurace](./media/configure-single-sign-on-non-gallery-applications/customapp3.png)

## <a name="saml-based-single-sign-on"></a>Jednotné přihlašování pomocí SAML
Tuto možnost použijte ke konfiguraci ověřování na základě SAML pro aplikaci. To vyžaduje, aby podporoval aplikace SAML 2.0. Mají shromažďovat informace o tom, jak používat funkce SAML aplikace, než budete pokračovat. Dokončování následujících částí nakonfigurovat jednotné přihlašování mezi aplikací a službou Azure AD.

### <a name="enter-basic-saml-configuration"></a>Zadejte základní konfiguraci SAML

Nastavení Azure AD, zadejte základní konfiguraci SAML. Můžete ručně zadat hodnoty nebo nahrát soubor metadat a extrahovat hodnotu pole.

  ![Společnost litware domény a adresy URL](./media/configure-single-sign-on-non-gallery-applications/customapp4.png)

- **Přihlašovací adresa URL (iniciovaného Zprostředkovatelem přihlašování pouze)** – Pokud uživatel přejde za účelem přihlášení k této aplikaci. Pokud aplikace je nakonfigurovaná k provedení služby zahájené poskytovatelem jednotného přihlašování, když uživatel přejde na tuto adresu URL, poskytovatele služeb provede nezbytné přesměrování do služby Azure AD k ověření a přihlášení uživatele. Pokud toto pole se vyplní, budou služby Azure AD používat tuto adresu URL ke spuštění aplikace z Office 365 a Azure AD přístupového panelu. Pokud toto pole je tento parametr vynechán, pak Azure AD se místo toho provést zprostředkovatele identity – zahájeno přihlašování při spuštění aplikace z Office 365, Azure AD přístupovém panelu, nebo z Azure AD jednotné přihlašování – adresy URL (kopírovatelné z karty řídicí panel).
- **Identifikátor** – aplikace, pro kterou jednotného přihlašování je konfigurován jedinečně identifikoval. Tuto hodnotu jako element Issuer v AuthRequest (požadavku SAML), kterou aplikace odeslala, lze najít. Tato hodnota se rovněž zobrazuje jako **Entity ID** v veškerá metadata SAML poskytovaný aplikací. Naleznete v dokumentaci aplikace SAML podrobnosti o tom, co je jeho hodnota Entity ID nebo cílová skupina. 

    Následuje příklad toho, jak se v odeslané aplikací do služby Azure AD pomocí požadavku SAML zobrazuje identifikátor nebo vystavitele:

    ```
    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>
    ```

- **Adresa URL pro odpověď** – adresa URL odpovědi je, kde se očekává, že aplikace přijímat tokenu SAML. To se také označuje jako adresu URL Assertion Consumer Service (ACS). Naleznete v dokumentaci aplikace SAML podrobnosti o tom, co je jeho token odpověď SAML adresu URL nebo adresa URL služby ACS. 

    Chcete-li nakonfigurovat více adres Replyurl můžete použít následující skript prostředí PowerShell.

    ```PowerShell
    $sp = Get-AzureADServicePrincipal -SearchString "<Exact App  name>"
    $app = Get-AzureADApplication -SearchString "<Exact app name>"
    Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls "<ReplyURLs>"
    Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls "<ReplyURLs>"
    ```

Další informace najdete v tématu [žádosti o ověření SAML 2.0 a odpovědi, které podporuje Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML)


### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>Zkontrolovat nebo přizpůsobit deklarací identity vystavených v tokenu SAML

Když se uživatel přihlásí k aplikaci, Azure AD vydá SAML token aplikaci, která obsahuje informace (nebo deklarace identity) informace o uživateli, který jednoznačně identifikuje je. Ve výchozím nastavení to zahrnuje uživatelské jméno, e-mailová adresa, jméno a příjmení uživatele. 

Při prohlížení nebo úpravách deklarace identity odesílají v tokenu SAML pro aplikaci v rámci **atributy** kartu.

  ![Atributy](./media/configure-single-sign-on-non-gallery-applications/customapp7.png)

Existují dva důvody, proč může být potřeba upravit deklarací identity vystavených v tokenu SAML:

- Aplikace se zapsala do vyžadují jinou sadu deklarací identity identifikátory URI nebo hodnoty deklarací identity.
- Vaše aplikace nasazená způsobem, který vyžaduje deklarace identity NameIdentifier na něco jiného než uživatelské jméno (také označované LLAP hlavní uživatelské jméno) uložená v Azure Active Directory. 

Další informace najdete v tématu [přizpůsobování deklarací identity vystavených v tokenu SAML pro podnikové aplikace](./../develop/../develop/active-directory-saml-claims-customization.md). 



### <a name="review-certificate-expiration-data-status-and-email-notification"></a>Zkontrolujte data vypršení platnosti certifikátu, stav a e-mailové oznámení

Při vytváření galerie nebo aplikace mimo Galerii Azure AD vytvoří certifikát specifické pro aplikaci s datum vypršení platnosti 3 let od data vytvoření. Je nutné tento certifikát k nastavení vztahu důvěryhodnosti mezi službami Azure AD a aplikace. Podrobnosti o formát certifikátu najdete v dokumentaci k vaší aplikace SAML. 

Ze služby Azure AD si můžete stáhnout certifikát Base64 nebo nezpracovaném formátu. Kromě toho můžete získat certifikát stáhnout tento soubor metadat XML aplikace nebo adresa URL federačních metadat aplikace.

  ![Certifikát](./media/configure-single-sign-on-non-gallery-applications/certificate.png)

Ověřte, zda je certifikát:

- Datum vypršení platnosti požadované. Můžete nakonfigurovat datum vypršení platnosti maximálně 3 roky.
- Stav aktivní. Pokud je ve stavu neaktivní, změňte stav na aktivní. Chcete-li změnit stav, zkontrolujte **aktivní** a pak konfiguraci uložte. 
- Správné oznámení e-mailu. Pokud aktivní certifikát blíží datum vypršení platnosti, Azure AD bude odesílat oznámení e-mailovou adresu nakonfigurované v tomto poli.  

Další informace najdete v tématu [Správa certifikátů pro federované jednotné přihlašování v Azure Active Directory](manage-certificates-for-federated-single-sign-on.md).

### <a name="set-up-target-application"></a>Nastavení cílové aplikace

Konfigurace aplikace pro jednotné přihlašování, vyhledejte dokumentaci k aplikaci. Můžete vyhledat dokumentaci, přejděte na konec stránky založené na SAML konfigurace přihlašování a potom klikněte na **konfigurovat <application name>** . 

Požadované hodnoty liší v závislosti na aplikaci. Podrobnosti najdete v dokumentaci k vaší aplikace SAML. Přihlašování a adresy URL odhlašovací služby i přeložit na stejný koncový bod, což je koncový bod zpracování požadavku SAML pro vaši instanci služby Azure AD. SAML Entity ID se hodnota, která se zobrazí jako vystavitele v tokenu SAML vydaném pro aplikaci.


### <a name="assign-users-and-groups-to-your-saml-application"></a>Přiřazení uživatelů a skupin pro aplikace SAML

Jakmile vaše aplikace byla nakonfigurovaná pro použití služby Azure AD jako zprostředkovatele identity založené na SAML, je téměř připraveni k testování. Jako ovládací prvek zabezpečení Azure AD nevydá token umožňuje uživateli přihlásit do aplikace, pokud Azure AD udělil přístup pro uživatele. Uživatelé mohou mít udělen přístup přímo nebo prostřednictvím členství ve skupině. 

Přiřadit uživatele nebo skupinu do vaší aplikace, klikněte na tlačítko **přiřadit uživatele** tlačítko. Vyberte uživatele nebo skupiny, které chcete přiřadit a pak vyberte **přiřadit** tlačítko.

  ![Přiřazení uživatelů](./media/configure-single-sign-on-non-gallery-applications/customapp6.png)

Přiřazení uživatele vám umožní vystavit token pro uživatele Azure AD. Navíc způsobí, že dlaždice pro tuto aplikaci zobrazit uživatele přístupovém panelu. Dlaždici aplikace se také zobrazí ve Spouštěči aplikací Office 365, pokud uživatel používá Office 365. 

> [!NOTE] 
> Můžete nahrát logo dlaždice aplikace s využitím **nahrajte Logo** tlačítko **konfigurovat** kartu pro aplikaci. 


### <a name="test-the-saml-application"></a>Testování aplikace SAML

Před testováním aplikace SAML, musíte mít nastavení aplikace s Azure AD a přiřadit uživatele nebo skupiny k aplikaci. K otestování aplikace SAML, naleznete v tématu [ladění založené na SAML jednotného přihlašování k aplikacím v Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md).

## <a name="password-single-sign-on"></a>Heslo jednotného přihlašování

Tuto možnost použijte ke konfiguraci [založené na heslech jednotného přihlašování](what-is-single-sign-on.md) pro webovou aplikaci, která má přihlašovací stránku HTML. Jednotné přihlašování založené na heslech, také označuje jako heslo vaulting, umožňuje spravovat přístup uživatelů a hesel do webové aplikace, které nepodporují federace identit. Také je užitečná v případech, kdy je potřeba několik uživatelé sdílejí jeden účet, jako například účtům sociálních médií aplikace vaší organizace. 

Po výběru **Další**, zobrazí se výzva k zadání adresu URL vaší aplikace webové přihlašovací stránky. Poznámka: Tato hodnota musí být na stránce, která obsahuje vstupní pole uživatelské jméno a heslo. Po zadání, Azure AD spustí proces k analýze přihlašovací stránku pro zadání uživatelského jména a hesla vstup. Pokud proces není úspěšné, pak se provede vás procesem alternativní postup instalace rozšíření prohlížeče (vyžaduje aplikaci Internet Explorer, Chrome nebo Firefox) vám umožní ručně zachytit pole.

Jakmile jsou zachyceny na přihlašovací stránku, může být přiřazena uživatelům a skupinám a přihlašovacích údajů zásady je možné nastavit stejně jako normální [heslem jednotného přihlašování aplikace](what-is-single-sign-on.md).

> [!NOTE] 
> Můžete nahrát logo dlaždice aplikace s využitím **nahrajte Logo** tlačítko **konfigurovat** kartu pro aplikaci. 
>

## <a name="existing-single-sign-on"></a>Existující jednotné přihlašování
Vyberte tuto možnost, chcete-li přidat odkaz na aplikaci na přístupový Panel Azure AD nebo Office 365 portál vaší organizace. Můžete přidat odkazy na vlastní webové aplikace, které používají Azure Active Directory Federation Services (nebo jiné služby federation service) místo Azure AD pro ověřování. Nebo můžete přidat přímé odkazy na konkrétní stránky SharePoint nebo jiných webových stránek, které chcete zobrazit na přístupových panelech uživatele. 

Po výběru **Další**, zobrazí se výzva k zadání adresu URL aplikace propojení. Po dokončení, uživatelů a skupin může být přiřazen k aplikaci, což způsobí, že aplikace bude zobrazovat v [Spouštěči aplikací Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) nebo [přístupový panel služby Azure AD](end-user-experiences.md) pro tyto uživatele.

> [!NOTE] 
> Můžete nahrát logo dlaždice aplikace s využitím **nahrajte Logo** tlačítko **konfigurovat** kartu pro aplikaci. 
>

## <a name="related-articles"></a>Související články
- [Přizpůsobení deklarací identity vystavených v tokenu SAML pro předběžně integrované aplikace](../develop/active-directory-saml-claims-customization.md)
- [Řešení potíží s založené na SAML jednotného přihlašování](../develop/howto-v1-debug-saml-sso-issues.md)

