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
ms.subservice: management
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ea274914ac70540d5763884bbd01e41e349a530d
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879826"
---
# <a name="change-the-license-model-for-a-sql-virtual-machine-in-azure"></a>Změna licenčního modelu virtuálního počítače SQL v Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


Tento článek popisuje, jak změnit model licencí pro SQL Server virtuální počítač (VM) v Azure pomocí [rozšíření agenta SQL IaaS](./sql-server-iaas-agent-extension-automate-management.md).

## <a name="overview"></a>Přehled

Existují tři licenční modely pro virtuální počítač Azure, který je hostitelem SQL Server: průběžné platby, Zvýhodněné hybridní využití Azure (AHB) a vysoká dostupnost/zotavení po havárii (HA/DR). Model licencí vašeho SQL Server virtuálního počítače můžete upravit pomocí Azure Portal, rozhraní příkazového řádku Azure nebo PowerShellu. 

- Model s průběžnými **platbami** znamená, že náklady na spuštění virtuálního počítače Azure za sekundu zahrnují náklady na SQL Server licenci.
- [Zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) umožňuje používat vlastní SQL Server licenci s virtuálním počítačem, na kterém běží SQL Server. 
- Typ licence **ha/Dr** se používá pro [bezplatnou repliku ha/Dr](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) v Azure. 

Zvýhodněné hybridní využití Azure umožňuje použití licencí SQL Server se Software Assurance ("kvalifikovaná licence") na virtuálních počítačích Azure. U Zvýhodněné hybridní využití Azure se zákazníkům neúčtují použití licence SQL Server na virtuálním počítači. Ale pořád musí platit za náklady na základní cloudové výpočetní prostředky (tj. základní sazba), úložiště a zálohy. Musí také platit za vstupně-výstupní operace spojené s jejich používáním služeb (podle potřeby).

Podle [podmínek pro produkty](https://www.microsoft.com/licensing/terms/productoffering/MicrosoftAzureServices/EAEAS)společnosti Microsoft: "zákazníci musí značit, že používají Azure SQL Database (spravované Instance, Elastický fond a izolovaná databáze), Azure Data Factory, služba SSIS (SQL Server Integration Services) nebo SQL Server Virtual Machines v oblasti Zvýhodněné hybridní využití Azure pro SQL Server při konfiguraci úloh v Azure."

Chcete-li určit použití Zvýhodněné hybridní využití Azure pro SQL Server na virtuálním počítači Azure a kompatibilní, máte tři možnosti:

- Zřízení virtuálního počítače pomocí SQL Server image s vlastní licencí z Azure Marketplace Tato možnost je k dispozici pouze pro zákazníky, kteří mají smlouva Enterprise.
- Zřídit virtuální počítač pomocí SQL Server image s průběžnými platbami z Azure Marketplace a aktivovat Zvýhodněné hybridní využití Azure.
- Samoobslužná instalace SQL Server na virtuálním počítači Azure, ruční [registrace pomocí rozšíření agenta SQL IaaS](sql-agent-extension-manually-register-single-vm.md)a aktivace zvýhodněné hybridní využití Azure.

Typ licence SQL Server se dá nakonfigurovat při zřizování virtuálního počítače, nebo kdykoli později. Přepínání modelů licencí nepředstavuje žádné výpadky, nerestartuje virtuální počítač ani službu SQL Server, nepřidá žádné další náklady a okamžitě se projeví. Aktivace Zvýhodněné hybridní využití Azure *snižuje* náklady.

## <a name="prerequisites"></a>Požadavky

Změna modelu licencování virtuálního počítače s SQL Server má následující požadavky: 

- [Předplatné Azure](https://azure.microsoft.com/free/)
- [SQL Server virtuální počítač](./create-sql-vm-portal.md) zaregistrovaný pomocí [rozšíření agenta SQL IaaS](./sql-server-iaas-agent-extension-automate-management.md).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) je požadavek na využití [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). 


## <a name="change-license-model"></a>Změna licenčního modelu

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Licenční model můžete změnit přímo z portálu: 

1. Otevřete [Azure Portal](https://portal.azure.com) a otevřete [prostředek virtuálních počítačů SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) pro váš virtuální počítač s SQL Server. 
1. V části **Nastavení** vyberte **Konfigurovat** . 
1. Vyberte možnost **zvýhodněné hybridní využití Azure** a zaškrtnutím políčka potvrďte, že máte licenci SQL Server se Software Assurance. 
1. V dolní části stránky **Konfigurace** vyberte **použít** . 

![Zvýhodněné hybridní využití Azure na portálu](./media/licensing-model-azure-hybrid-benefit-ahb-change/ahb-in-portal.png)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ke změně modelu licence můžete použít rozhraní příkazového řádku Azure CLI.  

Zadejte následující hodnoty pro **typ licence**:
- `AHUB` pro Zvýhodněné hybridní využití Azure
- `PAYG` pro průběžné platby
- `DR` Aktivace bezplatné repliky HA/DR


```azurecli-interactive
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type <license-type>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pro změnu modelu licence můžete použít PowerShell.

Zadejte následující hodnoty pro **typ licence**:
- `AHUB` pro Zvýhodněné hybridní využití Azure
- `PAYG` pro průběžné platby
- `DR` Aktivace bezplatné repliky HA/DR


```powershell-interactive
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType <license-type>
```

---

## <a name="remarks"></a>Poznámky

- Zákazníci Azure Cloud Solution Provider (CSP) můžou pomocí Zvýhodněné hybridní využití Azure nejdřív nasadit virtuální počítač s průběžnými platbami a pak ho převést na vlastní licenci, pokud mají aktivní program Software Assurance.
- Při vyřazení prostředku virtuálního počítače SQL se vrátíte k pevně zakódovanému nastavení licence image. 
- Možnost změnit model licencí je funkcí rozšíření agenta SQL IaaS. Nasazení bitové kopie Azure Marketplace pomocí Azure Portal automaticky registruje SQL Server virtuální počítač s příponou. Zákazníci, kteří si SQL Server vlastní instalaci, ale budou muset [zaregistrovat SQL Server virtuální počítač](sql-agent-extension-manually-register-single-vm.md)ručně. 
- Přidání virtuálního počítače SQL Server do skupiny dostupnosti vyžaduje opětovné vytvoření virtuálního počítače. V takovém případě se všechny virtuální počítače přidané do skupiny dostupnosti vrátí k výchozímu typu licence s průběžnými platbami. Zvýhodněné hybridní využití Azure bude nutné znovu povolit. 


## <a name="limitations"></a>Omezení

Změna licenčního modelu je:
   - Dostupné jenom pro zákazníky se [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview).
   - Podporuje se jenom pro edice Standard a Enterprise systému SQL Server. Změny licencí pro Express, web a vývojáře se nepodporují. 
   - Podporováno pouze pro virtuální počítače nasazené pomocí modelu Azure Resource Manager. Virtuální počítače nasazené prostřednictvím klasického modelu se nepodporují. 
   - K dispozici pouze pro veřejné nebo Azure Government cloudy. 
   - Podporuje se jenom na virtuálních počítačích, které mají jedno síťové rozhraní (NIC). 

> [!Note]
> Zvýhodněné hybridní využití Azure můžou mít jenom SQL Server licencování s licencemi na Software Assurance nebo předplatnými. Pokud používáte licencování serveru a licencí CAL pro SQL Server a máte program Software Assurance, můžete použít vlastní licenci na image virtuálního počítače Azure SQL Server a využít tak mobilitu licencí pro tyto servery, ale nemůžete využít jiné funkce Zvýhodněné hybridní využití Azure. 

## <a name="known-errors"></a>Známé chyby

Přečtěte si nejčastější chyby a jejich řešení. 

**Nepovedlo se najít prostředek Microsoft. SqlVirtualMachine/SqlVirtualMachines/ \<resource-group> ve skupině prostředků \<resource-group> .**

K této chybě dochází, když se pokusíte změnit model licence na virtuálním počítači s SQL Server, který nebyl zaregistrován pomocí rozšíření agenta SQL Server IaaS:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Budete muset zaregistrovat předplatné u poskytovatele prostředků a pak [zaregistrovat svůj SQL Server virtuální počítač s rozšířením agenta SQL IaaS](sql-agent-extension-manually-register-single-vm.md). 


**Virtuální počítač \<vmname\> má víc než jednu přidruženou síťovou kartu.**

K této chybě dochází na virtuálních počítačích, které mají více než jednu síťovou kartu. Před změnou modelu licencování odeberte jednu ze síťových adaptérů. I když po změně modelu licencí můžete síťové rozhraní přidat zpátky do virtuálního počítače, operace v Azure Portal, například automatické zálohování a opravy, se už nebudou podporovat. 


## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích: 

* [Přehled SQL Server na virtuálním počítači s Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Nejčastější dotazy k SQL Server na virtuálním počítači s Windows](frequently-asked-questions-faq.md)
* [Doprovodné materiály k cenách pro SQL Server na virtuálním počítači s Windows](pricing-guidance.md)
* [Poznámky k verzi pro SQL Server na virtuálním počítači s Windows](../../database/doc-changes-updates-release-notes.md)
* [Přehled rozšíření agenta SQL IaaS](./sql-server-iaas-agent-extension-automate-management.md)
