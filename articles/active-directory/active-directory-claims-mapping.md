---
title: Deklarace identity mapování v Azure Active Directory (verze public preview) | Microsoft Docs
description: Tato stránka popisuje mapování deklarace Azure Active Directory.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: billmath
ms.openlocfilehash: e35a33cbe77d9d29b975ede8535abbded2cde4c3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="claims-mapping-in-azure-active-directory-public-preview"></a>Deklarace identity mapování v Azure Active Directory (verze public preview)

>[!NOTE]
>Tato funkce nahrazuje a nahrazuje [deklarací přizpůsobení](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization) dnes nabízených prostřednictvím portálu. Pokud upravíte deklarace identity pomocí portálu kromě metoda grafu nebo PowerShell zmíněné v tomto dokumentu na stejnou aplikaci, tokeny vydán pro s aplikací budou ignorovat konfigurace na portálu.
Konfigurace provedená prostřednictvím metody popsané v tomto dokumentu se neprojeví v portálu.

Tato funkce slouží k přizpůsobení deklarace identity vygenerované v tokeny pro konkrétní aplikace v jejich klienta ve Správci klientů. Deklarace identity mapování zásady, které můžete použít:

- Vyberte deklarace, které jsou součástí tokeny.
- Vytvoření typů deklarací identity, které již neexistují.
- Zvolte nebo změnit zdroj dat vygenerované v konkrétní deklarací identity.

>[!NOTE]
>Tato funkce je aktuálně ve verzi public preview. Připravte se na obnovit nebo odeberte všechny změny. Tato funkce je k dispozici v libovolné předplatné služby Azure Active Directory (Azure AD) verzi public Preview. Když je tato funkce obecně dostupná, může vyžadovat některé aspekty funkce však předplatné služby Azure Active Directory premium. Tato funkce podporuje konfiguraci zásad mapování deklarace identity pro protokoly WS-Fed, SAML, OAuth a OpenID Connect.

## <a name="claims-mapping-policy-type"></a>Mapování typu zásady deklarace identity
Ve službě Azure AD **zásad** objekt představuje sadu pravidel vynucené u jednotlivých aplikací, nebo na všechny aplikace v organizaci. Každý typ zásad se strukturou jedinečnou sadu vlastností, které se pak použijí pro objekty, na které jsou přiřazeny.

Deklarace A mapování zásad je typ **zásad** objektu, která upraví deklarace identity vygenerované v tokeny vydané pro konkrétní aplikace.

## <a name="claim-sets"></a>Sady deklarací identity
Existují určité sady deklarací identity, které definují, jak a kdy se používají v tokenech.

### <a name="core-claim-set"></a>Základní sady deklarací.
Deklarace identity v sadě deklarací identity core jsou k dispozici v každé tokenu, bez ohledu na zásady. Tyto deklarace identity jsou také považovány za omezený a nemůže být upraven.

### <a name="basic-claim-set"></a>Sada základní deklarací identity
Sada základní deklarací identity obsahuje deklarace identity, které jsou vygenerované ve výchozím nastavení pro tokeny (kromě základní sady deklarací). Tyto deklarace můžete tento parametr vynechán nebo upravit pomocí deklarace identity mapování zásad.

### <a name="restricted-claim-set"></a>Sada deklarací identity s omezeným přístupem
Pomocí zásad nemůže být upraven deklarace identity s omezeným přístupem. Zdroj dat nelze změnit, a při generování tyto deklarace identity, použije se žádná transformace.

#### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tabulka 1: JSON Web Token (JWT) omezené sady deklarací.
|Typ deklarace identity (název)|
| ----- |
|_claim_names|
|_claim_sources|
|access_token|
|account_type|
|acr|
|Objektu actor|
|actortoken|
|AIO|
|altsecid|
|AMR|
|app_chain|
|app_displayname|
|app_res|
|appctx|
|appctxsender|
|appid|
|appidacr|
|Kontrolní výraz|
|at_hash|
|oblast|
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
|csr|
|csr_type|
|ID zařízení|
|dns_names|
|domain_dns_name|
|domain_netbios_name|
|e_exp|
|e-mail|
|endpoint|
|enfpolids|
|Exp|
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
|ipaddr|
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
|hodnotu Nonce|
|OID|
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
|Obor|
|scp|
|identifikátor SID|
|Podpis|
|signin_state|
|src1|
|src2|
|Sub –|
|tbid|
|tenant_display_name|
|tenant_region_scope|
|thumbnail_photo|
|TID|
|tokenAutologonEnabled|
|trustedfordelegation|
|unique_name|
|hlavní název uživatele|
|user_setting_sync_url|
|uživatelské jméno|
|uti|
|ver|
|verified_primary_email|
|verified_secondary_email|
|wids|
|win_ver|

#### <a name="table-2-security-assertion-markup-language-saml-restricted-claim-set"></a>Tabulka 2: Security (Assertion Markup Language SAML) omezené sady deklarací.
|Typ deklarace identity (URI)|
| ----- |
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expired|
|http://schemas.microsoft.com/identity/claims/accesstoken|
|http://schemas.microsoft.com/identity/claims/openid2_id|
|http://schemas.microsoft.com/identity/claims/identityprovider|
|http://schemas.microsoft.com/identity/claims/objectidentifier|
|http://schemas.microsoft.com/identity/claims/puid|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier[MR1] |
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

## <a name="claims-mapping-policy-properties"></a>Deklarace identity mapování vlastnosti zásad
Pomocí vlastnosti deklarací, mapování na ovládací prvek deklarace, které jsou vygenerované zásad a kde jsou data pochází z. Pokud je nastavené žádné zásady, problémy s tokeny systému obsahující základní deklarace sadu, sadě základní deklarací identity a jakékoli [volitelné deklarací](develop/active-directory-optional-claims.md) , aplikace se rozhodli přijímat.

### <a name="include-basic-claim-set"></a>Patří nastavení základní deklarace identity

**Řetězec:** IncludeBasicClaimSet

**Datový typ:** logická hodnota (True nebo False)

**Souhrn:** tato vlastnost určuje, zda sada základní deklarace identity je součástí tokeny vliv na tuto zásadu. 

- Pokud je nastaven na hodnotu True, všechny deklarace identity v sadě základní deklarace identity jsou vygenerované v tokeny zásadami ovlivněná. 
- Pokud nastaven na hodnotu False, deklarace identity v sadě deklarací identity základní nejsou v tokeny, pokud se zařazují jednotlivě ve vlastnosti deklarace identity schématu stejné zásady.

>[!NOTE] 
>Deklarace identity v sadě deklarací identity core jsou k dispozici v každé tokenu, bez ohledu na to, co je tato vlastnost nastavená na. 

### <a name="claims-schema"></a>Schéma deklarace identity

**Řetězec:** ClaimsSchema

**Datový typ:** JSON blob pomocí jedné nebo víc položek schématu deklarace identity

**Souhrn:** definuje tato vlastnost deklarace, které se nacházejí v tokenech zásadami ovlivněná, kromě sady základní deklarace identity a základní sady deklarací.
U jednotlivých deklarací identity položek schéma definované v této vlastnosti určité informace se vyžaduje. Je nutné zadat, kde jsou data pocházejí z (**hodnotu** nebo **ID zdrojového nebo pár**), a které deklarace identity data jsou vydávány jako (**deklarace identity typu**).

### <a name="claim-schema-entry-elements"></a>Prvky schématu vstupní deklarace identity

**Hodnota:** prvku hodnoty definuje statickou hodnotu jako data, která mají být vygenerované v deklaraci identity.

**ID zdrojového nebo pár:** elementy zdrojový a ID definovat, kde jsou data v deklaraci identity pochází z. 

Element zdroje musí být nastaven na jednu z následujících: 


- "user": data v deklaraci identity je vlastnost v objektu User. 
- "aplikace": data v deklaraci identity je vlastnost v objektu služby aplikace (klient). 
- "prostředek": data v deklaraci identity je vlastnost v objektu služby prostředků.
- "cílová skupina": data v deklaraci identity je vlastnost v objektu služby, která je cílová skupina tokenu (klienta nebo prostředků-instanční objekt).
- "společnost": data v deklaraci identity je vlastnost u objektu prostředku klienta společnosti.
- "transformace": data v deklaraci identity je z transformace deklarace identity (viz část "Transformace deklarací identity" později v tomto článku). 

Pokud je zdroj transformace **TransformationID** element musí být součástí této deklarace identity definici.

ID elementu identifikuje, jehož vlastnost ve zdroji poskytuje hodnotu pro deklarace identity. V následující tabulce jsou uvedeny hodnoty ID, které jsou platné pro každou hodnotu zdroje.

#### <a name="table-3-valid-id-values-per-source"></a>Tabulka 3: Hodnoty platné ID na zdroj
|Zdroj|ID|Popis|
|-----|-----|-----|
|Uživatel|surname|Příjmení|
|Uživatel|givenName|jméno|
|Uživatel|displayname|Zobrazovaný název|
|Uživatel|objectid|ObjectID|
|Uživatel|mail|E-mailová adresa|
|Uživatel|userprincipalname|Hlavní název uživatele (UPN)|
|Uživatel|Oddělení|Oddělení|
|Uživatel|onpremisessamaccountname|Na místní název účtu Sam|
|Uživatel|název pro rozhraní NetBIOS|Název pro rozhraní NetBios|
|Uživatel|dnsdomainname|Dns Domain Name|
|Uživatel|onpremisesecurityidentifier|Identifikátor zabezpečení na místě|
|Uživatel|NázevSpolečnosti|Název organizace|
|Uživatel|streetAddress|Ulice|
|Uživatel|PSČ|PSČ|
|Uživatel|preferredlanguange|Upřednostňovaný jazyk|
|Uživatel|onpremisesuserprincipalname|místní hlavní název uživatele|
|Uživatel|mailnickname|Přezdívka e-mailu|
|Uživatel|extensionattribute1|Atribut rozšíření 1|
|Uživatel|extensionattribute2|Atribut rozšíření 2|
|Uživatel|extensionattribute3|Rozšíření atribut 3|
|Uživatel|extensionattribute4|Atribut rozšíření 4|
|Uživatel|extensionattribute5|Rozšíření atribut 5|
|Uživatel|extensionattribute6|Atribut rozšíření 6|
|Uživatel|extensionattribute7|Atribut rozšíření 7|
|Uživatel|extensionattribute8|Atribut rozšíření 8|
|Uživatel|extensionattribute9|Rozšíření atribut 9|
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
|Uživatel|Číslo zaměstnance|ID zaměstnance|
|Uživatel|facsimiletelephonenumber|Faxem telefonní číslo|
|aplikace, prostředků, cílová skupina|displayname|Zobrazovaný název|
|aplikace, prostředků, cílová skupina|proti|ObjectID|
|aplikace, prostředků, cílová skupina|tags|Značka instančního objektu|
|Společnost|tenantcountry|Země klienta|

**TransformationID:** TransformationID element je třeba zadat pouze v případě, že element zdroj je nastaven na hodnotu "transformace".

- Tento element musí odpovídat ID elementu transformace položky v **ClaimsTransformation** vlastnost, která definuje, jak vygenerovat data pro tuto deklaraci.

**Typ deklarace identity:** **JwtClaimType** a **SamlClaimType** elementy definovat, které deklarace identity odkazuje tato položka schématu deklarace identity.

- JwtClaimType musí obsahovat název deklarace na být vygenerované v Jwt.
- SamlClaimType musí obsahovat identifikátor URI deklarace identity pro vypuštění v tokenech SAML.

>[!NOTE]
>Názvy a identifikátory URI deklarací identity v sadě s omezeným přístupem deklarací identity nelze použít u elementů typu deklarace identity. Další informace najdete v části "Výjimky a omezení" dále v tomto článku.

### <a name="claims-transformation"></a>Transformace deklarace identity

**Řetězec:** ClaimsTransformation

**Datový typ:** objekt blob JSON, s jedné nebo víc položek transformace 

**Souhrn:** pomocí této vlastnosti můžete použít běžné transformace pro zdroj dat, ke generování výstupní data pro deklarace zadané ve schématu deklarací identity.

**ID:** odkazovat na tuto položku transformaci v položce schématu TransformationID deklarace identity pomocí ID elementu. Tato hodnota musí být jedinečný pro každý záznam transformaci v rámci této zásady.

**TransformationMethod:** TransformationMethod element identifikuje, které operace generování dat pro deklarace identity.

Založené na metodě vybrali, se očekává sadu vstupy a výstupy. Tyto jsou definované pomocí **InputClaims**, **vstupní parametry** a **OutputClaims** elementy.

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tabulka 4: Metody transformaci a očekávané vstupy a výstupy
|TransformationMethod|Očekávaný vstup|Očekávaný výstup|Popis|
|-----|-----|-----|-----|
|Spojit|řetězec1, řetězec2, oddělovače|outputClaim|Spojení vstup řetězce s použitím oddělovače v rozmezí. Příklad: řetězec1: "foo@bar.com", řetězec2: "izolovaného prostoru", oddělovač: "." výsledkem outputClaim: "foo@bar.com.sandbox"|
|ExtractMailPrefix|mail|outputClaim|Extrahuje místní část e-mailovou adresu. Příklad: e-mailu: "foo@bar.com" výsledkem outputClaim: "foo". Pokud ne @ přihlašovací je nainstalován, pak bude vrácen orignal vstupní řetězec, jako je.|

**InputClaims:** použít InputClaims element k předání dat z položky schématu deklarace identity transformace. Má dva atributy: **ClaimTypeReferenceId** a **TransformationClaimType**.

- **ClaimTypeReferenceId** je spojen s ID elementu vstupu schématu deklarace identity se najít odpovídající vstupní deklaraci identity. 
- **TransformationClaimType** umožňuje poskytnout jedinečný název pro tento vstup. Tento název se musí shodovat s jedním z očekávané vstupů pro metodu transformace.

**Vstupní parametry:** použít element vstupní parametry k předání konstantní hodnotu transformace. Má dva atributy: **hodnotu** a **ID**.

- **Hodnota** je skutečná konstantní hodnota mají být předány.
- **ID** umožňuje poskytnout jedinečný název pro tento vstup. Tento název se musí shodovat s jedním z očekávané vstupů pro metodu transformace.

**OutputClaims:** použijte OutputClaims element k ukládání dat generované transformaci a tie k položce schématu deklarace identity. Má dva atributy: **ClaimTypeReferenceId** a **TransformationClaimType**.

- **ClaimTypeReferenceId** je spojen s ID vstupu schématu deklarace identity k vyhledání příslušné výstupní deklarací identity.
- **TransformationClaimType** umožňuje poskytnout jedinečný název pro tento výstup. Tento název se musí shodovat s jedním očekávané výstupy pro metodu transformace.

### <a name="exceptions-and-restrictions"></a>Výjimky a omezení

**SAML NameID a UPN:** atributů, ze kterých zdrojového hodnoty NameID a UPN a transformace deklarací identity, které jsou povoleny, jsou omezené.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tabulka 5: Atributy povolena jako zdroj dat pro SAML NameID
|Zdroj|ID|Popis|
|-----|-----|-----|
|Uživatel|mail|E-mailová adresa|
|Uživatel|userprincipalname|Hlavní název uživatele (UPN)|
|Uživatel|onpremisessamaccountname|Na místní název účtu Sam|
|Uživatel|Číslo zaměstnance|ID zaměstnance|
|Uživatel|extensionattribute1|Atribut rozšíření 1|
|Uživatel|extensionattribute2|Atribut rozšíření 2|
|Uživatel|extensionattribute3|Rozšíření atribut 3|
|Uživatel|extensionattribute4|Atribut rozšíření 4|
|Uživatel|extensionattribute5|Rozšíření atribut 5|
|Uživatel|extensionattribute6|Atribut rozšíření 6|
|Uživatel|extensionattribute7|Atribut rozšíření 7|
|Uživatel|extensionattribute8|Atribut rozšíření 8|
|Uživatel|extensionattribute9|Rozšíření atribut 9|
|Uživatel|extensionattribute10|Atribut rozšíření 10|
|Uživatel|extensionattribute11|Atribut rozšíření 11|
|Uživatel|extensionattribute12|Atribut rozšíření 12|
|Uživatel|extensionattribute13|Atribut rozšíření 13|
|Uživatel|extensionattribute14|Atribut rozšíření 14|
|Uživatel|extensionattribute15|Atribut rozšíření 15|

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tabulka 6: Transformace metody povolené pro SAML NameID
|TransformationMethod|Omezení|
| ----- | ----- |
|ExtractMailPrefix|Žádný|
|Spojit|Přípona je připojený k musí být ověřené domény klienta prostředků.|

### <a name="custom-signing-key"></a>Vlastní podpisového klíče
Vlastní podpisový klíč musí být přiřazeni k objektu zabezpečení služby pro deklarace identity mapování zásad se projeví. Tento klíč jsou podepsané všechny tokeny vydané, které má vliv zásady. Aplikace musí být nastaven na přijímání tokeny podepsaný pomocí tohoto klíče. To zajistí potvrzení, změnilo tokeny Tvůrce deklarace identity mapování zásad. To chrání aplikace z deklarací identity mapování zásady vytvořené pomocí nebezpečného actors.

### <a name="cross-tenant-scenarios"></a>Scénáře napříč klienta
Mapování zásad deklarace identity se nevztahují na uživatele typu Host. Pokud uživatel guest pokusí o přístup k aplikaci s deklarací, mapování zásad, které jsou přiřazené k jeho instanční objekt, je výchozí token vydán (zásada nemá žádný vliv).

## <a name="claims-mapping-policy-assignment"></a>Mapování přiřazení zásady deklarace identity
Deklarace identity mapování zásad můžete přiřadit pouze hlavní objekty služby.

### <a name="example-claims-mapping-policies"></a>Příklad deklarací mapování zásad

Ve službě Azure AD je možné mnoho scénářů, pokud můžete přizpůsobit pro konkrétní službu objektů vygenerované v tokeny deklarací identity. V této části jsme provede několik běžných scénářů, které vám může pomoct pochopit, jak používat deklarace identity mapování typ zásad.

#### <a name="prerequisites"></a>Požadavky
V následujících příkladech vytvářet, aktualizovat, propojení a odstranit zásady pro objekty služby. Pokud jste ještě Azure AD, doporučujeme vám, další informace o tom, jak získat klienta Azure AD, než budete pokračovat v těchto příkladech. 

Chcete-li začít, proveďte následující kroky:


1. Stáhněte si nejnovější [verze public preview modul Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.127).
2.  Pomocí příkazu Connect pro přihlášení k účtu správce služby Azure AD. Spusťte tento příkaz pokaždé, když zahájit novou relaci.
    
     ``` powershell
    Connect-AzureAD -Confirm
    
    ```
3.  Pokud chcete zobrazit všechny zásady, které byly vytvořeny ve vaší organizaci, spusťte následující příkaz. Doporučujeme vám, spusťte tento příkaz po většinu operací v následujících scénářích, zkontrolujte, že vaše zásady vytváření podle očekávání.
   
    ``` powershell
        Get-AzureADPolicy
    
    ```
#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Příklad: Vytvořte a přiřaďte zásadu, která vynechejte základní deklarace identity z tokeny vydané instanční objekt.
V tomto příkladu vytvoříte zásadu, která odebere tokeny vydané pro objekty propojené služby do základní sady deklarací.


1. Vytvořte deklarace identity mapování zásad. Tato zásada, objekty ke konkrétnímu služby odebere základní sady z tokeny deklarací identity.
    1. Chcete-li vytvořit zásadu, spusťte tento příkaz: 
    
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims” -Type "ClaimsMappingPolicy"
    ```
    2. Chcete zobrazit nové zásady a získat zásady ObjectId, spusťte následující příkaz:
    
     ``` powershell
    Get-AzureADPolicy
    ```
2.  Přiřaďte zásady instančního objektu. Také je potřeba získat ObjectId služby hlavní. 
    1.  Pokud chcete zobrazit všechna firemní objekty služby, se můžete dotazovat Microsoft Graph. Nebo v Azure AD Graph Průzkumníku přihlášení k účtu Azure AD.
    2.  Až budete mít ObjectId hlavní služby, spusťte následující příkaz:  
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```
#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Příklad: Vytvořte a přiřaďte zásadu, která zahrnuje EmployeeID a TenantCountry jako deklarace identity v tokenech vystaveno pro objekt služby.
V tomto příkladu vytvoříte zásadu, která přidá EmployeeID a TenantCountry tokeny vydané objekty propojené služby. EmployeeID jsou vydávány jako typ deklarace identity názvu v tokeny SAML a tokeny Jwt. TenantCountry jsou vydávány jako typ deklarace země v tokeny SAML a tokeny Jwt. V tomto příkladu budeme nadále obsahují základní deklarace nastavit v tokenů.

1. Vytvořte deklarace identity mapování zásad. Tato zásada, propojený s objekty konkrétní služby, přidá EmployeeID a TenantCountry deklarace do tokenů.
    1. Chcete-li vytvořit zásadu, spusťte tento příkaz:  
     
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name","JwtClaimType":"name"},{"Source":"company","ID":" tenantcountry ","SamlClaimType":" http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country ","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample” -Type "ClaimsMappingPolicy"
    ```
    
    2. Chcete zobrazit nové zásady a získat zásady ObjectId, spusťte následující příkaz:
     
     ``` powershell  
    Get-AzureADPolicy
    ```
2.  Přiřaďte zásady instančního objektu. Také je potřeba získat ObjectId služby hlavní. 
    1.  Pokud chcete zobrazit všechna firemní objekty služby, se můžete dotazovat Microsoft Graph. Nebo v Azure AD Graph Průzkumníku přihlášení k účtu Azure AD.
    2.  Až budete mít ObjectId hlavní služby, spusťte následující příkaz:  
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```
#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Příklad: Vytvořte a přiřaďte zásadu, která používá transformaci deklarací identity v tokenech vystaveno pro objekt služby.
V tomto příkladu vytvoříte zásadu, která vydá vlastní deklarace identity "JoinedData" Jwt vystaveno pro objekty propojené služby. Toto tvrzení obsahuje hodnotu vytvořený sloučením data uložená v atributu extensionattribute1 v objektu user s ".sandbox". V tomto příkladu jsme vyloučit základní sada v tokeny deklarací identity.


1. Vytvořte deklarace identity mapování zásad. Tato zásada, propojený s objekty konkrétní služby, přidá EmployeeID a TenantCountry deklarace do tokenů.
    1. Chcete-li vytvořit zásadu, spusťte tento příkaz: 
     
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy" 
    ```
    
    2. Chcete zobrazit nové zásady a získat zásady ObjectId, spusťte následující příkaz: 
     
     ``` powershell
    Get-AzureADPolicy
    ```
2.  Přiřaďte zásady instančního objektu. Také je potřeba získat ObjectId služby hlavní. 
    1.  Pokud chcete zobrazit všechna firemní objekty služby, se můžete dotazovat Microsoft Graph. Nebo v Azure AD Graph Průzkumníku přihlášení k účtu Azure AD.
    2.  Až budete mít ObjectId hlavní služby, spusťte následující příkaz: 
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```
