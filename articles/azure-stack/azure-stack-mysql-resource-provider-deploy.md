---
title: Používání databáze MySQL na Azure zásobníku | Microsoft Docs
description: Zjistěte, jak můžete nasadit databází MySQL jako služba na Azure zásobníku a rychlé kroky k nasazení adaptér zprostředkovatele prostředků MySQL serveru.
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
ms.date: 06/25/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: e4c3eb1d7dfd4894576d5fbed52cf4de5fed9e44
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938108"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack"></a>Nasazení poskytovatele prostředků MySQL v Azure zásobníku

Pomocí zprostředkovatele prostředků MySQL Server vystavit databází MySQL jako služba Azure zásobníku. Zprostředkovatel prostředků MySQL běží jako služba na virtuálním počítači (VM) Windows serveru 2016 Server Core.

## <a name="prerequisites"></a>Požadavky

Existuje několik předpokladů, které musí být splněné před nasazením poskytovatel prostředků Azure zásobníku MySQL. Chcete-li tyto požadavky splňují, proveďte kroky v tomto článku na počítači, který přístup privilegované koncový bod virtuálního počítače.

* Pokud jste zatím žádný nevytvořili, [zaregistrovat Azure zásobníku](.\azure-stack-registration.md) s Azure, takže si můžete stáhnout položky Azure marketplace.
* Přidejte požadované jádra serveru systému Windows virtuálního počítače do zásobníku Azure marketplace stažením **Windows Server 2016 Datacenter - jádra serveru** bitové kopie. Skript můžete také použít k vytvoření [bitové kopie systému Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). Ujistěte se, zda že jste vybrali možnost jádra při spuštění skriptu.

  >[!NOTE]
  >Pokud potřebujete nainstalovat aktualizace, můžete umístit jedné. Balíček MSU v cestě místní závislostí. Pokud je více než jeden. MSU nalezeno, MySQL prostředků zprostředkovatele instalace se nezdaří.

* Stáhnout poskytovatele prostředků MySQL binární a pak spusťte Self-Extractor extrahujte obsah do dočasného adresáře.

  >[!NOTE]
  >Chcete-li nasadit zprostředkovatele databáze MySQL na systém, který nemá přístup k Internetu, zkopírujte [mysql konektor net 6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi) soubor do místní sdílené složky. Zadejte název sdílené složky, když se zobrazí výzva k jeho. Je nutné nainstalovat moduly Azure a Azure PowerShell zásobníku.

* Poskytovatel prostředků má minimální odpovídající Azure zásobníku sestavení. Zajistěte, aby že si stáhnout správné binární verze zásobník Azure, který používáte.

    | Verze Azure zásobníku | Verze databáze MySQL RP|
    | --- | --- |
    | Verze 1804 (1.0.180513.1)|[MySQL RP verze 1.1.24.0](https://aka.ms/azurestackmysqlrp1804) |
    | Verze 1802 (1.0.180302.1) | [MySQL RP verze 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) |
    | Verze 1712 (1.0.180102.3 nebo 1.0.180106.1 (integrované systémy)) | [MySQL RP verze 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) |

### <a name="certificates"></a>Certifikáty

Pro ASDK se v rámci tohoto procesu instalace vytvoří certifikát podepsaný svým držitelem. Pro systém Azure zásobníku integrované je nutné zadat příslušný certifikát. Pokud potřebujete přidat vlastní certifikát, umístit soubor .pfx v **DependencyFilesLocalPath** , která splňuje následující kritéria:

* Certifikát zástupných znaků pro \*.dbadapter.\< oblast\>.\< externí plně kvalifikovaný název domény\> nebo certifikát jedné lokalitě s běžný název mysqladapter.dbadapter.\< oblast\>.\< externí plně kvalifikovaný název domény\>.
* Certifikát musí být důvěryhodný. Řetěz certifikátů, musí existovat bez nutnosti zprostředkující certifikáty.
* V DependencyFilesLocalPath existuje pouze jeden certifikát souboru.
* Název souboru nesmí mít žádné speciální znaky ani mezery.

## <a name="deploy-the-resource-provider"></a>Nasazení poskytovatele prostředků

Jakmile máte k dispozici všechny požadované součásti nainstalované, spusťte **DeployMySqlProvider.ps1** skriptu pro nasazení poskytovatele prostředků MYSQL. Skript DeployMySqlProvider.ps1 se extrahuje jako součást MySQL binární zprostředkovatele prostředků, který jste stáhli pro vaši verzi Azure zásobníku.

> [!IMPORTANT]
> Systém, který používáte skript na musí být systém Windows 10 nebo Windows Server 2016 na nejnovější verzi modulu runtime rozhraní .NET nainstalované.

K nasazení poskytovatele prostředků MySQL, otevřete nové zvýšenými okno konzoly prostředí PowerShell a přejděte do adresáře, které jste extrahovali binární soubory zprostředkovatele prostředků MySQL. Doporučujeme používat nové okno prostředí PowerShell, aby nedocházelo k problémům způsobené moduly Powershellu, které již byly načteny.

Spustit **DeployMySqlProvider.ps1** skript, který dokončí následující úkoly:

* Ukládání certifikáty a další artefakty na účet úložiště v Azure zásobníku.
* Publikuje Galerie balíčky, abyste mohli nasadit pomocí Galerie databází MySQL.
* Publikuje balíček Galerie pro nasazení hostitelskými servery.
* Nasadí virtuálního počítače pomocí bitové kopie k jádra systému Windows Server 2016 jste stáhli a poté nainstaluje poskytovatele prostředků MySQL.
* Zaregistruje místní záznam DNS, který se mapuje na zprostředkovateli prostředků virtuálních počítačů.
* Zaregistruje poskytovatel prostředků s místní správce prostředků Azure, pro operátor a uživatelské účty.
* V případě potřeby nainstaluje jednu aktualizaci Windows Server během instalace zprostředkovatele prostředků.

> [!NOTE]
> Při spuštění nasazení zprostředkovatele prostředků MySQL, **system.local.mysqladapter** po vytvoření skupiny prostředků. To může trvat až 75 minut na dokončení čtyři nasazení vyžaduje do této skupiny prostředků.

### <a name="deploymysqlproviderps1-parameters"></a>Parametry DeployMySqlProvider.ps1

Můžete určit tyto parametry z příkazového řádku. Pokud to neuděláte, nebo pokud žádné ověření parametru selže, se zobrazí výzva k poskytování požadované parametry.

| Název parametru | Popis | Komentář nebo výchozí hodnotu |
| --- | --- | --- |
| **CloudAdminCredential** | Přihlašovací údaje pro správce cloudu potřebné pro přístup k privilegované koncový bod. | _Požadované_ |
| **AzCredential** | Přihlašovací údaje pro účet správce služby Azure zásobníku. Pomocí stejných přihlašovacích údajů, které jste použili pro nasazení Azure zásobníku. | _Požadované_ |
| **VMLocalCredential** | Přihlašovací údaje pro účet místního správce MySQL zprostředkovatele prostředků virtuálních počítačů. | _Požadované_ |
| **PrivilegedEndpoint** | IP adresa nebo název DNS privilegované koncového bodu. |  _Požadované_ |
| **DependencyFilesLocalPath** | Cesta k místní sdílené složky, která obsahuje [mysql konektor net 6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi). Pokud zadáte jednu z těchto cest, soubor certifikátu musí být umístěny v tomto adresáři také. | _Volitelné_ pro jeden uzel, _povinné_ pro více uzly. |
| **DefaultSSLCertificatePassword** | Heslo pro certifikát .pfx. | _Požadované_ |
| **MaxRetryCount** | Počet přístupů, které chcete opakovat každé operace, pokud dojde k selhání.| 2 |
| **RetryDuration** | Interval časového limitu mezi opakovanými pokusy, v sekundách. | 120 |
| **Odinstalace** | Odebere poskytovatele prostředků a všechny přidružené prostředky (viz následující poznámky k). | Ne |
| **Režim DebugMode** | Brání automatické čištění při selhání. | Ne |
| **AcceptLicense** | Přeskočí řádku tak, aby přijímal GPL licence.  <http://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

> [!NOTE]
> SKU může trvat až jednu hodinu, mají být zobrazeny v portálu. Databázi nelze vytvořit, dokud nebude verze SKU nasazené a spuštěná.

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>Nasazení poskytovatele prostředků MySQL pomocí vlastního skriptu

K vyloučení jakékoli ruční konfigurace při nasazování poskytovatele prostředků, můžete upravit následující skript. Podle potřeby pro vaše nasazení Azure zásobníku, změňte výchozí informace o účtu a hesla.

```powershell
# Install the AzureRM.Bootstrapper module and set the profile.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile

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
# Find the ERCS01 IP address first, and make sure the certificate file is in the specified directory.
. $tempDir\DeployMySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert `
    -AcceptLicense

```

Po dokončení skriptu instalace zprostředkovatele prostředků, aktualizujte webový prohlížeč, abyste měli jistotu, že vidíte nejnovější aktualizace.

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Ověření nasazení pomocí portálu Azure zásobníku

1. Přihlaste se k portálu pro správu jako správce služeb.
2. Vyberte **skupiny prostředků**
3. Vyberte **systému.\< umístění\>.mysqladapter** skupinu prostředků.
4. Na stránce Souhrn pro prostředek skupiny přehled, zprávy v části **nasazení** by měla být **3 úspěšné**.
5. Můžete získat podrobnější informace o nasazení zprostředkovatele prostředků v části **nastavení**. Vyberte **nasazení** například získat informace o: stav, časové razítko a dobu trvání pro každé nasazení.

## <a name="next-steps"></a>Další postup

[Přidat hostitelskými servery](azure-stack-mysql-resource-provider-hosting-servers.md)
