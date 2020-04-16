---
title: Spuštění runbooků v hybridním runbooku Azure Automation Worker
description: Tento článek obsahuje informace o spouštění runbooků na počítačích v místním datovém centru nebo poskytovateli cloudu s rolí Hybridní pracovník sady Runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: b65c72e0c65cf9aa84cb614478fbdf78258f3054
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405827"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Spuštění runbooků v hybridním pracovníkovi runbooku

Sady Runbook, které cílí na hybridní pracovník runbooku, obvykle spravují prostředky v místním počítači nebo proti prostředkům v místním prostředí, kde je pracovník nasazen. Runbooky v Azure Automation obvykle spravují prostředky v cloudu Azure. I když se používají jinak, runbooky, které běží v Azure Automation a runbooky, které běží na hybridní runbook worker jsou identické ve struktuře.

Při vytváření runbooku pro spuštění na hybridním pracovníkovi runbooku byste měli upravit a otestovat runbook v počítači, který je hostitelem pracovníka. Hostitelský počítač má všechny moduly prostředí PowerShell a přístup k síti potřebný ke správě a přístupu k místním prostředkům. Jakmile otestujete runbook na počítači hybrid Runbook Worker, můžete ji nahrát do prostředí Azure Automation, kde ji můžete spustit na pracovníka. 

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Oprávnění runbooku pro pracovníka hybridního runbooku

Při přístupu k prostředkům jiné než Azure nemohou runbooky spuštěné na hybridním pracovníkovi runbooku používat mechanismus ověřování, který se obvykle používá v případě, že se sady Runbook ověřují prostředky Azure. Runbook buď poskytuje vlastní ověřování místním prostředkům, nebo konfiguruje ověřování pomocí [spravovaných identit pro prostředky Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Můžete také zadat účet Spustit jako, který poskytne uživatelský kontext pro všechny sady Runbook.

### <a name="runbook-authentication"></a>Ověřování pomocí runbooku

Ve výchozím nastavení jsou sady Runbook spuštěny v místním počítači. V systému Windows jsou spuštěny v kontextu místního **systémového** účtu. Pro Linux, běží v kontextu speciálního uživatelského účtu **nxautomation**. V obou případech musí sady Runbook poskytovat vlastní ověřování prostředkům, ke kterým přistupují.

Datové [zdroje pověření](automation-credentials.md) a [certifikátů](automation-certificates.md) v aplikaci Runbook můžete použít s rutinami, které umožňují zadat pověření, aby se aplikace runbook mohla ověřit na různé prostředky. Následující příklad ukazuje část runbooku, která restartuje počítač. Načte pověření z datového zdroje pověření a název počítače z proměnné ho `Restart-Computer` majetku a potom použije tyto hodnoty s rutinou.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Můžete také použít aktivitu [InlineScript.](automation-powershell-workflow.md#inlinescript) `InlineScript`Umožňuje spouštět bloky kódu v jiném počítači s pověřeními určenými [společným parametrem PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="run-as-account"></a>Účet Spustit jako

Místo toho, aby vaše runbook poskytovat vlastní ověřování pro místní prostředky, můžete zadat spustit jako účet pro skupinu hybridní runbook pracovník. Chcete-li to provést, musíte definovat [prostředek pověření,](automation-credentials.md) který má přístup k místním prostředkům. Tyto prostředky zahrnují úložiště certifikátů a všechny sady Runbook spuštěné pod těmito pověřeními v hybridním pracovníkovi sady Runbook ve skupině.

Uživatelské jméno pověření musí být v jednom z následujících formátů:

* Doména\uživatelské_jméno
* username@domain
* uživatelské jméno (pro místní účty v místním počítači)

Pomocí následujícího postupu určete účet Spustit jako pro skupinu hybridních pracovníků runbooku.

1. Vytvořte [datový zdroj pověření](automation-credentials.md) s přístupem k místním prostředkům.
2. Otevřete účet Automation na webu Azure Portal.
3. Vyberte dlaždici **Hybridní pracovní skupiny** a pak vyberte skupinu.
4. Vyberte **Všechna nastavení**následovaná **nastavením hybridní pracovní skupiny**.
5. Změňte hodnotu **Spustit jako** z **výchozí** na **Vlastní**.
6. Vyberte pověření a klepněte na tlačítko **Uložit**.

### <a name="managed-identities-for-azure-resources"></a><a name="managed-identities-for-azure-resources"></a>Spravované identity pro prostředky Azure

Hybridní pracovníci Runbooku na virtuálních počítačích Azure můžou k ověření prostředků Azure používat spravované identity pro prostředky Azure. Použití spravovaných identit pro prostředky Azure namísto účtů Spustit jako poskytuje výhody, protože nemusíte:

* Exportujte certifikát Spustit jako a importujte jej do hybridního pracovníka sady Runbook.
* Obnovte certifikát používaný účtem Spustit jako.
* Zpracovat objekt připojení Spustit jako v kódu runbooku.

Postupujte podle následujících kroků a použijte spravovanou identitu pro prostředky Azure v hybridním pracovníkovi runbooku.

1. Vytvořte virtuální počítač Azure.
2. Nakonfigurujte spravované identity pro prostředky Azure na virtuálním počítači. Viz [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí portálu Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
3. Udělit virtuálnímu týmu přístup ke skupině prostředků ve Správci prostředků. Viz: [Použití spravované identity přiřazené k virtuálnímu virtuálnímu montovně windows pro přístup ke Správci prostředků](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
4. Nainstalujte pracovníka hybridního runbooku na virtuální počítač. Viz [Nasazení pracovníka hybridního runbooku Windows](automation-windows-hrw-install.md).
5. Aktualizujte runbook tak, aby používal rutinu [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) s parametrem `Identity` k ověření prostředků Azure. Tato konfigurace snižuje potřebu používat účet Spustit jako a provádět přidruženou správu účtu.

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> `Connect-AzAccount -Identity`pracuje pro pracovníka hybridní sady Runbook pomocí systémově přiřazené identity a identity přiřazené jednomu uživateli. Pokud používáte více identit přiřazených uživatelem v pracovníkovi hybridního `AccountId` runbooku, musí vaše runbook zadat parametr pro `Connect-AzAccount` výběr konkrétní identity přiřazené uživateli.

### <a name="automation-run-as-account"></a><a name="runas-script"></a>Automatizace spustit jako účet

Jako součást automatizovaného procesu sestavení pro nasazení prostředků v Azure můžete vyžadovat přístup k místním systémům pro podporu úlohy nebo sady kroků ve vaší sekvenci nasazení. Chcete-li zajistit ověřování proti Azure pomocí spustit jako účet, musíte nainstalovat certifikát účtu Spustit jako.

Následující runbook prostředí PowerShell s názvem **Export-RunAsCertificateToHybridWorker**– exportuje certifikát Run As z vašeho účtu Azure Automation. Runbook stáhne a importuje certifikát do úložiště certifikátů místního počítače na hybridní mnoství, které je připojeno ke stejnému účtu. Jakmile tento krok dokončí, sada Runbook ověří, že se pracovník může úspěšně ověřit do Azure pomocí účtu Spustit jako.

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
>Pro sady Runbook `Add-AzAccount` `Add-AzureRMAccount` prostředí PowerShell `Connect-AzAccount`a jsou aliasy pro . Při prohledávání položek knihovny, `Connect-AzAccount`pokud nevidíte , můžete použít `Add-AzAccount`nebo můžete aktualizovat moduly v účtu automatizace.

Dokončení přípravy účtu Spustit jako:

1. Uložte runbook **Export-RunAsCertificateToHybridWorker** do počítače s příponou **PS1.**
2. Importujte jej do účtu Automation.
3. Upravte runbook a měňte hodnotu `Password` proměnné na vlastní heslo. 
4. Publikujte runbook.
5. Spusťte runbook a zaměřte se na skupinu Hybrid Runbook Worker, která spouští a ověřuje sady Runbook pomocí účtu Spustit jako. 
6. Zkontrolujte datový proud úlohy, abyste zjistili, že hlásí pokus o import certifikátu do místního úložiště počítače a následuje s více řádky. Toto chování závisí na tom, kolik účtů automatizace definujete ve vašem předplatném a stupeň úspěchu ověřování.

## <a name="job-behavior-on-hybrid-runbook-workers"></a>Chování úlohu u hybridních pracovníků runbooku

Azure Automation zpracovává úlohy na hybridních runbookových pracovnících poněkud odlišně od úloh spuštěných v karanténách zabezpečení Azure. Jedním z klíčových rozdílů je, že neexistuje žádné omezení doby trvání úlohy pracovníků runbooku. Sady Runbook spouštěné v karanténách zabezpečení Azure jsou omezeny na tři hodiny z důvodu [spravedlivého podílu](automation-runbook-execution.md#fair-share).

Pro dlouhotrvající runbook, chcete se ujistit, že je odolný vůči možnému restartování, například pokud počítač, který je hostitelem pracovníka restartuje. Pokud se hostitelský počítač hybridního runbooku restartuje, všechny spuštěné úlohy sady Runbook se restartují od začátku nebo z posledního kontrolního bodu pro runbooky pracovního postupu prostředí PowerShell. Po restartování úlohy runbooku více než třikrát, je pozastavena.

Nezapomeňte, že úlohy pro hybridní pracovníky runbooku běží pod místním systémovým účtem v systému Windows nebo v účtu **nxautomation** v systému Linux. Pro Linux, musíte zajistit, že účet **nxautomation** má přístup k umístění, kde jsou uloženy moduly runbook. Při použití rutiny [Install-Module,](/powershell/module/powershellget/install-module) nezapomeňte zadat AllUsers `Scope` pro parametr, aby zajistily, že účet **nxautomation** má přístup. Další informace o PowerShellu na Linuxu najdete [v tématu Známé problémy pro PowerShell na platformách jiných než Windows](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms).

## <a name="starting-a-runbook-on-a-hybrid-runbook-worker"></a>Spuštění runbooku v hybridním pracovníkovi runbooku

[Spuštění sady Runbook v Azure Automation](automation-starting-a-runbook.md) popisuje různé metody pro spuštění sady Runbook. Spuštění sady Runbook v hybridním pracovníkovi sady Runbook používá možnost **Spustit,** která umožňuje zadat název skupiny Hybridní pracovní doba sady Runbook. Když je zadána skupina, jeden z pracovníků v této skupině načte a spustí runbook. Pokud váš runbook neurčuje tuto možnost, Azure Automation spustí runbook jako obvykle.

Když spustíte runbook na webu Azure Portal, zobrazí se vám možnost **Spustit na,** pro kterou můžete vybrat **Azure** nebo **Hybridní worker**. Pokud vyberete **hybridní pracovník**, můžete z rozevíracího seznamu vybrat skupinu Hybridní pracovník runbooku.

Použijte `RunOn` parametr s rutinou [Start-AzAutomationRunbook.](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) Následující příklad používá prostředí Windows PowerShell ke spuštění runbooku s názvem **Test-Runbook** ve skupině hybridních pracovníků runbooku s názvem MyHybridGroup.

```azurepowershell-interactive
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> Pokud máte nainstalovanou starší verzi powershellu, měli byste [si stáhnout nejnovější verzi prostředí PowerShell.](https://azure.microsoft.com/downloads/) Tuto verzi nainstalujte pouze na pracovní stanici, kde spouštíte runbook z prostředí PowerShell. Není nutné jej instalovat do počítače hybridního počítače runbook worker, pokud nemáte v úmyslu spustit runbooky z tohoto počítače.

## <a name="working-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Práce s podepsanými runbooky na pracovníkovi hybridního srun windows

Můžete nakonfigurovat pracovníka hybridní sady Runbook systému Windows tak, aby spouštěl pouze podepsané sady Runbook.

> [!IMPORTANT]
> Jakmile nakonfigurujete hybridní hospodařící pracovník a spouštějte pouze podepsané sady Runbook, sady Runbook, které nebyly podepsány, se v pracovníkovi nespustí.

### <a name="create-signing-certificate"></a>Vytvořit podpisový certifikát

Následující příklad vytvoří certifikát podepsaný svým držitelem, který lze použít k podepisování runbooků. Tento kód vytvoří certifikát a exportuje jej tak, aby ho pracovník hybridnísady Runbook mohl importovat později. Kryptografický otisk je také vrácena pro pozdější použití v odkazování na certifikát.

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

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Importovat certifikát a nakonfigurovat pracovníky pro ověření podpisu

Zkopírujte certifikát, který jste vytvořili, do každého pracovníka hybridního runbooku ve skupině. Spusťte následující skript pro import certifikátu a nakonfigurujte pracovníky tak, aby používali ověření podpisu v sadách Runbook.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Podepsání runbooků pomocí certifikátu

S hybridní mise Runbook Workers nakonfigurovanými tak, aby používali pouze podepsané sady Runbook, musíte podepsat runbooky, které mají být použity u pracovníka hybridního sady Runbook. Pomocí následujícího ukázkového kódu prostředí PowerShell podepište tyto sady Runbook.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Po podepsání sady Runbook je nutné ji importovat do účtu Automation a publikovat ji pomocí bloku podpisu. Informace o importu runbooků najdete [v tématu Import runbooku ze souboru do Azure Automation](manage-runbooks.md#importing-a-runbook).

## <a name="working-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Práce s podepsanými runbooky na hybridním pracovníkovi sady Runbook pro Linux

Aby bylo možné pracovat s podepsanými runbooky, linuxový hybridní runbook worker musí mít [modul gpg](https://gnupg.org/index.html) spustitelný soubor v místním počítači.

> [!IMPORTANT]
> Jakmile nakonfigurujete hybridní hospodařící pracovník a spouštějte pouze podepsané sady Runbook, sady Runbook, které nebyly podepsány, se v pracovníkovi nespustí.

### <a name="create-a-gpg-keyring-and-keypair"></a>Vytvoření klíčenky GPG a páru klíčů

Chcete-li vytvořit klíčenku GPG a pár klíčů, použijte účet **nxautomation** hybridního runbooku.

1. Pomocí aplikace sudo se přihlaste jako účet **nxautomation.**

    ```bash
    sudo su – nxautomation
    ```

2. Jakmile používáte **nxautomation**, vygenerujte dvojici kláves GPG. GPG vás provede kroky. Musíte zadat jméno, e-mailovou adresu, čas vypršení platnosti a přístupové heslo. Pak počkejte, až je dostatek entropie na počítači pro klíč, který má být generován.

    ```bash
    sudo gpg --generate-key
    ```

3. Vzhledem k tomu, že adresář GPG byl generován s sudo, musíte změnit jeho vlastníka na **nxautomation** pomocí následujícího příkazu.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Zpřístupnění klíčenky hybridnímu pracovníkovi runbooku

Po vytvoření klíčenky jej zpřístupníte hybridnímu pracovníkovi runbooku. Upravte soubor nastavení **/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf** tak, aby do `[worker-optional]`části souboru zahrnul následující ukázkový kód .

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Ověření, zda je ověření podpisu zapnuto

Pokud bylo v počítači zakázáno ověřování podpisu, musíte jej zapnout spuštěním následujícího příkazu sudo. Nahraďte `<LogAnalyticsworkspaceId>` iD pracovního prostoru.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Podepsání runbooku

Po nakonfigurování ověření podpisu podepište runbook pomocí následujícího příkazu GPG.

```bash
gpg –-clear-sign <runbook name>
```

Podepsaný soubor Runbook se nazývá ** <runbook name>.asc**.

Podepsané runbooky teď můžete nahrát do Azure Automation a spustit jako běžný runbook.

## <a name="next-steps"></a>Další kroky

* Další informace o metodách spuštění sady Runbook najdete v [tématu Spuštění runbooku v Azure Automation](automation-starting-a-runbook.md).
* Informace o tom, jak používat textový editor pro práci s runbooky PowerShellu v Azure Automation, najdete v [tématu Úprava runbooku v Azure Automation](automation-edit-textual-runbook.md).
* Pokud vaše sady Runbook nedokončí úspěšně, přečtěte si průvodce odstraňováním potíží pro [selhání spuštění sady Runbook](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* Další informace o prostředí PowerShell, včetně jazykových odkazů a výukových modulů, najdete v dokumentu [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
* Odkaz na rutinu prostředí PowerShell naleznete v tématu [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
