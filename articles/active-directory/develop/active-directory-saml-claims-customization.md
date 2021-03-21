---
title: Přizpůsobení deklarací identity tokenu SAML aplikace
titleSuffix: Microsoft identity platform
description: Naučte se přizpůsobit deklarace identity vydané platformou Microsoft Identity Platform v tokenu SAML pro podnikové aplikace.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: kenwith
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.openlocfilehash: 0cccf45037320b476b1a44cafa8074bacadacbc8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103600944"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Postupy: přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace

V současné době platforma Microsoft identity podporuje jednotné přihlašování (SSO) s většinou podnikových aplikací, včetně předem integrovaných aplikací v galerii aplikací Azure AD i vlastních aplikací. Když se uživatel k aplikaci přihlašuje přes Microsoft Identity Platform pomocí protokolu SAML 2,0, Microsoft Identity platforme do aplikace pošle token (prostřednictvím HTTP POST). A pak aplikace ověří a použije token k přihlášení uživatele místo výzvy k zadání uživatelského jména a hesla. Tyto tokeny SAML obsahují informace o uživateli označovaném jako *deklarace identity*.

*Deklarace* identity představuje informace o tom, že poskytovatel identity uvádí informace o uživateli v tokenu, který tento uživatel vystavuje. V [tokenu SAML](https://en.wikipedia.org/wiki/SAML_2.0)jsou tato data obvykle obsažena v příkazu atributu SAML. Jedinečné ID uživatele je obvykle reprezentované v předmětu SAML označovaném také jako identifikátor názvu.

Standardně Microsoft Identity Platform vydá token SAML vaší aplikaci, který obsahuje `NameIdentifier` deklaraci identity s hodnotou uživatelského jména uživatele (označovanou také jako hlavní název uživatele) ve službě Azure AD, která může uživatele jednoznačně identifikovat. Token SAML obsahuje taky další deklarace identity, které obsahují e-mailovou adresu uživatele, jméno a příjmení.

Pokud chcete zobrazit nebo upravit deklarace identity vystavené v tokenu SAML pro aplikaci, otevřete aplikaci v Azure Portal. Pak otevřete oddíl **atributy uživatele & deklarace identity** .

![V Azure Portal otevřete část atributy uživatele & deklarace identity.](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Existují dva možné důvody, proč možná budete muset upravit deklarace identity vystavené v tokenu SAML:

* Aplikace vyžaduje, `NameIdentifier` aby deklarace nebo NameId mohla být jiná než uživatelské jméno (nebo hlavní název uživatele) uložené ve službě Azure AD.
* Aplikace byla napsána tak, aby vyžadovala jinou sadu identifikátorů URI deklarace identity nebo hodnot deklarace identity.

## <a name="editing-nameid"></a>Úpravy nameID

Postup úpravy NameID (hodnota identifikátoru názvu):

1. Otevřete stránku **hodnota identifikátoru názvu** .
1. Vyberte atribut nebo transformaci, kterou chcete použít u atributu. Volitelně můžete zadat formát, který má deklarace identity NameID mít.

   ![Upravit hodnotu NameID (identifikátor názvu)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>Formát NameID

Pokud požadavek SAML obsahuje element NameIDPolicy s konkrétním formátem, pak bude v žádosti v rámci platformy Microsoft Identity Standard dodržen formát.

Pokud požadavek SAML neobsahuje element pro NameIDPolicy, bude platforma identity Microsoftu vystavovat NameID se zadaným formátem. Pokud není zadaný žádný formát, platforma Microsoft Identity Platform použije výchozí formát zdroje přidružený k vybranému zdroji deklarací.

V rozevíracím seznamu **zvolit formát identifikátoru názvu** můžete vybrat jednu z následujících možností.

| Formát NameID | Description |
|---------------|-------------|
| **Výchozí** | Platforma Microsoft Identity Platform bude používat výchozí formát zdroje. |
| **Trvalý** | Platforma Microsoft Identity Platform bude používat jako formát NameID jako trvalá. |
| **EmailAddress** | Platforma Microsoft Identity Platform bude používat EmailAddress jako formát NameID. |
| **Unspecified** | Platforma Microsoft Identity bude používat nespecifikovanou jako formát NameID. |

Dočasná NameID je také podporována, ale v rozevíracím seznamu není k dispozici a nelze ji nakonfigurovat na straně Azure. Další informace o atributu NameIDPolicy naleznete v tématu [Single Sign-On Protocol SAML](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Atributy

Vyberte požadovaný zdroj pro `NameIdentifier` deklaraci (nebo NameId). Můžete vybrat z následujících možností.

| Název | Description |
|------|-------------|
| E-mail | E-mailová adresa uživatele |
| Třídy | Hlavní název uživatele (UPN) uživatele |
| onpremisessamaccountname | Název účtu SAM, který byl synchronizovaný z místní služby Azure AD |
| objektu | ObjectID uživatele v Azure AD |
| zaměstnance | ID zaměstnance uživatele |
| Rozšíření adresáře | Rozšíření adresáře [synchronizovaná z místní služby Active Directory pomocí Azure AD Connect synchronizace](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Atributy rozšíření 1-15 | Atributy místních rozšíření používaných k rozšíření schématu Azure AD |

Další informace naleznete v [tabulce 3: platné hodnoty ID na zdroj](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

Všem deklaracím, které definujete v Azure AD, můžete také přiřadit jakoukoli konstantní (statickou) hodnotu. Použijte následující postup k přiřazení konstantní hodnoty:

1. V <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>v části **atributy uživatele & deklaracemi** klikněte na ikonu **Upravit** a upravte deklarace.
1. Klikněte na požadovanou deklaraci identity, kterou chcete upravit.
1. Zadejte konstantní hodnotu bez uvozovek ve **zdrojovém atributu** dle vaší organizace a klikněte na **Uložit**.

    ![Atributy org & deklarace identity v Azure Portal](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. Hodnota konstanty se zobrazí, jak je uvedeno níže.

    ![Úprava atributů & deklarací v části Azure Portal](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>Speciální deklarace – transformace

Můžete také použít funkce transformace deklarací identity.

| Funkce | Description |
|----------|-------------|
| **ExtractMailPrefix()** | Odebere příponu domény z e-mailové adresy nebo hlavního názvu uživatele. Tím se extrahuje jenom první část uživatelského jména, který se předává (například "joe_smith" místo joe_smith@contoso.com ). |
| **Join ()** | Připojí k atributu ověřenou doménu. Pokud má vybraná hodnota identifikátoru uživatele doména, extrahuje uživatelské jméno, aby se připojila vybraná ověřená doména. Pokud například joe_smith@contoso.com jako hodnotu identifikátoru uživatele vyberete e-mail () a jako ověřenou doménu vyberete contoso.onmicrosoft.com, bude to mít za následek joe_smith@contoso.onmicrosoft.com . |
| **ToLower ()** | Převede znaky vybraného atributu na malá písmena. |
| **ToUpper ()** | Převede znaky vybraného atributu na velká písmena. |

## <a name="adding-application-specific-claims"></a>Přidání deklarací specifických pro aplikaci

Přidání deklarací specifických pro aplikaci:

1. V **atributu uživatele & deklarací identity** výběrem možnosti **Přidat novou deklaraci** identity otevřete stránku **Spravovat deklarace identity uživatelů** .
1. Zadejte **název** deklarací identity. Hodnota nemusí bezpodmínečně odpovídat vzoru identifikátoru URI podle specifikace SAML. Pokud potřebujete vzor identifikátoru URI, můžete ho umístit do pole **obor názvů** .
1. Vyberte **zdroj** , ve kterém bude deklarace identity získávat svou hodnotu. Můžete vybrat atribut uživatele z rozevíracího seznamu zdrojový atribut nebo použít transformaci na atribut uživatele před jeho vygenerováním jako deklarace identity.

### <a name="claim-transformations"></a>Transformace deklarací identity

Chcete-li použít transformaci na atribut uživatele:

1. V části **Spravovat deklaraci identity** vyberte *transformovat* jako zdroj deklarace a otevřete stránku **Spravovat transformaci** .
2. Vyberte funkci z rozevíracího seznamu transformace. V závislosti na vybrané funkci budete muset zadat parametry a konstantní hodnotu pro vyhodnocení v transformaci. Další informace o dostupných funkcích najdete v následující tabulce.
3. Chcete-li použít více transformací, klikněte na tlačítko **Přidat transformaci**. Můžete použít maximálně dvě transformace na deklaraci identity. Například můžete nejprve extrahovat předponu e-mailu `user.mail` . Pak zadejte řetězec na velká písmena.

   ![Transformace více deklarací identity](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

K transformaci deklarací lze použít následující funkce.

| Funkce | Description |
|----------|-------------|
| **ExtractMailPrefix()** | Odebere příponu domény z e-mailové adresy nebo hlavního názvu uživatele. Tím se extrahuje jenom první část uživatelského jména, který se předává (například "joe_smith" místo joe_smith@contoso.com ). |
| **Join ()** | Vytvoří novou hodnotu spojením dvou atributů. Volitelně můžete použít oddělovač mezi dvěma atributy. Pro transformaci deklarace NameID je spojení omezené na ověřenou doménu. Pokud má vybraná hodnota identifikátoru uživatele doména, extrahuje uživatelské jméno, aby se připojila vybraná ověřená doména. Pokud například joe_smith@contoso.com jako hodnotu identifikátoru uživatele vyberete e-mail () a jako ověřenou doménu vyberete contoso.onmicrosoft.com, bude to mít za následek joe_smith@contoso.onmicrosoft.com . |
| **ToLowercase ()** | Převede znaky vybraného atributu na malá písmena. |
| **ToUppercase ()** | Převede znaky vybraného atributu na velká písmena. |
| **Obsahuje ()** | Vytvoří výstup atributu nebo konstanty, pokud vstup odpovídá zadané hodnotě. Jinak můžete zadat jiný výstup, pokud se neshodují.<br/>Například pokud chcete vygenerovat deklaraci identity, kde je hodnota e-mailová adresa uživatele, pokud obsahuje doménu " @contoso.com ", jinak chcete vytvořit výstup hlavního názvu uživatele. Uděláte to tak, že nakonfigurujete následující hodnoty:<br/>*Parametr 1 (vstup)*: User.email<br/>*Hodnota*: " @contoso.com "<br/>Parametr 2 (výstup): user.email<br/>Parametr 3 (výstup, pokud se neshoduje): User. userPrincipalName |
| **EndWith()** | Vytvoří výstup atributu nebo konstanty, pokud vstupní hodnota končí zadanou hodnotou. Jinak můžete zadat jiný výstup, pokud se neshodují.<br/>Například pokud chcete vygenerovat deklaraci identity, kde je hodnota ID zaměstnance uživatele, pokud ID zaměstnance končí na "000", jinak chcete vytvořit výstup atributu rozšíření. Uděláte to tak, že nakonfigurujete následující hodnoty:<br/>*Parametr 1 (vstup)*: User. ČísloZaměstnance<br/>*Hodnota*: "000"<br/>Parametr 2 (výstup): User. ČísloZaměstnance<br/>Parametr 3 (výstup, pokud se neshoduje): User. extensionAttribute1 |
| **StartWith()** | Vytvoří výstup atributu nebo konstanty, pokud vstup začíná zadanou hodnotou. Jinak můžete zadat jiný výstup, pokud se neshodují.<br/>Například pokud chcete vygenerovat deklaraci identity, kde je hodnota ID zaměstnance uživatele, pokud země nebo oblast začíná řetězcem "US", jinak chcete vytvořit výstup atributu rozšíření. Uděláte to tak, že nakonfigurujete následující hodnoty:<br/>*Parametr 1 (vstup)*: User. Country<br/>*Hodnota*: US<br/>Parametr 2 (výstup): User. ČísloZaměstnance<br/>Parametr 3 (výstup, pokud se neshoduje): User. extensionAttribute1 |
| **Extract () – po porovnání** | Vrátí podřetězec poté, co odpovídá zadané hodnotě.<br/>Pokud je například hodnota vstupu "Finance_BSimon", odpovídá hodnota "Finance_", takže výstup deklarace identity je "BSimon". |
| **Extract () – Před spárováním** | Vrátí dílčí řetězec, dokud neodpovídá zadané hodnotě.<br/>Pokud je například hodnota vstupu "BSimon_US", odpovídá hodnota "_US", takže výstup deklarace identity je "BSimon". |
| **Extract ()-Between – shoda** | Vrátí dílčí řetězec, dokud neodpovídá zadané hodnotě.<br/>Pokud je například hodnota vstupu "Finance_BSimon_US", první vyhovující hodnota je "finance \_ ", druhá hodnota pro porovnání je " \_ US", výstup deklarace identity je "BSimon". |
| **ExtractAlpha () – předpona** | Vrátí prefixovou část řetězce v abecedním pořadí.<br/>Pokud je například hodnota vstupu "BSimon_123", pak vrátí "BSimon". |
| **ExtractAlpha () – Přípona** | Vrátí příponu v abecedním pořadí řetězce.<br/>Pokud je například hodnota vstupu "123_Simon", pak vrátí "Simon". |
| **ExtractNumeric () – předpona** | Vrátí číselnou část řetězce předpony.<br/>Pokud je například hodnota vstupu "123_BSimon", pak vrátí "123". |
| **ExtractNumeric () – Přípona** | Vrátí číselnou část řetězce přípony.<br/>Pokud je například hodnota vstupu "BSimon_123", pak vrátí "123". |
| **IfEmpty()** | Vytvoří výstup atributu nebo konstanty, pokud má vstup hodnotu null nebo je prázdný.<br/>Například pokud chcete výstupovat atribut uložený v ExtensionAttribute, pokud je ID zaměstnance pro daného uživatele prázdné. Uděláte to tak, že nakonfigurujete následující hodnoty:<br/>Parametr 1 (vstup): User. ČísloZaměstnance<br/>Parametr 2 (výstup): User. extensionAttribute1<br/>Parametr 3 (výstup, pokud se neshoduje): User. ČísloZaměstnance |
| **IfNotEmpty()** | Vytvoří výstup atributu nebo konstanty, pokud vstupní hodnota není null nebo prázdná.<br/>Například pokud chcete výstupovat atribut uložený v ExtensionAttribute, pokud ID zaměstnance pro daného uživatele není prázdné. Uděláte to tak, že nakonfigurujete následující hodnoty:<br/>Parametr 1 (vstup): User. ČísloZaměstnance<br/>Parametr 2 (výstup): User. extensionAttribute1 |

Pokud potřebujete další transformace, pošlete svůj nápad do fóra pro [názory ve službě Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) v kategorii *aplikace SaaS* .

## <a name="emitting-claims-based-on-conditions"></a>Generování deklarací identity na základě podmínek

Můžete zadat zdroj deklarace identity na základě typu uživatele a skupiny, do které uživatel patří. 

Typ uživatele může být:
- **Jakékoli**: všichni uživatelé mají povolený přístup k aplikaci.
- **Členové**: nativní člen tenanta
- **Všichni hosté**: uživatel se převezme z externí organizace s Azure AD nebo bez něj.
- **Hosty AAD**: uživatel typu Host patří do jiné organizace, která používá službu Azure AD.
- **Externí hosté**: uživatel typu Host patří do externí organizace, která nemá službu Azure AD.


Jedním z situací, kdy je to užitečné, je, že je zdroj deklarace identity jiný pro hosta a zaměstnance, který přistupuje k aplikaci. Možná budete chtít určit, že pokud je uživatel zaměstnancem, NameID je zdrojem z user.email, ale pokud je uživatel hostem, pak je NameID od uživatele. extensionAttribute1.

Přidání podmínky deklarace identity:

1. V části **Spravovat deklaraci identity** rozbalte podmínky deklarace identity.
2. Vyberte typ uživatele.
3. Vyberte skupiny, do kterých má uživatel patřit. V rámci všech deklarací pro danou aplikaci můžete vybrat až 50 jedinečných skupin. 
4. Vyberte **zdroj** , ve kterém bude deklarace identity získávat svou hodnotu. Můžete vybrat atribut uživatele z rozevíracího seznamu zdrojový atribut nebo použít transformaci na atribut uživatele před jeho vygenerováním jako deklarace identity.

Pořadí, ve kterém přidáte podmínky, je důležité. Azure AD vyhodnotí podmínky shora dolů a určí, která hodnota se má v deklaracích generovat. Poslední hodnota, která se shoduje s výrazem, bude v deklaraci identity vygenerována.

Například Britta Simon je uživatel typu Host v tenantovi contoso. Patří do jiné organizace, která používá taky Azure AD. Když se Britta pokusí přihlásit k společnosti Fabrikam níže uvedeným nastavením, vyhodnotí podmínky v rámci platformy Microsoft identity.

Nejprve identita platformy Microsoft ověří, jestli je typ uživatele Britta `All guests` . Od této chvíle platí, že platforma Microsoft Identity Platform přiřadí zdroji deklarace identity `user.extensionattribute1` . Za druhé, platforma identity Microsoftu ověřuje, jestli je typ uživatele Britta `AAD guests` , protože to má taky hodnotu true, ale platforma Microsoft Identity Platform přiřadí zdroji deklarace identity `user.mail` . Nakonec se deklarace identity vydává s hodnotou `user.mail` pro Britta.

![Podmíněné konfigurace deklarací identity](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>Další kroky

* [Správa aplikací v Azure AD](../manage-apps/what-is-application-management.md)
* [Konfigurace jednotného přihlašování u aplikací, které nejsou v galerii aplikací Azure AD](../manage-apps/configure-saml-single-sign-on.md)
* [Řešení potíží s jednotným přihlašováním založeným na SAML](../manage-apps/debug-saml-sso-issues.md)
