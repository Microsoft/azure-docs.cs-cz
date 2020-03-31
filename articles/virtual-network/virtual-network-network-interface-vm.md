---
title: Přidání síťových rozhraní do virtuálních počítačů Azure nebo jejich odebrání
description: Přečtěte si, jak přidat síťová rozhraní do virtuálních počítačů nebo je odebrat.
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
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 4169bfb5da5b1ad13bab0eb01397f7c1fb20b11b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060328"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Přidání nebo odebrání síťových rozhraní na virtuálních počítačích

Zjistěte, jak přidat existující síťové rozhraní při vytváření virtuálního počítače (VM) Azure. Také se naučit přidávat nebo odebírat síťová rozhraní z existujícího virtuálního aplikace v zastaveném (problémovém) stavu. Síťové rozhraní umožňuje virtuálnímu počítači Azure komunikovat s internetem, Azure a místními prostředky. Virtuální modul má jedno nebo více síťových rozhraní. 

Pokud potřebujete přidat, změnit nebo odebrat adresy IP pro síťové rozhraní, přečtěte si informace [o řešení zásad Správy ip adres síťového rozhraní](virtual-network-network-interface-addresses.md). Informace o vytvoření, změně nebo odstranění síťových rozhraní naleznete v [tématu Správa síťových rozhraní](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud ho nemáte, nastavte si účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Před zahájením zbývající části tohoto článku proveďte jeden z těchto úkolů:

- **Uživatelé portálu:** Přihlaste se na [portál Azure](https://portal.azure.com) pomocí svého účtu Azure.

- **Uživatelé PowerShellu:** Buď spusťte příkazy v [prostředí Azure Cloud Shell](https://shell.azure.com/powershell), nebo spusťte PowerShell z počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Na kartě prohlížeče Azure Cloud Shell najděte rozevírací seznam **Select environment** a pak vyberte **PowerShell,** pokud ještě není vybraný.

    Pokud používáte PowerShell místně, použijte modul Azure PowerShell verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az.Network`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Spuštěním příkazu `Connect-AzAccount` vytvořte připojení k Azure.

- **Uživatelé rozhraní příkazového řádku Azure (CLI):** Buď spusťte příkazy v [prostředí Azure Cloud Shell](https://shell.azure.com/bash)nebo spusťte rozhraní příkazového řádku z vašeho počítače. Pokud používáte azure CLI verze 2.0.26 nebo novější, pokud používáte azure cli místně. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Spuštěním příkazu `az login` vytvořte připojení k Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Přidání existujících síťových rozhraní do nového virtuálního mísy

Když vytvoříte virtuální počítač prostřednictvím portálu, portál vytvoří síťové rozhraní s výchozím nastavením a připojí síťové rozhraní k virtuálnímu počítači za vás. Portál nelze použít k přidání existujících síťových rozhraní do nového virtuálního počítači nebo k vytvoření virtuálního počítače s více síťovými rozhraními. Můžete to udělat pomocí cli nebo PowerShell. Ujistěte se, že se seznámíte s [omezeními](#constraints). Pokud vytvoříte virtuální počítač s více síťovými rozhraními, musíte také nakonfigurovat operační systém tak, aby je správně používal po vytvoření virtuálního počítače. Přečtěte si, jak nakonfigurovat [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) nebo [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) pro více síťových rozhraní.

### <a name="commands"></a>Příkazy

Před vytvořením virtuálního [virtuálního modulu vytvořte síťové rozhraní](virtual-network-network-interface.md#create-a-network-interface).

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-network-nic-create)|
|PowerShell|[Nové rozhraní AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="add-a-network-interface-to-an-existing-vm"></a>Přidání síťového rozhraní k existujícímu virtuálnímu virtuálnímu mněmu

Přidání síťového rozhraní do virtuálního počítače:

1. Přejděte na [portál Azure](https://portal.azure.com) a najděte existující virtuální počítač. Vyhledejte a vyberte **virtuální počítače**.

2. Vyberte název virtuálního počítače. Virtuální modul musí podporovat počet síťových rozhraní, která chcete přidat. Informace o tom, kolik síťových rozhraní jednotlivé velikosti virtuálních zařízení podporují, najdete v tématu velikosti v Azure pro [linuxové virtuální počítače](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [virtuální počítače s Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

3. Na panelu příkazů Virtuální ho virtuálního stavitele vyberte **Zastavit**a pak **OK** v potvrzovacím dialogovém okně. Pak počkejte, až se **stav** virtuálního virtuálního zařízení změní na **Zastaveno (navráceno).**

4. Na řádku nabídek virtuálního modulu zvolte **Síťové** > **připojení síťového rozhraní**. Potom v **části Připojit existující síťové rozhraní**vyberte síťové rozhraní, které chcete připojit, a vyberte **OK**.

    >[!NOTE]
    >Vybrané síťové rozhraní nemůže mít povolenou zrychlenou síť, nemůže jí být přiřazena adresa IPv6 a musí existovat ve stejné virtuální síti se síťovým rozhraním aktuálně připojeným k virtuálnímu počítači.

    Pokud nemáte existující síťové rozhraní, musíte ho nejprve vytvořit. Chcete-li tak učinit, vyberte **možnost Vytvořit síťové rozhraní**. Další informace o vytvoření síťového rozhraní naleznete v [tématu Vytvoření síťového rozhraní](virtual-network-network-interface.md#create-a-network-interface). Další informace o dalších omezeních při přidávání síťových rozhraní do virtuálních počítačů najdete v [tématu Omezení](#constraints).

5. Na řádku nabídek virtuálního počítače zvolte **Přehled** > **start,** chcete-li restartovat virtuální počítač.

Teď můžete nakonfigurovat operační systém virtuálního počítače tak, aby správně používal více síťových rozhraní. Přečtěte si, jak nakonfigurovat [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) nebo [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) pro více síťových rozhraní.

### <a name="commands"></a>Příkazy

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az vm nic přidat](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-add) (odkaz); [podrobné kroky](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (odkaz); [podrobné kroky](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Zobrazení síťových rozhraní pro virtuální počítač

Můžete zobrazit síťová rozhraní aktuálně připojená k virtuálnímu počítače, abyste se dozvěděli o konfiguraci jednotlivých síťových rozhraní a adresách IP přiřazených jednotlivým síťovým rozhraním. 

1. Přejděte na [portál Azure](https://portal.azure.com) a najděte existující virtuální počítač. Vyhledejte a vyberte **virtuální počítače**.

    >[!NOTE]
    >Přihlaste se pomocí účtu, kterému je přiřazena role Vlastník, Přispěvatel nebo Síťový přispěvatel pro vaše předplatné. Další informace o tom, jak přiřadit role k účtům, najdete [v tématu předdefinované role pro řízení přístupu na základě rolí Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).

2. Vyberte název virtuálního virtuálního serveru, pro který chcete zobrazit připojená síťová rozhraní.

3. Na řádku nabídek virtuálního virtuálního zařízení vyberte **Síť .**

Informace o nastavení síťových rozhraní a jejich změně naleznete v [tématu Správa síťových rozhraní](virtual-network-network-interface.md). Informace o přidávání, změně nebo odebírání adres IP přiřazených síťovému rozhraní naleznete v [tématu Správa adres IP síťového rozhraní](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Příkazy

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az vm nic seznam](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-list)|
|PowerShell|[Získat-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Odebrání síťového rozhraní z virtuálního zařízení

1. Přejděte na [portál Azure](https://portal.azure.com) a najděte existující virtuální počítač. Vyhledejte a vyberte **virtuální počítače**.

2. Vyberte název virtuálního virtuálního serveru, pro který chcete zobrazit připojená síťová rozhraní.

3. Na panelu nástrojů virtuálního nástroje vyberte **Zastavit**.

4. Počkejte, až se **stav** virtuálního virtuálního zařízení změní na **Zastaveno (navráceno).**

5. Na panelu nabídek virtuálního modulu zvolte Síťové**rozhraní odpojení** **sítě** > .

6. V dialogovém okně **Odpojit síťové rozhraní** vyberte síťové rozhraní, které chcete odpojit. Pak vyberte **OK**.

    >[!NOTE]
    >Pokud je v seznamu uvedeno pouze jedno síťové rozhraní, nelze jej odpojit, protože virtuální počítač musí mít vždy připojeno alespoň jedno síťové rozhraní.

### <a name="commands"></a>Příkazy

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az vm nic odstranit](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-remove) (odkaz); [podrobné kroky](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (odkaz); [podrobné kroky](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Omezení

- Virtuální modul musí mít k němu připojeno alespoň jedno síťové rozhraní.

- Virtuální počítače může mít jenom tolik síťových rozhraní, které jsou k němu připojeny jako velikost virtuálního počítače podporuje. Další informace o tom, kolik síťových rozhraní jednotlivé velikosti virtuálních zařízení podporuje, najdete v tématu velikosti v Azure pro [linuxové virtuální počítače](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [virtuální počítače s Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Všechny velikosti podporují alespoň dvě síťová rozhraní.

- Síťová rozhraní, která přidáte k virtuálnímu virtuálnímu mněmu, nelze aktuálně připojit k jinému virtuálnímu virtuálnímu síti. Další informace o vytváření síťových rozhraní naleznete [v tématu Vytvoření síťového rozhraní](virtual-network-network-interface.md#create-a-network-interface).

- V minulosti bylo možné přidat síťová rozhraní pouze do virtuálních počítačů, které podporovaly více síťových rozhraní a byly vytvořeny s alespoň dvěma síťovými rozhraními. Síťové rozhraní nelze přidat k virtuálnímu virtuálnímu počítače, který byl vytvořen s jedním síťovým rozhraním, a to ani v případě, že velikost virtuálního počítače podporuje více než jedno síťové rozhraní. Naopak můžete odebrat pouze síťová rozhraní z virtuálního zařízení s alespoň třemi síťovými rozhraními, protože virtuální aplikace vytvořené s alespoň dvěma síťovými rozhraními musely mít vždy alespoň dvě síťová rozhraní. Tato omezení již neplatí. Teď můžete vytvořit virtuální virtuální počítače s libovolným počtem síťových rozhraní (až do počtu podporovaného velikostí virtuálního počítače).

- Ve výchozím nastavení je první síťové rozhraní připojené k virtuálnímu virtuálnímu modulu *primární* síťové rozhraní. Všechna ostatní síťová rozhraní ve virtuálním virtuálním ms jsou *sekundární* síťová rozhraní.

- Můžete určit, do kterého síťového rozhraní odesíláte odchozí provoz. Virtuální modul však ve výchozím nastavení odesílá veškerý odchozí provoz na adresu IP, která je přiřazena k primární konfiguraci IP primárního síťového rozhraní.

- V minulosti všechny virtuální aplikace v rámci stejné skupiny dostupnosti byly nutné mít jedno nebo více síťových rozhraní. Virtuální počítače s libovolným počtem síťových rozhraní teď můžou existovat ve stejné sadě dostupnosti, až do počtu podporovaného velikostí virtuálního počítače. Virtuální ho lze přidat jenom do skupiny dostupnosti, když se vytvoří. Další informace o sadách dostupnosti najdete [v tématu Správa dostupnosti virtuálních počítačů v Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).

- Síťová rozhraní ve stejném virtuálním počítači můžete připojit k různým podsítím v rámci virtuální sítě. Všechna síťová rozhraní však musí být připojena ke stejné virtuální síti.

- Do back-endového fondu nástroje Azure Load Balancer můžete přidat libovolnou IP adresu pro libovolnou konfiguraci IP adresy libovolného primárního nebo sekundárního síťového rozhraní. V minulosti bylo možné do fondu back-endu přidat pouze primární adresu IP pro primární síťové rozhraní. Další informace o adresách IP a konfiguracích najdete v tématu [Přidání, změna nebo odebrání ADRES IP](virtual-network-network-interface-addresses.md).

- Odstraněním virtuálního virtuálního soudu neodstraníte síťová rozhraní, která jsou k němu připojena. Když odstraníte virtuální hod, síťová rozhraní se odpojí od virtuálního virtuálního zařízení. Tato síťová rozhraní můžete přidat do různých virtuálních zařízení nebo je odstranit.

- Stejně jako u IPv6, nelze připojit síťové rozhraní s akcelerované sítě povolené pro virtuální ho po jeho vytvoření. Chcete-li dále využít výhod zrychlených sítí, musíte také provést kroky v rámci operačního systému virtuálního zařízení. Přečtěte si další informace o zrychlených sítích a dalších omezeních při jeho používání pro virtuální počítače [s Windows](create-vm-accelerated-networking-powershell.md) nebo [Linuxem.](create-vm-accelerated-networking-cli.md)

## <a name="next-steps"></a>Další kroky

Pokud chcete vytvořit virtuální počítač s více síťovými rozhraními nebo IP adresami, přečtěte si to tak, že:

|Úkol|Nástroj|
|---|---|
|Vytvoření virtuálního počítače s několika síťovými kartami|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Vytvoření jednoho virtuálního virtuálního počítače nic s více adresami IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Vytvoření jednoho virtuálního počítače nic s privátní adresou IPv6 (za Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [šablona Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
