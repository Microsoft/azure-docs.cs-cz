---
title: "Připojit virtuální sítě pomocí virtuální sítě partnerský vztah - portálu Azure | Microsoft Docs"
description: "Zjistěte, jak připojit virtuální sítě pomocí virtuální sítě partnerský vztah."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: ac0c1033546758a77b43298a5fa8cba5f5204650
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Připojit virtuální sítě pomocí virtuální sítě pomocí portálu Azure vytvoření partnerského vztahu.

Virtuální sítě můžete připojit k sobě navzájem s partnerský vztah virtuální sítě. Jakmile se kterými mají partnerský virtuální sítě, prostředky v obě virtuální sítě jsou komunikovat s mezi sebou, se stejnou latenci a šířky pásma, jako kdyby byly prostředky ve stejné virtuální síti. Tento článek se týká vytvoření vztahu a partnerského vztahu dvě virtuální sítě. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte dvě virtuální sítě
> * Vytvoření partnerského vztahu mezi virtuálními sítěmi
> * Testování partnerského vztahu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.

## <a name="create-virtual-networks"></a>Vytvoření virtuálních sítí

1. Vyberte **+ vytvořit prostředek** na horní, levého horního rohu portálu Azure.
2. Vyberte **sítě**a potom vyberte **virtuální síť**.
3. Jak je znázorněno na následujícím obrázku, zadejte *myVirtualNetwork1* pro **název**, *10.0.0.0/16* pro **adresní prostor**,  **myResourceGroup** pro **skupiny prostředků**, *Subnet1* pro podsíť **název**, 10.0.0.0/24 pro podsíť **rozsah adres** , vyberte **umístění** a **předplatné**, přijměte zbývající výchozí hodnoty a pak vyberte **vytvořit**:

    ![Vytvoření virtuální sítě](./media/tutorial-connect-virtual-networks-portal/create-virtual-network.png)

4. Znovu, dokončete kroky 1 až 3 s následujícími změnami:
    - **Name**: *myVirtualNetwork2*
    - **Skupina prostředků**: vyberte **použít existující** a pak vyberte **myResourceGroup**.
    - **Adresní prostor**: *10.1.0.0/16*
    - **Rozsah adres podsítě**: *10.1.0.0/24*

    Předpona adresy pro *myVirtualNetwork2* virtuální sítě se nepřekrývá s adresním prostoru *myVirtualNetwork1* virtuální sítě. Nejde partnerský uzel virtuálních sítí s překrývající se adresní prostory.

## <a name="peer-virtual-networks"></a>Sdílené virtuální sítě

1. Do vyhledávacího pole v horní části portálu Azure, začněte psát *MyVirtualNetwork1*. Když **myVirtualNetwork1** se zobrazí ve výsledcích hledání, vyberte ho.
2. Vyberte **partnerských vztahů**v části **nastavení**a potom vyberte **+ přidat**, jak je znázorněno na následujícím obrázku:

    ![Vytvoření partnerského vztahu](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Zadejte nebo vyberte informace zobrazené na následujícím obrázku a potom vyberte **OK**. Vyberte *myVirtualNetwork2* virtuální sítě, vyberte **virtuální síť**, pak vyberte *myVirtualNetwork2*.

    ![Nastavení partnerského vztahu](./media/tutorial-connect-virtual-networks-portal/peering-settings.png)

    **Stav partnerského vztahu** je *získaných*, jak je znázorněno na následujícím obrázku:

    ![Stav partnerského vztahu](./media/tutorial-connect-virtual-networks-portal/peering-status.png)

    Pokud nevidíte stav, aktualizujte webový prohlížeč.

4. Vyhledejte *myVirtualNetwork2* virtuální sítě. Když se vrátí ve výsledcích hledání, vyberte ho.
5. Proveďte kroky 1 – 3 znovu, s následujícími změnami a potom vyberte **OK**:
    - **Name**: *myVirtualNetwork2-myVirtualNetwork1*
    - **Virtuální síť**: *myVirtualNetwork1*

    **Stav partnerského vztahu** je *připojeno*. Azure také změnit stav partnerského vztahu *myVirtualNetwork2 myVirtualNetwork1* partnerského vztahu z *získaných* k *připojeno.* Partnerský vztah virtuální sítě není plně navázat, dokud je stav partnerského vztahu pro obě virtuální sítě *připojeno.* 

Partnerské vztahy jsou mezi dvěma virtuálními sítěmi, ale nejsou přechodné. Ano, například pokud jste chtěli také peer *myVirtualNetwork2* k *myVirtualNetwork3*, musíte vytvořit další vztahy mezi virtuálními sítěmi *myVirtualNetwork2* a *myVirtualNetwork3*. I když *myVirtualNetwork1* je peered s *myVirtualNetwork2*, prostředků v rámci *myVirtualNetwork1* může jenom přístup k prostředkům v  *myVirtualNetwork3* Pokud *myVirtualNetwork1* byl také peered s *myVirtualNetwork3*. 

Před partnerský vztah produkční virtuální sítě, je doporučeno, důkladně Seznamte se s [partnerského vztahu přehled](virtual-network-peering-overview.md), [Správa partnerský vztah](virtual-network-manage-peering.md), a [limity virtuální sítě ](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). I když tento článek ukazuje, partnerský vztah mezi dvěma virtuálními sítěmi ve stejném předplatném a umístění, můžete také partnerský uzel virtuálních sítí v [různých oblastech](#register) a [různých předplatných Azure](create-peering-different-subscriptions.md#portal). Můžete také vytvořit [hvězdicové sítě návrhů](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) s partnerský vztah.

## <a name="test-peering"></a>Testování partnerského vztahu

Chcete-li otestovat síťové komunikace mezi virtuálními počítači v různých virtuálních sítích prostřednictvím partnerský vztah, nasazení virtuálního počítače pro každou podsíť a pak komunikaci mezi virtuálními počítači. 

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvoření virtuálního počítače v každé virtuální sítě, abyste mohli ověřit komunikace mezi nimi později.

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

1. Vyberte **+ vytvořit prostředek** na horní, levého horního rohu portálu Azure.
2. Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**. Můžete vybrat jiný operační systém, ale zbývající kroky předpokládají, že jste vybrali **Windows Server 2016 Datacenter**. 
3. Vyberte nebo zadejte následující informace pro **Základy**, pak vyberte **OK**:
    - **Název**: *myVm1*
    - **Skupina prostředků**: vyberte **použít existující** a pak vyberte *myResourceGroup*.
    - **Umístění**: vyberte *východní USA*.

    **Uživatelské jméno** a **heslo** zadáte se používají v pozdější fázi. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). **Umístění** a **předplatné** vybrané musí být stejné jako umístění a virtuální síť se předplatné. Není to nutné vybrat stejnou skupinu prostředků, který byl vytvořen virtuální sítě v, ale pro tento článek je vybrán stejnou skupinu prostředků.
4. Vyberte velikost virtuálního počítače v části **zvolte velikost**.
5. Vyberte nebo zadejte následující informace pro **nastavení**, pak vyberte **OK**:
    - **Virtuální síť**: Ujistěte se, že **myVirtualNetwork1** je vybrána. Pokud ne, vyberte **virtuální síť** a pak vyberte **myVirtualNetwork1** pod **zvolte virtuální sítě**.
    - **Podsíť**: Ujistěte se, že **Subnet1** je vybrána. Pokud ne, vyberte **podsíť** a pak vyberte **Subnet1** pod **zvolte podsíť**, jak je znázorněno na následujícím obrázku:
    
        ![Nastavení virtuálního počítače](./media/tutorial-connect-virtual-networks-portal/virtual-machine-settings.png)
 
6. V části **vytvořit** v **Souhrn**, vyberte **vytvořit** ke spuštění nasazení virtuálního počítače.
7. Proveďte kroky 1 – 6 znovu, ale zadat *Můjvp2* pro **název** virtuálního počítače a vyberte *myVirtualNetwork2* pro **virtuální síť**.

Azure přiřazené *10.0.0.4* jako privátní IP adresu *myVm1* virtuální počítač a 10.1.0.4 k *Můjvp2* virtuálního počítače, protože byly první dostupnou IP adresu adresy ve *Subnet1* z *myVirtualNetwork1* a *myVirtualNetwork2* virtuální sítě, v uvedeném pořadí.

Virtuální počítače trvat několik minut pro vytvoření. Příklady zbývajících kroků nepokračujte, dokud jsou oba virtuální počítače vytvořené.

### <a name="test-virtual-machine-communication"></a>Testovací virtuální počítač komunikace

1. V *vyhledávání* pole v horní části portálu, začněte psát *myVm1*. Když **myVm1** se zobrazí ve výsledcích hledání, vyberte ho.
2. Vytvoření připojení ke vzdálené ploše *myVm1* virtuálního počítače tak, že vyberete **připojit**, jak je znázorněno na následujícím obrázku:

    ![Připojení k virtuálnímu počítači](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Chcete-li připojit k virtuálnímu počítači, otevřete stažený soubor RDP. Po zobrazení výzvy vyberte **Connect**.
4. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače (budete muset vybrat možnost **další možnosti**, pak **použít jiný účet**, zadat přihlašovací údaje, které jste zadali při zpracování je Vytvoření virtuálního počítače), pak vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Vyberte **Ano** pokračovat v připojení.
6. V pozdější fázi, ping slouží ke komunikaci s *Můjvp2* virtuální počítač z *myVmWeb* virtuálního počítače. Příkaz ping používá protokol ICMP, který byl odepřen přes bránu Windows Firewall ve výchozím nastavení. Povolte protokol ICMP přes bránu Windows firewall tak, že zadáte následující příkaz z příkazového řádku:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    I když příkazu ping je v tomto článku použít pro testování, což ICMP přes bránu Windows Firewall pro nasazení v produkčním prostředí se nedoporučuje.

7. Pro připojení k *Můjvp2* virtuální počítač, zadejte následující příkaz z příkazového řádku *myVm1* virtuálního počítače:

    ```
    mstsc /v:10.1.0.4
    ```
    
8. Vzhledem k tomu, že jste povolili příkazu ping na *myVm1*, můžete ho teď ping podle IP adresy:

    ```
    ping 10.0.0.4
    ```
    
    Obdržíte čtyři odpovědi. Pokud jste příkaz ping podle názvu virtuálního počítače (*myVm1*), místo jeho IP adresu, ping nezdaří, protože *myVm1* název Neznámý hostitel. Rozlišení názvů výchozí Azure funguje mezi virtuálními počítači ve stejné virtuální síti, ale není mezi virtuálními počítači v různých virtuálních sítích. Překládat názvy virtuálních sítí, je nutné [nasazení serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md) nebo použijte [privátní domén Azure DNS](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

9. Vaše relace protokolu RDP na obě odpojit *myVm1* a *Můjvp2*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, odstraňte skupinu prostředků a všechny prostředky, které obsahuje: 

1. Zadejte *myResourceGroup* v **vyhledávání** pole v horní části portálu. Až se zobrazí **myResourceGroup** ve výsledcích hledání vyberte ho.
2. Vyberte **Odstranit skupinu prostředků**.
3. Zadejte *myResourceGroup* pro **název skupiny prostředků typu:** a vyberte **odstranit**.

**<a name="register"></a>Registrace pro partnerského vztahu preview globální virtuální sítě**

Vytváření partnerských vztahů virtuálních sítí ve stejné oblasti je všeobecně dostupné. Partnerský vztah virtuální sítě v různých oblastech je aktuálně ve verzi preview. V tématu [aktualizace virtuální sítě](https://azure.microsoft.com/updates/?product=virtual-network) pro dostupné oblasti. Rovnocenných počítačů virtuálních sítí v oblastech, nejprve je nutné zaregistrovat verzi Preview. Nelze zaregistrovat pomocí portálu, ale můžete zaregistrovat pomocí [prostředí PowerShell](tutorial-connect-virtual-networks-powershell.md#register) nebo [rozhraní příkazového řádku Azure](tutorial-connect-virtual-networks-cli.md#register). Pokud se pokusíte peer virtuální sítě v různých oblastech před registrací pro funkce, partnerský vztah selže.

## <a name="next-steps"></a>Další postup

V tomto článku jste se naučili připojení dvě sítě pomocí virtuální sítě partnerský vztah.

Dál svého počítače připojit k virtuální síti prostřednictvím sítě VPN a pracovat s prostředky ve virtuální síti, nebo peered virtuální sítě.

> [!div class="nextstepaction"]
> [Připojení počítače k virtuální síti.](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
