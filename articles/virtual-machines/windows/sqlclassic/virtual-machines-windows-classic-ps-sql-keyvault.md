---
title: Integrace trezoru klíčů s klasickým virtuálním počítačem Azure SQL Server
description: Zjistěte, jak automatizovat konfiguraci šifrování serveru SQL Server pro použití s trezorem klíčů Azure. Toto téma vysvětluje, jak používat Azure Key Vault Integrace s virtuálními počítači SQL Server vytvořit v modelu klasické nasazení.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978128"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Konfigurace integrace úložiště klíčů Azure pro SQL Server na virtuálních počítačích Azure (classic)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Classic](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Přehled
Existuje více funkcí šifrování serveru SQL Server, jako je [například transparentní šifrování dat (TDE),](https://msdn.microsoft.com/library/bb934049.aspx) [šifrování na úrovni sloupců (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)a [šifrování záloh](https://msdn.microsoft.com/library/dn449489.aspx). Tyto formy šifrování vyžadují správu a ukládání kryptografických klíčů, které používáte pro šifrování. Služba Azure Key Vault (AKV) je navržena tak, aby zlepšila zabezpečení a správu těchto klíčů v zabezpečeném a vysoce dostupném umístění. [Sql Server Connector](https://www.microsoft.com/download/details.aspx?id=45344) umožňuje SQL Server používat tyto klíče z Azure Key Vault.

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../azure-resource-manager/management/deployment-models.md). Tento článek popisuje použití modelu klasické nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

Pokud používáte SQL Server s místními počítači, existují [kroky, které můžete postupovat pro přístup k Azure Key Vault z místního počítače SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Ale pro SQL Server ve virtuálních počítačích Azure můžete ušetřit čas pomocí funkce *Integrace úložiště klíčů Azure.* Pomocí několika rutin Azure PowerShell, které tuto funkci povolí, můžete automatizovat konfiguraci potřebnou pro přístup k trezoru klíčů pro virtuální počítač SQL.

Pokud je tato funkce povolena, automaticky nainstaluje konektor SQL Server, nakonfiguruje zprostředkovatele EKM pro přístup k úložišti klíčů Azure a vytvoří pověření, které vám umožní přístup k trezoru. Pokud jste se podívali na kroky ve výše uvedené místní dokumentaci, uvidíte, že tato funkce automatizuje kroky 2 a 3. Jediné, co byste ještě museli udělat ručně, je vytvořit trezor klíčů a klíče. Odtud je automatizováno celé nastavení virtuálního počítače SQL. Jakmile tato funkce dokončí toto nastavení, můžete spustit příkazy T-SQL a začít šifrovat databáze nebo zálohy obvyklým způsobem.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Konfigurace integrace AKV
Pomocí PowerShellu nakonfigurujte integraci trezoru klíčů Azure. Následující části poskytují přehled požadovaných parametrů a pak ukázkový skript prostředí PowerShell.

### <a name="install-the-sql-server-iaas-extension"></a>Instalace rozšíření SQL Server IaaS
Nejprve [nainstalujte rozšíření SQL Server IaaS](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Pochopení vstupních parametrů
V následující tabulce jsou uvedeny parametry potřebné ke spuštění skriptu prostředí PowerShell v další části.

| Parametr | Popis | Příklad |
| --- | --- | --- |
| **$akvURL** |**Adresa URL trezoru klíčů** |"https:\//contosokeyvault.vault.azure.net/" |
| **$spName** |**Název hlavního_serveru service** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Tajný klíč hlavního povinného servisu** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**Název přihlašovacího údaje:** Integrace se službou Azure Key Vault vytvoří přihlašovací údaje v rámci SQL Serveru, díky čemuž mají virtuální počítače přístup do trezoru klíčů. Zvolte název pro tyto přihlašovací údaje. |"mycred1" |
| **$vmName** |**Název virtuálního počítače**: Název dříve vytvořeného virtuálního počítače SQL. |"myvmname" |
| **$serviceName** |**Název služby**: Název cloudové služby, který je přidružen k virtuálnímu virtuálnímu mísu SQL. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>Povolení integrace AKV s PowerShellem
Rutina **New-AzureVMSqlServerKeyVaultConfig** vytvoří objekt konfigurace pro funkci integrace úložiště klíčů Azure. **Rozšíření Set-AzureVMSqlServerExtension** konfiguruje tuto integraci s parametrem **KeyVaultCredentialSettings.** Následující kroky ukazují, jak používat tyto příkazy.

1. V Azure PowerShell, nejprve nakonfigurujte vstupní parametry s konkrétní hodnoty, jak je popsáno v předchozích částech tohoto tématu. Následující skript je příkladem.
   
        $akvURL = "https:\//contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. Potom použijte následující skript ke konfiguraci a povolení integrace AKV.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

Rozšíření agenta SQL IaaS aktualizuje virtuální ho virtuálního počítače SQL pomocí této nové konfigurace.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

