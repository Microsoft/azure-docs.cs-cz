---
title: Assety připojení v Azure Automation.
description: Assety připojení v Azure Automation. obsahují informace potřebné pro připojení k externí službě nebo aplikaci z runbooku nebo konfigurace DSC. Tento článek vysvětluje podrobnosti připojení a jak pracovat s nimi v textové a grafické vytváření obsahu.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2a28c8056e6dc25148299415a63a32993e874e01
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284536"
---
# <a name="connection-assets-in-azure-automation"></a>Assety připojení v Azure Automation.

Asset připojení Automation obsahuje informace potřebné pro připojení k externí službě nebo aplikaci z runbooku nebo konfigurace DSC. To může zahrnovat informace požadované pro ověřování, jako je uživatelské jméno a heslo spolu s informacemi o připojení, jako je například adresa URL nebo portu. Hodnota připojení se zachovat všechny vlastnosti pro připojení ke konkrétní aplikaci v jeden prostředek na rozdíl od vytváření několika proměnných. Uživatel může upravovat hodnoty pro připojení na jednom místě a název připojení můžete předat do runbooku nebo konfigurace DSC v jeden parametr. Vlastnosti připojení jsou přístupné z runbooku nebo konfigurace DSC se **Get-AutomationConnection** aktivity. 

Když vytvoříte připojení, je nutné zadat *typ připojení*. Typ připojení je šablonu, která definuje sadu vlastností. Připojení se definuje hodnoty pro jednotlivé vlastnosti definované v jeho typ připojení. Typy připojení jsou přidány do služby Azure Automation v integračních modulech nebo vytvářené pomocí [rozhraní API služby Azure Automation](https://msdn.microsoft.com/library/azure/mt163818.aspx) Pokud modulu integrace obsahuje typ připojení a importovat do účtu Automation. V opačném případě je potřeba vytvořit soubor metadat pro určení typu připojení služby Automation.  Další informace ohledně této najdete v tématu [moduly integrace](automation-integration-modules.md).  

>[!NOTE]
>Zabezpečené prostředky ve službě Azure Automation zahrnovat přihlašovací údaje, certifikátů, připojení a zašifrované proměnné. Tyto prostředky jsou zašifrované a uložené ve službě Azure Automation jednotlivých účtů automation pomocí jedinečný klíč, který je generován. Tento klíč uložený ve službě Key Vault. Před uložením o zabezpečený prostředek, je klíč načíst ze služby Key Vault a použije k zašifrování assetu.

## <a name="windows-powershell-cmdlets"></a>Rutiny Windows Powershellu

Rutiny v následující tabulce se používají k vytváření a Správa připojení Automation pomocí prostředí Windows PowerShell. Se dodávají jako součást [modulu Azure PowerShell](/powershell/azure/overview) která je k dispozici pro použití v runbooků služeb automatizace a konfigurace DSC.

|Rutina|Popis|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|Načte připojení. Zahrnuje zatřiďovací tabulku s hodnotami polí připojení.|
|[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Vytvoří nové připojení.|
|[Remove-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Odebere existující připojení.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Nastaví hodnotu konkrétního pole pro existující připojení.|

## <a name="activities"></a>Aktivity

Aktivity v následující tabulce se používají pro přístup k připojením v runbooku nebo konfigurace DSC.

|Aktivity|Popis|
|---|---|
|[Get-AutomationConnection](/powershell/module/servicemanagement/azure/get-azureautomationconnection?view=azuresmps-3.7.0)|Získá připojení pro použití. Vrátí zatřiďovací tabulku s vlastnostmi připojení.|

>[!NOTE] 
>Byste neměli používat proměnné s názvem parametru – **Get-AutomationConnection** protože to může zkomplikovat zjišťování závislostí mezi runbooky nebo konfigurace DSC a assety připojení v době návrhu.

 
## <a name="python2-functions"></a>Funkce Python2 
Funkce v následující tabulce se používá pro přístup k připojením v sadě runbook Python2. 

| Funkce | Popis | 
|:---|:---| 
| automationassets.get_automation_connection | Načte připojení. Vrátí slovník s vlastnostmi připojení. | 

> [!NOTE] 
> Je nutné naimportovat modul "automationassets" v horní části stránky sady Python runbook pro přístup k funkce asset.

## <a name="creating-a-new-connection"></a>Vytváří se nové připojení

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Chcete-li vytvořit nové připojení pomocí webu Azure portal

1. Ve svém účtu automation, klikněte na tlačítko **prostředky** části otevřete **prostředky** okno.
2. Klikněte na tlačítko **připojení** části otevřete **připojení** okno.
3. Klikněte na tlačítko **přidat připojení** v horní části okna.
4. V **typ** rozevíracím seznamu vyberte typ připojení, které chcete vytvořit. Formulář zobrazíte vlastnosti pro příslušný konkrétní typ.
5. Vyplňte formulář a klikněte na tlačítko **vytvořit** uložit nové připojení.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Chcete-li vytvořit nové připojení pomocí prostředí Windows PowerShell

Vytvoření nového připojení pomocí prostředí Windows PowerShell [New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection) rutiny. Tato rutina má parametr s názvem **ConnectionFieldValues** , který očekává, že [zatřiďovací tabulku](https://technet.microsoft.com/library/hh847780.aspx) definování hodnot pro vlastnosti určené typ připojení.

Pokud jste se seznámili s automatizace [účet Spustit jako](automation-sec-configure-azure-runas-account.md) k ověření runbooků pomocí instančního objektu, skript prostředí PowerShell, uvedený jako alternativu k vytvoření účtu spustit jako z portálu, vytvoří nové připojení pomocí následující ukázkové příkazy z prostředku.  

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Budete moct vytvořit asset připojení, protože při vytváření účtu Automation, automaticky zahrnuje několik globální moduly ve výchozím nastavení spolu s typem připojení pomocí skriptu **AzureServicePrincipal** do Vytvořte **AzureRunAsConnection** prostředek připojení.  To je důležité mít na paměti, protože při pokusu o vytvoření nového prostředku připojení pro připojení ke službě nebo aplikaci s metodou různých ověřovacích, to se nezdaří, protože typ připojení se už definované ve vašem účtu Automation.  Další informace o tom, jak vytvořit vlastní typ připojení pro vaše vlastní nebo modul z [Galerie prostředí PowerShell](https://www.powershellgallery.com), naleznete v tématu [integrační moduly](automation-integration-modules.md)
  
## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Použití připojení v runbooku nebo konfigurace DSC

Načíst připojení v runbooku nebo konfigurace DSC se **Get-AutomationConnection** rutiny.  Nelze použít [Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection) aktivity.  Tato aktivita načítá hodnoty různých polí v připojení a vrátí je jako [zatřiďovací tabulku](https://go.microsoft.com/fwlink/?LinkID=324844) které pak jde použít s příslušnými příkazy v runbooku nebo konfigurace DSC.

### <a name="textual-runbook-sample"></a>Ukázka textové sady runbook

Následující vzorové příkazy ukazují, jak používat k ověření pomocí prostředků Azure Resource Manageru v sadě runbook, již bylo zmíněno dříve, účet Spustit jako.  Používá asset připojení reprezentující účet Spustit jako, který se odkazuje pomocí certifikátu instančního objektu, nikoli přihlašovací údaje.  

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection 
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!IMPORTANT]
> **Add-AzureRmAccount** je nyní alias pro **Connect-AzureRMAccount**. Při vyhledávání knihovny položky, pokud se nezobrazí **Connect-AzureRMAccount**, můžete použít **Add-AzureRmAccount**, nebo ve vašem účtu Automation můžete aktualizovat moduly.

### <a name="graphical-runbook-samples"></a>Ukázky grafického runbooku

Přidáte **Get-AutomationConnection** aktivitu grafický runbook tak, že kliknete pravým tlačítkem na připojení v podokně knihovna grafický editor a vyberete **přidat na plátno**.

![Přidat na plátno](media/automation-connections/connection-add-canvas.png)

Následující obrázek ukazuje příklad použití připojení v grafický runbook.  Toto je stejný příklad výše uvedené pro ověřování pomocí účtu spustit jako s textovou sady runbook.  V tomto příkladu **konstantní hodnota** datovou sadu pro **získat připojení spustit jako** aktivitou, která používá objekt připojení pro ověřování.  A [propojení kanálu](automation-graphical-authoring-intro.md#links-and-workflow) je zde použita, protože sada parametrů ServicePrincipalCertificate je očekáván jeden objekt.

![získat připojení](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Ukázkový runbook Python2
Následující příklad ukazuje, jak ověřovat pomocí připojení spustit jako v sadě runbook Python2.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
  """ Returns credentials to authenticate against Azure resoruce manager """
  from OpenSSL import crypto
  from msrestazure import azure_active_directory
  import adal

  # Get the Azure Automation Run As service principal certificate
  cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
  pks12_cert = crypto.load_pkcs12(cert)
  pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

  # Get Run As connection information for the Azure Automation service principal
  application_id = runas_connection["ApplicationId"]
  thumbprint = runas_connection["CertificateThumbprint"]
  tenant_id = runas_connection["TenantId"]

  # Authenticate with service principal certificate
  resource = "https://management.core.windows.net/"
  authority_url = ("https://login.microsoftonline.com/" + tenant_id)
  context = adal.AuthenticationContext(authority_url)
  return azure_active_directory.AdalAuthentication(
    lambda: context.acquire_token_with_client_certificate(
      resource,
      application_id,
      pem_pkey,
      thumbprint)
  )

# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

## <a name="next-steps"></a>Další postup

- Kontrola [odkazy v vytváření grafického obsahu](automation-graphical-authoring-intro.md#links-and-workflow) pochopit, jak k řízení a řízení toku logiku v runboocích.  

- Další informace o službě Azure Automation použití modulů Powershellu a osvědčené postupy pro vytváření vlastních modulů Powershellu fungovat jako moduly integrace v rámci Azure Automation najdete v tématu [moduly integrace](automation-integration-modules.md).  
