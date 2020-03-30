---
title: Přizpůsobení deklarací aplikací klienta Azure AD (PowerShell)
titleSuffix: Microsoft identity platform
description: Tato stránka popisuje mapování deklarací identity služby Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 49860504da8dd2a1b994a23a24df95f59c959c90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263189"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Postup: Přizpůsobení deklarací vysílaných v tokenech pro konkrétní aplikaci v tenantovi (Preview)

> [!NOTE]
> Tato funkce nahrazuje a nahrazuje [deklarace identity](active-directory-saml-claims-customization.md) nabízené prostřednictvím portálu dnes. Ve stejné aplikaci, pokud přizpůsobit deklarace identity pomocí portálu kromě Graph/PowerShell metoda podrobně v tomto dokumentu, tokeny vydané pro tuto aplikaci bude ignorovat konfiguraci na portálu. Konfigurace provedené prostřednictvím metod popsaných v tomto dokumentu se na portálu neprojeví.

Tato funkce se používá správci tenanta k přizpůsobení deklarací vyzařovaných v tokenech pro konkrétní aplikaci v jejich tenantovi. Zásady mapování deklarací identity můžete použít k:

- Vyberte, které deklarace identity jsou zahrnuty v tokenech.
- Vytvořte typy deklarací, které ještě neexistují.
- Zvolte nebo změňte zdroj dat vyzařovaných v konkrétních deklaracích identity.

> [!NOTE]
> Tato funkce je v současné době ve verzi Public Preview. Buďte připravení na to, že jakékoli změny se můžou zrušit nebo odebrat. Tato funkce je dostupná v libovolném předplatném Služby Azure Active Directory (Azure AD) během veřejné verze Preview. Pokud se však funkce stane obecně dostupná, některé aspekty této funkce mohou vyžadovat předplatné Azure AD premium. Tato funkce podporuje konfiguraci zásad mapování deklarací pro protokoly WS-Fed, SAML, OAuth a OpenID Connect.

## <a name="claims-mapping-policy-type"></a>Typ zásad mapování deklarací

Ve službě Azure AD představuje objekt **zásad** sadu pravidel vynucených v jednotlivých aplikacích nebo ve všech aplikacích v organizaci. Každý typ zásady má jedinečnou strukturu se sadou vlastností, které jsou pak použity na objekty, ke kterým jsou přiřazeny.

Zásady mapování deklarací jsou typem objektu **Policy,** který upravuje deklarace vysílané v tokenech vydaných pro konkrétní aplikace.

## <a name="claim-sets"></a>Sady deklarací

Existují určité sady deklarací, které definují, jak a kdy se používají v tokenech.

| Sada deklarací | Popis |
|---|---|
| Základní sada deklarací | Jsou k dispozici v každém tokenu bez ohledu na zásady. Tyto deklarace identity jsou také považovány za omezené a nelze je změnit. |
| Základní sada deklarací | Zahrnuje deklarace identity, které jsou ve výchozím nastavení emitovány pro tokeny (kromě základní sady deklarací). Základní deklarace identity můžete vynechat nebo upravit pomocí zásad mapování deklarací identity. |
| Sada omezených deklarací | Nelze změnit pomocí zásad. Zdroj dat nelze změnit a při generování těchto deklarací identity není použita žádná transformace. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tabulka 1: Sada omezených deklarací JSON Web Token (JWT)

| Typ deklarace (název) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| Herec |
| actortoken |
| Aio |
| altsecid |
| Amr |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| Appid |
| appidacr |
| Tvrzení |
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
| Cnf |
| kód |
| ovládací prvky |
| credential_keys |
| Csr |
| csr_type |
| Deviceid |
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
| Grafu |
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
| iat |
| zprostředkovatel identity |
| Idp |
| in_corp |
| Instance |
| ipaddr |
| isbrowserhostedapp |
| Iss |
| jwk |
| key_id |
| key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| nameid |
| Nbf |
| netbios_name |
| Nonce |
| Oid |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| heslo |
| platf |
| polids |
| pop_jwk |
| preferred_username |
| previous_refresh_token |
| primary_sid |
| puid |
| pwd_exp |
| pwd_url |
| redirect_uri |
| refresh_token |
| refreshtoken |
| request_nonce |
| prostředek |
| role |
| role |
| scope |
| Scp |
| Sid |
| Podpis |
| signin_state |
| src1 |
| src2 |
| Dílčí |
| tbid |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| Tid |
| tokenAutologonEnabled |
| důvěryhodnýpro delegování |
| unique_name |
| Upn |
| user_setting_sync_url |
| uživatelské jméno |
| Uti |
| Ver |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>Tabulka 2: Sada nároků s omezeným přístupem SAML

| Typ deklarace (URI) |
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

## <a name="claims-mapping-policy-properties"></a>Vlastnosti zásad mapování deklarací

Chcete-li řídit, jaké deklarace jsou emitovány a odkud data pocházejí, použijte vlastnosti zásadmapování deklarací. Pokud zásada není nastavena, systém vydává tokeny, které obsahují základní sadu deklarací, základní sadu deklarací a všechny [volitelné deklarace identity,](active-directory-optional-claims.md) které se aplikace rozhodla přijmout.

### <a name="include-basic-claim-set"></a>Zahrnout základní sadu deklarací

**Řetězec:** IncludeBasicClaimSet

**Datový typ:** Logická hodnota (pravda nebo nepravda)

**Shrnutí:** Tato vlastnost určuje, zda je základní sada deklarací zahrnuta do tokenů ovlivněných touto zásadou.

- Pokud je nastavena na True, všechny deklarace identity v základní sadě deklarací jsou emitovány v tokenech ovlivněných zásadou. 
- Pokud je nastavena na False, deklarace identity v základní sadě deklarací nejsou v tokenech, pokud nejsou jednotlivě přidány do vlastnosti schématu deklarací stejné zásady.

> [!NOTE] 
> Deklarace v základní sadě deklarací jsou k dispozici v každém tokenu, bez ohledu na to, co tato vlastnost je nastavena na. 

### <a name="claims-schema"></a>Schéma deklarací identity

**Řetězec:** ClaimsSchema

**Datový typ:** Objekt blob JSON s jednou nebo více položkami schématu deklarací

**Shrnutí:** Tato vlastnost definuje, které deklarace identity jsou k dispozici v tokeny ovlivněny zásady, kromě základní sady deklarací a základní deklarace.
Pro každou položku schématu deklarace definované v této vlastnosti jsou vyžadovány určité informace. Určete, odkud data pocházejí (**dvojice Hodnota** nebo **Zdroj/ID**) a které deklarace tvrdí, že data jsou emitována jako (**Typ deklarace).**

### <a name="claim-schema-entry-elements"></a>Prvky položky schématu deklarací

**Hodnota:** Value Element definuje statickou hodnotu jako data, která mají být vyzařována v deklaraci.

**Dvojice zdroj/ID:** Elementy Source a ID definují, odkud pocházejí data v deklaraci. 

Nastavte element Source na jednu z následujících hodnot: 

- "uživatel": Data v deklaraci je vlastnost objektu User. 
- "aplikace": Data v deklaraci je vlastnost na instančním objektu služby aplikace (klienta). 
- "prostředek": Data v deklaraci je vlastnost na instančním objektu služby prostředku.
- "cílová skupina": Data v deklaraci je vlastnost na instanční objekt, který je cílovou skupinou tokenu (klient nebo instanční objekt služby prostředku).
- "společnost": Data v deklaraci je vlastnost na objektu společnosti klienta prostředku.
- "transformace": Data v deklaraci je z transformace deklarací (viz část "Transformace deklarací identity" dále v tomto článku).

Pokud je zdrojem transformace, **prvek TransformationID** musí být zahrnuty v této definici deklarace také.

Prvek ID určuje, která vlastnost na zdroji poskytuje hodnotu deklarace. V následující tabulce jsou uvedeny hodnoty ID platné pro každou hodnotu zdroje.

#### <a name="table-3-valid-id-values-per-source"></a>Tabulka 3: Platné hodnoty ID pro zdroj

| Zdroj | ID | Popis |
|-----|-----|-----|
| Uživatel | surname | Příjmení |
| Uživatel | givenname | jméno |
| Uživatel | Displayname | Zobrazovaný název |
| Uživatel | Objectid | ObjectId |
| Uživatel | pošta | E-mailová adresa |
| Uživatel | Userprincipalname | Hlavní název uživatele |
| Uživatel | Oddělení|Oddělení|
| Uživatel | název_účtu premisespremisessamname | Místní název účtu SAM |
| Uživatel | netbiosname| Název rozhraní NetBios |
| Uživatel | název dnsdomainname | Název domény DNS |
| Uživatel | identifikátor zabezpečení podle místního | Místní identifikátor zabezpečení |
| Uživatel | Companyname| Název organizace |
| Uživatel | Streetaddress | Adresa |
| Uživatel | Postalcode | PSČ |
| Uživatel | preferredlanguange | Preferovaný jazyk |
| Uživatel | onpremisesuserprincipalname | Místní upn |
| Uživatel | přezdívka na poště | Přezdívka pošty |
| Uživatel | atribut extensionattribute1 | Atribut rozšíření 1 |
| Uživatel | atribut extensionattribute2 | Atribut rozšíření 2 |
| Uživatel | atribut rozšíření3 | Atribut rozšíření 3 |
| Uživatel | atribut extensionattribute4 | Atribut rozšíření 4 |
| Uživatel | atribut extensionattribute5 | Atribut rozšíření 5 |
| Uživatel | atribut extensionattribute6 | Atribut rozšíření 6 |
| Uživatel | atribut rozšíření7 | Atribut rozšíření 7 |
| Uživatel | atribut extensionattribute8 | Atribut rozšíření 8 |
| Uživatel | atribut extensionattribute9 | Atribut rozšíření 9 |
| Uživatel | atribut extensionattribute10 | Atribut rozšíření 10 |
| Uživatel | atribut extensionattribute11 | Atribut rozšíření 11 |
| Uživatel | atribut extensionattribute12 | Atribut rozšíření 12 |
| Uživatel | atribut extensionattribute13 | Atribut rozšíření 13 |
| Uživatel | atribut extensionattribute14 | Atribut rozšíření 14 |
| Uživatel | atribut extensionattribute15 | Atribut rozšíření 15 |
| Uživatel | othermail | Jiná pošta |
| Uživatel | country | Země |
| Uživatel | city | Město |
| Uživatel | state | Stav |
| Uživatel | pracovní titul | Název pozice |
| Uživatel | Employeeid | Identifikační číslo zaměstnance |
| Uživatel | faxčíslo | Faxové telefonní číslo |
| aplikace, zdroj, publikum | Displayname | Zobrazovaný název |
| aplikace, zdroj, publikum | Namítl | ObjectId |
| aplikace, zdroj, publikum | tags | Značka hlavního povinného servisu |
| Společnost | tenantzemě | Země nájemce |

**ID transformace:** Prvek TransformationID musí být poskytnuta pouze v případě, že Source prvek je nastavena na "transformace".

- Tento prvek musí odpovídat ID prvek položky transformace v **ClaimsTransformation** vlastnost, která definuje, jak jsou generována data pro tuto deklaraci identity.

**Typ deklarace:** **Prvky JwtClaimType** a **SamlClaimType** definují, na které deklarace nároku se vztahuje tato položka schématu deklarací.

- JwtClaimType musí obsahovat název deklarace, která má být emitován v JWTs.
- SamlClaimType musí obsahovat identifikátor URI deklarace deklarace, která má být emitován v tokenech SAML.

> [!NOTE]
> Názvy a identifikátory URI deklarací deklarací v omezené sadě deklarací identity nelze použít pro prvky typu deklarace identity. Další informace naleznete v části Výjimky a omezení dále v tomto článku.

### <a name="claims-transformation"></a>Transformace deklarací identity

**Řetězec:** ClaimsTransformation

**Datový typ:** Objekt blob JSON s jednou nebo více položkami transformace 

**Shrnutí:** Pomocí této vlastnosti můžete použít běžné transformace na zdrojová data ke generování výstupních dat pro deklarace identity zadané ve schématu deklarací.

**ID:** Pomocí prvku ID můžete odkazovat na tuto položku transformace v položce schématu deklarací Transformizování ID. Tato hodnota musí být jedinečná pro každou položku transformace v rámci této zásady.

**Metoda transformace:** Prvek TransformationMethod určuje, která operace se provádí ke generování dat pro deklaraci.

Na základě zvolené metody se očekává sada vstupů a výstupů. Definujte vstupy a výstupy pomocí **inputclaims**, **InputParameters** a **OutputClaims.**

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tabulka 4: Transformační metody a očekávané vstupy a výstupy

|Metoda transformace|Očekávaný vstup|Očekávaný výstup|Popis|
|-----|-----|-----|-----|
|Spojit|string1, string2, oddělovač|outputClaim|Spojuje vstupní řetězce pomocí oddělovače mezi nimi. Příklad: string1:"foo@bar.com" , string2:"sandbox" , oddělovač:"."foo@bar.com.sandboxmá výsledky outputClaim:" "|
|Extrahovatpřednastavení služby Amail|pošta|outputClaim|Extrahuje místní část e-mailové adresy. Například: mail:"foo@bar.com" vede k outputClaim:"foo". Pokud \@ není k dispozici žádné znamení, je původní vstupní řetězec vrácen tak, jak je.|

**InputClaims:** Pomocí prvku InputClaims předavte data z položky schématu deklarace identity do transformace. Má dva atributy: **ClaimTypeReferenceId** a **TransformationClaimType**.

- **ClaimTypeReferenceId** je spojen s ID prvek položky schématu deklarace najít příslušnou vstupní deklaraci. 
- **TransformationClaimType** se používá k zadání jedinečného názvu tohoto vstupu. Tento název se musí shodovat s jedním z očekávaných vstupů pro metodu transformace.

**Parametry vstupu:** Pomocí prvku InputParameters předavte transformaci konstantní hodnotu. Má dva atributy: **Hodnota** a **ID**.

- **Hodnota** je skutečná konstantní hodnota, která má být předána.
- **ID** se používá k zadání jedinečného názvu vstupu. Název musí odpovídat jednomu z očekávaných vstupů pro metodu transformace.

**OutputClaims:** Pomocí elementu OutputClaims uchovávat data generovaná transformací a navazujte je na položku schématu deklarace identity. Má dva atributy: **ClaimTypeReferenceId** a **TransformationClaimType**.

- **ClaimTypeReferenceId** je spojen s ID položky schématu deklarace najít příslušnou výstupní deklaraci.
- **TransformationClaimType** se používá k zadání jedinečného názvu výstupu. Název musí odpovídat jednomu z očekávaných výstupů pro metodu transformace.

### <a name="exceptions-and-restrictions"></a>Výjimky a omezení

**SAML NameID a UPN:** Atributy, ze kterých jsou zdrojem NameID a UPN hodnoty a deklarace transformace, které jsou povoleny, jsou omezené. Uvedené povolené hodnoty naleznete v tabulce 5 a tabulce 6.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tabulka 5: Atributy povolené jako zdroj dat pro SAML NameID

|Zdroj|ID|Popis|
|-----|-----|-----|
| Uživatel | pošta|E-mailová adresa|
| Uživatel | Userprincipalname|Hlavní název uživatele|
| Uživatel | název_účtu premisespremisessamname|Název účtu Sam v místním prostředí|
| Uživatel | Employeeid|Identifikační číslo zaměstnance|
| Uživatel | atribut extensionattribute1 | Atribut rozšíření 1 |
| Uživatel | atribut extensionattribute2 | Atribut rozšíření 2 |
| Uživatel | atribut rozšíření3 | Atribut rozšíření 3 |
| Uživatel | atribut extensionattribute4 | Atribut rozšíření 4 |
| Uživatel | atribut extensionattribute5 | Atribut rozšíření 5 |
| Uživatel | atribut extensionattribute6 | Atribut rozšíření 6 |
| Uživatel | atribut rozšíření7 | Atribut rozšíření 7 |
| Uživatel | atribut extensionattribute8 | Atribut rozšíření 8 |
| Uživatel | atribut extensionattribute9 | Atribut rozšíření 9 |
| Uživatel | atribut extensionattribute10 | Atribut rozšíření 10 |
| Uživatel | atribut extensionattribute11 | Atribut rozšíření 11 |
| Uživatel | atribut extensionattribute12 | Atribut rozšíření 12 |
| Uživatel | atribut extensionattribute13 | Atribut rozšíření 13 |
| Uživatel | atribut extensionattribute14 | Atribut rozšíření 14 |
| Uživatel | atribut extensionattribute15 | Atribut rozšíření 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tabulka 6: Metody transformace povolené pro SAML NameID

| Metoda transformace | Omezení |
| ----- | ----- |
| Extrahovatpřednastavení služby Amail | Žádný |
| Spojit | Přípona, která je spojena, musí být ověřenou doménou klienta prostředků. |

### <a name="custom-signing-key"></a>Vlastní podpisový klíč

Vlastní podpisový klíč musí být přiřazen k objektu instančního objektu, aby se zásady mapování deklarací projevily. Tím je zajištěno potvrzení, že tokeny byly změněny tvůrcem zásad mapování deklarací identity a chrání aplikace před zásadami mapování deklarací vytvořenými škodlivými aktéry. Chcete-li přidat vlastní podpisový klíč, můžete pomocí rutiny `new-azureadapplicationkeycredential` Prostředí Azure PowerShell vytvořit pověření symetrického klíče pro objekt aplikace. Další informace o této rutině Prostředí Azure PowerShell najdete v tématu [New-AzureADApplicationKeyCredential](https://docs.microsoft.com/powerShell/module/Azuread/New-AzureADApplicationKeyCredential?view=azureadps-2.0).

Aplikace, které mají povolené mapování deklarací identity, musí ověřit své podpisové klíče tokenů připojením `appid={client_id}` k jejich [požadavkům na metadata OpenID Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document). Níže je uveden formát dokumentu metadat OpenID Connect, který byste měli použít: 

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="cross-tenant-scenarios"></a>Scénáře napříč tenanty

Zásady mapování deklarací se nevztahují na uživatele typu Host. Pokud se uživatel typu Host pokusí o přístup k aplikaci se zásadami mapování deklarací přiřazenými k instančnímu objektu, je vydán výchozí token (zásada nemá žádný vliv).

## <a name="claims-mapping-policy-assignment"></a>Přiřazení zásad mapování deklarací

Zásady mapování deklarací lze přiřadit pouze k objektům instančního objektu.

### <a name="example-claims-mapping-policies"></a>Příklad zásad mapování deklarací identity

Ve službě Azure AD je možné mnoho scénářů, když můžete přizpůsobit deklarace vysílané v tokenech pro konkrétní instanční objekty. V této části procházíme několik běžných scénářů, které vám pomohou pochopit, jak používat typ zásad mapování deklarací identity.

#### <a name="prerequisites"></a>Požadavky

V následujících příkladech vytvoříte, aktualizujete, propojíte a odstraníte zásady pro instanční objekty. Pokud jste novým Azure AD, doporučujeme vám, abyste [se dozvěděli, jak získat klienta Azure AD,](quickstart-create-new-tenant.md) než budete pokračovat v těchto příkladech.

Chcete-li začít, postupujte takto:

1. Stáhněte si nejnovější [verzi preview modulu Azure AD PowerShell .](https://www.powershellgallery.com/packages/AzureADPreview)
1. Spusťte příkaz Připojit a přihlaste se ke svému účtu správce Azure AD. Spusťte tento příkaz při každém spuštění nové relace.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Chcete-li zobrazit všechny zásady, které byly vytvořeny ve vaší organizaci, spusťte následující příkaz. Doporučujeme spustit tento příkaz po většině operací v následujících scénářích, chcete-li zkontrolovat, zda jsou vaše zásady vytvářeny podle očekávání.

   ``` powershell
   Get-AzureADPolicy
   ```

#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Příklad: Vytvoření a přiřazení zásady k vynechání základních deklarací tokenů vydaných instančnímu objektu

V tomto příkladu vytvoříte zásadu, která odebere základní sadu deklarací z tokenů vydaných propojeným instančním objektům.

1. Vytvořte zásadu mapování deklarací identity. Tato zásada propojená s konkrétními objekty zabezpečení služeb odebere základní sadu deklarací z tokenů.
   1. Chcete-li vytvořit zásadu, spusťte tento příkaz: 
    
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Chcete-li zobrazit novou zásadu a získat zásadu ObjectId, spusťte následující příkaz:
    
      ``` powershell
      Get-AzureADPolicy
      ```
1. Přiřaďte zásadu k instančnímu objektu. Také je nutné získat ObjectId instančního objektu.
   1. Chcete-li zobrazit všechny instanční objekty vaší organizace, můžete [zadat dotaz na rozhraní Microsoft Graph API](/graph/traverse-the-graph). Nebo v [Aplikaci Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)se přihlaste ke svému účtu Azure AD.
   2. Pokud máte Objekt objektu zabezpečení instančního objektu, spusťte následující příkaz:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Příklad: Vytvoření a přiřazení zásady, která zahrne ID zaměstnance a TenantCountry jako deklarace identity v tokenech vydaných instančnímu objektu.

V tomto příkladu vytvoříte zásadu, která přidá EmployeeID a TenantCountry na tokeny vydané propojeným instančním objektům. EmployeeID je vydáván jako typ deklarace názvu v tokenech SAML i JWTs. TenantCountry je emitován jako typ deklarace země v tokenech SAML a JWTs. V tomto příkladu budeme i nadále zahrnovat základní deklarace nastavené v tokenech.

1. Vytvořte zásadu mapování deklarací identity. Tato zásada, propojená s konkrétními objekty zabezpečení služeb, přidá deklarace identity EmployeeID a TenantCountry do tokenů.
   1. Chcete-li vytvořit zásadu, spusťte následující příkaz:  
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. Chcete-li zobrazit novou zásadu a získat zásadu ObjectId, spusťte následující příkaz:
     
      ``` powershell  
      Get-AzureADPolicy
      ```
1. Přiřaďte zásadu k instančnímu objektu. Také je nutné získat ObjectId instančního objektu. 
   1. Chcete-li zobrazit všechny instanční objekty vaší organizace, můžete [zadat dotaz na rozhraní Microsoft Graph API](/graph/traverse-the-graph). Nebo v [Aplikaci Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)se přihlaste ke svému účtu Azure AD.
   2. Pokud máte Objekt objektu zabezpečení instančního objektu, spusťte následující příkaz:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Příklad: Vytvoření a přiřazení zásady, která používá transformaci deklarací identity v tokenech vydaných instančnímu objektu.

V tomto příkladu vytvoříte zásadu, která vydává vlastní deklaraci "JoinedData" do JWTs vydané propojené instanční objekty. Toto tvrzení obsahuje hodnotu vytvořenou spojením dat uložených v atributu extensionattribute1 v objektu uživatele pomocí ".sandbox". V tomto příkladu vyloučíme základní deklarace nastavené v tokenech.

1. Vytvořte zásadu mapování deklarací identity. Tato zásada, propojená s konkrétními objekty zabezpečení služeb, přidá deklarace identity EmployeeID a TenantCountry do tokenů.
   1. Chcete-li vytvořit zásadu, spusťte následující příkaz:
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. Chcete-li zobrazit novou zásadu a získat zásadu ObjectId, spusťte následující příkaz: 
     
      ``` powershell
      Get-AzureADPolicy
      ```
1. Přiřaďte zásadu k instančnímu objektu. Také je nutné získat ObjectId instančního objektu. 
   1. Chcete-li zobrazit všechny instanční objekty vaší organizace, můžete [zadat dotaz na rozhraní Microsoft Graph API](/graph/traverse-the-graph). Nebo v [Aplikaci Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)se přihlaste ke svému účtu Azure AD.
   2. Pokud máte Objekt objektu zabezpečení instančního objektu, spusťte následující příkaz: 
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="see-also"></a>Viz také

Informace o přizpůsobení deklarací vydaných v tokenu SAML prostřednictvím portálu Azure najdete v tématu [Postup: Přizpůsobení deklarací vydaných v tokenu SAML pro podnikové aplikace.](active-directory-saml-claims-customization.md)
