---
title: Správa poskytovatele prostředků SQL ve službě Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak můžete spravovat služby poskytovatele prostředků SQL ve službě Azure Stack.
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
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "36300906"
---
# <a name="sql-resource-provider-maintenance-operations"></a>Operace údržby poskytovatele prostředků SQL

Poskytovatele prostředků SQL běží na virtuálním počítači s uzamčená. Povolit operace údržby, budete muset aktualizovat zabezpečení virtuálních počítačů. Chcete-li to udělat principu nejnižších možných oprávnění, můžete použít [Powershellu Just Enough Administration (JEA)](https://docs.microsoft.com/en-us/powershell/jea/overview) koncový bod *DBAdapterMaintenance*. Instalační balíček pro poskytovatele prostředků zahrnuje skript pro tuto operaci.

## <a name="patching-and-updating"></a>Opravy a aktualizace

Poskytovatele prostředků SQL není Údržba v rámci služby Azure Stack, protože se jedná součást doplňku. Společnost Microsoft poskytuje aktualizace u poskytovatele prostředků SQL podle potřeby. Při vydání aktualizovaných adaptér SQL je poskytován skript pro instalaci aktualizace. Tento skript vytvoří nový virtuální počítač, migrace stavu předchozího poskytovatele virtuálních počítačů do nového virtuálního počítače poskytovatele prostředků. Další informace najdete v tématu [aktualizovat poskytovatele prostředků SQL](azure-stack-sql-resource-provider-update.md).

### <a name="provider-virtual-machine"></a>Virtuální počítač poskytovatele

Protože poběží poskytovatele prostředků *uživatele* virtuální počítač, budete muset použít vyžadovaných oprav a aktualizací po jejich uvedení na trh. Můžete balíčky aktualizací Windows, které jsou k dispozici jako součást cyklu aktualizace a opravy aktualizací pro virtuální počítač.

## <a name="backuprestoredisaster-recovery"></a>Zálohování/obnovení nebo zotavení po havárii

 Protože se jedná součást doplňku, poskytovatele prostředků SQL nejsou zálohovány jako součást procesu Azure Stack obchodní kontinuity podnikových procesů po havárii pro zotavení (BCDR). Skripty, poskytneme vám pro tyto operace:

- Zálohování informací o stavu (uložený v účtu úložiště Azure Stack.)
- Obnovení poskytovatele prostředků, pokud plnohodnotných obnovení je povinný.

>[!NOTE]
>Pokud je nutné provést obnovení, databázové servery musí obnovit před obnovením poskytovatele prostředků.

## <a name="updating-sql-credentials"></a>Aktualizují se přihlašovací údaje SQL

Jste zodpovědní za vytváření a Správa účtů správce systému na SQL serverech. Poskytovatel prostředků vyžaduje účet s těmito oprávněními pro správu databází pro uživatele, ale nemusí mít přístup k datům uživatelů. Pokud je potřeba aktualizovat hesla správce systému na serverech SQL, můžete použít rozhraní poskytovatele prostředků správce změnit heslo. Tato hesla se ukládají ve službě Key Vault ve vaší instanci služby Azure Stack.

Chcete-li upravit nastavení, vyberte **Procházet** &gt; **prostředky pro správu** &gt; **servery hostující SQL** &gt; **Přihlášeních SQL** a vyberte uživatelské jméno. Změnu musí v instanci SQL nejprve provést (a všechny repliky, pokud je to nutné.) V části **nastavení**vyberte **heslo**.

![Aktualizovat heslo správce](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="secrets-rotation"></a>Otočení tajných kódů

*Tyto pokyny platí jenom pro Azure Stack integrované systémy verzi 1804 a novější. Nepokoušejte se otočí tajné klíče z verze pre-1804 Azure Stack.*

Při použití poskytovatele prostředků SQL nebo MySQL pomocí služby Azure Stack integrované systémy, můžete otočit tyto tajné kódy infrastruktury (nasazení):

- Externí certifikát SSL [zadali při nasazení](azure-stack-pki-certs.md).
- Prostředků poskytovatele virtuálních počítačů heslo místního správce účet zadaný během nasazení.
- Heslo diagnostických uživatele (dbadapterdiag) poskytovatele prostředků.

### <a name="powershell-examples-for-rotating-secrets"></a>Příklady prostředí PowerShell pro výměnu tajných kódů

**Změňte všechny tajné kódy ve stejnou dobu.**

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

**Diagnostické uživatel heslo změňte.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd
```

**Změna hesla účtu místního Správce virtuálních počítačů.**

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
|AzCredential|Azure přihlašovací údaje účtu správce služby zásobníku.|
|CloudAdminCredential|Azure Stack cloudu domény účtu přihlašovací údaje správce.|
|PrivilegedEndpoint|Privilegované koncový bod pro přístup k Get AzureStackStampInformation.|
|DiagnosticsUserPassword|Diagnostika heslo k uživatelskému účtu.|
|VMLocalCredential|Účet místního správce na virtuálním počítači MySQLAdapter.|
|DefaultSSLCertificatePassword|Výchozí certifikát SSL (* pfx) heslo.|
|DependencyFilesLocalPath|Závislost soubory místní cestu.|
|     |     |

### <a name="known-issues"></a>Známé problémy

**Problém**: tajné kódy rotace protokolů.<br>
Protokoly pro rotaci tajné klíče se neshromažďují automaticky selhání vlastních skriptů tajných kódů otočení při jejím spuštění.

**Alternativní řešení**:<br>
Použijte rutinu Get-AzsDBAdapterLogs shromažďovat všechny protokoly poskytovatele prostředků, včetně AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log uložené v C:\Logs.

## <a name="update-the-virtual-machine-operating-system"></a>Aktualizace operačního systému virtuálního počítače

Použijte jednu z následujících metod k aktualizaci operačního systému virtuálního počítače.

- Nainstalujte nejnovější balíček poskytovatele prostředků pomocí aktuálně opravené image jádru Windows serveru 2016.
- Instalace balíčku aktualizace Windows při instalaci nebo aktualizaci u poskytovatele prostředků.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Aktualizace definic Windows Defenderu virtuálního počítače

Aktualizace definic Windows Defenderu:

1. Stáhnout aktualizace definic Windows Defenderu z [definic Windows Defenderu](https://www.microsoft.com/en-us/wdsi/definitions).

   V definicích aktualizovat stránku, přejděte dolů k položce "Ručně stáhnout a nainstalovat definice". Stáhněte si soubor 64-bit "Antivirus Windows Defenderu pro Windows 10 a Windows 8.1".

   Můžete taky použít [tento přímý odkaz](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) na stažení nebo spuštění souboru fpam fe.exe.

2. Vytvořte relaci Powershellu se koncový bod údržby SQL resource provider adaptéru virtuálního počítače.

3. Zkopírujte soubor aktualizace definic k virtuálnímu počítači pomocí koncový bod relace údržby.

4. V relaci Powershellu údržby, spusťte *aktualizace DBAdapterWindowsDefenderDefinitions* příkazu.

5. Po instalaci definice, doporučujeme vám odstranit soubor aktualizace definic pomocí *odebrat ItemOnUserDrive* příkazu.

**Ukázkový skript Powershellu pro aktualizace definic.**

Můžete upravit a spustit následující skript pro aktualizaci definice Defender. Hodnoty ve skriptu nahraďte hodnotami z vašeho prostředí.

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

Shromažďování protokolů z uzamčená virtuálního počítače, můžete použít PowerShell Just Enough Administration (JEA) koncového bodu *DBAdapterDiagnostics*. Tento koncový bod poskytuje následující příkazy:

- **Get-AzsDBAdapterLog**. Tento příkaz vytvoří balíček zip diagnostické protokoly pro poskytovatele prostředků a uloží soubor na disku uživatelské relace. Můžete spustit tento příkaz bez parametrů a poslední 4 hodiny protokoly se shromažďují.
- **Odebrat AzsDBAdapterLog**. Tento příkaz odebere existující balíčky protokolu v poskytovateli prostředků virtuálního počítače.

### <a name="endpoint-requirements-and-process"></a>Koncový bod požadavky a proces

Při instalaci nebo aktualizaci, poskytovatel prostředků **dbadapterdiag** vytvořený uživatelský účet. Tento účet budete používat ke shromažďování diagnostických protokolů.

>[!NOTE]
>Heslo účtu dbadapterdiag je stejný jako heslo pro místního správce na virtuálním počítači, který se vytvoří během poskytovatele nasazení nebo aktualizace.

Použít *DBAdapterDiagnostics* příkazy, vytvořit vzdálené relace prostředí PowerShell k virtuálnímu počítači poskytovatele prostředků a spustit **Get-AzsDBAdapterLog** příkazu.

Nastavte časové rozpětí pro shromažďování protokolů pomocí **datum FromDate** a **ToDate** parametry. Pokud nechcete zadat jeden nebo oba tyto parametry, se používají následující výchozí hodnoty:

- Datum FromDate je čtyři hodiny před aktuálním časem.
- Do data je aktuální čas.

**Ukázkový skript Powershellu pro shromažďování protokolů.**

Tento skript ukazuje, jak shromažďovat diagnostické protokoly z poskytovatele prostředků virtuálního počítače.

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

[Přidat SQL Server, který hostuje servery](azure-stack-sql-resource-provider-hosting-servers.md)
