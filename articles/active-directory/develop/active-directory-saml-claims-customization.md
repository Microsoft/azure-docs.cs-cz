---
title: Přizpůsobení deklarací tokenů SAML pro podnikové aplikace v Azure AD | Microsoft Docs
description: Naučte se přizpůsobit deklarace identity vydané v tokenu SAML pro podnikové aplikace v Azure AD.
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
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c0625a233b3b4a949feff2e289361a26fc8dc5a
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835355"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Postup: Přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace

V současné době Azure Active Directory (Azure AD) podporuje jednotné přihlašování (SSO) s většinou podnikových aplikací, včetně aplikací předem integrovaných v galerii aplikací Azure AD i ve vlastních aplikacích. Když se uživatel k aplikaci přes Azure AD ověřuje pomocí protokolu SAML 2,0, Azure AD pošle token do aplikace (prostřednictvím HTTP POST). A pak aplikace ověří a použije token k přihlášení uživatele místo výzvy k zadání uživatelského jména a hesla. Tyto tokeny SAML obsahují informace o uživateli označovaném jako *deklarace identity*.

*Deklarace* identity představuje informace o tom, že poskytovatel identity uvádí informace o uživateli v tokenu, který tento uživatel vystavuje. V [tokenu SAML](https://en.wikipedia.org/wiki/SAML_2.0)jsou tato data obvykle obsažena v příkazu atributu SAML. Jedinečné ID uživatele je obvykle reprezentované v předmětu SAML označovaném také jako identifikátor názvu.

Ve výchozím nastavení služba Azure AD vydá token SAML vaší aplikaci, který obsahuje `NameIdentifier` deklaraci identity s hodnotou uživatelského jména uživatele (označovanou také jako hlavní název uživatele) ve službě Azure AD, která může uživatele jednoznačně identifikovat. Token SAML obsahuje taky další deklarace identity, které obsahují e-mailovou adresu uživatele, jméno a příjmení.

Pokud chcete zobrazit nebo upravit deklarace identity vystavené v tokenu SAML pro aplikaci, otevřete aplikaci v Azure Portal. Pak otevřete oddíl **atributy uživatele & deklarace identity** .

![V Azure Portal otevřete část atributy uživatele & deklarace identity.](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Existují dva možné důvody, proč možná budete muset upravit deklarace identity vystavené v tokenu SAML:

* Aplikace vyžaduje, `NameIdentifier` aby deklarace nebo NameId mohla být jiná než uživatelské jméno (nebo hlavní název uživatele) uložené ve službě Azure AD.
* Aplikace byla napsána tak, aby vyžadovala jinou sadu identifikátorů URI deklarace identity nebo hodnot deklarace identity.

## <a name="editing-nameid"></a>Úpravy NameID

Postup úpravy NameID (hodnota identifikátoru názvu):

1. Otevřete stránku **hodnota identifikátoru názvu** .
1. Vyberte atribut nebo transformaci, kterou chcete použít u atributu. Volitelně můžete zadat formát, který má deklarace identity NameID mít.

   ![Upravit hodnotu NameID (identifikátor názvu)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>Formát NameID

Pokud požadavek SAML obsahuje element NameIDPolicy s určitým formátem, bude služba Azure AD respektovat formát v žádosti.

Pokud požadavek SAML neobsahuje element pro NameIDPolicy, bude Azure AD vystavovat NameID s vámi zadaným formátem. Pokud není zadaný žádný formát, Azure AD použije výchozí formát zdroje přidružený k vybranému zdroji deklarací.

V rozevíracím seznamu **zvolit formát identifikátoru názvu** můžete vybrat jednu z následujících možností.

| Formát NameID | Popis |
|---------------|-------------|
| **Výchozí** | Azure AD bude používat výchozí formát zdroje. |
| **Perzistentní** | Služba Azure AD bude používat jako formát NameID jako trvalá. |
| **EmailAddress** | Azure AD bude používat EmailAddress jako formát NameID. |
| **Neurčené** | Azure AD použije nespecifikovaný formát NameID. |
| **Transient (přechodná)** | Azure AD bude jako formát NameID používat přechodný. |

Další informace o atributu NameIDPolicy najdete v tématu [protokol SAML jednotného přihlašování](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Atributy

Vyberte požadovaný zdroj pro `NameIdentifier` deklaraci (nebo NameId). Můžete vybrat z následujících možností.

| Name | Popis |
|------|-------------|
| Email | E-mailová adresa uživatele |
| userprincipalName | Hlavní název uživatele (UPN) uživatele |
| onpremisessamaccount | Název účtu SAM, který byl synchronizovaný z místní služby Azure AD |
| objektu | objectID uživatele v Azure AD |
| EmployeeID | ČísloZaměstnance uživatele |
| Rozšíření adresáře | Rozšíření adresáře [synchronizovaná z místní služby Active Directory pomocí Azure AD Connect synchronizace](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Atributy rozšíření 1-15 | Atributy místních rozšíření používaných k rozšíření schématu Azure AD |

Další informace najdete v [tabulce 3: Platné hodnoty ID na zdroj](active-directory-claims-mapping.md#table-3-valid-id-values-per-source)

### <a name="special-claims---transformations"></a>Speciální deklarace – transformace

Můžete také použít funkce transformace deklarací identity.

| Funkce | Popis |
|----------|-------------|
| **ExtractMailPrefix()** | Odebere příponu domény z e-mailové adresy nebo hlavního názvu uživatele. Tím se extrahuje jenom první část uživatelského jména, který se předává (například "joe_smith" místo joe_smith@contoso.com). |
| **Join ()** | Připojí k atributu ověřenou doménu. Pokud má vybraná hodnota identifikátoru uživatele doména, extrahuje uživatelské jméno, aby se připojila vybraná ověřená doména. Pokud například jako hodnotu identifikátoru uživatele vyberete e-mailjoe_smith@contoso.com() a jako ověřenou doménu vyberete contoso.onmicrosoft.com, bude to joe_smith@contoso.onmicrosoft.commít za následek. |
| **ToLower()** | Převede znaky vybraného atributu na malá písmena. |
| **ToUpper ()** | Převede znaky vybraného atributu na velká písmena. |

## <a name="adding-application-specific-claims"></a>Přidání deklarací specifických pro aplikaci

Přidání deklarací specifických pro aplikaci:

1. V **atributu uživatele & deklarací identity**výběrem možnosti **Přidat novou deklaraci** identity otevřete stránku **Spravovat deklarace identity uživatelů** .
1. Zadejte **název** deklarací identity. Hodnota nemusí bezpodmínečně odpovídat vzoru identifikátoru URI podle specifikace SAML. Pokud potřebujete vzor identifikátoru URI, můžete ho umístit do pole **obor názvů** .
1. Vyberte **zdroj** , ve kterém bude deklarace identity získávat svou hodnotu. Můžete vybrat atribut uživatele z rozevíracího seznamu zdrojový atribut nebo použít transformaci na atribut uživatele před jeho vygenerováním jako deklarace identity.

### <a name="application-specific-claims---transformations"></a>Deklarace identity specifické pro aplikaci – transformace

Můžete také použít funkce transformace deklarací identity.

| Funkce | Popis |
|----------|-------------|
| **ExtractMailPrefix()** | Odebere příponu domény z e-mailové adresy nebo hlavního názvu uživatele. Tím se extrahuje jenom první část uživatelského jména, který se předává (například "joe_smith" místo joe_smith@contoso.com). |
| **Join ()** | Vytvoří novou hodnotu spojením dvou atributů. Volitelně můžete použít oddělovač mezi dvěma atributy. |
| **ToLower()** | Převede znaky vybraného atributu na malá písmena. |
| **ToUpper ()** | Převede znaky vybraného atributu na velká písmena. |
| **Obsahuje ()** | Vytvoří výstup atributu nebo konstanty, pokud vstup odpovídá zadané hodnotě. Jinak můžete zadat jiný výstup, pokud se neshodují.<br/>Například pokud chcete vygenerovat deklaraci identity, kde je hodnota e-mailová adresa uživatele, pokud obsahuje doménu "@contoso.com", jinak chcete vytvořit výstup hlavního názvu uživatele. Uděláte to tak, že nakonfigurujete následující hodnoty:<br/>*Parametr 1 (vstup)* : User. email<br/>*Hodnota*:@contoso.com""<br/>Parametr 2 (výstup): User. email<br/>Parametr 3 (výstup, pokud se neshoduje): User. userPrincipalName |
| **EndWith()** | Vytvoří výstup atributu nebo konstanty, pokud vstupní hodnota končí zadanou hodnotou. Jinak můžete zadat jiný výstup, pokud se neshodují.<br/>Například pokud chcete vygenerovat deklaraci identity, kde je hodnota ČísloZaměstnance uživatele, pokud je hodnota ČísloZaměstnance na konci "000", jinak chcete vytvořit výstup atributu rozšíření. Uděláte to tak, že nakonfigurujete následující hodnoty:<br/>*Parametr 1 (vstup)* : User. ČísloZaměstnance<br/>*Hodnota*: "000"<br/>Parametr 2 (výstup): User. ČísloZaměstnance<br/>Parametr 3 (výstup, pokud se neshoduje): User. extensionAttribute1 |
| **StartWith()** | Vytvoří výstup atributu nebo konstanty, pokud vstup začíná zadanou hodnotou. Jinak můžete zadat jiný výstup, pokud se neshodují.<br/>Například pokud chcete vygenerovat deklaraci identity, kde je hodnota ČísloZaměstnance uživatele, pokud země nebo oblast začíná řetězcem "US", jinak chcete vytvořit výstup atributu rozšíření. Uděláte to tak, že nakonfigurujete následující hodnoty:<br/>*Parametr 1 (vstup)* : User. Country<br/>*Hodnota*: VYLEPŠENÍ<br/>Parametr 2 (výstup): User. ČísloZaměstnance<br/>Parametr 3 (výstup, pokud se neshoduje): User. extensionAttribute1 |
| **Extract () – po porovnání** | Vrátí podřetězec poté, co odpovídá zadané hodnotě.<br/>Pokud má například hodnota vstupu hodnotu "Finance_BSimon", odpovídá hodnota "Finance_", takže výstup deklarace identity je "BSimon". |
| **Extract () – Před spárováním** | Vrátí dílčí řetězec, dokud neodpovídá zadané hodnotě.<br/>Pokud má například hodnota vstupu hodnotu "BSimon_US", odpovídá hodnota "_US", takže výstup deklarace identity je "BSimon". |
| **Extract ()-Between – shoda** | Vrátí dílčí řetězec, dokud neodpovídá zadané hodnotě.<br/>Pokud je hodnota vstupu například "Finance_BSimon_US", první vyhovující hodnota je "Finance_", druhá hodnota pro porovnání je "_US", pak je výstup deklarace identity "BSimon". |
| **ExtractAlpha() - Prefix** | Vrátí prefixovou část řetězce v abecedním pořadí.<br/>Pokud je například hodnota vstupu "BSimon_123", pak vrátí "BSimon". |
| **ExtractAlpha() - Suffix** | Vrátí příponu v abecedním pořadí řetězce.<br/>Pokud je například hodnota vstupu "123_Simon", pak vrátí "Simon". |
| **ExtractNumeric() - Prefix** | Vrátí číselnou část řetězce předpony.<br/>Pokud je například hodnota vstupu "123_BSimon", pak vrátí "123". |
| **ExtractNumeric() - Suffix** | Vrátí číselnou část řetězce přípony.<br/>Pokud je například hodnota vstupu "BSimon_123", pak vrátí "123". |
| **IfEmpty()** | Vytvoří výstup atributu nebo konstanty, pokud má vstup hodnotu null nebo je prázdný.<br/>Například pokud chcete výstupovat atribut uložený v ExtensionAttribute, pokud je pole ČísloZaměstnance pro daného uživatele prázdné. Uděláte to tak, že nakonfigurujete následující hodnoty:<br/>Parametr 1 (vstup): User. ČísloZaměstnance<br/>Parametr 2 (výstup): User. extensionAttribute1<br/>Parametr 3 (výstup, pokud se neshoduje): User. ČísloZaměstnance |
| **IfNotEmpty()** | Vytvoří výstup atributu nebo konstanty, pokud vstupní hodnota není null nebo prázdná.<br/>Například pokud chcete výstupovat atribut uložený v ExtensionAttribute, pokud není pole ČísloZaměstnance pro daného uživatele prázdné. Uděláte to tak, že nakonfigurujete následující hodnoty:<br/>Parametr 1 (vstup): User. ČísloZaměstnance<br/>Parametr 2 (výstup): User. extensionAttribute1 |

Pokud potřebujete další transformace, pošlete svůj nápad do fóra pro [názory ve službě Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) v kategorii *aplikace SaaS* .

## <a name="next-steps"></a>Další kroky

* [Správa aplikací v Azure AD](../manage-apps/what-is-application-management.md)
* [Konfigurace jednotného přihlašování u aplikací, které nejsou v galerii aplikací Azure AD](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Řešení potíží s jednotným přihlašováním založeném na SAML](howto-v1-debug-saml-sso-issues.md)
