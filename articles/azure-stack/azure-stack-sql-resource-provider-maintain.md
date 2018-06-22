---
title: Zachování zprostředkovatele prostředků SQL v Azure zásobníku | Microsoft Docs
description: Zjistěte, jak je možné uchovávat služba Zprostředkovatel prostředků SQL v Azure zásobníku.
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
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: ad899739dab1dc51d64368d2136ab87f73f6f3a0
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300906"
---
# <a name="sql-resource-provider-maintenance-operations"></a>Operace údržby zprostředkovatele prostředků SQL

Zprostředkovatel prostředků SQL se spouští na uzamčeném virtuálního počítače. Pro povolení operací údržby, musíte aktualizovat zabezpečení virtuálního počítače. Chcete-li to provést, pomocí Princip nejnižších nutných oprávnění, můžete použít [prostředí PowerShell právě dostatečně správy (JEA)](https://docs.microsoft.com/en-us/powershell/jea/overview) koncový bod *DBAdapterMaintenance*. Instalační balíček zprostředkovatele prostředků obsahuje skript pro tuto operaci.

## <a name="patching-and-updating"></a>Opravy a aktualizace

Zprostředkovatel prostředků SQL není servis jako součást zásobník Azure, protože se jedná o součást rozšíření. Microsoft poskytuje aktualizace pro poskytovatele prostředků SQL podle potřeby. Až bude vydaná aktualizovaná adaptér SQL, skript je určen k použití aktualizace. Tento skript vytvoří nového zprostředkovatele prostředků virtuální počítač, migrace stavu staré poskytovatele virtuálních počítačů do nového virtuálního počítače. Další informace najdete v tématu [aktualizujte zprostředkovatele prostředků SQL](azure-stack-sql-resource-provider-update.md).

### <a name="provider-virtual-machine"></a>Virtuální počítač poskytovatele

Vzhledem k poskytovateli prostředků běží na *uživatele* virtuálního počítače, je třeba použít vyžadovaných oprav a aktualizací po jejich jste vydání. Balíčky aktualizací systému Windows, které jsou k dispozici jako součást cyklu opravy a aktualizace slouží k instalaci aktualizací pro virtuální počítač.

## <a name="backuprestoredisaster-recovery"></a>Zálohování nebo obnovení nebo zotavení po havárii

 Protože se jedná o součást rozšíření, poskytovatel prostředků SQL se nezálohuje společně v rámci procesu služby Azure zásobníku obchodní kontinuity po havárii obnovení (BCDR). Skripty se poskytují pro následující operace:

- Zálohování stavu informace (uložené v účtu úložiště Azure zásobníku.)
- Obnovení poskytovatele prostředků, pokud je potřeba obnovení úplné zásobníku.

>[!NOTE]
>Pokud je nutné provést obnovení, je nutné obnovit databázové servery, než je obnovit poskytovatele prostředků.

## <a name="updating-sql-credentials"></a>Aktualizuje se přihlašovací údaje SQL

Jste zodpovědní za vytváření a Správa účtů správce systému na serverech SQL. Zprostředkovatel prostředků vyžaduje účet s těchto oprávnění ke správě databáze pro uživatele, ale nepotřebuje přístup k datům uživatelů. Pokud je potřeba aktualizovat hesla správce systému na serverech SQL, můžete změnit heslo správce rozhraní poskytovatele prostředků. Tato hesla jsou uloženy v Key Vault ve vaší instanci Azure zásobníku.

Chcete-li upravit nastavení, vyberte **Procházet** &gt; **prostředky pro správu** &gt; **hostování servery SQL** &gt; **Přihlášeních SQL** a vyberte uživatelské jméno. Změny musí provést na instanci SQL nejprve (a všechny repliky, pokud je to nutné.) V části **nastavení**, vyberte **heslo**.

![Aktualizovat heslo správce](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="secrets-rotation"></a>Otočení tajné klíče

*Tyto pokyny platí pouze pro Azure zásobníku integrované systémy verze 1804 a později. Nepokoušejte rovnou otočit tajné klíče z verze zásobníku pre-1804 Azure.*

Při SQL a MySQL zprostředkovatelé prostředků pomocí Azure zásobníku integraci systémů, můžete otočit následující tajné klíče infrastruktury (nasazení):

- Externí certifikát SSL [během nasazení zadána](azure-stack-pki-certs.md).
- Prostředek zprostředkovatele virtuální počítač heslo místního správce účtu během nasazení zadána.
- Heslo diagnostiky uživatele (dbadapterdiag) zprostředkovatele prostředků.

### <a name="powershell-examples-for-rotating-secrets"></a>Příklady prostředí PowerShell pro výměnu tajné klíče

**Změňte všech tajných klíčů ve stejnou dobu.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd  `
    -VMLocalCredential $localCreds
```

**Změňte heslo k uživatelskému diagnostiky.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd
```

**Změňte heslo k účtu místního Správce virtuálních počítačů.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds
```

**Změňte heslo certifikátu SSL.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd
```

### <a name="secretrotationsqlproviderps1-parameters"></a>Parametry SecretRotationSQLProvider.ps1

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

**Problém**: protokoly otočení tajných klíčů.<br>
V protokolech otočení tajné klíče nejsou shromažďovány automaticky, pokud tajný otočení vlastní skript selže při spuštění.

**Alternativní řešení**:<br>
Použijte rutinu Get-AzsDBAdapterLogs shromažďovat všechny protokoly zprostředkovatele prostředků, včetně AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log, uloží do C:\Logs.

## <a name="update-the-virtual-machine-operating-system"></a>Aktualizujte operační systém virtuálního počítače

Použijte jednu z následujících metod k aktualizaci operačního systému virtuálního počítače.

- Nainstalujte nejnovější balíček zprostředkovatele prostředků pomocí bitové kopie aktuálně nainstalovanou opravou jádro systému Windows Server 2016.
- Nainstalujte balíček služby Windows Update během instalace nebo aktualizujte zprostředkovatele prostředků.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Aktualizace definic program Windows Defender virtuálního počítače

K aktualizaci definic program Windows Defender:

1. Stažení aktualizace definic program Windows Defender z [Windows Defender definice](https://www.microsoft.com/en-us/wdsi/definitions).

   Na definice aktualizace stránky, přejděte dolů na "Ruční stažení a instalace definice". Stáhněte si soubor 64-bit "Antivirus Windows Defender pro Windows 10 a Windows 8.1".

   Můžete taky použít [tento přímý odkaz](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) na stažení a spuštění fpam fe.exe souboru.

2. Vytvořte relaci prostředí PowerShell ke koncovému bodu údržby SQL prostředků zprostředkovatele adaptér virtuálního počítače.

3. Zkopírujte soubor aktualizace definic do virtuálního počítače pomocí relace koncového bodu údržby.

4. V relaci prostředí PowerShell údržby, spusťte *aktualizace DBAdapterWindowsDefenderDefinitions* příkaz.

5. Po instalaci definice doporučujeme odstranit soubor aktualizace definic pomocí *odebrat ItemOnUserDrive* příkaz.

**Příklad skriptu prostředí PowerShell pro aktualizaci definice.**

Můžete upravit a spusťte následující skript k aktualizaci definic Defender. Nahraďte hodnoty ve skriptu hodnotami ze svého prostředí.

```powershell
# Set credentials for local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download the Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.
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

## <a name="collect-diagnostic-logs"></a>Shromažďování diagnostických protokolů

Shromažďování protokolů z uzamčeném virtuálního počítače, můžete koncový bod prostředí PowerShell právě dostatečně správy (JEA) *DBAdapterDiagnostics*. Tento koncový bod nabízí následující příkazy:

- **Get-AzsDBAdapterLog**. Tento příkaz vytvoří balíček zip protokolů diagnostiky zprostředkovatele prostředků a uloží soubor na disku uživatelské relace. Můžete spustit tento příkaz bez parametrů a poslední čtyři hodiny protokolů se shromažďují.
- **Odebrat AzsDBAdapterLog**. Tento příkaz odebere existující balíčky protokolu na poskytovateli prostředků virtuálních počítačů.

### <a name="endpoint-requirements-and-process"></a>Koncový bod požadavcích a postupu

Při instalaci nebo aktualizaci, poskytovatel prostředků **dbadapterdiag** vytvořený uživatelský účet. Tento účet budete používat ke shromažďování diagnostických protokolů.

>[!NOTE]
>Heslo účtu dbadapterdiag je stejné jako heslo použité pro místního správce ve virtuálním počítači, který se vytvoří během nasazení zprostředkovatele nebo aktualizace.

Použít *DBAdapterDiagnostics* příkazy, vytvořit vzdálené relace prostředí PowerShell k virtuálnímu počítači zprostředkovatele prostředků a spustit **Get-AzsDBAdapterLog** příkaz.

Nastavit časové rozpětí pro shromáždění protokolů pomocí **FromDate** a **ToDate** parametry. Pokud nezadáte jeden nebo oba tyto parametry, používají se následující výchozí nastavení:

- Datum FromDate je čtyři hodiny před aktuálním časem.
- Do data je aktuální čas.

**Příklad skriptu prostředí PowerShell pro shromažďování protokolů.**

Tento skript je ukázkou, jak shromažďovat diagnostické protokoly od zprostředkovatele prostředků virtuálních počítačů.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
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
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session.
$session | Remove-PSSession
```

## <a name="next-steps"></a>Další postup

[Přidání hostitelské servery SQL Server](azure-stack-sql-resource-provider-hosting-servers.md)
