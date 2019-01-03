---
title: Spouštění runbooků v Azure Automation Hybrid Runbook Worker
description: Tento článek obsahuje informace o spouštění runbooků v počítačích v místním datovém centru nebo poskytovatele cloudu s rolí pracovního procesu Hybrid Runbook Worker.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 07/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 89f8b4a842c9a632c661d9770d17c1ec01d4211e
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53582248"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Spouštění runbooků v procesu Hybrid Runbook Worker

Není žádný rozdíl ve struktuře sady runbook, která spustí ve službě Azure Automation a sady runbook, která spustí v procesu Hybrid Runbook Worker. Sady Runbook, které můžete používat s jednotlivými pravděpodobně výrazně lišit. Tento rozdíl je, protože runbooky, které se zaměřují funkce Hybrid Runbook Worker obvykle správu prostředků v místním počítači, samotný nebo s prostředky v místním prostředí, kde je nasazen. Runbooky ve službě Azure Automation obvykle správu prostředků v cloudu Azure.

Při vytváření sady runbook spouštět v procesu Hybrid Runbook Worker, upravíte a testovat sady runbook na počítači, který je hostitelem Hybrid worker. Hostitelský počítač má všechny moduly Powershellu a přístup k síti, které potřebujete ke správě a přístup k místním prostředkům. Jakmile na počítač hybridního pracovního procesu testování sady runbook, ho můžete nahrát do prostředí Azure Automation, kde je k dispozici ke spuštění v procesu Hybrid worker. Je důležité vědět, který úlohy, ve kterých běží pod účtem místního systému pro Windows nebo zvláštní uživatelský účet **nxautomation** v Linuxu. Toto chování můžete zavést drobné rozdíly při autorizaci sad runbook pro Hybrid Runbook Worker. Tyto změny byste měli zkontrolovat při psaní vašich sadách runbook.

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Spuštění runbooku v procesu Hybrid Runbook Worker

[Spuštění Runbooku ve službě Azure Automation](automation-starting-a-runbook.md) popisuje různé způsoby spuštění sady runbook. Přidá funkce hybrid Runbook Worker **RunOn** možnost zadávat název skupině Hybrid Runbook Worker. Pokud je skupina zadaná, sada runbook je načten a spustit pomocí jedné z pracovních procesů v této skupině. Pokud není tato možnost zadána, pak spustí se ve službě Azure Automation jako za normálních okolností.

Při spuštění sady runbook na portálu Azure portal, zobrazí se vám **spouštět** možnost, kde můžete vybrat **Azure** nebo **Hybrid Worker**. Pokud vyberete **Hybrid Worker**, pak můžete vybrat skupinu z rozevíracího seznamu.

Použití **RunOn** parametru. Můžete spustit sadu runbook s názvem Test-Runbook ve skupině Hybrid Worker Runbooku s názvem MyHybridGroup pomocí Windows Powershellu následující příkaz.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> **RunOn** parametr byl přidán do **Start AzureAutomationRunbook** rutiny ve verzi 0.9.1 prostředí Azure PowerShell. Měli byste [stáhnout nejnovější verzi](https://azure.microsoft.com/downloads/) Pokud máte některou z dřívějších verzí nainstalované. Stačí tuto verzi nainstalovat na pracovní stanici, kde jsou spuštění runbooku z Powershellu. Není nutné k instalaci na počítač pracovního procesu, pokud máte v úmyslu spouštění sad runbook z tohoto počítače"

## <a name="runbook-permissions"></a>Oprávnění sady Runbook

Runbooky, které běží v procesu Hybrid Runbook Worker nelze použít stejnou metodu, která se obvykle používá pro runbooky ověřované pro prostředky Azure, protože přistupuje prostředků není v Azure. Sada runbook může poskytnout vlastní ověřování k místním prostředkům, nebo můžete nakonfigurovat ověřování pomocí [spravovaných identit pro prostředky Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager
). Můžete také zadat účet Spustit jako pro poskytnutí kontextu uživatele pro všechny sady runbook.

### <a name="runbook-authentication"></a>Ověřování Runbooků

Ve výchozím nastavení, runbooky spuštěné v kontextu účtu místního systému pro Windows a zvláštní uživatelský účet **nxautomation** pro Linux v místním počítači, tedy musí zadat své vlastní ověřování k prostředkům, které přistupují k .

Můžete použít [přihlašovacích údajů](automation-credentials.md) a [certifikát](automation-certificates.md) prostředky ve vašem runbooku pomocí rutiny, které umožňují zadat přihlašovací údaje, abyste je mohli ověřit vůči různým prostředkům. Následující příklad ukazuje část sady runbook, který restartuje počítač. Načte přihlašovací údaje z asset přihlašovacích údajů a název počítače z variabilní prostředek a potom pomocí rutiny Restart-Computer použije tyto hodnoty.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Můžete také použít [InlineScript](automation-powershell-workflow.md#inlinescript), což umožňuje spuštění bloků kódu na jiném počítači pomocí přihlašovacích údajů, které jsou určeny [společný parametr PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="runas-account"></a>Účet Spustit jako

Ve výchozím nastavení používá funkce Hybrid Runbook Worker místní systém pro Windows a zvláštní uživatelský účet **nxautomation** pro Linux ke spuštění sady runbook. Namísto toho, aby runbooky, které poskytují vlastní ověřování k místním prostředkům, můžete zadat **RunAs** účet pro skupinu hybridních pracovních procesů. Můžete zadat [asset přihlašovacích údajů](automation-credentials.md) , který má přístup k místním prostředkům, a všechny runbooky spuštěné pod tyto přihlašovací údaje při spuštění v procesu Hybrid Runbook Worker ve skupině.

Uživatelské jméno pro přihlašovací údaje, které musí být v jednom z následujících formátů:

* domain\username
* username@domain
* uživatelské jméno (pro účty místní vůči v místním počítači)

Následující postup použijte k určení účtu RunAs pro skupinu hybridních pracovních procesů:

1. Vytvoření [asset přihlašovacích údajů](automation-credentials.md) s přístupem k místním prostředkům.
2. Na webu Azure Portal otevřete účet Automation.
3. Vyberte **skupiny hybridních pracovních procesů** dlaždici a pak vyberte skupinu.
4. Vyberte **všechna nastavení** a potom **nastavení skupiny hybridních pracovních procesů**.
5. Změna **spustit jako** z **výchozí** k **vlastní**.
6. Vyberte přihlašovací údaje a klikněte na tlačítko **Uložit**.

### <a name="managed-identities-for-azure-resources"></a>Spravované identity pro prostředky Azure

Hybridní pracovní procesy Runbook Worker běžící na virtuálních počítačích Azure umožňuje spravovaným identitám pro prostředky Azure mohli ověřovat prostředky Azure. Existuje mnoho výhod pomocí spravované identity pro prostředky Azure prostřednictvím účty spustit jako.

* Není nutné spustit jako certifikát exportovat a importovat ho do procesu Hybrid Runbook Worker
* Není nutné obnovovat certifikát používaný účet Spustit jako
* Není potřeba zpracovat objekt připojení spustit jako ve vašem kódu sady runbook

Použití spravované identity pro prostředky Azure v procesu Hybrid Runbook worker, budete muset provést následující kroky:

1. Vytvoření virtuálního počítače Azure
2. [Konfigurace spravovaných identit pro prostředky Azure na svém virtuálním počítači](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)
3. [Udělení přístupu vašich virtuálních počítačů do skupiny prostředků v Resource Manageru](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)
4. [Získání přístupového tokenu pomocí spravované identity systém přiřadil Virtuálního počítače](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-resource-manager)
5. [Instalace procesu Hybrid Runbook Worker Windows](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker) na virtuálním počítači.

Po dokončení předchozích kroků můžete použít `Connect-AzureRmAccount -Identity` v runbooku mohli ověřovat prostředky Azure. Tato konfigurace snižuje nutnost používat účet Spustit jako a správa certifikátů pro účet Spustit jako.

```powershell
# Connect to Azure using the Managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
Connect-AzureRmAccount -Identity

# Get all VM names from the subscription
Get-AzureRmVm | Select Name
```

### <a name="runas-script"></a>Účet Spustit jako služby Automation

Jako součást procesu automatické sestavení pro nasazení prostředků v Azure můžou vyžadovat přístup k místním systémům podpory ve vašem nasazení pořadí úloh nebo sadu kroků. Pro podporu ověřování proti Azure pomocí účtu spustit jako, budete muset nainstalovat certifikátu účtu spustit jako.

Následující Powershellový runbook **Export RunAsCertificateToHybridWorker**, exportuje certifikát spustit jako z vašeho účtu Azure Automation a soubory ke stažení a naimportuje do úložiště certifikátů místního počítače na hybridní pracovního procesu, který je připojený ke stejné účtu. Po dokončení tohoto kroku, tak ověří, že se že pracovní proces můžete provádět ověření do Azure pomocí účtu spustit jako.

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
> **Add-AzureRmAccount** je nyní alias pro **Connect-AzureRMAccount**. Při vyhledávání knihovny položky, pokud se nezobrazí **Connect-AzureRMAccount**, můžete použít **Add-AzureRmAccount**, nebo ve vašem účtu Automation můžete aktualizovat moduly.

Uložit *Export RunAsCertificateToHybridWorker* sady runbook na počítači s `.ps1` rozšíření. Importovat do účtu Automation a upravit sadu runbook změnit hodnotu proměnné `$Password` s vlastní heslo. Publikování a pak spustit sadu runbook. Cílové skupiny Hybrid Worker, který spustíte a ověření runbooků pomocí účtu spustit jako. Datový proud úlohy sestav se importovat certifikát do úložiště místního počítače a řídí s více řádky. Toto chování závisí na tom, kolik účtů služby Automation, můžete definovat v rámci vašeho předplatného, a pokud je ověření úspěšné.

## <a name="job-behavior"></a>Chování úlohy

Úlohy jsou zpracovány mírně liší na procesy Hybrid Runbook Worker než když jsou spuštěné na Azure karantény. Jedním klíčovým rozdílem je, že neexistuje žádné omezení na dobu trvání úlohy na procesy Hybrid Runbook Worker. Spuštění sady Runbook v Azure izolovaných prostorů jsou omezena na 3 hodin z důvodu [spravedlivé sdílení](automation-runbook-execution.md#fair-share). Pro dlouho běžící runbooku chcete Ujistěte se, že je odolný vůči možné restartovat. Například, pokud je počítač, který je hostitelem hybridní restartování pracovního procesu. Pokud Hybrid worker hostitelský počítač restartován, všechny spuštěné úlohy runbooku se restartuje, od začátku nebo od posledního kontrolního bodu pro runbooky pracovních postupů Powershellu. Po sady runbook restartování úlohy více než 3krát a pak je pozastavený.

## <a name="run-only-signed-runbooks"></a>Spustit pouze podepsané sady Runbook

Procesy hybrid Runbook Worker může být nakonfigurován pro spouštění jenom podepsané runbooky s určitou konfigurací. Následující část popisuje, jak vytvořit proces Hybrid Runbook Worker pro spuštění sady runbook podepsaný a podepsání vaší sady runbook.

> [!NOTE]
> Po nakonfigurování Hybrid Runbook Worker pro spuštění sady runbook jenom podepsané sady runbook, které mají **není** byl podepsán se nepovedlo se provést na pracovní proces.

### <a name="create-signing-certificate"></a>Vytvořit certifikát pro podpis

Následující příklad vytvoří certifikát podepsaný svým držitelem, který lze použít pro podepisování sady runbook. Ukázka vytvoří certifikát a vyexportuje ho. Certifikát se později importovat do procesy Hybrid Runbook Worker. Kryptografický otisk se vrátí, že tato hodnota se používá později tak, aby odkazovaly certifikátu.

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

### <a name="configure-the-hybrid-runbook-workers"></a>Konfigurovat pracovní procesy Hybrid Runbook Worker

Zkopírujte certifikát vytvořený pro každého procesu Hybrid Runbook Worker ve skupině. Spusťte následující skript pro import certifikátu a konfigurace procesu Hybrid Worker používat ověřování podpisu pro sady runbook.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Podepsat vaší sady Runbook pomocí certifikátu

S Hybrid Runbook nakonfigurován pro použití pracovních procesů pouze podepsaný sady runbook, musíte podepsat sady runbook, které se mají použít v procesu Hybrid Runbook Worker. Použijte následující ukázku prostředí PowerShell k podepisování svých runbooků.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Když sada runbook obsahuje podpis, musíte importovat do účtu Automation a publikovat s blok signatury. Informace o importování sad runbook najdete v tématu [import runbooku ze souboru do Azure Automation](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation).

## <a name="troubleshoot"></a>Řešení potíží

Pokud vaše sady runbook nejsou úspěšně dokončit, zkontrolujte na Průvodce odstraňováním potíží [selhání spuštění sady runbook](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).

## <a name="next-steps"></a>Další postup

* Další informace o různých metodách, které můžete použít ke spuštění sady runbook najdete v tématu [spuštění Runbooku ve službě Azure Automation](automation-starting-a-runbook.md).
* Vysvětlení různé způsoby, jak pracovat s Powershellovými runbooky ve službě Azure Automation pomocí textového editoru, najdete v tématu [úpravy sady Runbook ve službě Azure Automation](automation-edit-textual-runbook.md)
