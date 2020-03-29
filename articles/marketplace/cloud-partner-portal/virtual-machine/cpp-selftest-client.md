---
title: Samočinný test klienta pro předběžnou ověření virtuálního počítače | Azure Marketplace
description: Jak vytvořit klienta s vlastním testem pro předběžnou validaci image virtuálního počítače pro Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: dsindona
ms.openlocfilehash: fb568400cb60f108303909353bfa703e98ab6157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286417"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-virtual-machine-image"></a>Vytvoření klienta s vlastním testem pro předběžnou ověření image virtuálního počítače Azure

Tento článek použijte jako vodítko pro vytvoření klientské služby, která spotřebovává rozhraní API pro vlastní testování. Rozhraní API pro autotestování můžete použít k předběžnému ověření virtuálního počítače(VM), abyste zajistili, že splňuje nejnovější požadavky na publikování na Azure Marketplace. Tato klientská služba umožňuje otestovat virtuální počítač před odesláním nabídky pro certifikaci Microsoftu.

## <a name="development-and-testing-overview"></a>Přehled vývoje a testování

Jako součást procesu autotestu vytvoříte místního klienta, který se připojí k Azure Marketplace a ověří virtuální počítač spuštěný ve vašem předplatném Azure. Virtuální počítač může být spuštěn operační systém Windows nebo Linux.

Místní klient spustí skript, který se ověřuje pomocí rozhraní API pro samočinné testování, odesílá informace o připojení a přijímá výsledky testů.

Kroky vysoké úrovně pro vytvoření klienta s vlastním testem jsou:

1. Zvolte klienta Služby Azure Active Directory (AD) pro vaši aplikaci.
2. Zaregistrujte klientskou aplikaci.
3. Vytvořte token pro klientskou aplikaci Azure AD.
4. Předajte token do rozhraní API pro vlastní testování.

Po vytvoření klienta, můžete otestovat proti virtuálnímu počítači.

### <a name="self-test-client-authorization"></a>Autorizace klienta s vlastním testem

Následující diagram znázorňuje, jak funguje autorizace pro službu pro volání pomocí pověření klienta (sdílený tajný klíč nebo certifikát.)

![Proces autorizace klienta](./media/stclient-dev-process.png)

## <a name="the-self-test-client-api"></a>Rozhraní API klienta pro vlastní testování

Rozhraní API pro autotestování obsahuje jeden koncový bod, který podporuje pouze metodu POST.  Má následující strukturu.

```
Uri:             https://isvapp.azurewebsites.net/selftest-vm
Method:          Post
Request Header:  Content-Type: "application/json"
Authorization:   "Bearer xxxx-xxxx-xxxx-xxxxx"
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
|  Autorizace     |  Řetězec "Bearer xxxx-xxxx-xxxx-xxxxx" obsahuje klientský token služby Azure Active Directory (AD), který lze vytvořit pomocí prostředí PowerShell.          |
|  Název DNS           |  Název DNS virtuálního_ virtuálního_    |
|  Uživatel              |  Uživatelské jméno pro přihlášení k virtuálnímu virtuálnímu mísu         |
|  Heslo          |  Heslo pro přihlášení k virtuálnímu počítače          |
|  Operační systém                |  Operační systém virtuálního provozu: buď `Linux` nebo`Windows`          |
|  PortNo            |  Otevřete číslo portu pro připojení k virtuálnímu virtuálnímu provozu. Číslo portu je `22` obvykle `5986` pro Linux a pro Windows.          |
|  |  |

## <a name="consuming-the-api"></a>Využívání rozhraní API

Rozhraní API pro samočinné testování můžete využívat pomocí prostředí PowerShell nebo cURL.

### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>Použití Prostředí PowerShell ke spotřebovávat rozhraní API v operačním systému Linux

Pokud chcete volat rozhraní API v PowerShellu, postupujte takto:

1. Pomocí `Invoke-WebRequest` příkazu volejte rozhraní API.
2. Metoda je Post a typ obsahu je JSON, jak je znázorněno v následujícím příkladu kódu a snímání obrazovky.
3. Zadejte parametry těla ve formátu JSON.

Následující příklad kódu ukazuje volání PowerShellu do rozhraní API.

```powershell
$accesstoken = "Get token for your Client AAD App"
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
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" -Headers $headers;
$Content = $res | ConvertFrom-Json
```
Následující snímek obrazovky ukazuje příklad pro volání rozhraní API v Prostředí PowerShell.

![Rozhraní API pro volání pomocí PowerShellu pro operační systém Linux](./media/stclient-call-api-ps-linuxvm.png)

Pomocí předchozího příkladu můžete načíst JSON a analyzovat jej získat následující podrobnosti:

```powershell
$testresult = ConvertFrom-Json -InputObject (ConvertFrom-Json -InputObject $res)

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

Následující snímek obrazovky, `$res.Content`který ukazuje , poskytuje podrobnosti o výsledcích testů ve formátu JSON.

![Výsledky JSON z volání PowerShellu na Linux](./media/stclient-pslinux-rescontent-json.png)

Následující snímek obrazovky ukazuje příklad výsledků testů JSON zobrazených v online prohlížeči JSON (například [Code Beautify](https://codebeautify.org/jsonviewer) nebo [JSON Viewer](https://jsonformatter.org/json-viewer)).

![Výsledky JSON z volání PowerShellu pro virtuální počítač s Linuxem](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>Použití prostředí PowerShell ke spotřebovávat rozhraní API v systému Windows OS

Pokud chcete volat rozhraní API v PowerShellu, postupujte takto:

1. Pomocí `Invoke-WebRequest` příkazu volejte rozhraní API.
2. Metoda je Post a typ obsahu je JSON, jak je znázorněno v následujícím příkladu kódu a snímání obrazovky.
3. Vytvořte parametry Body ve formátu JSON.

Následující příklad kódu ukazuje volání PowerShellu do rozhraní API.

```powershell
$accesstoken = "Get token for your Client AAD App"
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
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" -Headers $headers;
$Content = $res | ConvertFrom-Json
```

Následující snímek obrazovky ukazuje příklad pro volání rozhraní API v Prostředí PowerShell.

![Rozhraní API pro volání pomocí Prostředí PowerShell pro virtuální virtuální mísu s Windows](./media/stclient-call-api-ps-windowsvm.png)

Pomocí předchozího příkladu můžete načíst JSON a analyzovat jej získat následující podrobnosti:

```powershell
$testresult = ConvertFrom-Json -InputObject (ConvertFrom-Json -InputObject $res)

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

Následující snímek obrazovky, `$res.Content`který ukazuje , poskytuje podrobnosti o výsledcích testů ve formátu JSON.

![Výsledky json z volání prostředí PowerShell do Systému Windows](./media/stclient-pswindows-rescontent-json.png)

Následující snímek obrazovky zobrazuje výsledky testů zobrazené v online prohlížeči JSON.
(například [Kód Zkrášlit](https://codebeautify.org/jsonviewer), [Prohlížeč JSON](https://jsonformatter.org/json-viewer))

![JSON výsledky z volání prostředí PowerShell na virtuální počítač se systémem Windows](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>Použití cURL ke spotřebovávat API na operačním systému Linux

Chcete-li volat rozhraní API s cURL, postupujte takto:

1. Pomocí příkazu curl volejte rozhraní API.
2. Metoda je Post a typ obsahu je JSON, jak je znázorněno v následujícím fragmentu kódu.

```
CURL POST -H "Content-Type:application/json"
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX"
https://isvapp.azurewebsites.net/selftest-vm
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'
```

Na následující obrazovce je uveden příklad použití curl volání rozhraní API.

![Volání rozhraní API pomocí příkazu curl](./media/stclient-consume-api-curl.png)

Následující snímek obrazovky ukazuje výsledky JSON z volání curl.

![JSON výsledky z curl volání](./media/stclient-consume-api-curl-json.png)


## <a name="choose-the-azure-ad-tenant-for-the-app"></a>Výběr klienta Azure AD pro aplikaci

Pomocí následujících kroků zvolte klienta Azure AD, kde chcete vytvořit aplikaci.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Na horním řádku nabídek vyberte svůj účet a v seznamu Adresář vyberte klienta služby Active Directory, ve kterém chcete aplikaci zaregistrovat. Nebo vyberte ikonu **Adresář + Odběr** a zobcvujte filtr globálníodběr. Následující snímek obrazovky ukazuje příklad tohoto filtru.

   ![Výběr filtru předplatného](./media/stclient-subscription-filter.png)

3. Na levém navigačním panelu vyberte **Všechny služby** a pak vyberte **Azure Active Directory**.

   V následujících krocích můžete potřebovat název klienta (nebo název adresáře) nebo ID klienta (nebo ID adresáře).

   **Jak získat informace o tenantovi:**

   V **přehledu služby Azure Active Directory**vyhledejte výraz Vlastnosti a vyberte **vlastnosti**. Jako příklad používáte následující snímek obrazovky:

   - **Název** – název klienta nebo název adresáře
   - **ID adresáře** – ID klienta nebo ID adresáře nebo použití posuvníku k vyhledání vlastností.

   ![Stránka vlastností služby Azure Active Directory](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>Registrace klientské aplikace

Pomocí následujících kroků zaregistrujte klientskou aplikaci.

1. Na levém navigačním panelu vyberte **Všechny služby** a pak vyberte **Registrace aplikací**.
2. V části **Registrace aplikací**vyberte + Registrace **nové aplikace**.
3. V části **Vytvořit**zadejte informace požadované pro následující pole:

   - **Název** – Zadejte popisný název aplikace. Například "SelfTestClient".
   - **Typ aplikace** – výběr **webové aplikace/rozhraní API**
   - **Přihlašovací adresa URL** – zadejte "https:\//isvapp.azurewebsites.net/selftest-vm"

4. Vyberte **Vytvořit**.
5. V části **Registrace aplikací** nebo **Registrovaná aplikace**zkopírujte **ID aplikace**.

   ![Získání ID aplikace](./media/stclient-app-id.png)

6. Na registrovaném panelu nástrojů aplikace vyberte **Nastavení**.
7. Vyberte **Požadovaná oprávnění** ke konfiguraci oprávnění pro vaši aplikaci.
8. V části **Požadovaná oprávnění**vyberte **+ Přidat**.
9. V části **Přidat přístup k rozhraní API**vyberte Vybrat rozhraní **API**.
10. V **části Vyberte rozhraní API**zadejte "Windows Azure classic deployment model" pro hledání rozhraní API.
11. Ve výsledcích hledání vyberte **klasický model nasazení Windows Azure** a klikněte na **Vybrat**.

    ![Konfigurace víceklientského klienta pro aplikaci](./media/stclient-select-api.png)

12. V části **Přidat přístup k rozhraní API**vyberte možnost Vybrat **oprávnění**.
13. Vyberte **Access "Rozhraní API pro správu služeb Windows Azure "**.

    ![Povolení přístupu k rozhraní API pro aplikaci](./media/stclient-enable-api-access.png)

14. Klepněte na **tlačítko Vybrat**.
15. Vyberte **Done** (Hotovo).
16. V části **Nastavení** vyberte **Vlastnosti**.
17. V části **Vlastnosti**přejděte dolů na **Víceklientské**. Vyberte **ano**.

    ![Konfigurace víceklientského klienta pro aplikaci](./media/stclient-yes-multitenant.png)

18. Vyberte **Uložit**.
19. V části **Nastavení**vyberte **Klávesy**.
20. Vytvořte tajný klíč výběrem textového pole **Popis** klíče. Nakonfigurujte následující pole:

    - Zadejte název klíče. Například "selftestclient"
    - V rozevíracím seznamu **EXPIRES** vyberte možnost "Za 1 rok".
    - Chcete-li klíč vygenerovat, vyberte **uložit.**
    - V části **HODNOTA**zkopírujte klíč.

      >[!Important]
      >Hodnotu klíče po ukončení formuláře **Klíče** neuvidíte.

    ![Formulář hodnoty klíče](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>Vytvoření tokenu pro klientskou aplikaci

Pomocí některého z následujících programů můžete vytvořit a získat token pomocí rozhraní OAuth REST API:

- Postman
- cURL v Linuxu
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>Vytvoření a získání tokenu pomocí Pošťáka

 Chcete-li požádat Auth0 o žetony pro některou [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) z vašich autorizovaných aplikací, proveďte operaci POST ke koncovému bodu s datovou částí v následujícím formátu:

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```

Předat následující parametry v textu požadavku:

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

V hlavičce požadavku předavte následující parametry:

```
Content-Type: application/x-www-form-urlencoded
```

Následující snímek obrazovky ukazuje příklad použití Postman získat token.

![Získat žeton s Pošťákem](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>Vytvoření a získání tokenu pomocí cURL v Linuxu

Chcete-li požádat Auth0 o žetony pro některou [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) z vašich autorizovaných aplikací, proveďte operaci POST ke koncovému bodu s datovou částí v následujícím formátu:

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

Následující snímek obrazovky ukazuje příklad použití příkazu curl k získání tokenu.

![Získat token s příkazem curl](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>Vytvoření a získání tokenu pomocí c&#35;

Chcete-li požádat Auth0 o žetony pro některou z\/vašich autorizovaných aplikací, proveďte operaci POST na https: /soamtenant.auth0.com/oauth/token koncovém bodu s datovou částí v následujícím formátu:

```csharp
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

### <a name="to-create-and-get-a-token-using-powershell"></a>Vytvoření a získání tokenu pomocí PowerShellu

Chcete-li požádat Auth0 o žetony pro některou z\/vašich autorizovaných aplikací, proveďte operaci POST na https: /soamtenant.auth0.com/oauth/token koncovém bodu s datovou částí v následujícím formátu:

```powershell
$clientId = "Application Id of AD Client APP";
$clientSecret = "Secret Key of AD Client APP "
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

## <a name="pass-the-client-app-token-to-the-api"></a>Předání tokenu klientské aplikace do rozhraní API

Předavte token rozhraní API pro samočinné testování pomocí následujícího kódu v hlavičce autorizace:

```powershell
$redirectUri = 'https://isvapp.azurewebsites.net/selftest-vm'
$accesstoken = 'place your token here'

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
Write-Output 'Test Results:'
$result.Content
```

## <a name="test-your-self-test-client"></a>Otestujte svého klienta s vlastním testem

Chcete-li otestovat klienta, postupujte takto:

1. Nasaďte virtuální ho virtuálního mísu, který chcete otestovat.
2. Volání rozhraní API pro samočinné testování pomocí tokenu klientské aplikace pro autorizaci.
3. Získejte výsledky testů ve formátu JSON.

### <a name="test-result-examples"></a>Příklady výsledků testu

Následující úryvky zobrazují výsledky testů ve formátu JSON.

**Výsledky testů pro virtuální mísu windows:**

```json
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

```json
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

## <a name="next-steps"></a>Další kroky

Po úspěšném otestování virtuálního počítače Azure můžete [nabídku publikovat](./cpp-publish-offer.md).
