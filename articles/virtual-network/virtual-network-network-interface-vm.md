---
title: Přidání síťových rozhraní nebo jejich odebrání z virtuálních počítačů Azure
description: Přečtěte si, jak přidat síťová rozhraní nebo odebrat síťová rozhraní z virtuálních počítačů.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: kumud
ms.openlocfilehash: a55bf014a2da10069e4e6a5f6f4eb4b8cd9ff205
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196758"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Přidat síťová rozhraní nebo odebrat síťová rozhraní z virtuálních počítačů

Naučte se, jak přidat existující síťové rozhraní, když vytvoříte virtuální počítač Azure nebo přidáte nebo odeberete síťová rozhraní ze stávajícího virtuálního počítače ve stavu Zastaveno (přidělení zrušeno). Síťové rozhraní umožňuje virtuálnímu počítači Azure komunikovat s internetem, Azure a místními prostředky. Virtuální počítač může mít jedno nebo víc síťových rozhraní. 

Pokud potřebujete přidat, změnit nebo odebrat IP adresy pro síťové rozhraní, přečtěte si téma [Správa IP adres síťového rozhraní](virtual-network-network-interface-addresses.md). Pokud potřebujete vytvořit, změnit nebo odstranit síťová rozhraní, přečtěte si téma [Správa síťových rozhraní](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Před dokončením kroků v jakékoli části tohoto článku proveďte následující úlohy:

- Pokud ještě nemáte účet Azure, zaregistrujte si [bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí svého účtu Azure.
- Pokud k dokončení úkolů v tomto článku používáte příkazy prostředí PowerShell, buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním PowerShellu z počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.
- Pokud k dokončení úkolů v tomto článku používáte příkazy rozhraní příkazového řádku Azure (CLI), buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z počítače. Tento kurz vyžaduje Azure CLI verze 2.0.26 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, je také potřeba spustit `az login` a vytvořit připojení k Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Přidání existujících síťových rozhraní do nového virtuálního počítače

Když vytvoříte virtuální počítač prostřednictvím portálu, portál vytvoří síťové rozhraní s výchozím nastavením a připojí ho k virtuálnímu počítači za vás. K novému virtuálnímu počítači nemůžete přidat existující síťová rozhraní ani vytvořit virtuální počítač s více síťovými rozhraními pomocí Azure Portal. Můžete to provést pomocí rozhraní příkazového řádku nebo PowerShellu, ale nezapomeňte se seznámit s [omezeními](#constraints). Pokud vytvoříte virtuální počítač s více síťovými rozhraními, musíte ho také nakonfigurovat tak, aby po vytvoření virtuálního počítače správně používal tyto operační systémy. Přečtěte si, jak nakonfigurovat [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) nebo [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) pro více síťových rozhraní.

### <a name="commands"></a>Příkazy

Před vytvořením virtuálního počítače vytvořte síťové rozhraní pomocí postupu v části [Vytvoření síťového rozhraní](virtual-network-network-interface.md#create-a-network-interface).

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[New-AzVM](/powershell/module/az.compute/new-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Přidání síťového rozhraní do existujícího virtuálního počítače

1. Přihlaste se k portálu Azure.
2. Do vyhledávacího pole v horní části portálu zadejte název virtuálního počítače, do kterého chcete přidat síťové rozhraní, nebo vyhledejte virtuální počítač tak, že vyberete **všechny služby**a pak **virtuální počítače**. Po nalezení virtuálního počítače ho vyberte. Virtuální počítač musí podporovat počet síťových rozhraní, která chcete přidat. Pokud chcete zjistit, kolik síťových rozhraní podporuje jednotlivé velikosti virtuálních počítačů, přečtěte si téma [velikosti pro virtuální počítače Linux v Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [velikosti pro virtuální počítače s Windows v Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  
3. V části **Nastavení**vyberte **Přehled**. Vyberte **zastavit**a potom počkejte, než se **stav** virtuálního počítače změní na **Zastaveno (přidělení zrušeno)** .
4. V části **Nastavení**vyberte **sítě**.
5. Vyberte **připojit síťové rozhraní**. Ze seznamu síťových rozhraní, která nejsou aktuálně připojená k jinému virtuálnímu počítači, vyberte ten, který se má připojit.

   >[!NOTE]
   >Síťové rozhraní, které vyberete, nemůže mít povolené urychlení sítě, nemůže mít přiřazenou adresu IPv6 a musí existovat ve stejné virtuální síti jako ta, která obsahuje síťové rozhraní aktuálně připojené k virtuálnímu počítači.

   Pokud nemáte existující síťové rozhraní, musíte ho nejdřív vytvořit. Provedete to tak, že vyberete **vytvořit síťové rozhraní**. Další informace o tom, jak vytvořit síťové rozhraní, najdete v tématu [Vytvoření síťového rozhraní](virtual-network-network-interface.md#create-a-network-interface). Další informace o dalších omezeních při přidávání síťových rozhraní k virtuálním počítačům najdete v tématu věnovaném [omezením](#constraints).

6. Vyberte **OK**.
7. V části **Nastavení**vyberte **Přehled** **a spusťte virtuální** počítač.
8. Nakonfigurujte operační systém virtuálního počítače tak, aby používal více síťových rozhraní správně. Přečtěte si, jak nakonfigurovat [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) nebo [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) pro více síťových rozhraní.

### <a name="commands"></a>Příkazy
|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ VM nic Add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (Reference) nebo [detailed Steps](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (Referenční dokumentace) nebo [Podrobné kroky](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Zobrazit síťová rozhraní pro virtuální počítač

Můžete zobrazit síťová rozhraní aktuálně připojená k virtuálnímu počítači a získat další informace o konfiguraci každého síťového rozhraní a IP adresy přiřazené jednotlivým síťovým rozhraním. 

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu, který má přiřazenou roli vlastníka, přispěvatele nebo přispěvatele sítě pro vaše předplatné. Další informace o tom, jak přiřadit role k účtům, najdete v tématu [předdefinované role pro řízení přístupu na základě role v Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Do pole, které obsahuje **prostředky vyhledávání** textu v horní části Azure Portal zadejte **virtuální počítače**. Když se **virtuální počítače** zobrazí ve výsledcích hledání, vyberte ji.
3. Vyberte název virtuálního počítače, pro který chcete zobrazit síťová rozhraní.
4. V části **Nastavení** pro vybraný virtuální počítač vyberte **sítě**. Další informace o nastaveních síťových rozhraní a o tom, jak je změnit, najdete v tématu [Správa síťových rozhraní](virtual-network-network-interface.md). Další informace o tom, jak přidat, změnit nebo odebrat IP adresy přiřazené k síťovému rozhraní, najdete v tématu [Správa IP adres síťového rozhraní](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Příkazy

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Odebrání síťového rozhraní z virtuálního počítače

1. Přihlaste se k portálu Azure.
2. Do vyhledávacího pole v horní části portálu vyhledejte název virtuálního počítače, ze kterého chcete odebrat (odpojit) síťové rozhraní, nebo vyhledejte virtuální počítač tak, že vyberete **všechny služby**a pak **virtuální počítače**. Po nalezení virtuálního počítače ho vyberte.
3. V části **Nastavení**klikněte na **Přehled**a pak na **zastavit**. Počkejte, až se **stav** virtuálního počítače změní na **Zastaveno (přidělení zrušeno)** .
4. V části **Nastavení**vyberte **sítě**.
5. Vyberte **Odpojit síťové rozhraní**. Ze seznamu síťových rozhraní, která jsou aktuálně připojená k virtuálnímu počítači, vyberte síťové rozhraní, které se má odpojit.

   >[!NOTE]
   >Pokud je v seznamu uvedeno jenom jedno síťové rozhraní, nemůžete ho odpojit, protože k virtuálnímu počítači se musí vždycky připojit aspoň jedno síťové rozhraní.
6. Vyberte **OK**.

### <a name="commands"></a>Příkazy

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ VM nic Remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (Reference) nebo [detailed Steps](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (Referenční dokumentace) nebo [Podrobné kroky](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Omezení

- Virtuální počítač musí mít připojené aspoň jedno síťové rozhraní.
- Virtuální počítač může mít k virtuálnímu počítači k dispozici jenom tolik síťových rozhraní, jako podporuje velikost virtuálního počítače. Další informace o tom, kolik síťových rozhraní podporuje jednotlivé velikosti virtuálních počítačů, najdete v tématu [velikosti pro virtuální počítače Linux v Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [velikosti pro virtuální počítače s Windows v Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Všechny velikosti podporují alespoň dvě síťová rozhraní.
- Síťová rozhraní, která přidáte do virtuálního počítače, se momentálně nedají připojit k jinému virtuálnímu počítači. Další informace o tom, jak vytvářet síťová rozhraní, najdete v tématu [Vytvoření síťového rozhraní](virtual-network-network-interface.md#create-a-network-interface).
- V minulosti mohla být síťová rozhraní přidána pouze do virtuálních počítačů, které podporovaly více síťových rozhraní a byly vytvořeny s alespoň dvěma síťovými rozhraními. Síťové rozhraní se nedalo přidat k virtuálnímu počítači, který se vytvořil s jedním síťovým rozhraním, a to i v případě, že velikost virtuálního počítače podporuje víc síťových rozhraní. Naopak můžete odebrat jenom síťová rozhraní z virtuálního počítače s alespoň třemi síťovými rozhraními, protože virtuální počítače vytvořené pomocí aspoň dvou síťových rozhraní vždycky musely mít aspoň dvě síťová rozhraní. Žádná z těchto omezení již neplatí. Nyní můžete vytvořit virtuální počítač s libovolným počtem síťových rozhraní (až do počtu podporovaného velikostí virtuálního počítače).
- Ve výchozím nastavení je první síťové rozhraní připojené k virtuálnímu počítači definované jako *primární* síťové rozhraní. Všechna ostatní síťová rozhraní ve virtuálním počítači jsou *sekundární* síťová rozhraní.
- I když můžete řídit, které síťové rozhraní jste odeslali odchozí provoz, ve výchozím nastavení se veškerý odchozí provoz z virtuálního počítače pošle na IP adresu přiřazenou primární konfiguraci protokolu IP primárního síťového rozhraní.
- V minulosti musely mít všechny virtuální počítače ve stejné skupině dostupnosti jedno nebo několik síťových rozhraní. Virtuální počítače s libovolným počtem síťových rozhraní teď můžou existovat ve stejné skupině dostupnosti až do počtu, který podporuje velikost virtuálního počítače. Virtuální počítač můžete přidat do skupiny dostupnosti jenom po jeho vytvoření. Další informace o skupinách dostupnosti najdete v tématu [Správa dostupnosti virtuálních počítačů v Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- I když se síťová rozhraní ve stejném virtuálním počítači dají připojit k různým podsítím v rámci virtuální sítě, musí být všechna síťová rozhraní připojená ke stejné virtuální síti.
- Můžete přidat libovolnou IP adresu pro jakoukoli konfiguraci protokolu IP libovolného primárního nebo sekundárního síťového rozhraní do fondu Azure Load Balancer back-end. V minulosti se do fondu back-endu mohla přidat jenom primární IP adresa pro primární síťové rozhraní. Další informace o IP adresách a konfiguracích najdete v tématu [Přidání, změna nebo odebrání IP adres](virtual-network-network-interface-addresses.md).
- Odstraněním virtuálního počítače neodstraníte síťová rozhraní, která jsou k němu připojená. Při odstranění virtuálního počítače se síťová rozhraní odpojí z virtuálního počítače. Můžete přidat síťová rozhraní do různých virtuálních počítačů nebo je odstranit.
- Stejně jako u protokolu IPv6 nemůžete připojit síťové rozhraní s povolenými akcelerovanými síťovými službami k virtuálnímu počítači po jeho vytvoření. Aby bylo možné využít urychlené síťové služby, musíte také provést kroky v operačním systému virtuálního počítače. Další informace o akcelerovaných sítích a dalších omezeních při jejich použití pro virtuální počítače se [systémem Windows](create-vm-accelerated-networking-powershell.md) nebo [Linux](create-vm-accelerated-networking-cli.md) .

## <a name="next-steps"></a>Další kroky
Pokud chcete vytvořit virtuální počítač s více síťovými rozhraními nebo IP adresami, přečtěte si následující články:

|Úkol|Nástroj|
|---|---|
|Vytvoření virtuálního počítače s několika síťovými kartami|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Vytvoření virtuálního počítače s jedním síťovým ADAPTÉRem s více adresami IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Vytvoření virtuálního počítače s jedním síťovým ADAPTÉRem s privátní adresou IPv6 (za Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager šablona](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
