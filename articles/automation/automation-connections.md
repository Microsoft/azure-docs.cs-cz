---
title: Assety připojení v Azure Automation.
description: Připojení prostředky ve službě Azure Automation obsahují informace potřebné pro připojení na externí službu nebo aplikaci z runbooku nebo konfigurace DSC. Tento článek vysvětluje podrobnosti o připojení a jak pracovat s nimi v textové a grafické vytváření.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: d2c2cc2964b8223a564d45359f332597a1ca1f87
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="connection-assets-in-azure-automation"></a>Assety připojení v Azure Automation.

Prostředek připojení automatizace obsahuje informace potřebné pro připojení na externí službu nebo aplikaci z runbooku nebo konfigurace DSC. To může zahrnovat informace požadované pro ověřování, jako je uživatelské jméno a heslo kromě informací o připojení, jako je například adresa URL nebo portu. Hodnota připojení je uchovávání všechny vlastnosti pro připojení ke konkrétní aplikaci v jeden prostředek a vytváření více proměnných. Uživatele můžete upravit hodnoty pro připojení na jednom místě, a můžete předat název připojení do sady runbook nebo konfigurace DSC v jeden parametr. Vlastnosti připojení jsou přístupné z sada runbook nebo konfigurace DSC s **Get-AutomationConnection** aktivity. 

Při vytváření připojení, je nutné zadat *typ připojení*. Typ připojení je šablonu, která definuje sadu vlastností. Připojení definuje hodnoty pro každou vlastnost v jeho typu připojení. Typy připojení se přidají do Azure Automation v integračních modulech nebo vytvořené pomocí [rozhraní API služby Azure Automation](http://msdn.microsoft.com/library/azure/mt163818.aspx) Pokud modul integrace obsahuje typ připojení a je importovat do vašeho účtu Automation. V ostatních případech musíte vytvořit soubor metadat pro určení typu připojení automatizace.  Další informace týkající se to najdete v tématu [moduly integrace](automation-integration-modules.md).  

>[!NOTE]
>Zabezpečené prostředky ve službě Azure Automation zahrnovat přihlašovací údaje, připojení, certifikátů a zašifrované proměnné. Tyto prostředky jsou zašifrovány a uložené ve službě Azure Automation pomocí jedinečný klíč, který se vygeneruje pro každý účet automation. Tento klíč je uložený v Key Vault. Před uložením o zabezpečený prostředek, je klíč načtený ze Key Vault a pak použije k zašifrování asset.

## <a name="windows-powershell-cmdlets"></a>Rutiny prostředí Windows PowerShell

Rutiny v následující tabulce se používají k vytváření a Správa připojení Automation pomocí prostředí Windows PowerShell. Se dodávají jako součást [modul Azure PowerShell](/powershell/azure/overview) která je k dispozici pro použití v runbooků služeb automatizace a konfigurace DSC.

|Rutina|Popis|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|Načte připojení. Zahrnuje zatřiďovací tabulku s hodnotami polí připojení.|
|[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Vytvoří nové připojení.|
|[Remove-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Odeberte existující připojení.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Nastaví hodnotu konkrétního pole pro existující připojení.|

## <a name="activities"></a>Aktivity

Aktivity v následující tabulce se používají pro přístup k připojením v runbooku nebo konfigurace DSC.

|Aktivity|Popis|
|---|---|
|[Get-AutomationConnection](/powershell/module/azure/get-azureautomationconnection?view=azuresmps-3.7.0)|Získá připojení pro použití. Vrátí hodnotu hash tabulku s vlastností připojení.|

>[!NOTE] 
>Byste neměli používat proměnné s názvem parametru – **Get - AutomationConnection** vzhledem k tomu, že to může zkomplikovat zjišťování závislostí mezi runbooky nebo konfigurace DSC a prostředky připojení v době návrhu.

 
## <a name="python2-functions"></a>Funkce Python2 
Funkce v následující tabulce se používá pro přístup k připojením v runbooku Python2. 

| Funkce | Popis | 
|:---|:---| 
| automationassets.get_automation_connection | Načte připojení. Vrátí slovník s vlastností připojení. | 

> [!NOTE] 
> Chcete-li přístup funkce asset musí naimportovat modul "automationassets" v horní části runbookem Python.

## <a name="creating-a-new-connection"></a>Vytvoření nového připojení

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Chcete-li vytvořit nové připojení pomocí portálu Azure

1. Z vašeho účtu automation, klikněte na tlačítko **prostředky** část otevřete **prostředky** okno.
2. Klikněte **připojení** část otevřete **připojení** okno.
3. Klikněte na tlačítko **přidat připojení** v horní části okna.
4. V **typ** rozevíracího seznamu, vyberte typ připojení, které chcete vytvořit. Formulář nabídne vlastnosti pro daný typ.
5. Vyplňte formulář a klikněte na tlačítko **vytvořit** uložit nové připojení.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Chcete-li vytvořit nové připojení pomocí prostředí Windows PowerShell

Vytvoření nového připojení pomocí prostředí Windows PowerShell [New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection) rutiny. Tato rutina má parametr s názvem **ConnectionFieldValues** , očekává [zatřiďovací tabulku](http://technet.microsoft.com/library/hh847780.aspx) definování hodnoty pro každé z vlastností definované typ připojení.

Pokud jste se seznámili s automatizace [účet Spustit jako](automation-sec-configure-azure-runas-account.md) k ověření runbooků pomocí objektu služby, skript prostředí PowerShell, uvedený jako alternativu k vytvoření účtu spustit jako z portálu, vytvoří nové připojení pomocí následující ukázkové příkazy Asset.  

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues 
```

Budete moci vytvořit prostředek připojení, protože při vytváření účtu Automation, automaticky obsahuje několik globální modulů ve výchozím nastavení společně s typem připojení pomocí tohoto skriptu **AzurServicePrincipal** na Vytvořte **AzureRunAsConnection** asset připojení.  To je důležité mějte na paměti, protože při pokusu o vytvoření nového prostředku připojení pro připojení k službě nebo aplikaci pomocí metody různá ověřovací ho selže, protože typ připojení není již definována v účtu Automation.  Další informace o tom, jak vytvořit vlastní typ připojení pro váš vlastní nebo modul z [Galerie prostředí PowerShell](https://www.powershellgallery.com), najdete v části [moduly integrace](automation-integration-modules.md)
  
## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Použití připojení v runbooku nebo konfigurace DSC

Načtení připojení v runbooku nebo konfigurace DSC s **Get-AutomationConnection** rutiny.  Nelze použít [Get-AzureRmAutomationConnection](https://docs.microsoft.com/powershell/resourcemanager/azurerm.automation/v1.0.12/Get-AzureRmAutomationConnection?redirectedfrom=msdn) aktivity.  Tato aktivita načítá hodnoty různých polí v připojení a vrací je jako [zatřiďovací tabulku](http://go.microsoft.com/fwlink/?LinkID=324844) který pak může být použit s příslušnými příkazy v sada runbook nebo konfigurace DSC.

### <a name="textual-runbook-sample"></a>Ukázkový textový

Následující vzorové příkazy ukazují, jak používat účet Spustit jako, již bylo zmíněno dříve, k ověření s prostředky Azure Resource Manager ve vašem runbooku.  Používá asset připojení představující účet Spustit jako, která odkazuje na základě certifikátu instančního objektu, nikoli přihlašovací údaje.  

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection 
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint 
```

### <a name="graphical-runbook-samples"></a>Ukázky grafický runbook

Přidání **Get-AutomationConnection** aktivitu grafický runbook pravým tlačítkem myši na připojení v podokně knihovna grafického editoru a výběrem **přidat na plátno**.

![](media/automation-connections/connection-add-canvas.png)

Následující obrázek ukazuje příklad použití připojení v grafický runbook.  Toto je stejný příkladu výše pro ověřování pomocí účtu spustit jako s textový.  Tento příklad používá **konstantní hodnota** sady dat **získat připojení spustit jako** aktivity, která používá objekt připojení pro ověřování.  A [propojení kanálu](automation-graphical-authoring-intro.md#links-and-workflow) zde slouží vzhledem k tomu, že zadaná sada parametrů ServicePrincipalCertificate je očekáván jeden objekt.

![](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Ukázkový runbook Python2
Následující příklad ukazuje, jak chcete ověřit pomocí připojení spustit jako v sadě runbook Python2.

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

- Zkontrolujte [odkazy v vytváření grafického obsahu](automation-graphical-authoring-intro.md#links-and-workflow) pochopit, jak k řízení a řízení toku logiky ve vašich sadách runbook.  

- Další informace o používání Azure Automation moduly Powershellu a osvědčené postupy pro vytváření vlastních modulů Powershellu fungovat jako moduly integrace v rámci Azure Automation najdete v tématu [moduly integrace](automation-integration-modules.md).  
