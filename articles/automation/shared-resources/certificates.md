---
title: Správa certifikátu v Azure Automation
description: Certifikáty jsou bezpečně uložené v Azure Automation tak, aby Runbooky a konfigurace DSC k nim měli přístup, aby je bylo možné ověřit u prostředků Azure a jiných výrobců. V tomto článku se dozvíte o podrobnostech certifikátů a o tom, jak s nimi pracovat v textovém i grafickém vytváření.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69bc1a0333365e15452c6d3b253266d37d99b608
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732829"
---
# <a name="manage-certificates-in-azure-automation"></a>Správa certifikátů v Azure Automation

Certifikáty jsou bezpečně uložené v Azure Automation tak, aby k nim měly přístup Runbooky nebo konfigurace DSC pomocí aktivity [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) pro prostředky Azure Resource Manager. Zabezpečené úložiště certifikátů umožňuje vytvářet Runbooky a konfigurace DSC, které používají certifikáty pro ověřování, nebo je přidat do prostředků Azure nebo třetích stran.

Zabezpečené prostředky v Azure Automation zahrnují přihlašovací údaje, certifikáty, připojení a šifrované proměnné. Tyto prostředky jsou zašifrované a uložené v Azure Automation pomocí jedinečného klíče, který se generuje pro každý účet Automation. Tento klíč je uložený v systému spravovaném Key Vault. Před uložením zabezpečeného prostředku se klíč načte z Key Vault a pak se použije k zašifrování assetu. Tento proces je spravovaný pomocí Azure Automation.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](../automation-update-azure-modules.md).

## <a name="az-powershell-cmdlets"></a>AZ PowerShell rutiny

V případě AZ se k vytváření a správě prostředků přihlašovacích údajů automatizace pomocí Windows PowerShellu použijí rutiny uvedené v následující tabulce. Dodávají se jako součást [modulu AZ. Automation](/powershell/azure/overview), který je k dispozici pro použití v sadách Automation a konfiguracích DSC.

|Rutina |Popis|
| --- | ---|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Nahraje certifikát služby pro zadanou cloudovou službu.|
|[Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Načte informace o certifikátu, který se má použít v sadě Runbook nebo konfiguraci DSC. Certifikát můžete načíst jenom pomocí `Get-AutomationCertificate` aktivity.|
|[New-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Vytvoří nový certifikát v Azure Automation.|
|[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Odebere certifikát z Azure Automation.|
|[Set-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|Nastaví vlastnosti pro existující certifikát, včetně nahrání souboru certifikátu a nastavení hesla pro soubor **. pfx** .|

## <a name="activities"></a>Aktivity

Aktivity v následující tabulce se používají pro přístup k certifikátům v rámci sady Runbook a konfigurací DSC.

| Aktivity | Popis |
|:---|:---|
|`Get-AutomationCertificate`|Načte certifikát, který se má použít v sadě Runbook nebo konfiguraci DSC. Vrátí objekt [System. Security. Cryptography. X509Certificates. X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) .|

> [!NOTE] 
> Nepoužívejte proměnné v `Name` parametru `Get-AutomationCertificate` v sadě Runbook nebo konfiguraci DSC. Použití proměnných v tomto parametru ztěžuje zjišťování závislostí mezi sadami Runbook a konfigurací DSC a proměnných automatizace v době návrhu.

## <a name="python-2-functions"></a>Python 2 – funkce

Funkce v následující tabulce se používá pro přístup k certifikátům v sadě Runbook Python 2.

| Funkce | Popis |
|:---|:---|
| `automationassets.get_automation_certificate` | Načte informace o prostředku certifikátu. |

> [!NOTE]
> Aby bylo možné získat `automationassets` přístup k funkcím assetu, musíte importovat modul na začátek Runbooku sady Python.

## <a name="creating-a-new-certificate"></a>Vytváří se nový certifikát.

Když vytváříte nový certifikát, nahrajete soubor. cer nebo. pfx do Azure Automation. Pokud označíte certifikát jako exportovatelný, můžete ho přenést z úložiště certifikátů Azure Automation. Pokud ji nelze exportovat, lze ji použít pouze k podepisování v rámci sady Runbook nebo konfigurace DSC. Azure Automation vyžaduje, aby měl certifikát poskytovatele **Microsoft Enhanced kryptografických služeb RSA a AES**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Vytvořit nový certifikát s Azure Portal

1. V účtu Automation kliknutím na **assets (prostředky** ) otevřete stránku assety.
2. Vyberte **certifikáty** a otevřete stránku certifikáty.
3. V horní části stránky klikněte na **Přidat certifikát** .
4. Do pole **název** zadejte název certifikátu.
5. Pokud chcete vyhledat soubor **. cer** nebo **. pfx** , klikněte v části **nahrát soubor certifikátu**na **Vybrat soubor** . Pokud vyberete soubor **. pfx**, zadejte heslo a určete, jestli se dá exportovat.
6. Kliknutím na **vytvořit** uložte nový prostředek certifikátu.

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
$PfxCertPath = '<PFX cert path'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>'
$AutomationAccountName = '<automation account name>'
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
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="using-a-certificate"></a>Použití certifikátu

Chcete-li použít certifikát, použijte `Get-AutomationCertificate` aktivitu. Nemůžete použít rutinu [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) , protože vrací informace o prostředku certifikátu, ale ne samotný certifikát.

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

Přidejte `Get-AutomationCertificate` aktivitu do grafického Runbooku tak, že kliknete pravým tlačítkem na certifikát v podokně Knihovna a vyberete **Přidat na plátno**.

![Přidat certifikát na plátno](../media/certificates/automation-certificate-add-to-canvas.png)

Následující obrázek ukazuje příklad použití certifikátu v grafickém Runbooku. To je stejné jako v předchozím příkladu, který ukazuje, jak přidat certifikát do cloudové služby z textové sady Runbook.

![Příklad grafického vytváření](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Příklad Pythonu 2

Následující příklad ukazuje, jak získat přístup k certifikátům v sadách Runbook Python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Další kroky

- Další informace o práci s odkazy na řízení logického toku aktivit provedených sadou Runbook naleznete [v tématu odkazy v grafickém vytváření](../automation-graphical-authoring-intro.md#links-and-workflow). 
