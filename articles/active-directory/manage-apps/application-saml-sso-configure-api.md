---
title: Použití rozhraní API pro Microsoft Graph ke konfiguraci jednotného přihlašování založeného na SAML
titleSuffix: Azure Active Directory
description: Potřebujete pro víc instancí aplikace nastavit jednotné přihlašování založené na SAML? Naučte se, jak ušetřit čas pomocí Microsoft Graph rozhraní API k automatizaci konfigurace jednotného přihlašování založeného na SAML.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: luleon
ms.openlocfilehash: 50ee9e3c22c885931e2586f65ba2fa3353fccfeb
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2020
ms.locfileid: "85355841"
---
# <a name="automate-saml-based-sso-app-configuration-with-microsoft-graph-api"></a>Automatizace konfigurace aplikace jednotného přihlašování založeného na SAML pomocí rozhraní Microsoft Graph API

V tomto článku se dozvíte, jak vytvořit a nakonfigurovat aplikaci z Galerie Azure Active Directory (Azure AD). Tento článek používá AWS jako příklad, ale postupy v tomto článku můžete použít pro libovolnou aplikaci založenou na SAML v galerii Azure AD.

**Postup použití rozhraní API Microsoft Graph k automatizaci konfigurace jednotného přihlašování založeného na SAML**

| Krok  | Podrobnosti  |
|---------|---------|
| [1. Vytvoření aplikace Galerie](#step-1-create-the-gallery-application) | Přihlaste se k klientovi rozhraní API. <br> Načtení aplikace Galerie <br> Vytvoření aplikace Galerie|
| [2. konfigurace jednotného přihlašování](#step-2-configure-single-sign-on) | Načíst ID objektu aplikace a ID objektu zabezpečení služby <br> Nastavit režim jednotného přihlašování <br> Nastavte základní adresy URL SAML, jako je identifikátor, adresa URL odpovědi, přihlašovací adresa URL. <br> Přidat aplikační role (volitelné)|
| [3. Konfigurace mapování deklarací identity](#step-3-configure-claims-mapping) | Vytvořit zásadu mapování deklarací <br> Přiřazení zásad mapování deklarací k instančnímu objektu|
| [4. konfigurace podpisového certifikátu](#step-4-configure-signing-certificate) | Vytvoření certifikátu <BR> Přidat vlastní podpisový klíč <br> Aktivace vlastního podpisového klíče|
| [5. přiřazení uživatelů](#step-5-assign-users) | Přiřazení uživatelů a skupin k aplikaci
| [6. konfigurace strany aplikace](#step-6-configure-the-application-side)| Získat metadata Azure AD SAML

**Seznam všech rozhraní API používaných v dokumentaci**

Ujistěte se, že máte odpovídající oprávnění k volání následujících rozhraní API.

|Typ prostředku |Metoda |
|---------|---------|
|[applicationTemplate](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta)|[Seznam applicationTemplate](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) <br>[Vytvoření instance applicationTemplate](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http)|
|[servicePrincipals](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-1.0)|[Aktualizovat servicePrincipal](https://docs.microsoft.com/graph/api/serviceprincipal-update?view=graph-rest-1.0&tabs=http) <br> [Vytvořit appRoleAssignments](https://docs.microsoft.com/graph/api/serviceprincipal-post-approleassignments?view=graph-rest-1.0&tabs=http) <br> [Přiřadit claimsMappingPolicies](https://docs.microsoft.com/graph/api/serviceprincipal-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http)|
|[vyrovnání](https://docs.microsoft.com/graph/api/resources/application?view=graph-rest-1.0)|[Aktualizovat aplikaci](https://docs.microsoft.com/graph/api/application-update?view=graph-rest-1.0&tabs=http)|
|[claimsMappingPolicy](https://docs.microsoft.com/graph/api/resources/claimsmappingpolicy?view=graph-rest-beta)| [Vytvořit claimsMappingPolicy](https://docs.microsoft.com/graph/api/claimsmappingpolicy-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http)

>[!NOTE]
>Objekty odpovědi uvedené v tomto článku je možné zkrátit, aby bylo možné je přečíst. Všechny vlastnosti budou vráceny ze skutečného volání.

## <a name="step-1-create-the-gallery-application"></a>Krok 1: Vytvoření aplikace Galerie

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Přihlaste se k Microsoft Graph Exploreru (doporučeno), autorovi nebo jakémukoli jinému klientovi API, který používáte.

1. Spustit [průzkumníka Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)
2. Vyberte možnost **Přihlásit se pomocí Microsoftu** a přihlaste se pomocí globálního správce Azure AD nebo přihlašovacích údajů správce aplikací.
3. Po úspěšném přihlášení se zobrazí podrobnosti o uživatelském účtu v levém podokně.

### <a name="retrieve-the-gallery-application-template-identifier"></a>Načtení identifikátoru šablony aplikace Galerie

Aplikace v galerii aplikací Azure AD mají každý z nich [šablonu aplikace](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) , která popisuje metadata pro danou aplikaci. Pomocí této šablony můžete ve vašem tenantovi vytvořit instanci aplikace a instančního objektu pro správu.

#### <a name="request"></a>Žádost

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>Odpověď

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "Amazon Web Services (AWS)",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": null    
  
}
```

### <a name="create-the-gallery-application"></a>Vytvoření aplikace Galerie

Pomocí ID šablony, které jste načetli pro aplikaci v posledním kroku, [vytvořte instanci](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) aplikace a instančního objektu ve vašem tenantovi.

> [!NOTE] 
> K vytvoření instance [aplikací mimo galerii](add-non-gallery-app.md)můžete použít rozhraní applicationTemplate API. Použijte applicationTemplateId `8adf8e6e-67b2-4cf2-a259-e3dc5476c621` .
#### <a name="request"></a>Žádost

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/8b1025e4-1dd2-430b-a150-2ef79cd700f5/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>Odpověď


<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationServicePrincipal"
} -->

```http
HTTP/1.1 201 OK
Content-type: application/json


{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-configure-single-sign-on"></a>Krok 2: Konfigurace jednotného přihlašování

### <a name="retrieve-app-object-id-and-service-principal-object-id"></a>Načíst ID objektu aplikace a ID objektu zabezpečení služby

Použijte odpověď z předchozího volání a načtěte a uložte ID objektu aplikace a ID objektu zabezpečení služby.

```
"application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63"
}
"servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e"
}
```
### <a name="set-single-sign-on-mode"></a>Nastavit režim jednotného přihlašování

V tomto příkladu nastavíte `saml` jako režim jednotného přihlašování v [typu prostředku servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-1.0). Další vlastnosti jednotného přihlašování SAML, které můžete konfigurovat, jsou: `notificationEmailAddresses` , `loginUrl` a.`samlSingleSignOnSettings.relayState`

#### <a name="request"></a>Žádost

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: servicePrincipal/json

{
    "preferredSingleSignOnMode": "saml"
}
```

#### <a name="response"></a>Odpověď

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```

### <a name="set-basic-saml-urls-such-as-identifier-reply-url-sign-on-url"></a>Nastavte základní adresy URL SAML, jako je identifikátor, adresa URL odpovědi, přihlašovací adresa URL.

Nastavte identifikátor a adresy URL odpovědí pro AWS v objektu Application.

#### <a name="request"></a>Žádost

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/applications/cbc071a6-0fa5-4859-8g55-e983ef63df63
Content-type: applications/json

{
    "web": {
        "redirectUris": [
            "https://signin.aws.amazon.com/saml"
        ] 
    },
    "identifierUris": [
        "https://signin.aws.amazon.com/saml"
    ]    
}
```
#### <a name="response"></a>Odpověď

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```
### <a name="add-app-roles-optional"></a>Přidat aplikační role (volitelné)

Pokud aplikace vyžaduje informace o roli v tokenu, přidejte definici rolí do objektu Application. Pro AWS můžete [Povolit zřizování uživatelů](../app-provisioning/application-provisioning-configure-api.md) pro načtení všech rolí z daného účtu AWS. 

Další informace najdete v tématu [konfigurace deklarace identity role vydané v tokenu SAML](../develop/active-directory-enterprise-app-role-management.md) .

> [!NOTE] 
> Při přidávání rolí aplikace neměňte výchozí aplikační role msiam_access. 

#### <a name="request"></a>Žádost

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/serviceprincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: serviceprincipals/json

{
"appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "454dc4c2-8176-498e-99df-8c4efcde41ef",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-admin,arn:aws:iam::212743507312:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Finance,WAAD",
            "displayName": "Finance,WAAD",
            "id": "8642d5fa-18a3-4245-ab8c-a96000c1a217",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-finance,arn:aws:iam::212743507312:saml-provider/WAAD"
        }
    ]

}
```

#### <a name="response"></a>Odpověď

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-3-configure-claims-mapping"></a>Krok 3: Konfigurace mapování deklarací identity

### <a name="create-claims-mapping-policy"></a>Vytvořit zásadu mapování deklarací

Kromě základních deklarací identity nakonfigurujte následující deklarace identity pro Azure AD pro vygenerování v tokenu SAML:

| Název deklarace identity | Zdroj  |
|---------|---------|
| `https://aws.amazon.com/SAML/Attributes/Role` | assignedroles| 
| `https://aws.amazon.com/SAML/Attributes/RoleSessionName` | třídy |
| `https://aws.amazon.com/SAML/Attributes/SessionDuration` | "900" |
| role | assignedroles |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` | třídy |

Další informace najdete v tématu [přizpůsobení deklarací identity emitovaných v tokenu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).

#### <a name="request"></a>Žádost

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/v1.0/policies/claimsMappingPolicies
Content-type: claimsMappingPolicies/json

{
    "definition":[
            "{\"ClaimsMappingPolicy\": {
                \"Version\": 1,
                \"IncludeBasicClaimSet\": \"true\",
                \"ClaimsSchema\": [
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/Role\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"900\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"appRoles\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"
                    }
                ]
            }
        }"

    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

#### <a name="response"></a>Odpověď

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.claimsMappingPolicies",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: claimsMappingPolicies/json

{
    "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#policies/claimsMappingPolicies/$entity",
    "id": "6b33aa8e-51f3-41a6-a0fd-d660d276197a",
    "definition": [
        "{\"ClaimsMappingPolicy\": {\"Version\": 1,\"IncludeBasicClaimSet\": \"true\",\"ClaimsSchema\": [{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"https://aws.amazon.com/SAML/Attributes/Role\"\r\n                    },{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"},{\"Source\": \"user\",\"ID\": \"900\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"},{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"appRoles\"},{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"}]}}"
    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

### <a name="assign-claims-mapping-policy-to-service-principal"></a>Přiřazení zásad mapování deklarací k instančnímu objektu

#### <a name="request"></a>Žádost

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/claimsMappingPolicies/$ref

Content-type: claimsMappingPolicies/json

{
  "@odata.id":"https://graph.microsoft.com/v1.0/policies/claimsMappingPolicies/6b33aa8e-51f3-41a6-a0fd-d660d276197a"
}
```

#### <a name="response"></a>Odpověď

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-4-configure-signing-certificate"></a>Krok 4: Konfigurace podpisového certifikátu

Použití rozhraní [applicationTemplate](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta) API ve výchozím nastavení nevytváří podpisový certifikát. Vytvořte vlastní podpisový certifikát a přiřaďte ho k aplikaci. 

### <a name="create-a-custom-signing-certificate"></a>Vytvoření vlastního podpisového certifikátu

K otestování můžete použít následující příkaz prostředí PowerShell k získání certifikátu podepsaného svým držitelem. Pomocí osvědčeného postupu zabezpečení z vaší společnosti vytvořte podpisový certifikát pro produkční prostředí.

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [string]$fqdn,
    [Parameter(Mandatory=$true)]
    [string]$pwd,
    [Parameter(Mandatory=$true)]
    [string]$location
) 

if (!$PSBoundParameters.ContainsKey('location'))
{
    $location = "."
} 

$cert = New-SelfSignedCertificate -certstorelocation cert:\currentuser\my -DnsName $fqdn
$pwdSecure = ConvertTo-SecureString -String $pwd -Force -AsPlainText
$path = 'cert:\currentuser\my\' + $cert.Thumbprint
$cerFile = $location + "\\" + $fqdn + ".cer"
$pfxFile = $location + "\\" + $fqdn + ".pfx" 

Export-PfxCertificate -cert $path -FilePath $pfxFile -Password $pwdSecure
Export-Certificate -cert $path -FilePath $cerFile
```

### <a name="add-a-custom-signing-key"></a>Přidat vlastní podpisový klíč

Do instančního objektu přidejte následující informace:

* Privátní klíč
* Heslo
* Veřejný klíč 

Extrahujte privátní a veřejný klíč s kódováním base64 ze souboru PFX. Další informace o vlastnostech najdete v tématu [typ prostředku přihlašovacích údajů](https://docs.microsoft.com/graph/api/resources/keycredential?view=graph-rest-1.0)ke službě.

Ujistěte se, že keyId pro přihlašovací údaje použité pro Sign se shoduje s keyIdem passwordCredential.

Můžete vygenerovat `customkeyIdentifier` pomocí získání hodnoty hash kryptografického otisku certifikátu.

```csharp
  public string GetSha256FromThumbprint(string thumbprint)
  {
      var message = Encoding.ASCII.GetBytes(thumbprint);
      SHA256Managed hashString = new SHA256Managed();
      return Convert.ToBase64String(hashString.ComputeHash(message));
  }
```

#### <a name="request"></a>Žádost

> [!NOTE]
> Hodnota klíče ve vlastnosti klíčová slova je pro čtení zkrácená. Hodnota je Base 64 kódovaná. U privátního klíče se jedná o vlastnost `usage` Sign. Pro veřejný klíč `usage` je vlastnost "ověřit".

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "keyCredentials":[
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Sign",
            "key":"MIIKIAIBAz.....HBgUrDgMCERE20nuTptI9MEFCh2Ih2jaaLZBZGeZBRFVNXeZmAAgIH0A==",
            "displayName": "CN=awsAPI"
        },
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "e35a7d11-fef0-49ad-9f3e-aacbe0a42c42",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "key": "MIIDJzCCAg+gAw......CTxQvJ/zN3bafeesMSueR83hlCSyg==",
            "displayName": "CN=awsAPI"
        }

    ],
    "passwordCredentials": [
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "endDateTime": "2022-01-27T19:40:33Z",
            "startDateTime": "2020-04-20T19:40:33Z",
            "secretText": "61891f4ee44d"
        }
    ]
}
```

#### <a name="response"></a>Odpověď

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

### <a name="activate-the-custom-signing-key"></a>Aktivace vlastního podpisového klíče

Je nutné nastavit `preferredTokenSigningKeyThumbprint` vlastnost na kryptografický otisk certifikátu, který má služba Azure AD použít k podepsání odpovědi SAML. 

#### <a name="request"></a>Žádost

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "preferredTokenSigningKeyThumbprint": "AC09FEF18DDE6983EE2A164FBA3C4DD7518BD787"
}
```

#### <a name="response"></a>Odpověď

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```
## <a name="step-5-assign-users"></a>Krok 5: přiřazení uživatelů

### <a name="assign-users-and-groups-to-the-application"></a>Přiřazení uživatelů a skupin k aplikaci

Přiřaďte k instančnímu objektu následujícího uživatele a přiřaďte AWS_Role1. 

| Název  | ID  |
|---------|---------|
| ID uživatele (principalId) | 6cad4079-4e79-4a3f-9efb-ea30a14bdb26 |
| Typ (principalType) | Uživatel |
| ID aplikační role (appRoleId) | 454dc4c2-8176-498e-99df-8c4efcde41ef |
| servicePrincipalID (resourceId) | 515f62cb-d18a-4dca-bec3-bb0bf31deeea |

#### <a name="request"></a>Žádost

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/appRoleAssignments

Content-type: appRoleAssignments/json

{
  "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
  "principalType": "User",
  "appRoleId":"454dc4c2-8176-498e-99df-8c4efcde41ef",
  "resourceId":"515f62cb-d18a-4dca-bec3-bb0bf31deeea"
}
```
#### <a name="response"></a>Odpověď

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 201 
Content-type: appRoleAssignments/json

{
    "id": "rq7hyzl4yECaNZleMrTpDV-OCe5TEl5Ao_o76XMrRFU",
    "creationTimestamp": "2020-04-23T17:38:13.2508567Z",
    "appRoleId": "454dc4c2-8176-498e-99df-8c4efcde41ef",
    "principalDisplayName": "User 1",
    "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
    "principalType": "User",
    "resourceDisplayName": "AWS API Created",
    "resourceId": "515f62cb-d18a-4dca-bec3-bb0bf31deeea"
}
```

Další informace najdete v tématu typ prostředku [appRoleAssignment](https://docs.microsoft.com/graph/api/resources/approleassignment?view=graph-rest-1.0) .

## <a name="step-6-configure-the-application-side"></a>Krok 6: Konfigurace strany aplikace

### <a name="get-azure-ad-saml-metadata"></a>Získat metadata Azure AD SAML

Pomocí následující adresy URL získáte metadata Azure AD SAML pro konkrétní nakonfigurovanou aplikaci. Metadata obsahují mimo jiné informace, jako je například podpisový certifikát, Azure AD entityID a Azure AD SingleSignOnService.

https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id}

## <a name="next-steps"></a>Další kroky
- [Použití rozhraní API pro Microsoft Graph ke konfiguraci zřizování uživatelů](../app-provisioning/application-provisioning-configure-api.md)
- [Použití sestavy aktivity aplikace AD FS k migraci aplikací do Azure AD](migrate-adfs-application-activity.md)
