---
title: Přizpůsobení deklarací tokenu SAML aplikace Azure AD
titleSuffix: Microsoft identity platform
description: Zjistěte, jak přizpůsobit deklarace vydané v tokenu SAML pro podnikové aplikace ve službě Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: article
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.openlocfilehash: 52779b7ffea0f33676426f145a700c7181cf0bf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263085"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Postup: přizpůsobení deklarací vydaných v tokenu SAML pro podnikové aplikace

Azure Active Directory (Azure AD) dnes podporuje jednotné přihlašování (SSO) s většinou podnikových aplikací, včetně aplikací předem integrovaných v galerii aplikací Azure AD i vlastních aplikací. Když se uživatel ověří do aplikace prostřednictvím Azure AD pomocí protokolu SAML 2.0, Azure AD odešle token do aplikace (prostřednictvím HTTP POST). A potom aplikace ověří a použije token k přihlášení uživatele namísto výzvy k zadání uživatelského jména a hesla. Tyto tokeny SAML obsahují informace o uživateli známé jako *deklarace identity*.

*Deklarace identity* je informace, které poskytovatel identity uvádí o uživateli uvnitř tokenu, který pro tohoto uživatele vydají. V [tokenu SAML](https://en.wikipedia.org/wiki/SAML_2.0)jsou tato data obvykle obsažena v příkazu atributu SAML. Jedinečné ID uživatele je obvykle reprezentováno v předmětu SAML, který se také nazývá identifikátor názvu.

Ve výchozím nastavení Azure AD vydává token SAML `NameIdentifier` do vaší aplikace, která obsahuje deklaraci s hodnotou uživatelského jména uživatele (označované také jako hlavní jméno uživatele) ve službě Azure AD, který můžete jednoznačně identifikovat uživatele. Token SAML také obsahuje další deklarace identity obsahující e-mailovou adresu, jméno a příjmení uživatele.

Chcete-li zobrazit nebo upravit deklarace vydané v tokenu SAML do aplikace, otevřete aplikaci na webu Azure Portal. Potom otevřete část **Atributy uživatele & deklarace identity.**

![Otevření části Atributy uživatele & deklarace identity na webu Azure Portal](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Existují dva možné důvody, proč může být nutné upravit deklarace vydané v tokenu SAML:

* Aplikace `NameIdentifier` vyžaduje, aby deklarace nebo NameID byla něco jiného než uživatelské jméno (nebo hlavní jméno uživatele) uložené ve službě Azure AD.
* Aplikace byla napsána tak, aby vyžadovala jinou sadu identifikátorů URI deklarací nebo hodnot deklarací deklarací.

## <a name="editing-nameid"></a>Úprava nameID

Úprava Názvu ID (hodnota identifikátoru názvu):

1. Otevřete stránku **hodnota identifikátoru názvu.**
1. Vyberte atribut nebo transformaci, kterou chcete u atributu použít. Volitelně můžete zadat formát, který má mít deklarace NameID.

   ![Úprava hodnoty NameID (identifikátor názvu)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID formát

Pokud požadavek SAML obsahuje prvek NameIDPolicy s určitým formátem, pak Azure AD bude respektovat formát v požadavku.

Pokud požadavek SAML neobsahuje prvek pro NameIDPolicy, pak Azure AD vydá NameID s formátem, který zadáte. Pokud není zadán žádný formát Azure AD bude používat výchozí zdrojový formát přidružený ke zdroji deklarace zabezpečení vybrané.

V rozevíracím souboru **Zvolit formát identifikátoru názvu** můžete vybrat jednu z následujících možností.

| NameID formát | Popis |
|---------------|-------------|
| **Výchozí** | Azure AD bude používat výchozí zdrojový formát. |
| **Trvalý** | Azure AD bude používat trvalý jako formát NameID. |
| **Emailaddress** | Azure AD bude používat emailaddress jako formát NameID. |
| **Unspecified** | Azure AD bude používat nespecifikované jako formát NameID. |
| **Kvalifikovaný název domény systému Windows** | Azure AD bude používat WindowsDomainQualifiedName jako formát NameID. |

Přechodné NameID je také podporována, ale není k dispozici v rozevírací nabídce a nelze nakonfigurovat na straně Azure. Další informace o atributu NameIDPolicy naleznete v [tématu Single Sign-On SAML protocol](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Atributy

Vyberte požadovaný zdroj `NameIdentifier` pro deklaraci (nebo NameID). Můžete si vybrat z následujících možností.

| Name (Název) | Popis |
|------|-------------|
| E-mail | E-mailová adresa uživatele |
| Userprincipalname | Hlavní jméno uživatele (UPN) uživatele |
| onpremisessamaccount | Název účtu SAM, který byl synchronizován z místního Azure AD |
| Objectid | Objekty uživatele ve službě Azure AD |
| Employeeid | ID zaměstnance uživatele |
| Rozšíření adresáře | Rozšíření adresářů [synchronizovaná z místní služby Active Directory pomocí synchronizace Azure AD Connect Sync](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Atributy rozšíření 1-15 | Místní atributy rozšíření používané k rozšíření schématu Azure AD |

Další informace naleznete v [tabulce 3: Platné hodnoty ID pro zdroj](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

Můžete také přiřadit všechny konstantní (statické) hodnota jakékoli deklarace identity, které definujete ve službě Azure AD. Chcete-li přiřadit konstantní hodnotu, postupujte podle následujících kroků:

1. Na [webu Azure Portal](https://portal.azure.com/)klikněte v části **Atributy uživatele & deklarací** identity kliknutím na ikonu **Upravit** a upravte deklarace identity.

1. Klikněte na požadovanou deklaraci, kterou chcete upravit.

1. Zadejte konstantní hodnotu bez uvozovek do **atributu Zdroj** podle vaší organizace a klepněte na **tlačítko Uložit**.

    ![Otevření části Atributy uživatele & deklarace identity na webu Azure Portal](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. Konstantní hodnota se zobrazí níže.

    ![Otevření části Atributy uživatele & deklarace identity na webu Azure Portal](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>Speciální nároky - transformace

Můžete také použít funkce transformace deklarací.

| Funkce | Popis |
|----------|-------------|
| **ExtractMailPrefix()** | Odebere příponu domény z e-mailové adresy nebo hlavního jména uživatele. Tím se extrahuje pouze první část uživatelského jména, které prochází joe_smith@contoso.com(například "joe_smith" místo ). |
| **Připojit()** | Připojí atribut k ověřené doméně. Pokud má vybraná hodnota identifikátoru uživatele doménu, extrahuje uživatelské jméno pro připojení vybrané ověřené domény. Pokud například jako hodnotujoe_smith@contoso.comidentifikátoru uživatele vyberete e-mail ( ) a joe_smith@contoso.onmicrosoft.comjako ověřenou doménu vyberete contoso.onmicrosoft.com, bude to mít za následek . |
| **ToLower()** | Převede znaky vybraného atributu na malá písmena. |
| **Toupper()** | Převede znaky vybraného atributu na velká písmena. |

## <a name="adding-application-specific-claims"></a>Přidání deklarací specifických pro aplikaci

Přidání deklarací specifických pro aplikaci:

1. V **části Atributy uživatele & deklarace identity**vyberte **Přidat novou deklaraci a** otevřete stránku Spravovat **deklarace identity uživatelů.**
1. Zadejte **název** deklarací identity. Hodnota není nezbytně nutné sledovat vzor URI, podle specifikace SAML. Pokud potřebujete vzorek identifikátoru URI, můžete jej umístit do pole **Obor názvů.**
1. Vyberte **zdroj,** kde deklarace bude načítat jeho hodnotu. Můžete vybrat atribut uživatele z rozevíracího souboru atributu zdroj nebo použít transformaci na atribut uživatele před jeho vyzařováním jako deklarace.

### <a name="claim-transformations"></a>Transformace deklarací

Použití transformace na atribut uživatele:

1. V **části Spravovat deklaraci**vyberte *transformaci* jako zdroj deklarace, chcete-li otevřít stránku **Spravovat transformaci.**
2. Vyberte funkci z rozbalovací ho souboru transformace. V závislosti na vybrané funkci budete muset zadat parametry a konstantní hodnotu pro vyhodnocení v transformaci. Další informace o dostupných funkcích naleznete v následující tabulce.
3. Chcete-li použít více transformací, klikněte na **Přidat transformaci**. Můžete použít maximálně dvě transformace deklarace. Můžete například nejprve extrahovat předponu e-mailu . `user.mail` Potom proveďte velké písmeno řetězce.

   ![Úprava hodnoty NameID (identifikátor názvu)](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

Následující funkce můžete použít k transformaci deklarací identity.

| Funkce | Popis |
|----------|-------------|
| **ExtractMailPrefix()** | Odebere příponu domény z e-mailové adresy nebo hlavního jména uživatele. Tím se extrahuje pouze první část uživatelského jména, které prochází joe_smith@contoso.com(například "joe_smith" místo ). |
| **Připojit()** | Vytvoří novou hodnotu spojením dvou atributů. Volitelně můžete použít oddělovač mezi dvěma atributy. Pro transformaci deklarace názvu NameID je spojení omezeno na ověřenou doménu. Pokud má vybraná hodnota identifikátoru uživatele doménu, extrahuje uživatelské jméno pro připojení vybrané ověřené domény. Pokud například jako hodnotujoe_smith@contoso.comidentifikátoru uživatele vyberete e-mail ( ) a joe_smith@contoso.onmicrosoft.comjako ověřenou doménu vyberete contoso.onmicrosoft.com, bude to mít za následek . |
| **ToLower()** | Převede znaky vybraného atributu na malá písmena. |
| **Toupper()** | Převede znaky vybraného atributu na velká písmena. |
| **Obsahuje()** | Výstupy atribut nebo konstanta, pokud vstup odpovídá zadané hodnotě. V opačném případě můžete zadat jiný výstup, pokud neexistuje žádná shoda.<br/>Například pokud chcete vyzařovat deklaraci, kde hodnota je e-mailová@contoso.comadresa uživatele, pokud obsahuje doménu " ", jinak chcete výstup hlavního jména uživatele. Chcete-li to provést, nakonfigurujte následující hodnoty:<br/>*Parametr 1(vstup)*: user.email<br/>*Hodnota*:@contoso.com"<br/>Parametr 2 (výstup): user.email<br/>Parametr 3 (výstup, pokud neexistuje žádná shoda): user.userprincipalname |
| **EndWith()** | Vyvýstupy atribut nebo konstantu, pokud vstup končí zadanou hodnotou. V opačném případě můžete zadat jiný výstup, pokud neexistuje žádná shoda.<br/>Například pokud chcete vyzařovat deklaraci, kde hodnota je ID zaměstnance uživatele, pokud ID zaměstnance končí "000", jinak chcete výstup atribut rozšíření. Chcete-li to provést, nakonfigurujte následující hodnoty:<br/>*Parametr 1(vstup)*: user.employeeid<br/>*Hodnota*: "000"<br/>Parametr 2 (výstup): user.employeeid<br/>Parametr 3 (výstup, pokud neexistuje žádná shoda): user.extensionattribute1 |
| **Startwith()** | Výstupy atribut nebo konstanta, pokud vstup začíná zadanou hodnotou. V opačném případě můžete zadat jiný výstup, pokud neexistuje žádná shoda.<br/>Například pokud chcete vyzařovat deklaraci, kde hodnota je ID zaměstnance uživatele, pokud země nebo oblast začíná "USA", jinak chcete výstup atribut rozšíření. Chcete-li to provést, nakonfigurujte následující hodnoty:<br/>*Parametr 1(vstup)*: user.country<br/>*Hodnota*: "US"<br/>Parametr 2 (výstup): user.employeeid<br/>Parametr 3 (výstup, pokud neexistuje žádná shoda): user.extensionattribute1 |
| **Extract() - Po spárování** | Vrátí podřetězec za zadanou hodnotou.<br/>Například pokud je hodnota vstupu "Finance_BSimon", odpovídající hodnota je "Finance_", pak výstup deklarace je "BSimon". |
| **Extract() - Před sladěním** | Vrátí podřetězec, dokud neodpovídá zadané hodnotě.<br/>Například pokud je hodnota vstupu "BSimon_US", odpovídající hodnota je "_US", pak výstup deklarace je "BSimon". |
| **Extract() - Mezi odpovídající** | Vrátí podřetězec, dokud neodpovídá zadané hodnotě.<br/>Například pokud je hodnota vstupu "Finance_BSimon_US", první odpovídající hodnota je "Finance_", druhá odpovídající hodnota je "_US", pak výstup deklarace je "BSimon". |
| **ExtractAlpha() - předpona** | Vrátí předponu abecední část řetězce.<br/>Například pokud je hodnota vstupu "BSimon_123", pak vrátí "BSimon". |
| **ExtractAlpha() - přípona** | Vrátí příponu abecední část řetězce.<br/>Například pokud je hodnota vstupu "123_Simon", vrátí "Simon". |
| **ExtractNumeric() - Předpona** | Vrátí číselnou část řetězce předpony.<br/>Například pokud je hodnota vstupu "123_BSimon", vrátí "123". |
| **ExtractNumeric() - přípona** | Vrátí číselnou část řetězce přípony.<br/>Například pokud je hodnota vstupu "BSimon_123", vrátí "123". |
| **IfEmpty()** | Výstupy atribut nebo konstanta, pokud je vstup null nebo prázdný.<br/>Například pokud chcete výstup atribut uložený v extensionattribute, pokud ID zaměstnance pro daného uživatele je prázdný. Chcete-li to provést, nakonfigurujte následující hodnoty:<br/>Parametr 1(input): user.employeeid<br/>Parametr 2 (výstup): user.extensionattribute1<br/>Parametr 3 (výstup, pokud neexistuje žádná shoda): user.employeeid |
| **IfNotEmpty()** | Výstupy atribut nebo konstanta, pokud vstup není null nebo prázdné.<br/>Například pokud chcete výstup atribut uložený v extensionattribute, pokud ID zaměstnance pro daného uživatele není prázdný. Chcete-li to provést, nakonfigurujte následující hodnoty:<br/>Parametr 1(input): user.employeeid<br/>Parametr 2 (výstup): user.extensionattribute1 |

Pokud potřebujete další transformace, odešlete svůj nápad ve [fóru pro zpětnou vazbu ve službě Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) v kategorii *aplikací SaaS.*

## <a name="emitting-claims-based-on-conditions"></a>Vyzařování pohledávek na základě podmínek

Můžete určit zdroj deklarace na základě typu uživatele a skupiny, do které uživatel patří. 

Typ uživatele může být:
- **Any**: Všichni uživatelé mají přístup k aplikaci.
- **Členové**: Nativní člen nájemce
- **Všichni hosté**: Uživatel je přenesen z externí organizace s nebo bez Azure AD.
- **Hosté AAD**: Uživatel hosta patří do jiné organizace pomocí Azure AD.
- **Externí hosté**: Uživatel hosta patří do externí organizace, která nemá Azure AD.


Jeden scénář, kde je to užitečné je, když zdroj deklarace se liší pro hosta a zaměstnance přístup k aplikaci. Můžete určit, že pokud je uživatel zaměstnancem NameID pochází z user.email, ale pokud je uživatel hostem, potom NameID pochází z user.extensionattribute1.

Přidání podmínky deklarace pohledávky:

1. V **části Spravovat deklaraci**rozbalte podmínky deklarace.
2. Vyberte typ uživatele.
3. Vyberte skupiny, do kterých má uživatel patřit. Můžete vybrat až 10 jedinečných skupin ve všech deklaracích identity pro danou aplikaci. 
4. Vyberte **zdroj,** kde deklarace bude načítat jeho hodnotu. Můžete vybrat atribut uživatele z rozevíracího souboru atributu zdroj nebo použít transformaci na atribut uživatele před jeho vyzařováním jako deklarace.

Pořadí, ve kterém přidáte podmínky, je důležité. Azure AD vyhodnotí podmínky shora dolů a rozhodne, jakou hodnotu má být ve deklaraci vypouštěna. 

Například Brita Simon je uživatel typu host a klient contoso. Patří do jiné organizace, která také používá Azure AD. Vzhledem k níže uvedené konfiguraci pro aplikaci Fabrikam, když Brita pokusí přihlásit do Fabrikam, Azure AD vyhodnotí podmínky takto.

Za prvé, Azure AD ověří, pokud `All guests`Brita typ uživatele je . Vzhledem k tomu, že to platí pak Azure `user.extensionattribute1`AD přiřadí zdroj pro deklaraci . Za druhé, Azure AD ověří, pokud `AAD guests`Brita typ uživatele je , protože to platí také `user.mail`pak Azure AD přiřadí zdroj pro deklaraci . Nakonec je deklarace vyzařována s hodnotou `user.email` pro Brita.

![Deklarace podmíněné konfigurace](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>Další kroky

* [Správa aplikací ve službě Azure AD](../manage-apps/what-is-application-management.md)
* [Konfigurace aplikací jednotného přihlašování, které nejsou v galerii aplikací Azure AD](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Poradce připotíží k jednotnému přihlašování na saml](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
