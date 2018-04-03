---
title: Používání databází MySQL jako PaaS v zásobníku Azure | Microsoft Docs
description: Zjistěte, jak můžete nasadit MySQL zprostředkovatel prostředků a poskytovat databází MySQL jako službu v Azure zásobníku.
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
ms.openlocfilehash: 66e1d5691b431be0c3d040570b13e8d16b1669ef
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Používání databází MySQL v zásobníku Microsoft Azure

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Můžete nasadit poskytovatele prostředků MySQL v Azure zásobníku. Poté, co nasadíte poskytovatele prostředků, můžete vytvořit MySQL servery a databáze pomocí šablony nasazení Azure Resource Manager. Databáze MySQL můžete zadat taky jako služba. 

Databáze MySQL, které jsou společné pro webové servery, podporují mnoho platformy webu. Například po nasazení poskytovatele prostředků, vytvoříte weby WordPress z webové aplikace platformy jako služba (PaaS) rozšíření pro Azure zásobníku.

Pokud chcete nasadit zprostředkovatele databáze MySQL na systém, který nemá přístup k Internetu, zkopírujte soubor [mysql konektor net 6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi) do místní sdílené složky. Po zobrazení výzvy ho zadejte název této sdílené složky. Je nutné nainstalovat moduly Azure a Azure PowerShell zásobníku.


## <a name="mysql-server-resource-provider-adapter-architecture"></a>Architektura adaptér zprostředkovatele prostředků serveru MySQL

Zprostředkovatel prostředků se skládá ze tří součástí:

- **MySQL prostředků zprostředkovatele adaptér virtuálního počítače**, což je virtuální počítač Windows, který běží služby poskytovatele.

- **Zprostředkovatel prostředků samotné**, která zpracovává požadavky pro zřizování a zpřístupňuje databáze prostředků.

- **Servery, které hostují MySQL Server**, které poskytují kapacitu pro databáze, které se nazývají hostitelské servery.

Tato verze již vytváří instance databáze MySQL. To znamená, že je potřeba vytvořit sami nebo poskytovat přístup k externí instance SQL. Přejděte [galerii pro rychlý start Azure zásobníku](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) pro šablonu příklad, který můžete:
- Vytvoření databáze MySQL serveru za vás.
- Stáhnout a nasadit MySQL Server z Azure Marketplace.

> [!NOTE]
> Hostitelské servery, které jsou nainstalovány v Azure zásobníku integrované systémy musí být vytvořeny z předplatného klienta. Nemohou být vytvářeny z předplatného výchozího zprostředkovatele. Musí být vytvořený z portálu klienta nebo z relace prostředí PowerShell příslušné přihlášení. Všechny hostitelské servery jsou fakturovatelné virtuální počítače a musí mít příslušnou licenci. Správce služby můžete být vlastníkem předplatného klienta.

### <a name="required-privileges"></a>Požadovaná oprávnění
Systémový účet musí mít následující oprávnění:

1.  Databáze: Vytvořit, vyřadit
2.  Přihlášení: Vytvořit, nastavit, vyřadit, udělit, odvolat

## <a name="deploy-the-resource-provider"></a>Nasazení poskytovatele prostředků

1. Pokud jste tak již neučinili, zaregistrujte vaší development kit a stáhnout bitovou kopii jádro systému Windows Server 2016 Datacenter ke stažení přes správu Marketplace. Je nutné použít bitovou kopii jádro systému Windows Server 2016. Skript můžete také použít k vytvoření [bitové kopie systému Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Nezapomeňte vybrat možnost jádra.)


2. Přihlaste se k hostiteli, který přístup privilegované koncový bod virtuálního počítače.

    - Instalace Azure SDK Přihlaste se na fyzickém hostiteli. 
    - V systémech s více uzly hostitele musí být systém, kterým může přistupovat privilegované koncový bod.
    
    >[!NOTE]
    > Systém, na kterém je spuštěn skript *musí* být systém Windows 10 nebo Windows Server 2016 na nejnovější verzi modulu runtime rozhraní .NET nainstalované. Instalace selže. Azure SDK zásobníku hostitele splňuje tato kritéria.
    

3. Stáhněte poskytovatele prostředků MySQL binární. Pak spusťte Self-Extractor extrahujte obsah do dočasného adresáře.

    >[!NOTE] 
    > Poskytovatel prostředků má minimální odpovídající Azure zásobníku sestavení. Ujistěte se, že jste stáhnout správné binární verze zásobník Azure, který běží.

    | Azure sestavení zásobníku | Instalační program MySQL RP |
    | --- | --- |
    | 1802: 1.0.180302.1 | [MySQL RP verze 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) |
    | 1712: 1.0.180102.3 nebo 1.0.180106.1 (více uzly) | [MySQL RP version 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) |
    | 1711: 1.0.171122.1 | [MySQL RP version 1.1.12.0](https://aka.ms/azurestackmysqlrp1711) |
    | 1710: 1.0.171028.1 | [MySQL RP version 1.1.8.0](https://aka.ms/azurestackmysqlrp1710) |

4.  Pro sadu SDK Azure se vytvoří certifikát podepsaný svým držitelem v rámci tohoto procesu. Pro více uzly je nutné zadat příslušný certifikát.

    Pokud potřebujete přidat vlastní certifikát, umístit soubor .pfx v **DependencyFilesLocalPath** , která splňuje následující kritéria:

    - Certifikát zástupných znaků pro \*.dbadapter.\< oblast\>.\< externí plně kvalifikovaný název domény\> nebo certifikát jedné lokalitě s běžný název mysqladapter.dbadapter.\< oblast\>.\< externí plně kvalifikovaný název domény\>.

    - Tento certifikát musí být důvěryhodný. To znamená řetěz certifikátů, musí existovat bez nutnosti zprostředkující certifikáty.

    - V DependencyFilesLocalPath existuje pouze jeden certifikát souboru.
    
    - Název souboru nesmí obsahovat žádné speciální znaky ani mezery.


5. Otevřete **nové** konzolu prostředí PowerShell se zvýšenými oprávněními (správce). Pak přejděte do adresáře, které jste extrahovali soubory. Použijte nové okno se pokud chcete vyhnout potížím, které by mohly nastat z nesprávné modulů prostředí PowerShell, které jsou již načtena v systému.

6. [Nainstalovat Azure PowerShell verze 1.2.11](azure-stack-powershell-install.md).

7. Spusťte skript `DeployMySqlProvider.ps1`.

Skript provede tyto kroky:

* Stáhne MySQL konektor binárního souboru (to se dá zajistit offline).
* Ukládání certifikáty a další artefakty na účet úložiště v Azure zásobníku.
* Publikuje Galerie balíčky, abyste mohli nasadit databází SQL v galerii.
* Publikuje balíček Galerie pro nasazení hostitelskými servery.
* Virtuální počítač se nasadí pomocí bitové kopie systému Windows Server 2016, který byl vytvořen v kroku 1. Nainstaluje taky poskytovatele prostředků.
* Zaregistruje místní záznam DNS, který se mapuje na zprostředkovateli prostředků virtuálních počítačů.
* Zaregistruje poskytovatel prostředků s místní Azure Resource Manager (klient a správce).


Můžete:
- Zadejte požadované parametry na příkazovém řádku.
- Spustit bez parametrů a pak je po zobrazení výzvy zadejte.

Tady je příklad, který můžete spustit z příkazového řádku prostředí PowerShell. Nezapomeňte změnit informace o účtu a hesla podle potřeby:


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
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Run the installation script from the folder where you extracted the installation files.
# Find the ERCS01 IP address first, and make sure the certificate
# file is in the specified directory.
. $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense

 ```


### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider.ps1 parameters
Tyto parametry můžete zadat na příkazovém řádku. Pokud ho použít nechcete, nebo pokud žádné parametr ověření selže, zobrazí se výzva k zadání požadované parametry.

| Název parametru | Popis | Komentář nebo výchozí hodnotu |
| --- | --- | --- |
| **CloudAdminCredential** | Přihlašovací údaje pro správce cloudu potřebné pro přístup k privilegované koncový bod. | _Požadované_ |
| **AzCredential** | Přihlašovací údaje pro účet správce služby Azure zásobníku. Pomocí stejných přihlašovacích údajů, které jste použili pro nasazení Azure zásobníku. | _Požadované_ |
| **VMLocalCredential** | Přihlašovací údaje pro účet místního správce MySQL zprostředkovatele prostředků virtuálních počítačů. | _Požadované_ |
| **PrivilegedEndpoint** | IP adresa nebo název DNS privilegované koncového bodu. |  _Požadované_ |
| **DependencyFilesLocalPath** | Cesta k místní sdílené složky, která obsahuje [mysql konektor net 6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi). Pokud zadáte jednu z těchto cest, soubor certifikátu musí být umístěny v tomto adresáři také. | _Volitelné_ (_povinné_ pro více uzly) |
| **DefaultSSLCertificatePassword** | Heslo pro certifikát .pfx. | _Požadované_ |
| **MaxRetryCount** | Počet přístupů, které chcete opakovat každé operace, pokud dojde k selhání.| 2 |
| **RetryDuration** | Interval časového limitu mezi opakovanými pokusy, v sekundách. | 120 |
| **Odinstalace** | Odebere poskytovatele prostředků a všechny přidružené prostředky (viz následující poznámky k). | Ne |
| **Režim DebugMode** | Brání automatické čištění při selhání. | Ne |
| **AcceptLicense** | Přeskočí řádku tak, aby přijímal GPL licence.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |


## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Ověření nasazení pomocí portálu Azure zásobníku

> [!NOTE]
>  Po dokončení skriptu instalace musíte aktualizovat na portálu najdete v okně správce.


1. Přihlaste se k portálu pro správu jako správce služeb.

2. Ověřte, zda nasazení proběhla úspěšně. Přejděte na **skupiny prostředků**a pak vyberte **systému.\< umístění\>.mysqladapter** skupinu prostředků. Ověřte, zda všechny čtyři nasazení proběhla úspěšně.

      ![Ověření nasazení MySQL RP](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png)

## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>Zadejte kapacitu připojením k hostitelskému serveru MySQL

1. Přihlaste se k portálu Azure zásobníku jako správce služby

2. Vyberte **prostředky pro správu** > **MySQL hostitelské servery** > **+ přidat**.

    Na **MySQL hostování servery** okně poskytovatele prostředků MySQL serveru můžete připojit k skutečné instancí MySQL serveru, které slouží jako zprostředkovatel prostředků back-end.

    ![Hostitelské servery](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. Zadejte podrobnosti připojení vaší instance serveru MySQL. Ujistěte se, že poskytují plně kvalifikovaný název domény (FQDN) nebo platná IPv4 adresa a není krátký název virtuálního počítače. Tato instalace již poskytuje výchozí instance databáze MySQL. Velikost, která je k dispozici pomáhá spravovat kapacity databáze poskytovatele prostředků. Mělo by být blíží fyzické kapacitě databázového serveru.

    > [!NOTE]
    >Pokud instance databáze MySQL byla přístupná pomocí klienta a správce Azure Resource Manager, mohou být umístěny pod kontrolou zprostředkovatele prostředků. Instance databáze MySQL *musí* přidělit výhradně k poskytovateli prostředků.

4. Při přidávání serverů, musíte je přiřadit k nové nebo existující SKU umožňující rozdílů mezi nabídek služeb.
  Například můžete mít instanci enterprise poskytuje:
    - kapacita databáze
    - Automatické zálohování
    - Rezervovat vysoce výkonné servery pro jednotlivá oddělení
 

Název SKU by měla odpovídat vlastnosti tak, aby klienti můžete umístit své databáze správně. Všechny hostitelské servery v SKU musí mít stejné funkce.

![Vytvoření databáze MySQL SKU](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)


>[!NOTE]
> SKU může trvat až jednu hodinu, mají být zobrazeny v portálu. Databázi nelze vytvořit, dokud nebude vytvořen verze SKU.


## <a name="test-your-deployment-by-creating-your-first-mysql-database"></a>Otestujte nasazení tak, že vytvoříte svoji první databázi MySQL


1. Přihlaste se k portálu Azure zásobníku jako správce služby

2. Vyberte **+ nový** > **Data + úložiště** > **databáze MySQL**.

3. Zadejte podrobnosti databáze.

    ![Vytvoření databáze MySQL testu](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. Vyberte SKU.

    ![Vyberte SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. Vytvořte nastavení přihlášení. Můžete znovu použít existující nastavení přihlášení nebo vytvořte novou. Toto nastavení obsahuje uživatelské jméno a heslo pro databázi.

    ![Vytvořte nové přihlašovací údaje databáze](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    Řetězec připojení zahrnuje název skutečné databázového serveru. Zkopírujte jej z portálu.

    ![Získání připojovacího řetězce pro databázi MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> Délka uživatelská jména nesmí překročit 32 znaků v MySQL 5.7. Ve starších edic ho nesmí překročit 16 znaků.


## <a name="add-capacity"></a>Přidat kapacity

Přidejte kapacity přidáním dalších serverů MySQL na portálu Azure zásobníku. Další servery, které lze přidat do nové nebo existující SKU. Ujistěte se, že vlastnosti serveru jsou stejné.


## <a name="make-mysql-databases-available-to-tenants"></a>Databáze MySQL zpřístupnit klientům
Vytvořte plány a nabízí databází MySQL zpřístupnit pro klienty. Například přidejte službu Microsoft.MySqlAdapter, přidat kvótu a tak dále.

![Vytvoření plánu a nabídky zahrnout databáze](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="update-the-administrative-password"></a>Aktualizace hesla pro správu
Heslo můžete upravit tak, že první změníte na instanci serveru MySQL. Vyberte **prostředky pro správu** > **MySQL hostitelské servery**. Pak vyberte hostitelský server. V **nastavení** panel, vyberte **heslo**.

![Aktualizovat heslo správce](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="update-the-mysql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>Aktualizace adaptéru pro zprostředkovatele prostředků MySQL (více uzly jen sestavení 1710 a novější)
Nový adaptér zprostředkovatele prostředků SQL může vydala, když jsou aktualizovány sestavení Azure zásobníku. Zatímco existující adaptér fungovat, doporučujeme aktualizovat na nejnovější verzi co nejdříve. 

Aktualizovat zprostředkovatele prostředků použijete *UpdateMySQLProvider.ps1* skriptu. Proces je podobný procesu použitý k instalaci zprostředkovatele prostředků, jak je popsáno v [nasazení poskytovatele prostředků](#deploy-the-resource-provider) tohoto článku. Skript je součástí stahování poskytovatele prostředků.

*UpdateMySQLProvider.ps1* skript vytvoří nový virtuální počítač s nejnovější kód zprostředkovatele prostředků a migraci nastavení z původního virtuálního počítače do nového virtuálního počítače. Zahrnout nastavení, které provádějí migraci databáze a hostování informace o serveru, a potřebné DNS záznam.

Skript vyžaduje použití stejné argumenty, které jsou popsány DeployMySqlProvider.ps1 skriptu. Zadejte certifikát zde také. 

Následuje příklad *UpdateMySQLProvider.ps1* skript, který můžete spustit z příkazového řádku prostředí PowerShell. Nezapomeňte změnit informace o účtu a hesla podle potřeby: 

> [!NOTE]
> Proces aktualizace se vztahuje pouze na integrované systémy.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense
 ```

### <a name="updatemysqlproviderps1-parameters"></a>UpdateMySQLProvider.ps1 parameters
Tyto parametry můžete zadat na příkazovém řádku. Pokud to neuděláte, nebo pokud žádné ověření parametru selže, budete vyzváni k poskytování požadované parametry.

| Název parametru | Popis | Komentář nebo výchozí hodnotu |
| --- | --- | --- |
| **CloudAdminCredential** | Přihlašovací údaje pro správce cloudu potřebné pro přístup k privilegované koncový bod. | _Požadované_ |
| **AzCredential** | Přihlašovací údaje pro účet správce služby Azure zásobníku. Pomocí stejných přihlašovacích údajů jako používat pro nasazování Azure zásobníku. | _Požadované_ |
| **VMLocalCredential** |Přihlašovací údaje pro účet místního správce zprostředkovatele prostředků SQL virtuálních počítačů. | _Požadované_ |
| **PrivilegedEndpoint** | IP adresa nebo název DNS privilegované koncového bodu. |  _Požadované_ |
| **DependencyFilesLocalPath** | Soubor .pfx certifikátu musí být umístěny v tomto adresáři také. | _Volitelné_ (_povinné_ pro více uzly) |
| **DefaultSSLCertificatePassword** | Heslo pro certifikát .pfx | _Požadované_ |
| **MaxRetryCount** | Počet přístupů, které chcete opakovat každé operace, pokud dojde k selhání.| 2 |
| **RetryDuration** | Interval časového limitu mezi opakovanými pokusy, v sekundách. | 120 |
| **Odinstalace** | Odeberte zprostředkovatele prostředků a všechny přidružené prostředky (viz následující poznámky k). | Ne |
| **Režim DebugMode** | Brání automatické čištění při selhání. | Ne |
| **AcceptLicense** | Přeskočí řádku tak, aby přijímal GPL licence.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |


## <a name="collect-diagnostic-logs"></a>Shromažďování diagnostických protokolů
Poskytovatel prostředků MySQL je uzamčeném dolů virtuálního počítače. Pokud bude nutné shromáždit protokoly z virtuálního počítače, koncový bod prostředí PowerShell právě dostatečně správy (JEA) _DBAdapterDiagnostics_ slouží pouze k tomuto účelu. Nejsou k dispozici prostřednictvím tento koncový bod dva příkazy:

* Get-AzsDBAdapterLog - připraví balíček zip obsahující RP diagnostické protokoly a vloží ho na jednotce uživatelské relace. Příkaz nelze volat bez parametrů a bude shromažďovat poslední čtyři hodiny protokolů.
* Remove-AzsDBAdapterLog - vyčistí existující balíčky protokolu na poskytovateli prostředků virtuálních počítačů

Volá se uživatelský účet _dbadapterdiag_ se vytvoří během nasazení RP nebo aktualizace pro připojení ke koncovému bodu diagnostiky pro extrahování RP protokoly. Heslo tohoto účtu je stejné jako heslo zadané pro účet místního správce během nasazení nebo aktualizovat.

Používat tyto příkazy, musíte vytvořit vzdálené relace prostředí PowerShell k virtuálnímu počítači zprostředkovatele prostředků a vyvolání příkazu. Volitelně můžete zadat parametry FromDate a do data. Pokud nezadáte alespoň jedna z obou z nich, datum FromDate bude čtyři hodiny před aktuálním časem. a ToDate bude aktuální čas.

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
Poskytovatel prostředků MySQL je uzamčeném dolů virtuálního počítače. Aktualizace zabezpečení prostředků zprostředkovatele virtuálního počítače lze provést prostřednictvím koncového bodu prostředí PowerShell právě dostatečně správy (JEA) _DBAdapterMaintenance_.

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

2. Vytvoření relace prostředí PowerShell ke koncovému bodu údržby MySQL RP adaptér virtuálního počítače
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


## <a name="remove-the-mysql-resource-provider-adapter"></a>Odeberte adaptér zprostředkovatele prostředků MySQL

Odebrat zprostředkovatele prostředků, je nutné nejprve odebrat všechny závislosti.

1. Ujistěte se, že máte původní balíček nasazení, který jste stáhli pro tuto verzi zprostředkovatele prostředků.

2. Od zprostředkovatele prostředků je třeba odstranit všechny databáze klienta. (Odstranit databáze klienta neodstraní data.) Tato úloha by měli provádět klientů sami.

3. Klienti musí registraci z oboru názvů.

4. Správce musí odstranit hostitelskými servery z adaptéru MySQL.

5. Správce musí odstranit všechny plány, které odkazují na adaptér MySQL.

6. Správce musí odstranit všechny kvóty, které jsou spojeny s adaptérem MySQL.

7. Spusťte znovu skript nasazení s následující prvky:
    - -Odinstalovat parametr
    - Koncové body Azure Resource Manager
    - DirectoryTenantID
    - Přihlašovací údaje k účtu správce služby
