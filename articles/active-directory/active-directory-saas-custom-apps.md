---
title: Konfigurovat Azure AD jednotného přihlašování pro aplikace | Microsoft Docs
description: Zjistěte, jak k samoobslužné připojit aplikace do Azure Active Directory pomocí SAML a jednotné přihlašování založené na heslech
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: mtillman
ms.assetid: 0d42eb0c-6d3f-4557-9030-e88e86709a19
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/20/2018
ms.author: asmalser
ms.reviewer: luleon
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aeb67a73a69684b89609c6b04160357b244f62b7
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754447"
---
# <a name="configure-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>Konfigurovat jednotné přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory

Tento článek se týká funkce, která umožňuje správci nakonfigurovat jednotné přihlašování k aplikacím, které nejsou k dispozici v galerii aplikací Azure Active Directory *bez nutnosti psaní kódu*. Tato funkce byla vydána z 18. listopadu 2015 technical preview a je součástí [Azure Active Directory Premium](fundamentals/active-directory-whatis.md). Pokud místo toho hledáte Průvodce pro vývojáře o tom, jak integrovat vlastních aplikací s Azure AD prostřednictvím kódu, přečtěte si téma [scénáře ověřování pro Azure AD](active-directory-authentication-scenarios.md).

Poskytuje seznam aplikací, které jsou známé pro podporu forma jednotné přihlašování s Azure Active Directory, jak je popsáno v galerii aplikací Azure Active Directory [v tomto článku](manage-apps/what-is-single-sign-on.md). Jakmile (jako IT specialista nebo systémový integrátor ve vaší organizaci) naleznete na aplikaci, kterou chcete připojit, můžete začít používat podle podrobné pokyny uvedené na portálu Azure umožňující jednotného přihlašování.

Zákazníci s [Azure Active Directory Premium](fundamentals/active-directory-whatis.md) licenci získat také tyto další funkce:

* Samoobslužné integrace každou aplikaci, která podporuje poskytovatele identity SAML 2.0 (spouštěná SP nebo spouštěná IdP)
* Samoobslužné integrace webové aplikace, který má k HTML na přihlašovací stránce pomocí [jednotné přihlašování založené na heslech](manage-apps/what-is-single-sign-on.md#password-based-single-sign-on)
* Připojení aplikace, které používají protokol SCIM pro zřizování uživatelů samoobslužné služby ([zde popsané](manage-apps/use-scim-to-provision-users-and-groups.md))
* Umožňuje přidat odkazy na všechny aplikace v [Spouštěč aplikace Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) nebo [přístupový panel Azure AD](manage-apps/what-is-single-sign-on.md#deploying-azure-ad-integrated-applications-to-users)

To může zahrnovat pouze aplikace SaaS, které můžete použít, ale nebyly dosud byl na zahrnuté pro galerii aplikací Azure AD, ale třetí strany webových aplikací, které vaše organizace má nasazené servery, které řídíte, buď v cloudu nebo místně.

Tyto možnosti, také známé jako *šablony integrace aplikace*, poskytovat založených na standardech spojovací body pro aplikace, které podporují SAML, SCIM nebo ověřování pomocí formulářů a zahrnují flexibilní možnosti a nastavení pro Kompatibilita s široký počet aplikací. 

## <a name="adding-an-unlisted-application"></a>Přidání aplikace neuvedené
K připojení aplikace pomocí šablony integrace aplikaci, přihlaste se k portálu Azure pomocí účtu správce služby Azure Active Directory. Vyhledejte **služby Active Directory > podnikové aplikace, které > novou aplikaci > Non Galerie aplikace** vyberte **přidat**a potom **přidat aplikaci z Galerie** .

  ![](./media/active-directory-saas-custom-apps/customapp1.png)

V galerii aplikací můžete přidat aplikaci neuvedené výběrem **aplikace bez Galerie** dlaždice, které se zobrazí ve výsledcích hledání, pokud nebyla nalezena požadovaná aplikace. Po zadání název vaší aplikace, můžete nakonfigurovat, volby jednotného přihlašování a chování. 

**Rychlý tip**: jako osvědčený postup, zkontrolujte, jestli aplikace už existuje v galerii aplikací pomocí funkce vyhledávání. Pokud je nalezen aplikace a její popis uvádí jednotné přihlašování, pak je již nepodporuje aplikace pro federované jednotné přihlašování.

  ![](./media/active-directory-saas-custom-apps/customapp2.png)

Přidání aplikace tímto způsobem nabízí prostředí podobné tomu, které jsou k dispozici pro předběžně integrované aplikace. Chcete-li začít, vyberte **nakonfigurovat jednotné přihlašování** nebo klikněte na **jednotného přihlašování** z levé navigační nabídky. Na další obrazovce nabízí možnosti pro konfiguraci jednotné přihlašování. Tyto možnosti jsou popsány v následujících částech tohoto článku.
  
![](./media/active-directory-saas-custom-apps/customapp3.png)

## <a name="saml-based-single-sign-on"></a>Jednotné přihlašování pomocí SAML
Vyberte tuto možnost konfigurace založené na SAML ověřování pro aplikaci. To vyžaduje, aby aplikace podporují SAML 2.0. By shromažďovat informace o tom, jak používat funkce SAML aplikace než budete pokračovat. Přečtěte si následující části nakonfigurovat jednotné přihlašování mezi aplikací a Azure AD.

### <a name="enter-basic-saml-configuration"></a>Zadejte základní konfigurace SAML

Chcete-li nastavit Azure AD, zadejte základní konfigurace SAML. Můžete ručně zadat hodnoty nebo nahrát soubor metadat získání hodnoty polí.

  ![Litware domény a adresy URL](./media/active-directory-saas-custom-apps/customapp4.png)

- **Přihlašovací adresa URL (spouštěná SP pouze)** – Pokud uživatel přejde na přihlásit se k této aplikaci. Pokud aplikace je nakonfigurovaná k provedení služby spouštěná zprostředkovatele jednotné přihlašování, a poté když uživatel přejde na tuto adresu URL, na poskytovatele služby bude proveďte potřebné přesměrování do služby Azure AD k ověření a přihlaste se s uživatelem v. Pokud toto pole je vyplněný, budou Azure AD používat tuto adresu URL pro spuštění aplikace z Office 365 a Azure AD přístupového panelu. Pokud toto pole je tento parametr vynechán, a poté Azure AD bude místo toho proveďte zprostředkovatele identity-iniciované přihlašování při spuštění aplikace z Office 365 přístupový Panel Azure AD, nebo z Azure AD přihlášení adresu jednotného (kopírovatelná z karty řídicí panel).
- **Identifikátor** -musí jednoznačně identifikovat aplikace, pro který jednotné přihlašování je konfigurován. Můžete najít tuto hodnotu jako element vystavitele v AuthRequest (žádost SAML) odeslaných aplikací. Tato hodnota se rovněž zobrazuje jako **Entity ID** v veškerá metadata SAML poskytuje aplikace. Podívejte se do dokumentace aplikace SAML podrobnosti na to, co je jeho hodnota Entity ID nebo cílovou skupinu. 

    Následuje příklad, jak zobrazuje identifikátor nebo vystavitele SAML požadavek odeslaný aplikací do Azure AD:

    ```
    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>
    ```

- **Adresa URL odpovědi** – adresa URL odpovědi je, kde se předpokládá, že aplikace přijímat tokenu SAML. Tím se také označuje jako adresu URL Assertion příjemce Service (ACS). Podívejte se do dokumentace aplikace SAML podrobnosti na to, co je odpovědět tokenu SAML adresu URL nebo adresa URL služby ACS. 

    Konfigurace více replyURLs můžete použít následující skript prostředí PowerShell.

    ```PowerShell
    $sp = Get-AzureADServicePrincipal -SearchString "<Exact App  name>"
    $app = Get-AzureADApplication -SearchString "<Exact app name>"
    Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls "<ReplyURLs>"
    Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls "<ReplyURLs>"
    ```

Další informace najdete v tématu [žádosti o ověření SAML 2.0 a odpovědi, které podporuje Azure Active Directory (Azure AD)](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML)


### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>Zkontrolovat nebo přizpůsobit deklarace identity vystavené v tokenu SAML

Když se uživatel přihlásí k aplikaci, Azure AD vydá SAML token aplikaci, která obsahuje informace (nebo deklarace identity) o uživatele, který jednoznačně identifikuje je. Ve výchozím nastavení to zahrnuje uživatelské jméno, e-mailová adresa, jméno a příjmení uživatele. 

Můžete zobrazit nebo upravit deklarace identity odeslat v tokenu SAML, aby aplikace v rámci **atributy** kartě.

  ![](./media/active-directory-saas-custom-apps/customapp7.png)

Existují dva důvody, proč je potřeba upravit deklarace identity vystavené v tokenu SAML:

- Aplikace byla zapsána vyžadují jinou sadu deklarací identity identifikátory URI nebo hodnot deklarací identity.
- Aplikace byly nasazeny způsobem, který vyžaduje NameIdentifier deklarace identity na něco jiného než uživatelské jméno (hlavní název uživatele NEBOLI) uložené ve službě Azure Active Directory. 

Další informace najdete v tématu [přizpůsobení deklarace identity vystavené v tokenu SAML pro podnikové aplikace](./develop/active-directory-saml-claims-customization.md). 



### <a name="review-certificate-expiration-data-status-and-email-notification"></a>Zkontrolujte data vypršení platnosti certifikátu, stav a e-mailových oznámení

Při vytváření galerie nebo Non-galerii aplikací Azure AD vytvoří certifikát specifické pro aplikaci s datum vypršení platnosti 3 let od data vytvoření. Je nutné tento certifikát nastavit vztah důvěryhodnosti mezi službou Azure AD a aplikace. Podrobnosti o formát certifikátu najdete v dokumentaci aplikace SAML. 

Z Azure AD můžete si stáhnout certifikát v Base64 nebo v nezpracovaném formátu. Kromě toho můžete získat certifikát tak, že stáhnete soubor XML metadata aplikace nebo pomocí adresu URL federační metadata aplikace.

  ![Certifikát](./media/active-directory-saas-custom-apps/certificate.png)

Ověřte, zda že je certifikát:

- Datum vypršení platnosti požadované. Můžete nakonfigurovat datum vypršení platnosti maximálně 3 roky.
- Ve stavu aktivní. Pokud je ve stavu neaktivní, stav změníte na aktivní. Chcete-li změnit stav, zkontrolujte **Active** a pak konfiguraci uložte. 
- E-mailové oznámení správné. Pokud aktivní certifikát se blíží datum vypršení platnosti, Azure AD bude odesláno upozornění e-mailovou adresu nakonfigurované v tomto poli.  

Další informace najdete v tématu [spravovat certifikáty pro federované jednotné přihlašování v Azure Active Directory](manage-apps/manage-certificates-for-federated-single-sign-on.md).

### <a name="set-up-target-application"></a>Nastavení cílové aplikace

Konfigurace aplikace pro jednotné přihlašování, vyhledejte dokumentaci k aplikaci. Pokud chcete vyhledat v dokumentaci, přejděte na konci stránky přihlašování v konfiguraci na základě SAML a potom klikněte na **konfigurace <application name>** . 

Požadované hodnoty lišit v závislosti na aplikaci. Podrobnosti najdete v dokumentaci k aplikace SAML. Přihlášení a odhlášení adresu URL služby i odkazující na stejné koncového bodu, což je koncový bod SAML zpracování požadavků pro vaše instance služby Azure AD. SAML Entity ID je hodnota, která se zobrazí jako vydavatel v tokenu SAML, který byl vydán pro aplikace.


### <a name="assign-users-and-groups-to-your-saml-application"></a>Přiřazení uživatelů a skupin do vaší aplikace SAML

Jakmile vaše aplikace byla nakonfigurovaná pro použití Azure AD jako zprostředkovatele identity na základě SAML, je téměř připraveni otestovat. Jako ovládací prvek zabezpečení Azure AD nevydá token umožňuje uživatelům přihlášení do aplikace, pokud Azure AD udělil přístup pro uživatele. Uživatelé mohou mít udělen přístup přímo nebo prostřednictvím členství ve skupinách. 

Chcete-li přiřadit uživatele nebo skupiny do vaší aplikace, klikněte na tlačítko **přiřadit uživatele** tlačítko. Vyberte uživatele nebo skupiny, které chcete přiřadit a pak vyberte **přiřadit** tlačítko.

  ![](./media/active-directory-saas-custom-apps/customapp6.png)

Přiřazení uživatele vám umožní vystavit token pro uživatele Azure AD. Také způsobuje, že dlaždice pro tuto aplikaci, než se objeví na Panel přístupu uživatele. Dlaždici aplikace se také zobrazí v Spouštěč aplikace Office 365, pokud uživatel používá Office 365. 

> [!NOTE] 
> Můžete nahrávat logo dlaždice pro aplikace pomocí **nahrát Logo** na tlačítko **konfigurace** kartě pro aplikaci. 


### <a name="test-the-saml-application"></a>Testování aplikace SAML

Před testováním aplikace SAML, musíte mít nastavení aplikace s Azure AD a přiřadit uživatele nebo skupiny do aplikace. K otestování aplikace SAML, najdete v části [ladění na základě SAML jednotného přihlašování k aplikacím v Azure Active Directory](develop/active-directory-saml-debugging.md).

## <a name="password-single-sign-on"></a>Heslo jednotné přihlašování

Vyberte tuto možnost, chcete-li nakonfigurovat [založené na heslech jednotné přihlašování](manage-apps/what-is-single-sign-on.md) pro webovou aplikaci, která má přihlašovací stránku HTML. Jednotné přihlašování založené na heslech, také označuje jako heslo vaulting, můžete spravovat přístup uživatelů a hesla k webovým aplikacím, které nepodporují federaci identit. Je také užitečné v případech, kdy je potřeba několik uživatelé sdílet jeden účet, například k účtům aplikace sociálních médií vaší organizace. 

Po výběru **Další**, zobrazí se výzva k zadání adresu URL aplikace založené na webu přihlašovací stránky. Všimněte si, že to musí být stránky, která obsahuje vstupní pole uživatelské jméno a heslo. Jakmile se zadá, Azure AD spustí proces analyzovat přihlašovací stránce zadejte uživatelské jméno a heslo zadejte. Pokud proces není úspěšné, pak provede vás alternativní procesem instalace rozšíření prohlížeče (vyžaduje Internet Explorer, Chrome nebo Firefox), které vám umožní ručně zaznamenat pole.

Jakmile zachytí přihlašovací stránku, může přiřadit uživatele a skupiny a zásady přihlašovacích údajů můžete nastavit stejně jako regulární [heslo jednotného přihlašování k aplikacím](manage-apps/what-is-single-sign-on.md).

> [!NOTE] 
> Můžete nahrávat logo dlaždice pro aplikace pomocí **nahrát Logo** na tlačítko **konfigurace** kartě pro aplikaci. 
>

## <a name="existing-single-sign-on"></a>Existující jednotné přihlašování
Vyberte tuto možnost, chcete-li přidat odkaz na aplikaci na portál přístupový Panel Azure AD nebo Office 365 vaší organizace. To můžete použít k přidání odkazů na vlastní webové aplikace, které používají Azure Active Directory Federation Services (nebo jiné služby federation service) namísto Azure AD pro ověřování. Nebo můžete přidat přímé odkazy na konkrétní stránky SharePoint nebo jiných webových stránek, které chcete zobrazit na panelů přístup uživatelů. 

Po výběru **Další**, zobrazí se výzva k zadání adresy URL aplikace má být propojen s. Po dokončení, uživatelů a skupin může být přiřazen aplikaci, které způsobí, že aplikace se zobrazí v [Spouštěč aplikace Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) nebo [přístupový panel Azure AD](manage-apps/what-is-single-sign-on.md#deploying-azure-ad-integrated-applications-to-users) pro tyto uživatele.

> [!NOTE] 
> Můžete nahrávat logo dlaždice pro aplikace pomocí **nahrát Logo** na tlačítko **konfigurace** kartě pro aplikaci. 
>

## <a name="related-articles"></a>Související články

- [Rejstřík článků o správě aplikací ve službě Azure Active Directory](active-directory-apps-index.md)
- [Postup přizpůsobení deklarace identity vystavené v tokenu SAML pro předběžně integrované aplikace](active-directory-saml-claims-customization.md)
- [Řešení potíží s na základě SAML jednotné přihlašování](active-directory-saml-debugging.md)

