---
title: Správa certifikátů v Azure Automation
description: Tento článek obsahuje informace o tom, jak pracovat s certifikáty pro přístup pomocí runbooků a konfigurací DSC.
services: automation
ms.subservice: shared-capabilities
ms.date: 09/10/2020
ms.topic: conceptual
ms.openlocfilehash: 1c79b7c239c41e8d195230423b17fa3c5a7f51a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91825812"
---
# <a name="manage-certificates-in-azure-automation"></a>Správa certifikátů v Azure Automation

Azure Automation ukládá certifikáty bezpečně pro přístup pomocí runbooků a konfigurací DSC pomocí rutiny [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) pro prostředky Azure Resource Manager. Zabezpečené úložiště certifikátů umožňuje vytvářet Runbooky a konfigurace DSC, které používají certifikáty k ověřování, nebo je přidat do prostředků Azure nebo třetích stran.

>[!NOTE]
>Zabezpečené prostředky v Azure Automation zahrnují přihlašovací údaje, certifikáty, připojení a šifrované proměnné. Tyto prostředky jsou šifrované a uložené v automatizaci pomocí jedinečného klíče, který se generuje pro každý účet Automation. Služba Automation ukládá klíč do služby Key Vault spravované systémem. Před uložením zabezpečeného prostředku Automation načte klíč z Key Vault a pak ho použije k zašifrování assetu. 

## <a name="powershell-cmdlets-to-access-certificates"></a>Rutiny PowerShellu pro přístup k certifikátům

Rutiny v následující tabulce vytvářejí a spravují certifikáty služby Automation pomocí prostředí PowerShell. Dodávají se jako součást [AZ moduls](modules.md#az-modules).

|Rutina |Popis|
| --- | ---|
|[Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate)|Načte informace o certifikátu, který se má použít v sadě Runbook nebo konfiguraci DSC. Samotný certifikát můžete načíst jenom pomocí interní `Get-AutomationCertificate` rutiny.|
|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)|Vytvoří nový certifikát ve službě Automation.|
|[Remove-AzAutomationCertificate](/powershell/module/Az.Automation/Remove-AzAutomationCertificate)|Odebere certifikát z automatizace.|
|[Set-AzAutomationCertificate](/powershell/module/Az.Automation/Set-AzAutomationCertificate)|Nastaví vlastnosti pro existující certifikát, včetně nahrání souboru certifikátu a nastavení hesla pro soubor **. pfx** .|

Pomocí rutiny [Add-AzureCertificate](/powershell/module/servicemanagement/azure.service/add-azurecertificate) lze také nahrát certifikát služby pro zadanou cloudovou službu.

## <a name="internal-cmdlets-to-access-certificates"></a>Interní rutiny pro přístup k certifikátům

Interní rutina v následující tabulce se používá pro přístup k certifikátům ve vašich sadách Runbook. Tato rutina se dodává s globálním modulem `Orchestrator.AssetManagement.Cmdlets` . Další informace najdete v tématu [interní rutiny](modules.md#internal-cmdlets).

| Interní rutina | Description |
|:---|:---|
|`Get-AutomationCertificate`|Načte certifikát, který se má použít v sadě Runbook nebo konfiguraci DSC. Vrátí objekt [System. Security. Cryptography. X509Certificates. X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) .|

> [!NOTE] 
> Nepoužívejte proměnné v `Name` parametru `Get-AutomationCertificate` v sadě Runbook nebo konfiguraci DSC. Tyto proměnné mohou zkomplikovat zjišťování závislostí mezi sadami Runbook a konfigurací DSC a automatizačními proměnnými v době návrhu.

## <a name="python-2-functions-to-access-certificates"></a>Funkce Python 2 pro přístup k certifikátům

Použijte funkci v následující tabulce pro přístup k certifikátům v sadě Runbook Python 2.

| Funkce | Description |
|:---|:---|
| `automationassets.get_automation_certificate` | Načte informace o prostředku certifikátu. |

> [!NOTE]
> `automationassets`Aby bylo možné získat přístup k funkcím assetu, musíte importovat modul na začátek Runbooku sady Python.

## <a name="create-a-new-certificate"></a>Vytvořit nový certifikát

Když vytváříte nový certifikát, nahrajete soubor. cer nebo. pfx pro automatizaci. Pokud označíte certifikát jako exportovatelný, můžete ho přenést z úložiště certifikátů služby Automation. Pokud ji nelze exportovat, lze ji použít pouze k podepisování v rámci sady Runbook nebo konfigurace DSC. Automatizace vyžaduje, aby měl certifikát od poskytovatele **Microsoft Enhanced kryptografických služeb RSA a AES**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Vytvořit nový certifikát s Azure Portal

1. Z účtu Automation v levém podokně vyberte **certifikáty** pod **sdíleným prostředkem**.
1. Na stránce **certifikáty** vyberte **Přidat certifikát**.
1. Do pole **název** zadejte název certifikátu.
1. Chcete-li vyhledat soubor **. cer** nebo **. pfx** , v části **nahrát soubor certifikátu**zvolte **možnost vybrat soubor**. Pokud vyberete soubor **. pfx** , zadejte heslo a určete, jestli se dá exportovat.
1. Vyberte **vytvořit** a uložte nový prostředek certifikátu.

### <a name="create-a-new-certificate-with-powershell"></a>Vytvoření nového certifikátu pomocí PowerShellu

Následující příklad ukazuje, jak vytvořit nový certifikát automatizace a označit ho jako exportovatelný. Tento příklad importuje existující soubor **. pfx** .

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Vytvoření nového certifikátu s Správce prostředků šablonou

Následující příklad ukazuje, jak nasadit certifikát do účtu Automation pomocí šablony Správce prostředků prostřednictvím PowerShellu:

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path and filename>'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>' #A name of your choosing
$ResourceGroupName = '<resource group name>' #The one that holds your automation account
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName $ResourceGroupName -TemplateFile .\template.json
```

## <a name="get-a-certificate"></a>Získat certifikát

K načtení certifikátu použijte interní `Get-AutomationCertificate` rutinu. Nemůžete použít rutinu [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) , protože vrací informace o prostředku certifikátu, ale ne samotný certifikát.

### <a name="textual-runbook-example"></a>Příklad textového Runbooku

Následující příklad ukazuje, jak přidat certifikát do cloudové služby v sadě Runbook. V této ukázce je heslo načteno z šifrované proměnné automatizace.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-example"></a>Příklad grafického Runbooku

Přidejte aktivitu pro interní `Get-AutomationCertificate` rutinu do grafického Runbooku tak, že kliknete pravým tlačítkem na certifikát v podokně Knihovna a vyberete **Přidat na plátno**.

![Snímek obrazovky s přidáním certifikátu na plátno](../media/certificates/automation-certificate-add-to-canvas.png)

Následující obrázek ukazuje příklad použití certifikátu v grafickém Runbooku.

![Snímek obrazovky s příkladem vytváření grafického obsahu](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Příklad Pythonu 2

Následující příklad ukazuje, jak získat přístup k certifikátům v sadách Python 2 Runbooky.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Další kroky

* Další informace o rutinách používaných pro přístup k certifikátům najdete v tématu [Správa modulů v Azure Automation](modules.md).
* Obecné informace o sadách Runbook naleznete [v tématu Spuštění Runbooku v Azure Automation](../automation-runbook-execution.md).
* Podrobnosti o konfiguracích DSC najdete v tématu [Přehled konfigurace stavu Azure Automation](../automation-dsc-overview.md).
