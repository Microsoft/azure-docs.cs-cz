---
title: Sami otestovat klienta aplikace Azure SaaS kód předběžně ověřit | Dokumentace Microsoftu
description: Postup vytvoření samoobslužné testovacího klienta pro předběžné ověření aplikace Azure SaaS.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/24/2018
ms.author: pbutlerm
ms.openlocfilehash: 4a43d9463f00462b3a68cc82b1896a1392a72037
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54082711"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-saas-application"></a>Vytvořit sami otestovat klienta aplikace Azure SaaS kód předběžně ověřit

Použijte tento článek jako vodítko pro vytvoření klienta služby, která využívá rozhraní API samočinný test. Rozhraní API samočinný test můžete předběžně ověřit virtuální počítač (VM) k zajištění, že jsou že splněné požadavky pro publikování na nejnovější Azure Marketplace. Tato služba klienta umožňuje testovat virtuální počítač předtím, než odešlete vaši nabídku Microsoftu k certifikaci.

## <a name="development-and-testing-overview"></a>Vývoj a testování – přehled

Jako součást procesu samočinný test vytvoříte místního klienta, který se připojuje k webu Azure Marketplace se ověřit virtuální počítač spuštěný ve vašem předplatném Azure. Virtuální počítač může být spuštěn operační systém Windows nebo Linux.

Místní klient spustí skript, který se ověřuje pomocí rozhraní API pro automatický test, informace o připojení odesílá a přijímá výsledky testů.

Jsou základní kroky pro vytvoření klienta samočinný test:

1. Zvolte tenanta Azure Active Directory (AD) pro vaši aplikaci.
2. Registrace aplikace klienta.
3. Vytvoří token pro klientskou aplikaci služby Azure AD.
4. Předejte token samočinný test rozhraní API.

Po vytvoření klienta, takže ji můžete otestovat proti vašemu virtuálnímu počítači.

### <a name="self-test-client-authorization"></a>Samočinný test ověřování klienta

Následující diagram znázorňuje, jak funguje ověřování pro volání mezi službami pomocí přihlašovacích údajů klienta (sdílený tajný klíč nebo certifikát).

![Proces autorizace klienta](./media/stclient-dev-process.png)

## <a name="the-self-test-client-api"></a>Klient samočinný test rozhraní API

Rozhraní API má následující strukturu.

```
Uri:             https://isvapp.azurewebsites.net/selftest
Method:          Post
Request Header:  Content-Type: “application/json”
Authorization:   “Bearer xxxx-xxxx-xxxx-xxxxx”
Request body:    The Request body parameters should use the following JSON format:
                 {
                   "DNSName":"XXXX.westus.cloudapp.azure.com",
                   "User":"XXX",
                   "Password":"XXX@1234567",
                   "OS":"XXX",
                   "PortNo":"22",
                   "CompanyName":"ABCD",
                 }

```

Následující tabulka popisuje pole rozhraní API.


|      Pole         |    Popis    |
|  ---------------   |  ---------------  |
|        Autorizace            |         Řetězec "Nosiče xxxx-xxxx-xxxx-xxxxx" obsahuje token klienta Azure Active Directory (AD), které lze vytvořit pomocí prostředí PowerShell.          |
|          DNSName          |         Název DNS virtuálního počítače, který se chystáte otestovat.          |
|          Uživatel          |          Uživatelské jméno pro připojení k a přihlášení k virtuálnímu počítači.         |
|           Heslo         |         Heslo pro připojení k a přihlášení k virtuálnímu počítači.          |
|          Operační systém          |         Operační systém virtuálního počítače, "Linux" nebo "Windows".          |
|         PortNo           |         Číslo otevřený port pro připojení k virtuálnímu počítači. Číslo portu je obvykle "22" pro systémy Linux a "5986" pro Windows.          |

## <a name="consuming-the-api"></a>Používání rozhraní API

Můžete využívat rozhraní API sami otestovat pomocí prostředí PowerShell nebo nástroj cURL.

### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>Použití Powershellu k využívání rozhraní API v operačním systému Linux

Pro volání rozhraní API v prostředí PowerShell, postupujte podle těchto kroků:

1. Použití `Invoke-WebRequest` příkaz pro volání rozhraní API.
2. Je metoda Post a typu obsahu je JSON, jak je znázorněno na následujícím snímku příkladu a na obrazovce kódu.
3. Vytvoření parametry textu ve formátu JSON.

Následující příklad kódu ukazuje volání rozhraní API prostředí PowerShell.

```powershell
$accesstoken = “Get token for your Client AAD App”
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Linux"
    "PortNo" = "22"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers; 
$Content = $res | ConvertFrom-Json
```

Následující snímek obrazovky ukazuje příklad pro volání rozhraní API v prostředí PowerShell.

![Volání rozhraní API pomocí prostředí PowerShell pro operační systém Linux](./media/stclient-call-api-ps-linuxvm.png)

Použijeme předchozí příklad, můžete načíst ve formátu JSON a analyzovat ji, abyste získali následující podrobnosti:

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++) 
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

Následující obrazovce zachycení, který ukazuje `$res.Content`, poskytuje podrobnosti o výsledkům testů ve formátu JSON.

![JSON výsledky z volání Powershellu pro Linux](./media/stclient-pslinux-rescontent-json.png)

Následující snímek obrazovky ukazuje příklad výsledků testů JSON v online prohlížeč JSON.
(například [kód Beautify](https://codebeautify.org/jsonviewer), [prohlížeč JSON](https://jsonformatter.org/json-viewer))

![JSON výsledky z volání Powershellu na virtuálním počítači s Linuxem](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>Využívají rozhraní API v operačním systému Windows pomocí Powershellu

Pro volání rozhraní API v prostředí PowerShell, postupujte podle těchto kroků:

1. Použití `Invoke-WebRequest` příkaz pro volání rozhraní API.
2. Je metoda Post a typu obsahu je JSON, jak je znázorněno na následujícím snímku příkladu a na obrazovce kódu.
3. Vytvoření parametry textu ve formátu JSON.

Následující příklad kódu ukazuje volání rozhraní API prostředí PowerShell.

```powershell
$accesstoken = “Get token for your Client AAD App”
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Windows"
    "PortNo" = "5986"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

Následující snímek obrazovky ukazuje příklad pro volání rozhraní API v prostředí PowerShell.

![Volání rozhraní API pomocí prostředí PowerShell pro Windows virtuální počítač](./media/stclient-call-api-ps-windowsvm.png)

Použijeme předchozí příklad, můžete načíst ve formátu JSON a analyzovat ji, abyste získali následující podrobnosti:

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++) 
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}

```

Následující obrazovce zachycení, který ukazuje `$res.Content`, poskytuje podrobnosti o výsledkům testů ve formátu JSON.

![Volání výsledky JSON z prostředí PowerShell pro Windows](./media/stclient-pswindows-rescontent-json.png)

Následující snímek obrazovky ukazuje výsledky testů v online prohlížeč JSON.
(například [kód Beautify](https://codebeautify.org/jsonviewer), [prohlížeč JSON](https://jsonformatter.org/json-viewer))

![JSON výsledky z volání virtuálního počítače Windows Powershellu](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>Používáme nástroj cURL k využívání rozhraní API v operačním systému Linux

Volání rozhraní API pomocí cURL postupujte podle těchto kroků:

1. Pomocí příkazu curl k volání rozhraní API.
2. Je metoda Post a typu obsahu je JSON, jak je znázorněno v následujícím fragmentu kódu.

```
CURL POST -H "Content-Type:application/json" 
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX”
https://isvapp.azurewebsites.net/selftest 
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'

```
Následující obrazovka ukazuje příklad pomocí příkazu curl k volání rozhraní API.

![Volání rozhraní API pomocí příkazu curl](./media/stclient-consume-api-curl.png)

Následující snímek obrazovky ukazuje výsledky JSON z volání curl.

![JSON výsledky z volání curl](./media/stclient-consume-api-curl-json.png)

## <a name="choose-the-azure-ad-tenant-for-the-app"></a>Zvolte tenanta Azure AD pro aplikaci

Pomocí následujících kroků vyberte tenanta Azure AD, kde chcete vytvořit aplikaci.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. V horní nabídce vyberte svůj účet a v seznamu adresářů zvolte tenanta Active Directory, ve které chcete zaregistrovat aplikaci. Nebo vyberte **adresář a předplatné** ikonu zjistíte, globální filtr předplatných. Následující snímek obrazovky ukazuje příklad tohoto filtru.

   ![Vyberte filtr předplatných](./media/stclient-subscription-filter.png)

3. V levém navigačním panelu vyberte **všechny služby** a pak vyberte **Azure Active Directory**.

   V následujících krocích, může být nutné název tenanta (nebo název adresáře) nebo ID tenanta (nebo ID adresáře).

   **Pokud chcete získat informace o tenantovi:**
  
   V **Přehled služby Azure Active Directory**, vyhledejte "Properties" a pak vyberte **vlastnosti**. Jako příklad použijeme následující snímek obrazovky:

   - **Název** – název tenanta nebo název adresáře
   - **ID adresáře** – ID tenanta nebo ID adresáře nebo pomocí posuvníku najít vlastnosti.

   ![Stránka vlastností služby Azure Active Directory](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>Registrace aplikace klienta

Následující postup použijte k registraci klientské aplikace.

1. V levém navigačním panelu vyberte **všechny služby** a pak vyberte **registrace aplikací**.
2. V části **registrace aplikací**vyberte **+ registrace nové aplikace**.
3. V části **vytvořit**, zadejte informace požadované pro následující pole:

   - **Název** – zadejte popisný název aplikace. Například "SelfTestClient".
   - **Typ aplikace** – vyberte **webové aplikace nebo rozhraní API**
   - **Adresa URL přihlašování** – typ "https://isvapp.azurewebsites.net/selftest"

4. Vyberte **Vytvořit**.
5. V části **registrace aplikací** nebo **registrovaná aplikace**, kopie **ID aplikace**.

   ![Získání ID aplikace](./media/stclient-app-id.png)

6. Na panelu nástrojů registrovaná aplikace vyberte **nastavení**.
7. Vyberte **požadovaná oprávnění** konfigurace oprávnění pro vaši aplikaci.
8. V části **požadovaná oprávnění**vyberte **+ přidat**.
9. V části **přístup přes rozhraní API přidat**, vyberte **vyberte rozhraní API**.
10. V části **vyberte rozhraní API**, typ "Model nasazení Azure classic Windows" pro hledání rozhraní API.
11. Ve výsledcích hledání vyberte **modelu nasazení classic Windows Azure** a potom klikněte na tlačítko **vyberte**.

    ![Konfigurace víceklientské aplikace](./media/stclient-select-api.png)

12. V části **přístup přes rozhraní API přidat**, vyberte **vyberte oprávnění**.
13. Vyberte **přístup "Windows Azure Service Management API"**.

    ![Povolit přístup přes rozhraní API pro aplikaci](./media/stclient-enable-api-access.png)

14. Klikněte na **Vybrat**.
15. Vyberte **Done** (Hotovo).
16. V části **nastavení**vyberte **vlastnosti**.
17. V části **vlastnosti**, přejděte dolů k položce **víceklientských**. Vyberte **Ano**.  

    ![Konfigurace víceklientské aplikace](./media/stclient-yes-multitenant.png)

18. Vyberte **Uložit**.
19. V části **nastavení**vyberte **klíče**.
20. Vytvoření tajného klíče výběrem klíč **popis** textového pole. Vyplňte následující pole:

    - Zadejte název klíče. Například "selftestclient"
    - Na **EXPIRES** rozevíracího seznamu vyberte možnost "za 1 rok.".
    - Vyberte **Uložit** ke generování klíče.
    - V části **hodnota**, klíč si zkopírujte.

     >[!Important]
     >Nebudete moci zobrazit hodnota klíče po ukončení formuláře klíče.

    ![Hodnota klíče formuláře](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>Vytvořit token pro klientské aplikace

Vytvoření a získání tokenu pomocí rozhraní REST API OAuth, můžete použít některý z následujících programů:

- Postman
- cURL v Linuxu
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>Vytvoření a získání tokenu pomocí nástroje Postman

 Auth0 požádat o tokeny pro žádné z autorizovaných aplikacích, proveďte operaci POST do [ https://login.microsoftonline.com/common/oauth2/token ](https://login.microsoftonline.com/common/oauth2/token) koncový bod s datovou částí v následujícím formátu:

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```
Předejte tyto parametry v textu požadavku:

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

V hlavičce žádosti, předejte následující parametry:

```
Content-Type: application/x-www-form-urlencoded
```

Následující snímek obrazovky ukazuje příklad získá token, a pomocí nástroje Postman.

![Získání tokenu pomocí nástroje Postman](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>Vytvoření a získání tokenu v Linuxu pomocí cURL

Auth0 požádat o tokeny pro žádné z autorizovaných aplikacích, proveďte operaci POST do [ https://login.microsoftonline.com/common/oauth2/token ](https://login.microsoftonline.com/common/oauth2/token) koncový bod s datovou částí v následujícím formátu:

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

Následující snímek obrazovky ukazuje příklad pomocí příkazu curl k získání tokenu.

![Získání tokenu pomocí příkazu curl](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>Vytvoření a získání tokenu pomocí jazyka C&#35;

Auth0 požádat o tokeny pro žádné z autorizovaných aplikacích, proveďte operaci POST do [ https://soamtenant.auth0.com/oauth/token ](https://soamtenant.auth0.com/oauth/token) koncový bod s datovou částí v následujícím formátu:

```
string clientId = "Your Application Id";
string clientSecret = "Your Application Secret";
string audience = "https://management.core.windows.net";
string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, "https://login.microsoftonline.com/common/oauth2/token");
string grantType = "client_credentials";
var client = new RestClient(authority);
var request = new RestRequest(Method.POST);
       request.AddHeader("content-type", "application/x-www-form- urlencoded");

string requestBody = "grant_type=" + grantType + "&" + "client_id=" +   clientId + "&" + "client_secret=" + clientSecret + "&" + "resource=" +  audience;

request.AddParameter("application/x-www-form-urlencoded", requestBody,  ParameterType.RequestBody);

IRestResponse response = client.Execute(request);
var content = response.Content;
var token = JObject.Parse(content)["access_token"];
```

### <a name="to-create-and-get-a-token-using-powershell"></a>Vytvoření a získání tokenu pomocí Powershellu

Auth0 požádat o tokeny pro žádné z autorizovaných aplikacích, proveďte operaci POST do [ https://soamtenant.auth0.com/oauth/token ](https://soamtenant.auth0.com/oauth/token) koncový bod s datovou částí v následujícím formátu:

```
$clientId = "Application Id of AD Client APP";
$clientSecret = "Secret Key of AD Client APP “
$audience = "https://management.core.windows.net";
$authority = "https://login.microsoftonline.com/common/oauth2/token"
$grantType = "client_credentials";

$requestBody = "grant_type=" + $grantType + "&" + "client_id=" +  $clientId   + "&" + "client_secret=" + $clientSecret + "&" + "resource=" + $audience;

$headers = New-Object  "System.Collections.Generic.Dictionary[[String],[String]]"
             $headers.Add("ContentType", "application/x-www-form-urlencoded")
resp = Invoke-WebRequest -Method Post -Uri $authority -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $requestBody

$token = $resp.Content | ConvertFrom-Json
$token.AccessToken

```

## <a name="pass-the-client-app-token-to-the-api"></a>Předat token aplikace klienta pro rozhraní API

Předejte token samočinný test rozhraní API v hlavičce autorizace pomocí následujícího kódu:

```
$redirectUri = ‘https://isvapp.azurewebsites.net/selftest’
$accesstoken = ‘place your token here’

$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body =
@{
      'DNSName'="XXXX.cloudapp.azure.com";
      'User'="XXX";
      'Password'="XXXX@12345";
      'OS'="Linux";
      'PortNo'="22"
  } | ConvertTo-Json

$result=Invoke-WebRequest -Method Post -Uri $redirectUri -Headers $headers -ContentType 'application/json' -Body $Body
$result
echo 'Test Results:'
$result.Content

```

## <a name="test-your-self-test-client"></a>Testování klienta samočinný test

Chcete-li otestovat klienta, postupujte takto:

1. Nasazení virtuálního počítače, které chcete testovat.
2. Volání rozhraní API sami otestovat pomocí tokenu aplikace vašeho klienta pro ověřování.
3. Získání výsledků testů ve formátu JSON.

### <a name="test-result-examples"></a>Příklady výsledků testu

Následující fragmenty kódu zobrazit výsledky testů ve formátu JSON.

**Výsledky testů pro virtuální počítač s Windows:**

```
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Microsoft Windows Server 2016 Datacenter",
  "OSVersion": "10.0.14393",
  "CreatedDate": "06/01/2018 07:48:04",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "Tes tID": "1.1.4",
      "TestCaseName": "OS Architecture",
      "Description": "Azure supports 64bit Operating System Only.",
      "Result": "Passed",
      "ActualValue": "64 Bit",
      "Re quiredValue": "OS Should be 64 bit"
    },
    {
      "TestID": "1.1.5",
      "TestCaseName": "User account dependency",
      "Description": "Application execution should not have de pendency on administrator account.",
      "Result": "Passed",
      "ActualValue": "isv",
      "RequiredValue": "Logged in user should not be an administrator"
    },
    {
      "TestID": " 1.1.6",
      "TestCaseName": "Failover Cluster",
      "Description": "Windows Server Failover Clustering feature is not yet supported, Application should not have dependency on this feature.",
      "Result": "Passed",
      "ActualValue": "Disabled",
      "RequiredValue": "Failover Clustering feature is disabled"
    },
```

**Výsledky testů pro virtuální počítač s Linuxem:**

```
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Ubuntu  16.04",
  "OSVersion": "16.04",
  "CreatedDate": "06/01/2018 07:04:24",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "TestID": "48",
      "TestCaseName": "Bash Hi story",
      "Description": "Bash history files should be cleared before creating the VM image.",
      "Result": "Passed",
      "ActualValue": "No file Exist",
      "RequiredVal ue": "1024"
    },
    {
      "TestID": "39",
      "TestCaseName": "Linux Agent Version",
      "Description": "Azure Linux Agent 2.2.10 and above should be installed. ",
      "Result": "Pas sed",
      "ActualValue": "2.2.20",
      "RequiredValue": "2.2.10"
    },
    {
      "TestID": "40",
      "TestCaseName": "Required Kernel Parameters",
      "Description": "Verifies the following  kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300",
      "Result": "Passed",
      "ActualValue": "Matched Parameter: console=ttyS0,earlypri ntk=ttyS0,rootdelay=300",
      "RequiredValue": "console=ttyS0#earlyprintk=ttyS0#rootdelay=300"
    },
```

## <a name="next-steps"></a>Další postup

Jakmile úspěšně otestujete aplikace Azure SaaS, můžete [publikování nabídky](./cpp-publish-offer.md)