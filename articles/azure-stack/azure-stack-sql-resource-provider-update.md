---
title: Aktualizace zprostředkovatele prostředků Azure SQL zásobníku | Microsoft Docs
description: Zjistěte, jak můžete aktualizovat zprostředkovatele prostředků zásobníku SQL Azure.
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
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: ac5073d1abc32b7598a869750f9c5a801559e9e6
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264073"
---
# <a name="update-the-sql-resource-provider"></a>Aktualizujte zprostředkovatele prostředků SQL

*Platí pro: Azure zásobníku integrované systémy.*

Nový zprostředkovatel prostředků SQL může vydala, když Azure zásobníku se aktualizuje na nové sestavení. I když existující adaptéru i nadále fungovat, doporučujeme aktualizovat na nejnovější verzi co nejdříve.

>[!IMPORTANT]
>Je třeba nainstalovat aktualizace v pořadí, ve kterém se budou vydané. Nelze přeskočit verze. Najdete v seznamu verzí v [nasazení požadavky na poskytovatele prostředků](.\azure-stack-sql-resource-provider-deploy.md#prerequisites).

## <a name="overview"></a>Přehled

Chcete-li aktualizovat poskytovatele prostředků, použijte *UpdateSQLProvider.ps1* skriptu. Tento skript je součástí stahování nového poskytovatele prostředků SQL. Aktualizace proces je podobný proces používaný k [nasazení poskytovatele prostředků](.\azure-stack-sql-resource-provider-deploy.md). Skript aktualizace používá stejné argumenty jako skript DeploySqlProvider.ps1 a budete potřebovat k poskytování informací o certifikátu.

### <a name="update-script-processes"></a>Procesy skript aktualizace

*UpdateSQLProvider.ps1* skript vytvoří nový virtuální počítač (VM) s nejnovější kód zprostředkovatele prostředků.

>[!NOTE]
>Doporučujeme si stáhnout nejnovější jádro systému Windows Server 2016 image z Marketplace správy. Pokud potřebujete nainstalovat aktualizace, můžete umístit **jeden** MSU balíčku v cestě místní závislostí. Skript se nezdaří, pokud existuje více než jeden soubor MSU v tomto umístění.

Po *UpdateSQLProvider.ps1* skript vytvoří nový virtuální počítač, skript migruje od původního poskytovatele virtuálních počítačů následující nastavení:

* Informace o databázi
* hostování informace o serveru
* požadovaný záznam DNS

### <a name="update-script-powershell-example"></a>Aktualizovat příklad skriptu prostředí PowerShell

Můžete upravit a spusťte následující skript z zvýšenými PowerShell ISE. Mějte na paměti, chcete-li změnit informace o účtu a hesla podle potřeby pro vaše prostředí.

> [!NOTE]
> Tento proces aktualizace se vztahuje pouze na systémy integrované zásobník Azure.

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

Když spustíte skript, můžete zadat následující parametry z příkazového řádku. Pokud to neuděláte, nebo pokud žádné ověření parametru selže, se zobrazí výzva k poskytování požadované parametry.

| Název parametru | Popis | Komentář nebo výchozí hodnotu |
| --- | --- | --- |
| **CloudAdminCredential** | Přihlašovací údaje pro správce cloudu potřebné pro přístup k privilegované koncový bod. | _Požadované_ |
| **AzCredential** | Přihlašovací údaje pro účet správce služby Azure zásobníku. Pomocí stejných přihlašovacích údajů, které jste použili pro nasazení Azure zásobníku. | _Požadované_ |
| **VMLocalCredential** | Přihlašovací údaje pro účet místního správce zprostředkovatele prostředků SQL virtuálních počítačů. | _Požadované_ |
| **PrivilegedEndpoint** | IP adresa nebo název DNS privilegované koncového bodu. |  _Požadované_ |
| **DependencyFilesLocalPath** | Také musíte umístit soubor .pfx certifikátu v tomto adresáři. | _Volitelný pro jeden uzel, ale povinné pro více uzly_ |
| **DefaultSSLCertificatePassword** | Heslo pro certifikát .pfx. | _Požadované_ |
| **MaxRetryCount** | Počet přístupů, které chcete opakovat každé operace, pokud dojde k selhání.| 2 |
| **RetryDuration** |Interval časového limitu mezi opakovanými pokusy, v sekundách. | 120 |
| **Odinstalace** | Odebere poskytovatele prostředků a všechny přidružené prostředky. | Ne |
| **Režim DebugMode** | Brání automatické čištění při selhání. | Ne |

## <a name="next-steps"></a>Další postup

[Spravovat poskytovatele prostředků SQL](azure-stack-sql-resource-provider-maintain.md)
