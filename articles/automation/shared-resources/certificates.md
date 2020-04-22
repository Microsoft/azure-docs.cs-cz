---
title: Správa certifikátu v Azure Automation
description: Certifikáty se bezpečně ukládají v Azure Automation, takže runbooky nebo konfigurace DSC k nim mají přístup k ověření podle prostředků Azure a třetích stran. Tento článek vysvětluje podrobnosti o certifikátech a jak s nimi pracovat v textovéi a grafické tvorbě.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69bc1a0333365e15452c6d3b253266d37d99b608
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732829"
---
# <a name="manage-certificates-in-azure-automation"></a>Správa certifikátů v Azure Automation

Certifikáty jsou bezpečně uložené v Azure Automation, takže k nim mají přístup pomocí runbooků nebo konfigurací DSC pomocí aktivity [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) pro prostředky Azure Resource Manager. Zabezpečené úložiště certifikátů umožňuje vytvářet sady Runbook a konfigurace DSC, které používají certifikáty pro ověřování nebo je přidávají do Azure nebo prostředků třetích stran.

Zabezpečené datové zdroje v Azure Automation zahrnují přihlašovací údaje, certifikáty, připojení a šifrované proměnné. Tyto prostředky jsou šifrované a uložené v Azure Automation pomocí jedinečného klíče, který se generuje pro každý účet automatizace. Tento klíč je uložen v systémově spravovaném trezoru klíčů. Před uložením zabezpečeného datového zdroje je klíč načten z trezoru klíčů a poté použit k šifrování datového zdroje. Tento proces spravuje Azure Automation.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](../automation-update-azure-modules.md).

## <a name="az-powershell-cmdlets"></a>Rutiny prostředí Az PowerShell

Pro Az rutiny v následující tabulce se používají k vytvoření a správě prostředků pověření automatizace pomocí prostředí Windows PowerShell. Dodávají se jako součást [modulu Az.Automation](/powershell/azure/overview), který je k dispozici pro použití v runbookech automation a konfiguracích DSC.

|Rutina |Popis|
| --- | ---|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Nahraje certifikát služby pro zadanou cloudovou službu.|
|[Certifikát Get-AzAutomation](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Načte informace o certifikátu, který má být používán v konfiguraci runbooku nebo DSC. Samotný certifikát můžete načíst `Get-AutomationCertificate` pouze pomocí aktivity.|
|[Nový certifikát AzAutomation](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Vytvoří nový certifikát v Azure Automation.|
|[Odebrat azautomationcertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Odebere certifikát z Azure Automation.|
|[Set-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|Nastaví vlastnosti existujícího certifikátu včetně nahrání souboru certifikátu a nastavení hesla pro soubor **PFX.**|

## <a name="activities"></a>Aktivity

Aktivity v následující tabulce se používají pro přístup k certifikátům v konfiguracích runbook a DSC.

| Aktivity | Popis |
|:---|:---|
|`Get-AutomationCertificate`|Získá certifikát pro použití v konfiguraci runbook nebo DSC. Vrátí objekt [System.Security.Cryptography.X509Certificates.X509Certificate2.](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)|

> [!NOTE] 
> Měli byste se vyhnout `Name` použití `Get-AutomationCertificate` proměnných v parametru v konfiguraci runbooknebo DSC. Použití proměnných v tomto parametru komplikuje zjišťování závislostí mezi runbooky nebo konfiguracemi DSC a proměnnými automatizace v době návrhu.

## <a name="python-2-functions"></a>Funkce Pythonu 2

Funkce v následující tabulce se používá pro přístup k certifikátům v pythonu 2 runbook.

| Funkce | Popis |
|:---|:---|
| `automationassets.get_automation_certificate` | Načte informace o datovém zdroji certifikátu. |

> [!NOTE]
> Chcete-li `automationassets` získat přístup k funkcím datových zdrojů, musíte modul importovat na začátku sady Runbook v Pythonu.

## <a name="creating-a-new-certificate"></a>Vytvoření nového certifikátu

Když vytvoříte nový certifikát, nahrajete soubor CER nebo .pfx do Azure Automation. Pokud certifikát označíte jako exportovatelný, můžete ho přenést z úložiště certifikátů Azure Automation. Pokud není exportovatelný, lze jej použít pouze pro podepisování v rámci konfigurace runbooku nebo DSC. Azure Automation vyžaduje, aby certifikát měl poskytovatele **Microsoft Enhanced RSA a AES Cryptographic Provider**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Vytvoření nového certifikátu na webu Azure Portal

1. Na účtu Automation kliknutím na **Datové zdroje** otevřete stránku Datové zdroje.
2. Výběrem **možnosti Certifikáty** otevřete stránku Certifikáty.
3. V horní části stránky klikněte na **Přidat certifikát.**
4. Do pole **Název** zadejte název certifikátu.
5. Chcete-li vyhledat soubor **CER** nebo **.pfx,** klepněte v části **Nahrát soubor certifikátu**na **vybrat soubor** . Pokud vyberete soubor **.pfx,** zadejte heslo a určete, zda jej lze exportovat.
6. Kliknutím na **Vytvořit** uložte nový datový zdroj certifikátu.

### <a name="create-a-new-certificate-with-powershell"></a>Vytvoření nového certifikátu pomocí PowerShellu

Následující příklad ukazuje, jak vytvořit nový certifikát automatizace a označit jej exportovat. Tento příklad importuje existující soubor **.pfx.**

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Vytvoření nového certifikátu pomocí šablony Správce prostředků

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
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="using-a-certificate"></a>Použití certifikátu

Chcete-li použít certifikát, použijte aktivitu. `Get-AutomationCertificate` Rutinu [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) nelze použít, protože vrací informace o datovém zdroji certifikátu, ale nikoli o samotném certifikátu.

### <a name="textual-runbook-example"></a>Textový příklad runbooku

Následující příklad ukazuje, jak přidat certifikát do cloudové služby v runbooku. V této ukázce je heslo načteno z proměnné šifrované automatizace.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-example"></a>Příklad grafického seznamu runbook

Přidejte `Get-AutomationCertificate` aktivitu do grafického runbooku tak, že kliknete pravým tlačítkem myši na certifikát v podokně Knihovna a vyberete **přidat na plátno**.

![Přidání certifikátu na plátno](../media/certificates/automation-certificate-add-to-canvas.png)

Následující obrázek ukazuje příklad použití certifikátu v grafické mašitě runbooku. Toje stejné jako v předchozím příkladu, který ukazuje, jak přidat certifikát do cloudové služby z textové ho stu.

![Ukázkové vytváření grafiky](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Příklad pythonu 2

Následující příklad ukazuje, jak získat přístup k certifikátům v runbookech Pythonu2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Další kroky

- Další informace o práci s odkazy na řízení logického toku aktivit prováděných v aplikaci Runbook naleznete [v tématu Odkazy v grafickém vytváření](../automation-graphical-authoring-intro.md#links-and-workflow). 
