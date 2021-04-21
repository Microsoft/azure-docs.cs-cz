---
title: Správa připojení v Azure Automation
description: V tomto článku se dozvíte, jak spravovat Azure Automation připojení k externím službám nebo aplikacím a jak s nimi pracovat v sadách Runbook.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: has-adal-ref, devx-track-azurepowershell
ms.openlocfilehash: 4b5983d6ea4ea9065d1292a2c5ee0c094cea093b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834889"
---
# <a name="manage-connections-in-azure-automation"></a>Správa připojení v Azure Automation

Azure Automation prostředek připojení obsahuje níže uvedené informace. Tyto informace jsou vyžadovány pro připojení k externí službě nebo aplikaci z Runbooku nebo konfigurace DSC. 

* Informace potřebné pro ověřování, například uživatelské jméno a heslo
* Informace o připojení, jako je adresa URL nebo port

Prostředek připojení uchovává všechny vlastnosti pro připojení ke konkrétní aplikaci, takže není nutné vytvářet více proměnných. Hodnoty pro připojení můžete upravit na jednom místě a název připojení k sadě Runbook nebo konfiguraci DSC můžete předat v jednom parametru. Sada Runbook nebo konfigurace přistupuje k vlastnostem připojení pomocí interní `Get-AutomationConnection` rutiny.

Když vytvoříte připojení, musíte zadat typ připojení. Typ připojení je šablona, která definuje sadu vlastností. Typ připojení můžete přidat k Azure Automation pomocí integračního modulu se souborem metadat. Je také možné vytvořit typ připojení pomocí [rozhraní Azure Automation API](/previous-versions/azure/reference/mt163818(v=azure.100)) , pokud modul Integration Module zahrnuje typ připojení a je importován do svého účtu Automation. 

>[!NOTE]
>Zabezpečené prostředky v Azure Automation zahrnují přihlašovací údaje, certifikáty, připojení a šifrované proměnné. Tyto prostředky jsou zašifrované a uložené v Azure Automation pomocí jedinečného klíče, který se generuje pro každý účet Automation. Azure Automation ukládá klíč do Key Vault spravovaném systémem. Před uložením zabezpečeného assetu Automation načte klíč z Key Vault a pak ho použije k zašifrování prostředku. 

## <a name="connection-types"></a>Typy připojení

Azure Automation zpřístupní následující předdefinované typy připojení:

* `Azure` – Představuje připojení používané ke správě klasických prostředků.
* `AzureServicePrincipal` – Představuje připojení, které používá účet Spustit jako pro Azure.
* `AzureClassicCertificate` – Představuje připojení používané účtem Azure spustit jako pro Classic.

Ve většině případů nemusíte vytvářet prostředek připojení, protože se vytvoří při vytváření [účtu Spustit jako](automation-security-overview.md).

## <a name="powershell-cmdlets-to-access-connections"></a>Rutiny prostředí PowerShell pro přístup k připojením

Rutiny v následující tabulce vytvářejí a spravují připojení automatizace pomocí PowerShellu. Dodávají se jako součást [AZ moduls](shared-resources/modules.md#az-modules).

|Rutina|Popis|
|---|---|
|[Get-AzAutomationConnection](/powershell/module/az.automation/get-azautomationconnection)|Načte informace o připojení.|
|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)|Vytvoří nové připojení.|
|[Remove-AzAutomationConnection](/powershell/module/Az.Automation/Remove-AzAutomationConnection)|Odebere existující připojení.|
|[Set-AzAutomationConnectionFieldValue](/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue)|Nastaví hodnotu konkrétního pole pro existující připojení.|

## <a name="internal-cmdlets-to-access-connections"></a>Interní rutiny pro přístup k připojením

Interní rutina v následující tabulce se používá pro přístup k připojením ve vašich sadách Runbook a konfiguracích DSC. Tato rutina se dodává s globálním modulem `Orchestrator.AssetManagement.Cmdlets` . Další informace najdete v tématu [interní rutiny](shared-resources/modules.md#internal-cmdlets).

|Interní rutina|Description|
|---|---|
|`Get-AutomationConnection` | Načte hodnoty různých polí v připojení a vrátí je jako [zatřiďovací tabulku](/powershell/module/microsoft.powershell.core/about/about_hash_tables). Tuto zatřiďovací tabulku pak můžete použít s příslušnými příkazy v konfiguraci sady Runbook nebo DSC.|

>[!NOTE]
>Vyhněte se použití proměnných s `Name` parametrem `Get-AutomationConnection` . Použití proměnných v tomto případě může zkomplikovat zjišťování závislostí mezi sadami Runbook a konfigurací DSC a assety připojení v době návrhu.

## <a name="python-functions-to-access-connections"></a>Funkce Pythonu pro přístup k připojením

Funkce v následující tabulce slouží k přístupu k připojením v sadě Runbook Python 2 a 3. Sady Python 3 Runbooky jsou momentálně ve verzi Preview.

| Funkce | Description |
|:---|:---|
| `automationassets.get_automation_connection` | Načte připojení. Vrátí slovník s vlastnostmi připojení. |

> [!NOTE]
> Aby bylo možné `automationassets` získat přístup k funkcím assetu, musíte importovat modul v horní části Runbooku sady Python.

## <a name="create-a-new-connection"></a>Vytvoření nového připojení

### <a name="create-a-new-connection-with-the-azure-portal"></a>Vytvoření nového připojení pomocí Azure Portal

Vytvoření nového připojení v Azure Portal:

1. V účtu Automation klikněte na **připojení** v části **sdílené prostředky**.
2. Klikněte na **+ Přidat připojení** na stránce připojení.
4. V poli **typ** v podokně nové připojení vyberte typ připojení, které chcete vytvořit. Vaše volby jsou `Azure` , `AzureServicePrincipal` a `AzureClassicCertificate` . 
5. Ve formuláři se zobrazí vlastnosti typu připojení, který jste zvolili. Vyplňte formulář a kliknutím na **vytvořit** uložte nové připojení.

### <a name="create-a-new-connection-with-windows-powershell"></a>Vytvoření nového připojení pomocí prostředí Windows PowerShell

Pomocí rutiny vytvořte nové připojení pomocí prostředí Windows PowerShell `New-AzAutomationConnection` . Tato rutina má `ConnectionFieldValues` parametr, který očekává zatřiďovací tabulku definující hodnoty pro každou z vlastností definovaných typem připojení.

Následující příklady příkazů můžete použít jako alternativu k vytvoření účtu Spustit jako z portálu pro vytvoření nového prostředku připojení.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Když vytváříte účet Automation, ve výchozím nastavení zahrnuje několik globálních modulů společně s typem připojení `AzureServicePrincipal` pro vytvoření `AzureRunAsConnection` prostředku připojení. Pokud se pokusíte vytvořit nový prostředek připojení pro připojení ke službě nebo aplikaci s jinou metodou ověřování, operace se nezdařila, protože typ připojení ještě není ve vašem účtu Automation definován. Další informace o vytvoření vlastního typu připojení pro vlastní modul najdete v tématu [Přidání typu připojení](#add-a-connection-type).

## <a name="add-a-connection-type"></a>Přidat typ připojení

Pokud se vaše sada Runbook nebo konfigurace DSC připojuje k externí službě, musíte definovat typ připojení ve [vlastním modulu](shared-resources/modules.md#custom-modules) s názvem Integration Module. Tento modul obsahuje soubor metadat, který určuje vlastnosti typu připojení a má název **&lt; Module &gt;-Automation.json**, umístěný ve složce modulu komprimovaného souboru **zip** . Tento soubor obsahuje pole připojení, která jsou nutná pro připojení k systému nebo službě, kterou modul představuje. Pomocí tohoto souboru můžete pro typ připojení nastavit názvy polí, typy dat, stav šifrování a volitelný stav. 

V následujícím příkladu je šablona ve formátu souboru **. JSON** , která definuje vlastnosti uživatelského jména a hesla pro vlastní typ připojení s názvem `MyModuleConnection` :

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="get-a-connection-in-a-runbook-or-dsc-configuration"></a>Získání připojení v sadě Runbook nebo konfiguraci DSC

Načtěte připojení v sadě Runbook nebo konfiguraci DSC pomocí interní `Get-AutomationConnection` rutiny. Tato rutina je preferována prostřednictvím `Get-AzAutomationConnection` rutiny, protože načítá hodnoty připojení místo informací o připojení.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Následující příklad ukazuje, jak použít účet Spustit jako k ověření pomocí Azure Resource Manager prostředků v Runbooku. Používá prostředek připojení představující účet Spustit jako, který odkazuje na objekt služby založený na certifikátu.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

# <a name="python"></a>[Python](#tab/python2)

Následující příklad ukazuje, jak provést ověření pomocí připojení spustit jako v sadě Runbook Python 2 a 3.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
    """ Returns credentials to authenticate against Azure resource manager """
    from OpenSSL import crypto
    from msrestazure import azure_active_directory
    import adal

    # Get the Azure Automation Run As service principal certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    pks12_cert = crypto.load_pkcs12(cert)
    pem_pkey = crypto.dump_privatekey(
        crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

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
runas_connection = automationassets.get_automation_connection(
    "AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

---

### <a name="graphical-runbook-examples"></a>Příklady grafického Runbooku

Do grafického Runbooku můžete přidat aktivitu pro interní `Get-AutomationConnection` rutinu. Klikněte pravým tlačítkem na připojení v podokně Knihovna v grafickém editoru a vyberte **Přidat na plátno**.

![Přidat na plátno](media/automation-connections/connection-add-canvas.png)

Následující obrázek ukazuje příklad použití objektu připojení v grafickém Runbooku. Tento příklad používá `Constant value` datovou sadu pro `Get RunAs Connection` aktivitu, která používá objekt připojení pro ověřování. [Odkaz na kanál](automation-graphical-authoring-intro.md#use-links-for-workflow) se tady používá, protože `ServicePrincipalCertificate` sada parametrů očekává jeden objekt.

![získat připojení](media/automation-connections/automation-get-connection-object.png)

## <a name="next-steps"></a>Další kroky

* Další informace o rutinách používaných pro přístup k připojení najdete v tématu [Správa modulů v Azure Automation](shared-resources/modules.md).
* Obecné informace o sadách Runbook naleznete [v tématu Spuštění Runbooku v Azure Automation](automation-runbook-execution.md).
* Podrobnosti o konfiguracích DSC najdete v tématu [Přehled konfigurace stavu](automation-dsc-overview.md).
