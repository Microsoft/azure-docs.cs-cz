---
title: Hromadná registrace virtuálních počítačů SQL v Azure u poskytovatele prostředků virtuálního počítače SQL | Dokumenty společnosti Microsoft
description: Hromadná registrace virtuálních operátorů SQL Server u poskytovatele prostředků virtuálního virtuálního připojení SQL pro zlepšení správy.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/21/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 015aa4c209a99921a930a51b15c3d0230722519a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75353892"
---
# <a name="bulk-register-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>Hromadná registrace virtuálních počítačů SQL v Azure s poskytovatelem prostředků virtuálních počítačů SQL

Tento článek popisuje, jak hromadně zaregistrovat virtuální počítač SQL Server (VM) `Register-SqlVMs` v Azure s poskytovatelem prostředků virtuálního počítače SQL pomocí rutiny Prostředí PowerShell.

Rutinu `Register-SqlVMs` lze použít k registraci všech virtuálních počítačů v daném seznamu předplatných, skupin prostředků nebo seznamu konkrétních virtuálních počítačů. Rutina zaregistruje virtuální počítače v _režimu zjednodušené_ správy a potom vygeneruje [sestavu i soubor protokolu](#output-description). 

Proces registrace nese žádné riziko, nemá žádné prostoje a nebude restartovat SQL Server nebo virtuální počítač. 

Další informace o poskytovateli prostředků naleznete v tématu [ZPROSTŘEDKOVATEL Prostředků virtuálního virtuálního připojení SQL](virtual-machines-windows-sql-register-with-resource-provider.md). 

## <a name="prerequisites"></a>Požadavky

Chcete-li zaregistrovat virtuální počítač SQL Server u poskytovatele prostředků, budete potřebovat následující: 

- [Předplatné Azure,](https://azure.microsoft.com/free/) které bylo [zaregistrováno u poskytovatele prostředků](virtual-machines-windows-sql-register-with-resource-provider.md#register-subscription-with-rp) a obsahuje neregistrované virtuální počítače SQL Serveru. 
- Pověření klienta použitá k registraci virtuálních počítačů existují v některé z následujících rolí RBAC: **přispěvatel virtuálního počítače**, **přispěvatel**nebo **vlastník**. 
- Nejnovější verze [prostředí Az PowerShell](/powershell/azure/new-azureps-module-az). 
- Nejnovější verze [aplikace Az.SqlVirtualMachine](https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0).

## <a name="getting-started"></a>Začínáme

Než budete pokračovat, musíte nejprve vytvořit místní kopii skriptu, importovat jej jako modul PowerShellu a připojit se k Azure. 

### <a name="create-script"></a>Vytvořit skript

Chcete-li skript vytvořit, zkopírujte [celý skript](#full-script) z konce tohoto `RegisterSqlVMs.psm1`článku a uložte jej místně jako . 

### <a name="import-script"></a>Importovat skript

Po vytvoření skriptu jej můžete importovat jako modul v terminálu Powershellu. 

Otevřete terminál prostředí PowerShell pro správu a přejděte na místo, kam jste soubor uložili. `RegisterSqlVMs.psm1` Potom spusťte následující rutinu prostředí PowerShell importovat skript jako modul: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Připojení k Azure

K Azure se můžete připojit pomocí následující rutiny Prostředí PowerShell:

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-list-of-subscriptions"></a>Všechny virtuální jevy v seznamu předplatných 

Pomocí následující rutiny můžete zaregistrovat všechny virtuální počítače SQL Serveru v seznamu předplatných:

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

Příklad výstupu: 

```
Number of Subscriptions registration failed for 
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

## <a name="all-vms-in-a-single-subscription"></a>Všechny virtuální hody v jednom předplatném

Pomocí následující rutiny zaregistrujte všechny virtuální počítače SQL Server v jednom předplatném: 

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

## <a name="all-vms-in-multiple-resource-groups"></a>Všechny virtuální uživatele ve více skupinách prostředků

Pomocí následující rutiny můžete zaregistrovat všechny virtuální počítače SQL Serveru ve více skupinách prostředků v rámci jednoho předplatného:

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

## <a name="all-vms-in-a-resource-group"></a>Všechny virtuální hody ve skupině prostředků

Pomocí následující rutiny můžete zaregistrovat všechny virtuální počítače SQL Serveru v jedné skupině prostředků: 

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

## <a name="specific-vms-in-single-resource-group"></a>Konkrétní virtuální virtuální společnosti v jedné skupině prostředků

Následující rutina slouží k registraci konkrétních virtuálních počítačů SQL Serveru v rámci jedné skupiny prostředků:

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

## <a name="specific-vm"></a>Konkrétní virtuální virtuální ms

Pomocí následující rutiny zaregistrujte konkrétní virtuální počítač SQL Server: 

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

Při každém použití rutiny `Register-SqlVMs` je generována sestava i soubor protokolu. 

### <a name="report"></a>Sestava

Sestava je generována `.txt` jako `RegisterSqlVMScriptReport<Timestamp>.txt` soubor s názvem, kde časové razítko je čas, kdy byla rutina spuštěna. V sestavě jsou uvedeny následující podrobnosti:

| **Výstupní hodnota** | **Popis** |
| :--------------  | :-------------- | 
| Počet registrací odběrů se nezdařil, protože nemáte přístup nebo jsou nesprávná pověření. | To poskytuje počet a seznam odběrů, které měly problémy s poskytnutým ověřováním. Podrobnou chybu naleznete v protokolu vyhledáním ID předplatného. | 
| Počet odběrů, které nelze vyzkoušet, protože nejsou registrovány na RP | Tato část obsahuje počet a seznam odběrů, které nebyly zaregistrovány u poskytovatele prostředků virtuálního virtuálního připojení SQL. |
| Celkový počet nalezených virtuálních mispozií | Počet virtuálních počítačů, které byly nalezeny v rozsahu parametrů předaných rutiny. | 
| Virtuální virtuální společnosti už registrované | Počet virtuálních počítačů, které byly přeskočeny, protože již byly zaregistrovány u poskytovatele prostředků. |
| Počet úspěšně registrovaných virtuálních mandů | Počet virtuálních počítačů, které byly úspěšně registrovány po spuštění rutiny. Zobrazí seznam registrovaných virtuálních počítačů ve formátu `SubscriptionID, Resource Group, Virtual Machine`. | 
| Počet virtuálních dispozičních společností se nepodařilo zaregistrovat z důvodu chyby. | Počet virtuálních počítačů, které se nepodařilo zaregistrovat z důvodu nějaké chyby. Podrobnosti o chybě lze nalézt v souboru protokolu. | 
| Počet virtuálních počítačích přeskočených jako virtuální počítač nebo agent gust na virtuálním počítači není spuštěn | Počet a seznam virtuálních počítačů, které nelze zaregistrovat jako virtuální počítač nebo agenta hosta ve virtuálním počítači, nebyly spuštěny. Ty lze zopakovat po spuštění virtuálního počítače nebo agenta hosta. Podrobnosti naleznete v souboru protokolu. |
| Počet přeskočených virtuálních počítačích, protože v systému Windows neběží SQL Server | Počet virtuálních počítačů, které byly přeskočeny, protože nejsou spuštěny SQL Server nebo nejsou virtuální počítač windows. Virtuální počítače jsou uvedeny `SubscriptionID, Resource Group, Virtual Machine`ve formátu . | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Protokol 

Chyby jsou zaznamenány v `VMsNotRegisteredDueToError<Timestamp>.log` souboru protokolu s názvem kde časové razítko je čas, kdy byl skript spuštěn. Pokud je chyba na úrovni předplatného, protokol obsahuje OdběrID oddělené čárkami a chybová zpráva. Pokud je chyba s registrací virtuálního počítače, protokol obsahuje ID předplatného, název skupiny prostředků, název virtuálního počítače, kód chyby a zprávy oddělené čárkami. 

## <a name="remarks"></a>Poznámky

Při registraci virtuálních připojení SQL Server u poskytovatele prostředků pomocí zadaný skript, zvažte následující:

- Registrace u poskytovatele prostředků vyžaduje agenta hosta spuštěného na virtuálním počítači SQL Server. Bitové kopie systému Windows Server 2008 nemají agenta hosta, takže tyto virtuální počítače se nezdaří a musí být registrovány ručně pomocí [režimu správy NoAgent](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes).
- K dispozici je logika opakování integrované k překonání transparentní chyby. Pokud je virtuální počítač úspěšně zaregistrován, pak se jedná o rychlou operaci. Pokud se však registrace nezdaří, bude každý virtuální počítač opakován.  Jako takový byste měli povolit významný čas na dokončení procesu registrace - i když skutečný časový požadavek závisí na typu a počtu chyb. 

## <a name="full-script"></a>Celý skript

Úplný skript na GitHubu najdete [v tématu Hromadné registrace virtuálních počítačů SQL pomocí prostředí Az PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1). 

Zkopírujte celý skript a `RegisterSqLVMs.psm1`uložte jej jako .

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>Další kroky

Další informace najdete v těchto článcích: 

* [Přehled sql serveru na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Nejčastější dotazy pro SQL Server na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Pokyny k cenovým hodnotě pro SQL Server na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Poznámky k verzi pro SQL Server na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)
