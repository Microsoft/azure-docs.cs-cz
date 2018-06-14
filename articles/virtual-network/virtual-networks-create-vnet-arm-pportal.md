---
title: Vytvoření virtuální sítě Azure s několika podsítěmi - portál | Microsoft Docs
description: Naučte se vytvořit virtuální síť s více podsítěmi pomocí portálu Azure.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 898bdef779282d7312c76696f744b97ec2dfcded
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2018
ms.locfileid: "29880553"
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-the-azure-portal"></a>Vytvoření virtuální sítě s několika podsítěmi pomocí portálu Azure

Virtuální síť umožňuje několik typů prostředků Azure pro komunikaci s Internetem a soukromě mezi sebou. Vytvoření více podsítí ve virtuální síti umožňuje segmentovat vaší síti, takže můžete filtrovat nebo řízení toku provozu mezi podsítěmi. V tomto článku zjistíte, jak:

> [!div class="checklist"]
> * Vytvoření virtuální sítě
> * Vytvoření podsítě
> * Testování síťovou komunikaci mezi virtuální počítače

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. Vyberte **+ vytvořit prostředek** na horní, levého horního rohu portálu Azure.
2. Vyberte **sítě**a potom vyberte **virtuální síť**.
3. Jak je znázorněno na následujícím obrázku, zadejte *myVirtualNetwork* pro **název**, *10.0.0.0/16* pro **adresní prostor**,  **myResourceGroup** pro **skupiny prostředků**, *veřejné* pro podsíť **název**, 10.0.0.0/24 pro podsíť **rozsahadres**, vyberte **umístění** a **předplatné**, přijměte zbývající výchozí hodnoty a pak vyberte **vytvořit**:

    ![Vytvoření virtuální sítě](./media/virtual-networks-create-vnet-arm-pportal/create-virtual-network.png)

    **Adresní prostor** a **rozsahu adres** jsou zadat v notaci CIDR. Zadaný **adresní prostor** zahrnuje 10.0.0.0-10.0.255.254 adresy IP. **Rozsah adres** zadat pro podsítě, musí být v rámci **adresní prostor** definované pro virtuální síť. Azure DHCP přiřadí IP adres z rozsahu adres podsítě v podsíti nejsou nasazené prostředky. Azure přiřadí prostředky nasazené v rámci pouze adresy 10.0.0.4-10.0.0.254 **veřejné** podsíť, protože Azure si vyhrazuje první čtyři adres (10.0.0.0-10.0.0.3 podsítě, v tomto příkladu) a poslední adres ( 10.0.0.255 podsítě, v tomto příkladu) v každé podsíti.

## <a name="create-a-subnet"></a>Vytvoření podsítě

1. V **hledání prostředků, služeb a dokumentace** pole v horní části portálu, začněte psát *myVirtualNetwork*. Když **myVirtualNetwork** se zobrazí ve výsledcích hledání, vyberte ho.
2. Vyberte **podsítě** a pak vyberte **+ podsítě**, jak je znázorněno na následujícím obrázku:

     ![Přidat podsíť](./media/virtual-networks-create-vnet-arm-pportal/add-subnet.png)
     
3. V **přidat podsíť** pole, která se zobrazí, zadejte *privátní* pro **název**, zadejte *10.0.1.0/24* pro **rozsahadres**a potom vyberte **OK**.  Rozsah adres podsítě se nesmí překrývat s rozsahy adres z jiné podsítě virtuální sítě. 

Před nasazením virtuálních sítí Azure a podsítě pro použití v provozním prostředí, se doporučuje, že jste důkladně Seznamte se s adresním prostorem [aspekty](manage-virtual-network.md#create-a-virtual-network) a [limity virtuální síťové](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Jakmile se prostředky nasadí do podsítí, může vyžadovat některé virtuální síť a podsíť změny, jako je například změna rozsahy adres, opakované nasazení existující prostředky Azure, které jsou nasazeny v rámci podsítě.

## <a name="test-network-communication"></a>Test síťové komunikace

Virtuální síť umožňuje několik typů prostředků Azure pro komunikaci s Internetem a soukromě mezi sebou. Virtuální počítač je jeden typ prostředku, který můžete nasadit do virtuální sítě. Vytvořte dva virtuální počítače ve virtuální síti, abyste je mohli otestovat síťovou komunikaci mezi nimi a Internetu v pozdější fázi.

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

1. Vyberte **+ vytvořit prostředek** na horní, levého horního rohu portálu Azure.
2. Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**. Můžete vybrat jiný operační systém, ale zbývající kroky předpokládají, že jste vybrali **Windows Server 2016 Datacenter**. 
3. Vyberte nebo zadejte následující informace pro **Základy**, pak vyberte **OK**:
    - **Name**: *myVmWeb*
    - **Skupina prostředků**: vyberte **použít existující** a pak vyberte *myResourceGroup*.
    - **Umístění**: vyberte *východní USA*.

    **Uživatelské jméno** a **heslo** zadáte se používají v pozdější fázi. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). **Umístění** a **předplatné** vybrané musí být stejné jako umístění a virtuální síť se předplatné. Není to nutné vybrat stejnou skupinu prostředků, který byl vytvořen virtuální sítě v, ale pro tento kurz je vybrán stejnou skupinu prostředků.
4. Vyberte velikost virtuálního počítače v části **zvolte velikost**.
5. Vyberte nebo zadejte následující informace pro **nastavení**, pak vyberte **OK**:
    - **Virtuální síť**: Ujistěte se, že **myVirtualNetwork** je vybrána. Pokud ne, vyberte **virtuální síť** a pak vyberte **myVirtualNetwork** pod **zvolte virtuální sítě**.
    - **Podsíť**: Ujistěte se, že **veřejné** je vybrána. Pokud ne, vyberte **podsíť** a pak vyberte **veřejné** pod **zvolte podsíť**, jak je znázorněno na následujícím obrázku:
    
        ![Nastavení virtuálního počítače](./media/virtual-networks-create-vnet-arm-pportal/virtual-machine-settings.png)
 
6. V části **vytvořit** v **Souhrn**, vyberte **vytvořit** ke spuštění nasazení virtuálního počítače.
7. Proveďte kroky 1 – 6 znovu, ale zadat *myVmMgmt* pro **název** virtuálního počítače a vyberte **privátní** pro **podsítě**.

Virtuální počítače trvat několik minut pro vytvoření. Příklady zbývajících kroků nepokračujte, dokud jsou oba virtuální počítače vytvořené.

Virtuální počítače vytvořené v tomto článku mít jeden [síťové rozhraní](virtual-network-network-interface.md) s jednu IP adresu, která se dynamicky přiřadit síťové rozhraní. Poté, co nasadíte virtuální počítač, můžete [přidat víc veřejných a privátních IP adres, nebo změnit způsob přiřazení IP adresy statické](virtual-network-network-interface-addresses.md#add-ip-addresses). Můžete [přidat síťových rozhraní](virtual-network-network-interface-vm.md#vm-add-nic), až do limitu nepodporuje [velikost virtuálního počítače](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vyberete při vytváření virtuálního počítače. Můžete také [povolit single-root I/O virtualization (SR-IOV)](create-vm-accelerated-networking-powershell.md) virtuálních počítačů, ale pouze tehdy, když vytvoření virtuálního počítače s velikostí virtuálních počítačů, která podporuje možnost.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>Komunikace mezi virtuálními počítači a s Internetem

1. V *vyhledávání* pole v horní části portálu, začněte psát *myVmMgmt*. Když **myVmMgmt** se zobrazí ve výsledcích hledání, vyberte ho.
2. Vytvoření připojení ke vzdálené ploše *myVmMgmt* virtuálního počítače tak, že vyberete **připojit**, jak je znázorněno na následujícím obrázku:

    ![Připojení k virtuálnímu počítači](./media/virtual-networks-create-vnet-arm-pportal/connect-to-virtual-machine.png)  

3. Chcete-li připojit k virtuálnímu počítači, otevřete stažený soubor RDP. Po zobrazení výzvy vyberte **Connect**.
4. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače (budete muset vybrat možnost **další možnosti**, pak **použít jiný účet**, zadat přihlašovací údaje, které jste zadali při zpracování je Vytvoření virtuálního počítače), pak vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Vyberte **Ano** pokračovat v připojení.
6. V pozdější fázi, ping slouží ke komunikaci s *myVmMgmt* virtuální počítač z *myVmWeb* virtuálního počítače. Příkaz ping používá protokol ICMP, který byl odepřen přes bránu Windows Firewall ve výchozím nastavení. Povolte protokol ICMP přes bránu Windows firewall tak, že zadáte následující příkaz z příkazového řádku:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    I když v tomto článku je použít příkaz ping, což ICMP přes bránu Windows Firewall pro nasazení v produkčním prostředí se nedoporučuje.
7. Z bezpečnostních důvodů je běžné omezit počet virtuálních počítačů, které můžete vzdáleně připojit k ve virtuální síti. V tomto kurzu *myVmMgmt* virtuální počítač se používá ke správě *myVmWeb* virtuálního počítače ve virtuální síti. Pro vzdálenou plochu *myVmWeb* virtuální počítač z *myVmMgmt* virtuální počítač, zadejte následující příkaz z příkazového řádku:

    ``` 
    mstsc /v:myVmWeb
    ```
8. Pro komunikaci se *myVmMgmt* virtuální počítač z *myVmWeb* virtuální počítač, zadejte následující příkaz z příkazového řádku:

    ```
    ping myvmmgmt
    ```

    Zobrazí se výstup podobný výstupu v následujícím příkladu:
    
    ```
    Pinging myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net [10.0.1.4] with 32 bytes of data:
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    
    Ping statistics for 10.0.1.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 0ms, Average = 0ms
    ```
      
    Uvidíte, že adresa *myVmMgmt* 10.0.1.4 je virtuální počítač. 10.0.1.4 se první dostupná IP adresa v rozsahu adres *privátní* podsítě, které jste nasadili *myVmMgmt* virtuálního počítače v předchozím kroku.  Zjistíte, že plně kvalifikovaný název domény virtuálního počítače je *myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net*. I když *dar5p44cif3ulfq00wxznl3i3f* část názvu domény se liší pro virtuální počítač, zbývající části názvu domény jsou stejné. 

    Ve výchozím nastavení používají všechny virtuální počítače Azure výchozí služba Azure DNS. Všechny virtuální počítače v rámci virtuální sítě můžete vyřešit názvy všechny ostatní virtuální počítače ve stejné virtuální síti pomocí služby DNS Azure, je výchozí. Místo použití služba DNS výchozí Azure, můžete použít serveru DNS nebo funkcí privátní domény služby Azure DNS. Podrobnosti najdete v tématu [překladu IP adresy serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) nebo [pomocí Azure DNS pro domény privátní](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

9. Instalace Internetové informační služby (IIS) pro systém Windows Server na *myVmWeb* virtuální počítač, zadejte následující příkaz z relace prostředí PowerShell:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

10. Po dokončení instalace služby IIS odpojit *myVmWeb* relace vzdálené plochy, což zanechá v *myVmMgmt* relace vzdálené plochy. Otevřete webový prohlížeč a přejděte do http://myvmweb. Zobrazí úvodní stránce služby IIS.
11. Odpojení *myVmMgmt* relace vzdálené plochy.
12. Najít veřejnou IP adresu *myVmWeb* virtuálního počítače. Vytvoření Azure *myVmWeb* virtuální počítač, prostředek veřejné IP adresy s názvem *myVmWeb* také vytvořil a přiřazené k virtuálnímu počítači. Uvidíte, že byl přiřazen 52.170.5.92 pro **veřejnou IP adresu** k *myVmMgmt* virtuálního počítače na obrázku v kroku 2. K vyhledání přiřazené veřejné IP adresy *myVmWeb* virtuální počítač, vyhledejte *myVmWeb* na portálu vyhledávacího pole, pak ho vyberte když se objeví ve výsledcích hledání.

    Když virtuální počítač není potřeba mít přiřazené veřejnou IP adresu, Azure přiřadí veřejnou IP adresu pro každý virtuální počítač, který vytvoříte, ve výchozím nastavení. K virtuálnímu počítači komunikovat z Internetu, musí mít veřejnou IP adresu přiřazenou k virtuálnímu počítači. Všechny virtuální počítače mohou komunikovat odchozí přes Internet, zda je k virtuálnímu počítači přiřazena veřejnou IP adresu. Další informace o odchozí připojení k Internetu v Azure najdete v tématu [odchozí připojení v Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
13. Ve vašem počítači, přejděte na veřejnou IP adresu *myVmWeb* virtuálního počítače. Pokus o zobrazení úvodní stránka IIS ze svého počítače selže. Pokus selže, protože když byly nasazené virtuální počítače, Azure vytvoří skupinu zabezpečení sítě pro každý virtuální počítač, ve výchozím nastavení. 

     Skupina zabezpečení sítě obsahuje pravidla zabezpečení, která povolují nebo odepírají příchozí a odchozí síťový provoz pomocí portu a IP adresy. Výchozí skupina zabezpečení sítě, který vytvořili Azure umožňuje komunikaci přes všechny porty mezi prostředky ve stejné virtuální síti. Výchozí skupina zabezpečení sítě pro virtuální počítače s Windows, odmítne veškerý příchozí provoz z Internetu přes všechny porty, s výjimkou TCP port 3389 (RDP). V důsledku toho ve výchozím nastavení, můžete také RDP přímo na *myVmWeb* virtuálního počítače z Internetu, i když není vhodné portu 3389 otevřete na webový server. Vzhledem k tomu, že procházení webu komunikuje přes port 80, komunikaci z Internetu se nezdaří, protože není pravidlo ve skupině zabezpečení výchozí sítě umožňuje provoz přes port 80.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, odstraňte skupinu prostředků a všechny prostředky, které obsahuje: 

1. Zadejte *myResourceGroup* v **vyhledávání** pole v horní části portálu. Až se zobrazí **myResourceGroup** ve výsledcích hledání vyberte ho.
2. Vyberte **Odstranit skupinu prostředků**.
3. Zadejte *myResourceGroup* pro **název skupiny prostředků typu:** a vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak nasadíte virtuální síť s více podsítěmi. Také jste zjistili, že při vytváření virtuálního počítače s Windows Azure vytvoří rozhraní sítě, připojí k virtuálnímu počítači a vytvoří skupinu zabezpečení sítě, který umožňuje pouze provoz přes port 3389, z Internetu. Přechodu na dalším kurzu se dozvíte, jak filtrovat síťový provoz do podsítí, nikoli pro jednotlivé virtuální počítače.

> [!div class="nextstepaction"]
> [Filtr síťový provoz do podsítí](./virtual-networks-create-nsg-arm-pportal.md)
