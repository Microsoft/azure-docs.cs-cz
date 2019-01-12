---
title: Aktualizuje se poskytovatel prostředků Azure Stack SQL | Dokumentace Microsoftu
description: Zjistěte, jak můžete aktualizovat poskytovatele prostředků Azure Stack SQL.
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
ms.openlocfilehash: 00a7644663b4628d20dbe598def158bc120a7aee
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245447"
---
# <a name="update-the-sql-resource-provider"></a>Aktualizace poskytovatele prostředků SQL

*Platí pro: Integrované systémy Azure Stack.*

Nového poskytovatele prostředků SQL může být uvolněna při aktualizaci služby Azure Stack pro nové sestavení. I když stávajícího poskytovatele prostředků i nadále fungovat, doporučujeme aktualizuje na nejnovější verzi co nejdřív. 

Od verze verze 1.1.33.0 poskytovatele prostředků SQL, aktualizace jsou kumulativní a nemusí být nainstalován v pořadí, ve kterém byly vydány; tak dlouho, dokud jste od verze 1.1.24.0 nebo novější. Například pokud používáte verzi 1.1.24.0 poskytovatele prostředků SQL, pak můžete upgradovat na verzi 1.1.33.0 nebo později bez nutnosti nejprve nainstalujte verzi 1.1.30.0. Pokud chcete zkontrolovat verze zprostředkovatele dostupných prostředků a verze se podporují ve službě Azure Stack, najdete v seznamu verzí v [nasazení požadavky na poskytovatele prostředků](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

Chcete-li aktualizovat poskytovatele prostředků, použijte *UpdateSQLProvider.ps1* skriptu. Tento skript je součástí ke stažení nového poskytovatele prostředků SQL. Aktualizace proces je podobný procesu pro [nasazení poskytovatele prostředků](./azure-stack-sql-resource-provider-deploy.md). Skript aktualizace používá stejné argumenty jako skript DeploySqlProvider.ps1 a budete muset zadat informace o certifikátu.

 > [!IMPORTANT]
 > Před upgradem poskytovatele prostředků, přečtěte si poznámky k verzi pro další informace o nové funkce, opravy a známých problémech, které můžou ovlivnit nasazení.

## <a name="update-script-processes"></a>Aktualizace skriptu procesy

*UpdateSQLProvider.ps1* skript vytvoří nový virtuální počítač (VM) s nejnovějším kódem poskytovatele prostředků.

> [!NOTE]
> Doporučujeme stáhnout nejnovější jádru Windows serveru 2016 image z Marketplace správu. Pokud je potřeba nainstalovat aktualizace, můžete umístit **jeden** MSU balíček v cestě místní závislosti. Skript selže, pokud existuje více než jeden soubor MSU v tomto umístění.

Po *UpdateSQLProvider.ps1* skript vytvoří nový virtuální počítač, skript migruje od předchozího poskytovatele virtuálních počítačů následující nastavení:

* informace o databázi
* hostování informace o serveru
* vyžaduje záznam DNS

## <a name="update-script-parameters"></a>Aktualizovat parametry skriptu

Můžete zadat následující parametry z příkazového řádku při spuštění **UpdateSQLProvider.ps1** skript prostředí PowerShell. Pokud ne, nebo pokud libovolný parametr ověření nezdaří, budete vyzváni k poskytnutí požadovaných parametrů.

| Název parametru | Popis | Komentář nebo výchozí hodnotu |
| --- | --- | --- |
| **CloudAdminCredential** | Přihlašovací údaje pro správce cloudu potřebné pro přístup k privilegovaným koncový bod. | _Požadováno_ |
| **AzCredential** | Přihlašovací údaje pro účet správce služby Azure Stack. Použijte stejné přihlašovací údaje, které jste použili k nasazení Azure Stack. | _Požadováno_ |
| **VMLocalCredential** | Přihlašovací údaje pro účet místního správce poskytovatele prostředků SQL virtuálního počítače. | _Požadováno_ |
| **PrivilegedEndpoint** | IP adresa nebo název DNS privileged koncového bodu. |  _Požadováno_ |
| **AzureEnvironment** | Prostředí Azure účet správce služby, které jste použili k nasazení Azure Stack. Vyžaduje se jenom pro nasazení služby Azure AD. Názvy prostředí podporované jsou **AzureCloud**, **AzureUSGovernment**, nebo pokud používáte Čína Azure AD, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Také je nutné umístit váš soubor PFX certifikátu v tomto adresáři. | _Volitelné pro jeden uzel, ale povinné pro více uzly_ |
| **DefaultSSLCertificatePassword** | Heslo pro certifikát PFX. | _Požadováno_ |
| **MaxRetryCount** | Počet pokusů, které chcete opakovat každé operace, pokud dojde k selhání.| 2 |
| **RetryDuration** |Časový interval mezi opakovanými pokusy, během několika sekund. | 120 |
| **Odinstalace** | Odebere poskytovatele prostředků a všechny související prostředky. | Ne |
| **Režim DebugMode** | Zabraňuje automatickému čištění při selhání. | Ne |

## <a name="update-script-powershell-example"></a>Aktualizovat ukázkový skript prostředí PowerShell
Následuje příklad použití *UpdateSQLProvider.ps1* skript, který můžete spustit z konzole Powershellu se zvýšenými oprávněními. Nezapomeňte změnit informace o proměnných a hesla podle potřeby:  

> [!NOTE]
> Tento proces aktualizace platí jenom pro integrované systémy Azure Stack.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.5.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service administrator account (this can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential required for privileged endpoint access.
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
  -AzureEnvironment $AzureEnvironment `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `

 ```

## <a name="next-steps"></a>Další postup

[Spravovat poskytovatele prostředků SQL](azure-stack-sql-resource-provider-maintain.md)
