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
ms.date: 01/11/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: f3129c27d123aa448f8293f7e1827dc3ebc5c82e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239073"
---
# <a name="update-the-mysql-resource-provider"></a>Aktualizace poskytovatele prostředků MySQL 

*Platí pro: Integrované systémy Azure Stack.*

Nové MySQL adaptéru poskytovatele prostředků mohou být vydána při sestavení služby Azure Stack se aktualizují. I když existující adaptéru i nadále fungovat, doporučujeme, aktualizuje na nejnovější verzi co nejdřív. 

Od verze verze 1.1.33.0 poskytovatele prostředků MySQL, aktualizace jsou kumulativní a nemusí být nainstalován v pořadí, ve kterém byly vydány; tak dlouho, dokud jste od verze 1.1.24.0 nebo novější. Například pokud používáte verzi 1.1.24.0 poskytovatele prostředků MySQL, potom můžete upgradovat na verzi 1.1.33.0 nebo později bez nutnosti nejprve nainstalujte verzi 1.1.30.0. Pokud chcete zkontrolovat verze zprostředkovatele dostupných prostředků a verze se podporují ve službě Azure Stack, najdete v seznamu verzí v [nasazení požadavky na poskytovatele prostředků](./azure-stack-mysql-resource-provider-deploy.md#prerequisites).

Aktualizovat poskytovatele prostředků, použijete **UpdateMySQLProvider.ps1** skriptu. Proces je podobný procesu pro instalaci poskytovatele prostředků, jak je popsáno v [nasazení poskytovatele prostředků](#deploy-the-resource-provider) části tohoto článku. Skript je součástí stažení zprostředkovatele prostředků. 

 > [!IMPORTANT]
 > Před upgradem poskytovatele prostředků, přečtěte si poznámky k verzi pro další informace o nové funkce, opravy a známých problémech, které můžou ovlivnit nasazení.

## <a name="update-script-processes"></a>Aktualizace skriptu procesy

**UpdateMySQLProvider.ps1** skript vytvoří nový virtuální počítač s nejnovějším kódem poskytovatele prostředků a migraci nastavení ze starého virtuálního počítače do nového virtuálního počítače. Nastavení, která migraci zahrnují databáze a hostování informace o serveru a záznamu DNS nezbytné. 

>[!NOTE]
>Doporučujeme stáhnout nejnovější jádru Windows serveru 2016 image z Marketplace správu. Pokud je potřeba nainstalovat aktualizace, můžete umístit **jeden** MSU balíček v cestě místní závislosti. Skript selže, pokud existuje více než jeden soubor MSU v tomto umístění.

Tento skript vyžaduje použití stejné argumenty, které jsou popsány DeployMySqlProvider.ps1 skriptu. Zadejte certifikát zde také.  


## <a name="update-script-parameters"></a>Aktualizovat parametry skriptu 
Můžete zadat následující parametry z příkazového řádku při spuštění **UpdateMySQLProvider.ps1** skript prostředí PowerShell. Pokud ne, nebo pokud libovolný parametr ověření nezdaří, budete vyzváni k poskytnutí požadovaných parametrů. 

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

## <a name="update-script-example"></a>Ukázkový skript aktualizace
Následuje příklad použití *UpdateMySQLProvider.ps1* skript, který můžete spustit z konzole Powershellu se zvýšenými oprávněními. Nezapomeňte změnit informace o proměnných a hesla podle potřeby:  

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

## <a name="next-steps"></a>Další postup
[Spravovat poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-maintain.md)
