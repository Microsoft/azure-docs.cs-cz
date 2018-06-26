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
ms.date: 06/25/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: e1505761a0bd1ea9dabdd0b2cbab7af902198311
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938328"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack"></a>Nasazení systému SQL Server zprostředkovatel prostředků v Azure zásobníku

Pomocí zprostředkovatele prostředků Azure zásobníku SQL Server lze zobrazit SQL databáze jako služba Azure zásobníku. Zprostředkovatel prostředků SQL je spuštěna jako služba na virtuálním počítači (VM) Windows serveru 2016 Server Core.

## <a name="prerequisites"></a>Požadavky

Existuje několik předpokladů, které musí být splněné před nasazením poskytovatel prostředků Azure SQL zásobníku. Pro splnění těchto požadavků, proveďte následující kroky v počítači, který přístup privilegované koncový bod virtuálního počítače:

- Pokud jste zatím žádný nevytvořili, [zaregistrovat Azure zásobníku](.\azure-stack-registration.md) s Azure, takže si můžete stáhnout položky Azure marketplace.
- Přidejte požadované jádra serveru systému Windows virtuálního počítače do zásobníku Azure marketplace stažením **Windows Server 2016 Datacenter - jádra serveru** bitové kopie. Skript můžete také použít k vytvoření [bitové kopie systému Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). Ujistěte se, zda že jste vybrali možnost jádra při spuštění skriptu.

  >[!NOTE]
  >Pokud potřebujete nainstalovat aktualizace, můžete umístit jeden balíček MSU v cestě místní závislostí. Pokud je nalezen více než jeden soubor MSU, instalace zprostředkovatele prostředků SQL se nezdaří.

- Stáhnout poskytovatele prostředků SQL binární a pak spusťte Self-Extractor extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimální odpovídající Azure zásobníku sestavení. Zajistěte, aby že si stáhnout správné binární verze zásobník Azure, který používáte.

    |Verze Azure zásobníku|Verze SQL RP|
    |-----|-----|
    |Verze 1804 (1.0.180513.1)|[SQL RP verze 1.1.24.0](https://aka.ms/azurestacksqlrp1804)
    |Verze 1802 (1.0.180302.1)|[SQL RP verze 1.1.18.0](https://aka.ms/azurestacksqlrp1802)|
    |Verze 1712 (1.0.180102.3, 1.0.180103.2 nebo 1.0.180106.1 (integrované systémy))|[SQL RP verze 1.1.14.0](https://aka.ms/azurestacksqlrp1712)|
    |     |     |

### <a name="certificates"></a>Certifikáty

Integrované systémy jenom pro instalace. Je nutné zadat certifikát SQL PaaS PKI, který je popsaný v části volitelné certifikáty PaaS z [požadavky na Azure zásobníku nasazení infrastruktury veřejných KLÍČŮ](.\azure-stack-pki-certs.md#optional-paas-certificates). Umístěte soubor .pfx do umístění, které **DependencyFilesLocalPath** parametr.

## <a name="deploy-the-sql-resource-provider"></a>Nasazení poskytovatele prostředků SQL

Jakmile máte k dispozici všechny požadované součásti nainstalované, spusťte **DeploySqlProvider.ps1** skriptu pro nasazení poskytovatele prostředků SQL. Skript DeploySqlProvider.ps1 se extrahuje jako součást binární zprostředkovatele prostředků SQL, který jste stáhli pro vaši verzi Azure zásobníku.

> [!IMPORTANT]
> Systém, který používáte skript na musí být systém Windows 10 nebo Windows Server 2016 na nejnovější verzi modulu runtime rozhraní .NET nainstalované.

Chcete-li nasadit poskytovatele prostředků SQL, otevřete **nové** zvýšenými PowerShell okně konzoly a přejděte do adresáře, které jste extrahovali binární soubory zprostředkovatele prostředků SQL. Doporučujeme používat nové okno prostředí PowerShell, aby nedocházelo k problémům způsobené moduly Powershellu, které již byly načteny.

Spusťte skript DeploySqlProvider.ps1, který dokončí následující úkoly:

- Ukládání certifikáty a další artefakty na účet úložiště v Azure zásobníku.
- Publikuje Galerie balíčky, abyste mohli nasadit pomocí Galerie databází SQL.
- Publikuje balíček Galerie pro nasazení hostitelskými servery.
- Nasadí virtuálního počítače pomocí bitové kopie k jádra systému Windows Server 2016 jste stáhli a poté nainstaluje poskytovatele prostředků SQL.
- Zaregistruje místní záznam DNS, který se mapuje na zprostředkovateli prostředků virtuálních počítačů.
- Zaregistruje poskytovatel prostředků s místní správce prostředků Azure, pro operátor a uživatelské účty.
- V případě potřeby nainstaluje jednu aktualizaci Windows Server během instalace zprostředkovatele prostředků.

> [!NOTE]
> Při spuštění nasazení zprostředkovatele prostředků SQL, **system.local.sqladapter** po vytvoření skupiny prostředků. To může trvat až 75 minut na dokončení čtyři požadovaná nasazení do této skupiny prostředků.

### <a name="deploysqlproviderps1-parameters"></a>Parametry DeploySqlProvider.ps1

Můžete zadat následující parametry z příkazového řádku. Pokud to neuděláte, nebo pokud žádné ověření parametru selže, se zobrazí výzva k poskytování požadované parametry.

| Název parametru | Popis | Komentář nebo výchozí hodnotu |
| --- | --- | --- |
| **CloudAdminCredential** | Přihlašovací údaje pro správce cloudu potřebné pro přístup k privilegované koncový bod. | _Požadované_ |
| **AzCredential** | Přihlašovací údaje pro účet správce služby Azure zásobníku. Pomocí stejných přihlašovacích údajů, které jste použili pro nasazení Azure zásobníku. | _Požadované_ |
| **VMLocalCredential** | Přihlašovací údaje pro účet místního správce zprostředkovatele prostředků SQL virtuálních počítačů. | _Požadované_ |
| **PrivilegedEndpoint** | IP adresa nebo název DNS privilegované koncového bodu. |  _Požadované_ |
| **DependencyFilesLocalPath** | Soubor .pfx certifikátu musí být umístěny v tomto adresáři také. | _Volitelné_ (_povinné_ pro integrované systémy) |
| **DefaultSSLCertificatePassword** | Heslo pro certifikát .pfx. | _Požadované_ |
| **MaxRetryCount** | Počet přístupů, které chcete opakovat každé operace, pokud dojde k selhání.| 2 |
| **RetryDuration** | Interval časového limitu mezi opakovanými pokusy, v sekundách. | 120 |
| **Odinstalace** | Odebere poskytovatele prostředků a všechny přidružené prostředky (viz následující poznámky k). | Ne |
| **Režim DebugMode** | Brání automatické čištění při selhání. | Ne |

>[!NOTE]
> SKU může trvat až jednu hodinu, mají být zobrazeny v portálu. Databázi nelze vytvořit, dokud nebude verze SKU nasazené a spuštěná.

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Nasazení pomocí vlastního skriptu poskytovatele prostředků SQL

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
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account can be Azure Active Directory or Active Directory Federation Services.
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change to the directory If folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert

 ```

Po dokončení skriptu instalace zprostředkovatele prostředků, aktualizujte webový prohlížeč, abyste měli jistotu, že vidíte nejnovější aktualizace.

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Ověření nasazení pomocí portálu Azure zásobníku

Můžete použít následující kroky ověřte, zda je poskytovatel prostředků SQL úspěšně nasazeny.

1. Přihlaste se k portálu pro správu jako správce služeb.
2. Vyberte **skupiny prostředků**.
3. Vyberte **systému.\< umístění\>.sqladapter** skupinu prostředků.
4. Zprávy v části **nasazení**, znázorňuje následující snímek obrazovky, by měla být **4 úspěšné**.

      ![Ověření nasazení poskytovatele prostředků SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)

5. Můžete získat podrobnější informace o nasazení zprostředkovatele prostředků v části **nastavení**. Vyberte **nasazení** například získat informace o: stav, časové razítko a dobu trvání pro každé nasazení.

## <a name="next-steps"></a>Další postup

[Přidat hostitelskými servery](azure-stack-sql-resource-provider-hosting-servers.md)
