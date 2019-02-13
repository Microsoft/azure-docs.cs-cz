---
title: Přizpůsobení deklarací identity v tokenech pro konkrétní aplikaci v tenantovi Azure AD (Public Preview), protože ho
description: Tato stránka popisuje mapování deklarací služby Azure Active Directory.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2018
ms.author: celested
ms.reviewer: paulgarn, hirsin, jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: aad699df2de8b745058784790e672f5b8c6e98e9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56209740"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Postup: Přizpůsobení deklarací identity v tokenech pro konkrétní aplikaci v tenantovi (Preview), protože ho

> [!NOTE]
> Tato funkce nahrazuje a nahrazuje [deklarací přizpůsobení](active-directory-saml-claims-customization.md) nabízeným prostřednictvím portálu. Ve stejné aplikaci pokud je přizpůsobení deklarací identity pomocí portálu kromě metodu grafu nebo Powershellu podrobně popsané v tomto dokumentu, tokeny vydané pro, že aplikace bude ignorovat konfigurace na portálu. Konfigurace provedené pomocí metody popsané v tomto dokumentu se neprojeví v portálu.

Tato funkce umožňuje správci tenantů přizpůsobení deklarací identity v tokenech pro danou aplikaci v rámci jejich tenanta, protože ho. Můžete použít zásady mapování deklarací identity:

- Vyberte, které deklarace identity jsou zahrnuty v tokenech.
- Vytvoření typů deklarací identity, které už neexistují.
- Zvolte nebo změnit zdroj dat v konkrétní deklarace identity.

> [!NOTE]
> Tato funkce je aktuálně ve verzi public preview. Buďte připravení na to, že jakékoli změny se můžou zrušit nebo odebrat. Tato funkce je k dispozici v každé předplatné Azure Active Directory (Azure AD) ve verzi public preview. Až tato funkce bude obecně dostupná, může vyžadovat některé aspekty funkcí však předplatné služby Azure AD premium. Tato funkce podporuje konfiguraci zásady mapování deklarací identity pro protokoly WS-Fed, SAML, OAuth a OpenID Connect.

## <a name="claims-mapping-policy-type"></a>Mapování typů zásad deklarace identity

Ve službě Azure AD **zásady** objekt představuje sadu pravidel vynucovat u jednotlivých aplikací, nebo na všechny aplikace v organizaci. Každý typ zásad má strukturu jedinečnou sadu vlastností, které se poté použijí na objekty, které jsou přiřazeny.

Deklarace A mapování zásad je typ **zásady** objekt, který upraví deklarace identity zaznamenávány do tokenů vydaných pro určité aplikace.

## <a name="claim-sets"></a>Sady deklarací

Existují určité sady deklarací identity, které definují, jak a kdy se používá v tokenech.

| Sada deklarací identity | Popis |
|---|---|
| Základní sada deklarací identity | Jsou k dispozici v každé tokenu bez ohledu na to, zásady. Tyto deklarace jsou také považovány za s omezením pomocí specifikátoru a nejde změnit. |
| Základní množině | Obsahuje deklarace identity, které jsou emitovány ve výchozím nastavení pro tokeny (kromě základní sady deklarací identity). Můžete vynechat nebo upravit základní deklarace identity pomocí mapování zásad deklarace identity. |
| Sada deklarací identity s omezeným přístupem | Nelze změnit pomocí zásad. Zdroj dat nelze změnit, a žádná transformace je při generování tyto deklarace. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tabulka 1: JSON Web Token (JWT) s omezením pomocí specifikátoru sady deklarací.

|Typ deklarace identity (název)|
| ----- |
|_claim_names|
|_claim_sources|
|access_token|
|account_type|
|acr|
|objekt actor|
|actortoken|
|aio|
|altsecid|
|AMR|
|app_chain|
|app_displayname|
|app_res|
|appctx|
|appctxsender|
|ID aplikace|
|appidacr|
|assertion|
|at_hash|
|aud|
|auth_data|
|auth_time|
|authorization_code|
|azp|
|azpacr|
|c_hash|
|ca_enf|
|cc|
|cert_token_use|
|client_id|
|cloud_graph_host_name|
|cloud_instance_name|
|možností cnf|
|kód|
|ovládací prvky|
|credential_keys|
|žádosti o podepsání certifikátu|
|csr_type|
|ID zařízení|
|dns_names|
|domain_dns_name|
|domain_netbios_name|
|e_exp|
|e-mail|
|endpoint|
|enfpolids|
|exp|
|expires_on|
|grant_type|
|graf|
|group_sids|
|skupiny|
|hasgroups|
|hash_alg|
|home_oid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expired|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier|
|IAT|
|identityprovider|
|idp|
|in_corp|
|instance|
|IPADDR|
|isbrowserhostedapp|
|iss|
|jwk|
|key_id|
|key_type|
|mam_compliance_url|
|mam_enrollment_url|
|mam_terms_of_use_url|
|mdm_compliance_url|
|mdm_enrollment_url|
|mdm_terms_of_use_url|
|nameid|
|nbf|
|netbios_name|
|Hodnota Nonce|
|identifikátor objektu|
|on_prem_id|
|onprem_sam_account_name|
|onprem_sid|
|openid2_id|
|heslo|
|platf|
|polids|
|pop_jwk|
|preferred_username|
|previous_refresh_token|
|primary_sid|
|identifikátor PUID|
|pwd_exp|
|pwd_url|
|redirect_uri|
|refresh_token|
|refreshtoken|
|request_nonce|
|prostředek|
|role|
|role|
|scope|
|scp|
|identifikátor SID|
|podpis|
|signin_state|
|src1|
|src2|
|Sub|
|tbid|
|tenant_display_name|
|tenant_region_scope|
|thumbnail_photo|
|TID.|
|tokenAutologonEnabled|
|trustedfordelegation|
|unique_name|
|upn|
|user_setting_sync_url|
|uživatelské jméno|
|uti|
|verze|
|verified_primary_email|
|verified_secondary_email|
|wids|
|win_ver|

### <a name="table-2-saml-restricted-claim-set"></a>Tabulka 2: SAML s omezením pomocí specifikátoru sady deklarací.

|Typ deklarace identity (URI)|
| ----- |
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expired|
|http://schemas.microsoft.com/identity/claims/accesstoken|
|http://schemas.microsoft.com/identity/claims/openid2_id|
|http://schemas.microsoft.com/identity/claims/identityprovider|
|http://schemas.microsoft.com/identity/claims/objectidentifier|
|http://schemas.microsoft.com/identity/claims/puid|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier [MR1] |
|http://schemas.microsoft.com/identity/claims/tenantid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod|
|http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/groups|
|http://schemas.microsoft.com/claims/groups.link|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/role|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/wids|
|http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant|
|http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown|
|http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged|
|http://schemas.microsoft.com/2014/03/psso|
|http://schemas.microsoft.com/claims/authnmethodsreferences|
|http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier|
|http://schemas.microsoft.com/identity/claims/scope|

## <a name="claims-mapping-policy-properties"></a>Mapování vlastnosti zásad deklarace identity

Řídit, jaké deklarace identity jsou emitovány a odkud data pochází, použijte vlastnosti deklarací mapování zásad. Pokud zásady není nastavena, problémy s tokeny systému, které zahrnují základní deklarace identity sadu, sady základní deklarace identity a jakékoli [nepovinných deklarací identity](active-directory-optional-claims.md) , který dostávat rozhodl aplikace.

### <a name="include-basic-claim-set"></a>Základní deklarace identity skupinu

**Řetězec:** IncludeBasicClaimSet

**Datový typ:** Logická hodnota (True nebo False)

**Shrnutí:** Tato vlastnost určuje, zda sady základní deklarací je součástí tokeny vliv těchto zásad. 

- Pokud je nastavený na hodnotu True, všechny deklarace identity v sadě základní deklarace identity jsou zaznamenávány do tokenů tyto zásady ovlivní. 
- Není-li nastaven na hodnotu False, deklarací identity v sadě základní deklarace identity v tokenech, pokud jsou jednotlivě přidají ve vlastnosti deklarace identity schématu stejné zásady.

> [!NOTE] 
> Deklarace identity v základní deklaraci identity sady jsou k dispozici v každé tokenu, bez ohledu na to, co je tato vlastnost nastavená na. 

### <a name="claims-schema"></a>Deklarace identity schématu

**Řetězec:** ClaimsSchema

**Datový typ:** Objekt blob JSON pomocí jedné nebo více položek schématu deklarace identity

**Shrnutí:** Tato vlastnost definuje, které deklarace identity jsou k dispozici v tokenech ovlivněné zásadami kromě sady základní deklarace identity a základní sada deklarací identity.
Pro každou položku schématu deklarace identity definované v této vlastnosti se vyžaduje určité informace. Určit, odkud data pocházejí (**hodnotu** nebo **zdroj/ID pár**), a které deklarace identity dat je vygenerován jako (**typ deklarace identity**).

### <a name="claim-schema-entry-elements"></a>Prvky schématu vstupní deklarace identity

**Hodnota:** Hodnota elementu definuje statickou hodnotu jako data, která mají být zaznamenávány do deklarace identity.

**Zdroj/ID pár:** Elementy zdroje a ID definovat, kde je zdrojem dat v deklaraci identity. 

Nastavte prvek zdroje na jednu z následujících hodnot: 

- "user": Data v deklaraci identity se vlastnost v objektu User. 
- "aplikace": Data v deklaraci identity se vlastnost v objektu služby aplikace (klient). 
- "prostředek": Data v deklaraci identity je vlastnost na prostředek instanční objekt.
- "skupina": Data v deklaraci identity se vlastnost na instanční objekt, který je cílová skupina tokenu (klienta nebo prostředků instanční objekt).
- "společnost": Data v deklaraci identity je vlastnost u objektu prostředku tenanta společnosti.
- "transformace": Data v deklaraci identity se z transformace deklarací identity (viz oddíl "Transformace deklarací identity" dále v tomto článku).

Pokud je zdroj transformace **TransformationID** elementu musí být součástí této deklarace identity definice.

ID element identifikuje, která vlastnost na zdroj obsahuje hodnotu pro deklarace identity. V následující tabulce jsou uvedeny hodnoty ID, které jsou platné pro každou hodnotu zdroje.

#### <a name="table-3-valid-id-values-per-source"></a>Tabulka 3: Platné hodnoty ID jeden zdroj

|Zdroj|ID|Popis|
|-----|-----|-----|
|Uživatel|Příjmení|Název rodiny|
|Uživatel|givenName|jméno|
|Uživatel|DisplayName|Zobrazovaný název|
|Uživatel|ID objektu|ObjectID|
|Uživatel|mail|E-mailová adresa|
|Uživatel|userprincipalname|Hlavní název uživatele|
|Uživatel|Oddělení|Oddělení|
|Uživatel|onpremisessamaccountname|Na místní název účtu Sam|
|Uživatel|netbiosname|Název rozhraní NetBios|
|Uživatel|název_domény_DNS|Dns Domain Name|
|Uživatel|onpremisesecurityidentifier|on-premises identifikátor zabezpečení|
|Uživatel|Firma|Název organizace|
|Uživatel|streetaddress|Ulice|
|Uživatel|PSČ|Poštovní směrovací číslo|
|Uživatel|preferredlanguange|Upřednostňovaný jazyk|
|Uživatel|onpremisesuserprincipalname|místní hlavní název uživatele|
|Uživatel|mailnickname|Přezdívka pro poštu|
|Uživatel|extensionattribute1|Atributů rozšíření 1|
|Uživatel|extensionattribute2|Atribut rozšíření 2|
|Uživatel|extensionattribute3|Atribut rozšíření 3|
|Uživatel|extensionattribute4|Atribut rozšíření 4|
|Uživatel|extensionattribute5|Atribut rozšíření 5|
|Uživatel|extensionattribute6|Atribut rozšíření 6|
|Uživatel|extensionattribute7|Atribut rozšíření 7|
|Uživatel|extensionattribute8|Atribut rozšíření 8|
|Uživatel|extensionattribute9|Atribut rozšíření 9|
|Uživatel|extensionattribute10|Atribut rozšíření 10|
|Uživatel|extensionattribute11|Atribut rozšíření 11|
|Uživatel|extensionattribute12|Atribut rozšíření 12|
|Uživatel|extensionattribute13|Atribut rozšíření 13|
|Uživatel|extensionattribute14|Atribut rozšíření 14|
|Uživatel|extensionattribute15|Atribut rozšíření 15|
|Uživatel|othermail|Další e-mailu|
|Uživatel|Země|Země|
|Uživatel|city|Město|
|Uživatel|state|Stav|
|Uživatel|pracovní funkce|Funkce|
|Uživatel|EmployeeID|ID zaměstnance|
|Uživatel|facsimiletelephonenumber|Faxem telefonní číslo|
|aplikace, prostředků, cílovou skupinu|DisplayName|Zobrazovaný název|
|aplikace, prostředků, cílovou skupinu|námitky|ObjectID|
|aplikace, prostředků, cílovou skupinu|tags|Značka objektu služby|
|Společnost|tenantcountry|Zemi tenanta|

**TransformationID:** TransformationID element musí být zadaná jenom v případě, že Source element nastavená na "transformace".

- Tento element musí odpovídat ID elementu vstupu transformace v **ClaimsTransformation** vlastnost, která definuje způsob generování dat pro tuto deklaraci.

**Typ deklarace identity:** **JwtClaimType** a **SamlClaimType** elementy definovat, které deklarace identity odkazuje položka schéma této deklarace identity.

- JwtClaimType musí obsahovat název deklarace identity, aby byly vypuštěny v tokeny Jwt.
- SamlClaimType musí obsahovat identifikátor URI deklarace identity, aby byly vypuštěny v tokenech SAML.

> [!NOTE]
> Názvy a identifikátory URI deklarací v sadě deklarací identity s omezeným přístupem, které nelze použít pro prvky typu deklarace identity. Další informace najdete v části "Výjimky a omezení" dále v tomto článku.

### <a name="claims-transformation"></a>Transformace deklarací identity

**Řetězec:** ClaimsTransformation

**Datový typ:** Objekt blob JSON pomocí jedné nebo více položek transformace 

**Shrnutí:** Pomocí této vlastnosti použít běžné transformace na zdroj dat, se vygenerovat výstupní data pro deklarace identity určená ve schématu deklarací identity.

**ID:** Pomocí ID elementu odkazují na tuto položku transformace v položce schématu TransformationID deklarací identity. Tato hodnota musí být jedinečný pro každou položku transformace v rámci této zásadě.

**TransformationMethod:** TransformationMethod element identifikuje, které proběhlo generují data pro deklarace identity.

Podle zvolené metodě, je očekáván sadu vstupů a výstupů. Definovat vstupů a výstupů pomocí **InputClaims**, **vstupní parametry** a **OutputClaims** elementy.

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tabulka 4: Transformace metody a očekávané vstupy a výstupy

|TransformationMethod|Očekávaný vstup|Očekávaný výstup|Popis|
|-----|-----|-----|-----|
|Spojit|řetězec1, řetězec2, oddělovač|outputClaim|Spojení vstupních řetězců s použitím oddělovače mezi. Příklad: řetězec1: "foo@bar.com", řetězec2: "izolovaném prostoru", oddělovač: "." výsledkem outputClaim: "foo@bar.com.sandbox"|
|ExtractMailPrefix|mail|outputClaim|Extrahuje místní části e-mailovou adresu. Příklad: e-mailu: "foo@bar.com" výsledkem outputClaim: "foo". Pokud ne \@ přihlašování je k dispozici, pak původního vstupního řetězce je vrácen, jako je.|

**InputClaims:** Použijte InputClaims element předat data z položky schématu deklarace identity transformace. Má dva atributy: **ClaimTypeReferenceId** a **TransformationClaimType**.

- **ClaimTypeReferenceId** je spojen s ID elementu vstupu schématu deklarace identity se najít odpovídající vstupní deklaraci identity. 
- **TransformationClaimType** slouží k zadejte jedinečný název pro tento vstup. Tento název musí odpovídat jedné z očekávaných vstupy pro metodu transformace.

**Vstupní parametry:** Použijte element vstupní parametry k předání konstantní hodnota transformace. Má dva atributy: **Hodnota** a **ID**.

- **Hodnota** je skutečná konstantní hodnota má být předán.
- **ID** slouží k zadejte jedinečný název vstupu. Název musí odpovídat jedné z očekávaných vstupy pro metodu transformace.

**OutputClaims:** Použijte OutputClaims element pro uložení dat vygenerovaných vašimi transformaci a spojit je položka schématu deklarace identity. Má dva atributy: **ClaimTypeReferenceId** a **TransformationClaimType**.

- **ClaimTypeReferenceId** je spojen s ID schématu vstupní deklarace identity, který se má najít odpovídající výstupní deklarací.
- **TransformationClaimType** slouží k zadejte jedinečný název do výstupu. Název musí odpovídat jedné z očekávané výstupy pro metodu transformace.

### <a name="exceptions-and-restrictions"></a>Výjimky a omezení

**ID názvu SAML a hlavní název uživatele:** Atributy, z nichž zdrojové hodnoty NameID a hlavní název uživatele a transformace deklarací identity, které jsou povoleny, jsou omezeny. Zobrazit tabulky 5 a 6 zobrazíte povolené hodnoty.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tabulka 5: Atributy povolen jako zdroj dat pro SAML NameID

|Zdroj|ID|Popis|
|-----|-----|-----|
|Uživatel|mail|E-mailová adresa|
|Uživatel|userprincipalname|Hlavní název uživatele|
|Uživatel|onpremisessamaccountname|Na místní název účtu Sam|
|Uživatel|EmployeeID|ID zaměstnance|
|Uživatel|extensionattribute1|Atributů rozšíření 1|
|Uživatel|extensionattribute2|Atribut rozšíření 2|
|Uživatel|extensionattribute3|Atribut rozšíření 3|
|Uživatel|extensionattribute4|Atribut rozšíření 4|
|Uživatel|extensionattribute5|Atribut rozšíření 5|
|Uživatel|extensionattribute6|Atribut rozšíření 6|
|Uživatel|extensionattribute7|Atribut rozšíření 7|
|Uživatel|extensionattribute8|Atribut rozšíření 8|
|Uživatel|extensionattribute9|Atribut rozšíření 9|
|Uživatel|extensionattribute10|Atribut rozšíření 10|
|Uživatel|extensionattribute11|Atribut rozšíření 11|
|Uživatel|extensionattribute12|Atribut rozšíření 12|
|Uživatel|extensionattribute13|Atribut rozšíření 13|
|Uživatel|extensionattribute14|Atribut rozšíření 14|
|Uživatel|extensionattribute15|Atribut rozšíření 15|

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tabulka 6: Transformace metod povolených pro SAML NameID

|TransformationMethod|Omezení|
| ----- | ----- |
|ExtractMailPrefix|Žádný|
|Spojit|Přípona je připojen musí být ověřené domény prostředků tenanta.|

### <a name="custom-signing-key"></a>Vlastní podpisový klíč

Vlastní podpisový klíč musíte být přiřazeni k instanční objekt pro deklarace identity mapování zásad se projeví. Všechny tokeny vystavené, které má vliv zásady, které jsou podepsány pomocí vlastní podpisový klíč a aplikace musí být nakonfigurovaný tak, aby přijímal tokeny podepsán podpisový klíč. Tím se zajistí potvrzení, že tokeny byly změněny Tvůrce deklarací mapování zásad a chrání aplikace před deklarací mapování zásad vytvořených útočníky.

### <a name="cross-tenant-scenarios"></a>Scénáře napříč tenanty

Mapování zásad deklarace identity se nevztahují na uživatele typu Host. Pokud uživatel typu Host se pokusí o přístup k aplikaci s deklaracemi mapování zásady přiřazené jejich instančnímu objektu služby, objeví se výchozí token (zásady nemá žádný vliv).

## <a name="claims-mapping-policy-assignment"></a>Mapování přiřazení zásad deklarace identity

Mapování zásad deklarace identity je možné přiřadit pouze k instanční objekty.

### <a name="example-claims-mapping-policies"></a>Příklad deklarace mapování zásad

Ve službě Azure AD je možné mnoho scénářů, pokud si můžete přizpůsobit vygenerován pro konkrétní instanční objekty v tokeny deklarací identity. V této části provedeme několik běžných scénářů, které vám umožňují pochopit, jak používat deklarace identity mapování typu zásad.

#### <a name="prerequisites"></a>Požadavky

V následujících příkladech vytvářet, aktualizovat, propojení a odstranit zásady pro instanční objekty. Pokud začínáte s Azure AD, doporučujeme vám [Další informace o tom, jak získat tenanta Azure AD](quickstart-create-new-tenant.md) předtím, než budete pokračovat v těchto příkladech.

Abyste mohli začít, proveďte následující kroky:

1. Stáhněte si nejnovější [modulu Azure AD PowerShell verze public preview](https://www.powershellgallery.com/packages/AzureADPreview).
1. Spuštěním příkazu Connect pro přihlášení k účtu správce Azure AD. Spusťte tento příkaz pokaždé, když spustíte novou relaci.
    
     ``` powershell
    Connect-AzureAD -Confirm
    
    ```
1. Pokud chcete zobrazit všechny zásady, které byly vytvořeny ve vaší organizaci, spusťte následující příkaz. Doporučujeme vám, spusťte tento příkaz po většinu operací v následujících scénářích, chcete-li zkontrolovat, že vaše zásady se vytvářejí podle očekávání.
   
    ``` powershell
        Get-AzureADPolicy
    
    ```
#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Příklad: Vytvoření a přiřazení zásad chcete vynechat, nechte základní deklarace identity z tokenů vydaných pro objekt služby.
V tomto příkladu vytvoříte zásadu, která odebere základní sady deklarací z tokenů vydaných pro objekty zabezpečení propojenou službu.

1. Vytvoření deklarace mapování zásad. Tato zásada, ke konkrétnímu instanční objekty, odebere základní nastavení z tokenů deklarace identity.
    1. Chcete-li vytvořit zásadu, spusťte tento příkaz: 
    
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
    ```
    2. Chcete zobrazit nové zásady a získat zásady ObjectId, spusťte následující příkaz:
    
     ``` powershell
    Get-AzureADPolicy
    ```
1. Přiřaďte zásady instančního objektu služby. Také je potřeba získat ID objektu vaší služby instančního objektu. 
    1.  Pokud chcete zobrazit všechna firemní instanční objekty, se můžete dotazovat Microsoft Graphu. Nebo v Azure AD Graph Explorer přihlaste ke svému účtu Azure AD.
    2.  Až budete mít ObjectId instančního objektu služby, spusťte následující příkaz:  
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```

#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Příklad: Vytvoření a přiřazení zásad zahrnout EmployeeID a TenantCountry jako deklarace identity do tokenů vydaných pro instančního objektu

V tomto příkladu vytvoříte zásadu, která přidá EmployeeID a TenantCountry do tokenů vydaných pro objekty zabezpečení propojenou službu. EmployeeID je vygenerován jako typ deklarace identity názvu v tokeny SAML a tokeny Jwt. TenantCountry je vygenerován jako typ deklarace země v tokeny SAML a tokeny Jwt. V tomto příkladu jsme nadále zahrnují základní deklarace identity v tokenech.

1. Vytvoření deklarace mapování zásad. Tyto zásady, propojená na konkrétní instanční objekty, přidá deklarace identity EmployeeID a TenantCountry na tokeny.
    1. Chcete-li vytvořit zásadu, spusťte tento příkaz:  
     
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
    ```
    
    2. Chcete zobrazit nové zásady a získat zásady ObjectId, spusťte následující příkaz:
     
     ``` powershell  
    Get-AzureADPolicy
    ```
1. Přiřaďte zásady instančního objektu služby. Také je potřeba získat ID objektu vaší služby instančního objektu. 
    1.  Pokud chcete zobrazit všechna firemní instanční objekty, se můžete dotazovat Microsoft Graphu. Nebo v Azure AD Graph Explorer přihlaste ke svému účtu Azure AD.
    2.  Až budete mít ObjectId instančního objektu služby, spusťte následující příkaz:  
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```

#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Příklad: Vytvoření a přiřazení zásad, která používá transformace deklarací identity do tokenů vydaných pro instančního objektu

V tomto příkladu vytvoříte zásadu, která generuje vlastní deklarace identity "JoinedData" do tokeny Jwt vystaveno pro objekty zabezpečení propojenou službu. Tato deklarace identity obsahuje hodnotu vytvořený spojením data uložená v atributu extensionattribute1 v objektu user s ".sandbox". V tomto příkladu vylučujeme základní deklarace identity v tokenech.

1. Vytvoření deklarace mapování zásad. Tyto zásady, propojená na konkrétní instanční objekty, přidá deklarace identity EmployeeID a TenantCountry na tokeny.
    1. Chcete-li vytvořit zásadu, spusťte tento příkaz: 
     
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy" 
    ```
    
    2. Chcete zobrazit nové zásady a získat zásady ObjectId, spusťte následující příkaz: 
     
     ``` powershell
    Get-AzureADPolicy
    ```
1. Přiřaďte zásady instančního objektu služby. Také je potřeba získat ID objektu vaší služby instančního objektu. 
    1.  Pokud chcete zobrazit všechna firemní instanční objekty, se můžete dotazovat Microsoft Graphu. Nebo v Azure AD Graph Explorer přihlaste ke svému účtu Azure AD.
    2.  Až budete mít ObjectId instančního objektu služby, spusťte následující příkaz: 
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```
