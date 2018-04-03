---
title: Používání databáze SQL v Azure zásobníku | Microsoft Docs
description: Zjistěte, jak můžete nasadit databází SQL jako služba na Azure zásobníku a rychlé kroky k nasazení adaptér zprostředkovatele prostředků systému SQL Server.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: jeffgo
ms.openlocfilehash: d0b287eb61087e90c898aad5273ab5be8c1f98b2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Databáze SQL pro použití v zásobníku Microsoft Azure

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Používat adaptér zprostředkovatele prostředků systému SQL Server jako službu vystavit databází SQL [zásobník Azure](azure-stack-poc.md). Po instalaci poskytovatele prostředků a připojte ho k jedné nebo více instancí systému SQL Server, můžete vytvořit vás a uživatele:
- Databáze pro nativní cloudové aplikace.
- Weby, které jsou založené na SQL.
- Úlohy, které jsou založené na SQL.
Nemáte pokaždé, když zřízení virtuálního počítače (VM) který je hostitelem systému SQL Server.

Zprostředkovatel prostředků nepodporuje všechny možnosti správy databáze z [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Například fondů elastické databáze a schopnost vytočit výkonu databáze nahoru a dolů automaticky nejsou k dispozici. Však nepodporuje Podpora zprostředkovatele prostředků podobné vytvářet, číst, aktualizovat a odstranit operace. Rozhraní API není kompatibilní s databází SQL.

## <a name="sql-resource-provider-adapter-architecture"></a>Architektura adaptér zprostředkovatele prostředků SQL
Zprostředkovatel prostředků se skládá ze tří součástí:

- **Adaptér pro poskytovatele prostředků SQL virtuálních počítačů**, což je virtuální počítač Windows, který spouští služby poskytovatele.
- **Zprostředkovatel prostředků samotné**, která zpracovává požadavky pro zřizování a zpřístupňuje databáze prostředků.
- **Servery, které jsou hostiteli systému SQL Server**, které poskytují kapacitu pro databáze názvem hostitelskými servery.

Musíte vytvořit jednu (nebo více) instance systému SQL Server nebo poskytovat přístup k externí instance systému SQL Server.

> [!NOTE]
> Hostitelské servery, které jsou nainstalovány v Azure zásobníku integrované systémy musí být vytvořeny z předplatného klienta. Nemohou být vytvářeny z předplatného výchozího zprostředkovatele. Musí být vytvořený z portálu klienta nebo z relace prostředí PowerShell příslušné přihlášení. Všechny hostitelské servery jsou fakturovatelné virtuální počítače a musí mít příslušnou licenci. Správce služby můžete být vlastníkem předplatného klienta.

## <a name="deploy-the-resource-provider"></a>Nasazení poskytovatele prostředků

1. Pokud jste tak již neučinili, zaregistrujte vaší development kit a stáhnout bitovou kopii jádro systému Windows Server 2016 Datacenter ke stažení přes správu Marketplace. Je nutné použít bitovou kopii jádro systému Windows Server 2016. Skript můžete také použít k vytvoření [bitové kopie systému Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Nezapomeňte vybrat možnost jádra.)

2. Přihlaste se k hostiteli, který přístup privilegované koncový bod virtuálního počítače.

    - Na Azure zásobníku Development Kit instalace Přihlaste se k fyzického hostitele.

    - V systémech s více uzly hostitele musí být systém, kterým může přistupovat privilegované koncový bod.
    
    >[!NOTE]
    > Systém, kde je spuštěn skript *musí* být systém Windows 10 nebo Windows Server 2016 na nejnovější verzi modulu runtime rozhraní .NET nainstalované. Instalace selže. Azure SDK zásobníku hostitele splňuje tato kritéria.


3. Stáhněte poskytovatele prostředků SQL binární. Pak spusťte Self-Extractor extrahujte obsah do dočasného adresáře.

    >[!NOTE] 
    > Poskytovatel prostředků má minimální odpovídající Azure zásobníku sestavení. Ujistěte se, že jste stáhnout správné binární verze zásobník Azure, který běží.

    | Azure sestavení zásobníku | Instalační program zprostředkovatele prostředků SQL |
    | --- | --- |
    | 1802: 1.0.180302.1 | [SQL RP verze 1.1.18.0](https://aka.ms/azurestacksqlrp1802) |
    | 1712: 1.0.180102.3, 1.0.180103.2 nebo 1.0.180106.1 (více uzly) | [SQL RP verze 1.1.14.0](https://aka.ms/azurestacksqlrp1712) |
    | 1711: 1.0.171122.1 | [SQL RP verze 1.1.12.0](https://aka.ms/azurestacksqlrp1711) |
    | 1710: 1.0.171028.1 | [SQL RP verze 1.1.8.0](https://aka.ms/azurestacksqlrp1710) |
  

4. Pro zásobník sadu Azure SDK se vytvoří certifikát podepsaný svým držitelem v rámci tohoto procesu. Pro integrované systémy je nutné zadat příslušný certifikát.

   Chcete-li přidat vlastní certifikát, umístěte soubor .pfx v **DependencyFilesLocalPath** následujícím způsobem:

    - Certifikát zástupných znaků pro \*.dbadapter.\< oblast\>.\< externí plně kvalifikovaný název domény\> nebo certifikát jedné lokalitě s běžný název sqladapter.dbadapter.\< oblast\>.\< externí plně kvalifikovaný název domény\>.

    - Tento certifikát musí být důvěryhodný. To znamená řetěz certifikátů, musí existovat bez nutnosti zprostředkující certifikáty.

    - V adresáři, který ukazuje parametr DependencyFilesLocalPath může existovat pouze jeden certifikát souboru.

    - Název souboru nesmí obsahovat žádné speciální znaky.


5. Otevřete **nové** konzolu prostředí PowerShell se zvýšenými oprávněními (správce) a změny adresáře, kam jste extrahovali soubory. Použijte nové okno se pokud chcete vyhnout potížím, které by mohly nastat z nesprávné modulů prostředí PowerShell, které jsou již načtena v systému.

6. [Nainstalovat Azure PowerShell verze 1.2.11](azure-stack-powershell-install.md).

7. Spusťte skript DeploySqlProvider.ps1, který provádí tyto kroky:

    - Ukládání certifikáty a další artefakty na účet úložiště v Azure zásobníku.
    - Publikuje Galerie balíčky, abyste mohli nasadit databází SQL v galerii.
    - Publikuje balíček Galerie pro nasazení hostitelskými servery.
    - Virtuální počítač se nasadí pomocí bitové kopie systému Windows Server 2016, který byl vytvořen v kroku 1 a poté nainstaluje poskytovatele prostředků.
    - Zaregistruje místní záznam DNS, který se mapuje na zprostředkovateli prostředků virtuálních počítačů.
    - Zaregistruje poskytovatel prostředků s místní Azure Resource Manager (uživatele a správce).
    - Volitelně nainstaluje jednu aktualizaci Windows během instalace RP

8. Doporučujeme si stáhnout nejnovější jádro systému Windows Server 2016 image z Marketplace správy. Pokud potřebujete nainstalovat aktualizace, můžete umístit jedné. Balíček MSU v cestě místní závislostí. Pokud je více než jeden. MSU nalezeno, skript selže.


Tady je příklad, který můžete spustit z příkazového řádku prostředí PowerShell. (Nezapomeňte změnit informace o účtu a hesla podle potřeby.)

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 parameters
Tyto parametry můžete zadat na příkazovém řádku. Pokud ho použít nechcete, nebo pokud žádné parametr ověření selže, zobrazí se výzva k zadání požadované parametry.

| Název parametru | Popis | Komentář nebo výchozí hodnotu |
| --- | --- | --- |
| **CloudAdminCredential** | Přihlašovací údaje pro správce cloudu potřebné pro přístup k privilegované koncový bod. | _Požadované_ |
| **AzCredential** | Přihlašovací údaje pro účet správce služby Azure zásobníku. Pomocí stejných přihlašovacích údajů jako používat pro nasazování Azure zásobníku. | _Požadované_ |
| **VMLocalCredential** | Přihlašovací údaje pro účet místního správce zprostředkovatele prostředků SQL virtuálních počítačů. | _Požadované_ |
| **PrivilegedEndpoint** | IP adresa nebo název DNS privilegované koncového bodu. |  _Požadované_ |
| **DependencyFilesLocalPath** | Soubor .pfx certifikátu musí být umístěny v tomto adresáři také. | _Volitelné_ (_povinné_ pro více uzly) |
| **DefaultSSLCertificatePassword** | Heslo pro certifikát .pfx. | _Požadované_ |
| **MaxRetryCount** | Počet přístupů, které chcete opakovat každé operace, pokud dojde k selhání.| 2 |
| **RetryDuration** | Interval časového limitu mezi opakovanými pokusy, v sekundách. | 120 |
| **Odinstalace** | Odebere poskytovatele prostředků a všechny přidružené prostředky (viz následující poznámky k). | Ne |
| **Režim DebugMode** | Brání automatické čištění při selhání. | Ne |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Ověření nasazení pomocí portálu Azure zásobníku

> [!NOTE]
>  Po dokončení skriptu instalace musíte aktualizovat na portálu najdete v okně správce.


1. Přihlaste se k portálu pro správu jako správce služeb.

2. Ověřte, zda nasazení proběhla úspěšně. Přejděte na **skupiny prostředků**. Vyberte **systému.\< umístění\>.sqladapter** skupinu prostředků. Ověřte, zda všechny čtyři nasazení proběhla úspěšně.

      ![Ověření nasazení poskytovatele prostředků SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="update-the-sql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>Aktualizace adaptéru pro zprostředkovatele prostředků SQL (více uzly jen sestavení 1710 a novější)
Nový adaptér zprostředkovatele prostředků SQL může vydala, když jsou aktualizovány sestavení Azure zásobníku. Zatímco existující adaptér fungovat, doporučujeme aktualizovat na nejnovější verzi co nejdříve. Aktualizace musí být nainstalované v pořadí: nedá přeskočit verze (najdete v tabulce v kroku 3 tohoto [nasazení poskytovatele prostředků](#deploy-the-resource-provider)).

Aktualizovat zprostředkovatele prostředků použijete *UpdateSQLProvider.ps1* skriptu. Proces je podobný procesu použitý k instalaci zprostředkovatele prostředků, jak je popsáno v [nasazení poskytovatele prostředků](#deploy-the-resource-provider) tohoto článku. Skript je součástí stahování poskytovatele prostředků.

*UpdateSQLProvider.ps1* skript vytvoří nový virtuální počítač s nejnovější kód zprostředkovatele prostředků a migraci nastavení z původního virtuálního počítače do nového virtuálního počítače. Zahrnout nastavení, které provádějí migraci databáze a hostování informace o serveru, a potřebné DNS záznam.

Skript vyžaduje použití stejné argumenty, které jsou popsány DeploySqlProvider.ps1 skriptu. Zadejte certifikát zde také. 

Doporučujeme si stáhnout nejnovější jádro systému Windows Server 2016 image z Marketplace správy. Pokud potřebujete nainstalovat aktualizace, můžete umístit jedné. Balíček MSU v cestě místní závislostí. Pokud je více než jeden. MSU nalezeno, skript selže.

Následuje příklad *UpdateSQLProvider.ps1* skript, který můžete spustit z příkazového řádku prostředí PowerShell. Nezapomeňte změnit informace o účtu a hesla podle potřeby: 

> [!NOTE]
> Proces aktualizace se vztahuje pouze na integrované systémy.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="updatesqlproviderps1-parameters"></a>UpdateSQLProvider.ps1 parameters
Tyto parametry můžete zadat na příkazovém řádku. Pokud ho použít nechcete, nebo pokud žádné parametr ověření selže, zobrazí se výzva k zadání požadované parametry.

| Název parametru | Popis | Komentář nebo výchozí hodnotu |
| --- | --- | --- |
| **CloudAdminCredential** | Přihlašovací údaje pro správce cloudu potřebné pro přístup k privilegované koncový bod. | _Požadované_ |
| **AzCredential** | Přihlašovací údaje pro účet správce služby Azure zásobníku. Pomocí stejných přihlašovacích údajů, které jste použili pro nasazení Azure zásobníku. | _Požadované_ |
| **VMLocalCredential** | Přihlašovací údaje pro účet místního správce zprostředkovatele prostředků SQL virtuálních počítačů. | _Požadované_ |
| **PrivilegedEndpoint** | IP adresa nebo název DNS privilegované koncového bodu. |  _Požadované_ |
| **DependencyFilesLocalPath** | Soubor .pfx certifikátu musí být umístěny v tomto adresáři také. | _Volitelné_ (_povinné_ pro více uzly) |
| **DefaultSSLCertificatePassword** | Heslo pro certifikát .pfx. | _Požadované_ |
| **MaxRetryCount** | Počet přístupů, které chcete opakovat každé operace, pokud dojde k selhání.| 2 |
| **RetryDuration** |Interval časového limitu mezi opakovanými pokusy, v sekundách. | 120 |
| **Odinstalace** | Odebere poskytovatele prostředků a všechny přidružené prostředky (viz následující poznámky k). | Ne |
| **Režim DebugMode** | Brání automatické čištění při selhání. | Ne |


## <a name="collect-diagnostic-logs"></a>Shromažďování diagnostických protokolů
Poskytovatel prostředků SQL je uzamčeném dolů virtuálního počítače. Pokud bude nutné shromáždit protokoly z virtuálního počítače, koncový bod prostředí PowerShell právě dostatečně správy (JEA) _DBAdapterDiagnostics_ slouží pouze k tomuto účelu. Nejsou k dispozici prostřednictvím tento koncový bod dva příkazy:

* Get-AzsDBAdapterLog - připraví balíček zip obsahující RP diagnostické protokoly a vloží ho na jednotce uživatelské relace. Příkaz nelze volat bez parametrů a bude shromažďovat poslední čtyři hodiny protokolů.
* Remove-AzsDBAdapterLog - vyčistí existující balíčky protokolu na poskytovateli prostředků virtuálních počítačů

Volá se uživatelský účet _dbadapterdiag_ se vytvoří během nasazení RP nebo aktualizace pro připojení ke koncovému bodu diagnostiky pro extrahování RP protokoly. Heslo tohoto účtu je stejné jako heslo zadané pro účet místního správce během nasazení nebo aktualizovat.

Pokud chcete používat tyto příkazy, musíte vytvořit vzdálené relace prostředí PowerShell k virtuálnímu počítači zprostředkovatele prostředků a vyvolání příkazu. Volitelně můžete zadat parametry FromDate a do data. Pokud nezadáte alespoň jedna z obou z nich, datum FromDate bude čtyři hodiny před aktuálním časem. a ToDate bude aktuální čas.

Tento ukázkový skript demonstruje použití těchto příkazů:

```
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<see above>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, $diagnosticsUserPassword)
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

## <a name="maintenance-operations-integrated-systems"></a>Operace údržby (integrované systémy)
Poskytovatel prostředků SQL je uzamčeném dolů virtuálního počítače. Aktualizace zabezpečení prostředků zprostředkovatele virtuálního počítače lze provést prostřednictvím koncového bodu prostředí PowerShell právě dostatečně správy (JEA) _DBAdapterMaintenance_.

Skript se poskytuje s instalační balíček RP usnadňuje těchto operací.

### <a name="update-the-virtual-machine-operating-system"></a>Aktualizujte operační systém virtuálního počítače
Aktualizovat virtuální počítač Windows serveru několika způsoby:
* Instalovat nejnovější balíček zprostředkovatele prostředků pomocí bitové kopie aktuálně nainstalovanou opravou jádro systému Windows Server 2016
* Instalovat balíček služby Windows Update během instalace nebo aktualizace RP


### <a name="update-the-virtual-machine-windows-defender-definitions"></a>Aktualizace definic program Windows Defender virtuálního počítače

Postupujte podle těchto kroků provedete aktualizaci definice Defender:

1. Stažení aktualizace definic program Windows Defender z [definic programu Windows Defender](https://www.microsoft.com/en-us/wdsi/definitions)

    Na této stránce, v části "Ruční stažení a instalace definice" stáhnout "antivirové Windows Defender pro Windows 10 a Windows 8.1" 64bitový soubor. 
    
    Přímý odkaz: https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64

2. Vytvoření relace prostředí PowerShell ke koncovému bodu údržby SQL RP adaptér virtuálního počítače
3. Zkopírujte soubor aktualizace definice k počítači adaptér DB přes koncový bod relace údržby
4. V prostředí PowerShell údržby relace vyvolání _aktualizace DBAdapterWindowsDefenderDefinitions_ příkaz
5. Po instalaci se doporučuje odebrat soubor aktualizace použité definice. Může být odebrán na pomocí relace údržby _Remove-ItemOnUserDrive)_ příkaz.


Tady je ukázkový skript k aktualizaci definic Defender (nahraďte adresu nebo název virtuálního počítače s skutečná hodnota):

```
# Set credentials for the diagnostic user
$diagPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$diagCreds = New-Object System.Management.Automation.PSCredential `
    ("dbadapterdiag", $vmLocalAdminPass)$diagCreds = Get-Credential

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "XX.XX.XX.XX"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"
 
# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64 `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $diagCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\mpam-fe.exe"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinitions -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```

## <a name="remove-the-sql-resource-provider-adapter"></a>Odeberte adaptér zprostředkovatele prostředků SQL

Odebrat zprostředkovatele prostředků, je nutné nejprve odebrat všechny závislosti.

1. Ujistěte se, že máte původní balíček nasazení, který jste stáhli pro tuto verzi adaptér zprostředkovatele prostředků SQL.

2. Od zprostředkovatele prostředků je třeba odstranit všechny uživatelské databáze. (Odstranění uživatelských databází neodstraní data.) Tato úloha by měla provést sami uživatelé.

3. Správce musí odstranit hostitelskými servery z adaptéru zprostředkovatele prostředků SQL.

4. Správce musí odstranit všechny plány, které odkazují na adaptéru zprostředkovatele prostředků SQL.

5. Správce musí odstranit všechny identifikátory SKU a kvóty, které jsou spojeny s adaptérem zprostředkovatele prostředků SQL.

6. Spusťte znovu skript nasazení s následující prvky:
    - -Odinstalovat parametr
    - Koncové body Azure Resource Manager
    - DirectoryTenantID
    - Přihlašovací údaje k účtu správce služby


## <a name="next-steps"></a>Další postup

[Přidání hostitelské servery](azure-stack-sql-resource-provider-hosting-servers.md) a [vytváření databází](azure-stack-sql-resource-provider-databases.md).

Zkuste jiné [PaaS služby](azure-stack-tools-paas-services.md) jako [poskytovatele prostředků serveru MySQL](azure-stack-mysql-resource-provider-deploy.md) a [poskytovatele prostředků App Services](azure-stack-app-service-overview.md).
