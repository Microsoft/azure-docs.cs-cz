---
title: "Směrování síťového provozu - portálu Azure | Microsoft Docs"
description: "Zjistěte, jak ke směrování síťového provozu s směrovací tabulku pomocí portálu Azure."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 4cc814e1fd3ee8979662695f9dec3dcce335dc2a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Směrovat síťový provoz s směrovací tabulku pomocí portálu Azure

Azure automaticky trasy provoz mezi všech podsítí v rámci virtuální sítě, ve výchozím nastavení. Můžete vytvořit vlastní trasy k přepsání Azure výchozí směrování. Možnost vytvářet vlastní trasy je užitečné, pokud například chcete směrovat přenos mezi podsítěmi přes bránu firewall. V tomto článku zjistíte, jak:

> [!div class="checklist"]
> * Vytvořit směrovací tabulku
> * Vytvořit trasu
> * Přidružení tabulku směrování pro podsíť virtuální sítě
> * Testování směrování
> * Řešení potíží s směrování

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.

## <a name="create-a-route-table"></a>Vytvořit směrovací tabulku

Azure směrování provozu mezi všech podsítí ve virtuální síti, ve výchozím nastavení. Další informace o výchozích tras Azure najdete v tématu [systémové trasy](virtual-networks-udr-overview.md). Pokud chcete přepsat výchozí Azure a směrování, vytvořit směrovací tabulku, která obsahuje trasy a přidružit tabulku směrování pro podsíť virtuální sítě.

1. Vyberte **+ vytvořit prostředek** na horní, levého horního rohu portálu Azure.
2. Vyberte **sítě**a potom vyberte **směrovací tabulku**.
3. Vyberte vaše **předplatné** a vyberte nebo zadejte následující informace a potom vyberte **vytvořit**:
 
    ![Vytvořit směrovací tabulku](./media/tutorial-create-route-table-portal/create-route-table.png) 

## <a name="create-a-route"></a>Vytvořit trasu

Směrovací tabulka obsahuje nula nebo víc tras. 

1. V *hledání prostředků, služeb a dokumentace* pole v horní části portálu, začněte psát *myRouteTablePublic*. Když **myRouteTablePublic** se zobrazí ve výsledcích hledání, vyberte ho.
2. V části **nastavení**, vyberte **trasy** a pak vyberte **+ přidat**, jak je znázorněno na následujícím obrázku:

    ![Přidat trasy](./media/tutorial-create-route-table-portal/add-route.png) 
 
4. V části **přidat trasy**, vyberte nebo zadejte následující informace a potom vyberte **OK**:
    - **Název trasy**: *ToPrivateSubnet*
    - **Předpona adresy**: 10.0.1.0/24
    - **Typ dalšího směrování**: vyberte **virtuální zařízení**.
    - **Adresa dalšího směrování**: 10.0.2.4

    Trasy, která bude směrovat všechny přenosy určené do předpona adresy 10.0.1.0/24 prostřednictvím sítě virtuálního zařízení s IP adresou 10.0.2.4. Virtuální zařízení sítě a podsítě s předponou zadaná adresa se vytvoří v dalších krocích. Trasy přepíše Azure výchozí směrování, který směruje provoz mezi podsítěmi přímo. Každý postup určuje typ dalšího směrování. Typ dalšího přechodu dá pokyn Azure směrování provozu. V tomto příkladu je typ dalšího přechodu *VirtualAppliance*. Další informace o všech dostupných další typy směrování v Azure a jejich použití naleznete v tématu [dalšího směrování typy](virtual-networks-udr-overview.md#custom-routes).

## <a name="associate-a-route-table-to-a-subnet"></a>Přidružení tabulku směrování pro podsíť

Než budete moct přidružit tabulku směrování pro podsíť, je nutné vytvořit virtuální síť a podsíť:

1. Vyberte **+ vytvořit prostředek** na horní, levého horního rohu portálu Azure.
2. Vyberte **sítě**a potom vyberte **virtuální síť**.
3. V části **vytvořit virtuální síť**vyberte, nebo zadejte následující informace a pak vyberte **vytvořit**:
    
    - **Name**: *myVirtualNetwork*
    - **Adresní prostor**: *10.0.0.0/16*
    - **Předplatné**: vyberte své předplatné.
    - **Skupina prostředků**: vyberte **použít existující** a vyberte **myResourceGroup**.
    - **Umístění**: vyberte *východní USA*
    - **Název podsítě**: *veřejné*
    - **Rozsah adres:** *10.0.0.0/24*

4. V **hledání prostředků, služeb a dokumentace** pole v horní části portálu, začněte psát *myVirtualNetwork*. Když **myVirtualNetwork** se zobrazí ve výsledcích hledání, vyberte ho.
5. Přidejte dva další podsítě virtuální sítě. V části **nastavení**, vyberte **podsítě** a pak vyberte **+ podsítě**, jak je znázorněno na následujícím obrázku:

    ![Přidání podsítě](./media/tutorial-create-route-table-portal/add-subnet.png) 

6. Vyberte nebo zadejte následující informace a potom vyberte **OK**:
    - **Název**: privátní
    - **Adresní prostor**: *10.0.1.0/24*

7. Proveďte kroky 5 a 6 znovu, poskytuje následující informace:
    - **Název**: hraniční sítě
    - **Adresní prostor**: *10.0.2.0/24*

Můžete přidružit směrovací tabulku do nula nebo více podsítí. Podsíť může mít žádnou nebo jednu směrovací tabulku přidružené k němu. Odchozí provoz z podsítě se směruje na základě Azure výchozí trasy a jakékoli vlastní trasy, která jste přidali do tabulky směrování, kterou přidružíte k podsíti. Přidružení *myRouteTablePublic* směrovací tabulku, aby *veřejné* podsítě:

1. **MyVirtualNetwork - podsítě** políčko se zobrazí po dokončení předchozího kroku. V části **nastavení**, vyberte **podsítě** a pak vyberte **veřejné**.
2. Jak je znázorněno na následujícím obrázku, vyberte **směrovací tabulku**, pak vyberte **MyRouteTablePublic**.

    ![Přidružení směrovací tabulku](./media/tutorial-create-route-table-portal/associate-route-table.png) 

3. Vyberte **Uložit**.

## <a name="test-routing"></a>Testování směrování

K testování směrování, vytvoříte virtuální počítač, který slouží jako virtuální zařízení sítě, prostřednictvím směruje trasy, kterou jste vytvořili v předchozím kroku. Po vytvoření virtuálního zařízení sítě, budete nasazovat do virtuálního počítače *veřejné* a *privátní* podsítě. Budete pak směrovat provoz z *veřejné* podsítě, který *privátní* podsítě virtuální síťové zařízení.

### <a name="create-a-network-virtual-appliance"></a>Vytvořit virtuální síťové zařízení

V předchozím kroku jste vytvořili trasu, která zadaný virtuální zařízení sítě jako typ dalšího směrování. Virtuální počítač spuštěný síťové aplikace se často označuje jako virtuální zařízení sítě. V produkčním prostředí je virtuální zařízení sítě, které můžete nasadit často předem nakonfigurovaný virtuální počítač. Několik síťových virtuálních zařízení jsou k dispozici na [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1). V tomto článku se vytvoří základní virtuální počítač.

1. Vyberte **+ vytvořit prostředek** na horní, levého horního rohu portálu Azure.
2. Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**. Můžete vybrat jiný operační systém, ale zbývající kroky předpokládají, že jste vybrali **Windows Server 2016 Datacenter**. 
3. Vyberte nebo zadejte následující informace pro **Základy**, pak vyberte **OK**:
    - **Název**: *myVmNva*
    - **Skupina prostředků**: vyberte **použít existující** a pak vyberte *myResourceGroup*.
    - **Umístění**: vyberte *východní USA*.

    **Uživatelské jméno** a **heslo** zadáte se používají v pozdější fázi. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). **Umístění** a **předplatné** vybrané musí být stejné jako umístění a virtuální síť se předplatné. Není to nutné vybrat stejnou skupinu prostředků, který byl vytvořen virtuální sítě v, ale pro tento kurz je vybrán stejnou skupinu prostředků.
4. Vyberte velikost virtuálního počítače v části **zvolte velikost**.
5. Vyberte nebo zadejte následující informace pro **nastavení**, pak vyberte **OK**:
    - **Virtuální síť**: Ujistěte se, že **myVirtualNetwork** je vybrána. Pokud ne, vyberte **virtuální síť**, pak vyberte **myVirtualNetwork** pod **zvolte virtuální sítě**.
    - **Podsíť**: vyberte **podsíť** a pak vyberte **DMZ** pod **zvolte podsíť**.
    - **Veřejná IP adresa**: vyberte **veřejnou IP adresu** a vyberte **žádné** pod **zvolte veřejnou IP adresu**. Žádná veřejná IP adresa je přiřazen k tomuto virtuálnímu počítači, vzhledem k tomu, že ji nebude připojen k z Internetu.
6. V části **vytvořit** v **Souhrn**, vyberte **vytvořit** ke spuštění nasazení virtuálního počítače.

Virtuální počítač trvá několik minut pro vytvoření. Nebudete pokračovat k dalšímu kroku až Azure dokončí vytváření virtuálního počítače a otevře pole s informacemi o virtuálním počítači.

Při vytváření virtuálního počítače Azure také vytvořit [síťové rozhraní](virtual-network-network-interface.md) v *DMZ* podsítě a připojit síťové rozhraní k virtuálnímu počítači. Předávání IP musí být povolen pro každé rozhraní síť Azure, který předává provoz určený pro každou IP adresu, která není přiřazen k síťovému rozhraní.

1. Do pole Otevřít pro virtuální počítač po jeho vytvoření, v části **nastavení**, vyberte **sítě**a potom vyberte **myvmnva158** (rozhraní sítě Azure vytvořené pro virtuální počítač má jiné číslo po **myvmnva**), jak je znázorněno na následujícím obrázku:

    ![Sítě virtuálních počítačů](./media/tutorial-create-route-table-portal/virtual-machine-networking.png) 

    Pokud jste vytvořili virtuální síťové zařízení v *DMZ* podsíť, Azure automaticky vytvořen rozhraní sítě, síťové rozhraní připojené k virtuálnímu počítači a přiřazenou privátní IP adresu  *10.0.2.4* na síťové rozhraní. 

2. V části **nastavení**, vyberte **konfigurace protokolu IP**, vyberte **povoleno** pro **předávání IP**a potom vyberte **uložit** , jak je znázorněno na následujícím obrázku:

    ![Povolení předávání IP](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png) 

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte dva virtuální počítače ve virtuální síti, abyste mohli ověřit, že provoz z *veřejné* podsítě se směruje na *privátní* podsítě virtuální zařízení sítě později.

Dokončit kroky 1 – 6 [vytvořit virtuální zařízení sítě](#create-a-network-virtual-appliance). Použijte stejná nastavení v kroku 3 a 5, s výjimkou následující změny:

|Virtuální počítač   |Název      |Podsíť      | Veřejná IP adresa     |
|---------         |--------- | -----------|---------              |
|Virtuální počítač 1 | myVmWeb  | Veřejné     | Přijměte výchozí portálu |
|virtuální počítač 2 | myVmMgmt | Privátní    | Přijměte výchozí portálu |

Můžete vytvořit *myVmMgmt* virtuálního počítače, zatímco Azure vytváří *myVmWeb* virtuálního počítače. Následující kroky nepokračujte, dokud Azure dokončí vytváření oba virtuální počítače.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>Směrovat provoz prostřednictvím sítě virtuálního zařízení

1. V *vyhledávání* pole v horní části portálu, začněte psát *myVmMgmt*. Když **myVmMgmt** se zobrazí ve výsledcích hledání, vyberte ho.
2. Vytvoření připojení ke vzdálené ploše *myVmMgmt* virtuálního počítače tak, že vyberete **připojit**, jak je znázorněno na následujícím obrázku:

    ![Připojení k virtuálnímu počítači](./media/tutorial-create-route-table-portal/connect-to-virtual-machine.png)  

3. Chcete-li připojit k virtuálnímu počítači, otevřete stažený soubor RDP. Po zobrazení výzvy vyberte **Connect**.
4. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače (budete muset vybrat možnost **další možnosti**, pak **použít jiný účet**, zadat přihlašovací údaje, které jste zadali při zpracování je Vytvoření virtuálního počítače), pak vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Vyberte **Ano** pokračovat v připojení.
6. V pozdější fázi příkaz tracert.exe slouží k otestování, směrování. Tracert používá protokol ICMP, který byl odepřen přes bránu Windows Firewall. Povolte protokol ICMP přes bránu Windows firewall tak, že zadáte následující příkaz z příkazového řádku:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    I když tracert se používá k testování směrování v tomto článku, což ICMP přes bránu Windows Firewall pro nasazení v produkčním prostředí se nedoporučuje.
7. Jste povolili předávání IP v rámci Azure pro síťové rozhraní virtuálního počítače v [předávání IP povolit](#enable-ip-forwarding). V rámci virtuálního počítače operační systém nebo aplikace běžící v rámci virtuálního počítače, musí taky umět předávat síťový provoz. Když nasadíte virtuální síťové zařízení v provozním prostředí, zařízení obvykle filtry, protokoly nebo provádí některé další funkce před předáním provoz. V tomto článku však operační systém jednoduše předává veškerý provoz, které obdrží. Povolení předávání IP v rámci operačního systému *myVmNva* podle následujících kroků z *myVmMgmt* virtuálního počítače:

    Vzdálenou plochu *myVmNva* virtuálního počítače pomocí následujícího příkazu z příkazového řádku:

    ``` 
    mstsc /v:myvmnva
    ```
    
    Pokud chcete povolit předávání v operačním systému protokolu IP, zadejte následující příkaz v prostředí PowerShell:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```
    
    Restartujte virtuální počítač, který bude také odpojit relaci vzdálené plochy.
8. Při stále připojen k *myVmMgmt* virtuálního počítače, po *myVmNva* restartování virtuálního počítače, vytvořte relaci vzdálené plochy k *myVmWeb* virtuálního počítače Pomocí následujícího příkazu:

    ``` 
    mstsc /v:myVmWeb
    ```
    
    Povolte protokol ICMP přes bránu Windows firewall tak, že zadáte následující příkaz z příkazového řádku:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

9. K testování směrování síťový provoz *myVmMgmt* virtuální počítač z *myVmWeb* virtuální počítač, zadejte následující příkaz z příkazového řádku:

    ```
    tracert myvmmgmt
    ```

    Odpověď je stejný jako v následujícím příkladu:
    
    ```
    Tracing route to myvmmgmt.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:
        
    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4
        
    Trace complete.
    ```
      
    Uvidíte, že prvním skoku je 10.0.2.4, což je virtuální zařízení sítě privátní IP adresu. Druhé směrování je 10.0.1.4, privátní IP adresu *myVmMgmt* virtuálního počítače. Trasy přidat do *myRouteTablePublic* směrovací tabulky a přidružené k *veřejné* podsíť způsobila Azure pro směrování provozu prostřednictvím hodnocení chyb zabezpečení, nikoli přímo do *privátní* podsítě.
10.  Zavřete relaci vzdálené plochy k *myVmWeb* virtuálního počítače, což zanechá jste stále připojeni k *myVmMgmt* virtuálního počítače.
11. K testování směrování síťový provoz *myVmWeb* virtuální počítač z *myVmMgmt* virtuální počítač, zadejte následující příkaz z příkazového řádku:

    ```
    tracert myvmweb
    ```

    Odpověď je stejný jako v následujícím příkladu:

    ```
    Tracing route to myvmweb.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:
    
    1     1 ms     1 ms     1 ms  10.0.0.4
    
    Trace complete.
    ```

    Uvidíte, že provoz se směruje přímo z *myVmMgmt* virtuální počítač *myVmWeb* virtuálního počítače. Ve výchozím nastavení Azure trasy provoz přímo mezi podsítěmi.
12. Zavřete relaci vzdálené plochy k *myVmMgmt* virtuálního počítače.

## <a name="troubleshoot-routing"></a>Řešení potíží s směrování

Jste se naučili v předchozích krocích, Azure, použije výchozí trasy, lze, Volitelně můžete přepsat pomocí vlastní trasy. V některých případech nemusí být provoz směruje podle očekávání jako. Můžete použít [dalšího směrování](../network-watcher/network-watcher-check-next-hop-portal.md) schopností sledovací proces sítě Azure k určení, jak je směrování provozu mezi dvěma virtuálními počítači Azure. 

1. V *vyhledávání* pole v horní části portálu, začněte psát *sledovací proces sítě*. Když **sledovací proces sítě** se zobrazí ve výsledcích hledání, vyberte ho.
2. V části **nástroj pro diagnostiku sítě**, vyberte **dalšího směrování**.
3. K testování směrování provozu z *myVmWeb* (10.0.0.4) virtuálního počítače *myVmMgmt* (10.0.1.4) virtuální počítač, vyberte předplatné, zadejte informace zobrazené na následujícím obrázku (vaše **Síťové rozhraní** název se mírně liší) a potom vyberte **dalšího směrování**:

    ![Další směrování](./media/tutorial-create-route-table-portal/next-hop.png)  

    **Výsledek** výstup informuje, že dalšího směrování IP adresu pro provoz z *myVmWeb* k *myVmMgmt* je 10.0.2.4 ( *myVmNva* virtuální počítač), typ dalšího směrování je *VirtualAppliance*, a že je směrovací tabulce, která způsobí, že směrování *myRouteTablePublic*.

Efektivní trasy pro každé síťové rozhraní jsou kombinaci Azure výchozí trasy a všech tras, které definujete. Pokud chcete zobrazit všechny trasy efektivní pro rozhraní sítě ve virtuálním počítači, proveďte následující kroky:

1. V *vyhledávání* pole v horní části portálu, začněte psát *myVmWeb*. Když **myVmWeb** se zobrazí ve výsledcích hledání, vyberte ho.
2. V části **nastavení**, vyberte **sítě**a potom vyberte **myvmweb369** (síťové rozhraní Azure vytvořené pro virtuální počítač má jiné číslo po **myvmweb**).
3. V části **podporu + Poradce při potížích s**, vyberte **efektivní trasy**, jak je znázorněno na následujícím obrázku:

    ![Efektivní trasy](./media/tutorial-create-route-table-portal/effective-routes.png) 

    Zobrazí Azure výchozí trasy a trasy, které jste přidali v *myRouteTablePublic* směrovací tabulku. Další informace o tom, jak Azure vybere trasy ze seznamu tras, najdete v části [jak Azure vybere trasu](virtual-networks-udr-overview.md#how-azure-selects-a-route).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, odstraňte skupinu prostředků a všechny prostředky, které obsahuje: 

1. Zadejte *myResourceGroup* v **vyhledávání** pole v horní části portálu. Až se zobrazí **myResourceGroup** ve výsledcích hledání vyberte ho.
2. Vyberte **Odstranit skupinu prostředků**.
3. Zadejte *myResourceGroup* pro **název skupiny prostředků typu:** a vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

V tomto článku vytvořit směrovací tabulku a přidružené k podsíti. Vytvořili jste virtuální zařízení sítě směrovat přenosy z veřejných podsítě do privátní podsítě. Když nasadíte mnoho prostředků Azure v rámci virtuální sítě, zdrojů u některých služeb Azure PaaS nelze nasadit do virtuální sítě. Můžete dál omezit přístup k prostředkům některé služby Azure PaaS provoz jenom z podsítě virtuální sítě, když. Přechodu na v dalším kurzu se dozvíte, jak omezit přístup k síti na Azure PaaS prostředky.

> [!div class="nextstepaction"]
> [Omezit přístup k síti na PaaS prostředky](virtual-network-service-endpoints-configure.md#azure-portal)
