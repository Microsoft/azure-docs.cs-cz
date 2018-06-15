---
title: Síťová rozhraní pro přidání nebo odebrání z virtuálních počítačů Azure | Microsoft Docs
description: Naučte se síťová rozhraní pro přidání nebo odebrání síťová rozhraní z virtuálních počítačů.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: jdial
ms.openlocfilehash: 6193dcc6ba2e78c55ed6c6f769aea50fcfb3ac40
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31603455"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Síťová rozhraní pro přidání nebo odebrání síťová rozhraní z virtuálních počítačů

Zjistěte, jak přidat existující rozhraní sítě, když vytvoříte virtuální počítač Azure (VM), nebo chcete přidat nebo odebrat síťová rozhraní ze stávajícího virtuálního počítače v zastaveném stavu (deallocated). Síťové rozhraní umožňuje virtuální počítač Azure ke komunikaci s Internetem, Azure a místních prostředků. Virtuální počítač může mít jeden nebo víc síťových rozhraní. 

Pokud třeba chcete přidat, změnit nebo odebírat IP adresy pro síťové rozhraní, najdete v části [Správa IP adres rozhraní sítě](virtual-network-network-interface-addresses.md). Pokud potřebujete vytvořit, změnit, nebo odstranit síťové rozhraní, najdete v části [Spravovat síťová rozhraní](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Než začnete

Před dokončením kroků v žádné části tohoto článku dokončete následující úlohy:

- Pokud nemáte účet Azure, si zaregistrovat [Bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí účtu Azure.
- Pokud pomocí příkazů prostředí PowerShell k dokončení úloh v tomto článku, buď spusťte příkazy [prostředí cloudu Azure](https://shell.azure.com/powershell), nebo pomocí spouštění prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje prostředí Azure PowerShell verze modulu 5.2.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.
- Pokud používáte rozhraní příkazového řádku Azure (CLI) příkazy k dokončení úloh v tomto článku, buď spusťte příkazy [prostředí cloudu Azure](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z vašeho počítače. Tento kurz vyžaduje Azure CLI verze 2.0.26 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` vytvořit připojení s Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Přidat existující rozhraní sítě na nový virtuální počítač

Při vytváření virtuálního počítače prostřednictvím portálu portál vytvoří rozhraní sítě s výchozím nastavením a připojí jej k virtuálnímu počítači. Nelze přidat existující rozhraní sítě na nový virtuální počítač, ani vytvoření virtuálního počítače s více síťovými rozhraními, pomocí portálu Azure. Můžete provést i pomocí rozhraní CLI nebo Powershellu, ale nezapomeňte seznámit se s [omezení](#constraints). Pokud vytvoříte virtuální počítač s více síťovými rozhraními, musíte také nakonfigurovat operační systém používat správně po vytvoření virtuálního počítače. Naučte se konfigurovat [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) nebo [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) pro více síťových rozhraní.

### <a name="commands"></a>Příkazy

Než vytvoříte virtuální počítač, vytvořte síťové rozhraní pomocí kroků v [vytvořit síťové rozhraní](virtual-network-network-interface.md#create-a-network-interface).

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_create)|
|PowerShell|[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Přidejte síťové rozhraní stávajícího virtuálního počítače

1. Přihlaste se k portálu Azure.
2. Do vyhledávacího pole v horní části portálu, zadejte název virtuálního počítače, do které chcete přidat rozhraní sítě, nebo vyhledejte pro virtuální počítač tak, že vyberete **všechny služby**a potom **virtuální počítače**. Když jste najít virtuálního počítače, vyberte ho. Virtuální počítač musí podporovat počet síťových rozhraní, které chcete přidat. Pokud chcete zjistit, kolik síťových rozhraní každý velikost virtuálního počítače podporuje, najdete v části [velikostí pro virtuální počítače s Linuxem v Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [velikosti pro systém Windows virtuálních počítačů v Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  
3. Vyberte **přehled**v části **nastavení**. Vyberte **Zastavit**a pak počkejte, dokud **stav** virtuálního počítače se změní na **zastaveném (nepřiřazeném)**. 
4. Vyberte **sítě**v části **nastavení**.
5. Vyberte **připojit síťové rozhraní**. V seznamu síťových rozhraní, které nejsou aktuálně připojena k jiným virtuálním Počítačem vyberte ten, který chcete připojit. 

    >[!NOTE]
    Síťové rozhraní, které vyberete nemůže mít accelerated sítě povolené, nemůže mít přiřazené adresy IPv6 a musí být ve stejné virtuální síti jako ten, který obsahuje síťové rozhraní aktuálně připojen k virtuálnímu počítači. 

    Pokud nemáte existující rozhraní sítě, musíte nejprve vytvoříte jeden. Chcete-li to provést, vyberte **vytvořit síťové rozhraní**. Další informace o tom, jak vytvořit rozhraní sítě najdete v tématu [vytvořit síťové rozhraní](virtual-network-network-interface.md#create-a-network-interface). Další informace o dalších omezení při přidávání síťová rozhraní pro virtuální počítače, najdete v části [omezení](#constraints).

6. Vyberte **OK**.
7. Vyberte **přehled**v části **nastavení**a potom **spustit** ke spuštění virtuálního počítače.
8. Konfigurace operačního systému virtuálního počítače správně používat více síťových rozhraní. Naučte se konfigurovat [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) nebo [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) pro více síťových rozhraní.

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[Přidejte síťový adaptér virtuálního počítače az](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_add) (referenční dokumentace) nebo [podrobné kroky](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Přidat AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referenční dokumentace) nebo [podrobné kroky](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Zobrazení síťových rozhraní pro virtuální počítač

Můžete zobrazit rozhraní sítě, který je aktuálně připojen k virtuálnímu počítači další informace o konfiguraci každé síťové rozhraní a IP adresy přiřazené k každé síťové rozhraní. 

1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu, který má přiřazenou roli vlastník, Přispěvatel nebo Přispěvatel sítě pro vaše předplatné. Další informace o tom, jak přiřadit role na účty, najdete v části [předdefinované role pro řízení přístupu Azure na základě rolí](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Do pole, která obsahuje text **vyhledávání prostředků** v horní části portálu Azure, zadejte **virtuální počítače**. Když **virtuální počítače** se zobrazí ve výsledcích hledání, vyberte ho.
3. Vyberte název virtuálního počítače, pro který chcete zobrazit síťová rozhraní.
4. V **nastavení** část virtuálního počítače jste vybrali, vyberte **sítě**. Další informace o nastavení síťového rozhraní a jak ji změnit, najdete v části [Spravovat síťová rozhraní](virtual-network-network-interface.md). Další informace o tom, jak přidat, změnit nebo odebrat IP adresy přiřazené k síťovému rozhraní najdete v tématu [Správa IP adres rozhraní sítě](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Příkazy

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_show)|
|PowerShell|[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Odstranit síťové rozhraní z virtuálního počítače

1. Přihlaste se k portálu Azure.
2. Do vyhledávacího pole v horní části portálu, vyhledejte název virtuálního počítače, kterou chcete odebrat (odpojit) síťové rozhraní z nebo procházení pro virtuální počítač tak, že vyberete **všechny služby**a potom **virtuální počítače**. Když jste najít virtuálního počítače, vyberte ho.
3. Vyberte **přehled**v části **nastavení**a potom **Zastavit**. Počkejte **stav** virtuálního počítače se změní na **zastaveném (nepřiřazeném)**. 
4. Vyberte **sítě**v části **nastavení**.
5. Vyberte **Odpojit síťové rozhraní**. Ze seznamu síťových rozhraní, které jsou aktuálně připojen k virtuálnímu počítači vyberte síťové rozhraní, které chcete odpojit. 

    >[!NOTE]
    Pokud pouze jedno síťové rozhraní je uvedena, nelze, odpojit, protože virtuální počítač musí mít vždy alespoň jedno síťové rozhraní, které jsou k němu připojen.
6. Vyberte **OK**.

### <a name="commands"></a>Příkazy

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[síťový adaptér virtuálního počítače az odebrat](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_remove) (referenční dokumentace) nebo [podrobné kroky](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Odebrat AzureRMVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referenční dokumentace) nebo [podrobné kroky](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Omezení

- Virtuální počítač musí mít aspoň jedno síťové rozhraní, které jsou k němu připojen.
- Virtuální počítač může mít pouze jako mnoho síťových rozhraní, které jsou k němu připojen jako podporuje velikost virtuálního počítače. Další informace o tom, kolik síťových rozhraní každý velikost virtuálního počítače podporuje, najdete v části [velikostí pro virtuální počítače s Linuxem v Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [velikosti pro systém Windows virtuálních počítačů v Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Všech velikostí podporovat aspoň dvě rozhraní sítě.
- Síťových rozhraní, které přidáte do virtuálního počítače nemůže aktuálně připojen k jiným virtuálním Počítačem. Další informace o tom, jak vytvořit síťových rozhraní najdete v tématu [vytvořit síťové rozhraní](virtual-network-network-interface.md#create-a-network-interface).
- V minulosti nebylo možné síťových rozhraní přidat pouze do virtuálních počítačů, které jsou podporovány více síťovými rozhraními a byly vytvořeny s aspoň dvě rozhraní sítě. Síťové rozhraní nelze přidat k virtuálnímu počítači, který byl vytvořen s jedno síťové rozhraní, i v případě, že velikost virtuálního počítače podporované více síťových rozhraní. Naopak je může odebrat pouze síťová rozhraní z virtuálního počítače s aspoň tři síťová rozhraní, protože virtuální počítače vytvořené pomocí aspoň dva síťové rozhraní se vždy museli mít aspoň dvě rozhraní sítě. Ani jeden z těchto omezení platí už. Nyní můžete vytvořit virtuální počítač s libovolný počet síťových rozhraní (až číslo nepodporuje velikost virtuálního počítače).
- Ve výchozím nastavení, je definován první síťové rozhraní připojené k virtuálnímu počítači, jako *primární* síťové rozhraní. Všechny ostatní síťová rozhraní virtuálních počítačů jsou *sekundární* síťových rozhraní.
- I když můžete řídit, které síťové rozhraní odeslán odchozí přenosy, ve výchozím nastavení, přiřazené primární konfiguraci IP primární síťové rozhraní IP adresy odeslání všechny odchozí přenosy z virtuálního počítače.
- V minulosti všechny virtuální počítače ve stejné skupině dostupnosti musely mít jeden nebo víc síťových rozhraní. Virtuální počítače s libovolný počet síťových rozhraní může nyní existovat ve stejné sadě dostupnosti, až na číslo nepodporuje velikost virtuálního počítače. Virtuální počítač můžete přidat pouze pro sadu, když je vytvořeno dostupnosti. Další informace o nastavení dostupnosti, najdete v části [Správa dostupnosti virtuálních počítačů v Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Pokud síťová rozhraní stejného virtuálního počítače můžete připojené k různým podsítím v rámci virtuální sítě, rozhraní sítě všechny připojen ke stejné virtuální síti.
- Přidáním jakékoli IP adresu pro všechny konfigurace IP jakékoli primární nebo sekundární síťové rozhraní na fond back-end pro vyrovnávání zatížení Azure. V minulosti pouze primární IP adresa pro primární síťové rozhraní nebylo možné přidat do fondu back-end. Další informace o IP adresy a konfigurace najdete v tématu [přidat, změnit nebo odebrat IP adres](virtual-network-network-interface-addresses.md).
- Odstranění virtuálního počítače neodstraní rozhraní sítě, které jsou připojené k němu. Při odstranění virtuálního počítače, jsou z virtuálního počítače odpojit rozhraní sítě. Můžete přidat rozhraní sítě do jiné virtuální počítače nebo je odstranit.
- Pokud má síťové rozhraní s privátní adresou IPv6 přiřazen, je nutné přidat (připojení) se k virtuálnímu počítači při vytváření virtuálního počítače. Síťové rozhraní s přiřazenou adresu IPv6 nelze přidat k virtuálnímu počítači po vytvoření virtuálního počítače. Pokud přidáte síťové rozhraní s přiřazenou privátní adresu IPv6, při vytváření virtuálního počítače, můžete přidat pouze rozhraní sítě k virtuálnímu počítači, bez ohledu na to, kolik síťových rozhraní, které podporuje velikost virtuálního počítače. V tématu [Správa IP adres rozhraní sítě](virtual-network-network-interface-addresses.md) Další informace o tom, jak přiřadit IP adresy pro síťová rozhraní.
- Stejně jako u IPv6, nemůžete připojit síťové rozhraní s Zrychlený sítě povolené pro virtuální počítač po jeho vytvoření. Další abyste mohli využívat Zrychlený sítě, je nutné také provést kroky v operačním systému virtuálního počítače. Zjistěte více o Zrychlený sítě a dalších omezení při použití, [Windows](create-vm-accelerated-networking-powershell.md) nebo [Linux](create-vm-accelerated-networking-cli.md) virtuálních počítačů.

## <a name="next-steps"></a>Další postup
Vytvoření virtuálního počítače s více síťových rozhraní nebo IP adresy, přečtěte si následující články:

### <a name="commands"></a>Příkazy

|Úkol|Nástroj|
|---|---|
|Vytvoření virtuálního počítače s několika síťovými kartami|[Rozhraní příkazového řádku](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [prostředí PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Vytvoření jednoho virtuálního počítače síťový adaptér s více adresami IPv4|[Rozhraní příkazového řádku](virtual-network-multiple-ip-addresses-cli.md), [prostředí PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Vytvoření jednoho virtuálního počítače síťový adaptér s privátní adresou IPv6 (za pro vyrovnávání zatížení Azure)|[Rozhraní příkazového řádku](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [prostředí PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [šablony Azure Resource Manageru](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|


