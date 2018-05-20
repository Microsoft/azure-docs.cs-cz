---
title: Používání databáze SQL v Azure zásobníku | Microsoft Docs
description: Zjistěte, jak můžete nasadit databází SQL jako služba na Azure zásobníku a rychlé kroky k nasazení adaptér zprostředkovatele prostředků systému SQL Server.
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
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 20b289c16a73bd20ed020987116975c8abe893f0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Databáze SQL pro použití v zásobníku Microsoft Azure
Pomocí zprostředkovatele prostředků Azure zásobníku SQL serveru ke zveřejnění databází SQL jako služba Azure zásobníku. Služba zprostředkovatele prostředků SQL běží na poskytovateli prostředků SQL virtuální počítač, který je virtuálního počítače jádra serveru systému Windows.

## <a name="prerequisites"></a>Požadavky
Existuje několik předpokladů, které musí být splněné před nasazením poskytovatel prostředků Azure SQL zásobníku. V počítači, který přístup privilegované koncový bod virtuálního počítače, proveďte následující kroky:

- Pokud jste zatím žádný nevytvořili, [zaregistrovat Azure zásobníku](.\azure-stack-registration.md) s Azure tak, aby si můžete stáhnout položky Azure marketplace.
- Přidejte požadované jádra serveru systému Windows virtuálního počítače do zásobníku Azure marketplace stažením **jádra Windows Server 2016 serveru** bitové kopie. Pokud potřebujete nainstalovat aktualizace, můžete umístit jedné. Balíček MSU v cestě místní závislostí. Pokud je více než jeden. MSU nalezeno, instalace zprostředkovatele prostředků SQL se nezdaří.
- Zprostředkovatel prostředků SQL binární stažení a spuštění Self-Extractor extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimální odpovídající Azure zásobníku sestavení. Ujistěte se, že jste stáhnout správné binární pro verzi Azure zásobníku, kterou používáte:
    - Azure zásobníku verze 1802 (1.0.180302.1): [SQL RP verze 1.1.18.0](https://aka.ms/azurestacksqlrp1802).
    - Azure zásobníku verze 1712 (1.0.180102.3, 1.0.180103.2 nebo 1.0.180106.1 (integrované systémy)): [SQL RP verze 1.1.14.0](https://aka.ms/azurestacksqlrp1712).
- Integrované systémy jenom pro instalace, musíte zadat certifikát SQL PaaS PKI, jak je popsáno v části volitelné certifikáty PaaS z [požadavky na Azure zásobníku nasazení infrastruktury veřejných KLÍČŮ](.\azure-stack-pki-certs.md#optional-paas-certificates), umístěním soubor .pfx do umístění specifikace **DependencyFilesLocalPath** parametr.
- Zajistěte, abyste měli [nejnovější verzi Azure PowerShell zásobníku](.\azure-stack-powershell-install.md) nainstalovaný (v1.2.11). 

## <a name="deploy-the-sql-resource-provider"></a>Nasazení poskytovatele prostředků SQL
Jakmile úspěšně dokončíte přípravu na nainstalovat poskytovatele prostředků SQL tak, že splňuje všechny požadavky, teď můžete spustit **DeploySqlProvider.ps1** skriptu pro nasazení poskytovatele prostředků SQL. DeploySqlProvider.ps1 skript se extrahuje jako součást poskytovatele prostředků SQL binární, který jste si stáhli odpovídající vaší verzí zásobník Azure. 

> [!IMPORTANT]
> Systém, kde je spuštěn skript musí být systém Windows 10 nebo Windows Server 2016 na nejnovější verzi modulu runtime rozhraní .NET nainstalované.


Abyste mohli nasadit poskytovatele prostředků SQL, otevřete nové zvýšenými oprávněními (správce) konzolu PowerShell a přejděte do adresáře, které jste extrahovali binární soubory zprostředkovatele prostředků SQL.

> [!NOTE]
> Aby nedocházelo k problémům, které by mohly nastat z nesprávné modulů prostředí PowerShell, které jsou již načtena v systému pomocí nového okna prostředí PowerShell.

Spusťte skript DeploySqlProvider.ps1, který provede následující kroky:
- Ukládání certifikáty a další artefakty na účet úložiště v Azure zásobníku.
- Publikuje Galerie balíčky, abyste mohli nasadit databází SQL v galerii.
- Publikuje balíček Galerie pro nasazení hostitelskými servery.
- Virtuální počítač se nasadí pomocí bitové kopie systému Windows Server 2016, který byl vytvořen v kroku 1 a poté nainstaluje poskytovatele prostředků.
- Zaregistruje místní záznam DNS, který se mapuje na zprostředkovateli prostředků virtuálních počítačů.
- Zaregistruje poskytovatel prostředků s místní Azure Resource Manager (uživatele a správce).
- Během instalace RP volitelně nainstaluje jednu aktualizaci systému Windows.

Nasazení zprostředkovatele prostředků SQL začne a vytvoří skupinu prostředků system.local.sqladapter. To může trvat až 75 minut na dokončení čtyři požadovaná nasazení do této skupiny prostředků.

### <a name="deploysqlproviderps1-parameters"></a>Parametry DeploySqlProvider.ps1
Tyto parametry můžete zadat na příkazovém řádku. Pokud ho použít nechcete, nebo pokud žádné parametr ověření selže, zobrazí se výzva k zadání požadované parametry.

| Název parametru | Popis | Komentář nebo výchozí hodnotu |
| --- | --- | --- |
| **CloudAdminCredential** | Přihlašovací údaje pro správce cloudu potřebné pro přístup k privilegované koncový bod. | _Požadované_ |
| **AzCredential** | Přihlašovací údaje pro účet správce služby Azure zásobníku. Pomocí stejných přihlašovacích údajů jako používat pro nasazování Azure zásobníku. | _Požadované_ |
| **VMLocalCredential** | Přihlašovací údaje pro účet místního správce zprostředkovatele prostředků SQL virtuálních počítačů. | _Požadované_ |
| **PrivilegedEndpoint** | IP adresa nebo název DNS privilegované koncového bodu. |  _Požadované_ |
| **DependencyFilesLocalPath** | Soubor .pfx certifikátu musí být umístěny v tomto adresáři také. | _Volitelné_ (_povinné_ pro integrované systémy) |
| **DefaultSSLCertificatePassword** | Heslo pro certifikát .pfx. | _Požadované_ |
| **MaxRetryCount** | Počet přístupů, které chcete opakovat každé operace, pokud dojde k selhání.| 2 |
| **RetryDuration** | Interval časového limitu mezi opakovanými pokusy, v sekundách. | 120 |
| **Odinstalace** | Odebere poskytovatele prostředků a všechny přidružené prostředky (viz následující poznámky k). | Ne |
| **Režim DebugMode** | Brání automatické čištění při selhání. | Ne |

>[!NOTE]
> SKU může trvat až jednu hodinu, mají být zobrazeny v portálu. Databázi nelze vytvořit, dokud nebude vytvořen verze SKU.


## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Nasazení pomocí vlastního skriptu poskytovatele prostředků SQL
Abyste se vyhnuli, při spuštění skriptu DeploySqlProvider.ps1 ručním zadáním požadované informace, můžete přizpůsobit následující ukázkový skript změnou výchozí informace o účtu a hesla podle potřeby:

```powershell
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

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Ověření nasazení pomocí portálu Azure zásobníku
Postup v této části lze zajistit, že poskytovatele prostředků SQL byl úspěšně nasazen.

> [!NOTE]
>  Po dokončení skriptu instalace bude nutné aktualizovat na portálu zobrazit položky správce okno a Galerie.

1. Přihlaste se k portálu pro správu jako správce služeb.

2. Ověřte, zda nasazení proběhla úspěšně. Přejděte na **skupiny prostředků**. Vyberte **systému.\< umístění\>.sqladapter** skupinu prostředků. Ověřte, zda všechny čtyři nasazení proběhla úspěšně.

      ![Ověření nasazení poskytovatele prostředků SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="next-steps"></a>Další postup

[Přidat hostitelskými servery](azure-stack-sql-resource-provider-hosting-servers.md)
