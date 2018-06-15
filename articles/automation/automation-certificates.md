---
title: Certifikát prostředky ve službě Azure Automation
description: Certifikáty můžete bezpečně uloženy ve službě Azure Automation, takže přístupná pomocí sady runbook nebo konfigurace DSC k ověřování na základě Azure a prostředky třetích stran.  Tento článek vysvětluje podrobnosti o certifikáty a postupy pro práci s nimi v textové a grafické vytváření.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 70d5c777f98a1e09125c7ef42ba597009bde084b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194329"
---
# <a name="certificate-assets-in-azure-automation"></a>Certifikát prostředky ve službě Azure Automation

Certifikáty můžete bezpečně uloženy ve službě Azure Automation, přístupná pomocí sady runbook nebo konfigurací DSC pomocí **Get-AzureRmAutomationCertificate** aktivity pro prostředky Azure Resource Manager. Tato funkce vám umožní vytvořit runboocích a konfiguracích DSC, které používají certifikáty k ověřování nebo přidá je do Azure nebo prostředky třetích stran.

>[!NOTE]
>Zabezpečené prostředky ve službě Azure Automation zahrnovat přihlašovací údaje, připojení, certifikátů a zašifrované proměnné. Tyto prostředky jsou zašifrovány a uložené ve službě Azure Automation pomocí jedinečný klíč, který se vygeneruje pro každý účet automation. Tento klíč je uložený v Key Vault. Před uložením o zabezpečený prostředek, je klíč načtený ze Key Vault a pak použije k zašifrování asset.

## <a name="azurerm-powershell-cmdlets"></a>Rutiny prostředí AzureRM PowerShell
Pro AzureRM rutiny v následující tabulce se používají k vytváření a správě prostředků přihlašovacích údajů automatizace v prostředí Windows PowerShell. Se dodávají jako součást [AzureRM.Automation modulu](/powershell/azure/overview) která je k dispozici pro použití v runbooků služeb automatizace a konfigurace DSC.

|Rutiny|Popis|
|:---|:---|
|[Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Načte informace o certifikátu pro použití v runbooku nebo konfigurace DSC. Samotný certifikát můžete načíst jenom z Get-AutomationCertificate aktivity.|
|[New-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Vytvoří nový certifikát do Azure Automation.|
[Remove-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Odebere certifikát Azure Automation.|Vytvoří nový certifikát do Azure Automation.
|[Set-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Nastaví vlastnosti existujícího certifikátu včetně nahrání souboru certifikátu a nastavení hesla pro .pfx.|
|[Add-AzureCertificate](https://msdn.microsoft.com/library/azure/dn495214.aspx)|Odešle certifikát služby pro zadané cloudové služby.|

## <a name="activities"></a>Aktivity
Aktivity v následující tabulce se používají pro přístup k certifikáty v sadě runbook a konfigurace DSC.

| Aktivity | Popis |
|:---|:---|
|Get-AutomationCertificate|Získá certifikát pro použití v runbooku nebo konfigurace DSC. Vrátí [System.Security.Cryptography.X509Certificates.X509Certificate2](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate2.aspx) objektu.|

> [!NOTE] 
> Byste neměli používat proměnné v parametru – Name **Get-AutomationCertificate** v sady runbook nebo konfigurace DSC jak komplikuje zjišťování závislostí mezi runbooky nebo konfigurace DSC a proměnné služeb automatizace v době návrhu.

## <a name="python2-functions"></a>Funkce Python2

Funkce v následující tabulce slouží k přístupu certifikáty v sadě runbook Python2.

| Funkce | Popis |
|:---|:---|
| automationassets.get_automation_certificate | Načte informace o prostředek certifikátu. |

> [!NOTE]
> Je nutné naimportovat **automationassets** modulu na začátku runbookem Python, aby měli přístup funkce asset.

## <a name="creating-a-new-certificate"></a>Vytvoření nového certifikátu

Když vytvoříte nový certifikát, odeslat soubor .cer nebo .pfx pro Azure Automation. Pokud označíte certifikátu jako exportovatelný, můžete ho přenést z úložiště certifikátů Azure Automation. Pokud není exportovatelný, pak je můžete jenom použije pro podepisování v rámci sady runbook nebo konfigurace DSC. Vyžaduje certifikát, který chcete mít zprostředkovatele služby Azure Automation: **Microsoft Enhanced RSA a AES Cryptographic Provider**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Chcete-li vytvořit nový certifikát pomocí portálu Azure

1. Z vašeho účtu Automation, klikněte **prostředky** dlaždici otevřete **prostředky** okno.
1. Klikněte **certifikáty** dlaždici otevřete **certifikáty** okno.
1. Klikněte na tlačítko **přidat certifikát** v horní části okna.
1. Zadejte název certifikátu **název** pole.
1. Chcete-li vyhledat soubor .cer nebo .pfx, klikněte na tlačítko **vyberte soubor** pod **nahrát soubor s certifikátem**. Pokud jste vybrali soubor .pfx, zadejte heslo a zda je povolen export.
1. Klikněte na tlačítko **vytvořit** uložit nový prostředek certifikátu.

### <a name="to-create-a-new-certificate-with-windows-powershell"></a>Chcete-li vytvořit nový certifikát v prostředí Windows PowerShell

Následující příklad ukazuje, jak vytvořit nový certifikát automatizace a označte ji jako exportovatelný. To Importuje existující soubor .pfx.

```powershell-interactive
$certName = 'MyCertificate'
$certPath = '.\MyCert.pfx'
$certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable -ResourceGroupName $ResourceGroup
```

## <a name="using-a-certificate"></a>Použití certifikátu

Chcete-li používat certifikát, použijte **Get-AutomationCertificate** aktivity. Nelze použít [Get-AzureRmAutomationCertificate](https://msdn.microsoft.com/library/mt603765.aspx) rutiny vzhledem k tomu, že ji vrací informace o prostředek certifikátu, ale ne samotný certifikát.

### <a name="textual-runbook-sample"></a>Ukázkový textový

Následující vzorový kód ukazuje, jak přidat certifikát do cloudové služby v sadě runbook. V této ukázce je z proměnná šifrované automatizace načíst heslo.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Ukázka grafický runbook

Přidání **Get-AutomationCertificate** na grafický runbook pravým tlačítkem na certifikát v podokně knihovna grafického editoru a výběrem **přidat na plátno**.

![Přidání certifikátu na plátno](media/automation-certificates/automation-certificate-add-to-canvas.png)

Následující obrázek ukazuje příklad použití certifikátu v grafický runbook. Toto je stejný jako v předchozím příkladu pro přidání certifikátu do cloudové služby z textové runbooku.

![Vytváření grafického obsahu příklad ](media/automation-certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Ukázka Python2
Následující příklad ukazuje, jak pro přístup k certifikáty v sadách runbook Python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert 
```

## <a name="next-steps"></a>Další postup

- Další informace o práci s odkazy na řízení logický tok vaše sada runbook je navržen pro provádění aktivit najdete v tématu [odkazy v vytváření grafického obsahu](automation-graphical-authoring-intro.md#links-and-workflow). 
