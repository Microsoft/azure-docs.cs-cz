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
ms.date: 09/04/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 017ac3214046b812ce003ab7a190f2f2b3f4f2e2
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697937"
---
# <a name="update-the-sql-resource-provider"></a>Aktualizace poskytovatele prostředků SQL

*Platí pro: integrované systémy Azure Stack.*

Nového poskytovatele prostředků SQL může být uvolněna při aktualizaci služby Azure Stack pro nové sestavení. I když existující adaptéru i nadále fungovat, doporučujeme aktualizuje na nejnovější verzi co nejdřív.

>[!IMPORTANT]
>Je třeba nainstalovat aktualizace v pořadí, ve kterém jejich uvedení na trh. Verze nelze přeskočit. Přečtěte si seznam verzí v [nasazení požadavky na poskytovatele prostředků](.\azure-stack-sql-resource-provider-deploy.md#prerequisites).

## <a name="overview"></a>Přehled

Chcete-li aktualizovat poskytovatele prostředků, použijte *UpdateSQLProvider.ps1* skriptu. Tento skript je součástí ke stažení nového poskytovatele prostředků SQL. Aktualizace proces je podobný procesu pro [nasazení poskytovatele prostředků](.\azure-stack-sql-resource-provider-deploy.md). Skript aktualizace používá stejné argumenty jako skript DeploySqlProvider.ps1 a budete muset zadat informace o certifikátu.

### <a name="update-script-processes"></a>Aktualizace skriptu procesy

*UpdateSQLProvider.ps1* skript vytvoří nový virtuální počítač (VM) s nejnovějším kódem poskytovatele prostředků.

>[!NOTE]
>Doporučujeme stáhnout nejnovější jádru Windows serveru 2016 image z Marketplace správu. Pokud je potřeba nainstalovat aktualizace, můžete umístit **jeden** MSU balíček v cestě místní závislosti. Skript selže, pokud existuje více než jeden soubor MSU v tomto umístění.

Po *UpdateSQLProvider.ps1* skript vytvoří nový virtuální počítač, skript migruje od předchozího poskytovatele virtuálních počítačů následující nastavení:

* informace o databázi
* hostování informace o serveru
* vyžaduje záznam DNS

### <a name="update-script-powershell-example"></a>Aktualizovat ukázkový skript prostředí PowerShell

Můžete upravit a spustit následující skript z se zvýšenými oprávněními ISE Powershellu. Nezapomeňte změnit informace o účtu a hesla podle potřeby pro vaše prostředí.

> [!NOTE]
> Tento proces aktualizace platí jenom pro integrované systémy Azure Stack.

```powershell
# Install the AzureRM.Bootstrapper module and set the profile.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

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
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `

 ```

## <a name="updatesqlproviderps1-parameters"></a>Parametry UpdateSQLProvider.ps1

Při spuštění skriptu můžete zadat následující parametry z příkazového řádku. Pokud ne, nebo pokud libovolný parametr ověření nezdaří, budete vyzváni k poskytnutí požadovaných parametrů.

| Název parametru | Popis | Komentář nebo výchozí hodnotu |
| --- | --- | --- |
| **CloudAdminCredential** | Přihlašovací údaje pro správce cloudu potřebné pro přístup k privilegovaným koncový bod. | _Vyžaduje_ |
| **AzCredential** | Přihlašovací údaje pro účet správce služby Azure Stack. Použijte stejné přihlašovací údaje, které jste použili k nasazení Azure Stack. | _Vyžaduje_ |
| **VMLocalCredential** | Přihlašovací údaje pro účet místního správce poskytovatele prostředků SQL virtuálního počítače. | _Vyžaduje_ |
| **PrivilegedEndpoint** | IP adresa nebo název DNS privileged koncového bodu. |  _Vyžaduje_ |
| **AzureEnvironment** | Prostředí azure účet správce služby, které jste použili k nasazení Azure Stack. Povinné, pokud se nejedná o služby AD FS. Názvy prostředí podporované jsou **AzureCloud**, **AzureUSGovernment**, nebo pokud používáte Azure Active Directory Čína, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Také je nutné umístit váš soubor PFX certifikátu v tomto adresáři. | _Volitelné pro jeden uzel, ale povinné pro více uzly_ |
| **DefaultSSLCertificatePassword** | Heslo pro certifikát PFX. | _Vyžaduje_ |
| **MaxRetryCount** | Počet pokusů, které chcete opakovat každé operace, pokud dojde k selhání.| 2 |
| **RetryDuration** |Časový interval mezi opakovanými pokusy, během několika sekund. | 120 |
| **Odinstalace** | Odebere poskytovatele prostředků a všechny související prostředky. | Ne |
| **Režim DebugMode** | Zabraňuje automatickému čištění při selhání. | Ne |

## <a name="next-steps"></a>Další postup

[Spravovat poskytovatele prostředků SQL](azure-stack-sql-resource-provider-maintain.md)
