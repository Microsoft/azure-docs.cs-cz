---
title: Jak chcete-li změnit licenční model virtuálního počítače s SQL serverem v Azure | Dokumentace Microsoftu
description: Zjistěte, jak přepnout licencování pro virtuální počítač SQL v Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d5b0ff70baaba0b409cbd91ec2c68c77b98ba085
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548062"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Jak změnit licenční model virtuálního počítače s SQL serverem v Azure
Tento článek popisuje, jak změnit licenční model pro virtuální počítače s SQL serverem v Azure pomocí nového poskytovatele prostředků SQL VM - **Microsoft.SqlVirtualMachine**. Existují dva licenční modely pro virtuální počítač (VM), který je hostitelem SQL serveru – s průběžnými platbami a používání vlastní licence (BYOL). A teď se pomocí webu Azure portal nebo rozhraní příkazového řádku Azure, můžete upravit který licenční model virtuálního počítače s SQL Server používá. 

**s průběžnými platbami** modelu (PAYG) znamená, že za sekundu náklady na provozování virtuálního počítače Azure zahrnují cenu licence SQL serveru.

**Přineste svůj – používání vlastní licence** model (BYOL) se také označuje jako [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/), a to umožňuje používat vlastní licenci na SQL Server v případě virtuálních počítačů s SQL serverem. Další informace o cenách najdete v tématu [cenové Průvodce virtuálního počítače s SQL serverem](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Přepínání mezi těmito dvěma modely licence s sebou nese náklady **bez výpadků**, neprovede restartování virtuálního počítače, přidá **bez dalších poplatků** (ve skutečnosti aktivace AHB *snižuje* nákladů) a je **okamžitou platností**. 

## <a name="remarks"></a>Poznámky

 - Zákazníky CSP se můžou využívat výhody AHB tak, že nejprve nasazení virtuálního počítače s průběžnými platbami a jeho převodu do přineste svůj – používání vlastní licence. 
 - Při registraci vlastní image virtuálního počítače s SQL serverem s poskytovatelem prostředků, zadejte typ licence = "AHUB". Opuštění licence zadejte jako prázdné nebo zadání "PAYG" způsobí, že registrace selže. 
 - Pokud odstraníte váš prostředek virtuálního počítače s SQL serverem, přejdete zpět na pevně zakódované licence nastavení bitové kopie. 
 - Umožňuje změnit licenční model je funkce poskytovatele prostředků virtuálního počítače s SQL. Manipulace s virtuálního počítače s SQL serverem v rámci portálu automaticky zaregistruje poskytovatele prostředků virtuálního počítače s SQL serverem. Však _některé_ zákazníci mohou muset ručně [registraci jejich virtuální počítač s SQL serverem](#register-sql-server-vm-with-the-sql-vm-resource-provider) s poskytovatelem prostředků, jako například:
     - Zákazníci, kteří nasazen jejich virtuální počítač SQL Server pomocí Powershellu nebo rozhraní příkazového řádku Azure. 
     - Zákazníci, kteří svým nainstalovali systém SQL Server na bitovou kopii jiného typu než SQL Server. 
     - Zákazníci, kteří nasazen jejich virtuální počítač s použitím vlastní virtuální pevné disky. 

 
## <a name="limitations"></a>Omezení

 - Možnost převést licenční model je aktuálně k dispozici pouze v případě, že začnete s imagí virtuálního počítače s SQL Serverem s průběžnými platbami. Pokud začnete s imagí s použitím vlastní licence z portálu, nebudete moct tuto image převést na průběžné platby.
 - Změna licenčního modelu je podporována pouze pro virtuální počítače nasazené pomocí modelu Resource Manager. Virtuální počítače nasazené pomocí klasického modelu, nejsou podporovány. 
 - Změna licenčního modelu je povolena pouze pro veřejné cloudové zařízení.
 - Změna licenčního modelu je podporována pouze na virtuálních počítačích, které mají jednu síťovou kartu (síťové rozhraní). Na virtuálních počítačích, které mají více než jedním síťovým rozhraním, byste měli nejprve odebrat jedné síťové karty (pomocí webu Azure portal) před dalším pokusem postup. V opačném případě bude narazíte na chybu podobnou této: ` The virtual machine '\<vmname\>' has more than one NIC associated.` I když je možné přidat síťové rozhraní zpět k virtuálnímu počítači po změně licencování, se operace provádí prostřednictvím okno konfigurace SQL, jako jsou automatické opravy a zálohování, nelze nadále považovat podporována.

## <a name="prerequisites"></a>Požadavky

Použití zprostředkovatele prostředků virtuálního počítače s SQL se vyžaduje rozšíření SQL IaaS. V důsledku toho pokud chcete pokračovat s využitím poskytovatele prostředků virtuálního počítače s SQL, budete potřebovat následující:
- [Předplatného Azure](https://azure.microsoft.com/free/).
- [Program software assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- A *s průběžnými platbami* [virtuálního počítače s SQL serverem](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) s [rozšíření SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) nainstalované. 

## <a name="with-the-azure-portal"></a>S využitím webu Azure Portal

Můžete změnit licenční model přímo z portálu. 

1. Přejděte k virtuálnímu počítači SQL serveru v rámci [webu Azure portal](https://portal.azure.com). 
1. Vyberte **konfigurace systému SQL Server** v **nastavení** podokně. 
1. Vyberte **upravit** v **licence SQL serveru** podokno, upravte licenci. 

![AHB portálu](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)

  >[!NOTE]
  > Tato možnost není k dispozici pro Image přineste svůj – používání vlastní licence. 

## <a name="with-azure-cli"></a>S využitím rozhraní příkazového řádku Azure

Chcete-li změnit váš licenční model můžete použít rozhraní příkazového řádku Azure.  

Následující fragment kódu přepne s průběžnými platbami licenční model BYOL (nebo pomocí programu zvýhodněné hybridní využití Azure):

```azurecli
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

Následující fragment kódu přepne model přineste si – vlastní licenci na průběžné platby: 

```azurecli
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

## <a name="register-sql-server-vm-with-the-sql-vm-resource-provider"></a>Zaregistrovat poskytovatele prostředků virtuálního počítače SQL virtuálního počítače s SQL serverem
V některých případech budete muset ručně zaregistrovat virtuální počítač s SQL serverem s poskytovatelem prostředků pro virtuální počítač SQL. K tomu může také muset ručně zaregistrujte poskytovatele prostředků ve vašem předplatném. 


### <a name="register-sql-vm-resource-provider-with-subscription"></a>Registrace poskytovatele prostředků SQL VM s předplatným 

Zaregistrovat poskytovatele prostředků SQL virtuálního počítače s SQL serverem, zaregistrujte poskytovatele prostředků ke svému předplatnému. Můžete provést pomocí webu Azure portal nebo rozhraní příkazového řádku Azure. 

#### <a name="with-the-azure-portal"></a>S využitím webu Azure Portal
Následující postup k registraci poskytovatele prostředků SQL ke svému předplatnému Azure pomocí webu Azure portal. 

1. Otevřete na webu Azure portal a přejděte do **všechny služby**. 
1. Přejděte do **předplatná** a vyberte předplatné, které vás zajímají.  
1. V **předplatná** okno, přejděte na **poskytovatelů prostředků**. 
1. Typ `sql` ve filtru, abyste vyvolali se zprostředkovatelé prostředků souvisejících s SQL. 
1. Vyberte buď *zaregistrovat*, *přeregistrovat*, nebo *Unregister* pro **Microsoft.SqlVirtualMachine** poskytovateli v závislosti na vaší požadovanou akci. 

   ![Upravit poskytovatele](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

#### <a name="with-azure-cli"></a>S využitím rozhraní příkazového řádku Azure
Následující fragment kódu se zaregistrujte poskytovatele prostředků virtuálního počítače SQL ke svému předplatnému Azure. 

```azurecli
# Register the new SQL resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```


### <a name="register-sql-server-vm-with-sql-resource-provider"></a>Virtuální počítač s SQL serverem zaregistrovat poskytovatele prostředků SQL
Až do vašeho předplatného zaregistrovaný poskytovatel prostředků virtuálního počítače s SQL, pak je možné zaregistrovat virtuální počítač s SQL serverem s poskytovatelem prostředků pomocí Azure CLI. 

Registraci virtuálního počítače SQL serverem pomocí Azure CLI následujícím fragmentem kódu: 

```azurecli
# Register your existing SQL Server VM with the new resource provider
az sql vm create -n <VMName> -g <ResourceGroupName> -l <VMLocation>
```


## <a name="known-errors"></a>Známých chyb

### <a name="sql-iaas-extension-is-not-installed-on-virtual-machine"></a>Na virtuálním počítači není nainstalovaná rozšíření SQL IaaS
Rozšíření SQL IaaS je potřebná předpokladem pro registraci virtuálního počítače s SQL serverem s poskytovatelem prostředků pro virtuální počítač s SQL. Pokud se pokusíte zaregistrovat virtuálního počítače s SQL Server před instalací rozšíření SQL IaaS, se setkají následující chybu:

`Sql IaaS Extension is not installed on Virtual Machine: '{0}'. Please make sure it is installed and in running state and try again later.`

Chcete-li vyřešit tento problém, nainstalujte rozšíření SQL IaaS před pokusem o registraci vašeho virtuálního počítače s SQL serverem. 

  > [!NOTE]
  > Instalace SQL IaaS rozšíření se restartovat službu systému SQL Server a by mělo být provedeno pouze během časového období údržby. Další informace najdete v tématu [instalace rozšíření SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension#installation). 


### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>Prostředek 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/ < resource-group >"ve skupině prostředků '< resource-group >' nebyl nalezen. Vlastnost 'sqlServerLicenseType' nebyl nalezen v tomto objektu. Ověřte, že vlastnost existuje a je možné nastavit.
Této chybě dochází při pokusu o změnu licenčního modelu na virtuálním počítači SQL serveru, který není zaregistrovaný u poskytovatele prostředků SQL. Musíte se zaregistrovat poskytovatele prostředků pro vaše [předplatné](#register-sql-vm-resource-provider-with-subscription)a pak zaregistrujte virtuální počítač s SQL serverem pomocí SQL [poskytovatele prostředků](#register-sql-server-vm-with-sql-resource-provider). 

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících článcích: 

* [Přehled SQL serveru na virtuálním počítači Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server na nejčastější dotazy týkající se virtuálních počítačů Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server na virtuálním počítači s Windows, doprovodné materiály k cenám](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server na poznámky k verzi virtuálního počítače Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


