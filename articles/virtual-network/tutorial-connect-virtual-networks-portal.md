---
title: Připojení virtuálních sítí pomocí partnerského vztahu VNet – kurz – Azure Portal
description: V tomto kurzu zjistíte, jak propojit virtuální sítě s využitím partnerského vztahu virtuálních sítí pomocí webu Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/22/2020
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 71ba82cfd5f0f4166d25983f3f80530da45bdeac
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932943"
---
# <a name="tutorial-connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Kurz: Propojení virtuálních sítí s využitím partnerského vztahu virtuálních sítí pomocí webu Azure Portal

Virtuální sítě můžete mezi sebou propojit s využitím partnerského vztahu virtuálních sítí. Tyto virtuální sítě můžou být ve stejné oblasti nebo v různých oblastech (označuje se také jako globální partnerský vztah). Po vytvoření partnerského vztahu virtuálních sítí budou moct prostředky v obou virtuálních sítích komunikovat mezi sebou se stejnou latencí a šířkou pásma, jako kdyby byly ve stejné virtuální síti. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření dvou virtuálních sítí
> * Propojení dvou virtuálních sítí s využitím partnerského vztahu virtuálních sítí
> * Nasazení virtuálního počítače do obou virtuálních sítí
> * Komunikace mezi virtuálními počítači

Pokud chcete, můžete tento kurz absolvovat s použitím [Azure CLI](tutorial-connect-virtual-networks-cli.md) nebo [Azure PowerShellu](tutorial-connect-virtual-networks-powershell.md).

## <a name="prerequisites"></a>Požadavky

Než začnete, budete potřebovat účet Azure s aktivním předplatným. Pokud ho nemáte, můžete si [účet zdarma vytvořit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-virtual-networks"></a>Vytvoření virtuálních sítí

1. V Azure Portal vyberte **vytvořit prostředek**.
2. Vyberte **Sítě** a pak vyberte **Virtuální síť**.
3. Na kartě **základy** zadejte nebo vyberte následující informace a u zbývajících nastavení přijměte výchozí hodnoty:

    |Nastavení|Hodnota|
    |---|---|
    |Předplatné| Vyberte své předplatné.|
    |Skupina prostředků| Vyberte **Vytvořit novou** a zadejte *myResourceGroup*.|
    |Oblast| Vyberte **USA – východ**.|
    |Name|myVirtualNetwork1|

4. Na kartě **IP adresy** zadejte 10.0.0.0/16 pro pole **adresní prostor** . Klikněte na tlačítko **Přidat podsíť** níže a jako **Rozsah adres podsítě** zadejte *Subnet1* pro **název podsítě** a 10.0.0.0/24.
5. Vyberte **zkontrolovat + vytvořit** a pak vyberte **vytvořit**.
   
5. Zopakujte kroky 1 až 5 s následujícími změnami:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVirtualNetwork2|
    |Adresní prostor|10.1.0.0/16|
    |Skupina prostředků| Vyberte **Použít existující** a pak vyberte **myResourceGroup**.|
    |Název podsítě | Podsíť Subnet2|
    |Rozsah adres podsítě|10.1.0.0/24|

## <a name="peer-virtual-networks"></a>Vytvoření partnerského vztahu virtuálních sítí

1. Do pole Hledat v horní části webu Azure Portal začněte zadávat *MyVirtualNetwork1*. Jakmile se ve výsledcích hledání zobrazí virtuální síť **myVirtualNetwork1**, vyberte ji.
2. V části **Nastavení** vyberte **partnerské vztahy** a pak vyberte **Přidat**, jak je znázorněno na následujícím obrázku:

    ![Vytvoření partnerského vztahu](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **OK**.

    |Nastavení|Hodnota|
    |---|---|
    |Název partnerského vztahu z myVirtualNetwork1 do vzdálené virtuální sítě|myVirtualNetwork1-myVirtualNetwork2 – při prvním načtení stránky se tady zobrazí fráze "Vzdálená virtuální síť". Po výběru vzdálené virtuální sítě se fráze "Vzdálená virtuální síť" nahradí názvem vzdálené virtuální sítě.|
    |Předplatné| Vyberte své předplatné.|
    |Virtuální síť|myVirtualNetwork2 – Pokud chcete vybrat virtuální síť *myVirtualNetwork2* , vyberte **virtuální síť** a pak vyberte **myVirtualNetwork2 (myResourceGroup)**. Můžete vybrat virtuální síť ve stejné oblasti nebo v jiné oblasti.|
    |Název partnerského vztahu z myVirtualNetwork2 do myVirtualNetwork1|myVirtualNetwork2-myVirtualNetwork1|

    ![Nastavení partnerského vztahu](./media/tutorial-connect-virtual-networks-portal/peering-settings-bidirectional.png)

    **Stav partnerského vztahu** je *připojeno*, jak je znázorněno na následujícím obrázku:

    ![Stav partnerského vztahu](./media/tutorial-connect-virtual-networks-portal/peering-status-connected.png)

    Pokud se stav nezobrazí, aktualizujte svůj prohlížeč.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte v obou virtuálních sítích virtuální počítač, abyste mezi nimi mohli v pozdějším kroku navázat komunikaci.

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

1. V Azure Portal vyberte **vytvořit prostředek**.
2. Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**. Můžete vybrat jiný operační systém, ale ve zbývajících krocích se předpokládá, že jste vybrali **Windows Server 2016 Datacenter**. 
3. V části **Základy** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:

    |Nastavení|Hodnota|
    |---|---|
    |Skupina prostředků| Vyberte **Použít existující** a pak vyberte **myResourceGroup**.|
    |Name|myVm1|
    |Umístění| Vyberte **USA – východ**.|
    |Uživatelské jméno| Zadejte libovolné uživatelské jméno.|
    |Heslo| Zadejte libovolné heslo. Heslo musí mít délku aspoň 12 znaků a musí splňovat [definované požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
   
4. Vyberte velikost virtuálního počítače pro možnost **Velikost** .
5. V části **sítě** vyberte následující hodnoty:

    |Nastavení|Hodnota|
    |---|---|
    |Virtuální síť| myVirtualNetwork1 – Pokud ještě není vybraná, vyberte **virtuální síť** a pak vyberte **myVirtualNetwork1**.|
    |Podsíť| Subnet1 – Pokud ještě není vybraná, vyberte **podsíť** a pak vyberte **Subnet1**.|
   
6. Vyberte **Sítě**. Vyberte možnost **Povolit vybrané porty** pro **veřejné příchozí porty** . V následujícím seznamu zvolte možnost **RDP** pro možnost **vybrat příchozí porty** . 

7. Kliknutím na tlačítko **Revize + vytvořit** v levém dolním rohu spusťte nasazení virtuálního počítače.

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
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
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

> [!div class="nextstepaction"]
> [Další informace o partnerském vztahu virtuálních sítí](virtual-network-peering-overview.md)


