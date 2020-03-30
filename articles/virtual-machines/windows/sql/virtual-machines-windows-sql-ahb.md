---
title: Změna licenčního modelu pro virtuální počítač SQL Server v Azure
description: Zjistěte, jak přepnout licencování pro virtuální počítač SQL Serveru v Azure z průběžných plateb na vlastní licenci pomocí hybridní výhody Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 502d1fe599accb29ccc99c9e527f8d1c8e1d52b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201814"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>Změna licenčního modelu pro virtuální počítač SQL Serveru v Azure
Tento článek popisuje, jak změnit licenční model pro virtuální počítač SQL Server (VM) v Azure pomocí nového poskytovatele prostředků virtuálního počítače SQL, **Microsoft.SqlVirtualMachine**.

Existují tři licenční modely pro virtuální počítač, který je hostitelem SQL Serveru: průběžně placené, hybridní výhody Azure a zotavení po havárii (DR). Licenční model virtuálního počítače SQL Serveru můžete upravit pomocí portálu Azure, Azure CLI nebo PowerShellu. 

- Model **průběžných plateb** znamená, že náklady na spuštění virtuálního počítače Azure za sekundu zahrnují náklady na licenci SQL Serveru.
- [Hybridní výhoda Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) umožňuje používat vlastní licenci SQL Serveru s virtuálním počítačem, se kterým běží SQL Server. 
- Typ licence **pro zotavení po havárii** se používá pro [bezplatnou repliku zotavení po havárii](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) v Azure. 

Hybridní výhoda Azure umožňuje používat licence SQL Serveru s programem Software Assurance ("kvalifikovaná licence") na virtuálních počítačích Azure. S hybridní výhodou Azure se zákazníkům neúčtují poplatky za použití licence SQL Serveru na virtuálním počítači. Ale musí stále platit za náklady na základní cloudové výpočetní (to znamená základní sazba), úložiště a zálohy. Musí také zaplatit za vstupně-iny spojené s jejich využíváním služeb (podle potřeby).

Podle podmínek produktu Microsoft: "Zákazníci musí uvést, že používají Azure SQL Database (spravovaná instance, elastický fond a jedna databáze), Azure Data Factory, SQL Server Integration Services nebo SQL Server Virtual Machines v rámci Azure Hybridní výhody pro SQL Server při konfiguraci úloh v Azure."

Chcete-li označit použití hybridnívýhody Azure pro SQL Server na virtuálním počítači Azure a být kompatibilní, máte tři možnosti:

- Zřídit virtuální počítač pomocí přineste si vlastní licenci SQL Server image z Azure Marketplace. Tato možnost je k dispozici pouze pro zákazníky, kteří mají smlouvu Enterprise.
- Zřídit virtuální počítač pomocí bitové kopie SQL Serveru s průběžnými platbami z Azure Marketplace a aktivovat hybridní výhodu Azure.
- Vlastní instalace SQL Serveru na virtuálním počítači Azure, ruční [registraci u poskytovatele prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md)a aktivace hybridní výhody Azure.

Typ licence SQL Server je nastavena při zřízení virtuálního uživatele. Můžete jej změnit kdykoliv poté. Přepínání mezi modely licencí nevede k prostojům, nerestartuje virtuální počítač ani službu SQL Server, nepřidává žádné další náklady a je splatná okamžitě. Aktivace hybridnívýhody Azure ve skutečnosti *snižuje* náklady.

## <a name="prerequisites"></a>Požadavky

Změna modelu licencování virtuálního počítače SQL Server má následující požadavky: 

- Předplatné [Azure](https://azure.microsoft.com/free/).
- [Virtuální modul SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registrovaný u poskytovatele prostředků [virtuálního virtuálního připojení SQL](virtual-machines-windows-sql-register-with-resource-provider.md).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) je požadavek na využití [hybridní výhody Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). 


## <a name="vms-already-registered-with-the-resource-provider"></a>Virtuální aplikace, které jsou už registrované u poskytovatele prostředků 

# <a name="portal"></a>[Portál](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Licenční model můžete upravit přímo z portálu: 

1. Otevřete [portál Azure](https://portal.azure.com) a otevřete prostředek [virtuálních počítačů SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) pro váš virtuální počítač SQL Server. 
1. V části **Nastavení**vyberte **Konfigurovat** . 
1. Vyberte možnost **Hybridní výhoda Azure** a zaškrtněte políčko, abyste potvrdili, že máte licenci SQL Serveru s programem Software Assurance. 
1. V dolní části stránky **Konfigurovat** vyberte **Použít.** 

![Hybridní výhoda Azure na portálu](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

K změně licenčního modelu můžete použít azure cli.  


**Hybridní výhoda Azure**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

**Plaťte průběžně:** 

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

Pomocí prostředí PowerShell můžete změnit licenční model.

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

## <a name="vms-not-registered-with-the-resource-provider"></a>Virtuální společnosti, které nejsou registrované u poskytovatele prostředků

Pokud jste zřídit SQL Server virtuální počítač z plateb za obcí Azure Marketplace image, pak typ licence SQL Server bude průběžně platit za vámi. Pokud jste zřídit SQL Server virtuální počítač pomocí přineste si vlastní licenci image z Azure Marketplace, pak typ licence bude AHUB. Všechny virtuální počítače SQL Serveru zřízené z výchozího nastavení (s průběžnými platbami) nebo s vlastní licencí azure marketplace se automaticky zaregistrují u poskytovatele prostředků virtuálních zařízení SQL, aby mohli změnit [typ licence](#vms-already-registered-with-the-resource-provider).

Sql Server se může nainstalovat jenom na virtuálním počítači Azure prostřednictvím hybridní výhody Azure. Tyto virtuální počítače byste měli [zaregistrovat u poskytovatele prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md) nastavením licence SQL Serveru jako hybridní výhody Azure, abyste označili využití hybridních výhod Azure podle podmínek produktu Microsoft.

Typ licence virtuálního počítače SQL Server můžete změnit jako průběžnou platbu nebo hybridní výhody Azure pouze v případě, že je virtuální počítač SQL Server registrovaný u poskytovatele prostředků virtuálního počítače SQL.

## <a name="remarks"></a>Poznámky

- Zákazníci Azure Cloud Solution Provider (CSP) můžou využít hybridní výhodu Azure tak, že nejdřív nasadíte virtuální počítač s průběžnými platbami a pak ho převedete na vlastní licenci, pokud mají aktivní software Assurance.
- Pokud přehodíte prostředek virtuálního počítače SQL Server, vrátíte se k pevně zakódovanému nastavení licence bitové kopie. 
- Možnost změnit licenční model je funkce poskytovatele prostředků virtuálního virtuálního uživatele SQL. Nasazení image Azure Marketplace prostřednictvím portálu Azure automaticky zaregistruje virtuální počítač SQL Server u poskytovatele prostředků. Ale zákazníci, kteří jsou samoinstalační SQL Server bude muset ručně [zaregistrovat jejich SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md). 
- Přidání virtuálního virtuálního serveru SQL Server do skupiny dostupnosti vyžaduje opětovné vytvoření virtuálního soudu. Všechny virtuální uživatele přidané do sady dostupnosti se proto vrátí k výchozímu typu licence s průběžnými platbami. Azure Hybridní výhoda bude muset být znovu povolena. 


## <a name="limitations"></a>Omezení

Změna licenčního modelu je:
   - K dispozici pouze zákazníkům se [službou Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview).
   - Podporováno pouze pro edice Standard a Enterprise serveru SQL Server. Změny licencí pro express, web a vývojář nejsou podporovány. 
   - Podporované jenom pro virtuální počítače nasazené prostřednictvím modelu Azure Resource Manager. Virtuální počítače nasazené prostřednictvím klasického modelu nejsou podporovány. 
   - K dispozici pouze pro veřejné cloudy nebo cloudy Azure Government. 
   - Podporováno pouze na virtuálních počítačích, které mají jedno síťové rozhraní (NIC). 


## <a name="known-errors"></a>Známé chyby

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>Nebyl nalezen prostředek Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<> skupiny\<prostředků v rámci skupiny prostředků "> skupiny prostředků".

K této chybě dochází při pokusu o změnu licenčního modelu na virtuálním počítači SQL Server, který nebyl zaregistrován u poskytovatele prostředků virtuálního počítači SQL:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Budete muset zaregistrovat své předplatné u poskytovatele prostředků a potom [zaregistrovat virtuální počítač SQL Server u poskytovatele prostředků](virtual-machines-windows-sql-register-with-resource-provider.md). 


### <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>Virtuální počítač\<' vmname\>' má více než jednu jinou a nic přidruženou

K této chybě dochází na virtuálních počítačích, které mají více než jednu jinou nenokovou a konto. Před změnou licenčního modelu odeberte jednu z nicotných zařízení. I když můžete přidat nic zpět do virtuálního počítače po změně licenčního modelu, operace na webu Azure, jako je automatické zálohování a opravy již nebude podporována. 


## <a name="next-steps"></a>Další kroky

Další informace najdete v těchto článcích: 

* [Přehled sql serveru na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Nejčastější dotazy pro SQL Server na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Pokyny k cenovým hodnotě pro SQL Server na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Poznámky k verzi pro SQL Server na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


