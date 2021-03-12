---
title: Spuštění Azure Automation runbooků na Hybrid Runbook Worker
description: Tento článek popisuje, jak na počítačích v místním datovém centru nebo v jiném poskytovateli cloudu spouštět Runbooky pomocí Hybrid Runbook Worker.
services: automation
ms.subservice: process-automation
ms.date: 03/10/2021
ms.topic: conceptual
ms.openlocfilehash: 6d1f504458aed440464015a34479d75992fe5c45
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103149371"
---
# <a name="run-runbooks-on-a-hybrid-runbook-worker"></a>Spouštění runbooků ve funkci Hybrid Runbook Worker

Sady Runbook, které běží na [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) obvykle spravují prostředky v místním počítači nebo k prostředkům v místním prostředí, kde je pracovní proces nasazen. Runbooky v Azure Automation obvykle spravují prostředky v cloudu Azure. I když se používají jinak, Runbooky, které běží v Azure Automation a runbooky, které běží na Hybrid Runbook Worker, jsou identické ve struktuře.

Když vytváříte Runbook ke spuštění na Hybrid Runbook Worker, měli byste sadu Runbook upravit a otestovat na počítači, který je hostitelem pracovního procesu. Hostitelský počítač má všechny moduly PowerShellu a přístup k síti nutné ke správě místních prostředků. Po otestování Runbooku na Hybrid Runbook Worker počítači ho můžete nahrát do prostředí Azure Automation, kde ho můžete spustit na pracovním procesu.

## <a name="plan-runbook-job-behavior"></a>Plánování chování úlohy Runbooku

Azure Automation zpracovává úlohy pro procesy Hybrid Runbook Worker jinak než úlohy spuštěné v izolovaném prostoru Azure. Pokud máte dlouho běžící sadu Runbook, ujistěte se, že je možné restartovat počítač. Podrobnosti o chování úlohy najdete v tématu [Hybrid Runbook Worker úlohy](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-jobs).

Úlohy pro procesy Hybrid Runbook Worker běží pod účtem místní **systém** ve Windows nebo v účtu **Nxautomation** v systému Linux. V případě systému Linux ověřte, zda má účet **nxautomation** přístup k umístění, kde jsou uloženy moduly sady Runbook. Když použijete rutinu [install-Module](/powershell/module/powershellget/install-module) , nezapomeňte pro parametr zadat AllUsers, aby `Scope` se zajistilo, že má účet **nxautomation** přístup. Další informace o PowerShellu v systému Linux najdete v tématu [známé problémy pro prostředí PowerShell na platformách jiných než Windows](/powershell/scripting/whats-new/what-s-new-in-powershell-70).

## <a name="configure-runbook-permissions"></a>Konfigurace oprávnění sady Runbook

Definujte oprávnění pro sadu Runbook pro spuštění na Hybrid Runbook Worker následujícími způsoby:

* Umožněte, aby sada Runbook poskytovala vlastní ověřování pro místní prostředky.
* Nakonfigurujte ověřování pomocí [spravovaných identit pro prostředky Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
* Zadejte účet Spustit jako, který bude poskytovat kontext uživatele pro všechny sady Runbook.

### <a name="use-runbook-authentication-to-local-resources"></a>Použití ověřování Runbooku pro místní prostředky

Pokud připravujete sadu Runbook, která poskytuje vlastní ověřování prostředků, použijte [přihlašovací údaje](./shared-resources/credentials.md) a prostředky [certifikátů](./shared-resources/certificates.md) v sadě Runbook. K dispozici je několik rutin, které vám umožní zadat přihlašovací údaje, aby se sada Runbook mohla ověřit u různých prostředků. Následující příklad ukazuje část sady Runbook, která restartuje počítač. Načítá přihlašovací údaje z assetu přihlašovacích údajů a název počítače z variabilního prostředku a pak tyto hodnoty používá s `Restart-Computer` rutinou.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Můžete také použít aktivitu [InlineScript](automation-powershell-workflow.md#use-inlinescript) . `InlineScript` umožňuje spouštět bloky kódu na jiném počítači s přihlašovacími údaji.

### <a name="use-runbook-authentication-with-managed-identities"></a><a name="runbook-auth-managed-identities"></a>Použití ověřování Runbooku u spravovaných identit

Hybridní pracovní procesy Runbooku na virtuálních počítačích Azure můžou použít spravované identity k ověřování prostředků Azure. Použití spravovaných identit pro prostředky Azure místo účtů spustit jako přináší výhody, protože nemusíte provádět tyto akce:

* Exportujte certifikát spustit jako a pak ho importujte do Hybrid Runbook Worker.
* Obnovte certifikát používaný účtem spustit jako.
* Zpracujte objekt připojení spustit jako v kódu Runbooku.

Postupujte podle dalších kroků a použijte spravovanou identitu pro prostředky Azure na Hybrid Runbook Worker:

1. Vytvořte virtuální počítač Azure.
1. Nakonfigurujte spravované identity pro prostředky Azure na virtuálním počítači. Další informace najdete v tématu [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
1. Udělte virtuálnímu počítači přístup ke skupině prostředků v Správce prostředků. Informace o [použití spravované identity přiřazené systémem Windows VM pro přístup k Správce prostředků](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
1. Nainstalujte na virtuální počítač Hybrid Runbook Worker. Viz [nasazení Hybrid Runbook Worker Windows](automation-windows-hrw-install.md) nebo [nasazení Hybrid Runbook Worker pro Linux](automation-linux-hrw-install.md).
1. Aktualizujte sadu Runbook tak, aby k ověřování prostředků Azure použila rutinu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) s `Identity` parametrem. Tato konfigurace omezuje nutnost použít účet Spustit jako a provede přidruženou správu účtů.

    ```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
    ```

    > [!NOTE]
    > `Connect-AzAccount -Identity` funguje pro Hybrid Runbook Worker používající identitu přiřazenou systémem a jedinou identitu přiřazenou uživatelem. Pokud na Hybrid Runbook Worker použijete více uživatelsky přiřazených identit, je nutné, aby sada Runbook určila `AccountId` parametr pro `Connect-AzAccount` výběr konkrétní identity přiřazené uživatelem.

### <a name="use-runbook-authentication-with-run-as-account"></a>Použití ověřování Runbooku s účtem spustit jako

Místo toho, aby sada Runbook poskytovala vlastní ověřování pro místní prostředky, můžete zadat účet Spustit jako pro skupinu Hybrid Runbook Worker. Chcete-li zadat účet Spustit jako, je nutné definovat [Asset přihlašovacích údajů](./shared-resources/credentials.md) , který má přístup k místním prostředkům. Tyto prostředky zahrnují úložiště certifikátů a všechny sady Runbook běží pod těmito přihlašovacími údaji na Hybrid Runbook Worker ve skupině.

- Uživatelské jméno pro přihlašovací údaje musí být v jednom z následujících formátů:

   * jméno
   * username@domain
   * uživatelské jméno (pro účty místní k místnímu počítači)

- Pokud chcete použít PowerShell PowerShell **Export-RunAsCertificateToHybridWorker**, musíte na místním počítači nainstalovat AZ modules for Azure Automation.

#### <a name="use-a-credential-asset-to-specify-a-run-as-account"></a>Použití assetu přihlašovacích údajů k určení účtu Spustit jako

K určení účtu Spustit jako pro skupinu Hybrid Runbook Worker použijte následující postup:

1. Vytvořte [Asset přihlašovacích údajů](./shared-resources/credentials.md) s přístupem k místním prostředkům.
1. Otevřete účet Automation v Azure Portal.
1. Vyberte **Hybrid Worker skupiny** a pak vyberte konkrétní skupinu.
1. Vyberte **všechna nastavení** a potom **Nastavení skupiny hybridních pracovních procesů**.
1. Změňte hodnotu **Spustit jako** z **výchozí** na Custom ( **vlastní**).
1. Vyberte přihlašovací údaje a klikněte na **Uložit**.

## <a name="install-run-as-account-certificate"></a><a name="runas-script"></a>Nainstalovat certifikát účtu Spustit jako

Jako součást procesu automatizovaného sestavování pro nasazení prostředků v Azure můžete vyžadovat přístup k místním systémům pro podporu úlohy nebo sady kroků v sekvenci nasazení. Chcete-li zajistit ověřování v Azure pomocí účtu Spustit jako, je nutné nainstalovat certifikát účtu Spustit jako.

>[!NOTE]
>Tento Runbook PowerShellu v tuto chvíli neběží na počítačích se systémem Linux. Spouští se jenom na počítačích s Windows.
>

Následující PowerShellový Runbook s názvem **Export-RunAsCertificateToHybridWorker** exportuje certifikát spustit jako z účtu Azure Automation. Sada Runbook stáhne a importuje certifikát do úložiště certifikátů místního počítače na Hybrid Runbook Worker, která je připojena ke stejnému účtu. Jakmile tento krok dokončí, sada Runbook ověří, že se pracovní proces může úspěšně ověřit v Azure pomocí účtu Spustit jako.

>[!NOTE]
>Tento PowerShellový Runbook není navržený nebo nemá být spuštěný mimo účet Automation jako skript na cílovém počítači.
>

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
>Pro PowerShellové Runbooky `Add-AzAccount` a `Add-AzureRMAccount` jsou aliasy pro `Connect-AzAccount` . Pokud se při hledání položek knihovny nezobrazí `Connect-AzAccount` , můžete použít `Add-AzAccount` nebo aktualizovat moduly v účtu Automation.

Dokončení přípravy účtu Spustit jako:

1. Uložte Runbook **Export-RunAsCertificateToHybridWorker** do počítače s příponou **. ps1** .
1. Importujte ho do svého účtu Automation.
1. Upravte sadu Runbook a změňte hodnotu `Password` proměnné na vlastní heslo.
1. Publikujte Runbook.
1. Spusťte sadu Runbook, která cílí na Hybrid Runbook Worker skupinu, která spouští a ověřuje Runbooky pomocí účtu Spustit jako. 
1. Prohlédněte si datový proud úlohy a podívejte se, že se pokusí importovat certifikát do úložiště místního počítače a za ním následuje více řádků. Toto chování závisí na tom, kolik účtů služby Automation ve vašem předplatném definujete, a na stupni úspěšnosti ověřování.

## <a name="work-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Práce s podepsanými Runbooky ve Windows Hybrid Runbook Worker

Můžete nakonfigurovat Hybrid Runbook Worker systému Windows tak, aby spouštěla pouze podepsané Runbooky.

> [!IMPORTANT]
> Jakmile nakonfigurujete Hybrid Runbook Worker, aby spouštěla pouze podepsané Runbooky, nepodepsané Runbooky se v pracovním procesu nezdaří.

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

Když je Runbook podepsaný, musíte ho naimportovat do svého účtu Automation a publikovat ho pomocí bloku signatury. Informace o tom, jak importovat Runbooky, najdete v tématu [Import Runbooku](manage-runbooks.md#import-a-runbook).

## <a name="work-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Práce s podepsanými Runbooky v Hybrid Runbook Worker Linux

Aby bylo možné pracovat s podepsanými Runbooky, musí mít Hybrid Runbook Worker pro Linux na místním počítači spustitelný soubor [GPG](https://gnupg.org/index.html) .

> [!IMPORTANT]
> Jakmile nakonfigurujete Hybrid Runbook Worker, aby spouštěla pouze podepsané Runbooky, nepodepsané Runbooky se v pracovním procesu nezdaří.

K dokončení této konfigurace provedete následující kroky:

* Vytvoření GPGch klíčů a souboru KeyPair
* Zpřístupnit Hybrid Runbook Worker klíčů k dispozici
* Ověřte, jestli je zapnuté ověřování podpisů.
* Podepsat Runbook

### <a name="create-a-gpg-keyring-and-keypair"></a>Vytvoření GPGch klíčů a souboru KeyPair

Pokud chcete vytvořit GPG a souboru KeyPair, použijte [účet Hybrid Runbook Worker nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux).

1. Pomocí aplikace sudo se přihlaste jako účet **nxautomation** .

    ```bash
    sudo su – nxautomation
    ```

1. Po použití **nxautomation** vygenerujte GPG souboru KeyPair. GPG vás provede jednotlivými kroky. Musíte zadat jméno, e-mailovou adresu, čas vypršení platnosti a heslo. Potom počkejte, než bude na počítači dostatek entropie, aby se klíč vygeneroval.

    ```bash
    sudo gpg --generate-key
    ```

1. Vzhledem k tomu, že se adresář GPG vygeneroval pomocí sudo, musíte změnit jeho vlastníka na **nxautomation** pomocí následujícího příkazu.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Zpřístupnit Hybrid Runbook Worker klíčů k dispozici

Po vytvoření se Správce klíčů zpřístupní Hybrid Runbook Worker. Upravte soubor nastavení **Domů/nxautomation/State/Work. conf** , aby obsahoval následující vzorový kód v části soubor `[worker-optional]` .

```bash
gpg_public_keyring_path = /home/nxautomation/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Ověřte, jestli je zapnuté ověřování podpisů.

Pokud je na počítači zakázané ověřování podpisů, musíte ho zapnout spuštěním následujícího příkazu sudo. Nahraďte `<LogAnalyticsworkspaceId>` ID vašeho pracovního prostoru.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Podepsat Runbook

Jakmile nakonfigurujete ověřování podpisů, použijte následující příkaz GPG k podepsání sady Runbook.

```bash
gpg –-clear-sign <runbook name>
```

Podepsaná sada Runbook se nazývá **<runbook name> . asc**.

Teď můžete nahrát podepsaný Runbook do Azure Automation a spustit ho jako regulární Runbook.

## <a name="start-a-runbook-on-a-hybrid-runbook-worker"></a>Spuštění Runbooku na Hybrid Runbook Worker

[Spuštění Runbooku v Azure Automation](start-runbooks.md) popisuje různé metody spuštění Runbooku. Spuštění sady Runbook na Hybrid Runbook Worker používá možnost **Spustit na** , která umožňuje zadat název skupiny Hybrid Runbook Worker. Při zadání skupiny jeden z pracovních procesů v dané skupině načte a spustí sadu Runbook. Pokud sada Runbook tuto možnost neurčí, Azure Automation spouští Runbook jako obvykle.

Když spustíte Runbook v Azure Portal, zobrazí se vám možnost **Spustit na** , pro kterou můžete vybrat **Azure** nebo **Hybrid Worker**. Pokud vyberete možnost **Hybrid Worker**, můžete zvolit Hybrid Runbook Worker skupinu z rozevíracího seznamu.

Při spouštění sady Runbook pomocí prostředí PowerShell použijte `RunOn` parametr s rutinou [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) . Následující příklad používá prostředí Windows PowerShell ke spuštění sady Runbook s názvem **test-Runbook** ve skupině Hybrid Runbook Worker s názvem MyHybridGroup.

```azurepowershell-interactive
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

## <a name="logging"></a>protokolování

V rámci řešení potíží se sadami Runbook, které běží na Hybrid Runbook Worker, jsou protokoly uložené lokálně v následujícím umístění:

* Ve Windows v `C:\ProgramData\Microsoft\System Center\Orchestrator\<version>\SMA\Sandboxes` pro podrobné protokolování procesů běhu úloh. Události stavu úlohy Runbooku na vysoké úrovni se zapisují do protokolu událostí **Application a Services Logs\Microsoft-Automation\Operations** .

* V systému Linux se protokoly hybridního pracovního procesu uživatele dají najít na adrese `/home/nxautomation/run/worker.log` a na adrese můžete najít protokoly System Runbook Worker `/var/opt/microsoft/omsagent/run/automationworker/worker.log` .

## <a name="next-steps"></a>Další kroky

* Pokud vaše Runbooky nejsou úspěšně dokončeny, přečtěte si příručku Poradce při potížích s [chybami spuštění sady Runbook](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* Další informace o PowerShellu, včetně referenčních modulů jazyka a výukových modulů, najdete v tématu [dokumentace k PowerShellu](/powershell/scripting/overview).
* Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](/powershell/module/az.automation).
