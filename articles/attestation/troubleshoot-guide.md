---
title: Průvodce odstraňováním potíží Azure Attestation
description: Průvodce odstraňováním potíží s běžně zjištěnými problémy
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 46e3521a54f6bfdfbfb25634a09b8c8e0cfdcac0
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342989"
---
# <a name="microsoft-azure-attestation-troubleshooting-guide"></a>Průvodce odstraňováním potíží s Microsoft Azure ověření identity

Zpracování chyb v Azure Attestation se implementuje podle [pokynů pro REST API Microsoftu](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#7102-error-condition-responses). Chybová odpověď vrácená rozhraními API Azure Attestation obsahuje stavový kód HTTP a páry název/hodnota s názvy "Code" a "Message". Hodnota "Code" je čitelná pro člověka a je indikátorem typu chyby. Hodnota "Message" zamýšlí pomoci uživateli a poskytuje podrobnosti o chybě.

Pokud se problém nevyřeší v tomto článku, můžete také odeslat žádost o podporu Azure na [stránce podpory Azure](https://azure.microsoft.com/support/options/).

Níže jsou uvedeny některé příklady chyb vrácených ověřením identity Azure:

## <a name="1-http401--unauthorized-exception"></a>1. HTTP – 401: neoprávněná výjimka

### <a name="http-status-code"></a>Stavový kód HTTP
401

**Kód chyby** Přístupu

**Příklady scénářů**
  - Chyba ověření identity, pokud uživatel není přiřazený k roli čtenáře ověření identity
  - Nejde spravovat zásady ověření identity, protože uživatel není přiřazený k příslušným rolím.
  - Nejde spravovat podepsané zásady ověření identity, protože uživatel není přiřazený k příslušným rolím.

Uživatel s rolí čtenáře, který se pokouší upravit zásadu ověření identity v PowerShellu 

  ```powershell
  Set-AzAttestationPolicy : Operation returned HTTP Status Code 401
At line:1 char:1
+ Set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
  ```

**Postup při řešení potíží**

Aby bylo možné zobrazit zásady ověření identity/podepsané zásady, uživatel Azure AD vyžaduje oprávnění pro akce:
- Microsoft. Attestation/attestationProviders/ověření/čtení

  Toto oprávnění může být přiřazeno uživateli služby AD prostřednictvím role, jako je "vlastník" (oprávnění zástupných znaků) nebo "čtenář" (oprávnění zástupných znaků) nebo "čtenář" Reader "(specifická oprávnění pro Azure Attestation).

Aby bylo možné přidat nebo odstranit podepsané zásady nebo nakonfigurovat zásady, vyžaduje uživatel Azure AD následující oprávnění pro akce:
- Microsoft. Attestation/attestationProviders/Attestation/Write
- Microsoft. Attestation/attestationProviders/Attestation/DELETE

  Tato oprávnění je možné přiřadit uživateli služby AD prostřednictvím role, jako je "vlastník" (oprávnění zástupných znaků), přispěvatele (oprávnění zástupných znaků) nebo "Přispěvatela ověření" (specifická oprávnění pouze pro Azure Attestation).

Zákazníci se můžou rozhodnout použít výchozího poskytovatele pro ověření identity nebo vytvořit vlastní poskytovatele s vlastními zásadami. Aby bylo možné odesílat požadavky ověření identity vlastním zprostředkovatelům ověření identity, je pro uživatele vyžadována role "vlastník" (oprávnění zástupných znaků) nebo čtenář (oprávnění ke kterému se zástupným znakem) nebo "čtenář". Výchozí poskytovatelé jsou přístupná pro všechny uživatele Azure AD.

Pokud chcete ověřit role v PowerShellu, spusťte níže:

a. Spusťte PowerShell a přihlaste se k Azure pomocí rutiny Connect-AzAccount.

b. Ověření nastavení přiřazení role RBAC


  ```powershell
  $c = Get-AzContext
  Get-AzRoleAssignment -ResourceGroupName $attestationResourceGroup -ResourceName $attestationProvider -ResourceType Microsoft.Attestation/attestationProviders -SignInName $c.Account.Id
  ```

  Měli byste vidět přibližně toto:

  ```
  RoleAssignmentId   :/subscriptions/subscriptionId/providers/Microsoft.Authorization/roleAssignments/roleAssignmentId
  
  Scope              : /subscriptions/subscriptionId
  
  DisplayName        : displayName
  
  SignInName         : signInName
  
  RoleDefinitionName : Reader
  
  RoleDefinitionId   : roleDefinitionId
  
  ObjectId           : objectid
  
  ObjectType         : User
  
  CanDelegate        : False
 
  ```

c. Pokud v seznamu nenajdete odpovídající přiřazení role, postupujte podle pokynů v [tomto tématu](/azure/role-based-access-control/role-assignments-powershell) .

## <a name="2-http--400-errors"></a>2. chyby HTTP – 400

### <a name="http-status-code"></a>Stavový kód HTTP
400

Existují různé důvody, proč může požadavek vracet 400. Níže jsou uvedeny některé příklady chyb vrácených rozhraními API Azure Attestation:

### <a name="21-attestation-failure-due-to-policy-evaluation-errors"></a>2.1. Selhání ověřování kvůli chybám vyhodnocení zásad

Zásady ověření identity zahrnují autorizační pravidla a pravidla vystavování. Legitimace enklávy se vyhodnocuje na základě autorizačních pravidel. Pravidla vystavování definují deklarace identity, které se mají zahrnout do tokenu ověření identity. Pokud deklarace identity v enklávy legitimace nevyhovují autorizačním pravidlům, budou volání ověření zásad vracet chybu vyhodnocení zásad. 

**Kód chyby** PolicyEvaluationError

**Příklady scénářů** Pokud deklarace identity v enklávy uvozovkách neodpovídají autorizačním pravidlům zásad ověřování identity

```
Native operation failed with 65518: G:\Az\security\Attestation\src\AttestationServices\Instance\NativePolicyWrapper\NativePolicyEngine.cpp(168)\(null)!00007FF801762308: (caller: 00007FF80143DCC8) Exception(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Policy Engine Exception: A Deny claim was issued, authorization failed.]

G:\Az\security\Attestation\src\AttestationServices\Instance\Enclave\api.cpp(840)\(null)!00007FF801739FF3: (caller: 00007FF801232801) LogHr(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Unhandled Enclave Exception: "Policy Evaluation Error has occurred"]

```

**Postup řešení potíží** Uživatelé můžou vyhodnotit legitimaci enklávy proti zásadám ověření SGX před konfigurací stejného.

Odešlete požadavek na ověření identity rozhraní API zadáním textu zásad v parametru "draftPolicyForAttestation". Rozhraní AttestSgxEnclave API bude používat tento dokument zásad během volání ověřování identity. to se dá použít k otestování zásad ověření identity dřív, než se spotřebují. Token ověření identity vygenerovaný v případě, že toto pole je k dispozici, bude nezabezpečený.

Viz [Příklady zásad ověřování identity](/azure/attestation/policy-examples) .

### <a name="22-attestation-failure-due-to-invalid-input"></a>2.2. Selhání ověření identity z důvodu neplatného vstupu

**Kód chyby** InvalidParameter

**Příklady scénářů** Selhání ověření identity SGX z důvodu neplatného vstupu. Níže jsou uvedeny některé příklady chybových zpráv:
- Zadaná nabídka byla neplatná z důvodu chyby v dokumentaci nabídky. 
- Zadaná nabídka byla neplatná, protože zařízení, na kterém byla nabídka vygenerována, nesplňuje požadavky na směrný plán Azure.
- Zadaná citace byla neplatná, protože TCBInfo nebo QEID, které poskytla PCK Cache Service, byly neplatné.

**Postup při řešení potíží**

Microsoft Azure Attestation podporuje ověřování SGXch nabídek generovaných sadou Intel SDK a Open enklávy SDK.

V tématu [ukázky kódu](/samples/browse/?expanded=azure&terms=attestation) pro provádění ověření pomocí Open enklávy SDK/Intel SDK

### <a name="23-invalid-certificate-chain-error-while-uploading-policypolicy-signer"></a>2.3. Neplatná chyba řetězení certifikátů při nahrávání zásady/podepsané zásady

**Kód chyby** InvalidParameter

**Příklady scénářů** Nakonfigurujte podepsané zásady nebo přidejte nebo odstraňte podpis zásady, který je podepsaný neplatným řetězem certifikátů (například pokud rozšíření základní omezení kořenového certifikátu není nastavené na typ subjektu = CA).

```
Native operation failed with 65529: C:\source\src\AttestationServices\Instance\SgxPal\sgxcert.cpp(1074)\(null)!00007FFA285CDAED: (caller: 00007FFA285C36E8) Exception(0) 83FFFFF9 The requested item is not found    Msg:[Unable to find issuer certificate CN=attestationsigningcert]
C:\source\src\AttestationServices\Instance\Enclave\api.cpp(618)\(null)!00007FFA286DCBF8: (caller: 00007FFA285860D3) LogHr(0) 83FFFFF9 The requested item is not found    Msg:[Unhandled Enclave Exception: "The requested item is not found"]
At line:1 char:1
+ Set-AzAttestationPolicy -Name "testpolicy1" -ResourceGroupName "BugBa ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy

```

**Postup řešení potíží** Kořenový certifikát musí být označen jako vydaný certifikační autoritou (základní omezení X. 509), jinak nebude považován za platný certifikát. 

Zajistěte, aby bylo rozšíření základní omezení kořenového certifikátu nastavené tak, aby označovalo, že typ subjektu = CA.

V opačném případě se řetěz certifikátů považuje za neplatný.

Viz příklady [přihlášení k zásadám](/azure/attestation/policy-signer-examples) a [zásady](/azure/attestation/policy-examples) 

### <a name="24-adddelete-policy-signer-failure"></a>2.4. Přidat nebo odstranit chybu přihlášení k zásadám

**Kód chyby** InvalidOperation

**Příklady scénářů**

Když uživatel nahraje JWS bez deklarace identity Maa-policyCertificate

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\Enclave\enclave.cpp(2213)\(null)!: (caller: ) Exception(0) 83FF004A Bad
message    Msg:[Could not find "maa-policyCertificate" claim in policy token]
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner

```

Když uživatel neodešle certifikát ve formátu JWS

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\JsonWebToken\jsonwebtoken.cpp(375)\(null)!: (caller: ) LogHr(0) 83FF004A
Bad message    Msg:[RETURN_IF_TRUE('(firstPeriod == std::string::npos)') failed with 0x4a: Malformed JWT: Could not
find first period in the token.]
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message
Msg:[THROW_IF_ERROR('DecomposeJsonWebSignature(&policyJws, encodedJoseHeader, encodedJwsBody, jwsSignature)') failed
with 0x4a: 'Bad message']
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) Exception(0) 83FF004A Bad message
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner
```

**Postup řešení potíží** Chcete-li přidat nebo odstranit nový certifikát podepsané zásady, použijte RFC7519 JSON Web Token (JWT) s deklarací identity s názvem "x-MS-policyCertificate". Hodnota deklarace identity je RFC7517 webový klíč JSON, který obsahuje certifikát, který chcete přidat. Token JWT musí být podepsán pomocí privátního klíče jakéhokoli platného certifikátu podepsaného zásad přidruženým k poskytovateli. Viz [Příklady odhlašování zásad](/azure/attestation/policy-signer-examples).

### <a name="25-attestation-policy-configuration-failure"></a>2.5. Chyba konfigurace zásad ověření identity

**Kód chyby** PolicyParsingError

**Příklady scénářů** Zásady byly zadány s nesprávnou syntaxí (například chybějící středník)/Valid JWT.

```
Native operation failed with 65526: ..\NativePolicyWrapper\NativePolicyEngine.cpp(31)\(null)!: (caller: ) Exception(0) 83FFFFF6 Invalid policy was specified    Msg:[Policy Parser Exception Thrown: Offending
symbol: '['
Line: '2', Column: '1'
Failure message: 'mismatched input '[' expecting ';''
Failing rule: 'policy > versionInfo']
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FFFFF6 Invalid policy was specified    Msg:[Unhandled Enclave Exception: "Invalid policy was specified"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Kód chyby** InvalidOperation

**Příklady scénářů** Byl zadán neplatný obsah (například pokud je požadováno podepisování zásad), nahrajte zásady nebo zásady bez znaménka.

```
Native operation failed with 74: ..\Shared\base64url.h(226)\(null)!: (caller: ) Exception(0) 83FF004A Bad message    Msg:[Unknown base64 character: 41 (')')]
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad message"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Postup řešení potíží** Ujistěte se, že zásady v textovém formátu mají kódování UTF-8.

Pokud je požadováno podepisování zásad, je nutné nakonfigurovat zásady ověření identity pouze ve formátu RFC7519 JSON Web Token (JWT). Pokud není vyžadováno podepisování zásad, můžete nakonfigurovat zásady ve formátu text nebo JWT.

Chcete-li nakonfigurovat zásadu ve formátu JWT, použijte token JWT s deklarací identity s názvem "AttestationPolicy". Hodnota deklarace identity je Base64URL kódovaná verze textu zásady. Pokud je poskytovatel ověření identity nakonfigurovaný pomocí certifikátů podepisování zásad, musí se token JWT podepsat pomocí privátního klíče jakéhokoli platného certifikátu podepsaného zásad, který je přidružený k poskytovateli. 

Pokud chcete nakonfigurovat zásadu v textovém formátu, určete přímo text zásady.

V PowerShellu zadejte PolicyFormat jako JWT ke konfiguraci zásad ve formátu JWT. Výchozí formát zásad je text.

Viz [Příklady zásad](/azure/attestation/policy-examples) ověřování identity a [vytváření zásad ověření identity](/azure/attestation/author-sign-policy) . 

## <a name="3-azattestation-installation-issues-in-powershell"></a>3. AZ. Attestation instalace ověření identity v PowerShellu

V PowerShellu nejde nainstalovat moduly AZ nebo AZ. Attestation.

### <a name="error"></a>Chyba

Upozornění: nepovedlo se přeložit zdroj balíčku https://www.powershellgallery.com/api/v2 PackageManagement\Install-Package: pro zadaná kritéria hledání a název modulu se nenašla žádná shoda.

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

Galerie prostředí PowerShell má zastaralé verze protokolu TLS (Transport Layer Security) 1,0 a 1,1. 

Doporučuje se protokol TLS 1,2 nebo novější verze. 

Pokud chcete pokračovat v interakci s Galerie prostředí PowerShell, spusťte před příkazy Install-Module tento příkaz.

**[NET. Třída ServicePointManager]:: Tato SecurityProtocol = [NET. SecurityProtocolType]:: Tls12**

## <a name="4-policy-accessconfiguration-issues-in-powershell"></a>4. problémy s přístupem k zásadám a jejich konfigurací v PowerShellu

Uživatel přiřazený k příslušným rolím Ale problémy s autorizací při správě zásad ověřování pomocí PowerShellu.

### <a name="error"></a>Chyba
Klient s ID objektu nemá <object Id>  autorizaci k provedení akce Microsoft. Authorization/RoleAssignments/Write přes obor ' koupená/ <subscriptionId> resourcegroups/secure_enclave_poc/Providers/Microsoft.Authorization/RoleAssignments/ <role assignmentId> ' nebo je obor neplatný. Pokud byl přístup nedávno udělen, aktualizujte prosím svoje přihlašovací údaje.

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

Minimální verze AZ modules Required for support Operations Attestation jsou následující: 

 **AZ 4.5.0** 
 
 **AZ. Accounts 1.9.2**
 
 **AZ. Attestation 0.1.8** 

Spuštěním následujícího příkazu ověřte nainstalovanou verzi všech modulů AZ. 

```powershell
Get-InstalledModule 
```

Pokud se verze neshodují s minimálním požadavkem, spusťte příkazy Update-Module

například-Update-Module-Name AZ. Attestation

