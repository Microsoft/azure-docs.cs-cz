---
title: Prostředky certifikátů ve službě Azure Automation
description: Certifikáty jsou bezpečně v Azure Automation, takže k nim mají přístup pomocí runbooků nebo konfigurací DSC k ověření podle prostředků Azure a třetích stran.  Tento článek vysvětluje podrobnosti o certifikátech a jak s nimi pracovat v textovéi a grafické tvorbě.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a66f73e028594cf90f1fa1765910a3df3adbad1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849475"
---
# <a name="certificate-assets-in-azure-automation"></a>Prostředky certifikátů ve službě Azure Automation

Certifikáty jsou bezpečně uložené v Azure Automation, takže k nim mají přístup pomocí runbooků nebo konfigurací DSC pomocí aktivity **Get-AzureRmAutomationCertificate** pro prostředky Azure Resource Manageru. Tato funkce umožňuje vytvářet sady Runbook a Konfigurace DSC, které používají certifikáty pro ověřování nebo je přidá do Azure nebo prostředků třetích stran.

>[!NOTE]
>Zabezpečené datové zdroje v Azure Automation zahrnují přihlašovací údaje, certifikáty, připojení a šifrované proměnné. Tyto prostředky jsou šifrované a uložené v Azure Automation pomocí jedinečného klíče, který se generuje pro každý účet automatizace. Tento klíč je uložen v systémovém trezoru klíčů. Před uložením zabezpečeného datového zdroje je klíč načten z trezoru klíčů a poté použit k šifrování datového zdroje. Tento proces spravuje Azure Automation.

## <a name="azurerm-powershell-cmdlets"></a>Rutiny prostředí AzureRM PowerShell

Pro AzureRM rutiny v následující tabulce se používají k vytvoření a správě prostředků pověření automatizace pomocí prostředí Windows PowerShell. Dodávají se jako součást [modulu AzureRM.Automation](/powershell/azure/overview), který je k dispozici pro použití v runbookech automatizace a konfiguracích DSC.

|Rutiny|Popis|
|:---|:---|
|[Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Načte informace o certifikátu, který má být používán v konfiguraci runbooku nebo DSC. Samotný certifikát můžete načíst pouze z aktivity Get-AutomationCertificate.|
|[New-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Vytvoří nový certifikát do Azure Automation.|
[Remove-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Odebere certifikát z Azure Automation.|
|[Set-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Nastaví vlastnosti existujícího certifikátu včetně nahrání souboru certifikátu a nastavení hesla pro soubor .pfx.|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Nahraje certifikát služby pro zadanou cloudovou službu.|

## <a name="activities"></a>Aktivity

Aktivity v následující tabulce se používají pro přístup k certifikátům v konfiguracích runbook a DSC.

| Aktivity | Popis |
|:---|:---|
|Get-AutomationCertificate|Získá certifikát pro použití v konfiguraci runbook nebo DSC. Vrátí objekt [System.Security.Cryptography.X509Certificates.X509Certificate2.](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)|

> [!NOTE] 
> Měli byste se vyhnout použití proměnných v parametru –Name **Get-AutomationCertificate** v konfiguraci sady Runbook nebo DSC, protože komplikuje zjišťování závislostí mezi runbooky nebo konfigurací DSC a proměnnými automatizace v době návrhu.

## <a name="python2-functions"></a>Funkce Pythonu2

Funkce v následující tabulce se používá pro přístup k certifikátům v runbooku Python2.

| Funkce | Popis |
|:---|:---|
| automationassets.get_automation_certificate | Načte informace o datovém zdroji certifikátu. |

> [!NOTE]
> Chcete-li získat přístup k funkcím datových zdrojů, musíte importovat modul **Automationassets** na začátku runbooku Pythonu.

## <a name="creating-a-new-certificate"></a>Vytvoření nového certifikátu

Když vytvoříte nový certifikát, nahrajete soubor CER nebo .pfx do Azure Automation. Pokud certifikát označíte jako exportovatelný, můžete ho přenést z úložiště certifikátů Azure Automation. Pokud není exportovatelný, lze jej použít pouze pro podepisování v rámci konfigurace runbooku nebo DSC. Azure Automation vyžaduje, aby certifikát měl poskytovatele: **Microsoft Enhanced RSA a AES Cryptographic Provider**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Vytvoření nového certifikátu pomocí portálu Azure

1. Na účtu Automation klikněte na dlaždici **Prostředky** a otevřete stránku **Datové zdroje.**
2. Kliknutím na dlaždici **Certifikáty** otevřete stránku **Certifikáty.**
3. V horní části stránky klikněte na **Přidat certifikát.**
4. Do pole **Název** zadejte název certifikátu.
5. Chcete-li vyhledat soubor CER nebo .pfx, klepněte v části **Nahrát soubor certifikátu**na **vybrat soubor** . Pokud vyberete soubor .pfx, zadejte heslo a zda jej lze exportovat.
6. Kliknutím na **Vytvořit** uložte nový datový zdroj certifikátu.

### <a name="to-create-a-new-certificate-with-powershell"></a>Vytvoření nového certifikátu pomocí PowerShellu

Následující příklad ukazuje, jak vytvořit nový certifikát automatizace a označit jej exportovat. Tím se importuje existující soubor .pfx.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-resource-manager-template"></a>Vytvoření nového certifikátu pomocí šablony Správce prostředků

Následující příklad ukazuje, jak nasadit certifikát do účtu Automation pomocí šablony Správce prostředků prostřednictvím prostředí PowerShell:

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

Chcete-li použít certifikát, použijte **get-automationcertificate** aktivity. Rutinu [Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate) nelze použít, protože vrací informace o datovém zdroji certifikátu, ale nikoli o samotném certifikátu.

### <a name="textual-runbook-sample"></a>Ukázka textového seznamu runbooků

Následující ukázkový kód ukazuje, jak přidat certifikát do cloudové služby v runbooku. V této ukázce je heslo načteno z proměnné šifrované automatizace.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Ukázka grafického seznamu runbooků

**Get-AutomationCertificate** přidáte do grafického runbooku kliknutím pravým tlačítkem myši na certifikát v podokně Knihovna a výběrem **možnosti Přidat na plátno**.

![Přidání certifikátu na plátno](../media/certificates/automation-certificate-add-to-canvas.png)

Následující obrázek ukazuje příklad použití certifikátu v grafické mašitě runbooku. Toje stejné jako v předchozím příkladu, který ukazuje, jak přidat certifikát do cloudové služby z textové ho stu.

![Ukázkové vytváření grafiky](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Ukázka Pythonu2

Následující ukázka ukazuje, jak získat přístup k certifikátům v runbookech Pythonu2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Další kroky

- Další informace o práci s odkazy na řízení logického toku aktivit, které je vaše aplikace Runbook navržena k provádění, naleznete [v tématu Odkazy v grafickém vytváření](../automation-graphical-authoring-intro.md#links-and-workflow). 
