---
title: Použití databází MySQL ve službě Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak můžete nasadit databází MySQL jako služba v Azure stacku a rychlé kroky pro nasazení adaptéru poskytovatele prostředků MySQL Server.
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
ms.date: 07/02/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 8c758f78b661f492c98ac174d7fbbf8b7e3a5e52
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37345079"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack"></a>Nasazení poskytovatele prostředků MySQL ve službě Azure Stack

Používání poskytovatele prostředků MySQL Server vystavit databází MySQL jako služby Azure Stack. Poskytovatele prostředků MySQL běží jako služba na virtuálním počítači (VM) systému Windows Server 2016 Server Core.

## <a name="prerequisites"></a>Požadavky

Existuje několik předpokladů, které musí být splněné před nasazením poskytovatele prostředků Azure Stack MySQL. Budou odpovídat vašim požadavkům, proveďte kroky v tomto článku na počítači s přístupem k privilegovaným koncového bodu virtuálního počítače.

* Pokud jste tak již neučinili, [registrace Azure Stack](.\azure-stack-registration.md) s Azure, takže si můžete stáhnout položky Azure marketplace.
* Je nutné nainstalovat moduly Azure a Azure Stack Powershellu v systému Jestliže spustíte instalaci. Tento systém musí být bitovou kopii systému Windows 10 nebo Windows Server 2016 s nejnovější verzí modulu .NET runtime. Zobrazit [instalace Powershellu pro Azure Stack](.\azure-stack-powershell-install.md).
* Přidejte požadované jádra serveru systému Windows virtuální počítač na webu Marketplace služby Azure Stack stažením **systému Windows Server 2016 Datacenter - jádra serveru** bitové kopie.

  >[!NOTE]
  >Pokud je potřeba nainstalovat Windows update, můžete umístit jeden. MSU balíček v cestě místní závislosti. Pokud více než jeden. MSU soubor se nachází, se nezdaří instalace poskytovatele prostředků MySQL.

* Stáhnout poskytovatele prostředků MySQL binární a pak spusťte Self-Extractor extrahujte obsah do dočasného adresáře.

  >[!NOTE]
  >Chcete-li nasadit poskytovatele MySQL na systém, který nemá přístup k Internetu, zkopírujte [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) soubor na místní cestu. Zadejte název cesty pomocí **DependencyFilesLocalPath** parametru.

* Poskytovatel prostředků má minimální odpovídající Azure Stack sestavení. Ujistěte se, stáhněte si správnou binární soubor pro verzi služby Azure Stack, který používáte.

    | Verze služby Azure Stack | Verze poskytovatele prostředků MySQL|
    | --- | --- |
    | Verzi 1804 (1.0.180513.1)|[Poskytovatele prostředků MySQL verze 1.1.24.0](https://aka.ms/azurestackmysqlrp1804) |
    | Verzi 1802 (1.0.180302.1) | [Poskytovatele prostředků MySQL verze 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) |
    | Verzi 1712 (1.0.180102.3 nebo 1.0.180106.1 (integrované systémy)) | [Poskytovatele prostředků MySQL verze 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) |

### <a name="certificates"></a>Certifikáty

_Pro integrované systémy instalace pouze_. Je nutné zadat certifikát SQL PaaS PKI, který je popsaný v části volitelné PaaS certifikáty z [požadavky na infrastrukturu veřejných KLÍČŮ nasazení Azure Stack](.\azure-stack-pki-certs.md#optional-paas-certificates). Umístěte soubor .pfx do umístění určeného proměnnou **DependencyFilesLocalPath** parametru. Pro systémy ASDK neposkytují certifikát.

## <a name="deploy-the-resource-provider"></a>Nasazení poskytovatele prostředků

Jakmile máte nainstalovány požadované součásti, spusťte **DeployMySqlProvider.ps1** skriptu pro nasazení poskytovatele prostředků MYSQL. Jako součást binární poskytovatele prostředků MySQL, který jste stáhli pro vaši verzi sady Azure Stack je extrahován DeployMySqlProvider.ps1 skriptu.

Nasazení poskytovatele prostředků MySQL, otevřete okno konzole Powershellu s nová se zvýšenými oprávněními a přejděte do adresáře, které jste extrahovali binární soubory poskytovatele prostředků MySQL. Doporučujeme použít nové okno prostředí PowerShell, aby potenciální potíže způsobené aktivitami moduly Powershellu, které jsou už načteny.

Spustit **DeployMySqlProvider.ps1** skript, který dokončí následující úkoly:

* Certifikáty a další artefakty nahraje do účtu úložiště ve službě Azure Stack.
* Publikuje balíčky Galerie tak, že můžete nasadit pomocí Galerie databází MySQL.
* Publikuje balíček Galerie pro nasazení hostitelských serverů.
* Nasadí virtuální počítač pomocí základní image Windows serveru 2016 jste stáhli a poté nainstaluje poskytovatele prostředků MySQL.
* Zaregistruje místní záznam DNS, který se mapuje na zprostředkovateli prostředků virtuálního počítače.
* Zaregistruje poskytovatele prostředků s místní Azure Resource Manageru pro operátor a k uživatelským účtům.
* V případě potřeby nainstaluje jednu aktualizaci Windows Server během instalace zprostředkovatele prostředků.

> [!NOTE]
> Při spuštění nasazení poskytovatele prostředků MySQL, **system.local.mysqladapter** se vytvoří skupina prostředků. Může trvat až 75 minut na dokončení nasazení vyžaduje k této skupině prostředků.

### <a name="deploymysqlproviderps1-parameters"></a>Parametry DeployMySqlProvider.ps1

Můžete zadat tyto parametry z příkazového řádku. Pokud ne, nebo pokud libovolný parametr ověření nezdaří, budete vyzváni k poskytnutí požadovaných parametrů.

| Název parametru | Popis | Komentář nebo výchozí hodnotu |
| --- | --- | --- |
| **CloudAdminCredential** | Přihlašovací údaje pro správce cloudu potřebné pro přístup k privilegovaným koncový bod. | _Vyžaduje_ |
| **AzCredential** | Přihlašovací údaje pro účet správce služby Azure Stack. Použijte stejné přihlašovací údaje, které jste použili k nasazení Azure Stack. | _Vyžaduje_ |
| **VMLocalCredential** | Přihlašovací údaje pro účet místního správce poskytovatele prostředků MySQL virtuálního počítače. | _Vyžaduje_ |
| **PrivilegedEndpoint** | IP adresa nebo název DNS privileged koncového bodu. |  _Vyžaduje_ |
| **DependencyFilesLocalPath** | Pro integrované systémy pouze váš soubor PFX certifikátu musí být umístěna v tomto adresáři. Odpojené enviroments, stáhněte si [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) do tohoto adresáře. Volitelně můžete zkopírovat jeden balíček Windows Update MSU tady. | _Volitelné_ (_povinné_ pro integrované systémy nebo odpojených prostředích) |
| **DefaultSSLCertificatePassword** | Heslo pro certifikát PFX. | _Vyžaduje_ |
| **MaxRetryCount** | Počet pokusů, které chcete opakovat každé operace, pokud dojde k selhání.| 2 |
| **RetryDuration** | Časový interval mezi opakovanými pokusy, během několika sekund. | 120 |
| **Odinstalace** | Odebere poskytovatele prostředků a všechny související prostředky (viz následující poznámky). | Ne |
| **Režim DebugMode** | Zabraňuje automatickému čištění při selhání. | Ne |
| **AcceptLicense** | Přeskočí výzva k přijetí licence GPL.  <http://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>Nasazení poskytovatele prostředků MySQL pomocí vlastního skriptu

Chcete-li odstranit všechny ruční konfigurace při nasazení poskytovatele prostředků, můžete přizpůsobit následující skript. Podle potřeby pro vaše nasazení Azure Stack, změňte výchozí informace o účtu a hesla.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.3.0

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

# Change to the directory folder where you extracted the installation files. Do not provide a certificate on ASDK!
. $tempDir\DeployMySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert `
    -AcceptLicense

```

Po dokončení skriptu instalace poskytovatele prostředků, aktualizujte svůj prohlížeč, abyste měli jistotu, že vidíte nejnovější aktualizace.

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Ověření nasazení pomocí portálu Azure Stack

1. Přihlaste se k portálu pro správu jako správce služeb.
2. Vyberte **skupiny prostředků**
3. Vyberte **systému.\< umístění\>.mysqladapter** skupinu prostředků.
4. Na stránce Souhrn pro skupinu prostředků přehled měla by existovat žádná selhání nasazení.

## <a name="next-steps"></a>Další kroky

[Přidat hostitelské servery](azure-stack-mysql-resource-provider-hosting-servers.md)
