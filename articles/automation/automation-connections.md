---
title: Správa připojení v Azure Automation
description: Prostředky připojení v Azure Automation obsahují informace potřebné pro připojení k externí službě nebo aplikaci z runbooku nebo konfigurace DSC. Tento článek vysvětluje podrobnosti o připojení a jak s nimi pracovat v textové i grafické authoring.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: 90d4ec1bbfd0d76ffedf1505c9147376e3947c3c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729055"
---
# <a name="manage-connections-in-azure-automation"></a>Správa připojení v Azure Automation

Prostředek připojení automatizace obsahuje informace potřebné pro připojení k externí službě nebo aplikaci z konfigurace runbooku nebo DSC. To může zahrnovat informace potřebné pro ověřování, jako je uživatelské jméno a heslo kromě informací o připojení, jako je například adresa URL nebo port. Hodnota připojení je zachování všech vlastností pro připojení k určité aplikaci v jednom prostředku na rozdíl od vytváření více proměnných. Uživatel může upravit hodnoty pro připojení na jednom místě a můžete předat název připojení do runbooku nebo Konfigurace DSC v jednom parametru. Vlastnosti pro připojení lze přistupovat v konfiguraci runbook `Get-AutomationConnection` nebo DSC s aktivitou.

Při vytváření připojení je nutné zadat typ připojení. Typ připojení je šablona, která definuje sadu vlastností. Připojení definuje hodnoty pro každou vlastnost definovanou v jeho typu připojení. Typy připojení se přidají do Azure Automation v integračních modulech nebo vytvořené pomocí [rozhraní AZURE Automation API,](/previous-versions/azure/reference/mt163818(v=azure.100)) pokud integrační modul obsahuje typ připojení a importuje se do vašeho účtu Automation. V opačném případě je nutné vytvořit soubor metadat k určení typu připojení automatizace. Další informace naleznete v [tématu Integration Modules](automation-integration-modules.md).

>[!NOTE]
>Zabezpečené datové zdroje v Azure Automation zahrnují přihlašovací údaje, certifikáty, připojení a šifrované proměnné. Tyto prostředky jsou šifrované a uložené v Azure Automation pomocí jedinečného klíče, který se generuje pro každý účet automatizace. Tento klíč je uložen v systémovém trezoru klíčů. Před uložením zabezpečeného datového zdroje je klíč načten z trezoru klíčů a poté použit k šifrování datového zdroje. Tento proces spravuje Azure Automation.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="connection-types"></a>Typy připojení

V Azure Automation jsou k dispozici tři typy integrovaných připojení:

* **Azure** – toto připojení lze použít ke správě klasických prostředků.
* **AzureClassicCertificate** – toto připojení se používá účet **AzureClassicRunAs.**
* **AzureServicePrincipal** – toto připojení se používá účet **AzureRunAs.**

Ve většině případů není nutné vytvořit prostředek připojení, protože je vytvořen při vytváření [účtu Spustit jako](manage-runas-account.md).

## <a name="windows-powershell-cmdlets"></a>Rutiny prostředí Windows PowerShell

Rutiny v následující tabulce se používají k vytvoření a správě připojení automatizace pomocí prostředí Windows PowerShell. Dodávají se jako součást [modulu Azure PowerShell](/powershell/azure/overview), který je k dispozici pro použití v runbookech automatizace a konfiguracích DSC.

|Rutina|Popis|
|---|---|
|[Get-AzAutomationPřipojení](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|Načte připojení. Obsahuje hodnotu hashtable s hodnotami polí připojení.|
|[Nové připojení AzAutomation](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|Vytvoří nové připojení.|
|[Odebrat připojení AzAutomationConnection](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|Odebere existující připojení.|
|[Hodnota pole Set-AzAutomationConnectionFieldValue](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|Nastaví hodnotu konkrétního pole pro existující připojení.|

## <a name="activities"></a>Aktivity

Aktivity v následující tabulce se používají pro přístup k připojení mj.

|Aktivity|Popis|
|---|---|
|`Get-AutomationConnection` | Získá připojení k použití. Vrátí hodnotitelnou hodnotu hashs vlastností připojení.|

>[!NOTE]
>Nepoužívejte proměnné s `Name` parametrem `Get-AutomationConnection`. Použití tohoto parametru může zkomplikovat zjišťování závislostí mezi runbooky nebo konfiguracemi DSC a prostředky připojení v době návrhu.

## <a name="python-2-functions"></a>Funkce Pythonu 2

Funkce v následující tabulce se používá pro přístup k připojení mů e pythonu 2 runbook.

| Funkce | Popis |
|:---|:---|
| `automationassets.get_automation_connection` | Načte připojení. Vrátí slovník s vlastnostmi připojení. |

> [!NOTE]
> Chcete-li `automationassets` získat přístup k funkcím datových zdrojů, musíte importovat modul v horní části sady Runbook pythonu.

## <a name="creating-a-new-connection"></a>Vytvoření nového připojení

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Vytvoření nového připojení k portálu Azure

1. Z účtu automatizace kliknutím na část **Prostředky** otevřete okno **Prostředky.**
2. Klepnutím na část **Připojení** otevřete okno **Connections.**
3. Klikněte na **Přidat připojení** v horní části okna.
4. V rozevíracím souboru **Typ** vyberte typ připojení, které chcete vytvořit. Formulář zobrazí vlastnosti pro tento konkrétní typ.
5. Vyplňte formulář a kliknutím na **Vytvořit** uložte nové připojení.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Vytvoření nového připojení pomocí prostředí Windows PowerShell

Vytvořte nové připojení s prostředím Windows PowerShell pomocí rutiny. `New-AzAutomationConnection` Tato rutina má pojmenovaný `ConnectionFieldValues` parametr, který očekává [hodnotu hashtable](https://technet.microsoft.com/library/hh847780.aspx) definující hodnoty pro každou z vlastností definovaných typem připojení.

Následující ukázkové příkazy můžete použít jako alternativu k vytvoření účtu Spustit jako z portálu k vytvoření nového datového zdroje připojení.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Skript můžete použít k vytvoření datového zdroje připojení, protože při vytváření účtu Automation automaticky obsahuje několik `AzureServicePrincipal` globálních `AzureRunAsConnection` modulů ve výchozím nastavení spolu s typem připojení k vytvoření datového zdroje připojení. To je důležité mít na paměti, protože pokud se pokusíte vytvořit nový prostředek připojení pro připojení ke službě nebo aplikaci s jinou metodou ověřování, nezdaří se, protože typ připojení není již definován ve vašem účtu automation. Další informace o tom, jak vytvořit vlastní typ připojení pro vlastní nebo modul z [Galerie prostředí PowerShell](https://www.powershellgallery.com), naleznete [v tématu Integration Modules](automation-integration-modules.md).

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Použití připojení v konfiguraci runbooku nebo DSC

Načtení připojení v konfiguraci runbook nebo `Get-AutomationConnection` DSC s rutinou. Aktivitu `Get-AzAutomationConnection` nelze použít. Tato aktivita načte hodnoty různých polí v připojení a vrátí je jako [hodnotitelné](https://go.microsoft.com/fwlink/?LinkID=324844). Tuto hashtable lze pak použít s příslušnými příkazy v konfiguraci runbook nebo DSC.

### <a name="textual-runbook-sample"></a>Ukázka textového seznamu runbooků

Následující ukázkové příkazy ukazují, jak použít dříve uvedený účet Spustit jako k ověření pomocí prostředků Azure Resource Manager u sady Runbook. Používá prostředek připojení představující účet Spustit jako, který odkazuje na instanční objekt založený na certifikátu, nikoli na pověření.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!NOTE]
> Pro negrafické sady Runbook `Add-AzAccount` `Add-AzureRMAccount` prostředí PowerShell a jsou aliasy pro [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Můžete použít tyto rutiny nebo můžete [aktualizovat moduly](automation-update-azure-modules.md) v účtu Automation na nejnovější verze. Možná budete muset aktualizovat moduly, i když jste právě vytvořili nový účet Automatizace.

### <a name="graphical-runbook-samples"></a>Grafické ukázky runbooků

Aktivitu `Get-AutomationConnection` přidáte do grafického programu Runbook kliknutím pravým tlačítkem myši na připojení v podokně Knihovna grafického editoru a výběrem **možnosti Přidat na plátno**.

![přidat na plátno](media/automation-connections/connection-add-canvas.png)

Následující obrázek ukazuje příklad použití připojení v grafické matné knize runbook. Toto je stejný příklad, jak je uvedeno výše pro ověřování pomocí účtu Spustit jako s textovým runbookem. Tento příklad `Constant value` používá sadu `Get RunAs Connection` dat pro aktivitu, která používá objekt připojení pro ověřování. Propojení [kanálu](automation-graphical-authoring-intro.md#links-and-workflow) se zde `ServicePrincipalCertificate` používá, protože sada parametrů očekává jeden objekt.

![získání připojení](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-sample"></a>Ukázka runbooku pythonu 2

Následující ukázka ukazuje, jak ověřit pomocí připojení Spustit jako v pythonu 2 runbook.

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
* Odkaz na rutinu prostředí PowerShell naleznete v tématu [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Další informace o používání modulů PowerShellu azure automation a osvědčených postupech pro vytváření vlastních modulů PowerShellu pro práci jako integrační moduly v rámci Azure Automation najdete v [tématu Integration Modules](automation-integration-modules.md).