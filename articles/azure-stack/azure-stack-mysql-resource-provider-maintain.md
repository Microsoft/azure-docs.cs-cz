---
title: Zachování poskytovatele prostředků MySQL v Azure zásobníku | Microsoft Docs
description: Zjistěte, jak je možné uchovávat služba Zprostředkovatel prostředků MySQL v Azure zásobníku.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: bc1c96d2f027d459ca20fccb70cd94ac9e5cae94
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130134"
---
# <a name="mysql-resource-provider-maintenance-operations"></a>Operace údržby poskytovatele prostředků MySQL

Zprostředkovatel prostředků MySQL se spouští na uzamčeném virtuálního počítače. Pro povolení operací údržby, musíte aktualizovat zabezpečení virtuálního počítače. Uděláte to pomocí Princip nejnižších nutných oprávnění, můžete použít PowerShell právě dostatečně správy (JEA) koncový bod DBAdapterMaintenance. Instalační balíček zprostředkovatele prostředků obsahuje skript pro tuto operaci.

## <a name="update-the-virtual-machine-operating-system"></a>Aktualizujte operační systém virtuálního počítače

Vzhledem k poskytovateli prostředků běží na *uživatele* virtuálního počítače, je třeba použít vyžadovaných oprav a aktualizací po jejich jste vydání. Balíčky aktualizací systému Windows, které jsou k dispozici jako součást cyklu opravy a aktualizace slouží k instalaci aktualizací pro virtuální počítač.

Aktualizujte zprostředkovatele virtuální počítač pomocí jedné z následujících metod:

- Nainstalujte nejnovější balíček zprostředkovatele prostředků pomocí bitové kopie aktuálně nainstalovanou opravou jádro systému Windows Server 2016.
- Nainstalujte balíček služby Windows Update během instalace nebo aktualizujte zprostředkovatele prostředků.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Aktualizace definic program Windows Defender virtuálního počítače

K aktualizaci definic Defender, postupujte takto:

1. Stažení aktualizace definic program Windows Defender z [Windows Defender definice](https://www.microsoft.com/en-us/wdsi/definitions).

    Na stránce definice posuňte se dolů "Ruční stažení a instalace definice". Stáhněte si soubor 64-bit "Antivirus Windows Defender pro Windows 10 a Windows 8.1".

    Můžete taky použít [tento přímý odkaz](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) na stažení a spuštění fpam fe.exe souboru.

2. Otevřete relaci prostředí PowerShell ke koncovému bodu údržby MySQL prostředků zprostředkovatele adaptér virtuálního počítače.

3. Zkopírujte soubor aktualizace definice adaptéru zprostředkovatele prostředků virtuálního počítače pomocí relace koncového bodu údržby.

4. V relaci prostředí PowerShell údržby, spusťte _aktualizace DBAdapterWindowsDefenderDefinitions_ příkaz.

5. Po instalaci definice doporučujeme odstranit soubor aktualizace definic pomocí _Remove-ItemOnUserDrive)_ příkaz.

**Příklad skriptu prostředí PowerShell pro aktualizaci definice.**

Můžete upravit a spusťte následující skript k aktualizaci definic Defender. Nahraďte hodnoty ve skriptu hodnotami ze svého prostředí.

```powershell
# Set credentials for the local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.  
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate  

# Create a session to the maintenance endpoint.
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance

# Copy the defender update file to the adapter virtual machine.
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\"

# Install the update definitions.
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}

# Cleanup the definitions package file and session.
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession

```

## <a name="secrets-rotation"></a>Otočení tajné klíče

*Tyto pokyny platí pouze pro Azure zásobníku integrované systémy verze 1804 a později. Nepokoušejte rovnou otočit tajné klíče z verze pre-1804 Azure zásobníku.*

Při SQL a MySQL zprostředkovatelé prostředků pomocí Azure zásobníku integraci systémů, můžete otočit následující tajné klíče infrastruktury (nasazení):

- Externí certifikát SSL [během nasazení zadána](azure-stack-pki-certs.md).
- Prostředek zprostředkovatele virtuální počítač heslo místního správce účtu během nasazení zadána.
- Heslo diagnostiky uživatele (dbadapterdiag) zprostředkovatele prostředků.

### <a name="powershell-examples-for-rotating-secrets"></a>Příklady prostředí PowerShell pro výměnu tajné klíče

**Změňte všech tajných klíčů ve stejnou dobu.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd `  
    -VMLocalCredential $localCreds

```

**Změňte heslo k uživatelskému diagnostiky.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd

```

**Změňte heslo k účtu místního Správce virtuálních počítačů.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds

```

**Změňte heslo certifikátu SSL.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd

```

### <a name="secretrotationmysqlproviderps1-parameters"></a>Parametry SecretRotationMySQLProvider.ps1

|Parametr|Popis|
|-----|-----|
|AzCredential|Přihlašovací údaje pro účet Azure zásobníku Správce služby.|
|CloudAdminCredential|Azure zásobníku cloudu správce domény pověření účtu.|
|PrivilegedEndpoint|Koncový bod privilegovaného přístupu Get-AzureStackStampInformation.|
|DiagnosticsUserPassword|Diagnostika heslo uživatelského účtu.|
|VMLocalCredential|Účet místního správce ve virtuálním počítači MySQLAdapter.|
|DefaultSSLCertificatePassword|Výchozí certifikát SSL (* pfx) heslo.|
|DependencyFilesLocalPath|Závislost soubory místní cesta.|
|     |     |

### <a name="known-issues"></a>Známé problémy

**Problém:**<br>
V protokolech otočení tajné klíče nejsou shromažďovány automaticky, pokud tajný otočení skript selže při spuštění.

**Alternativní řešení:**<br>
Použijte rutinu Get-AzsDBAdapterLogs ke shromažďování všech prostředků zprostředkovatele protokoly, včetně AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log, uloží do C:\Logs.

## <a name="collect-diagnostic-logs"></a>Shromažďování diagnostických protokolů

Shromažďování protokolů z uzamčeném virtuálního počítače, můžete koncový bod DBAdapterDiagnostics prostředí PowerShell právě dostatečně správy JEA (). Tento koncový bod nabízí následující příkazy:

- **Get-AzsDBAdapterLog**. Tento příkaz vytvoří balíček zip protokolů diagnostiky zprostředkovatele prostředků a uloží soubor na disku uživatelské relace. Můžete spustit tento příkaz bez parametrů a poslední čtyři hodiny protokolů se shromažďují.

- **Odebrat AzsDBAdapterLog**. Tento příkaz odebere existující balíčky protokolu na poskytovateli prostředků virtuálních počítačů.

### <a name="endpoint-requirements-and-process"></a>Koncový bod požadavcích a postupu

Při instalaci nebo aktualizaci poskytovatele prostředků se vytvoří dbadapterdiag uživatelský účet. Tento účet budete používat ke shromažďování diagnostických protokolů.

>[!NOTE]
>Heslo účtu dbadapterdiag je stejné jako heslo použité pro místního správce ve virtuálním počítači, který se vytvoří během nasazení zprostředkovatele nebo aktualizace.

Použít _DBAdapterDiagnostics_ příkazy, vytvořit vzdálené relace prostředí PowerShell k virtuálnímu počítači zprostředkovatele prostředků a spustit **Get-AzsDBAdapterLog** příkaz.

Nastavit časové rozpětí pro shromáždění protokolů pomocí **FromDate** a **ToDate** parametry. Pokud nezadáte jeden nebo oba tyto parametry, používají se následující výchozí nastavení:

* Datum FromDate je čtyři hodiny před aktuálním časem.
* Do data je aktuální čas.

**Příklad skriptu prostředí PowerShell pro shromažďování protokolů.**

Tento skript je ukázkou, jak shromažďovat diagnostické protokoly od zprostředkovatele prostředků virtuálních počítačů.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'
$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds
        -ConfigurationName DBAdapterDiagnostics

# Sample that captures logs from the previous hour.
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs to the user drive.
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup the logs.
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove-AzsDBAdapterLog}
# Close the session.
$session | Remove-PSSession

```

## <a name="next-steps"></a>Další postup

[Odebrat zprostředkovatele prostředků MySQL](azure-stack-mysql-resource-provider-remove.md)
