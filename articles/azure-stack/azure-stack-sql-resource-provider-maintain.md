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
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: e7ddbe1235b3957a1e0cb7693ee728bfdbf9db6b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295655"
---
# <a name="maintenance-operations"></a>Operace údržby 
Poskytovatel prostředků SQL je uzamčeném dolů virtuálního počítače. Aktualizace zabezpečení prostředků zprostředkovatele virtuálního počítače lze provést prostřednictvím koncového bodu prostředí PowerShell právě dostatečně správy (JEA) _DBAdapterMaintenance_. Skript se poskytuje s instalační balíček RP usnadňuje těchto operací.

## <a name="patching-and-updating"></a>Opravy a aktualizace
Zprostředkovatel prostředků SQL není servis jako součást zásobník Azure, protože se jedná o součást rozšíření. Microsoft bude poskytovat aktualizace zprostředkovatele prostředků SQL podle potřeby. Zprostředkovatel prostředků SQL je vytvořena na _uživatele_ virtuálního počítače v rámci předplatného výchozí zprostředkovatel. Proto je potřeba zadat Windows opravy, proti antivirové atd. Windows aktualizovat balíčky, které jsou k dispozici jako součást cyklu opravy a aktualizace můžete použít pro instalaci aktualizací do virtuálního počítače Windows. Až bude vydaná aktualizovaná adaptér, skript je určen k použití aktualizace. Tento skript vytvoří nový virtuální počítač RP a migrujte nějaký stav, který už máte.

 ## <a name="backuprestoredisaster-recovery"></a>Zálohování nebo obnovení nebo zotavení po havárii
 Zprostředkovatel prostředků SQL není zálohován jako součást procesu Azure zásobníku BC-zotavení po Havárii, protože se jedná o součást rozšíření. Skripty se poskytnout pro usnadnění:
- Zálohování nezbytné stavu informace (uložené v účtu úložiště Azure zásobníku)
- V případě, že bude nezbytné celý zásobník obnovení, obnovení RP.
Databázové servery musí nejprve obnovit (v případě potřeby), než prostředek zprostředkovatele obnovit.

## <a name="updating-sql-credentials"></a>Aktualizuje se přihlašovací údaje SQL
Jste zodpovědní za vytváření a Správa účtů správce systému na serverech SQL. Zprostředkovatel prostředků potřebuje účet s těmito oprávněními ke správě databáze jménem uživatelů – nepotřebuje přístup k datům v těchto databází. Pokud je potřeba aktualizovat hesla sa na serverech SQL, můžete změnit heslo uložené používá zprostředkovatel prostředků funkcí aktualizace rozhraní správce poskytovatele prostředků. Tato hesla jsou uloženy v Key Vault ve vaší instanci Azure zásobníku.

Chcete-li upravit nastavení, klikněte na tlačítko **Procházet** &gt; **prostředky pro správu** &gt; **hostování servery SQL** &gt; **Přihlášeních SQL** a vyberte přihlašovací jméno. Změny musí provést na instanci SQL nejprve (a všechny repliky, a to v případě potřeby). V **nastavení** panelu a potom klikněte na **heslo**.

![Aktualizovat heslo správce](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="secrets-rotation"></a>Otočení tajné klíče 
*Tyto pokyny platí pouze pro Azure zásobníku integrované systémy verze 1804 a později. Nepokoušejte tajný otočení pre-1804 Azure zásobníku verze.*

Při SQL a MySQL zprostředkovatelé prostředků pomocí Azure zásobníku integraci systémů, můžete otočit následující tajné klíče infrastruktury (nasazení):
- Externí certifikát SSL [během nasazení zadána](azure-stack-pki-certs.md).
- Prostředek zprostředkovatele virtuální počítač heslo místního správce účtu během nasazení zadána.
- Heslo diagnostiky uživatele (dbadapterdiag) zprostředkovatele prostředků.

### <a name="powershell-examples-for-rotating-secrets"></a>Příklady prostředí PowerShell pro výměnu tajné klíče

**Změnit všech tajných klíčů ve stejnou dobu**
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

**Pouze změnit heslo uživatele diagnostiky**
```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd 
```

**Změnit heslo účtu místního Správce virtuálních počítačů**
```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds
```

**Změna certifikátu SSL**
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
|DiagnosticsUserPassword|Diagnostika heslo uživatele.|
|VMLocalCredential|Účet místního správce MySQLAdapter virtuálního počítače.|
|DefaultSSLCertificatePassword|Výchozí certifikát SSL (* pfx) heslo.|
|DependencyFilesLocalPath|Závislost soubory místní cesta.|
|     |     |

### <a name="known-issues"></a>Známé problémy
**Problém**: V protokolech otočení tajné klíče nejsou shromažďovány automaticky, pokud tajný otočení vlastní skript selže při spuštění.

**Alternativní řešení**: použijte rutinu Get-AzsDBAdapterLogs shromažďovat všechny protokoly zprostředkovatele prostředků, včetně AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log pod C:\Logs.

## <a name="update-the-virtual-machine-operating-system"></a>Aktualizujte operační systém virtuálního počítače
Aktualizovat virtuální počítač Windows serveru několika způsoby:
- Instalovat nejnovější balíček zprostředkovatele prostředků pomocí bitové kopie aktuálně nainstalovanou opravou jádro systému Windows Server 2016
- Instalovat balíček služby Windows Update během instalace nebo aktualizace RP

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Aktualizace definic program Windows Defender virtuálního počítače
Postupujte podle těchto kroků provedete aktualizaci definice Defender:

1. Stažení aktualizace definic program Windows Defender z [Windows Defender definice](https://www.microsoft.com/en-us/wdsi/definitions).

    Na této stránce, v části "Ruční stažení a instalace definice" stáhnout "antivirové Windows Defender pro Windows 10 a Windows 8.1" 64bitový soubor. 
    
    Přímý odkaz: https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64.

2. Vytvoření relace prostředí PowerShell ke koncovému bodu údržby SQL RP adaptér virtuálního počítače
3. Zkopírujte soubor aktualizace definice k počítači adaptér DB přes koncový bod relace údržby
4. V prostředí PowerShell údržby relace vyvolání _aktualizace DBAdapterWindowsDefenderDefinitions_ příkaz
5. Po instalaci se doporučuje odebrat soubor aktualizace použité definice. Může být odebrán na pomocí relace údržby _Remove-ItemOnUserDrive)_ příkaz.


Tady je ukázkový skript k aktualizaci definic Defender (nahraďte adresu nebo název virtuálního počítače s skutečná hodnota):

```powershell
# Set credentials for the RP VM local admin user
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession 
```


## <a name="collect-diagnostic-logs"></a>Shromažďování diagnostických protokolů
Poskytovatel prostředků SQL je uzamčeném dolů virtuálního počítače. Pokud bude nutné shromáždit protokoly z virtuálního počítače, koncový bod prostředí PowerShell právě dostatečně správy (JEA) _DBAdapterDiagnostics_ slouží pouze k tomuto účelu. Nejsou k dispozici prostřednictvím tento koncový bod dva příkazy:

- **Get-AzsDBAdapterLog**. Připraví balíček zip obsahující RP diagnostické protokoly a vloží ho na jednotce uživatelské relace. Příkaz nelze volat bez parametrů a bude shromažďovat poslední čtyři hodiny protokolů.
- **Odebrat AzsDBAdapterLog**. Vyčistí existující balíčky protokolu na poskytovateli prostředků virtuálních počítačů

Volá se uživatelský účet **dbadapterdiag** se vytvoří během nasazení RP nebo aktualizace pro připojení ke koncovému bodu diagnostiky pro extrahování RP protokoly. Heslo tohoto účtu je stejné jako heslo zadané pro účet místního správce během nasazení nebo aktualizovat.

Pokud chcete používat tyto příkazy, musíte vytvořit vzdálené relace prostředí PowerShell k virtuálnímu počítači zprostředkovatele prostředků a vyvolání příkazu. Volitelně můžete zadat parametry FromDate a do data. Pokud nezadáte alespoň jedna z obou z nich, datum FromDate bude čtyři hodiny před aktuálním časem. a ToDate bude aktuální čas.

Tento ukázkový skript demonstruje použití těchto příkazů:

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample captures logs from the previous one hour
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup logs
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session
$session | Remove-PSSession
```

## <a name="next-steps"></a>Další postup
[Přidání hostitelské servery SQL Server](azure-stack-sql-resource-provider-hosting-servers.md)
