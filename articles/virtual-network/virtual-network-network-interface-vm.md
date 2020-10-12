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
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: f7253be2844f40ca52df2f9b3bc9cbba552fea2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85480129"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Přidání nebo odebrání síťových rozhraní na virtuálních počítačích

Naučte se, jak přidat existující síťové rozhraní při vytváření virtuálního počítače Azure (VM). Naučte se také přidat nebo odebrat síťová rozhraní z existujícího virtuálního počítače ve stavu Zastaveno (přidělení zrušeno). Síťové rozhraní umožňuje virtuálnímu počítači Azure komunikovat s internetem, Azure a místními prostředky. Virtuální počítač má jedno nebo víc síťových rozhraní. 

Pokud potřebujete přidat, změnit nebo odebrat IP adresy pro síťové rozhraní, přečtěte si téma [Správa IP adres síťového rozhraní](virtual-network-network-interface-addresses.md). Chcete-li vytvořit, změnit nebo odstranit síťová rozhraní, přečtěte si téma [Správa síťových rozhraní](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud ho nemáte, nastavte účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Před zahájením zbývající části tohoto článku dokončete jednu z těchto úloh:

- **Uživatelé portálu**: Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.

- **Uživatelé PowerShellu**: buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo z počítače spusťte PowerShell. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Na kartě Azure Cloud Shell prohlížeč Najděte rozevírací seznam **Vybrat prostředí** a pak vyberte **PowerShell** , pokud už není vybraný.

    Pokud používáte PowerShell místně, použijte Azure PowerShell modul verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az.Network`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Spuštěním příkazu `Connect-AzAccount` vytvořte připojení k Azure.

- **Uživatelé rozhraní příkazového řádku Azure (CLI)**: buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/bash), nebo spusťte CLI z počítače. Pokud používáte Azure CLI místně, použijte Azure CLI verze 2.0.26 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Spuštěním příkazu `az login` vytvořte připojení k Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Přidání existujících síťových rozhraní do nového virtuálního počítače

Když vytvoříte virtuální počítač prostřednictvím portálu, portál vytvoří síťové rozhraní s výchozím nastavením a připojí síťové rozhraní k virtuálnímu počítači za vás. Portál nemůžete použít k přidání existujících síťových rozhraní do nového virtuálního počítače nebo k vytvoření virtuálního počítače s několika síťovými rozhraními. Obojí můžete provést pomocí rozhraní příkazového řádku nebo PowerShellu. Nezapomeňte se seznámit s [omezeními](#constraints). Pokud vytvoříte virtuální počítač s více síťovými rozhraními, musíte ho také nakonfigurovat tak, aby po vytvoření virtuálního počítače správně používal tyto operační systémy. Přečtěte si, jak nakonfigurovat [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) nebo [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) pro více síťových rozhraní.

### <a name="commands"></a>Příkazy

Než vytvoříte virtuální počítač, [vytvořte síťové rozhraní](virtual-network-network-interface.md#create-a-network-interface).

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-network-nic-create)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="add-a-network-interface-to-an-existing-vm"></a>Přidání síťového rozhraní do existujícího virtuálního počítače

Postup přidání síťového rozhraní k virtuálnímu počítači:

1. Chcete-li najít existující virtuální počítač, vyhledejte [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **virtuální počítače**.

2. Vyberte název vašeho virtuálního počítače. Virtuální počítač musí podporovat počet síťových rozhraní, která chcete přidat. Pokud chcete zjistit, kolik síťových rozhraní podporuje jednotlivé velikosti virtuálních počítačů, přečtěte si část velikosti v Azure pro virtuální počítače se systémem [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [virtuální počítače s Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

3. Na panelu příkazů virtuálního počítače vyberte **zastavit**a potom v potvrzovacím dialogovém okně klikněte na **OK** . Pak počkejte, než se **stav** virtuálního počítače změní na **Zastaveno (přidělení zrušeno)**.

4. Z panelu nabídky virtuálního počítače vyberte **sítě**  >  **připojit síťové rozhraní**. Pak v části **Připojit stávající síťové rozhraní**zvolte síťové rozhraní, které se chcete připojit, a vyberte **OK**.

    >[!NOTE]
    >Síťové rozhraní, které vyberete, nemůže mít povolené urychlení sítě, nemůže mít přiřazenou adresu IPv6 a musí existovat ve stejné virtuální síti se síťovým rozhraním aktuálně připojeným k virtuálnímu počítači.

    Pokud nemáte existující síťové rozhraní, musíte ho nejdřív vytvořit. Provedete to tak, že vyberete **vytvořit síťové rozhraní**. Další informace o tom, jak vytvořit síťové rozhraní, najdete v tématu [Vytvoření síťového rozhraní](virtual-network-network-interface.md#create-a-network-interface). Další informace o dalších omezeních při přidávání síťových rozhraní k virtuálním počítačům najdete v tématu věnovaném [omezením](#constraints).

5. V řádku nabídek virtuálního počítače vyberte **Přehled**  >  **Start** a restartujte virtuální počítač.

Nyní můžete nakonfigurovat operační systém virtuálního počítače tak, aby používal více síťových rozhraní správně. Přečtěte si, jak nakonfigurovat [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) nebo [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) pro více síťových rozhraní.

### <a name="commands"></a>Příkazy

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ VM nic Add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-add) (Reference); [podrobný postup](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (Referenční dokumentace); [podrobný postup](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Zobrazení síťových rozhraní pro virtuální počítač

Můžete zobrazit síťová rozhraní aktuálně připojená k virtuálnímu počítači a získat další informace o konfiguraci každého síťového rozhraní a IP adresy přiřazené jednotlivým síťovým rozhraním. 

1. Chcete-li najít existující virtuální počítač, vyhledejte [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **virtuální počítače**.

    >[!NOTE]
    >Přihlaste se pomocí účtu, který je přiřazený k roli vlastníka, přispěvatele nebo přispěvatele sítě pro vaše předplatné. Další informace o tom, jak přiřadit role k účtům, najdete v tématu [předdefinované role pro řízení přístupu na základě role v Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).

2. Vyberte název virtuálního počítače, pro který chcete zobrazit připojená síťová rozhraní.

3. Na řádku nabídek virtuálního počítače vyberte **sítě**.

Další informace o nastaveních síťových rozhraní a o tom, jak je změnit, najdete v tématu [Správa síťových rozhraní](virtual-network-network-interface.md). Další informace o tom, jak přidat, změnit nebo odebrat IP adresy přiřazené k síťovému rozhraní, najdete v tématu [Správa IP adres síťového rozhraní](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Příkazy

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ VM nic list](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-list)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Odebrání síťového rozhraní z virtuálního počítače

1. Chcete-li najít existující virtuální počítač, vyhledejte [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **virtuální počítače**.

2. Vyberte název virtuálního počítače, pro který chcete zobrazit připojená síťová rozhraní.

3. Na panelu nástrojů virtuálního počítače vyberte **zastavit**.

4. Počkejte, až se **stav** virtuálního počítače změní na **Zastaveno (přidělení zrušeno)**.

5. Z panelu nabídky virtuálního **počítače vyberte síť**  >  **Odpojit síťové rozhraní**.

6. V dialogovém okně **Odpojit síťové rozhraní** vyberte síťové rozhraní, které se chcete odpojit. Pak vyberte **OK**.

    >[!NOTE]
    >Pokud je v seznamu uvedeno jenom jedno síťové rozhraní, nemůžete ho odpojit, protože k virtuálnímu počítači se musí vždycky připojit aspoň jedno síťové rozhraní.

### <a name="commands"></a>Příkazy

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ VM nic Remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-remove) (Reference); [podrobný postup](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (Referenční dokumentace); [podrobný postup](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Omezení

- Virtuální počítač musí mít připojené aspoň jedno síťové rozhraní.

- Virtuální počítač může mít k virtuálnímu počítači k dispozici jenom tolik síťových rozhraní, jako podporuje velikost virtuálního počítače. Další informace o tom, kolik síťových rozhraní podporuje jednotlivé velikosti virtuálních počítačů, najdete v tématu velikosti v Azure pro virtuální počítače se systémem [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [virtuální počítače s Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Všechny velikosti podporují alespoň dvě síťová rozhraní.

- Síťová rozhraní, která přidáte do virtuálního počítače, se momentálně nedají připojit k jinému virtuálnímu počítači. Další informace o tom, jak vytvářet síťová rozhraní, najdete v tématu [Vytvoření síťového rozhraní](virtual-network-network-interface.md#create-a-network-interface).

- V minulosti jste mohli přidat síťová rozhraní pouze k virtuálním počítačům, které podporovaly více síťových rozhraní a byly vytvořeny s alespoň dvěma síťovými rozhraními. Síťové rozhraní se nepovedlo přidat k virtuálnímu počítači, který se vytvořil s jedním síťovým rozhraním, a to i v případě, že velikost virtuálního počítače podporuje víc než jedno síťové rozhraní. Naopak můžete odebrat jenom síťová rozhraní z virtuálního počítače s alespoň třemi síťovými rozhraními, protože virtuální počítače vytvořené pomocí aspoň dvou síťových rozhraní vždycky musely mít aspoň dvě síťová rozhraní. Tato omezení už neplatí. Nyní můžete vytvořit virtuální počítač s libovolným počtem síťových rozhraní (až do počtu podporovaného velikostí virtuálního počítače).

- Standardně je první síťové rozhraní připojené k virtuálnímu počítači *primární* síťové rozhraní. Všechna ostatní síťová rozhraní ve virtuálním počítači jsou *sekundární* síťová rozhraní.

- Můžete řídit, které síťové rozhraní odesíláte odchozí provoz. Virtuální počítač ale ve výchozím nastavení odesílá veškerý odchozí provoz na IP adresu, která je přiřazená k primární konfiguraci protokolu IP primárního síťového rozhraní.

- V minulosti musely mít všechny virtuální počítače ve stejné skupině dostupnosti jedno nebo několik síťových rozhraní. Virtuální počítače s libovolným počtem síťových rozhraní teď můžou existovat ve stejné skupině dostupnosti až do počtu, který podporuje velikost virtuálního počítače. Virtuální počítač můžete přidat do skupiny dostupnosti jenom po jeho vytvoření. Další informace o skupinách dostupnosti najdete v tématu [Správa dostupnosti virtuálních počítačů v Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).

- Můžete propojit síťová rozhraní ve stejném virtuálním počítači s různými podsítěmi v rámci virtuální sítě. Všechna síťová rozhraní ale musí být připojená ke stejné virtuální síti.

- Můžete přidat libovolnou IP adresu pro jakoukoli konfiguraci protokolu IP libovolného primárního nebo sekundárního síťového rozhraní do fondu Azure Load Balancer back-end. V minulosti se do fondu back-endu mohla přidat jenom primární IP adresa pro primární síťové rozhraní. Další informace o IP adresách a konfiguracích najdete v tématu [Přidání, změna nebo odebrání IP adres](virtual-network-network-interface-addresses.md).

- Odstraněním virtuálního počítače se neodstraní síťová rozhraní, která jsou k němu připojená. Při odstranění virtuálního počítače se síťová rozhraní odpojí z virtuálního počítače. Tato síťová rozhraní můžete přidat do různých virtuálních počítačů nebo je odstranit.

- Dosažení optimálního výkonu v dokumentaci vyžaduje urychlení sítě. V některých případech musíte explicitně povolit akcelerované síťové služby pro virtuální počítače s [Windows](create-vm-accelerated-networking-powershell.md) nebo [Linux](create-vm-accelerated-networking-cli.md) .

## <a name="next-steps"></a>Další kroky

Pokud chcete vytvořit virtuální počítač s více síťovými rozhraními nebo IP adresami, přečtěte si:

|Úloha|Nástroj|
|---|---|
|Vytvoření virtuálního počítače s několika síťovými kartami|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Vytvoření virtuálního počítače s jedním síťovým ADAPTÉRem s více adresami IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Vytvoření virtuálního počítače s jedním síťovým ADAPTÉRem s privátní adresou IPv6 (za Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager šablona](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
