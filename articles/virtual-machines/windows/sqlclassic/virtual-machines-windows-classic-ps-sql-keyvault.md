---
title: Trezor klíčů integraci se službou SQL Server na virtuálních počítačích Windows v Azure (klasický) | Microsoft Docs
description: Zjistěte, jak k automatizaci konfigurace systému SQL Server šifrování pro použití s Azure Key Vault. Toto téma vysvětluje, jak používat Azure Key Vault integrace s SQL serverem v modelu nasazení classic vytvořit virtuální počítače.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
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
ms.author: jroth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5fd0fb1f8ac9bb0132c64c195d4cc9c86ef8edd0
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29399725"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Konfigurace integrace Azure Key Vault pro SQL Server na virtuálních počítačích Azure (klasický)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Classic](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Přehled
Existuje více funkcí šifrování systému SQL Server, například [transparentní šifrování dat (šifrování TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [šifrování na úrovni sloupce (Vymazat)](https://msdn.microsoft.com/library/ms173744.aspx), a [zálohu šifrovacího](https://msdn.microsoft.com/library/dn449489.aspx). Tyto formuláře šifrování vyžadovat ke správě a ukládání kryptografických klíčů, které používáte pro šifrování. Službu službou Azure Key Vault (AZURE) slouží k vylepšení zabezpečení a správu tyto klíče v umístění zabezpečené a vysoce dostupné. [Konektor služby serveru SQL](http://www.microsoft.com/download/details.aspx?id=45344) umožňuje serveru SQL pro použití těchto klíčů z Azure Key Vault.

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

Pokud používáte systém SQL Server s místním počítačům, jsou [kroky, pomocí kterých můžete pro přístup k Azure Key Vault z vašeho místního počítače systému SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Ale pro SQL Server ve virtuálních počítačích Azure, můžete ušetřit čas pomocí *Azure Key Vault integrace* funkce. Pomocí několik rutin prostředí Azure PowerShell tuto funkci povolíte můžete automatizovat konfiguraci potřebné pro virtuální počítač SQL pro přístup k trezoru klíčů.

Pokud je tato funkce povolena, automaticky se nainstaluje konektor serveru SQL, nakonfiguruje zprostředkovatele EKM. pro přístup k Azure Key Vault a vytvoří pověření umožňují přístup k trezoru. Pokud zvážení kroky v dokumentaci k výše uvedených v místě, uvidíte, že tato funkce automatizuje kroky 2 a 3. Jediné, co by se stále potřeba udělat ručně je vytvoření trezoru klíčů a klíče. Z tohoto místa se automatizované celé nastavení virtuálního počítače SQL. Po dokončení této instalace této funkce můžete spustit příkazů T-SQL zahájíte šifrování databáze nebo zálohy běžným způsobem.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Konfigurace integrace se službou AZURE
Konfigurace Azure Key Vault integrace pomocí prostředí PowerShell. Následující části obsahují přehled požadované parametry a potom ukázkový skript PowerShell.

### <a name="install-the-sql-server-iaas-extension"></a>Instalace rozšíření IaaS systému SQL Server
První, [nainstalovat rozšíření SQL Server IaaS](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Pochopení vstupní parametry
Následující tabulka uvádí parametrů požadovaných pro spuštění skriptu prostředí PowerShell v další části.

| Parametr | Popis | Příklad: |
| --- | --- | --- |
| **$akvURL** |**Adresa URL trezoru klíčů** |"https://contosokeyvault.vault.azure.net/" |
| **$spName** |**Hlavní název služby** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Tajný klíč objektu služby** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**Název přihlašovacího údaje:** Integrace se službou Azure Key Vault vytvoří přihlašovací údaje v rámci SQL Serveru, díky čemuž mají virtuální počítače přístup do trezoru klíčů. Zvolte název pro tyto přihlašovací údaje. |"mycred1" |
| **$vmName** |**Název virtuálního počítače**: název vytvořeného virtuálního počítače s SQL. |"myvmname" |
| **$serviceName** |**Název služby**: název cloudové služby, který je přidružený virtuální počítač SQL. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>Povolení integrace se službou AZURE pomocí prostředí PowerShell
**New-AzureVMSqlServerKeyVaultCredentialConfig** rutina vytvoří objekt konfigurace pro funkci Azure Key Vault integrace. **Set-AzureVMSqlServerExtension** konfiguruje Tato integrace s **KeyVaultCredentialSettings** parametr. Následující kroky ukazují, jak používat tyto příkazy.

1. V prostředí Azure PowerShell nakonfigurujte nejprve vstupní parametry s konkrétní hodnoty, jak je popsáno v předchozí části tohoto tématu. Následující skript představuje příklad.
   
        $akvURL = "https://contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. Potom pomocí následujícího skriptu ke konfiguraci a povolení integrace se službou AZURE.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

Rozšíření agenta SQL IaaS aktualizuje virtuální počítač SQL tuto novou konfiguraci.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

