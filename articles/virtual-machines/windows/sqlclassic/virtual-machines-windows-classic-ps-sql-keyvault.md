---
title: Integrace služby Key Vault s SQL serverem na virtuálních počítačích s Windows v Azure (Classic) | Dokumentace Microsoftu
description: Zjistěte, jak automatizovat konfiguraci systému SQL Server šifrování pro použití se službou Azure Key Vault. Toto téma vysvětluje, jak pomocí integrace Azure Key Vault s SQL serverem vytvořte virtuální počítače v modelu nasazení classic.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ab8d41a7-1971-4032-ab71-eb435c455dc1
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/17/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 3711277ca11346cf73ff37c37ef4185d8bfdf6b9
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329867"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Konfigurace integrace Azure Key Vaultu pro SQL Server na virtuálních počítačích Azure (klasické)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Classic](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Přehled
Existuje více funkcí systému SQL Server šifrování, jako například [transparentní šifrování dat (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [šifrování na úrovni sloupce (Vymazat)](https://msdn.microsoft.com/library/ms173744.aspx), a [šifrování záloh](https://msdn.microsoft.com/library/dn449489.aspx). Tyto formy šifrování nutné ke správě a ukládání kryptografických klíčů, který používáte pro šifrování. Službu Azure Key Vaultu (AKV) je navržené pro zlepšení zabezpečení a správu těchto klíčů v zabezpečených a vysoce dostupných umístění. [Konektor SQL serveru](https://www.microsoft.com/download/details.aspx?id=45344) umožňuje SQL serveru používat tyto klíče ze služby Azure Key Vault.

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

Pokud používáte systém SQL Server pomocí místních počítačů, existují [kroky, pomocí kterých můžete pro přístup k Azure Key Vault z vašeho počítače systému SQL Server v místním](https://msdn.microsoft.com/library/dn198405.aspx). Ale pro SQL Server na virtuálních počítačích Azure, můžete ušetřit čas pomocí *integrace Azure Key Vault* funkce. S několika rutinami Azure Powershellu pro tuto funkci povolit můžete automatizovat konfiguraci pro virtuální počítač SQL pro přístup k trezoru klíčů.

Pokud je tato funkce povolena, automaticky se nainstaluje konektor SQL serveru, nakonfiguruje zprostředkovatele EKM. pro přístup k Azure Key Vault a vytvoří přihlašovací údaj, který umožňuje přístup k trezoru. Pokud podívali se na postup v dokumentaci k výše uvedené v místním, uvidíte, že tato funkce automatizuje kroky 2 a 3. Jediné, co by stále muset provést ručně, je vytvořit trezor klíčů a klíče. Tady je automatické celé nastavení virtuálního počítače s SQL. Po dokončení této instalace a nastavení tuto funkci můžete spustit příkazy T-SQL k zahájit šifrování vašich databází nebo zálohy jako obvykle.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Konfigurace integrace se službou AZURE
Konfigurace integrace Azure Key Vault pomocí Powershellu. Následující části poskytují přehled o požadované parametry a potom ukázkový skript Powershellu.

### <a name="install-the-sql-server-iaas-extension"></a>Instalace rozšíření SQL Server IaaS
Nejprve je potřeba [instalace rozšíření SQL Server IaaS](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Vysvětlení vstupní parametry
V následující tabulce jsou uvedeny parametrů požadovaných pro spuštění skriptu prostředí PowerShell v další části.

| Parametr | Popis | Příklad: |
| --- | --- | --- |
| **$akvURL** |**Adresa URL služby key vault** |https://contosokeyvault.vault.azure.net/ |
| **$spName** |**Hlavní název služby** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Tajný klíč instančního objektu** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**Název přihlašovacího údaje**: Integrace se službou AZURE vytvoří přihlašovací údaje v rámci SQL serveru, díky čemuž mají přístup k trezoru klíčů virtuální počítače. Zvolte název pro tyto přihlašovací údaje. |"mycred1" |
| **$vmName** |**Název virtuálního počítače**: Název dříve vytvořený virtuální počítač SQL. |"myvmname" |
| **$serviceName** |**Název služby**: Název cloudové služby, který je přidružený virtuální počítač SQL. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>Povolení integrace se službou AZURE pomocí Powershellu
**New-AzureVMSqlServerKeyVaultCredentialConfig** rutina vytvoří objekt konfigurace pro funkci integrace Azure Key Vault. **Set-AzureVMSqlServerExtension** konfiguruje Tato integrace s **KeyVaultCredentialSettings** parametru. Následující kroky ukazují, jak chcete použít tyto příkazy.

1. V prostředí Azure PowerShell nakonfigurujte nejprve vstupní parametry s konkrétní hodnoty, jak je popsáno v předchozích částech tohoto tématu. Následující skript představuje příklad.
   
        $akvURL = "https://contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. Pak pomocí následujícího skriptu ke konfiguraci a povolení integrace se službou AZURE.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

Rozšíření agenta SQL IaaS se aktualizuje virtuální počítač SQL tuto novou konfiguraci.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

