---
title: Přizpůsobení deklarací identity aplikace tenanta Azure AD (PowerShell)
titleSuffix: Microsoft identity platform
description: Tato stránka popisuje Azure Active Directory mapování deklarací identity.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: c300faf33f57518d26f82234bdff94a37235cd66
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92275785"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Postupy: přizpůsobení deklarací, které byly vygenerovány v tokenech pro konkrétní aplikaci v tenantovi (Preview)

> [!NOTE]
> Tato funkce nahradí a nahrazuje [přizpůsobení deklarací identity](active-directory-saml-claims-customization.md) , které dnes nabízí portál. Pokud ve stejné aplikaci přizpůsobíte deklarace identity pomocí portálu kromě metody grafu/PowerShellu popsané v tomto dokumentu, budou tokeny vydané pro tuto aplikaci ignorovat konfiguraci na portálu. Konfigurace provedené prostřednictvím metod popsaných v tomto dokumentu se na portálu neprojeví.

Tuto funkci používají správci tenanta k přizpůsobení deklarací identity vygenerovaných v tokenech pro konkrétní aplikaci ve svém tenantovi. Zásady mapování deklarací identity můžete použít k těmto akcím:

- Vyberte, které deklarace identity jsou zahrnuté v tokenech.
- Vytvořte typy deklarací, které ještě neexistují.
- Výběr nebo změna zdroje dat vydaných ve specifických deklaracích.

> [!NOTE]
> Tato funkce je aktuálně ve verzi Public Preview. Buďte připravení na to, že jakékoli změny se můžou zrušit nebo odebrat. Tato funkce je k dispozici ve všech předplatných Azure Active Directory (Azure AD) ve verzi Public Preview. Pokud se ale funkce všeobecně zpřístupní, můžou některé aspekty funkce vyžadovat předplatné Azure AD Premium. Tato funkce podporuje konfiguraci zásad mapování deklarací pro protokoly WS-dodávání, SAML, OAuth a OpenID Connect.

## <a name="claims-mapping-policy-type"></a>Typ zásad mapování deklarací identity

V Azure AD představuje objekt **zásad** sadu pravidel vydaných pro jednotlivé aplikace nebo pro všechny aplikace v organizaci. Každý typ zásad má jedinečnou strukturu se sadou vlastností, které jsou následně aplikovány na objekty, ke kterým jsou přiřazeny.

Zásada mapování deklarací identity je typ objektu **zásad** , který mění deklarace identity emitované v tokenech vydaných pro konkrétní aplikace.

## <a name="claim-sets"></a>Sady deklarací identity

Existují určité sady deklarací, které definují, jak a kdy se používají v tokenech.

| Sada deklarací identity | Popis |
|---|---|
| Základní sada deklarací identity | Jsou přítomny v každém tokenu bez ohledu na zásadu. Tyto deklarace jsou také považovány za omezené a nelze je upravit. |
| Základní sada deklarací identity | Zahrnuje deklarace identity, které jsou ve výchozím nastavení emitované pro tokeny (kromě základní sady deklarací identity). Základní deklarace identity můžete vynechat nebo upravit pomocí zásad mapování deklarací identity. |
| Omezená sada deklarací identity | Nejde upravit pomocí zásad. Zdroj dat nelze změnit a při generování těchto deklarací se nepoužije žádná transformace. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tabulka 1: sada omezené deklarace identity JSON Web Token (JWT)

| Typ deklarace identity (název) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| tříd |
| actortoken |
| Printer |
| altsecid |
| AMR |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| identifikátor |
| appidacr |
| Neplatný |
| at_hash |
| aud |
| auth_data |
| auth_time |
| authorization_code |
| azp |
| azpacr |
| c_hash |
| ca_enf |
| cc |
| cert_token_use |
| client_id |
| cloud_graph_host_name |
| cloud_instance_name |
| CNF |
| kód |
| ovládací prvky |
| credential_keys |
| manažer |
| csr_type |
| DeviceID |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| e-mail |
| endpoint |
| enfpolids |
| exp |
| expires_on |
| grant_type |
| zapisovací |
| group_sids |
| skupiny |
| hasgroups |
| hash_alg |
| home_oid |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expired` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` |
| IAT |
| IdentityProvider |
| IDP |
| in_corp |
| případě |
| ipaddr |
| isbrowserhostedapp |
| ISS |
| JWK |
| key_id |
| key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| NameId |
| NBF |
| netbios_name |
| generované |
| identifikátor |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| heslo |
| polids |
| pop_jwk |
| preferred_username |
| previous_refresh_token |
| primary_sid |
| identifikátor |
| pwd_exp |
| pwd_url |
| redirect_uri |
| refresh_token |
| refreshtoken kontextového tokenu |
| request_nonce |
| prostředek |
| role |
| Role |
| scope |
| SCP |
| ID |
| označení |
| signin_state |
| src1 |
| src2 |
| jednotk |
| tbid |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| identifikátory |
| tokenAutologonEnabled |
| trustedfordelegation |
| unique_name |
| názvu |
| user_setting_sync_url |
| username |
| identifikátor UTI |
| ver |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>Tabulka 2: omezená sada deklarací SAML

| Typ deklarace identity (URI) |
| ----- |
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expired`|
|`http://schemas.microsoft.com/identity/claims/accesstoken`|
|`http://schemas.microsoft.com/identity/claims/openid2_id`|
|`http://schemas.microsoft.com/identity/claims/identityprovider`|
|`http://schemas.microsoft.com/identity/claims/objectidentifier`|
|`http://schemas.microsoft.com/identity/claims/puid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier [MR1]`|
|`http://schemas.microsoft.com/identity/claims/tenantid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod`|
|`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`|
|`http://schemas.microsoft.com/claims/groups.link`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/wids`|
|`http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant`|
|`http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown`|
|`http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged`|
|`http://schemas.microsoft.com/2014/03/psso`|
|`http://schemas.microsoft.com/claims/authnmethodsreferences`|
|`http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier`|
|`http://schemas.microsoft.com/identity/claims/scope`|

## <a name="claims-mapping-policy-properties"></a>Vlastnosti zásad mapování deklarací identity

Chcete-li určit, jaké deklarace identity jsou emitovány a odkud data pocházejí, použijte vlastnosti zásady mapování deklarací identity. Pokud zásada není nastavená, systém vydá tokeny, které zahrnují základní sadu deklarací identity, základní sadu deklarací identity a všechny [volitelné deklarace identity](active-directory-optional-claims.md) , které se aplikace rozhodla přijmout.

### <a name="include-basic-claim-set"></a>Zahrnout základní sadu deklarací identity

**Řetězec:** IncludeBasicClaimSet

**Datový typ:** Logická hodnota (true nebo false)

**Shrnutí:** Tato vlastnost určuje, jestli je v tokenech ovlivněných touto zásadou zahrnutá základní sada deklarací identity.

- Pokud je nastavená hodnota true, všechny deklarace v základní sadě deklarací identity se vysílají v tokenech ovlivněných touto zásadou.
- Pokud je hodnota nastavena na false, deklarace v základní sadě deklarací identity nejsou v tokenech, pokud se nepřidaly do vlastnosti schématu deklarací ve stejné zásadě.

> [!NOTE]
> Deklarace identity v základních deklaracích jsou přítomny v každém tokenu bez ohledu na to, jaká vlastnost je nastavená na.

### <a name="claims-schema"></a>Schéma deklarací identity

**Řetězec:** ClaimsSchema

**Datový typ:** Objekt BLOB JSON s jednou nebo více položkami schématu deklarace identity

**Shrnutí:** Tato vlastnost určuje, které deklarace identity jsou k dispozici v tokenech ovlivněných touto zásadou, kromě základní sady deklarací identity a základní sady deklarací identity.
Pro každou položku schématu deklarace identity definovanou v této vlastnosti se vyžadují určité informace. Určete, kam data přicházejí (**hodnota**, **dvojice identifikátoru zdroje/ID**nebo **ExtensionID**) a která deklarace identity se vygeneruje (**typ deklarace identity**).

### <a name="claim-schema-entry-elements"></a>Prvky položky schématu deklarace identity

**Hodnota:** Element Value definuje statickou hodnotu jako data, která se mají v deklaraci identity vypouštět.

**Dvojice ID a zdroje:** Prvky zdroje a ID definují, z nichž jsou data v deklaraci identity zdrojová.

**ExtensionID pár zdrojů:** Elementy source a ExtensionID definují atribut rozšíření schématu adresáře, ze kterého jsou zdrojová data v deklaraci identity. Další informace najdete v tématu [použití atributů rozšíření schématu adresáře v deklaracích identity](active-directory-schema-extensions.md).

Nastavte zdrojový element na jednu z následujících hodnot:

- "User": data v deklaraci identity jsou vlastnost objektu User.
- "Application": data v deklaraci identity jsou vlastnost objektu služby aplikace (klienta).
- "prostředek": data v deklaraci identity jsou vlastnost objektu služby prostředku.
- "cílová skupina": data v deklaraci identity jsou vlastnost objektu služby, která je cílovou skupinou tokenu (buď klient nebo instanční objekt služby).
- "společnost": data v deklaraci identity jsou vlastností objektu společnosti tenanta prostředku.
- "transformace": data v deklaraci identity pochází z transformace deklarací identity (viz část transformace deklarací identity dále v tomto článku).

Pokud je zdrojem transformace, musí být do této definice deklarace identity zahrnut i element **TransformationID** .

Element ID určuje, která vlastnost ve zdroji poskytuje hodnotu pro deklaraci identity. V následující tabulce jsou uvedeny hodnoty ID platné pro každou hodnotu zdroje.

#### <a name="table-3-valid-id-values-per-source"></a>Tabulka 3: platné hodnoty ID na zdroj

| Zdroj | ID | Popis |
|-----|-----|-----|
| Uživatel | surname | Název rodiny |
| Uživatel | givenname | jméno |
| Uživatel | DisplayName | Zobrazovaný název |
| Uživatel | objektu | ObjectId |
| Uživatel | pošta | E-mailová adresa |
| Uživatel | třídy | Hlavní název uživatele |
| Uživatel | Oddělení|Oddělení|
| Uživatel | onpremisessamaccountname | Místní název účtu SAM |
| Uživatel | NetBiosName| Název pro rozhraní NetBios |
| Uživatel | dnsdomainname | Název domény DNS |
| Uživatel | onpremisesecurityidentifier | Místní identifikátor zabezpečení |
| Uživatel | společnosti| Název organizace |
| Uživatel | streetaddress | Adresa |
| Uživatel | ovládacím | PSČ |
| Uživatel | preferredlanguage | Preferovaný jazyk |
| Uživatel | onpremisesuserprincipalname | Místní hlavní název uživatele (UPN) |*
| Uživatel | mailNickname | Přezdívka pošty |
| Uživatel | extensionattribute1 | Atribut rozšíření 1 |
| Uživatel | extensionattribute2 | Atribut rozšíření 2 |
| Uživatel | extensionattribute3 | Atribut rozšíření 3 |
| Uživatel | extensionattribute4 | Atribut rozšíření 4 |
| Uživatel | extensionattribute5 | Atribut rozšíření 5 |
| Uživatel | extensionattribute6 | Atribut rozšíření 6 |
| Uživatel | extensionattribute7 | Atribut rozšíření 7 |
| Uživatel | extensionattribute8 | Atribut rozšíření 8 |
| Uživatel | extensionattribute9 | Atribut rozšíření 9 |
| Uživatel | extensionattribute10 | Atribut rozšíření 10 |
| Uživatel | extensionattribute11 | Atribut rozšíření 11 |
| Uživatel | extensionattribute12 | Atribut rozšíření 12 |
| Uživatel | extensionattribute13 | Atribut rozšíření 13 |
| Uživatel | extensionattribute14 | Atribut rozšíření 14 |
| Uživatel | extensionattribute15 | Atribut rozšíření 15 |
| Uživatel | othermail | Jiná pošta |
| Uživatel | country | Země/oblast |
| Uživatel | city | City (Město) |
| Uživatel | state | Stav |
| Uživatel | jobtitle | Název pozice |
| Uživatel | zaměstnance | ID zaměstnance |
| Uživatel | facsimiletelephonenumber | Telefonní číslo faxu |
| Uživatel | assignedroles | seznam rolí aplikace přiřazených uživateli|
| aplikace, prostředek, cílová skupina | DisplayName | Zobrazovaný název |
| aplikace, prostředek, cílová skupina | objektu | ObjectId |
| aplikace, prostředek, cílová skupina | tags | Značka objektu služby |
| Společnost | tenantcountry | Země/oblast tenanta |

**TransformationID:** Element TransformationID se musí poskytnout jenom v případě, že je zdrojový element nastavený na transformaci.

- Tento prvek musí odpovídat elementu ID položky transformace ve vlastnosti **ClaimsTransformation** , která definuje způsob generování dat pro tuto deklaraci identity.

**Typ deklarace identity:** Prvky **JwtClaimType** a **SamlClaimType** definují, na které deklarace se vztahuje tato položka schématu deklarace identity.

- JwtClaimType musí obsahovat název deklarace identity, která se má vygenerovat v JWTs.
- SamlClaimType musí obsahovat identifikátor URI deklarace identity, která se má vygenerovat v tokenech SAML.

* **atribut onPremisesUserPrincipalName:** Při použití alternativního ID se místní atribut userPrincipalName synchronizuje s atributem Azure AD onPremisesUserPrincipalName. Tento atribut je k dispozici pouze v případě, že je nakonfigurováno alternativní ID, ale je k dispozici prostřednictvím aplikace MS Graph beta: https://graph.microsoft.com/beta/me/ .

> [!NOTE]
> Názvy a identifikátory URI deklarací identity v omezené sadě deklarací nelze použít pro prvky typu deklarace identity. Další informace najdete v části výjimky a omezení dále v tomto článku.

### <a name="claims-transformation"></a>Transformace deklarací identity

**Řetězec:** ClaimsTransformation

**Datový typ:** Objekt BLOB JSON s jednou nebo více položkami transformace

**Shrnutí:** Tato vlastnost slouží k aplikování běžných transformací na zdrojová data, aby se vygenerovala výstupní data pro deklarace identity zadané ve schématu deklarací identity.

**ID:** Použijte element ID pro odkazování na tuto položku transformace v položce schématu deklarace identity TransformationID. Tato hodnota musí být jedinečná pro každou položku transformace v rámci této zásady.

**TransformationMethod:** Element TransformationMethod identifikuje, která operace se provádí za účelem generování dat pro danou deklaraci.

Na základě zvolené metody se očekává sada vstupů a výstupů. Definujte **vstupy a** výstupy pomocí prvků **InputClaims**, Inputs a **OutputClaims** .

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tabulka 4: metody transformace a očekávané vstupy a výstupy

|TransformationMethod|Očekávaný vstup|Očekávaný výstup|Popis|
|-----|-----|-----|-----|
|Spojit|řetězec1, řetězec2, oddělovač|outputClaim|Spojí vstupní řetězce pomocí oddělovače mezi. Například: řetězec1: " foo@bar.com ", řetězec2: "Sandbox", oddělovač: "." má za následek outputClaim: " foo@bar.com.sandbox "|
|ExtractMailPrefix|E-mail nebo hlavní název uživatele|extrahovaný řetězec|ExtensionAttributes 1-15 nebo jiná rozšíření schématu, která pro uživatele ukládají hodnotu hlavního názvu uživatele (UPN) nebo e-mailové adresy, například johndoe@contoso.com . Extrahuje místní část e-mailové adresy. Například: mail: " foo@bar.com " má za následek outputClaim: "foo". Pokud \@ není k dispozici žádný symbol, je původní vstupní řetězec vrácen tak, jak je.|

**InputClaims:** Pomocí elementu InputClaims předejte data ze záznamu schématu deklarace do transformace. Má dva atributy: **ClaimTypeReferenceId** a **TransformationClaimType**.

- **ClaimTypeReferenceId** je spojen s elementem ID položky schématu deklarace identity, aby bylo možné najít odpovídající vstupní deklaraci identity.
- **TransformationClaimType** slouží k udělení jedinečného názvu tomuto vstupu. Tento název musí odpovídat jednomu z očekávaných vstupů pro metodu transformace.

**Vstupní parametry:** K předání konstantní hodnoty transformaci použijte prvek s parametry. Má dva atributy: **hodnota** a **ID**.

- **Value** je skutečná hodnota konstanty, která má být předána.
- **ID** slouží k udělení jedinečného názvu vstupu. Název musí odpovídat jednomu z očekávaných vstupů pro metodu transformace.

**OutputClaims:** Použijte element OutputClaims k ukládání dat generovaných transformací a k jejich propojení na položku schématu deklarace identity. Má dva atributy: **ClaimTypeReferenceId** a **TransformationClaimType**.

- **ClaimTypeReferenceId** je spojen s ID položky schématu deklarací identity, aby bylo možné najít příslušnou výstupní deklaraci identity.
- **TransformationClaimType** se používá k poskytnutí jedinečného názvu výstupu. Název musí odpovídat jednomu z očekávaných výstupů pro metodu transformace.

### <a name="exceptions-and-restrictions"></a>Výjimky a omezení

**NameId SAML a hlavní název uživatele:** Atributy, ze kterých jste naNameIDi a hodnoty hlavního názvu uživatele (UPN) a povolené transformace deklarací identity, jsou omezené. Povolené hodnoty zobrazíte v tabulce 5 a tabulce 6.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tabulka 5: atributy povolené jako zdroj dat pro SAML NameID

|Zdroj|ID|Popis|
|-----|-----|-----|
| Uživatel | pošta|E-mailová adresa|
| Uživatel | třídy|Hlavní název uživatele|
| Uživatel | onpremisessamaccountname|Místní název účtu SAM|
| Uživatel | zaměstnance|ID zaměstnance|
| Uživatel | extensionattribute1 | Atribut rozšíření 1 |
| Uživatel | extensionattribute2 | Atribut rozšíření 2 |
| Uživatel | extensionattribute3 | Atribut rozšíření 3 |
| Uživatel | extensionattribute4 | Atribut rozšíření 4 |
| Uživatel | extensionattribute5 | Atribut rozšíření 5 |
| Uživatel | extensionattribute6 | Atribut rozšíření 6 |
| Uživatel | extensionattribute7 | Atribut rozšíření 7 |
| Uživatel | extensionattribute8 | Atribut rozšíření 8 |
| Uživatel | extensionattribute9 | Atribut rozšíření 9 |
| Uživatel | extensionattribute10 | Atribut rozšíření 10 |
| Uživatel | extensionattribute11 | Atribut rozšíření 11 |
| Uživatel | extensionattribute12 | Atribut rozšíření 12 |
| Uživatel | extensionattribute13 | Atribut rozšíření 13 |
| Uživatel | extensionattribute14 | Atribut rozšíření 14 |
| Uživatel | extensionattribute15 | Atribut rozšíření 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tabulka 6: metody transformace povolené pro NameID SAML

| TransformationMethod | Omezení |
| ----- | ----- |
| ExtractMailPrefix | Žádné |
| Spojit | Připojená přípona musí být ověřená doména tenanta prostředků. |

### <a name="custom-signing-key"></a>Vlastní podpisový klíč

Aby se zásady mapování deklarací projevily, musí se vlastní podpisový klíč přiřadit k instančnímu objektu služby. Tím se zajistí potvrzení, že se tokeny změnily tvůrcem zásad mapování deklarací identity a chrání aplikace před zásadami mapování deklarací, které vytvořily škodlivé objekty actor. Chcete-li přidat vlastní podpisový klíč, můžete použít rutinu Azure PowerShell [`New-AzureADApplicationKeyCredential`](/powerShell/module/Azuread/New-AzureADApplicationKeyCredential) k vytvoření přihlašovacích údajů klíče certifikátu pro objekt aplikace.

Aplikace, které mají povolené mapování deklarací, musí ověřit své podpisové klíče tokenu připojením `appid={client_id}` ke svým [žádostem o metadata OpenID Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document). Níže je uvedený formát dokumentu metadat OpenID Connect, který byste měli použít:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="cross-tenant-scenarios"></a>Scénáře pro více tenantů

Zásady mapování deklarací se nevztahují na uživatele typu Host. Pokud se uživatel typu Host pokusí o přístup k aplikaci se zásadami mapování deklarací přiřazenými k jejímu instančnímu objektu, je vydaný výchozí token (zásada nemá žádný vliv).

## <a name="claims-mapping-policy-assignment"></a>Přiřazení zásad mapování deklarací identity

Zásady mapování deklarací lze přiřadit pouze objektům instančních objektů.

### <a name="example-claims-mapping-policies"></a>Příklad zásad mapování deklarací identity

V Azure AD je mnoho scénářů možné, když můžete přizpůsobit deklarace identity vydávané v tokenech pro konkrétní instanční objekty. V této části se seznámíme s několika běžnými scénáři, které vám pomůžou naučit se používat typ zásad mapování deklarací identity.

> [!NOTE]
> Při vytváření zásad mapování deklarací identity můžete také vygenerovat deklaraci identity z atributu rozšíření schématu adresáře v tokenech. Použijte *ExtensionID* pro atribut Extension namísto *ID* v `ClaimsSchema` elementu.  Další informace o atributech rozšíření najdete v tématu [použití atributů rozšíření schématu adresáře](active-directory-schema-extensions.md).

#### <a name="prerequisites"></a>Předpoklady

V následujících příkladech můžete vytvořit, aktualizovat, propojit a odstranit zásady pro instanční objekty. Pokud s Azure AD teprve začínáte, doporučujeme vám seznámit se s tím, [Jak získat tenanta Azure AD](quickstart-create-new-tenant.md) , než budete pokračovat v těchto příkladech.

Začněte tím, že provedete následující kroky:

1. Stáhněte si nejnovější [verzi Public Preview modulu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Spusťte příkaz připojit a přihlaste se ke svému účtu správce Azure AD. Spusťte tento příkaz pokaždé, když spustíte novou relaci.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Chcete-li zobrazit všechny zásady, které byly vytvořeny ve vaší organizaci, spusťte následující příkaz. Tento příkaz doporučujeme spustit po většině operací v následujících scénářích, abyste zjistili, jestli se zásady vytvářejí podle očekávání.

   ``` powershell
   Get-AzureADPolicy
   ```

#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Příklad: Vytvořte a přiřaďte zásadu pro vynechání základních deklarací identity z tokenů vydaných pro instanční objekt.

V tomto příkladu vytvoříte zásadu, která odebere základní sadu deklarací identity z tokenů vydaných pro propojené objekty služby.

1. Vytvořte zásadu mapování deklarací identity. Tato zásada propojená s konkrétními instančními objekty odstraní základní sadu deklarací identity z tokenů.
   1. Chcete-li vytvořit zásadu, spusťte tento příkaz:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Chcete-li zobrazit nové zásady a získat ObjectId zásad, spusťte následující příkaz:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Přiřaďte zásadu k instančnímu objektu. Také je nutné získat ObjectId objektu služby.
   1. Pokud chcete zobrazit všechny instanční objekty vaší organizace, můžete zadat [dotaz na rozhraní Microsoft Graph API](/graph/traverse-the-graph). Nebo v [Microsoft Graph Exploreru](https://developer.microsoft.com/graph/graph-explorer)se přihlaste k účtu služby Azure AD.
   2. Pokud máte identifikátor ObjectId objektu služby, spusťte následující příkaz:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Příklad: Vytvořte a přiřaďte zásady, které budou zahrnovat ČísloZaměstnance a TenantCountry jako deklarace identity v tokenech vydaných pro instanční objekt.

V tomto příkladu vytvoříte zásadu, která přidá ČísloZaměstnance a TenantCountry k tokenům vydaným pro propojené objekty služby. Pole ČísloZaměstnance je vygenerováno jako název deklarace identity v tokenech SAML a v JWTs. TenantCountry je vygenerován jako typ deklarace země nebo oblasti v tokenech SAML a v JWTs. V tomto příkladu budeme v tokenech dál zahrnovat základní sady deklarací identity.

1. Vytvořte zásadu mapování deklarací identity. Tato zásada propojená s konkrétními instančními objekty přidá deklarace ČísloZaměstnance a TenantCountry do tokenů.
   1. Chcete-li vytvořit zásadu, spusťte následující příkaz:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Chcete-li zobrazit nové zásady a získat ObjectId zásad, spusťte následující příkaz:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Přiřaďte zásadu k instančnímu objektu. Také je nutné získat ObjectId objektu služby.
   1. Pokud chcete zobrazit všechny instanční objekty vaší organizace, můžete zadat [dotaz na rozhraní Microsoft Graph API](/graph/traverse-the-graph). Nebo v [Microsoft Graph Exploreru](https://developer.microsoft.com/graph/graph-explorer)se přihlaste k účtu služby Azure AD.
   2. Pokud máte identifikátor ObjectId objektu služby, spusťte následující příkaz:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Příklad: vytvoření a přiřazení zásady, která používá transformaci deklarací identity v tokenech vydaných pro instanční objekt

V tomto příkladu vytvoříte zásadu, která emituje vlastní deklaraci identity "JoinedData" a JWTs vydaná pro propojené objekty služby. Tato deklarace identity obsahuje hodnotu vytvořenou připojením k datům uloženým v atributu extensionAttribute1 objektu User s ". sandbox". V tomto příkladu vyloučíme základní sady deklarací identity v tokenech.

1. Vytvořte zásadu mapování deklarací identity. Tato zásada propojená s konkrétními instančními objekty přidá deklarace ČísloZaměstnance a TenantCountry do tokenů.
   1. Chcete-li vytvořit zásadu, spusťte následující příkaz:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Chcete-li zobrazit nové zásady a získat ObjectId zásad, spusťte následující příkaz:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Přiřaďte zásadu k instančnímu objektu. Také je nutné získat ObjectId objektu služby.
   1. Pokud chcete zobrazit všechny instanční objekty vaší organizace, můžete zadat [dotaz na rozhraní Microsoft Graph API](/graph/traverse-the-graph). Nebo v [Microsoft Graph Exploreru](https://developer.microsoft.com/graph/graph-explorer)se přihlaste k účtu služby Azure AD.
   2. Pokud máte identifikátor ObjectId objektu služby, spusťte následující příkaz:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="see-also"></a>Viz také

- Informace o tom, jak přizpůsobit deklarace identity vydané v tokenu SAML prostřednictvím Azure Portal, najdete v tématu [How to: Customize Claims vydaných v tokenu SAML pro podnikové aplikace.](active-directory-saml-claims-customization.md)
- Další informace o atributech rozšíření najdete v tématu [použití atributů rozšíření schématu adresáře v deklaracích identity](active-directory-schema-extensions.md).
