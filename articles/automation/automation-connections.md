---
title: Prostředky připojení v Azure Automation
description: Prostředky připojení v Azure Automation obsahují informace potřebné pro připojení k externí službě nebo aplikaci z Runbooku nebo konfigurace DSC. Tento článek vysvětluje podrobnosti o připojeních a o tom, jak s nimi pracovat v textovém i grafickém vytváření.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 01/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 90b2234607ad120c43e241fe4ae5222fe285803e
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001662"
---
# <a name="connection-assets-in-azure-automation"></a>Prostředky připojení v Azure Automation

Asset připojení Automation obsahuje informace potřebné pro připojení k externí službě nebo aplikaci z Runbooku nebo konfigurace DSC. Kromě informací o připojení, jako je například adresa URL nebo port, může obsahovat i informace vyžadované pro ověřování, jako je uživatelské jméno a heslo. Hodnota připojení udržuje všechny vlastnosti pro připojení k určité aplikaci v jednom prostředku, a to na rozdíl od vytvoření více proměnných. Uživatel může upravit hodnoty připojení na jednom místě a název připojení k sadě Runbook nebo konfiguraci DSC můžete předat v jednom parametru. K vlastnostem připojení se dá v sadě Runbook nebo v konfiguraci DSC získat přístup pomocí aktivity **Get-AutomationConnection** .

Když vytvoříte připojení, musíte zadat *Typ připojení*. Typ připojení je šablona, která definuje sadu vlastností. Připojení definuje hodnoty pro každou vlastnost definovanou v jejím typu připojení. Typy připojení se přidají do Azure Automation v integračních modulech nebo vytvořené pomocí [rozhraní API Azure Automation](/previous-versions/azure/reference/mt163818(v=azure.100)) , pokud modul Integration Module zahrnuje typ připojení a importuje ho do svého účtu Automation. V opačném případě budete muset vytvořit soubor metadat a zadat typ připojení Automation.  Další informace týkající se tohoto najdete v tématu věnovaném [modulům Integration](automation-integration-modules.md).

>[!NOTE]
>Zabezpečené prostředky v Azure Automation zahrnují přihlašovací údaje, certifikáty, připojení a šifrované proměnné. Tyto prostředky jsou zašifrované a uložené v Azure Automation pomocí jedinečného klíče, který se generuje pro každý účet Automation. Tento klíč je uložený v Key Vault spravovaném systémem. Před uložením zabezpečeného prostředku se klíč načte z Key Vault a pak se použije k zašifrování assetu. Tento proces je spravovaný pomocí Azure Automation.

## <a name="connection-types"></a>Typy připojení

Existují tři typy integrovaných připojení, které jsou k dispozici v Azure Automation:

* **Azure** – toto připojení se dá použít ke správě klasických prostředků.
* **AzureClassicCertificate** – toto připojení používá účet **AzureClassicRunAs** .
* **AzureServicePrincipal** – toto připojení používá účet **AzureRunAs** .

Ve většině případů nemusíte při vytváření [účtu runas](manage-runas-account.md)vytvořit prostředek připojení, který se vytvoří.

## <a name="windows-powershell-cmdlets"></a>Rutiny prostředí Windows PowerShell

Rutiny v následující tabulce se používají k vytváření a správě připojení automatizace pomocí Windows PowerShellu. Dodávají se jako součást [modulu Azure PowerShell](/powershell/azure/overview) , který je k dispozici pro použití v sadách Automation a konfiguracích DSC.

|Rutiny|Popis|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|Načte připojení. Obsahuje zatřiďovací tabulku s hodnotami polí připojení.|
|[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Vytvoří nové připojení.|
|[Remove-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Odebere existující připojení.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Nastaví hodnotu konkrétního pole pro existující připojení.|

## <a name="activities"></a>Aktivity

Aktivity v následující tabulce se používají pro přístup k připojením v sadě Runbook nebo konfiguraci DSC.

|Aktivity|Popis|
|---|---|
|[Get-AutomationConnection](/powershell/module/servicemanagement/azure/get-azureautomationconnection?view=azuresmps-3.7.0)|Načte připojení, které se má použít. Vrátí tabulku hash s vlastnostmi připojení.|

>[!NOTE]
>Měli byste se vyhnout použití proměnných s parametrem – Name v **Get-AutomationConnection** , protože to může zkomplikovat zjišťování závislostí mezi sadami Runbook a konfigurací DSC a assety připojení v době návrhu.


## <a name="python2-functions"></a>Funkce Python2
Funkce v následující tabulce slouží k přístupu k připojením v Runbooku Python2.

| Funkce | Popis |
|:---|:---|
| automationassets. Get _automation_connection | Načte připojení. Vrátí slovník s vlastnostmi připojení. |

> [!NOTE]
> Aby bylo možné získat přístup k funkcím assetu, musíte naimportovat modul "automationassets" v horní části Runbooku sady Python.

## <a name="creating-a-new-connection"></a>Vytváří se nové připojení.

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Vytvoření nového připojení pomocí Azure Portal

1. V účtu Automation kliknutím na část **aktiva** otevřete okno **assety** .
2. Kliknutím na část **připojení** otevřete okno **připojení** .
3. V horní části okna klikněte na **Přidat připojení** .
4. V rozevíracím seznamu **typ** vyberte typ připojení, které chcete vytvořit. Ve formuláři se zobrazí vlastnosti daného konkrétního typu.
5. Vyplňte formulář a kliknutím na **vytvořit** uložte nové připojení.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Vytvoření nového připojení pomocí prostředí Windows PowerShell

Pomocí rutiny [New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection) vytvořte nové připojení pomocí prostředí Windows PowerShell. Tato rutina má parametr s názvem **ConnectionFieldValues** , který očekává [zatřiďovací tabulku](https://technet.microsoft.com/library/hh847780.aspx) definující hodnoty pro každou z vlastností definovaných typem připojení.

Pokud znáte účet služby Automation [Spustit jako](automation-sec-configure-azure-runas-account.md) , abyste mohli ověřovat Runbooky pomocí instančního objektu, skript PowerShellu, který je k dispozici jako alternativa k vytvoření účtu Spustit jako z portálu, vytvoří nový prostředek připojení pomocí Následující ukázkové příkazy.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Pomocí skriptu můžete vytvořit prostředek připojení, protože když vytvoříte účet Automation, automaticky se ve výchozím nastavení přidá několik globálních modulů spolu s typem připojení **AzureServicePrincipal** a vytvoří se **.** Prostředek připojení AzureRunAsConnection  Je důležité mít na paměti, protože pokud se pokusíte vytvořit nový prostředek připojení pro připojení ke službě nebo aplikaci s jinou metodou ověřování, nezdaří se, protože typ připojení ještě není ve vašem účtu Automation definovaný.  Další informace o tom, jak vytvořit vlastní typ připojení pro vlastní nebo modul z [Galerie prostředí PowerShell](https://www.powershellgallery.com), najdete v tématu věnovaném [modulům Integration](automation-integration-modules.md) .

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Použití připojení v sadě Runbook nebo konfiguraci DSC

Připojení můžete načítat v sadě Runbook nebo konfiguraci DSC pomocí rutiny **Get-AutomationConnection** .  Nemůžete použít aktivitu [Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection) .  Tato aktivita načte hodnoty různých polí v připojení a vrátí je jako [zatřiďovací tabulku](https://go.microsoft.com/fwlink/?LinkID=324844) , která se pak dá použít s příslušnými příkazy v sadě Runbook nebo konfiguraci DSC.

### <a name="textual-runbook-sample"></a>Ukázka textové sady Runbook

Následující vzorové příkazy ukazují, jak používat účet Spustit jako zmíněný dříve, k ověření pomocí Azure Resource Manager prostředků v Runbooku.  Používá prostředek připojení představující účet Spustit jako, který odkazuje na instanční objekt založený na certifikátech, nikoli na přihlašovací údaje.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!IMPORTANT]
> **Add-AzureRmAccount** je teď alias pro **Connect-AzureRmAccount**. Pokud se při hledání položek knihovny nezobrazí **příkaz Connect-AzureRMAccount**, můžete použít příkaz **Add-AzureRMAccount**nebo můžete aktualizovat moduly v účtu Automation.

### <a name="graphical-runbook-samples"></a>Ukázky grafického Runbooku

Aktivitu **Get-AutomationConnection** přidáte do grafického Runbooku kliknutím pravým tlačítkem myši na připojení v podokně Knihovna v grafickém editoru a vybráním možnosti **Přidat na plátno**.

![Přidat na plátno](media/automation-connections/connection-add-canvas.png)

Následující obrázek ukazuje příklad použití připojení v grafickém Runbooku.  Toto je stejný příklad uvedený výše pro ověřování pomocí účtu Spustit jako s textovým Runbooku.  Tento příklad používá datovou sadu s **konstantní hodnotou** pro aktivitu **získat připojení runas** , která pro ověřování používá objekt připojení.  [Odkaz na kanál](automation-graphical-authoring-intro.md#links-and-workflow) se tady používá, protože sada parametrů ServicePrincipalCertificate očekává jeden objekt.

![získat připojení](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Ukázka Runbooku Python2
Následující příklad ukazuje, jak ověřit pomocí připojení spustit jako v Runbooku Python2.

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

- Další informace Azure Automation o používání modulů PowerShellu a osvědčených postupů pro vytváření vlastních modulů PowerShellu pro práci jako integračních modulů v Azure Automation najdete v tématu věnovaném [modulům Integration](automation-integration-modules.md).

