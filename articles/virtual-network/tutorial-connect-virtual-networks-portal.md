---
title: Propojení virtuálních sítí s využitím partnerského vztahu virtuálních sítí – Kurz – Azure Portal | Microsoft Docs
description: V tomto kurzu zjistíte, jak propojit virtuální sítě s využitím partnerského vztahu virtuálních sítí pomocí webu Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: d702253c7b58b0a29c03e6563238b56ae75fa0d1
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841784"
---
# <a name="tutorial-connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Kurz: Propojení virtuálních sítí s využitím partnerského vztahu virtuálních sítí pomocí webu Azure Portal

Virtuální sítě můžete mezi sebou propojit s využitím partnerského vztahu virtuálních sítí. Po vytvoření partnerského vztahu virtuálních sítí budou moct prostředky v obou virtuálních sítích komunikovat mezi sebou se stejnou latencí a šířkou pásma, jako kdyby byly ve stejné virtuální síti. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření dvou virtuálních sítí
> * Propojení dvou virtuálních sítí s využitím partnerského vztahu virtuálních sítí
> * Nasazení virtuálního počítače do obou virtuálních sítí
> * Komunikace mezi virtuálními počítači

Pokud chcete, můžete tento kurz absolvovat s použitím [Azure CLI](tutorial-connect-virtual-networks-cli.md) nebo [Azure PowerShellu](tutorial-connect-virtual-networks-powershell.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-virtual-networks"></a>Vytvoření virtuálních sítí

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Vyberte **Sítě** a pak vyberte **Virtuální síť**.
3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVirtualNetwork1|
    |Adresní prostor|10.0.0.0/16|
    |Předplatné| Vyberte své předplatné.|
    |Skupina prostředků| Vyberte **Vytvořit novou** a zadejte *myResourceGroup*.|
    |Umístění| Vyberte **USA – východ**.|
    |Název podsítě|Podsíť Subnet1|
    |Rozsah adres podsítě|10.0.0.0/24|

      ![Vytvoření virtuální sítě](./media/tutorial-connect-virtual-networks-portal/create-virtual-network.png)

4. Zopakujte kroky 1 až 3 s následujícími změnami:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVirtualNetwork2|
    |Adresní prostor|10.1.0.0/16|
    |Skupina prostředků| Vyberte **Použít existující** a pak vyberte **myResourceGroup**.|
    |Rozsah adres podsítě|10.1.0.0/24|

## <a name="peer-virtual-networks"></a>Vytvoření partnerského vztahu virtuálních sítí

1. Do pole Hledat v horní části webu Azure Portal začněte zadávat *MyVirtualNetwork1*. Jakmile se ve výsledcích hledání zobrazí virtuální síť **myVirtualNetwork1**, vyberte ji.
2. V části **NASTAVENÍ** vyberte **Partnerské vztahy** a pak vyberte **+ Přidat**, jak je znázorněno na následujícím obrázku:

    ![Vytvoření partnerského vztahu](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **OK**.

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVirtualNetwork1-myVirtualNetwork2|
    |Předplatné| Vyberte své předplatné.|
    |Virtuální síť|myVirtualNetwork2 – Virtuální síť *myVirtualNetwork2*, vyberete tak, že vyberete **Virtuální síť** a pak **myVirtualNetwork2**.|

    ![Nastavení partnerského vztahu](./media/tutorial-connect-virtual-networks-portal/peering-settings.png)

    **STAV PARTNERSKÉHO VZTAHU** je *Zahájeno*, jak je znázorněno na následujícím obrázku:

    ![Stav partnerského vztahu](./media/tutorial-connect-virtual-networks-portal/peering-status.png)

    Pokud se stav nezobrazí, aktualizujte svůj prohlížeč.

4. Do pole **Hledat** v horní části webu Azure Portal začněte zadávat *MyVirtualNetwork2*. Jakmile se ve výsledcích hledání zobrazí virtuální síť **myVirtualNetwork2**, vyberte ji.
5. Zopakujte kroky 2 až 3 s následujícími změnami a pak vyberte **OK**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVirtualNetwork2-myVirtualNetwork1|
    |Virtuální síť|myVirtualNetwork1|

    **STAV PARTNERSKÉHO VZTAHU** je *Připojeno*. Azure také změnilo stav partnerského vztahu *myVirtualNetwork2-myVirtualNetwork1* ze *Zahájeno* na *Připojeno*. Partnerský vztah virtuálních sítí není úplně navázaný, dokud stav partnerského vztahu pro obě virtuální sítě není *Připojeno*. 

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte v obou virtuálních sítích virtuální počítač, abyste mezi nimi mohli v pozdějším kroku navázat komunikaci.

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**. Můžete vybrat jiný operační systém, ale ve zbývajících krocích se předpokládá, že jste vybrali **Windows Server 2016 Datacenter**. 
3. V části **Základy** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVm1|
    |Uživatelské jméno| Zadejte libovolné uživatelské jméno.|
    |Heslo| Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Skupina prostředků| Vyberte **Použít existující** a pak vyberte **myResourceGroup**.|
    |Umístění| Vyberte **USA – východ**.|
4. V části **Zvolte velikost** vyberte velikost virtuálního počítače.
5. V části **Nastavení** vyberte následující hodnoty a pak vyberte **OK**:
    |Nastavení|Hodnota|
    |---|---|
    |Virtuální síť| myVirtualNetwork1 – Pokud ještě není vybraná, vyberte **Virtuální síť** a pak v části **Zvolte virtuální síť** vyberte **myVirtualNetwork1**.|
    |Podsíť| Subnet1 – Pokud ještě není vybraná, vyberte **Podsíť** a pak v části **Zvolte podsíť** vyberte **Subnet1**.|
    
    ![Nastavení virtuálního počítače](./media/tutorial-connect-virtual-networks-portal/virtual-machine-settings.png)
 
6. V části **Vytvořit** na kartě **Souhrn** vyberte **Vytvořit** a spusťte nasazování virtuálního počítače.

### <a name="create-the-second-vm"></a>Vytvoření druhého virtuálního počítače

Zopakujte kroky 1 až 6 s následujícími změnami:

|Nastavení|Hodnota|
|---|---|
|Název | myVm2|
|Virtuální síť | myVirtualNetwork2|

Vytvoření virtuálních počítačů trvá několik minut. Nepokračujte ve zbývajících krocích, dokud se oba virtuální počítače nevytvoří.

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

1. Do pole *Hledat* v horní části portálu začněte zadávat *myVm1*. Jakmile se ve výsledcích hledání zobrazí virtuální počítač **myVm1**, vyberte ho.
2. Vyberte **Připojit** a vytvořte připojení ke vzdálené ploše virtuálního počítače *myVm1*, jak je znázorněno na následujícím obrázku:

    ![Připojení k virtuálnímu počítači](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Pokud se chcete připojit k virtuálnímu počítači, otevřete stažený soubor RDP. Pokud se zobrazí výzva, vyberte **Připojit**.
4. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače (abyste mohli zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače, možná budete muset vybrat **Další možnosti** a pak **Použít jiný účet**), a pak vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Vyberte **Ano** a pokračujte v připojování.
6. V pozdějším kroku se ke komunikaci s virtuálním počítačem *myVm2* z virtuálního počítače *myVm1* používá příkaz ping. Příkaz ping využívá protokol ICMP (Internet Control Message Protocol), který má ve výchozím nastavení zakázaný průchod bránou Windows Firewall. Na virtuálním počítači *myVm1* povolte průchod protokolu ICMP bránou Windows Firewall, abyste v pozdějším kroku mohli tento virtuální počítač otestovat pomocí příkazu ping v PowerShellu z virtuálního počítače *myVm2*:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```
    
    Přestože se ke komunikaci mezi virtuálními počítači v tomto kurzu používá příkaz ping, povolení průchodu protokolu ICMP bránou Windows Firewall v produkčních prostředích se nedoporučuje.

7. Pokud se chcete připojit k virtuálnímu počítači *myVm2*, zadejte následující příkaz na příkazový řádek na virtuálním počítači *myVm1*:

    ```
    mstsc /v:10.1.0.4
    ```
    
8. Vzhledem k tomu, že jste na virtuálním počítači *myVm1* povolili protokol Ping, můžete ho teď otestovat pomocí příkazu ping na jeho IP adresu:

    ```
    ping 10.0.0.4
    ```
    
9. Odpojte se z relací RDP k oběma virtuálním počítačům *myVm1* a *myVm2*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků a všechny prostředky, které obsahuje: 

1. Do pole **Hledat** v horní části portálu zadejte *myResourceGroup*. Jakmile se ve výsledcích hledání zobrazí skupina prostředků **myResourceGroup**, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. V části **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ** zadejte *myResourceGroup* a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak propojit dvě virtuální sítě ve stejné oblasti Azure s využitím partnerského vztahu virtuálních sítí. Můžete vytvářet také partnerské vztahy virtuálních sítí v různých [podporovaných oblastech](virtual-network-manage-peering.md#cross-region) a [různých předplatných Azure](create-peering-different-subscriptions.md#portal), stejně jako vytvářet [návrhy hvězdicovitých sítí](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) s partnerskými vztahy. Další informace o partnerských vztazích virtuálních sítí najdete v tématech [Přehled partnerských vztahů virtuálních sítí](virtual-network-peering-overview.md) a [Správa partnerských vztahů virtuálních sítí](virtual-network-manage-peering.md).

Informace o připojení vlastního počítače k virtuální síti prostřednictvím sítě VPN a práci s prostředky ve virtuální síti nebo v partnerských virtuálních sítích najdete v tématu [Připojení počítače k virtuální síti](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
