---
title: Spouštění Runbooků v Azure Automation Hybrid Runbook Worker
description: Tento článek poskytuje informace o spouštění Runbooků v počítačích v místním datovém centru nebo poskytovateli cloudu s rolí Hybrid Runbook Worker.
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: 9b9196cde45686e42d1baf7faedf94bdb73acccc
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367053"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Spouštění Runbooků na Hybrid Runbook Worker

Sady Runbook, které cílí na Hybrid Runbook Worker obvykle spravují prostředky v místním počítači nebo na prostředky v místním prostředí, kde je pracovní proces nasazen. Runbooky v Azure Automation obvykle spravují prostředky v cloudu Azure. I když se používají jinak, Runbooky, které běží v Azure Automation a runbooky, které běží na Hybrid Runbook Worker, jsou identické ve struktuře.

Když vytváříte Runbook ke spuštění na Hybrid Runbook Worker, měli byste sadu Runbook upravit a otestovat na počítači, který je hostitelem pracovního procesu. Hostitelský počítač má všechny moduly PowerShellu a přístup k síti, které vyžaduje Správa a přístup k místním prostředkům. Po otestování Runbooku na Hybrid Runbook Worker počítači ho můžete nahrát do prostředí Azure Automation, kde ho můžete spustit na pracovním procesu. 

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](automation-update-azure-modules.md).

## <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Oprávnění sady Runbook pro Hybrid Runbook Worker

Když získávají přístup k prostředkům mimo Azure, Runbooky běžící na Hybrid Runbook Worker nemůžou používat ověřovací mechanismus, který používají Runbooky ověřování pro prostředky Azure. Sada Runbook buď poskytuje vlastní ověřování pro místní prostředky, nebo konfiguruje ověřování pomocí [spravovaných identit pro prostředky Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Můžete také zadat účet Spustit jako, který bude poskytovat kontext uživatele pro všechny sady Runbook.

### <a name="runbook-authentication"></a>Ověřování Runbooku

Ve výchozím nastavení se Runbooky spouštějí v místním počítači. Pro Windows běží v kontextu místního systémového účtu. Pro Linux běží v kontextu speciálního uživatelského účtu **nxautomation**. V obou případech musí Runbooky zadat vlastní ověřování pro prostředky, ke kterým přistupuje.

V sadě Runbook můžete použít prostředky [přihlašovacích údajů](automation-credentials.md) a [certifikátů](automation-certificates.md) s rutinami, které umožňují zadat přihlašovací údaje, aby se sada Runbook mohla ověřit u různých prostředků. Následující příklad ukazuje část sady Runbook, která restartuje počítač. Načítá přihlašovací údaje z assetu přihlašovacích údajů a název počítače z variabilního prostředku a pak tyto hodnoty používá pomocí rutiny `Restart-Computer`.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Můžete také použít aktivitu [InlineScript](automation-powershell-workflow.md#inlinescript) . `InlineScript` umožňuje spouštět bloky kódu na jiném počítači s přihlašovacími údaji určenými [společným parametrem PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="run-as-account"></a>Účet Spustit jako

Místo toho, aby sada Runbook poskytovala vlastní ověřování pro místní prostředky, můžete zadat účet Spustit jako pro skupinu Hybrid Runbook Worker. K tomu je potřeba definovat [Asset přihlašovacích údajů](automation-credentials.md) , který má přístup k místním prostředkům. Tyto prostředky zahrnují úložiště certifikátů a všechny sady Runbook běží pod těmito přihlašovacími údaji na Hybrid Runbook Worker ve skupině.

Uživatelské jméno pro přihlašovací údaje musí být v jednom z následujících formátů:

* domain\username
* username@domain
* uživatelské jméno (pro účty místní k místnímu počítači)

K určení účtu Spustit jako pro skupinu Hybrid Runbook Worker použijte následující postup.

1. Vytvořte [Asset přihlašovacích údajů](automation-credentials.md) s přístupem k místním prostředkům.
2. Otevřete účet Automation v Azure Portal.
3. Vyberte dlaždici **Hybrid Worker skupiny** a pak vyberte skupinu.
4. Vyberte **všechna nastavení**a potom **Nastavení skupiny hybridních pracovních procesů**.
5. Změňte hodnotu **Spustit jako** z **výchozí** na Custom ( **vlastní**).
6. Vyberte přihlašovací údaje a klikněte na **Uložit**.

### <a name="managed-identities-for-azure-resources"></a>Spravované identity pro prostředky Azure

Hybridní pracovní procesy Runbooku na virtuálních počítačích Azure můžou k ověřování prostředků Azure používat spravované identity pro prostředky Azure. Použití spravovaných identit pro prostředky Azure místo účtů spustit jako přináší výhody, protože nemusíte provádět tyto akce:

* Exportujte certifikát spustit jako a pak ho importujte do Hybrid Runbook Worker.
* Obnovte certifikát používaný účtem spustit jako.
* Zpracujte objekt připojení spustit jako v kódu Runbooku.

Postupujte podle dalších kroků a použijte spravovanou identitu pro prostředky Azure na Hybrid Runbook Worker.

1. Vytvořte virtuální počítač Azure.
2. Nakonfigurujte spravované identity pro prostředky Azure na virtuálním počítači. Další informace najdete v tématu [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
3. Udělte virtuálnímu počítači přístup ke skupině prostředků v Správce prostředků. Informace o [použití spravované identity přiřazené systémem Windows VM pro přístup k Správce prostředků](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
4. Nainstalujte na virtuální počítač službu Hybrid Runbook Worker. Viz [nasazení Hybrid Runbook Worker Windows](automation-windows-hrw-install.md).
5. Aktualizujte sadu Runbook tak, aby k ověření prostředků Azure použila rutinu [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) s parametrem `Identity`. Tato konfigurace omezuje nutnost použít účet Spustit jako a provede přidruženou správu účtů.

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> `Connect-AzAccount -Identity` funguje pro Hybrid Runbook Worker pomocí identity přiřazené systémem a jediné identity přiřazené uživatelem. Pokud na Hybrid Runbook Worker použijete více uživatelsky přiřazených identit, je nutné, aby sada Runbook určila parametr *accountid* pro `Connect-AzAccount` výběr konkrétní identity přiřazené uživatelem.

### <a name="runas-script"></a>Účet Spustit jako pro Automation

Jako součást procesu automatizovaného sestavování pro nasazení prostředků v Azure můžete vyžadovat přístup k místním systémům pro podporu úlohy nebo sady kroků v sekvenci nasazení. Chcete-li zajistit ověřování v Azure pomocí účtu Spustit jako, je nutné nainstalovat certifikát účtu Spustit jako.

Následující PowerShellový Runbook s názvem **Export-RunAsCertificateToHybridWorker**exportuje certifikát spustit jako z účtu Azure Automation. Sada Runbook stáhne a importuje certifikát do úložiště certifikátů místního počítače na Hybrid Runbook Worker, která je připojena ke stejnému účtu. Jakmile tento krok dokončí, sada Runbook ověří, že se pracovní proces může úspěšně ověřit v Azure pomocí účtu Spustit jako.

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>Pro Runbooky PowerShell jsou `Add-AzAccount` a `Add-AzureRMAccount` aliasy pro `Connect-AzAccount`. Pokud nevidíte `Connect-AzAccount`v položkách knihovny, můžete použít `Add-AzAccount`nebo můžete své moduly aktualizovat v účtu Automation.

Dokončení přípravy účtu Spustit jako:

1. Uložte Runbook **Export-RunAsCertificateToHybridWorker** do počítače s příponou **. ps1** .
2. Importujte ho do svého účtu Automation.
3. Upravte Runbook a změňte hodnotu `Password` proměnné o vlastním hesle. 
4. Publikujte Runbook.
5. Spusťte sadu Runbook, která cílí na Hybrid Runbook Worker skupinu, která spouští a ověřuje Runbooky pomocí účtu Spustit jako. 
6. Prohlédněte si datový proud úlohy a podívejte se, že se pokusí importovat certifikát do úložiště místního počítače a postupovat podle více řádků. Toto chování závisí na tom, kolik účtů služby Automation ve vašem předplatném definujete, a na stupni úspěšnosti ověřování.

## <a name="job-behavior-on-hybrid-runbook-workers"></a>Chování úlohy u procesů Hybrid Runbook Worker

Azure Automation zpracovává úlohy pro procesy Hybrid Runbook Workers trochu jinak než úlohy spuštěné v izolovaném prostoru Azure. Jedním z klíčových rozdílů je, že pro pracovní procesy Runbooku neexistuje žádné omezení doby trvání úloh. Sady Runbook spuštěné v Azure Sandbox jsou omezené na tři hodiny z důvodu [spravedlivého sdílení](automation-runbook-execution.md#fair-share).

Pro dlouhotrvající sadu Runbook se ujistěte, že je odolný proti možnému restartování, například v případě, že počítač, který je hostitelem restartování pracovního procesu. Pokud se Hybrid Runbook Worker hostitelský počítač restartuje, všechny spuštěné úlohy Runbooku se restartují od začátku nebo z posledního kontrolního bodu pro Runbooky pracovního postupu PowerShellu. Po restartování úlohy Runbooku je více než třikrát pozastaveno.

Pamatujte, že úlohy pro procesy Hybrid Runbook Worker běží pod účtem místní systém ve Windows nebo v účtu **nxautomation** v systému Linux. Pro Linux je nutné zajistit, aby měl účet **nxautomation** přístup k umístění, kde jsou uloženy moduly sady Runbook. Když použijete rutinu [install-Module](/powershell/module/powershellget/install-module) , nezapomeňte zadat AllUsers pro parametr `Scope`, aby se zajistilo, že má účet **nxautomation** přístup. Další informace o PowerShellu v systému Linux najdete v tématu [známé problémy pro prostředí PowerShell na platformách jiných než Windows](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms).

## <a name="starting-a-runbook-on-a-hybrid-runbook-worker"></a>Spuštění Runbooku na Hybrid Runbook Worker

[Spuštění sady Runbook v Azure Automation](automation-starting-a-runbook.md) popisuje různé metody spuštění sady Runbook. Po spuštění sady Runbook na Hybrid Runbook Worker se používá možnost **Spustit na** , která umožňuje zadat název skupiny Hybrid Runbook Worker. Při zadání skupiny jeden z pracovních procesů v dané skupině načte a spustí sadu Runbook. Pokud sada Runbook tuto možnost neurčí, Azure Automation spouští Runbook jako obvykle.

Když spustíte Runbook v Azure Portal, zobrazí se vám možnost **Spustit na** , pro kterou můžete vybrat **Azure** nebo **Hybrid Worker**. Pokud vyberete možnost **Hybrid Worker**, můžete zvolit Hybrid Runbook Worker skupinu z rozevíracího seznamu.

Použijte parametr `RunOn` s rutinou `Start-AzureAutomationRunbook`. Následující příklad používá prostředí Windows PowerShell ke spuštění sady Runbook s názvem **test-Runbook** ve skupině Hybrid Runbook Worker s názvem MyHybridGroup.

```azurepowershell-interactive
Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> Do `Start-AzureAutomationRunbook` ve verzi 0.9.1 Microsoft Azure PowerShell byl přidán parametr `RunOn`. [Nejnovější verzi](https://azure.microsoft.com/downloads/) si můžete stáhnout, pokud už máte nainstalovanou dřívější verzi. Tuto verzi nainstalujte jenom na pracovní stanici, kde spouštíte Runbook z PowerShellu. Nemusíte ho instalovat na Hybrid Runbook Worker počítač, pokud nechcete spouštět Runbooky z tohoto počítače.

## <a name="working-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Práce s podepsanými Runbooky ve Windows Hybrid Runbook Worker

Můžete nakonfigurovat Hybrid Runbook Worker systému Windows tak, aby spouštěla pouze podepsané Runbooky.

> [!IMPORTANT]
> Jakmile nakonfigurujete Hybrid Runbook Worker, aby spouštěla pouze podepsané Runbooky, nespustí se v pracovním procesu žádné nepodepsané Runbooky.

### <a name="create-signing-certificate"></a>Vytvořit podpisový certifikát

Následující příklad vytvoří certifikát podepsaný svým držitelem, který lze použít k podepisování runbooků. Tento kód vytvoří certifikát a vyexportuje ho, aby ho Hybrid Runbook Worker mohl později importovat. Kryptografický otisk se taky vrátí pro pozdější použití při odkazování na certifikát.

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
                                        -Subject "CN=contoso.com" `
                                        -KeyAlgorithm RSA `
                                        -KeyLength 2048 `
                                        -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
                                        -KeyExportPolicy Exportable `
                                        -KeyUsage DigitalSignature `
                                        -Type CodeSigningCert


# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Importovat certifikát a nakonfigurovat pracovní procesy pro ověřování podpisů

Zkopírujte certifikát, který jste vytvořili, do každého Hybrid Runbook Worker ve skupině. Spusťte následující skript, který importuje certifikát a nakonfiguruje pracovníky tak, aby používali ověřování podpisů v sadách Runbook.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Podepisování runbooků pomocí certifikátu

U procesů Hybrid Runbook Worker nakonfigurovaných pro použití pouze podepsaných runbooků musíte podepisovat Runbooky, které se mají používat na Hybrid Runbook Worker. Pro podepsání těchto runbooků použijte následující vzorový kód PowerShellu.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Když je Runbook podepsaný, musíte ho naimportovat do svého účtu Automation a publikovat ho pomocí bloku signatury. Informace o tom, jak importovat Runbooky, najdete v tématu [Import Runbooku ze souboru do Azure Automation](manage-runbooks.md#import-a-runbook).

## <a name="working-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Práce s podepsanými Runbooky v Hybrid Runbook Worker Linux

Aby bylo možné pracovat s podepsanými Runbooky, musí mít Hybrid Runbook Worker pro Linux na místním počítači spustitelný soubor [GPG](https://gnupg.org/index.html) .

> [!IMPORTANT]
> Jakmile nakonfigurujete Hybrid Runbook Worker, aby spouštěla pouze podepsané Runbooky, nespustí se v pracovním procesu žádné nepodepsané Runbooky.

### <a name="create-a-gpg-keyring-and-keypair"></a>Vytvoření GPGch klíčů a souboru KeyPair

Pokud chcete vytvořit GPG a souboru KeyPair, použijte účet Hybrid Runbook Worker **nxautomation** .

1. Pomocí aplikace sudo se přihlaste jako účet **nxautomation** .

    ```bash
    sudo su – nxautomation
    ```

2. Po použití **nxautomation**vygenerujte GPG souboru KeyPair. GPG vás provede jednotlivými kroky. Musíte zadat jméno, e-mailovou adresu, čas vypršení platnosti a heslo. Potom počkejte, než bude na počítači dostatek entropie, aby se klíč vygeneroval.

    ```bash
    sudo gpg --generate-key
    ```

3. Vzhledem k tomu, že se adresář GPG vygeneroval pomocí sudo, musíte změnit jeho vlastníka na **nxautomation** pomocí následujícího příkazu.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Zpřístupnit Hybrid Runbook Worker klíčů k dispozici

Po vytvoření se Správce klíčů zpřístupní Hybrid Runbook Worker. Upravte soubor nastavení **/var/opt/Microsoft/omsagent/State/automationworker/DIY/Worker.conf** tak, aby obsahoval následující ukázkový kód v části soubor `[worker-optional]`.

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Ověřte, jestli je zapnuté ověřování podpisů.

Pokud je na počítači zakázané ověřování podpisů, musíte ho zapnout spuštěním následujícího příkazu sudo. Nahraďte `<LogAnalyticsworkspaceId>` vaším ID pracovního prostoru.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Podepsat Runbook

Jakmile nakonfigurujete ověřování podpisů, použijte následující příkaz GPG k podepsání sady Runbook.

```bash
gpg –-clear-sign <runbook name>
```

Podepsaná sada Runbook se nazývá `<runbook name>.asc`.

Teď můžete nahrát podepsaný Runbook do Azure Automation a spustit ho jako regulární Runbook.

## <a name="next-steps"></a>Další kroky

* Další informace o metodách spuštění sady Runbook naleznete [v tématu Starting a Runbook in Azure Automation](automation-starting-a-runbook.md).
* Pokud chcete pochopit, jak používat textový editor pro práci se sadami Runbook PowerShell v Azure Automation, přečtěte si téma [Úprava Runbooku v Azure Automation](automation-edit-textual-runbook.md).
* Pokud vaše Runbooky nejsou úspěšně dokončeny, přečtěte si příručku Poradce při potížích s [chybami spuštění sady Runbook](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* Další informace o PowerShellu, včetně referenčních modulů jazyka a výukových modulů, najdete v [dokumentaci k PowerShellu](https://docs.microsoft.com/powershell/scripting/overview).
