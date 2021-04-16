---
title: Povolení spravované identity pro účet Azure Automation (Preview)
description: Tento článek popisuje, jak nastavit spravovanou identitu pro účty Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: 93c55c21bf740f2851cac1926bc673cebcd914b0
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514797"
---
# <a name="enable-a-managed-identity-for-your-azure-automation-account-preview"></a>Povolení spravované identity pro účet Azure Automation (Preview)

V tomto tématu se dozvíte, jak vytvořit spravovanou identitu pro účet Azure Automation a jak ji použít pro přístup k dalším prostředkům. Další informace o tom, jak spravovaná identita funguje s Azure Automation, najdete v tématu [spravované identity](automation-security-overview.md#managed-identities-preview).

## <a name="prerequisites"></a>Požadavky

- Účet a předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/). Spravovaná identita i cílové prostředky Azure, které vaše sada Runbook spravuje pomocí této identity, musí být ve stejném předplatném Azure.

- Nejnovější verze modulů účtů Azure Automation. V současné době se jedná o 1.6.0. (Podrobnosti o této verzi najdete v tématu [AZ. Automation 1.6.0](https://www.powershellgallery.com/packages/Az.Automation/1.6.0) .)

- Prostředek Azure, ke kterému chcete získat přístup ze sady Automation Runbook. Tento prostředek musí mít definovanou roli pro spravovanou identitu, což pomáhá službě Automation Runbook ověřit přístup k prostředku. Chcete-li přidat role, musíte být vlastníkem prostředku v odpovídajícím tenantovi služby Azure AD.

- Pokud chcete spustit hybridní úlohy pomocí spravované identity, aktualizujte Hybrid Runbook Worker na nejnovější verzi. Minimální požadované verze jsou:

   - Hybrid Runbook Worker pro Windows: verze 7.3.1125.0
   - Hybrid Runbook Worker pro Linux: verze 1.7.4.0

## <a name="enable-system-assigned-identity"></a>Povolit identitu přiřazenou systémem

>[!IMPORTANT]
>Nová identita na úrovni účtu Automation přepíše všechny předchozí identity přiřazené systémem na úrovni virtuálního počítače (které jsou popsány v tématu [použití ověřování Runbooku se spravovanými identitami](/automation-hrw-run-runbooks#runbook-auth-managed-identities)). Pokud spouštíte hybridní úlohy na virtuálních počítačích Azure, které k přístupu k prostředkům Runbooku používají identitu přiřazenou systémem virtuálního počítače, pak se pro hybridní úlohy použije identita účtu Automation. To znamená, že stávající spuštění úlohy může být ovlivněno, pokud jste použili funkci CMK (Customer Managed Keys) vašeho účtu Automation.<br/><br/>Pokud chcete i nadále používat spravovanou identitu virtuálního počítače, neměli byste povolit identitu na úrovni účtu Automation. Pokud jste ho už povolili, můžete zakázat spravovanou identitu účtu Automation. Viz [deaktivace spravované identity účtu Azure Automation](https://docs.microsoft.com/azure/automation/disable-managed-identity-for-automation).

Nastavení identit přiřazených systémem pro Azure Automation lze provést jedním ze dvou způsobů. Můžete buď použít Azure Portal, nebo Azure REST API.

>[!NOTE]
>Uživatelsky přiřazené identity ještě nejsou podporované.

### <a name="enable-system-assigned-identity-in-azure-portal"></a>Povolit identitu přiřazenou systémem v Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Přejděte do svého účtu Automation a v části **Nastavení účtu** vyberte **Identita** .

1. Nastavte možnost **systém přiřazeno** na **zapnuto** a stiskněte **Uložit**. Až budete vyzváni k potvrzení, vyberte **Ano**.

:::image type="content" source="media/managed-identity/managed-identity-on.png" alt-text="Povolení identity přiřazené systémem v Azure Portal.":::

Váš účet Automation teď může používat identitu přiřazenou systémem, která je zaregistrovaná ve službě Azure Active Directory (Azure AD) a která je reprezentovaná ID objektu.

:::image type="content" source="media/managed-identity/managed-identity-object-id.png" alt-text="ID spravovaného objektu identity":::

### <a name="enable-system-assigned-identity-through-the-rest-api"></a>Povolte identitu přiřazenou systémem prostřednictvím REST API

Spravovanou identitu přiřazenou systémem můžete nakonfigurovat na účet Automation pomocí následujícího REST API volání.

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Text požadavku
```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 .
 .
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
.
.
}
```

|Property (JSON) | Hodnota | Popis|
|----------|-----------|------------|
| PrincipalID | \<principal-ID\> | Globálně jedinečný identifikátor (GUID) instančního objektu služby pro spravovanou identitu, která představuje váš účet Automation v tenantovi Azure AD. Tento identifikátor GUID se někdy zobrazuje jako "ID objektu" nebo objectID. |
| tenantid | \<Azure-AD-tenant-ID\> | Globálně jedinečný identifikátor (GUID), který představuje tenanta Azure AD, ve kterém je teď účet Automation členem. V tenantovi služby Azure AD má instanční objekt stejný název jako účet Automation. |

## <a name="give-identity-access-to-azure-resources-by-obtaining-a-token"></a>Získání přístupu k prostředkům Azure pomocí získání tokenu

Účet Automation může pomocí spravované identity získat tokeny pro přístup k jiným prostředkům chráněným službou Azure AD, jako je například Azure Key Vault. Tyto tokeny nepředstavuje žádného konkrétního uživatele aplikace. Místo toho představují aplikaci, která přistupuje k prostředku. V takovém případě token představuje například účet Automation.

Než budete moci použít spravovanou identitu přiřazenou systémem pro ověřování, nastavte přístup k této identitě v prostředku Azure, ve kterém chcete identitu používat. K dokončení této úlohy přiřaďte příslušné roli k této identitě v cílovém prostředku Azure.

V tomto příkladu se používá Azure PowerShell k zobrazení způsobu přiřazení role přispěvatele v předplatném k cílovému prostředku Azure. Role přispěvatele se používá jako příklad a ve vašem případě může nebo nemusí být vyžadována.

```powershell
New-AzRoleAssignment -ObjectId <automation-Identity-object-id> -Scope "/subscriptions/<subscription-id>" -RoleDefinitionName "Contributor"
```

## <a name="authenticate-access-with-managed-identity"></a>Ověření přístupu pomocí spravované identity

Po povolení spravované identity účtu Automation a udělení přístupu identit k cílovému prostředku můžete tuto identitu zadat v sadách Runbook pro prostředky, které podporují spravovanou identitu. Pro podporu identit použijte rutinu AZ rutiny `Connect-AzAccount` . Viz téma [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) v referenční příručce k prostředí PowerShell.

```powershell
Connect-AzAccount -Identity
```

>[!NOTE]
>Pokud vaše organizace stále používá zastaralé rutiny AzureRM, můžete použít `Connect-AzureRMAccount -Identity` .

## <a name="generate-an-access-token-without-using-azure-cmdlets"></a>Generování přístupového tokenu bez použití rutin Azure

U koncových bodů HTTP se ujistěte, že je následující.
- Hlavička metadat musí být přítomna a měla by být nastavena na hodnotu "true".
- Prostředek musí být spolu s požadavkem předán jako parametr dotazu pro požadavek GET a jako data formuláře pro požadavek POST.
- Záhlaví X-IDENTITY-HEADER by mělo být nastavené na hodnotu proměnné prostředí IDENTITY_HEADER pro procesy Hybrid Runbook Worker. 
- Typ obsahu pro požadavek post musí být application/x-www-form-urlencoded. 

### <a name="sample-get-request"></a>Ukázka žádosti o získání

```powershell
$resource= "?resource=https://management.azure.com/" 
$url = $env:IDENTITY_ENDPOINT + $resource 
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$accessToken = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
Write-Output $accessToken.access_token
```

### <a name="sample-post-request"></a>Ukázka žádosti POST
```powershell
$url = $env:IDENTITY_ENDPOINT  
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$headers.Add("Metadata", "True") 
$body = @{resource='https://management.azure.com/' } 
$accessToken = Invoke-RestMethod $url -Method 'POST' -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $body 
Write-Output $accessToken.access_token
```

## <a name="sample-runbooks-using-managed-identity"></a>Ukázkové Runbooky využívající spravovanou identitu

### <a name="sample-runbook-to-access-a-sql-database-without-using-azure-cmdlets"></a>Ukázkový Runbook pro přístup k databázi SQL bez použití rutin Azure

```powershell
$queryParameter = "?resource=https://database.windows.net/" 
$url = $env:IDENTITY_ENDPOINT + $queryParameter
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$content =[System.Text.Encoding]::Default.GetString((Invoke-WebRequest -UseBasicParsing -Uri $url -Method 'GET' -Headers $Headers).RawContentStream.ToArray()) | ConvertFrom-Json 
$Token = $content.access_token 
echo "The managed identities for Azure resources access token is $Token" 
$SQLServerName = "<ServerName>"    # Azure SQL logical server name  
$DatabaseName = "<DBname>"     # Azure SQL database name 
Write-Host "Create SQL connection string" 
$conn = New-Object System.Data.SqlClient.SQLConnection  
$conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30" 
$conn.AccessToken = $Token 
Write-host "Connect to database and execute SQL script" 
$conn.Open()  
$ddlstmt = "CREATE TABLE Person( PersonId INT IDENTITY PRIMARY KEY, FirstName NVARCHAR(128) NOT NULL)" 
Write-host " " 
Write-host "SQL DDL command" 
$ddlstmt 
$command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn) 
Write-host "results" 
$command.ExecuteNonQuery() 
$conn.Close()
```

### <a name="sample-runbook-to-access-a-key-vault-using-azure-cmdlets"></a>Ukázkový Runbook pro přístup k trezoru klíčů pomocí rutin Azure

```powershell
Write-Output "Connecting to azure via  Connect-AzAccount -Identity" 
Connect-AzAccount -Identity 
Write-Output "Successfully connected with Automation account's Managed Identity" 
Write-Output "Trying to fetch value from key vault using MI. Make sure you have given correct access to Managed Identity" 
$secret = Get-AzKeyVaultSecret -VaultName '<KVname>' -Name '<KeyName>' 

$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue) 
try { 
  $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr) 
    Write-Output $secretValueText 
} finally { 
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr) 
}
```

### <a name="sample-python-runbook-to-get-a-token"></a>Ukázkový Runbook v Pythonu pro získání tokenu
 
```python
#!/usr/bin/env python3 
import os 
import requests  
# printing environment variables 
endPoint = os.getenv('IDENTITY_ENDPOINT')+"?resource=https://management.azure.com/" 
identityHeader = os.getenv('IDENTITY_HEADER') 
payload={} 
headers = { 
  'X-IDENTITY-HEADER': identityHeader,
  'Metadata': 'True' 
} 
response = requests.request("GET", endPoint, headers=headers, data=payload) 
print(response.text) 
```

## <a name="next-steps"></a>Další kroky

- Pokud potřebujete zakázat spravovanou identitu, přečtěte si téma [zakázání identity spravovaného účtu Azure Automation (Preview)](disable-managed-identity-for-automation.md).

- Přehled zabezpečení účtu Azure Automation najdete v tématu [Přehled ověřování účtu Automation](automation-security-overview.md).