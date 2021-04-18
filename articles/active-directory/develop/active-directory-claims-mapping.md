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
ms.openlocfilehash: e77155f8a6efd3916ae90fcb562d688bb5b5126f
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598886"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Postupy: přizpůsobení deklarací, které byly vygenerovány v tokenech pro konkrétní aplikaci v tenantovi (Preview)

> [!NOTE]
> Tato funkce nahradí a nahrazuje [přizpůsobení deklarací identity](active-directory-saml-claims-customization.md) , které dnes nabízí portál. Pokud ve stejné aplikaci přizpůsobíte deklarace identity pomocí portálu kromě metody grafu/PowerShellu popsané v tomto dokumentu, budou tokeny vydané pro tuto aplikaci ignorovat konfiguraci na portálu. Konfigurace provedené prostřednictvím metod popsaných v tomto dokumentu se na portálu neprojeví.

> [!NOTE]
> Tato funkce je aktuálně ve verzi Public Preview. Buďte připravení na to, že jakékoli změny se můžou zrušit nebo odebrat. Tato funkce je k dispozici ve všech předplatných Azure Active Directory (Azure AD) ve verzi Public Preview. Pokud se ale funkce všeobecně zpřístupní, můžou některé aspekty funkce vyžadovat předplatné Azure AD Premium. Tato funkce podporuje konfiguraci zásad mapování deklarací pro protokoly WS-dodávání, SAML, OAuth a OpenID Connect.

Tuto funkci používají správci tenanta k přizpůsobení deklarací identity vygenerovaných v tokenech pro konkrétní aplikaci ve svém tenantovi. Zásady mapování deklarací identity můžete použít k těmto akcím:

- Vyberte, které deklarace identity jsou zahrnuté v tokenech.
- Vytvořte typy deklarací, které ještě neexistují.
- Výběr nebo změna zdroje dat vydaných ve specifických deklaracích.

V tomto článku se seznámíme s několika běžnými scénáři, které vám pomůžou naučit se používat [typ zásad mapování deklarací identity](reference-claims-mapping-policy-type.md).

Při vytváření zásad mapování deklarací identity můžete také vygenerovat deklaraci identity z atributu rozšíření schématu adresáře v tokenech. Použijte *ExtensionID* pro atribut Extension namísto *ID* v `ClaimsSchema` elementu.  Další informace o atributech rozšíření najdete v tématu [použití atributů rozšíření schématu adresáře](active-directory-schema-extensions.md).

## <a name="prerequisites"></a>Požadavky

V následujících příkladech můžete vytvořit, aktualizovat, propojit a odstranit zásady pro instanční objekty. Zásady mapování deklarací lze přiřadit pouze objektům instančních objektů. Pokud s Azure AD teprve začínáte, doporučujeme vám seznámit se s tím, [Jak získat tenanta Azure AD](quickstart-create-new-tenant.md) , než budete pokračovat v těchto příkladech.

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

## <a name="omit-the-basic-claims-from-tokens"></a>Vynechat základní deklarace identity z tokenů

V tomto příkladu vytvoříte zásadu, která odebere [základní sadu deklarací identity](reference-claims-mapping-policy-type.md#claim-sets) z tokenů vydaných pro propojené objekty služby.

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

## <a name="include-the-employeeid-and-tenantcountry-as-claims-in-tokens"></a>Zahrnout ČísloZaměstnance a TenantCountry jako deklarace identity v tokenech

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

## <a name="use-a-claims-transformation-in-tokens"></a>Použití transformace deklarací identity v tokenech

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

## <a name="security-considerations"></a>Důležité informace o zabezpečení

Aplikace, které přijímají tokeny, spoléhají na skutečnost, že hodnoty deklarací jsou směrodatně vydány službou Azure AD a nelze je úmyslně poškodit. Pokud však upravíte obsah tokenu prostřednictvím zásad mapování deklarací, tyto předpoklady již nemusí být správné. Aplikace musí explicitně potvrdit, že se tokeny změnily autorem zásady mapování deklarací, aby se chránily před zásadami mapování deklarací, které vytvořily škodlivé objekty actor. To lze provést následujícími způsoby:

- Konfigurace vlastního podpisového klíče
- Aktualizujte manifest aplikace, aby přijímal mapované deklarace identity.
 
Bez této akce vrátí Azure AD [ `AADSTS50146` kód chyby](reference-aadsts-error-codes.md#aadsts-error-codes).

### <a name="custom-signing-key"></a>Vlastní podpisový klíč

Chcete-li přidat vlastní podpisový klíč do objektu instančního objektu, můžete použít rutinu Azure PowerShell [`New-AzureADApplicationKeyCredential`](/powerShell/module/Azuread/New-AzureADApplicationKeyCredential) k vytvoření přihlašovacích údajů klíče certifikátu pro objekt aplikace.

Aplikace, které mají povolené mapování deklarací, musí ověřit své podpisové klíče tokenu připojením `appid={client_id}` ke svým [žádostem o metadata OpenID Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document). Níže je uvedený formát dokumentu metadat OpenID Connect, který byste měli použít:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="update-the-application-manifest"></a>Aktualizovat manifest aplikace

Alternativně můžete nastavit `acceptMappedClaims` vlastnost na hodnotu `true` v [manifestu aplikace](reference-app-manifest.md). Jak je uvedeno v [apiApplication typu prostředku](/graph/api/resources/apiapplication#properties), umožňuje aplikaci použít mapování deklarací bez zadání vlastního podpisového klíče.

To vyžaduje, aby posluchač požadované domény používal ověřený název domény vašeho tenanta Azure AD, což znamená, že byste měli nastavit `Application ID URI` (reprezentované `identifierUris` v manifestu aplikace) například na `https://contoso.com/my-api` nebo (jednoduše pomocí výchozího názvu tenanta) `https://contoso.onmicrosoft.com/my-api` .

Pokud nepoužíváte ověřenou doménu, Azure AD vrátí `AADSTS501461` kód chyby se zprávou *"AcceptMappedClaims je podporován pouze pro cílovou skupinu tokenů, která odpovídá identifikátoru GUID aplikace nebo cílové skupině v rámci ověřených domén klienta. Buď změňte identifikátor prostředku, nebo použijte podpisový klíč specifický pro aplikaci. "*

## <a name="next-steps"></a>Další kroky

- Další informace najdete v článku referenční informace o [typu zásad pro mapování deklarací](reference-claims-mapping-policy-type.md) .
- Informace o tom, jak přizpůsobit deklarace identity vydané v tokenu SAML prostřednictvím Azure Portal, najdete v tématu [How to: Customize Claims vydaných v tokenu SAML pro podnikové aplikace.](active-directory-saml-claims-customization.md)
- Další informace o atributech rozšíření najdete v tématu [použití atributů rozšíření schématu adresáře v deklaracích identity](active-directory-schema-extensions.md).
