---
title: Prostředků certifikátů ve službě Azure Automation
description: Certifikáty jsou bezpečně ve službě Azure Automation, takže jsou dostupné sady runbook nebo konfigurace DSC k ověřování vůči Azure a materiály třetích stran.  Tento článek vysvětluje podrobnosti certifikátů a jak pracovat s nimi v textové a grafické vytváření obsahu.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d230fa97d009f0ee2a3bc86a0b6b7c8d40687a46
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61216028"
---
# <a name="certificate-assets-in-azure-automation"></a>Prostředků certifikátů ve službě Azure Automation

Certifikáty jsou bezpečně uložen ve službě Azure Automation, jsou dostupné sady runbook nebo konfigurace DSC pomocí **Get-AzureRmAutomationCertificate** aktivity pro prostředky Azure Resource Manageru. Tato funkce vám umožní vytvořit runboocích a konfiguracích DSC, které používají certifikáty k ověřování nebo přidá je do Azure nebo prostředky třetích stran.

>[!NOTE]
>Zabezpečené prostředky ve službě Azure Automation zahrnovat přihlašovací údaje, certifikátů, připojení a zašifrované proměnné. Tyto prostředky jsou zašifrované a uložené ve službě Azure Automation jednotlivých účtů automation pomocí jedinečný klíč, který je generován. Tento klíč je uložen v systému spravované služby Key Vault. Před uložením o zabezpečený prostředek, je klíč načíst ze služby Key Vault a použije k zašifrování assetu. Tento proces se spravuje přes Azure Automation.

## <a name="azurerm-powershell-cmdlets"></a>Rutiny AzureRM Powershellu

Pro AzureRM rutiny v následující tabulce se používají k vytváření a správě prostředků přihlašovacích údajů automation pomocí prostředí Windows PowerShell. Se dodávají jako součást [modulu Azure RM.Automation](/powershell/azure/overview), která je k dispozici pro použití v runbooků služeb automatizace a konfigurace DSC.

|Rutiny|Popis|
|:---|:---|
|[Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Načte informace o certifikátu pro použití v runbooku nebo konfigurace DSC. Vlastní certifikát lze načíst pouze z Get-AutomationCertificate aktivity.|
|[New-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Vytvoří nový certifikát do služby Azure Automation.|
[Remove-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Certifikát se odebere ze služby Azure Automation.|
|[Set-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Nastaví vlastnosti existujícího certifikátu včetně nahrání souboru certifikátu a nastavení hesla pro .pfx.|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Nahrání certifikátu služby pro zadaná Cloudová služba.|

## <a name="activities"></a>Činnosti

Aktivity v následující tabulce se používají pro přístup k certifikáty v sadě runbook a konfiguracích DSC.

| Činnosti | Popis |
|:---|:---|
|Get-AutomationCertificate|Získá certifikát pro použití v runbooku nebo konfigurace DSC. Vrátí [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) objektu.|

> [!NOTE] 
> Byste neměli používat proměnné v parametru – Name **Get-AutomationCertificate** v runbooku nebo konfigurace DSC jako to komplikuje zjišťování závislostí mezi runbooky nebo konfigurace DSC a proměnné služeb automatizace v době návrhu.

## <a name="python2-functions"></a>Funkce Python2

Funkce v následující tabulce slouží k přístupu k certifikáty v sadě runbook Python2.

| Funkce | Popis |
|:---|:---|
| automationassets.get_automation_certificate | Načte informace o prostředek certifikátu. |

> [!NOTE]
> Je nutné naimportovat **automationassets** modulu na začátku své sadě runbook Python pro přístup k prostředku funkcí.

## <a name="creating-a-new-certificate"></a>Vytváří se nový certifikát

Když vytvoříte nový certifikát, můžete nahrát soubor .cer nebo .pfx do Azure Automation. Pokud označíte jako exportovatelný certifikátu, můžete ho převést z úložiště certifikátů služby Azure Automation. Pokud není exportovatelný, pak ho jde použít jenom pro podepisování v runbooku nebo konfigurace DSC. Azure Automation vyžaduje certifikát, který chcete mít zprostředkovatele: **Microsoft Enhanced RSA a AES Cryptographic Provider**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Chcete-li vytvořit nový certifikát pomocí webu Azure portal

1. Ve svém účtu Automation, klikněte na tlačítko **prostředky** otevřete dlaždici **prostředky** stránky.
2. Klikněte na tlačítko **certifikáty** otevřete dlaždici **certifikáty** stránky.
3. Klikněte na tlačítko **přidání certifikátu** v horní části stránky.
4. Zadejte název certifikátu v **název** pole.
5. Procházením vyhledejte soubor .cer nebo .pfx, klikněte na tlačítko **vyberte soubor** pod **nahrát soubor certifikátu**. Pokud jste vybrali soubor .pfx, zadejte heslo a zda ho můžete exportovat.
6. Klikněte na tlačítko **vytvořit** uložit nový prostředek certifikátu.

### <a name="to-create-a-new-certificate-with-powershell"></a>Vytvoření nového certifikátu pomocí prostředí PowerShell

Následující příklad ukazuje, jak vytvořit nový certifikát služby Automation a označte ji jako exportovatelný. Tento postup importuje existujícího souboru .pfx.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-resource-manager-template"></a>Vytvoření nového certifikátu pomocí šablony Resource Manageru

Následující příklad ukazuje, jak nasadit certifikát do vašeho účtu Automation pomocí šablony Resource Manageru pomocí prostředí PowerShell:

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

Pokud chcete použít certifikát, použijte **Get-AutomationCertificate** aktivity. Nelze použít [Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate) rutiny, protože se vrátí informace o prostředek certifikátu, ale ne samotný certifikát.

### <a name="textual-runbook-sample"></a>Ukázka textové sady runbook

Následující ukázkový kód ukazuje, jak přidat certifikát do cloudové služby v sadě runbook. V této ukázce je načíst heslo z proměnné šifrované automatizace.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Ukázkový grafický runbook

Přidáte **Get-AutomationCertificate** grafický runbook tak, že kliknete pravým tlačítkem na certifikát v podokně knihovna a vyberete **přidat na plátno**.

![Přidání certifikátu do plátna](../media/certificates/automation-certificate-add-to-canvas.png)

Následující obrázek ukazuje příklad použití certifikátu v grafický runbook. To je stejný jako předchozí příklad, který ukazuje, jak přidat certifikát do cloudové služby z textové sady runbook.

![Příklad grafické vytváření](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Ukázka Python2

Následující příklad ukazuje, jak získat přístup k certifikáty v sadách runbook Python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Další postup

- Další informace o práci s odkazy na ovládací prvek logický tok vaše sada runbook je určené k provádění aktivit najdete v tématu [odkazy v vytváření grafického obsahu](../automation-graphical-authoring-intro.md#links-and-workflow). 
