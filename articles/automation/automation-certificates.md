---
title: Prostředků certifikátů ve službě Azure Automation
description: Certifikáty můžete bezpečně uložená ve službě Azure Automation, takže jsou dostupné sady runbook nebo konfigurace DSC k ověřování vůči Azure a materiály třetích stran.  Tento článek vysvětluje podrobnosti certifikátů a jak pracovat s nimi v textové a grafické vytváření obsahu.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c13da6ff7c864ffa365dbad33d6eb0cf2e35fa42
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42058042"
---
# <a name="certificate-assets-in-azure-automation"></a>Prostředků certifikátů ve službě Azure Automation

Certifikáty můžou být uložené bezpečně v Azure Automation, jsou dostupné sady runbook nebo konfigurace DSC pomocí **Get-AzureRmAutomationCertificate** aktivity pro prostředky Azure Resource Manageru. Tato funkce vám umožní vytvořit runboocích a konfiguracích DSC, které používají certifikáty k ověřování nebo přidá je do Azure nebo prostředky třetích stran.

>[!NOTE]
>Zabezpečené prostředky ve službě Azure Automation zahrnovat přihlašovací údaje, certifikátů, připojení a zašifrované proměnné. Tyto prostředky jsou zašifrované a uložené ve službě Azure Automation jednotlivých účtů automation pomocí jedinečný klíč, který je generován. Tento klíč uložený ve službě Key Vault. Před uložením o zabezpečený prostředek, je klíč načíst ze služby Key Vault a použije k zašifrování assetu.

## <a name="azurerm-powershell-cmdlets"></a>Rutiny AzureRM Powershellu
Pro AzureRM rutiny v následující tabulce se používají k vytváření a správě prostředků přihlašovacích údajů automation pomocí prostředí Windows PowerShell. Se dodávají jako součást [modulu Azure RM.Automation](/powershell/azure/overview) která je k dispozici pro použití v runbooků služeb automatizace a konfigurace DSC.

|Rutiny|Popis|
|:---|:---|
|[Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Načte informace o certifikátu pro použití v runbooku nebo konfigurace DSC. Vlastní certifikát lze načíst pouze z Get-AutomationCertificate aktivity.|
|[New-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Vytvoří nový certifikát do služby Azure Automation.|
[Remove-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Certifikát se odebere ze služby Azure Automation.|Vytvoří nový certifikát do služby Azure Automation.
|[Set-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Nastaví vlastnosti existujícího certifikátu včetně nahrání souboru certifikátu a nastavení hesla pro .pfx.|
|[Add-AzureCertificate](https://msdn.microsoft.com/library/azure/dn495214.aspx)|Nahrání certifikátu služby pro zadaná Cloudová služba.|

## <a name="activities"></a>Aktivity
Aktivity v následující tabulce se používají pro přístup k certifikáty v sadě runbook a konfiguracích DSC.

| Aktivity | Popis |
|:---|:---|
|Get-AutomationCertificate|Získá certifikát pro použití v runbooku nebo konfigurace DSC. Vrátí [System.Security.Cryptography.X509Certificates.X509Certificate2](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate2.aspx) objektu.|

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

Když vytvoříte nový certifikát, můžete nahrát soubor .cer nebo .pfx do Azure Automation. Pokud označíte jako exportovatelný certifikátu, můžete ho převést z úložiště certifikátů služby Azure Automation. Pokud není exportovatelný, pak ho jde použít jenom pro podepisování v runbooku nebo konfigurace DSC. Vyžaduje certifikát, který chcete mít zprostředkovatele služby Azure Automation: **Microsoft Enhanced RSA a AES Cryptographic Provider**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Chcete-li vytvořit nový certifikát pomocí webu Azure portal

1. Ve svém účtu Automation, klikněte na tlačítko **prostředky** otevřete dlaždici **prostředky** okno.
1. Klikněte na tlačítko **certifikáty** otevřete dlaždici **certifikáty** okno.
1. Klikněte na tlačítko **přidání certifikátu** v horní části okna.
1. Zadejte název certifikátu v **název** pole.
1. Procházením vyhledejte soubor .cer nebo .pfx, klikněte na tlačítko **vyberte soubor** pod **nahrát soubor certifikátu**. Pokud jste vybrali soubor .pfx, zadejte heslo a zda je povoleno export.
1. Klikněte na tlačítko **vytvořit** uložit nový prostředek certifikátu.

### <a name="to-create-a-new-certificate-with-windows-powershell"></a>Chcete-li vytvořit nový certifikát pomocí Windows Powershellu

Následující příklad ukazuje, jak vytvořit nový certifikát služby Automation a označte ji jako exportovatelný. Tento postup importuje existujícího souboru .pfx.

```powershell-interactive
$certName = 'MyCertificate'
$certPath = '.\MyCert.pfx'
$certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable -ResourceGroupName $ResourceGroup
```

## <a name="using-a-certificate"></a>Použití certifikátu

Pokud chcete použít certifikát, použijte **Get-AutomationCertificate** aktivity. Nelze použít [Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate?view=azurermps-6.6.0) rutiny, protože se vrátí informace o prostředek certifikátu, ale ne samotný certifikát.

### <a name="textual-runbook-sample"></a>Ukázka textové sady runbook

Následující ukázkový kód ukazuje, jak přidat certifikát do cloudové služby v sadě runbook. V této ukázce je načíst heslo z proměnné šifrované automatizace.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Ukázkový grafický runbook

Přidáte **Get-AutomationCertificate** grafický runbook tak, že kliknete pravým tlačítkem na certifikát v podokně knihovna grafický editor a vyberete **přidat na plátno**.

![Přidání certifikátu do plátna](media/automation-certificates/automation-certificate-add-to-canvas.png)

Následující obrázek ukazuje příklad použití certifikátu v grafický runbook. To je stejný jako v předchozím příkladu pro přidání certifikátu do cloudové služby z textové sady runbook.

![Příklad grafické vytváření ](media/automation-certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Ukázka Python2
Následující příklad ukazuje, jak získat přístup k certifikáty v sadách runbook Python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert 
```

## <a name="next-steps"></a>Další postup

- Další informace o práci s odkazy na ovládací prvek logický tok vaše sada runbook je určené k provádění aktivit najdete v tématu [odkazy v vytváření grafického obsahu](automation-graphical-authoring-intro.md#links-and-workflow). 
