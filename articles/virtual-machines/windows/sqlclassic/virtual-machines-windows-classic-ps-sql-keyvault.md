---
title: Integrace Key Vault s klasickým virtuálním počítačem Azure SQL Server
description: Naučte se automatizovat konfiguraci SQL Serverho šifrování pro použití s Azure Key Vault. Toto téma vysvětluje, jak pomocí Azure Key Vault integrace s SQL Server virtuálních počítačů vytvořit v modelu nasazení Classic.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ab8d41a7-1971-4032-ab71-eb435c455dc1
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/17/2017
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: f878c6f7a59328e2f68ffbaee066bba4a5b6c898
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978128"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Konfigurace integrace Azure Key Vault pro SQL Server v Azure Virtual Machines (Classic)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Classic](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Přehled
Existuje několik funkcí SQL Server šifrování, jako je [transparentní šifrování dat (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [šifrování na úrovni sloupce (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)a [šifrování záloh](https://msdn.microsoft.com/library/dn449489.aspx). Tyto formy šifrování vyžadují, abyste mohli spravovat a ukládat kryptografické klíče, které používáte pro šifrování. Služba Azure Key Vault (integrace) je navržená tak, aby vylepšila zabezpečení a správu těchto klíčů v zabezpečeném a vysoce dostupném umístění. [Konektor SQL serveru](https://www.microsoft.com/download/details.aspx?id=45344) umožňuje SQL Server používat tyto klíče z Azure Key Vault.

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Správce prostředků a Classic](../../../azure-resource-manager/management/deployment-models.md). Tento článek popisuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

Pokud používáte SQL Server s místními počítači, můžete postupovat [podle pokynů k přístupu k Azure Key Vault z místního počítače SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Ale pro SQL Server ve virtuálních počítačích Azure můžete ušetřit čas pomocí funkce *integrace Azure Key Vault* . Pomocí několika rutin Azure PowerShell pro povolení této funkce můžete automatizovat konfiguraci nutnou k tomu, aby virtuální počítač SQL měl přístup k trezoru klíčů.

Když je tato funkce povolená, nainstaluje se Konektor SQL Serveru automaticky, nakonfiguruje poskytovatele EKM pro přístup k Azure Key Vault a vytvoří přihlašovací údaje, které vám umožní přístup k trezoru. Pokud jste si prohlédli postup uvedený v předchozí dokumentaci, vidíte, že tato funkce automatizuje kroky 2 a 3. Jedinou věcí, kterou byste pořád museli ručně udělat, je vytvoření trezoru klíčů a klíčů. Odtud je celá instalace vašeho virtuálního počítače SQL automatizovaná. Až tato funkce dokončí tuto instalaci, můžete spustit příkazy T-SQL, které zahájí šifrování databází nebo zálohování, jako byste to udělali normálně.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Konfigurace integrace integrace
Ke konfiguraci Integrace Azure Key Vault použijte PowerShell. Následující části obsahují přehled požadovaných parametrů a pak vzorový skript PowerShellu.

### <a name="install-the-sql-server-iaas-extension"></a>Instalace rozšíření SQL Server IaaS
Nejdřív [nainstalujte SQL Server rozšíření IaaS](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Pochopení vstupních parametrů
V následující tabulce jsou uvedeny parametry potřebné ke spuštění skriptu prostředí PowerShell v další části.

| Parametr | Popis | Příklad: |
| --- | --- | --- |
| **$akvURL** |**Adresa URL trezoru klíčů** |"https:\//contosokeyvault.vault.azure.net/" |
| **$spName** |**Hlavní název služby** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Tajný klíč objektu služby** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**Název přihlašovacího údaje:** Integrace se službou Azure Key Vault vytvoří přihlašovací údaje v rámci SQL Serveru, díky čemuž mají virtuální počítače přístup do trezoru klíčů. Zvolte název pro tyto přihlašovací údaje. |"mycred1" |
| **$vmName** |**Název virtuálního počítače**: název dříve vytvořeného virtuálního počítače SQL. |"myvmname" |
| **$serviceName** |**Název služby**: název cloudové služby, který je přidružený k virtuálnímu počítači SQL. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>Povolení integrace integrace s prostředím PowerShell
Rutina **New-AzureVMSqlServerKeyVaultCredentialConfig** vytvoří objekt konfigurace pro funkci Integrace Azure Key Vault. Rutina **set-AzureVMSqlServerExtension** nakonfiguruje tuto integraci s parametrem **KeyVaultCredentialSettings** . Následující kroky ukazují, jak tyto příkazy použít.

1. V Azure PowerShell nejprve nakonfigurujte vstupní parametry s konkrétními hodnotami, jak je popsáno v předchozích částech tohoto tématu. Následující skript je příkladem.
   
        $akvURL = "https:\//contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. Potom pomocí následujícího skriptu nakonfigurujte a povolte integraci integrace.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

Rozšíření agenta SQL IaaS bude aktualizovat virtuální počítač SQL pomocí této nové konfigurace.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

