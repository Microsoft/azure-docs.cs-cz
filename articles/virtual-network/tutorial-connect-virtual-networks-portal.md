---
title: Připojit virtuální sítě pomocí virtuální sítě partnerský vztah - portálu Azure | Microsoft Docs
description: V tomto článku zjistěte, jak připojit virtuální sítě pomocí virtuální sítě partnerský vztah, pomocí portálu Azure.
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
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: b864c71a62289b3abef13a98b52683f7d928b8e1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Připojit virtuální sítě pomocí virtuální sítě pomocí portálu Azure vytvoření partnerského vztahu.

Virtuální sítě můžete připojit k sobě navzájem s partnerský vztah virtuální sítě. Jakmile se kterými mají partnerský virtuální sítě, prostředky v obě virtuální sítě jsou komunikovat s mezi sebou, se stejnou latenci a šířky pásma, jako kdyby byly prostředky ve stejné virtuální síti. V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> * Vytvořte dvě virtuální sítě
> * Připojení dvě virtuální sítě pomocí virtuální sítě partnerského vztahu
> * Nasazení virtuálního počítače (VM) do každé virtuální sítě
> * Komunikace mezi virtuálními počítači

Pokud dáváte přednost, můžete dokončit pomocí tohoto článku [rozhraní příkazového řádku Azure](tutorial-connect-virtual-networks-cli.md) nebo [prostředí Azure PowerShell](tutorial-connect-virtual-networks-powershell.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-virtual-networks"></a>Vytvoření virtuálních sítí

1. Vyberte **+ vytvořit prostředek** na horní, levého horního rohu portálu Azure.
2. Vyberte **sítě**a potom vyberte **virtuální síť**.
3. Zadejte, nebo vyberte následující informace, přijměte výchozí hodnoty pro zbývající nastavení a potom vyberte **vytvořit**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVirtualNetwork1|
    |Adresní prostor|10.0.0.0/16|
    |Předplatné| Vyberte své předplatné.|
    |Skupina prostředků| Vyberte **vytvořit nový** a zadejte *myResourceGroup*.|
    |Umístění| Vyberte **východní USA**.|
    |Název podsítě|Podsíť Subnet1|
    |Rozsah adres podsítě|10.0.0.0/24|

      ![Vytvoření virtuální sítě](./media/tutorial-connect-virtual-networks-portal/create-virtual-network.png)

4. Znovu, dokončete kroky 1 až 3 s následujícími změnami:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVirtualNetwork2|
    |Adresní prostor|10.1.0.0/16|
    |Skupina prostředků| Vyberte **použít existující** a pak vyberte **myResourceGroup**.|
    |Rozsah adres podsítě|10.1.0.0/24|

## <a name="peer-virtual-networks"></a>Sdílené virtuální sítě

1. Do vyhledávacího pole v horní části portálu Azure, začněte psát *MyVirtualNetwork1*. Když **myVirtualNetwork1** se zobrazí ve výsledcích hledání, vyberte ho.
2. Vyberte **partnerských vztahů**v části **nastavení**a potom vyberte **+ přidat**, jak je znázorněno na následujícím obrázku:

    ![Vytvoření partnerského vztahu](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Zadejte, nebo vyberte následující informace, přijměte výchozí hodnoty pro zbývající nastavení a potom vyberte **OK**.

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVirtualNetwork1-myVirtualNetwork2|
    |Předplatné| Vyberte své předplatné.|
    |Virtuální síť|myVirtualNetwork2 - vybrat *myVirtualNetwork2* virtuální sítě, vyberte **virtuální síť**, pak vyberte **myVirtualNetwork2**.|

    ![Nastavení partnerského vztahu](./media/tutorial-connect-virtual-networks-portal/peering-settings.png)

    **Stav partnerského vztahu** je *získaných*, jak je znázorněno na následujícím obrázku:

    ![Stav partnerského vztahu](./media/tutorial-connect-virtual-networks-portal/peering-status.png)

    Pokud nevidíte stav, aktualizujte webový prohlížeč.

4. V **vyhledávání** pole v horní části portálu Azure, začněte psát *MyVirtualNetwork2*. Když **myVirtualNetwork2** se zobrazí ve výsledcích hledání, vyberte ho.
5. Kroky 2 až 3 znovu, s následujícími změnami a potom vyberte **OK**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVirtualNetwork2-myVirtualNetwork1|
    |Virtuální síť|myVirtualNetwork1|

    **Stav partnerského vztahu** je *připojeno*. Azure také změnit stav partnerského vztahu *myVirtualNetwork2 myVirtualNetwork1* partnerského vztahu z *získaných* k *připojeno.* Partnerský vztah virtuální sítě není plně navázat, dokud je stav partnerského vztahu pro obě virtuální sítě *připojeno.* 

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvoření virtuálního počítače v každé virtuální sítě, aby mohl komunikovat mezi nimi později.

### <a name="create-the-first-vm"></a>Vytvoření první virtuální počítač

1. Vyberte **+ vytvořit prostředek** na horní, levého horního rohu portálu Azure.
2. Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**. Můžete vybrat jiný operační systém, ale zbývající kroky předpokládají, že jste vybrali **Windows Server 2016 Datacenter**. 
3. Zadejte nebo vyberte následující informace pro **Základy**, přijměte výchozí hodnoty pro zbývající nastavení a pak vyberte **vytvořit**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVm1|
    |Uživatelské jméno| Zadejte uživatelské jméno dle vlastního výběru.|
    |Heslo| Zadejte heslo dle vlastního výběru. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Skupina prostředků| Vyberte **použít existující** a pak vyberte **myResourceGroup**.|
    |Umístění| Vyberte **východní USA**.|
4. Vyberte velikost virtuálního počítače v části **zvolte velikost**.
5. Vyberte následující hodnoty pro **nastavení**, pak vyberte **OK**:
    |Nastavení|Hodnota|
    |---|---|
    |Virtuální síť| myVirtualNetwork1 – Pokud již není vybrána, vyberte **virtuální síť** a pak vyberte **myVirtualNetwork1** pod **zvolte virtuální sítě**.|
    |Podsíť| Subnet1 – Pokud je již není vybrána, vyberte **podsíť** a pak vyberte **Subnet1** pod **vyberte podsíť**.|
    
    ![Nastavení virtuálního počítače](./media/tutorial-connect-virtual-networks-portal/virtual-machine-settings.png)
 
6. V části **vytvořit** v **Souhrn**, vyberte **vytvořit** ke spuštění nasazení virtuálních počítačů.

### <a name="create-the-second-vm"></a>Vytvořit druhý virtuální počítač

Proveďte kroky 1 – 6 znovu, s následujícími změnami:

|Nastavení|Hodnota|
|---|---|
|Název | myVm2|
|Virtuální síť | myVirtualNetwork2|

Virtuální počítače trvat několik minut pro vytvoření. Příklady zbývajících kroků nepokračujte, dokud jsou oba virtuální počítače vytvořené.

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

1. V *vyhledávání* pole v horní části portálu, začněte psát *myVm1*. Když **myVm1** se zobrazí ve výsledcích hledání, vyberte ho.
2. Vytvoření připojení ke vzdálené ploše na *myVm1* virtuálních počítačů tak, že vyberete **Connect**, jak je znázorněno na následujícím obrázku:

    ![Připojení k virtuálnímu počítači](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Chcete-li připojit k virtuálnímu počítači, otevřete stažený soubor RDP. Po zobrazení výzvy vyberte **Connect**.
4. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače (budete muset vybrat možnost **další možnosti**, pak **použít jiný účet**, zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače), potom vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Vyberte **Ano** pokračovat v připojení.
6. V pozdější fázi, ping slouží ke komunikaci s *Můjvp2* virtuální počítač z *myVm1* virtuálních počítačů. Příkaz ping používá zprávu protokolu ICMP (Internet Control), který byl odepřen přes bránu Windows Firewall ve výchozím nastavení. Na *myVm1* virtuálních počítačů, povolit protokol ICMP přes bránu Windows firewall tak, aby se může odeslat příkaz ping tento virtuální počítač z *Můjvp2* v pozdější fázi, pomocí prostředí PowerShell:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```
    
    I když je příkazu ping použít ke komunikaci mezi virtuálními počítači v tomto článku, což ICMP přes bránu Windows Firewall pro nasazení v produkčním prostředí se nedoporučuje.

7. Pro připojení k *Můjvp2* virtuální počítač, zadejte následující příkaz z příkazového řádku *myVm1* virtuálních počítačů:

    ```
    mstsc /v:10.1.0.4
    ```
    
8. Vzhledem k tomu, že jste povolili příkazu ping na *myVm1*, můžete ho teď ping podle IP adresy:

    ```
    ping 10.0.0.4
    ```
    
9. Vaše relace protokolu RDP na obě odpojit *myVm1* a *Můjvp2*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, odstraňte skupinu prostředků a všechny prostředky, které obsahuje: 

1. Zadejte *myResourceGroup* v **vyhledávání** pole v horní části portálu. Až se zobrazí **myResourceGroup** ve výsledcích hledání vyberte ho.
2. Vyberte **Odstranit skupinu prostředků**.
3. Zadejte *myResourceGroup* pro **název skupiny prostředků typu:** a vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak připojit ve stejné oblasti Azure, dvě sítě pomocí virtuální sítě partnerský vztah. Můžete také peer virtuální sítě v různých [podporované oblasti](virtual-network-manage-peering.md#cross-region) a v [různých předplatných Azure](create-peering-different-subscriptions.md#portal), stejně, jako je vytváření [hvězdicové sítě návrhů](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) s partnerský vztah. Další informace o partnerském vztahu virtuální sítě najdete v tématu [partnerského vztahu Přehled virtuálních sítí](virtual-network-peering-overview.md) a [spravovat virtuální sítě partnerských vztahů](virtual-network-manage-peering.md).

Připojit k virtuální síti prostřednictvím sítě VPN svého počítače a komunikovat s prostředky ve virtuální síti, nebo peered virtuální sítě najdete v tématu [připojení počítače k virtuální síti](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
