---
title: Postup změny modelu licencování pro SQL Server virtuální počítač v Azure
description: Přečtěte si, jak přepínat licencování pro virtuální počítač SQL v Azure z "průběžné platby" na "vlastní licenci" pomocí Zvýhodněné hybridní využití Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 37457d8ce1189f9282f4763633e944e3c2d639c9
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816723"
---
# <a name="how-to-change-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Postup změny modelu licencování pro SQL Server virtuální počítač v Azure
Tento článek popisuje, jak změnit model licencování pro SQL Server virtuální počítač v Azure pomocí nového poskytovatele prostředků virtuálního počítače SQL – **Microsoft. SqlVirtualMachine**.

Existují dva modely licencování pro virtuální počítač (VM) hostující SQL Server – průběžné platby a Zvýhodněné hybridní využití Azure (AHB). Pomocí Azure Portal, Azure CLI nebo PowerShellu teď můžete upravit licenční model vašeho virtuálního počítače SQL Server. 

Model **průběžných plateb** (PAYG) znamená, že náklady na spuštění virtuálního počítače Azure za sekundu zahrnují náklady na SQL Server licenci.
[Zvýhodněné hybridní využití Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) umožňuje používat vlastní SQL Server licenci s virtuálním počítačem se systémem SQL Server. 

Zvýhodněné hybridní využití Microsoft Azure pro SQL Server umožňuje v Virtual Machines Azure používat licence SQL Server se Software Assurance ("kvalifikovaná licence"). U Zvýhodněné hybridní využití Azure pro SQL Server se zákazníkům neúčtují používání licence SQL Server na virtuálním počítači, ale přesto musí platit za cenu základního cloudového výpočetního prostředí (tj. základní sazba), úložiště a záloh a také I/O přidružená k jejich u. se službami (podle potřeby).

V souladu s podmínkami pro produkty společnosti Microsoft musí zákazníci určit, že používají Azure SQL Database (spravované instance, Elastický fond a Izolovaná databáze), Azure Data Factory, služba SSIS (SQL Server Integration Services) nebo SQL Server Virtual Machines v rámci Azure Hybrid. Výhody pro SQL Server při konfiguraci úloh v Azure

Chcete-li určit použití Zvýhodněné hybridní využití Azure pro SQL Server na virtuálním počítači Azure a splňovat předpisy, existují tři možnosti:

1. Zřízení virtuálního počítače pomocí SQL Server Image BYOL z webu Azure Marketplace, k dispozici pouze pro zákazníky s smlouva Enterprise.
1. Zřízení virtuálního počítače pomocí SQL Server Image PAYG z Azure Marketplace a aktivace AHB
1. SQL Server vlastní instalace na virtuálním počítači Azure, ručně [Zaregistrujte své SQL Server virtuální počítač](virtual-machines-windows-sql-register-with-resource-provider.md) a aktivujte AHB.

Typ licence SQL Server se nastaví, když se virtuální počítač zřídí a dá se změnit kdykoli později. Přepínání modelů licencí nevede k **výpadkům**, nerestartuje virtuální počítač, nepřidává **žádné další náklady** (ve skutečnosti aktivace AHB *snižuje* náklady) a je **okamžitě efektivní**. 

## <a name="prerequisites"></a>Požadavky

Použití poskytovatele prostředků virtuálního počítače SQL vyžaduje rozšíření SQL IaaS. Aby bylo možné pokračovat v používání poskytovatele prostředků virtuálního počítače SQL, budete potřebovat následující:
- [Předplatného Azure](https://azure.microsoft.com/free/).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- [SQL Server virtuální počítač](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) zaregistrovaný u nainstalovaného [poskytovatele prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md) . 


## <a name="change-license-for-vms-already-registered-with-resource-provider"></a>Změna licence pro virtuální počítače, které jsou už zaregistrované u poskytovatele prostředků 

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Licenční model můžete upravit přímo z portálu. 

1. Otevřete [Azure Portal](https://portal.azure.com) a spusťte [prostředek SQL Virtual Machines](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) pro váš virtuální počítač s SQL Server. 
1. V části **Nastavení**vyberte **Konfigurovat** . 
1. Vyberte možnost **zvýhodněné hybridní využití Azure** a zaškrtnutím políčka potvrďte, že máte licenci SQL Server se Software Assurance. 
1. V dolní části stránky **Konfigurace** vyberte **použít** . 

![AHB na portálu](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Ke změně licenčního modelu můžete použít Azure CLI.  

Následující fragment kódu přepíná model licence s průběžnými platbami na BYOL (nebo používá Zvýhodněné hybridní využití Azure):

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

Následující fragment kódu přepíná model vlastní licence na průběžné platby: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Ke změně licenčního modelu můžete použít PowerShell.

Následující fragment kódu přepíná model licence s průběžnými platbami na BYOL (nebo používá Zvýhodněné hybridní využití Azure):

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

Následující fragment kódu přepíná váš BYOL model s průběžnými platbami:

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```
---

## <a name="change-license-for-vms-not-registered-with-resource-provider"></a>Změna licence pro virtuální počítače, které nejsou zaregistrované u poskytovatele prostředků

Pokud jste zřídili SQL Server virtuální počítač z image Azure Marketplace PAYG, typ licence SQL se PAYG. Pokud jste zřídili SQL Server virtuální počítač pomocí Image BYOL z Azure Marketplace pak bude typ licence AHUB. Všechny SQL Server virtuální počítače zřízené jako výchozí (PAYG) nebo BYOL Azure Marketplace image se automaticky zaregistrují s poskytovatelem prostředků SQL VM, aby mohli změnit [typ licence](#change-license-for-vms-already-registered-with-resource-provider) .

Máte nárok jenom na samoobslužnou instalaci SQL Server na virtuálním počítači Azure prostřednictvím Zvýhodněné hybridní využití Azure a [tyto virtuální počítače byste měli zaregistrovat pomocí poskytovatele prostředků virtuálních počítačů SQL](virtual-machines-windows-sql-register-with-resource-provider.md) , a to tak, že nastavíte SQL Server licenci jako v souladu s podmínkami pro produkty Microsoftu.

Typ licence pro virtuální počítač s SQL Server můžete změnit jenom jako PAYG nebo AHB, pokud je virtuální počítač SQL registrovaný u poskytovatele prostředků SQL VM. a všechny virtuální počítače SQL by měly být zaregistrované s SQL VM RP pro dodržování předpisů licencování.

## <a name="remarks"></a>Poznámky

 - Zákazníci Azure Cloud Solution partner (CSP) můžou Zvýhodněné hybridní využití Azure využít tak, že nejdřív nasadí virtuální počítač s průběžnými platbami a pak ho převede na vlastní licenci, pokud mají aktivní SA.
 - Pokud SQL Server prostředek virtuálního počítače, vrátíte se k pevně zakódovanému nastavení licence image. 
  - Možnost změnit licenční model je funkcí poskytovatele prostředků virtuálního počítače SQL. Nasazení image na Marketplace prostřednictvím Azure Portal automaticky zaregistruje SQL Server virtuální počítač s poskytovatelem prostředků. Zákazníci, kteří si SQL Server vlastní instalaci, ale budou muset ručně [zaregistrovat svůj SQL Server virtuální počítač](virtual-machines-windows-sql-register-with-resource-provider.md). 
- Přidání virtuálního počítače SQL Server do skupiny dostupnosti vyžaduje opětovné vytvoření virtuálního počítače. V takovém případě se všechny virtuální počítače přidané do skupiny dostupnosti vrátí k výchozímu typu licence s průběžnými platbami a AHB bude nutné znovu povolit. 


## <a name="limitations"></a>Omezení

 - Změna licenčního modelu je dostupná jenom pro zákazníky se Software Assurance.
 - Změna licenčního modelu je podporovaná jenom pro edici Standard a Enterprise SQL Server. Změny licencí pro Express, web a vývojáře nejsou podporovány. 
 - Změna licenčního modelu je podporovaná jenom pro virtuální počítače nasazené pomocí modelu Správce prostředků. Virtuální počítače nasazené pomocí modelu Classic nejsou podporované. Svůj virtuální počítač můžete migrovat z modelu Classic do Resource Manageru (ARM) a zaregistrovat ho u poskytovatele prostředků virtuálního počítače SQL. Jakmile je virtuální počítač zaregistrovaný u poskytovatele prostředků virtuálního počítače SQL, budou na virtuálním počítači k dispozici změny modelu licencování. 
 - Změna licenčního modelu je povolená jenom pro instalace v rámci veřejného cloudu.
 - Změna licenčního modelu je podporovaná jenom na virtuálních počítačích, které mají jednu síťovou kartu (síťové rozhraní). Na virtuálních počítačích, které mají více než jednu síťovou kartu, před provedením postupu byste nejdřív měli odebrat jednu ze síťových karet (pomocí Azure Portal). V opačném případě dojde k chybě, která bude vypadat přibližně takto: `The virtual machine '\<vmname\>' has more than one NIC associated.`I když po změně režimu licencování budete moct síťové rozhraní přidat zpátky do virtuálního počítače, operace provedené prostřednictvím stránky konfigurace SQL v Azure Portal, jako jsou automatické opravy a zálohování, se už nepovažují za podporované.


## <a name="known-errors"></a>Známé chyby

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>Nepovedlo se najít prostředek Microsoft. SqlVirtualMachine\</SqlVirtualMachines/Resource-Group > v části\<skupina prostředků% Resource-Group >. Vlastnost ' sqlServerLicenseType ' nelze u tohoto objektu najít. Ověřte, zda vlastnost existuje a lze ji nastavit.
K této chybě dochází při pokusu o změnu licenčního modelu na SQL Serverm virtuálním počítači, který nebyl zaregistrován u poskytovatele prostředků virtuálního počítače SQL. Musíte zaregistrovat poskytovatele prostředků do svého předplatného a [](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-vm-resource-provider-with-subscription)pak zaregistrovat svůj SQL Server virtuální počítač s [poskytovatelem prostředků](virtual-machines-windows-sql-register-with-resource-provider.md)SQL. 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Nejde ověřit argument u parametru SKU.
K této chybě může dojít při pokusu o změnu SQL Serverho modelu licencování virtuálních počítačů při použití Azure PowerShell > 4,0:`Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Chcete-li vyřešit tuto chybu, odkomentujte tyto řádky v předchozím fragmentu kódu prostředí PowerShell při přepínání licenčního modelu:

  ```powershell-interactive
  # the following code snippet is necessary if using Azure Powershell version > 4
  $SqlVm.Kind= "LicenseChange"
  $SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
  $SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
  ```
  
K ověření verze Azure PowerShell použijte následující kód:
  
  ```powershell-interactive
  Get-Module -ListAvailable -Name Azure -Refresh
  ```

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících článcích: 

* [Přehled SQL Server na virtuálním počítači s Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Nejčastější dotazy k SQL Server na VIRTUÁLNÍm počítači s Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server doprovodné materiály k cenám pro virtuální počítače s Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Zpráva k vydání verze Windows VM SQL Server](virtual-machines-windows-sql-server-iaas-release-notes.md)


