---
title: Prostředky připojení ve službě Azure Automation
description: Prostředky připojení v Azure Automation obsahují informace potřebné pro připojení k externí službě nebo aplikaci z runbooku nebo konfigurace DSC. Tento článek vysvětluje podrobnosti o připojení a jak s nimi pracovat v textové i grafické authoring.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: b6276153921feb0e6f27194d36d1c32c1d0ffb3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75940820"
---
# <a name="connection-assets-in-azure-automation"></a>Prostředky připojení ve službě Azure Automation

Prostředek připojení automatizace obsahuje informace potřebné pro připojení k externí službě nebo aplikaci z konfigurace runbooku nebo DSC. To může zahrnovat informace potřebné pro ověřování, jako je uživatelské jméno a heslo kromě informací o připojení, jako je například adresa URL nebo port. Hodnota připojení je zachování všech vlastností pro připojení k určité aplikaci v jednom prostředku na rozdíl od vytváření více proměnných. Uživatel může upravit hodnoty pro připojení na jednom místě a můžete předat název připojení do runbooku nebo Konfigurace DSC v jednom parametru. Vlastnosti pro připojení lze přistupovat v konfiguraci runbook nebo DSC s **Get-AutomationConnection** aktivity.

Při vytváření připojení je nutné zadat *typ připojení*. Typ připojení je šablona, která definuje sadu vlastností. Připojení definuje hodnoty pro každou vlastnost definovanou v jeho typu připojení. Typy připojení se přidají do Azure Automation v integračních modulech nebo vytvořené pomocí [rozhraní AZURE Automation API,](/previous-versions/azure/reference/mt163818(v=azure.100)) pokud integrační modul obsahuje typ připojení a importuje se do vašeho účtu Automation. V opačném případě budete muset vytvořit soubor metadat pro určení typu připojení automatizace. Další informace naleznete v [tématu Integration Modules](automation-integration-modules.md).

>[!NOTE]
>Zabezpečené datové zdroje v Azure Automation zahrnují přihlašovací údaje, certifikáty, připojení a šifrované proměnné. Tyto prostředky jsou šifrované a uložené v Azure Automation pomocí jedinečného klíče, který se generuje pro každý účet automatizace. Tento klíč je uložen v systémovém trezoru klíčů. Před uložením zabezpečeného datového zdroje je klíč načten z trezoru klíčů a poté použit k šifrování datového zdroje. Tento proces spravuje Azure Automation.

## <a name="connection-types"></a>Typy připojení

V Azure Automation jsou k dispozici tři typy integrovaných připojení:

* **Azure** – toto připojení lze použít ke správě klasických prostředků.
* **AzureClassicCertificate** – toto připojení se používá účet **AzureClassicRunAs.**
* **AzureServicePrincipal** – toto připojení se používá účet **AzureRunAs.**

Ve většině případů není nutné vytvořit prostředek připojení, protože je vytvořen při vytváření [účtu RunAs](manage-runas-account.md).

## <a name="windows-powershell-cmdlets"></a>Rutiny prostředí Windows PowerShell

Rutiny v následující tabulce se používají k vytvoření a správě připojení automatizace pomocí prostředí Windows PowerShell. Dodávají se jako součást [modulu Azure PowerShell](/powershell/azure/overview), který je k dispozici pro použití v runbookech automatizace a konfiguracích DSC.

|Rutina|Popis|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|Načte připojení. Obsahuje tabulku hash s hodnotami polí připojení.|
|[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Vytvoří nové připojení.|
|[Remove-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Odebere existující připojení.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Nastaví hodnotu konkrétního pole pro existující připojení.|

## <a name="activities"></a>Aktivity

Aktivity v následující tabulce se používají pro přístup k připojení mj.

|Aktivity|Popis|
|---|---|
|Get-AutomationConnection | Získá připojení k použití. Vrátí tabulku hash s vlastnostmi připojení.|

>[!NOTE]
>Měli byste se vyhnout použití proměnných s parametrem –Name **get-AutomationConnection,** protože to může zkomplikovat zjišťování závislostí mezi runbooky nebo konfiguracemi DSC a prostředky připojení v době návrhu.


## <a name="python2-functions"></a>Funkce Pythonu2

Funkce v následující tabulce se používá pro přístup k připojením v runbooku Python2.

| Funkce | Popis |
|:---|:---|
| automationassets.get_automation_connection | Načte připojení. Vrátí slovník s vlastnostmi připojení. |

> [!NOTE]
> Chcete-li získat přístup k funkcím datového zdroje, musíte importovat modul "automationassets" v horní části sady Runbook pythonu.

## <a name="creating-a-new-connection"></a>Vytvoření nového připojení

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Vytvoření nového připojení k portálu Azure

1. Z účtu automatizace kliknutím na část **Prostředky** otevřete okno **Prostředky.**
2. Klepnutím na část **Připojení** otevřete okno **Connections.**
3. Klikněte na **Přidat připojení** v horní části okna.
4. V rozevíracím souboru **Typ** vyberte typ připojení, které chcete vytvořit. Formulář zobrazí vlastnosti pro tento konkrétní typ.
5. Vyplňte formulář a kliknutím na **Vytvořit** uložte nové připojení.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Vytvoření nového připojení pomocí prostředí Windows PowerShell

Vytvořte nové připojení s prostředím Windows PowerShell pomocí rutiny [New-AzureRmAutomationConnection.](/powershell/module/azurerm.automation/new-azurermautomationconnection) Tato rutina má parametr s názvem **ConnectionFieldValues,** který očekává, že [tabulka hash](https://technet.microsoft.com/library/hh847780.aspx) definuje hodnoty pro každou z vlastností definovaných typem připojení.

Pokud jste obeznámeni s [účtem](automation-sec-configure-azure-runas-account.md) Automat spustit jako k ověření sady Runbook pomocí instančního objektu, skript prostředí PowerShell, který je k dispozici jako alternativa k vytvoření účtu Spustit jako z portálu, vytvoří nový prostředek připojení pomocí následujících ukázkových příkazů.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Skript můžete použít k vytvoření prostředku připojení, protože při vytváření účtu Automation automaticky obsahuje několik globálních modulů ve výchozím nastavení spolu s typem připojení **AzureServicePrincipal** k vytvoření datového zdroje připojení **AzureRunAsConnection.** To je důležité mít na paměti, protože pokud se pokusíte vytvořit nový prostředek připojení pro připojení ke službě nebo aplikaci s jinou metodou ověřování, nezdaří se, protože typ připojení není již definován ve vašem účtu automation. Další informace o tom, jak vytvořit vlastní typ připojení pro vlastní nebo modul z [Galerie prostředí PowerShell](https://www.powershellgallery.com), najdete [v tématu Integrační moduly](automation-integration-modules.md)

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Použití připojení v konfiguraci runbooku nebo DSC

Načtete připojení v konfiguraci runbook nebo DSC s rutinou **Get-AutomationConnection.** Nelze použít [Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection) aktivity. Tato aktivita načte hodnoty různých polí v připojení a vrátí je jako [tabulku hash](https://go.microsoft.com/fwlink/?LinkID=324844), kterou lze použít s příslušnými příkazy v konfiguraci runbooku nebo DSC.

### <a name="textual-runbook-sample"></a>Ukázka textového seznamu runbooků

Následující ukázkové příkazy ukazují, jak použít dříve uvedený účet Spustit jako k ověření pomocí prostředků Azure Resource Manager u sady Runbook. Používá prostředek připojení představující účet Spustit jako, který odkazuje na instanční objekt založený na certifikátu, nikoli na pověření.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!IMPORTANT]
> **Add-AzureRmAccount** je teď alias pro **Connect-AzureRMAccount**. Při hledání položek knihovny, pokud nevidíte **Connect-AzureRMAccount**, můžete použít **Add-AzureRmAccount**nebo můžete aktualizovat moduly ve vašem účtu automatizace.

### <a name="graphical-runbook-samples"></a>Grafické ukázky runbooků

Aktivitu **Get-AutomationConnection** přidáte do grafického programu runbook kliknutím pravým tlačítkem myši na připojení v podokně **Knihovna** grafického editoru a výběrem **možnosti Přidat na plátno**.

![přidat na plátno](media/automation-connections/connection-add-canvas.png)

Následující obrázek ukazuje příklad použití připojení v grafické matné knize runbook. Toto je stejný příklad, který je uveden výše pro ověřování pomocí účtu Spustit jako s textovým runbookem. Tento příklad používá **hodnotu konstantní** sada dat pro **aktivitu Získat runas připojení,** která používá objekt připojení pro ověřování. Zde se používá [propojení kanálu,](automation-graphical-authoring-intro.md#links-and-workflow) protože sada parametrů ServicePrincipalCertificate očekává jeden objekt.

![získání připojení](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Ukázka runbooku pythonu2

Následující ukázka ukazuje, jak ověřit pomocí připojení Spustit jako v runbooku Python2.

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

- Zkontrolujte [odkazy v grafickém vytváření, abyste pochopili,](automation-graphical-authoring-intro.md#links-and-workflow) jak řídit a řídit tok logiky v sadách Runbook.

- Další informace o používání modulů PowerShellu azure automation a osvědčených postupech pro vytváření vlastních modulů PowerShellu pro práci jako integrační moduly v rámci Azure Automation najdete v [tématu Integration Modules](automation-integration-modules.md).