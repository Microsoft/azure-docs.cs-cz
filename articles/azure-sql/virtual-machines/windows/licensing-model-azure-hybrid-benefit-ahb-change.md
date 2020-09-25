---
title: Změna modelu licencí pro virtuální počítač s SQL v Azure
description: Přečtěte si, jak přepínat licencování pro SQL Server virtuální počítač v Azure z průběžných plateb podle aktuálního využití Zvýhodněné hybridní využití Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b09d808201d58b571b2fe5ceb2e228d4e1c21d11
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316949"
---
# <a name="change-the-license-model-for-a-sql-virtual-machine-in-azure"></a>Změna licenčního modelu virtuálního počítače SQL v Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


Tento článek popisuje, jak změnit model licencí pro SQL Server virtuální počítač (VM) v Azure pomocí nového poskytovatele prostředků SQL Server virtuálního počítače, **Microsoft. SqlVirtualMachine**.

Existují tři licenční modely pro virtuální počítač, který je hostitelem SQL Server: průběžné platby, Zvýhodněné hybridní využití Azure (AHB) a zotavení po havárii (DR). Model licencí vašeho SQL Server virtuálního počítače můžete upravit pomocí Azure Portal, rozhraní příkazového řádku Azure nebo PowerShellu. 

- Model s průběžnými **platbami** znamená, že náklady na spuštění virtuálního počítače Azure za sekundu zahrnují náklady na SQL Server licenci.
- [Zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) umožňuje používat vlastní SQL Server licenci s virtuálním počítačem, na kterém běží SQL Server. 
- Typ licence **pro obnovení po havárii** se používá pro [bezplatnou repliku Dr](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) v Azure. 

Zvýhodněné hybridní využití Azure umožňuje použití licencí SQL Server se Software Assurance ("kvalifikovaná licence") na virtuálních počítačích Azure. U Zvýhodněné hybridní využití Azure se zákazníkům neúčtují použití licence SQL Server na virtuálním počítači. Ale pořád musí platit za náklady na základní cloudové výpočetní prostředky (tj. základní sazba), úložiště a zálohy. Musí také platit za vstupně-výstupní operace spojené s jejich používáním služeb (podle potřeby).

Podle podmínek pro produkty společnosti Microsoft: "zákazníci musí značit, že používají Azure SQL Database (spravované instance, Elastický fond a Izolovaná databáze), Azure Data Factory, služba SSIS (SQL Server Integration Services) nebo SQL Server Virtual Machines v oblasti Zvýhodněné hybridní využití Azure pro SQL Server při konfiguraci úloh v Azure."

Chcete-li určit použití Zvýhodněné hybridní využití Azure pro SQL Server na virtuálním počítači Azure a kompatibilní, máte tři možnosti:

- Zřízení virtuálního počítače pomocí SQL Server image s vlastní licencí z Azure Marketplace Tato možnost je k dispozici pouze pro zákazníky, kteří mají smlouva Enterprise.
- Zřídit virtuální počítač pomocí SQL Server image s průběžnými platbami z Azure Marketplace a aktivovat Zvýhodněné hybridní využití Azure.
- Samoobslužná instalace SQL Server na virtuálním počítači Azure, ruční [registrace u poskytovatele prostředků virtuálního počítače SQL](sql-vm-resource-provider-register.md)a aktivace zvýhodněné hybridní využití Azure.

Typ licence SQL Server se dá nakonfigurovat při zřizování virtuálního počítače, nebo kdykoli později. Přepínání modelů licencí nepředstavuje žádné výpadky, nerestartuje virtuální počítač ani službu SQL Server, nepřidá žádné další náklady a okamžitě se projeví. Aktivace Zvýhodněné hybridní využití Azure *snižuje* náklady.

## <a name="prerequisites"></a>Požadavky

Změna modelu licencování virtuálního počítače s SQL Server má následující požadavky: 

- [Předplatné Azure](https://azure.microsoft.com/free/)
- [SQL Server virtuální počítač](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) zaregistrovaný u [poskytovatele prostředků virtuálního počítače SQL](sql-vm-resource-provider-register.md).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) je požadavek na využití [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). 


## <a name="vms-already-registered-with-the-resource-provider"></a>Virtuální počítače, které jsou už zaregistrované u poskytovatele prostředků 

# <a name="the-azure-portal"></a>[Azure Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Licenční model můžete změnit přímo z portálu: 

1. Otevřete [Azure Portal](https://portal.azure.com) a otevřete [prostředek virtuálních počítačů SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) pro váš virtuální počítač s SQL Server. 
1. V části **Nastavení**vyberte **Konfigurovat** . 
1. Vyberte možnost **zvýhodněné hybridní využití Azure** a zaškrtnutím políčka potvrďte, že máte licenci SQL Server se Software Assurance. 
1. V dolní části stránky **Konfigurace** vyberte **použít** . 

![Zvýhodněné hybridní využití Azure na portálu](./media/licensing-model-azure-hybrid-benefit-ahb-change/ahb-in-portal.png)


# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ke změně modelu licence můžete použít rozhraní příkazového řádku Azure CLI.  


**Zvýhodněné hybridní využití Azure**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

**Průběžné platby**: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

**Zotavení po havárii (DR)**

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type DR

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type DR
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pro změnu modelu licence můžete použít PowerShell.

**Zvýhodněné hybridní využití Azure**

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

**Průběžné platby**

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType PAYG
```

**Zotavení po havárii** 

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType DR
```

---

## <a name="vms-not-registered-with-the-resource-provider"></a>Virtuální počítače neregistrované u poskytovatele prostředků

Pokud jste zřídili SQL Server virtuální počítač z Azure Marketplace imagí s průběžnými platbami, SQL Server Typ licence bude průběžné platby. Pokud jste zřídili SQL Server virtuální počítač s použitím image s vlastní licencí z Azure Marketplace, typ licence bude AHUB. Všechny SQL Server virtuální počítače zřízené ve výchozím nastavení (průběžné platby) nebo vlastní licence Azure Marketplace se budou automaticky registrovat u poskytovatele prostředků virtuálního počítače SQL, aby mohli změnit [typ licence](#vms-already-registered-with-the-resource-provider).

K virtuálnímu počítači Azure se dá pomocí Zvýhodněné hybridní využití Azure nárokovat jenom samoobslužné instalace SQL Server. [Tyto virtuální počítače byste měli zaregistrovat u poskytovatele prostředků virtuálních počítačů SQL](sql-vm-resource-provider-register.md) , a to tak, že nastavíte licenci SQL Server jako zvýhodněné hybridní využití Azure, abyste označili zvýhodněné hybridní využití Azure využití podle podmínek produktu společnosti Microsoft.

Typ licence SQL Server virtuálního počítače můžete změnit jako průběžné platby nebo Zvýhodněné hybridní využití Azure, jenom když je virtuální počítač SQL Server zaregistrovaný u poskytovatele prostředků SQL VM.

## <a name="remarks"></a>Poznámky

- Zákazníci Azure Cloud Solution Provider (CSP) můžou pomocí Zvýhodněné hybridní využití Azure nejdřív nasadit virtuální počítač s průběžnými platbami a pak ho převést na vlastní licenci, pokud mají aktivní program Software Assurance.
- Pokud SQL Server prostředek virtuálního počítače, vrátíte se k pevně zakódovanému nastavení licence image. 
- Možnost změnit licenční model je funkcí poskytovatele prostředků virtuálního počítače SQL. Nasazení bitové kopie Azure Marketplace pomocí Azure Portal automaticky registruje SQL Server virtuální počítač s poskytovatelem prostředků. Zákazníci, kteří si SQL Server vlastní instalaci, ale budou muset [zaregistrovat SQL Server virtuální počítač](sql-vm-resource-provider-register.md)ručně. 
- Přidání virtuálního počítače SQL Server do skupiny dostupnosti vyžaduje opětovné vytvoření virtuálního počítače. V takovém případě se všechny virtuální počítače přidané do skupiny dostupnosti vrátí k výchozímu typu licence s průběžnými platbami. Zvýhodněné hybridní využití Azure bude nutné znovu povolit. 


## <a name="limitations"></a>Omezení

Změna licenčního modelu je:
   - Dostupné jenom pro zákazníky se [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview).
   - Podporuje se jenom pro edice Standard a Enterprise systému SQL Server. Změny licencí pro Express, web a vývojáře se nepodporují. 
   - Podporováno pouze pro virtuální počítače nasazené pomocí modelu Azure Resource Manager. Virtuální počítače nasazené prostřednictvím klasického modelu se nepodporují. 
   - K dispozici pouze pro veřejné nebo Azure Government cloudy. 
   - Podporuje se jenom na virtuálních počítačích, které mají jedno síťové rozhraní (NIC). 


## <a name="known-errors"></a>Známé chyby

Přečtěte si nejčastější chyby a jejich řešení. 

**Nepovedlo se najít prostředek Microsoft. SqlVirtualMachine/SqlVirtualMachines/ \<resource-group> ve skupině prostředků \<resource-group> .**

K této chybě dojde, když se pokusíte změnit model licence na virtuálním počítači s SQL Server, který nebyl zaregistrován u poskytovatele prostředků SQL VM:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Budete muset zaregistrovat předplatné u poskytovatele prostředků a pak [zaregistrovat svůj SQL Server virtuální počítač s poskytovatelem prostředků](sql-vm-resource-provider-register.md). 


**Virtuální počítač \<vmname\> má víc než jednu přidruženou síťovou kartu.**

K této chybě dochází na virtuálních počítačích, které mají více než jednu síťovou kartu. Před změnou modelu licencování odeberte jednu ze síťových adaptérů. I když po změně modelu licencí můžete síťové rozhraní přidat zpátky do virtuálního počítače, operace v Azure Portal, například automatické zálohování a opravy, se už nebudou podporovat. 


## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích: 

* [Přehled SQL Server na virtuálním počítači s Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Nejčastější dotazy k SQL Server na virtuálním počítači s Windows](frequently-asked-questions-faq.md)
* [Doprovodné materiály k cenách pro SQL Server na virtuálním počítači s Windows](pricing-guidance.md)
* [Poznámky k verzi pro SQL Server na virtuálním počítači s Windows](../../database/doc-changes-updates-release-notes.md)


