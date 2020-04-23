---
title: Správa připojení v Azure Automation
description: Prostředky připojení v Azure Automation obsahují informace potřebné pro připojení k externí službě nebo aplikaci z Runbooku nebo konfigurace DSC. Tento článek vysvětluje podrobnosti o připojeních a o tom, jak s nimi pracovat v textovém i grafickém vytváření.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: 39a41a60f4cabe995ebd458c4b906438d1e31bde
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82097111"
---
# <a name="manage-connections-in-azure-automation"></a>Správa připojení v Azure Automation

Asset připojení Automation obsahuje informace potřebné pro připojení k externí službě nebo aplikaci z Runbooku nebo konfigurace DSC. Kromě informací o připojení, jako je například adresa URL nebo port, může obsahovat i informace vyžadované pro ověřování, jako je uživatelské jméno a heslo. Hodnota připojení udržuje všechny vlastnosti pro připojení k určité aplikaci v jednom prostředku, a to na rozdíl od vytvoření více proměnných. Uživatel může upravit hodnoty připojení na jednom místě a název připojení k sadě Runbook nebo konfiguraci DSC můžete předat v jednom parametru. K vlastnostem připojení lze v sadě Runbook nebo v konfiguraci DSC přidružit `Get-AutomationConnection` aktivitu.

Když vytvoříte připojení, musíte zadat typ připojení. Typ připojení je šablona, která definuje sadu vlastností. Připojení definuje hodnoty pro každou vlastnost definovanou v jejím typu připojení. Typy připojení se přidají do Azure Automation v integračních modulech nebo vytvořené pomocí [rozhraní API Azure Automation](/previous-versions/azure/reference/mt163818(v=azure.100)) , pokud modul Integration Module zahrnuje typ připojení a importuje ho do svého účtu Automation. V opačném případě je nutné vytvořit soubor metadat k určení typu připojení Automation. Další informace o tomto tématu najdete v tématu věnovaném [modulům Integration](automation-integration-modules.md).

>[!NOTE]
>Zabezpečené prostředky v Azure Automation zahrnují přihlašovací údaje, certifikáty, připojení a šifrované proměnné. Tyto prostředky jsou zašifrované a uložené v Azure Automation pomocí jedinečného klíče, který se generuje pro každý účet Automation. Tento klíč je uložený v Key Vault spravovaném systémem. Před uložením zabezpečeného prostředku se klíč načte z Key Vault a pak se použije k zašifrování assetu. Tento proces je spravovaný pomocí Azure Automation.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](automation-update-azure-modules.md).

## <a name="connection-types"></a>Typy připojení

Existují tři typy integrovaných připojení, která jsou k dispozici v Azure Automation:

* **Azure** – toto připojení se dá použít ke správě klasických prostředků.
* **AzureClassicCertificate** – toto připojení používá účet **AzureClassicRunAs** .
* **AzureServicePrincipal** – toto připojení používá účet **AzureRunAs** .

Ve většině případů nemusíte vytvářet prostředek připojení, protože se vytvoří při vytváření [účtu Spustit jako](manage-runas-account.md).

## <a name="windows-powershell-cmdlets"></a>Rutiny prostředí Windows PowerShell

Rutiny v následující tabulce se používají k vytváření a správě připojení automatizace pomocí Windows PowerShellu. Dodávají se jako součást [modulu Azure PowerShell](/powershell/azure/overview), který je k dispozici pro použití v sadách Automation a konfiguracích DSC.

|Rutina|Popis|
|---|---|
|[Get-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|Načte připojení. Obsahuje zatřiďovací tabulku s hodnotami polí připojení.|
|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|Vytvoří nové připojení.|
|[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|Odebere existující připojení.|
|[Set-AzAutomationConnectionFieldValue](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|Nastaví hodnotu konkrétního pole pro existující připojení.|

## <a name="activities"></a>Aktivity

Aktivity v následující tabulce se používají pro přístup k připojením v sadě Runbook nebo konfiguraci DSC.

|Aktivity|Popis|
|---|---|
|`Get-AutomationConnection` | Načte připojení, které se má použít. Vrátí tabulku hash s vlastnostmi připojení.|

>[!NOTE]
>Vyhněte se použití proměnných `Name` s parametrem `Get-AutomationConnection`. Použití tohoto parametru může zkomplikovat zjišťování závislostí mezi sadami Runbook a konfigurací DSC a assety připojení v době návrhu.

## <a name="python-2-functions"></a>Python 2 – funkce

Funkce v následující tabulce slouží k přístupu k připojením v sadě Runbook Python 2.

| Funkce | Popis |
|:---|:---|
| `automationassets.get_automation_connection` | Načte připojení. Vrátí slovník s vlastnostmi připojení. |

> [!NOTE]
> Aby bylo možné získat `automationassets` přístup k funkcím assetu, musíte importovat modul v horní části Runbooku sady Python.

## <a name="creating-a-new-connection"></a>Vytváří se nové připojení.

### <a name="create-a-new-connection-with-the-azure-portal"></a>Vytvoření nového připojení pomocí Azure Portal

1. V účtu Automation kliknutím na část **aktiva** otevřete okno **assety** .
2. Kliknutím na část **připojení** otevřete okno **připojení** .
3. V horní části okna klikněte na **Přidat připojení** .
4. V rozevíracím seznamu **typ** vyberte typ připojení, které chcete vytvořit. Ve formuláři se zobrazí vlastnosti daného konkrétního typu.
5. Vyplňte formulář a kliknutím na **vytvořit** uložte nové připojení.

### <a name="create-a-new-connection-with-windows-powershell"></a>Vytvoření nového připojení pomocí prostředí Windows PowerShell

Pomocí `New-AzAutomationConnection` rutiny vytvořte nové připojení pomocí prostředí Windows PowerShell. Tato rutina má parametr s `ConnectionFieldValues` názvem, který očekává [zatřiďovací tabulku](https://technet.microsoft.com/library/hh847780.aspx) definující hodnoty pro každou z vlastností definovaných typem připojení.

Následující příklady příkazů můžete použít jako alternativu k vytvoření účtu Spustit jako z portálu pro vytvoření nového prostředku připojení.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Když vytváříte účet Automation, ve výchozím nastavení zahrnuje několik globálních modulů společně s typem `AzureServicePrincipal` připojení pro vytvoření prostředku `AzureRunAsConnection` připojení. Pokud se pokusíte vytvořit nový prostředek připojení pro připojení ke službě nebo aplikaci s jinou metodou ověřování, operace se nezdařila, protože typ připojení ještě není ve vašem účtu Automation definován. Další informace o vytvoření vlastního typu připojení pro vlastní modul [Galerie prostředí PowerShell](https://www.powershellgallery.com) najdete v tématu věnovaném [modulům Integration](automation-integration-modules.md).

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Použití připojení v sadě Runbook nebo konfiguraci DSC

Načtěte připojení v sadě Runbook nebo konfiguraci DSC pomocí `Get-AutomationConnection` rutiny. `Get-AzAutomationConnection` Aktivitu nelze použít. Tato aktivita načte hodnoty různých polí v připojení a vrátí je jako [zatřiďovací tabulku](https://go.microsoft.com/fwlink/?LinkID=324844). Tato zatřiďovací tabulka se pak dá použít s příslušnými příkazy v sadě Runbook nebo konfiguraci DSC.

### <a name="textual-runbook-sample"></a>Ukázka textové sady Runbook

Následující vzorové příkazy ukazují, jak používat účet Spustit jako zmíněný dříve, k ověření pomocí Azure Resource Manager prostředků v Runbooku. Používá prostředek připojení představující účet Spustit jako, který odkazuje na instanční objekt založený na certifikátech, nikoli na přihlašovací údaje.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!NOTE]
> Pro negrafické Runbooky PowerShellu `Add-AzAccount` a `Add-AzureRMAccount` jsou aliasy pro [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Tyto rutiny můžete použít nebo můžete [své moduly](automation-update-azure-modules.md) v účtu Automation aktualizovat na nejnovější verze. Vaše moduly možná budete muset aktualizovat i v případě, že jste právě vytvořili nový účet Automation.

### <a name="graphical-runbook-samples"></a>Ukázky grafického Runbooku

`Get-AutomationConnection` Aktivitu přidáte do grafického Runbooku kliknutím pravým tlačítkem myši na připojení v podokně Knihovna v grafickém editoru a vybráním možnosti **Přidat na plátno**.

![Přidat na plátno](media/automation-connections/connection-add-canvas.png)

Následující obrázek ukazuje příklad použití připojení v grafickém Runbooku. Toto je stejný příklad, jak je uvedený výše pro ověřování pomocí účtu Spustit jako s textovým Runbooku. Tento příklad používá `Constant value` datovou sadu pro `Get RunAs Connection` aktivitu, která pro ověřování používá objekt připojení. [Odkaz na kanál](automation-graphical-authoring-intro.md#links-and-workflow) se tady používá, protože `ServicePrincipalCertificate` sada parametrů očekává jeden objekt.

![získat připojení](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-sample"></a>Ukázka sady Runbook Python 2

Následující příklad ukazuje, jak ověřit pomocí připojení spustit jako v sadě Runbook Python 2.

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

## <a name="next-steps"></a>Další kroky

- Projděte si [odkazy v tématu vytváření grafického obsahu](automation-graphical-authoring-intro.md#links-and-workflow) , abyste pochopili, jak směrovat a řídit tok logiky ve vašich sadách Runbook.
* Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Další informace Azure Automation o používání modulů PowerShellu a osvědčených postupů pro vytváření vlastních modulů PowerShellu pro práci jako integračních modulů v Azure Automation najdete v tématu věnovaném [modulům Integration](automation-integration-modules.md).