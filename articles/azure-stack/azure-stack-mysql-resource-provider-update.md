---
title: Aktualizace poskytovatele prostředků Azure Stack MySQL | Dokumentace Microsoftu
description: Zjistěte, jak můžete aktualizovat poskytovatele prostředků Azure Stack MySQL.
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
ms.date: 01/08/2019
ms.author: jeffgilb
ms.reviewer: georgel
ms.openlocfilehash: 790a8bfed693f03cdadd036cab17eb94dee1c1ed
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119287"
---
# <a name="update-the-mysql-resource-provider"></a>Aktualizace poskytovatele prostředků MySQL 

*Platí pro: Integrované systémy Azure Stack.*

Nový adaptér poskytovatele prostředků SQL může uvolní, když jsou aktualizované sestavení služby Azure Stack. I když existující adaptéru i nadále fungovat, doporučujeme, aktualizuje na nejnovější verzi co nejdřív. 

> [!IMPORTANT]
> Je třeba nainstalovat aktualizace v pořadí, ve kterém jejich uvedení na trh. Verze nelze přeskočit. Přečtěte si seznam verzí v [nasazení požadavky na poskytovatele prostředků](./azure-stack-mysql-resource-provider-deploy.md#prerequisites).

## <a name="update-the-mysql-resource-provider-adapter-integrated-systems-only"></a>Aktualizace adaptéru poskytovatele prostředků MySQL (pouze pro integrované systémy)

Nový adaptér poskytovatele prostředků SQL může uvolní, když jsou aktualizované sestavení služby Azure Stack. I když existující adaptéru i nadále fungovat, doporučujeme, aktualizuje na nejnovější verzi co nejdřív.  
 
Aktualizovat poskytovatele prostředků, použijete **UpdateMySQLProvider.ps1** skriptu. Proces je podobný procesu pro instalaci poskytovatele prostředků, jak je popsáno v [nasazení poskytovatele prostředků](#deploy-the-resource-provider) části tohoto článku. Skript je součástí stažení zprostředkovatele prostředků. 

**UpdateMySQLProvider.ps1** skript vytvoří nový virtuální počítač s nejnovějším kódem poskytovatele prostředků a migraci nastavení ze starého virtuálního počítače do nového virtuálního počítače. Nastavení, která migraci zahrnují databáze a hostování informace o serveru a záznamu DNS nezbytné. 

>[!NOTE]
>Doporučujeme stáhnout nejnovější jádru Windows serveru 2016 image z Marketplace správu. Pokud je potřeba nainstalovat aktualizace, můžete umístit **jeden** MSU balíček v cestě místní závislosti. Skript selže, pokud existuje více než jeden soubor MSU v tomto umístění.

Tento skript vyžaduje použití stejné argumenty, které jsou popsány DeployMySqlProvider.ps1 skriptu. Zadejte certifikát zde také.  

Tady je příklad *UpdateMySQLProvider.ps1* skript, který můžete spustit z příkazového řádku Powershellu. Nezapomeňte změnit informace o účtu a hesla podle potřeby:  

> [!NOTE] 
> Proces aktualizace platí pouze pro integrované systémy. 

```powershell 
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.5.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time. 
$domain = "AzureStack" 

# For integrated systems, use the IP address of one of the ERCS virtual machines 
$privilegedEndpoint = "AzS-ERCS01" 

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

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
$tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds ` 
-VMLocalCredential $vmLocalAdminCreds ` 
-CloudAdminCredential $cloudAdminCreds ` 
-PrivilegedEndpoint $privilegedEndpoint ` 
-AzureEnvironment $AzureEnvironment `
-DefaultSSLCertificatePassword $PfxPass ` 
-DependencyFilesLocalPath $tempDir\cert ` 
-AcceptLicense 
``` 
 
### <a name="updatemysqlproviderps1-parameters"></a>Parametry UpdateMySQLProvider.ps1 
Tyto parametry můžete zadat na příkazovém řádku. Pokud ne, nebo pokud libovolný parametr ověření nezdaří, zobrazí se výzva k poskytnutí požadovaných parametrů. 

| Název parametru | Popis | Komentář nebo výchozí hodnotu | 
| --- | --- | --- | 
| **CloudAdminCredential** | Přihlašovací údaje pro správce cloudu potřebné pro přístup k privilegovaným koncový bod. | _Požadováno_ | 
| **AzCredential** | Přihlašovací údaje pro účet správce služby Azure Stack. Použijte stejné přihlašovací údaje, jako jste použili k nasazení Azure Stack. | _Požadováno_ | 
| **VMLocalCredential** |Přihlašovací údaje pro účet místního správce poskytovatele prostředků SQL virtuálního počítače. | _Požadováno_ | 
| **PrivilegedEndpoint** | IP adresa nebo název DNS privileged koncového bodu. |  _Požadováno_ | 
| **AzureEnvironment** | Prostředí Azure účet správce služby, které jste použili k nasazení Azure Stack. Vyžaduje se jenom pro nasazení služby Azure AD. Názvy prostředí podporované jsou **AzureCloud**, **AzureUSGovernment**, nebo pokud používáte Čína Azure AD, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Váš soubor certifikátu .pfx musíte umístit do tohoto adresáře také. | _Volitelné_ (_povinné_ pro více uzly) | 
| **DefaultSSLCertificatePassword** | Heslo pro certifikát PFX. | _Požadováno_ | 
| **MaxRetryCount** | Počet pokusů, které chcete opakovat každé operace, pokud dojde k selhání.| 2 | 
| **RetryDuration** | Časový interval mezi opakovanými pokusy, během několika sekund. | 120 | 
| **Odinstalace** | Odeberte poskytovatele prostředků a všechny související prostředky (viz následující poznámky). | Ne | 
| **Režim DebugMode** | Zabraňuje automatickému čištění při selhání. | Ne | 
| **AcceptLicense** | Přeskočí výzva k přijetí licence GPL.  (https://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 
 

## <a name="next-steps"></a>Další postup
[Spravovat poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-maintain.md)
