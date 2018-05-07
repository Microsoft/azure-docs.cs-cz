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
ms.openlocfilehash: 9154f509f9019c28515970869678aa6633d16163
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="update-the-sql-resource-provider-adapter"></a>Aktualizovat adaptér zprostředkovatele prostředků SQL
Nový adaptér zprostředkovatele prostředků SQL může vydala, když jsou aktualizovány sestavení Azure zásobníku. Zatímco existující adaptér fungovat, doporučujeme aktualizovat na nejnovější verzi co nejdříve. Aktualizace musí být nainstalované v pořadí: nedá přeskočit verze (najdete v seznamu verzí v [nasazení požadavky na poskytovatele prostředků](.\azure-stack-sql-resource-provider-deploy.md#prerequisites)).

Aktualizovat zprostředkovatele prostředků použijete *UpdateSQLProvider.ps1* skriptu. Proces je podobný procesu použitý k instalaci zprostředkovatele prostředků, jak je popsáno v [nasazení poskytovatele prostředků](.\azure-stack-sql-resource-provider-deploy.md) článku. Skript je součástí stahování poskytovatele prostředků.

*UpdateSQLProvider.ps1* skript vytvoří nový virtuální počítač s nejnovější kód zprostředkovatele prostředků a migraci nastavení z původního virtuálního počítače do nového virtuálního počítače. Zahrnout nastavení, které provádějí migraci databáze a hostování informace o serveru, a potřebné DNS záznam.

Skript vyžaduje použití stejné argumenty, které jsou popsány DeploySqlProvider.ps1 skriptu. Zadejte certifikát zde také. 

Doporučujeme si stáhnout nejnovější jádro systému Windows Server 2016 image z Marketplace správy. Pokud potřebujete nainstalovat aktualizace, můžete umístit jedné. Balíček MSU v cestě místní závislostí. Pokud je více než jeden. MSU nalezeno, skript selže.

Následuje příklad *UpdateSQLProvider.ps1* skript, který můžete spustit z příkazového řádku prostředí PowerShell. Nezapomeňte změnit informace o účtu a hesla podle potřeby: 

> [!NOTE]
> Proces aktualizace se vztahuje pouze na integrované systémy.

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

## <a name="updatesqlproviderps1-parameters"></a>Parametry UpdateSQLProvider.ps1
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


## <a name="next-steps"></a>Další postup

[Spravovat poskytovatele prostředků SQL](azure-stack-sql-resource-provider-maintain.md)
