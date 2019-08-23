---
title: Spouštění Runbooků v Azure Automation Hybrid Runbook Worker
description: Tento článek poskytuje informace o spouštění Runbooků v počítačích v místním datovém centru nebo poskytovateli cloudu s rolí Hybrid Runbook Worker.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 01/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c10262e50fff2903d7caf242304145a2ab93dbcd
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69970618"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Spouštění Runbooků na Hybrid Runbook Worker

Ve struktuře runbooků, které běží v Azure Automation a runbooky, které běží na Hybrid Runbook Worker, není žádný rozdíl. Sady Runbook, které používáte s nejpravděpodobnějším rozdílem, se výrazně liší. Důvodem je to, že Runbooky, které cílí na Hybrid Runbook Worker, obvykle spravují prostředky v místním počítači nebo k prostředkům v místním prostředí, kde je nasazený. Runbooky v Azure Automation obvykle spravují prostředky v cloudu Azure.

Když vytváříte Runbooky, které mají běžet na Hybrid Runbook Worker, měli byste Runbooky upravovat a testovat v počítači, který je hostitelem hybridního pracovního procesu. Hostitelský počítač má všechny moduly PowerShellu a přístup k síti, které potřebujete pro správu a přístup k místním prostředkům. Jakmile se Runbook na hybridním pracovním počítači testuje, můžete ho nahrát do prostředí Azure Automation, kde je možné ho spustit v hybridním pracovním procesu. Je důležité, abyste věděli, že úlohy spuštěné pod účtem místního systému pro Windows nebo speciální uživatelský účet `nxautomation` v systému Linux. V systému Linux to znamená, že je nutné zajistit, `nxautomation` aby měl účet přístup k umístění, kam vaše moduly ukládáte. Když použijete rutinu [install-Module]() , zadejte **AllUsers** k `-Scope` parametru, abyste si ověřili `naxautomation` , že má účet přístup.

Další informace o PowerShellu v systému Linux najdete v tématu [známé problémy pro prostředí PowerShell na platformách jiných než Windows](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms).

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Spuštění Runbooku na Hybrid Runbook Worker

[Spuštění sady Runbook v Azure Automation](automation-starting-a-runbook.md) popisuje různé metody spuštění sady Runbook. Hybrid Runbook Worker přidá možnost **RunOn** , kde můžete zadat název skupiny Hybrid Runbook Worker. Pokud je zadána skupina, sada Runbook se načte a spustí jedním z pracovních procesů v dané skupině. Pokud není tato možnost zadána, je spuštěna v Azure Automation jako normální.

Když v Azure Portal spustíte Runbook, zobrazí se vám možnost **Spustit** po, kde můžete vybrat **Azure** nebo **Hybrid Worker**. Pokud vyberete **Hybrid Worker**, můžete vybrat skupinu z rozevíracího seznamu.

Použijte parametr **RunOn** . Pomocí následujícího příkazu můžete spustit sadu Runbook s názvem test-Runbook ve skupině Hybrid Runbook Worker s názvem MyHybridGroup pomocí prostředí Windows PowerShell.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> Do rutiny **Start-AzureAutomationRunbook** ve verzi 0.9.1 Microsoft Azure PowerShell byl přidán parametr **RunOn** . [Nejnovější verzi](https://azure.microsoft.com/downloads/) si můžete stáhnout, pokud už máte nainstalovanou dřívější verzi. Tuto verzi musíte nainstalovat jenom na pracovní stanici, na které spouštíte Runbook z PowerShellu. Nemusíte ho instalovat na pracovní počítač, pokud nechcete spouštět Runbooky z tohoto počítače. "

## <a name="runbook-permissions"></a>Oprávnění sady Runbook

Runbooky běžící na Hybrid Runbook Worker nemůžou používat stejnou metodu, která se obvykle používá pro Runbooky ověřující prostředky Azure, protože přistupuje k prostředkům, které nejsou v Azure. Sada Runbook může buď poskytnout své vlastní ověřování místním prostředkům, nebo může nakonfigurovat ověřování pomocí [spravovaných identit pro prostředky](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager
)Azure. Můžete také zadat účet RunAs k poskytnutí kontextu uživatele pro všechny sady Runbook.

### <a name="runbook-authentication"></a>Ověřování Runbooku

Ve výchozím nastavení se Runbooky spouštějí v kontextu místního systémového účtu pro Windows a speciální uživatelský účet `nxautomation` pro Linux na místním počítači, takže musí poskytovat svoje vlastní ověřování pro prostředky, ke kterým přistupuje.

V sadě Runbook můžete použít prostředky [přihlašovacích údajů](automation-credentials.md) a [certifikátů](automation-certificates.md) s rutinami, které umožňují zadat přihlašovací údaje, abyste je mohli ověřit u různých prostředků. Následující příklad ukazuje část sady Runbook, která restartuje počítač. Načítá přihlašovací údaje z assetu přihlašovacích údajů a název počítače z variabilního prostředku a pak tyto hodnoty používá pomocí rutiny restart-Computer.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Můžete také použít [InlineScript](automation-powershell-workflow.md#inlinescript), což umožňuje spouštět bloky kódu na jiném počítači s přihlašovacími údaji, které jsou určeny pomocí [společného parametru PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="runas-account"></a>Účet RunAs

Ve výchozím nastavení používá Hybrid Runbook Worker pro spouštění Runbooků místní systém pro Windows a speciální `nxautomation` uživatelský účet pro Linux. Místo toho, aby Runbooky poskytovaly vlastní ověřování pro místní prostředky, můžete zadat účet **runas** pro skupinu hybridních pracovních procesů. Zadáte [Asset přihlašovacích údajů](automation-credentials.md) , který má přístup k místním prostředkům, a všechny Runbooky se spustí pod těmito přihlašovacími údaji při spuštění na Hybrid Runbook Worker ve skupině.

Uživatelské jméno pro přihlašovací údaje musí být v jednom z následujících formátů:

* domain\username
* username@domain
* uživatelské jméno (pro účty místní k místnímu počítači)

Pro zadání účtu RunAs pro skupinu hybridních pracovních procesů použijte následující postup:

1. Vytvořte [Asset přihlašovacích údajů](automation-credentials.md) s přístupem k místním prostředkům.
2. Otevřete účet Automation v Azure Portal.
3. Vyberte dlaždici **Hybrid Worker skupiny** a pak vyberte skupinu.
4. Vyberte **všechna nastavení** a pak **Nastavení skupiny hybridních pracovních procesů**.
5. Změňte **Spustit jako** z **výchozí** na **vlastní**.
6. Vyberte přihlašovací údaje a klikněte na **Uložit**.

### <a name="managed-identities-for-azure-resources"></a>Spravované identity pro prostředky Azure

Hybridní pracovní procesy Runbooku, které běží na virtuálních počítačích Azure, můžou k ověřování prostředků Azure používat spravované identity pro prostředky Azure. Existuje mnoho výhod používání spravovaných identit pro prostředky Azure prostřednictvím účtů spustit jako.

* Není nutné exportovat certifikát spustit jako a pak ho importovat do Hybrid Runbook Worker
* Není nutné obnovovat certifikát používaný účtem spustit jako.
* V kódu Runbooku není potřeba zpracovávat objekt připojení spustit jako.

Pokud chcete používat spravovanou identitu pro prostředky Azure v procesu Hybrid Runbook Worker, musíte provést následující kroky:

1. Vytvoření virtuálního počítače Azure
2. [Konfigurace spravovaných identit pro prostředky Azure na vašem VIRTUÁLNÍm počítači](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)
3. [Udělení přístupu k VIRTUÁLNÍmu počítači do skupiny prostředků v Správce prostředků](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)
4. [Získání přístupového tokenu pomocí spravované identity přiřazené systémem virtuálního počítače](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-resource-manager)
5. Nainstalujte na virtuální počítač [Hybrid Runbook Worker Windows](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker) .

Po dokončení předchozích kroků můžete pomocí `Connect-AzureRmAccount -Identity` sady Runbook ověřit prostředky Azure. Tato konfigurace omezuje nutnost použít účet Spustit jako a spravovat certifikát pro účet Spustit jako.

```powershell
# Connect to Azure using the Managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
Connect-AzureRmAccount -Identity

# Get all VM names from the subscription
Get-AzureRmVm | Select Name
```

### <a name="runas-script"></a>Účet Spustit jako pro Automation

Jako součást procesu automatizovaného sestavení pro nasazení prostředků v Azure můžete vyžadovat přístup k místním systémům pro podporu úlohy nebo sady kroků v sekvenci nasazení. Aby bylo možné podporovat ověřování v Azure pomocí účtu Spustit jako, je nutné nainstalovat certifikát účtu Spustit jako.

Následující PowerShellový Runbook, **Export – RunAsCertificateToHybridWorker**, exportuje certifikát spustit jako z účtu Azure Automation a stáhne a naimportuje ho do úložiště certifikátů místního počítače na hybridním pracovním procesu, který je připojený. ke stejnému účtu. Po dokončení tohoto kroku ověří pracovní proces, který se může úspěšně ověřit v Azure pomocí účtu Spustit jako.

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
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
Run this runbook in the hybrid worker where you want the certificate installed.
This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
AUTHOR: Azure Automation Team
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

# Test that authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm Azure Resource Manager calls are working
Get-AzureRmAutomationAccount | Select-Object AutomationAccountName
```

> [!IMPORTANT]
> **Add-AzureRmAccount** je teď alias pro **Connect-AzureRmAccount**. Pokud se při hledání položek knihovny nezobrazí **příkaz Connect-AzureRMAccount**, můžete použít příkaz **Add-AzureRMAccount**nebo můžete aktualizovat moduly v účtu Automation.

Uložte Runbook *Export-RunAsCertificateToHybridWorker* do počítače s `.ps1` příponou. Importujte ho do svého účtu Automation a upravte Runbook a změňte hodnotu proměnné `$Password` pomocí vlastního hesla. Publikování a spuštění sady Runbook. Určete cílovou skupinu Hybrid Worker, která spustí a ověří Runbooky pomocí účtu Spustit jako. Datový proud úlohy hlásí pokus o import certifikátu do úložiště místního počítače a následuje za více řádky. Toto chování závisí na tom, kolik účtů služby Automation ve vašem předplatném definujete a jestli je ověřování úspěšné.

## <a name="job-behavior"></a>Chování úlohy

Úlohy se na hybridních pracovních procesech Runbooku zpracovávají trochu jinak než při jejich spuštění v izolovaném prostoru Azure. Jedním z klíčových rozdílů je, že pro procesy Hybrid Runbook Worker neplatí žádné omezení doby trvání úloh. Sady Runbook spuštěné v Azure Sandbox jsou omezené na 3 hodiny kvůli [spravedlivému sdílení](automation-runbook-execution.md#fair-share). Pro dlouhotrvající sadu Runbook se ujistěte, že je odolný proti možnému restartování. Například pokud počítač, který je hostitelem restartování hybridního pracovního procesu. Pokud se počítač hostitele Hybrid Worker restartuje, pak se všechny spuštěné úlohy Runbooku restartují od začátku nebo z posledního kontrolního bodu pro Runbooky pracovního postupu PowerShellu. Po restartování úlohy Runbooku je více než třikrát pozastaveno.

## <a name="run-only-signed-runbooks"></a>Spustit pouze podepsané Runbooky

Procesy Hybrid Runbook Worker lze konfigurovat tak, aby spouštěly pouze podepsané Runbooky s určitou konfigurací. Následující část popisuje, jak nastavit pracovní procesy Hybrid Runbook pro spouštění podepsaných [Hybrid Runbook Worker Windows](#windows-hybrid-runbook-worker) a [Linux Hybrid Runbook Worker](#linux-hybrid-runbook-worker)

> [!NOTE]
> Jakmile nakonfigurujete Hybrid Runbook Worker, aby spouštěla pouze podepsané Runbooky, nespustí se v pracovním procesu žádné nepodepsané Runbooky.

### <a name="windows-hybrid-runbook-worker"></a>Hybrid Runbook Worker Windows

#### <a name="create-signing-certificate"></a>Vytvořit podpisový certifikát

Následující příklad vytvoří certifikát podepsaný svým držitelem, který lze použít k podepisování runbooků. Ukázka vytvoří certifikát a exportuje ho. Certifikát se importuje do procesů Hybrid Runbook Worker později. Kryptografický otisk se vrátí taky tato hodnota se později použije k odkazování na certifikát.

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

#### <a name="configure-the-hybrid-runbook-workers"></a>Konfigurace pracovních procesů Hybrid Runbook Worker

Zkopírujte certifikát vytvořený do každého Hybrid Runbook Worker ve skupině. Spusťte následující skript, který naimportuje certifikát a nakonfiguruje Hybrid Worker k použití ověřování podpisů pro Runbooky.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

#### <a name="sign-your-runbooks-using-the-certificate"></a>Podepisování runbooků pomocí certifikátu

U procesů Hybrid Runbook Worker nakonfigurovaných pro použití pouze podepsaných runbooků musíte podepisovat Runbooky, které se mají používat na Hybrid Runbook Worker. Pomocí následujícího ukázkového prostředí PowerShell podepište své Runbooky.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Po podepsání Runbooku se musí importovat do svého účtu Automation a publikovat pomocí bloku signatury. Informace o tom, jak importovat Runbooky, najdete v tématu [Import Runbooku ze souboru do Azure Automation](manage-runbooks.md#import-a-runbook).

### <a name="linux-hybrid-runbook-worker"></a>Hybrid Runbook Worker pro Linux

Aby bylo možné v Hybrid Runbook Worker pro Linux podepisovat Runbooky, Hybrid Runbook Worker musí mít na počítači přítomný spustitelný soubor [GPG](https://gnupg.org/index.html) .

#### <a name="create-a-gpg-keyring-and-keypair"></a>Vytvoření GPGch klíčů a souboru KeyPair

Pokud chcete vytvořit Správce klíčů a souboru KeyPair, musíte použít účet `nxautomation`Hybrid Runbook Worker.

Použijte `sudo` k přihlášení `nxautomation` jako účet.

```bash
sudo su – nxautomation
```

Po použití `nxautomation` účtu vygenerujte GPG souboru KeyPair.

```bash
sudo gpg --generate-key
```

GPG vás provede jednotlivými kroky vytvoření souboru KeyPair. Pro vygenerování klíče budete muset zadat jméno, e-mailovou adresu, čas vypršení platnosti a heslo a čekat na to, až se na počítači dokončí dostatečná entropie.

Vzhledem k tomu, že adresář GPG byl vygenerován pomocí sudo, je nutné změnit `nxautomation`jeho vlastníka na.

Chcete-li změnit vlastníka, spusťte následující příkaz.

```bash
sudo chown -R nxautomation ~/.gnupg
```

#### <a name="make-the-keyring-available-the-hybrid-runbook-worker"></a>Zpřístupnění Hybrid Runbook Workerho klíčů

Jakmile se vytvoří Správce klíčů, budete ho muset zpřístupnit Hybrid Runbook Worker. Upravte soubor `/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf` nastavení tak, aby obsahoval následující příklad v části.`[worker-optional]`

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

#### <a name="verify-signature-validation-is-on"></a>Ověřte, jestli je zapnuté ověřování podpisů.

Pokud je na počítači zakázané ověřování podpisů, budete ho muset zapnout. Spusťte následující příkaz, který povolí ověřování podpisů. Nahraďte `<LogAnalyticsworkspaceId>` ID vašeho pracovního prostoru.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

#### <a name="sign-a-runbook"></a>Podepsat Runbook

Po nakonfigurování ověřování podpisů můžete pro podepsání sady Runbook použít následující příkaz:

```bash
gpg –-clear-sign <runbook name>
```

Podepsaný Runbook bude mít název `<runbook name>.asc`.

Podepsaný Runbook se teď dá nahrát na Azure Automation a dá se spustit jako běžná sada Runbook.

## <a name="next-steps"></a>Další postup

* Další informace o různých metodách, které se dají použít ke spuštění Runbooku, najdete [v tématu Spuštění Runbooku v Azure Automation](automation-starting-a-runbook.md).
* Chcete-li pochopit různé způsoby práce s Runbooky PowerShellu v Azure Automation pomocí textového editoru, přečtěte si téma [Úprava sady Runbook v Azure Automation](automation-edit-textual-runbook.md)
* Pokud vaše Runbooky nejsou úspěšně dokončeny, přečtěte si příručku k odstraňování potíží s [chybami spuštění sady Runbook](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* Další informace o PowerShellu, včetně referenčních modulů jazyka a výukových modulů, najdete v [dokumentaci](https://docs.microsoft.com/en-us/powershell/scripting/overview)k PowerShellu.
