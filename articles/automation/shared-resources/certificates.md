---
title: Prostředky certifikátů v Azure Automation
description: Certifikáty jsou bezpečně v Azure Automation tak, aby k nim měly přístup Runbooky nebo konfigurace DSC k ověřování pomocí prostředků Azure a jiných výrobců.  V tomto článku se dozvíte o podrobnostech certifikátů a o tom, jak s nimi pracovat v textovém i grafickém vytváření.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a66f73e028594cf90f1fa1765910a3df3adbad1a
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849475"
---
# <a name="certificate-assets-in-azure-automation"></a>Prostředky certifikátů v Azure Automation

Certifikáty jsou bezpečně uložené v Azure Automation tak, aby k nim měly přístup Runbooky nebo konfigurace DSC pomocí aktivity **Get-AzureRmAutomationCertificate** pro prostředky Azure Resource Manager. Tato funkce umožňuje vytvářet Runbooky a konfigurace DSC, které používají certifikáty k ověřování nebo které je přidávají do prostředků Azure nebo třetích stran.

>[!NOTE]
>Zabezpečené prostředky v Azure Automation zahrnují přihlašovací údaje, certifikáty, připojení a šifrované proměnné. Tyto prostředky jsou zašifrované a uložené v Azure Automation pomocí jedinečného klíče, který se generuje pro každý účet Automation. Tento klíč je uložený v Key Vault spravovaném systémem. Před uložením zabezpečeného prostředku se klíč načte z Key Vault a pak se použije k zašifrování assetu. Tento proces je spravovaný pomocí Azure Automation.

## <a name="azurerm-powershell-cmdlets"></a>Rutiny PowerShellu pro AzureRM

Rutiny v následující tabulce pro AzureRM slouží k vytváření a správě assetů přihlašovacích údajů automatizace pomocí Windows PowerShellu. Dodávají se jako součást [modulu AzureRM. Automation](/powershell/azure/overview), který je k dispozici pro použití v sadách Automation a konfiguracích DSC.

|Rutiny|Popis|
|:---|:---|
|[Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Načte informace o certifikátu, který se má použít v sadě Runbook nebo konfiguraci DSC. Certifikát můžete načíst jenom z aktivity Get-AutomationCertificate.|
|[New-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Vytvoří nový certifikát do Azure Automation.|
[Remove-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Odebere certifikát z Azure Automation.|
|[Set-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Nastaví vlastnosti pro existující certifikát, včetně nahrání souboru certifikátu a nastavení hesla pro soubor. pfx.|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Nahraje certifikát služby pro zadanou cloudovou službu.|

## <a name="activities"></a>Aktivity

Aktivity v následující tabulce se používají pro přístup k certifikátům v rámci sady Runbook a konfigurací DSC.

| Aktivity | Popis |
|:---|:---|
|Get-AutomationCertificate|Načte certifikát, který se má použít v sadě Runbook nebo konfiguraci DSC. Vrátí objekt [System. Security. Cryptography. X509Certificates. X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) .|

> [!NOTE] 
> V sadě Runbook **nebo v konfiguraci** DSC byste se měli vyhnout používání proměnných v parametru – name, protože by to ztěžuje zjišťování závislostí mezi sadami Runbook a konfigurací DSC a proměnných automatizace v době návrhu.

## <a name="python2-functions"></a>Funkce Python2

Funkce v následující tabulce se používá pro přístup k certifikátům v Runbooku Python2.

| Funkce | Popis |
|:---|:---|
| automationassets.get_automation_certificate | Načte informace o prostředku certifikátu. |

> [!NOTE]
> Aby bylo možné získat přístup k funkcím assetu, musíte importovat modul **automationassets** na začátek Runbooku v Pythonu.

## <a name="creating-a-new-certificate"></a>Vytváří se nový certifikát.

Když vytváříte nový certifikát, nahrajete soubor. cer nebo. pfx do Azure Automation. Pokud označíte certifikát jako exportovatelný, můžete ho přenést z úložiště certifikátů Azure Automation. Pokud ji nelze exportovat, lze ji použít pouze k podepisování v rámci sady Runbook nebo konfigurace DSC. Azure Automation vyžaduje, aby certifikát měl poskytovatele: **Vylepšený zprostředkovatel kryptografických služeb RSA a AES společnosti Microsoft**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Vytvoření nového certifikátu s Azure Portal

1. V účtu Automation klikněte na dlaždici **assety** a otevřete stránku **assety** .
2. Kliknutím na dlaždici **certifikáty** otevřete stránku **certifikáty** .
3. V horní části stránky klikněte na **Přidat certifikát** .
4. Zadejte název certifikátu v **název** pole.
5. Pokud chcete vyhledat soubor. cer nebo. pfx, klikněte v části **nahrát soubor certifikátu**na **Vybrat soubor** . Pokud vyberete soubor. pfx, zadejte heslo a určete, jestli se může exportovat.
6. Kliknutím na **vytvořit** uložte nový prostředek certifikátu.

### <a name="to-create-a-new-certificate-with-powershell"></a>Vytvoření nového certifikátu pomocí prostředí PowerShell

Následující příklad ukazuje, jak vytvořit nový certifikát automatizace a označit ho jako exportovatelný. Tím se naimportuje existující soubor. pfx.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-resource-manager-template"></a>Vytvoření nového certifikátu pomocí šablony Správce prostředků

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
New-AzureRmResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="using-a-certificate"></a>Použití certifikátu

Chcete-li použít certifikát, použijte aktivitu **Get-AutomationCertificate** . Nemůžete použít rutinu [Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate) , protože vrací informace o prostředku certifikátu, ale ne samotný certifikát.

### <a name="textual-runbook-sample"></a>Ukázka textové sady Runbook

Následující vzorový kód ukazuje, jak přidat certifikát do cloudové služby v sadě Runbook. V této ukázce je heslo načteno z šifrované proměnné automatizace.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Ukázka grafického Runbooku

Do grafického Runbooku přidáte **Get-AutomationCertificate** kliknutím pravým tlačítkem myši na certifikát v podokně Knihovna a výběrem možnosti **Přidat na plátno**.

![Přidat certifikát na plátno](../media/certificates/automation-certificate-add-to-canvas.png)

Následující obrázek ukazuje příklad použití certifikátu v grafickém Runbooku. To je stejné jako v předchozím příkladu, který ukazuje, jak přidat certifikát do cloudové služby z textové sady Runbook.

![Příklad grafického vytváření](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Ukázka Python2

Následující příklad ukazuje, jak získat přístup k certifikátům v sadách Runbook Python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Další kroky

- Další informace o práci s odkazy na řízení logického toku aktivit, které je vaše sada Runbook navržena, najdete v tématu [odkazy v grafickém vytváření](../automation-graphical-authoring-intro.md#links-and-workflow). 
