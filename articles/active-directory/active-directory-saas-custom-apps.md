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
ms.openlocfilehash: 8b76809e615174e7c4e118c6043c8f3fbef3ee94
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="configuring-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory
Tento článek se týká funkce, která umožňuje správci nakonfigurovat jednotné přihlašování k aplikacím, které nejsou k dispozici v galerii aplikací Azure Active Directory *bez nutnosti psaní kódu*. Tato funkce byla vydána z 18. listopadu 2015 technical preview a je součástí [Azure Active Directory Premium](active-directory-whatis.md). Pokud místo toho hledáte Průvodce pro vývojáře o tom, jak integrovat vlastních aplikací s Azure AD prostřednictvím kódu, přečtěte si téma [scénáře ověřování pro Azure AD](active-directory-authentication-scenarios.md).

Poskytuje seznam aplikací, které jsou známé pro podporu forma jednotné přihlašování s Azure Active Directory, jak je popsáno v galerii aplikací Azure Active Directory [v tomto článku](active-directory-appssoaccess-whatis.md). Jakmile (jako IT specialista nebo systémový integrátor ve vaší organizaci) naleznete na aplikaci, kterou chcete připojit, můžete začít používat podle podrobné pokyny uvedené na portálu Azure umožňující jednotného přihlašování.

Zákazníci s [Azure Active Directory Premium](active-directory-whatis.md) licenci získat také tyto další funkce:

* Samoobslužné integrace každou aplikaci, která podporuje poskytovatele identity SAML 2.0 (spouštěná SP nebo spouštěná IdP)
* Samoobslužné integrace webové aplikace, který má k HTML na přihlašovací stránce pomocí [jednotné přihlašování založené na heslech](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
* Připojení aplikace, které používají protokol SCIM pro zřizování uživatelů samoobslužné služby ([zde popsané](active-directory-scim-provisioning.md))
* Umožňuje přidat odkazy na všechny aplikace v [Spouštěč aplikace Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) nebo [přístupový panel Azure AD](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)

To může zahrnovat pouze aplikace SaaS, které můžete použít, ale nebyly dosud byl na zahrnuté pro galerii aplikací Azure AD, ale třetí strany webových aplikací, které vaše organizace má nasazené servery, které řídíte, buď v cloudu nebo místně.

Tyto možnosti, také známé jako *šablony integrace aplikace*, poskytovat založených na standardech spojovací body pro aplikace, které podporují SAML, SCIM nebo ověřování pomocí formulářů a zahrnuje nastavení pro kompatibilitu s mnoho aplikací a flexibilní možnosti. 

## <a name="adding-an-unlisted-application"></a>Přidání aplikace neuvedené
K připojení aplikace pomocí šablony integrace aplikaci, přihlaste se k portálu Azure pomocí účtu správce služby Azure Active Directory a přejděte do **služby Active Directory > podnikové aplikace, které > nové aplikace > Aplikace bez Galerie** vyberte **přidat**a potom **přidat aplikaci z Galerie**.

  ![][1]

V galerii aplikací můžete přidat aplikaci neuvedené výběrem **aplikace bez Galerie** dlaždice, které se zobrazí ve výsledcích hledání, pokud nebyla nalezena požadovaná aplikace. Po zadání název vaší aplikace, můžete nakonfigurovat, volby jednotného přihlašování a chování. 

**Rychlý tip**: jako osvědčený postup, zkontrolujte, jestli aplikace už existuje v galerii aplikací pomocí funkce vyhledávání. Pokud je nalezen aplikace a její popis uvádí "single sign-on", pak je již nepodporuje aplikace pro federované jednotné přihlašování.

  ![][2]

Přidání aplikace tímto způsobem, poskytuje možnosti velmi podobné tomu, které jsou k dispozici pro předběžně integrované aplikace. Chcete-li začít, vyberte **nakonfigurovat jednotné přihlašování**. Na další obrazovce uvede následující tři možnosti pro konfiguraci jednotné přihlašování, které jsou popsány v následujících částech.

  ![][3]

## <a name="saml-based-sign-on"></a>Přihlášení na základě SAML
Vyberte tuto možnost konfigurace založené na SAML ověřování pro aplikaci. To vyžaduje, aby podporu aplikace SAML 2.0 a by měl shromažďovat informace o tom, jak používat funkce SAML aplikace než budete pokračovat. Po výběru **Další**, zobrazí se výzva k zadání tři různé adresy URL odpovídající koncových bodů SAML pro aplikaci.

  ![][4]

Jsou to:

* **Přihlašovací adresa URL (spouštěná SP pouze)** – Pokud uživatel přejde na přihlásit se k této aplikaci. Pokud aplikace je nakonfigurovaná k provedení služby spouštěná zprostředkovatele jednotné přihlašování, a poté když uživatel přejde na tuto adresu URL, na poskytovatele služby bude proveďte potřebné přesměrování do služby Azure AD k ověření a přihlaste se s uživatelem v. Pokud toto pole je vyplněný, budou Azure AD používat tuto adresu URL pro spuštění aplikace z Office 365 a Azure AD přístupového panelu. Pokud toto pole je tento parametr vynechán, a poté Azure AD bude místo toho proveďte zprostředkovatele identity-iniciované přihlašování při spuštění aplikace z Office 365 přístupový Panel Azure AD, nebo z Azure AD přihlášení adresu jednotného (kopírovatelná z karty řídicí panel).
* **URL vystavitele** -URL vystavitele musí jednoznačně identifikovat aplikace, pro který jednotné přihlašování je konfigurován. Toto je hodnota, která odešle Azure AD zpátky do aplikace, jako **cílovou skupinu** tokenu SAML a aplikace se očekává, že parametr pro jeho ověření. Tato hodnota se rovněž zobrazuje jako **Entity ID** v veškerá metadata SAML poskytuje aplikace. Podívejte se do dokumentace aplikace SAML podrobnosti na to, co je jeho hodnota Entity ID nebo cílovou skupinu. Dole je příklad, jak se zobrazuje adresu URL cílové skupiny v tokenu SAML vrátí aplikaci:
  
  ```
  <Subject>
    <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:unspecificed">chad.smith@example.com</NameID>
        <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
      </Subject>
      <Conditions NotBefore="2014-12-19T01:03:14.278Z" NotOnOrAfter="2014-12-19T02:03:14.278Z">
        <AudienceRestriction>
          <Audience>https://tenant.example.com</Audience>
        </AudienceRestriction>
      </Conditions>
    ```

* **Adresa URL odpovědi** – adresa URL odpovědi je, kde se předpokládá, že aplikace přijímat tokenu SAML. Tím se také označuje jako **Assertion příjemce Service (ACS) adresy URL**. Podívejte se do dokumentace aplikace SAML podrobnosti na to, co je odpovědět tokenu SAML adresu URL nebo adresa URL služby ACS.
  Po zadání těchto klikněte na tlačítko **Další** pokračovat na další obrazovce. Tato obrazovka poskytuje informace o co je potřeba nakonfigurovat na straně aplikace povolit pro přijetí tokenu SAML z Azure AD.

  * Klikněte na **konfigurace Litware**.
  
    ![][5]

Hodnoty, které jsou požadovány lišit v závislosti na aplikaci, takže podívejte se do dokumentace aplikace SAML podrobnosti. **Přihlašování** a **odhlášení** obě odkazující na stejné koncového bodu, což je koncový bod SAML zpracování požadavků pro vaše instance služby Azure AD adresu URL služby. URL vystavitele je hodnota, která se zobrazí jako "Vystavitele" uvnitř tokenu SAML vystaveno pro aplikace. 

Jakmile vaše aplikace byla nakonfigurovaná, klikněte na tlačítko **Další** tlačítko a potom **Complete** zavřete dialogové okno. 

## <a name="assigning-users-and-groups-to-your-saml-application"></a>Přiřazení uživatelů a skupin k aplikaci SAML
Jakmile vaše aplikace byla nakonfigurovaná pro použití Azure AD jako zprostředkovatele identity na základě SAML, je téměř připraveni otestovat. Jako ovládací prvek zabezpečení Azure AD nevydá token, což jim umožní přihlášení do aplikace, pokud mají udělené přístup pomocí služby Azure AD. Uživatelé mohou mít udělen přístup přímo nebo prostřednictvím skupiny, které jsou členy. 

Chcete-li přiřadit uživatele nebo skupiny do vaší aplikace, klikněte na tlačítko **přiřadit uživatele** tlačítko. Vyberte uživatele nebo skupiny, které chcete přiřadit a pak vyberte **přiřadit** tlačítko.

  ![][6]

Přiřazení uživatele vám umožní vystavit token pro uživatele, jakož i způsobuje dlaždice pro tuto aplikaci, než se objeví v uživatele přístupový Panel Azure AD. Dlaždici aplikace se také zobrazí v Spouštěč aplikace Office 365, pokud uživatel používá Office 365. 

Můžete nahrávat logo dlaždice pro aplikace pomocí **nahrát Logo** na tlačítko **konfigurace** kartě pro aplikaci. 

### <a name="customizing-the-claims-issued-in-the-saml-token"></a>Přizpůsobení deklarace identity vystavené v tokenu SAML
Když se uživatel přihlásí k aplikaci, Azure AD vydá SAML token aplikaci, která obsahuje informace (nebo deklarace identity) o uživatele, který jednoznačně identifikuje je. Ve výchozím nastavení to zahrnuje uživatelské jméno, e-mailová adresa, jméno a příjmení uživatele. 

Můžete zobrazit nebo upravit deklarace identity odeslat v tokenu SAML, aby aplikace v rámci **atributy** kartě.

  ![][7]

Existují dvě možné důvody, proč je potřeba upravit deklarace identity vystavené v tokenu SAML: •cílová aplikace byla zapsána na vyžadují jinou sadu deklarací identity identifikátory URI nebo nasazení aplikace •Your způsobem, který vyžaduje NameIdentifier hodnot deklarací identit deklarací na něco jiného než uživatelské jméno (hlavní název uživatele NEBOLI) uložené ve službě Azure Active Directory. 

Informace o tom, jak přidávat a upravovat deklarace pro tyto scénáře, podívejte se na to [článku o přizpůsobení deklarace identity](active-directory-saml-claims-customization.md). 

### <a name="testing-the-saml-application"></a>Testování aplikace SAML
Jakmile SAML adresy URL a certifikát jsou nakonfigurované v Azure AD a aplikace, uživatele nebo skupiny mají přiřazený k aplikaci v Azure a deklarace identity byly zkontrolovány a v případě potřeby upravit a pak uživatel je připraven k přihlášení do aplikace. 

Chcete otestovat, jednoduše přihlásit se k Azure AD přístupovému panelu na adrese https://myapps.microsoft.com pomocí uživatelského účtu, který jste přiřadili k aplikaci a potom klikněte na dlaždici aplikace, který ji procesu přihlášení. Alternativně můžete vyhledat přímo na adresu URL přihlašování pro aplikace a přihlaste se z ní. 

Ladění tipy, najdete [článek o tom, jak ladit na základě SAML jednotného přihlašování k aplikacím](active-directory-saml-debugging.md) 

## <a name="password-single-sign-on"></a>Heslo jednotné přihlašování
Vyberte tuto možnost, chcete-li nakonfigurovat [založené na heslech jednotné přihlašování](active-directory-appssoaccess-whatis.md) pro webovou aplikaci, která má přihlašovací stránku HTML. Jednotné přihlašování založené na heslech, také označuje jako heslo vaulting, můžete spravovat přístup uživatelů a hesla k webovým aplikacím, které nepodporují federaci identit. Je také užitečné v případech, kdy je potřeba několik uživatelé sdílet jeden účet, například k účtům aplikace sociálních médií vaší organizace. 

Po výběru **Další**, zobrazí se výzva k zadání adresu URL aplikace založené na webu přihlašovací stránky. Všimněte si, že to musí být stránky, která obsahuje vstupní pole uživatelské jméno a heslo. Jakmile se zadá, Azure AD spustí proces analyzovat přihlašovací stránce zadejte uživatelské jméno a heslo zadejte. Pokud proces není úspěšné, pak provede vás alternativní procesem instalace rozšíření prohlížeče (vyžaduje Internet Explorer, Chrome nebo Firefox), které vám umožní ručně zaznamenat pole.

Jakmile zachytí přihlašovací stránku, může přiřadit uživatele a skupiny a zásady přihlašovacích údajů můžete nastavit stejně jako regulární [heslo jednotného přihlašování k aplikacím](active-directory-appssoaccess-whatis.md).

Poznámka: Můžete nahrávat logo dlaždice pro aplikace pomocí **nahrát Logo** na tlačítko **konfigurace** kartě pro aplikaci. 

## <a name="existing-single-sign-on"></a>Existující jednotné přihlašování
Vyberte tuto možnost, chcete-li přidat odkaz na aplikaci na portál přístupový Panel Azure AD nebo Office 365 vaší organizace. To můžete použít k přidání odkazů na vlastní webové aplikace, které používají Azure Active Directory Federation Services (nebo jiné služby federation service) namísto Azure AD pro ověřování. Nebo můžete přidat přímé odkazy na konkrétní stránky SharePoint nebo jiných webových stránek, které chcete zobrazit na panelů přístup uživatelů. 

Po výběru **Další**, zobrazí se výzva k zadání adresy URL aplikace má být propojen s. Po dokončení, uživatelů a skupin může být přiřazen aplikaci, které způsobí, že aplikace se zobrazí v [Spouštěč aplikace Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) nebo [přístupový panel Azure AD](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) pro tyto uživatele.

Poznámka: Můžete nahrávat logo dlaždice pro aplikace pomocí **nahrát Logo** na tlačítko **konfigurace** kartě pro aplikaci.

## <a name="related-articles"></a>Související články
* [Rejstřík článků o správě aplikací ve službě Azure Active Directory](active-directory-apps-index.md)
* [Postup přizpůsobení deklarace identity vystavené v tokenu SAML pro předběžně integrované aplikace](active-directory-saml-claims-customization.md)
* [Řešení potíží s na základě SAML jednotné přihlašování](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saas-custom-apps/customapp1.png
[2]: ./media/active-directory-saas-custom-apps/customapp2.png
[3]: ./media/active-directory-saas-custom-apps/customapp3.png
[4]: ./media/active-directory-saas-custom-apps/customapp4.png
[5]: ./media/active-directory-saas-custom-apps/customapp5.png
[6]: ./media/active-directory-saas-custom-apps/customapp6.png
[7]: ./media/active-directory-saas-custom-apps/customapp7.png