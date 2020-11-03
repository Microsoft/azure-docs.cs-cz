---
title: Hromadně zaregistrujete virtuální počítače SQL v Azure pomocí poskytovatele prostředků virtuálních počítačů SQL | Microsoft Docs
description: Pro zlepšení spravovatelnosti hromadně Zaregistrujte SQL Server virtuálních počítačů pomocí poskytovatele prostředků virtuálního počítače SQL.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/21/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 0d6900d0fdf656fa8309b18971691bb35587f7f4
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286065"
---
# <a name="register-multiple-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>Registrace několika virtuálních počítačů SQL v Azure pomocí poskytovatele prostředků virtuálního počítače SQL
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento článek popisuje, jak pomocí rutiny PowerShellu hromadně zaregistrovat vaše SQL Server virtuální počítače v Azure pomocí poskytovatele prostředků virtuálního počítače SQL `Register-SqlVMs` . Když se zaregistrujete do poskytovatele prostředků virtuálního počítače SQL, nainstaluje se [rozšíření agenta SQL IaaS](sql-server-iaas-agent-extension-automate-management.md).

V tomto článku se naučíte hromadně registrovat SQL Server virtuálních počítačů. Případně můžete zaregistrovat [všechny SQL Server virtuální počítače automaticky](sql-vm-resource-provider-automatic-registration.md) nebo [jednotlivé virtuální počítače SQL Server](sql-vm-resource-provider-register.md). 

## <a name="overview"></a>Přehled

`Register-SqlVMs`Rutinu je možné použít k registraci všech virtuálních počítačů v daném seznamu předplatných, skupin prostředků nebo seznamu konkrétních virtuálních počítačů. Rutina zaregistruje virtuální počítače v režimu _prosté_ správy a pak vygeneruje [sestavu i soubor protokolu](#output-description). 

Proces registrace nevede k žádnému riziku bez výpadků a nerestartuje SQL Server ani virtuální počítač. 

Další informace najdete v tématu [poskytovatel prostředků virtuálního počítače SQL](sql-vm-resource-provider-register.md). 

## <a name="prerequisites"></a>Předpoklady

Pokud chcete zaregistrovat SQL Server virtuální počítač s poskytovatelem prostředků, budete potřebovat následující: 

- [Předplatné Azure](https://azure.microsoft.com/free/) , které je [zaregistrované u poskytovatele prostředků](sql-vm-resource-provider-register.md#register-subscription-with-rp) a obsahuje neregistrované virtuální počítače SQL Server. 
- Přihlašovací údaje klienta použité k registraci virtuálních počítačů existují v kterékoli z následujících rolí Azure: přispěvatel, **Přispěvatel** nebo **vlastník** **virtuálního počítače**. 
- Nejnovější verzi [AZ PowerShellu](/powershell/azure/new-azureps-module-az) 
- Nejnovější verzi [AZ. SqlVirtualMachine](https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0).

## <a name="get-started"></a>Začínáme

Než budete pokračovat, musíte nejdřív vytvořit místní kopii skriptu, importovat ji jako modul prostředí PowerShell a připojit se k Azure. 

### <a name="create-the-script"></a>Vytvoření skriptu

Chcete-li vytvořit skript, zkopírujte [celý skript](#full-script) na konci tohoto článku a uložte jej místně jako `RegisterSqlVMs.psm1` . 

### <a name="import-the-script"></a>Import skriptu

Po vytvoření skriptu ho můžete importovat jako modul v terminálu PowerShellu. 

Otevřete Správce terminálu PowerShellu a přejděte do umístění, kam jste `RegisterSqlVMs.psm1` soubor uložili. Pak spusťte následující rutinu PowerShellu a naimportujte skript jako modul: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Připojení k Azure

K připojení k Azure použijte následující rutinu PowerShellu:

```powershell-interactive
Connect-AzAccount
```


## <a name="register-all-vms-in-a-list-of-subscriptions"></a>Registrace všech virtuálních počítačů v seznamu předplatných 

K registraci všech SQL Serverch virtuálních počítačů v seznamu předplatných použijte následující rutinu:

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

Příklad výstupu: 

```
Number of subscriptions registration failed for 
because you do not have access or credentials are wrong: 1
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 4
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 3
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="register-all-vms-in-a-single-subscription"></a>Registrace všech virtuálních počítačů v jednom předplatném

K registraci všech SQL Server virtuálních počítačů v rámci jednoho předplatného použijte následující rutinu: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1
```

Příklad výstupu:

```
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 5
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the  guest agent on VM is not running: 2
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="register-all-vms-in-multiple-resource-groups"></a>Registrace všech virtuálních počítačů ve více skupinách prostředků

K registraci všech SQL Server virtuálních počítačů ve více skupinách prostředků v rámci jednoho předplatného použijte následující rutinu:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupList ResourceGroup1,ResourceGroup2
```

Příklad výstupu:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="register-all-vms-in-a-resource-group"></a>Registrace všech virtuálních počítačů ve skupině prostředků

K registraci všech SQL Server virtuálních počítačů do jedné skupiny prostředků použijte následující rutinu: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1
```

Příklad výstupu:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="register-specific-vms-in-a-single-resource-group"></a>Registrace konkrétních virtuálních počítačů v jedné skupině prostředků

K registraci konkrétních SQL Server virtuálních počítačů v rámci jedné skupiny prostředků použijte následující rutinu:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -VmList VM1,VM2,VM3
```

Příklad výstupu:

```
Total VMs Found: 3
VMs Already registered: 0
Number of VMs registered successfully: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="register-a-specific-vm"></a>Registrace konkrétního virtuálního počítače

K registraci konkrétního SQL Server virtuálního počítače použijte následující rutinu: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -Name VM1
```

Příklad výstupu:

```
Total VMs Found: 1
VMs Already registered: 0
Number of VMs registered successfully: 1

Please find the detailed report in  file RegisterSqlVMScriptReport1571314821.txt
```


## <a name="output-description"></a>Popis výstupu

Sestava i soubor protokolu jsou generovány při každém `Register-SqlVMs` použití rutiny. 

### <a name="report"></a>Sestava

Sestava je vygenerována jako `.txt` soubor s názvem `RegisterSqlVMScriptReport<Timestamp>.txt` , kde časové razítko je čas spuštění rutiny. V sestavě jsou uvedeny následující podrobnosti:

| **Výstupní hodnota** | **Popis** |
| :--------------  | :-------------- | 
| Počet registrací předplatných se nezdařil, protože nemáte přístup nebo jsou pověření nesprávná. | Tím zobrazíte počet a seznam předplatných, u kterých došlo k problémům se zadaným ověřením. Podrobnou chybu najdete v protokolu hledáním ID předplatného. | 
| Počet předplatných, která se nedala zkusit provést, protože nejsou zaregistrovaná na RP | Tato část obsahuje počet a seznam předplatných, která nejsou zaregistrovaná u poskytovatele prostředků virtuálního počítače SQL. |
| Celkový počet nalezených virtuálních počítačů | Počet virtuálních počítačů, které byly nalezeny v rozsahu parametrů předaných do rutiny. | 
| Virtuální počítače jsou už zaregistrované. | Počet virtuálních počítačů, které byly přeskočeny, protože byly již registrovány u poskytovatele prostředků. |
| Počet úspěšně zaregistrovaných virtuálních počítačů | Počet virtuálních počítačů, které byly úspěšně registrovány po spuštění rutiny. Zobrazí seznam registrovaných virtuálních počítačů ve formátu `SubscriptionID, Resource Group, Virtual Machine` . | 
| Kvůli chybě se nepovedlo zaregistrovat počet virtuálních počítačů. | Počet virtuálních počítačů, jejichž registrace se nezdařila z důvodu chyby. Podrobnosti o chybě najdete v souboru protokolu. | 
| Počet virtuálních počítačů vynechaných jako virtuální počítač nebo agent nárazová větru na virtuálním počítači není spuštěný. | Počet a seznam virtuálních počítačů, které se nepovedlo zaregistrovat, protože virtuální počítač nebo Agent hosta na virtuálním počítači neběží. Tato možnost se dá opakovat, jakmile se spustí virtuální počítač nebo Agent hosta. Podrobnosti najdete v souboru protokolu. |
| Počet vynechaných virtuálních počítačů, protože neběží SQL Server ve Windows | Počet virtuálních počítačů, které byly přeskočeny, protože neběží SQL Server nebo se nejedná o virtuální počítač s Windows. Virtuální počítače jsou uvedené ve formátu `SubscriptionID, Resource Group, Virtual Machine` . | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Protokol 

Chyby se zaznamenávají do souboru protokolu s názvem `VMsNotRegisteredDueToError<Timestamp>.log` , kde časové razítko je čas spuštění skriptu. Pokud je chyba na úrovni předplatného, protokol obsahuje ID předplatného oddělené čárkami a chybovou zprávu. Pokud se jedná o registraci virtuálního počítače, protokol obsahuje ID předplatného, název skupiny prostředků, název virtuálního počítače, kód chyby a zprávu oddělený čárkami. 

## <a name="remarks"></a>Poznámky

Když zaregistrujete SQL Server virtuálních počítačů pomocí poskytovatele prostředků pomocí poskytnutého skriptu, vezměte v úvahu následující skutečnosti:

- Registrace u poskytovatele prostředků vyžaduje, aby na virtuálním počítači s SQL Server spuštěný Agent hosta. Image Windows serveru 2008 nemají agenta hosta, takže tyto virtuální počítače selžou a musí být zaregistrované ručně pomocí [režimu správy Neagentů](sql-server-iaas-agent-extension-automate-management.md#management-modes).
- K překonání transparentních chyb je integrovaná logika opakování. Pokud je virtuální počítač úspěšně zaregistrován, jedná se o rychlou operaci. Pokud se ale registrace nezdaří, bude se každý virtuální počítač opakovat.  V takovém případě byste měli pro dokončení procesu registrace poskytnout značnou dobu – i když je požadavek na skutečný čas závislý na typu a počtu chyb. 

## <a name="full-script"></a>Celý skript

Úplný skript na GitHubu najdete v tématu [hromadné registraci virtuálních počítačů SQL pomocí AZ PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1). 

Zkopírujte celý skript a uložte ho jako `RegisterSqLVMs.psm1` .

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích: 

* [Přehled SQL Server na virtuálním počítači s Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Nejčastější dotazy k SQL Server na virtuálním počítači s Windows](frequently-asked-questions-faq.md)
* [Doprovodné materiály k cenách pro SQL Server na virtuálním počítači s Windows](pricing-guidance.md)
* [Poznámky k verzi pro SQL Server na virtuálním počítači s Windows](../../database/doc-changes-updates-release-notes.md)
