---
title: Přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace ve službě Azure AD | Dokumentace Microsoftu
description: Informace o přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace ve službě Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c1f8640918d433956935e9428e23aac59e36334
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65764653"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Postup: Přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace

Azure Active Directory (Azure AD) v současné době podporuje jednotné přihlašování (SSO) s Většina podnikových aplikací, včetně obě aplikace předem integrované v galerii aplikací Azure AD, jakož i vlastních aplikací. Při ověření uživatele k aplikaci prostřednictvím služby Azure AD pomocí protokolu SAML 2.0, Azure AD odešle token do aplikace (přes HTTP POST). A potom aplikaci ověří a použije token k přihlášení uživatele místo vás vyzve k zadání uživatelského jména a hesla. Tyto tokeny SAML obsahují informace o uživateli říká *deklarace identity*.

A *deklarace identity* informace, které stavy zprostředkovatelů identity o uživateli uvnitř tokenu vydaného pro tohoto uživatele. V [tokenu SAML](https://en.wikipedia.org/wiki/SAML_2.0), tato data je obvykle součástí příkaz Attribute SAML. Jedinečné ID uživatele je obvykle reprezentována zkratka jako identifikátor názvu předmětu SAML.

Ve výchozím nastavení, Azure AD vydá SAML token pro vaše aplikace, která obsahuje `NameIdentifier` deklarace identity s hodnotou uživatelské jméno (také označované jako hlavní název uživatele) v Azure AD, která může jednoznačné identifikaci uživatele. SAML token obsahuje také další deklarace identity, který obsahuje uživatele e-mailová adresa, jméno a příjmení.

K zobrazení nebo úpravám deklarací identity vystavených v tokenu SAML pro aplikaci, otevřete aplikaci na webu Azure portal. Otevřete **atributy uživatele a deklarace identity** oddílu.

![Části atributy uživatele a deklarace identity](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Existují dva možné důvody, proč může být potřeba upravit deklarací identity vystavených v tokenu SAML:

* Aplikace vyžaduje `NameIdentifier` nebo NameID za něco jiného než uživatelské jméno (nebo hlavní název uživatele) uložené ve službě Azure AD.
* Aplikace se zapsala do vyžadují jinou sadu deklarací identity identifikátory URI nebo hodnoty deklarací identity.

## <a name="editing-nameid"></a>Úpravy NameID

Chcete-li upravit NameID (hodnota identifikátoru název):

1. Otevřít **název hodnota identifikátoru** stránky.
1. Vyberte atribut nebo transformace, kterou chcete použít pro atribut. Volitelně můžete zadat požadovaný formát má deklarace identity NameID mít.

   ![Upravit hodnotu NameID (identifikátor názvu)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>Formát ID názvu položky

Pokud si požadavek SAML obsahuje element NameIDPolicy s konkrétním formátu, Azure AD dodrží formátu v požadavku.

Pokud si požadavek SAML neobsahuje prvek pro NameIDPolicy, pak Azure AD budou vydávat NameID ve formátu, který zadáte. Pokud je zadán žádný format Azure AD použije výchozí formát zdroje přidružený k vybraný zdroj deklarací identity.

Z **zvolte název identifikátoru formátu** rozevírací seznam, můžete vybrat jednu z následujících možností.

| Formát ID názvu položky | Popis |
|---------------|-------------|
| **Výchozí** | Azure AD použije výchozí formát zdroje. |
| **Trvalé** | Azure AD použije trvalá jako formát ID názvu položky. |
| **EmailAddress** | Azure AD použije EmailAddress jako formát ID názvu položky. |
| **Tento parametr zadán** | Azure AD použije formátu NameID Neurčeno. |
| **Transient (přechodná)** | Azure AD použije přechodná jako formát ID názvu položky. |

Další informace o atributu NameIDPolicy najdete v tématu [protokol jednotné přihlašování SAML](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Atributy

Vyberte požadovaný zdroj `NameIdentifier` (nebo NameID) deklarace identity. Můžete vybrat jednu z následujících možností.

| Název | Popis |
|------|-------------|
| E-mail | E-mailovou adresu uživatele |
| userprincipalName | Hlavní název uživatele (UPN) uživatele |
| onpremisessamaccount | Název účtu SAM, která je synchronizovaná z místní služby Azure AD |
| ID objektu | ID objektu uživatele ve službě Azure AD |
| EmployeeID | EmployeeID uživatele |
| Rozšíření adresáře | Rozšíření adresáře [synchronizované z místní služby Active Directory pomocí Azure AD Connect Sync](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Atributů rozšíření 1 – 15 | Místní atributy rozšíření používané k rozšíření schématu služby Azure AD |

Další informace najdete v tématu [tabulka 3: Platné hodnoty ID jeden zdroj](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

### <a name="special-claims---transformations"></a>Speciální – deklarace identity transformace

Můžete také použít funkce transformace deklarací identity.

| Funkce | Popis |
|----------|-------------|
| **ExtractMailPrefix()** | Odebere příponu domény od e-mailovou adresu nebo hlavní název uživatele. To vyextrahuje jenom první část uživatelské jméno se předává (například "joe_smith" namísto joe_smith@contoso.com). |
| **Join()** | Připojí se atribut s ověřenou doménu. Pokud je hodnota identifikátoru vybraného uživatele domény, bude extrahovat uživatelské jméno pro připojení vybrané ověřenou doménu. Například, pokud vyberete e-mailu (joe_smith@contoso.com) jako hodnotu identifikátoru uživatele a vyberte contoso.onmicrosoft.com jako ověřenou doménu, výsledkem bude joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Znaky v datech vybraného atributu převede na malá písmena. |
| **ToUpper()** | Znaky v datech vybraného atributu převede na velká písmena. |

## <a name="adding-application-specific-claims"></a>Přidání deklarace identity specifické pro aplikaci

Přidání deklarace identity specifické pro aplikaci:

1. V **atributy uživatele a deklarace identity**vyberte **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** stránky.
1. Zadejte **název** deklarací identity. Hodnota nemusí nezbytně se řídí vzorem identifikátoru URI, podle specifikace SAML. Pokud potřebujete vzor identifikátoru URI, můžete umístit, který **Namespace** pole.
1. Vyberte **zdroj** kam načíst jeho hodnotu deklarace identity. Můžete vybrat atribut uživatele z rozevíracího seznamu atributu zdroje nebo před generování jako deklaraci použít transformace na atribut uživatele.

### <a name="application-specific-claims---transformations"></a>Deklarace identity specifické pro aplikaci - transformace

Můžete také použít funkce transformace deklarací identity.

| Funkce | Popis |
|----------|-------------|
| **ExtractMailPrefix()** | Odebere příponu domény od e-mailovou adresu nebo hlavní název uživatele. To vyextrahuje jenom první část uživatelské jméno se předává (například "joe_smith" namísto joe_smith@contoso.com). |
| **Join()** | Vytvoří novou hodnotu díky připojení ke službě dva atributy. Volitelně můžete použít oddělovač mezi dva atributy. |
| **ToLower()** | Znaky v datech vybraného atributu převede na malá písmena. |
| **ToUpper()** | Znaky v datech vybraného atributu převede na velká písmena. |
| **Metoda contains()** | Atribut nebo – konstanta výstupy, pokud vstup odpovídá zadané hodnotě. Pokud není nalezena žádná shoda, v opačném případě můžete zadat jiný výstupní.<br/>Pokud chcete generovat deklarace identity, kde hodnota je e-mailovou adresu uživatele, pokud obsahuje doménu, například "@contoso.com", jinak chcete výstup hlavní název uživatele. K tomuto účelu by nakonfigurujte následující hodnoty:<br/>*Parametr 1(input)*: user.email<br/>*Hodnota*: "@contoso.com"<br/>Parametr 2 (výstup): user.email<br/>Parametr 3 (Pokud není nalezena žádná shoda výstup): user.userprincipalname |
| **EndWith()** | Atribut nebo – konstanta výstupy, pokud vstupní končí zadanou hodnotou. Pokud není nalezena žádná shoda, v opačném případě můžete zadat jiný výstupní.<br/>Pokud chcete generovat deklarace identity, kde hodnota je employeeid uživatele, pokud employeeid končí "000", jinak je třeba do výstupu atributu rozšíření. K tomuto účelu by nakonfigurujte následující hodnoty:<br/>*Parametr 1(input)*: user.employeeid<br/>*Hodnota*: "000"<br/>Parametr 2 (výstup): user.employeeid<br/>Parametr 3 (Pokud není nalezena žádná shoda výstup): user.extensionattribute1 |
| **StartWith()** | Atribut nebo – konstanta výstupy, pokud vstup začíná zadanou hodnotou. Pokud není nalezena žádná shoda, v opačném případě můžete zadat jiný výstupní.<br/>Pokud chcete generovat deklarace identity, kde hodnota je employeeid uživatele, pokud v zemi nebo oblasti začíná "USA", jinak je třeba do výstupu atributu rozšíření. K tomuto účelu by nakonfigurujte následující hodnoty:<br/>*Parametr 1(input)*: user.country<br/>*Hodnota*: "USA"<br/>Parametr 2 (výstup): user.employeeid<br/>Parametr 3 (Pokud není nalezena žádná shoda výstup): user.extensionattribute1 |
| **Extract() - po odpovídající** | Vrátí podřetězec po odpovídá zadané hodnotě.<br/>Například pokud vstupní hodnota je "Finance_BSimon", odpovídající hodnota je "Finance_", je výstupní deklarace identity "BSimon". |
| **Extract() - před odpovídající** | Vrátí podřetězec, dokud nebude odpovídat zadanou hodnotu.<br/>Například pokud vstupní hodnota je "BSimon_US", odpovídající hodnota je "_US", je výstupní deklarace identity "BSimon". |
| **Extract() - mezi odpovídajícími si** | Vrátí podřetězec, dokud nebude odpovídat zadanou hodnotu.<br/>Například pokud vstupní hodnota je "Finance_BSimon_US", "Finance_" je první odpovídající hodnotu, druhá odpovídající hodnota je "_US" a pak výstup deklarace identity je "BSimon". |
| **ExtractAlpha() - Prefix** | Vrátí část reprezentující abecedním předpona řetězce.<br/>Například pokud vstupní hodnota je "BSimon_123", pak vrátí "BSimon". |
| **ExtractAlpha() - Suffix** | Vrátí část reprezentující abecedním přípona řetězce.<br/>Například pokud vstupní hodnota je "123_Simon", pak vrátí "Simon". |
| **ExtractNumeric() - Prefix** | Vrátí část reprezentující číselné předpona řetězce.<br/>Například pokud vstupní hodnota je "123_BSimon", pak vrátí "123". |
| **ExtractNumeric() - Suffix** | Vrátí část reprezentující číselná přípona řetězce.<br/>Například pokud vstupní hodnota je "BSimon_123", pak vrátí "123". |
| **IfEmpty()** | Atribut nebo konstanta výstupy, pokud vstup má hodnotu null nebo prázdný.<br/>Například, pokud chcete výstup atribut uložené v extensionattribute Pokud employeeid pro daného uživatele je prázdný. K tomuto účelu by nakonfigurujte následující hodnoty:<br/>Parametr 1(input): user.employeeid<br/>Parametr 2 (výstup): user.extensionattribute1<br/>Parametr 3 (Pokud není nalezena žádná shoda výstup): user.employeeid |
| **IfNotEmpty()** | Atribut nebo – konstanta výstupy, pokud vstup není null nebo prázdný.<br/>Například, pokud chcete výstup atribut uložené v extensionattribute Pokud employeeid pro daného uživatele není prázdný. K tomuto účelu by nakonfigurujte následující hodnoty:<br/>Parametr 1(input): user.employeeid<br/>Parametr 2 (výstup): user.extensionattribute1 |

Pokud potřebujete další transformací, pošlete svůj námět do [fóru pro zpětnou vazbu ve službě Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) pod *aplikaci SaaS* kategorie.

## <a name="next-steps"></a>Další postup

* [Správa aplikací v Azure AD](../manage-apps/what-is-application-management.md)
* [Konfigurace jednotného přihlašování pro aplikace, které nejsou v galerii aplikací Azure AD](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Řešení potíží s založené na SAML jednotného přihlašování](howto-v1-debug-saml-sso-issues.md)
