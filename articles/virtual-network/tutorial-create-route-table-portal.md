---
title: Směrování síťového provozu - portálu Azure | Microsoft Docs
description: Zjistěte, jak ke směrování síťového provozu s směrovací tabulku pomocí portálu Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 980cf7b59ed16778bbb6cd1b657e3522407c79c9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Směrovat síťový provoz s směrovací tabulku pomocí portálu Azure

Azure automaticky trasy provoz mezi všech podsítí v rámci virtuální sítě, ve výchozím nastavení. Můžete vytvořit vlastní trasy k přepsání Azure výchozí směrování. Možnost vytvářet vlastní trasy je užitečné, pokud například chcete směrovat přenos mezi podsítěmi přes virtuální síťové zařízení (hodnocení chyb zabezpečení). V tomto článku zjistíte, jak:

> [!div class="checklist"]
> * Vytvořit směrovací tabulku
> * Vytvořit trasu
> * Vytvoření virtuální sítě s několika podsítěmi
> * Přidružení tabulku směrování pro podsíť
> * Vytvoření hodnocení chyb zabezpečení, který směruje provoz
> * Nasazení virtuálních počítačů (VM) do různých podsítí
> * Směrovat provoz z jedné podsítě do jiné prostřednictvím hodnocení chyb zabezpečení

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k portálu Azure v http://portal.azure.com.

## <a name="create-a-route-table"></a>Vytvořit směrovací tabulku

1. Vyberte **+ vytvořit prostředek** na horní, levého horního rohu portálu Azure.
2. Vyberte **sítě**a potom vyberte **směrovací tabulku**.
3. Zadejte, nebo vyberte následující informace, přijměte výchozí nastavení pro zbývající nastavení a potom vyberte **vytvořit**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myRouteTablePublic|
    |Předplatné| Vyberte své předplatné.|
    |Skupina prostředků | Vyberte **vytvořit nový** a zadejte *myResourceGroup*.|
    |Umístění|Východ USA|
 
    ![Vytvořit směrovací tabulku](./media/tutorial-create-route-table-portal/create-route-table.png) 

## <a name="create-a-route"></a>Vytvořit trasu

1. V *hledání prostředků, služeb a dokumentace* pole v horní části portálu, začněte psát *myRouteTablePublic*. Když **myRouteTablePublic** se zobrazí ve výsledcích hledání, vyberte ho.
2. V části **nastavení**, vyberte **trasy** a pak vyberte **+ přidat**, jak je znázorněno na následujícím obrázku:

    ![Přidat trasy](./media/tutorial-create-route-table-portal/add-route.png) 
 
3. V části **přidat trasy**, zadejte, nebo vyberte následující informace, přijměte výchozí nastavení pro zbývající nastavení a potom vyberte **vytvořit**:

    |Nastavení|Hodnota|
    |---|---|
    |Název trasy|ToPrivateSubnet|
    |Předpona adresy| 10.0.1.0/24|
    |Typ dalšího segmentu | Vyberte **virtuální zařízení**.|
    |Adresa dalšího segmentu| 10.0.2.4|

## <a name="associate-a-route-table-to-a-subnet"></a>Přidružení tabulku směrování pro podsíť

Než budete moct přidružit tabulku směrování pro podsíť, je nutné vytvořit virtuální síť a podsíť, a poté můžete přidružit k podsíti směrovací tabulka:

1. Vyberte **+ vytvořit prostředek** na horní, levého horního rohu portálu Azure.
2. Vyberte **sítě**a potom vyberte **virtuální síť**.
3. V části **vytvořit virtuální síť**, zadejte, nebo vyberte následující informace, přijměte výchozí nastavení pro zbývající nastavení a potom vyberte **vytvořit**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVirtualNetwork|
    |Adresní prostor| 10.0.0.0/16|
    |Předplatné | Vyberte své předplatné.|
    |Skupina prostředků|Vyberte **použít existující** a pak vyberte **myResourceGroup**.|
    |Umístění|Vyberte *východní USA*|
    |Název podsítě|Veřejné|
    |Rozsah adres|10.0.0.0/24|
    
4. V **hledání prostředků, služeb a dokumentace** pole v horní části portálu, začněte psát *myVirtualNetwork*. Když **myVirtualNetwork** se zobrazí ve výsledcích hledání, vyberte ho.
5. V části **nastavení**, vyberte **podsítě** a pak vyberte **+ podsítě**, jak je znázorněno na následujícím obrázku:

    ![Přidání podsítě](./media/tutorial-create-route-table-portal/add-subnet.png) 

6. Vyberte nebo zadejte následující informace a potom vyberte **OK**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|Privátní|
    |Adresní prostor| 10.0.1.0/24|

7. Proveďte kroky 5 a 6 znovu, poskytuje následující informace:

    |Nastavení|Hodnota|
    |---|---|
    |Název|DMZ|
    |Adresní prostor| 10.0.2.0/24|

8. **MyVirtualNetwork - podsítě** políčko se zobrazí po dokončení předchozího kroku. V části **nastavení**, vyberte **podsítě** a pak vyberte **veřejné**.
9. Jak je znázorněno na následujícím obrázku, vyberte **směrovací tabulku**, vyberte **MyRouteTablePublic**a potom vyberte **Uložit**:

    ![Přidružení směrovací tabulku](./media/tutorial-create-route-table-portal/associate-route-table.png) 

## <a name="create-an-nva"></a>Vytvoření hodnocení chyb zabezpečení

Hodnocení chyb zabezpečení sítě je virtuální počítač, který provádí síťové funkce, například směrování, fungující brána firewall může nebo optimalizace sítě WAN.

1. Vyberte **+ vytvořit prostředek** na horní, levého horního rohu portálu Azure.
2. Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**. Můžete vybrat jiný operační systém, ale zbývající kroky předpokládají, že jste vybrali **Windows Server 2016 Datacenter**. 
3. Vyberte nebo zadejte následující informace pro **Základy**, pak vyberte **OK**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVmNva|
    |Uživatelské jméno|Zadejte uživatelské jméno dle vlastního výběru.|
    |Heslo|Zadejte heslo dle vlastního výběru. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Skupina prostředků| Vyberte **použít existující** a pak vyberte *myResourceGroup*.|
    |Umístění|Vyberte **východní USA**.|
4. Vyberte velikost virtuálního počítače v části **zvolte velikost**.
5. Vyberte nebo zadejte následující informace pro **nastavení**, pak vyberte **OK**:

    |Nastavení|Hodnota|
    |---|---|
    |Virtuální síť|myVirtualNetwork – Pokud není vybrána, vyberte **virtuální síť**, pak vyberte **myVirtualNetwork** pod **zvolte virtuální sítě**.|
    |Podsíť|Vyberte **podsíť** a pak vyberte **DMZ** pod **zvolte podsíť**. |
    |Veřejná IP adresa| Vyberte **veřejnou IP adresu** a vyberte **žádné** pod **zvolte veřejnou IP adresu**. Žádné veřejná IP adresa je přiřazen k tomuto virtuálnímu počítači vzhledem k tomu, že ji nebude připojen k z Internetu.
6. V části **vytvořit** v **Souhrn**, vyberte **vytvořit** ke spuštění nasazení virtuálních počítačů.

    Virtuální počítač trvá několik minut pro vytvoření. Nebudete pokračovat k dalšímu kroku až Azure dokončí vytváření virtuálního počítače a otevře pole s informacemi o virtuální počítač.

7. Do pole Otevřít pro virtuální počítač po jeho vytvoření, v části **nastavení**, vyberte **sítě**a potom vyberte **myvmnva158** (síťové rozhraní Azure vytvořené pro vaše Virtuální počítač obsahuje odlišný počet po **myvmnva**), jak je znázorněno na následujícím obrázku:

    ![Sítě virtuálních počítačů](./media/tutorial-create-route-table-portal/virtual-machine-networking.png) 

8. Pro síťové rozhraní, abyste mohli předávat síťové přenosy odesílané do, který není určený pro vlastní IP adresu, musí být povolené předávání IP pro síťové rozhraní. V části **nastavení**, vyberte **konfigurace protokolu IP**, vyberte **povoleno** pro **předávání IP**a potom vyberte **uložit** , jak je znázorněno na následujícím obrázku:

    ![Povolení předávání IP](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png) 

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvoří dva virtuální počítače ve virtuální síti, takže můžete ověřit, že provoz z *veřejné* podsítě se směruje na *privátní* podsítě hodnocení chyb zabezpečení v pozdější fázi. Dokončit kroky 1 – 6 [vytvořit hodnocení chyb zabezpečení](#create-a-network-virtual-appliance). Použijte stejná nastavení v kroku 3 a 5, s výjimkou následující změny:

|Název virtuálního počítače      |Podsíť      | Veřejná IP adresa     |
|--------- | -----------|---------              |
| myVmPublic  | Veřejné     | Přijměte výchozí portálu |
| myVmPrivate | Privátní    | Přijměte výchozí portálu |

Můžete vytvořit *myVmPrivate* virtuálních počítačů, zatímco Azure vytváří *myVmPublic* virtuálních počítačů. Následující kroky nepokračujte, dokud Azure dokončí vytváření oba virtuální počítače.

## <a name="route-traffic-through-an-nva"></a>Směrovat provoz prostřednictvím hodnocení chyb zabezpečení

1. V *vyhledávání* pole v horní části portálu, začněte psát *myVmPrivate*. Když **myVmPrivate** virtuálního počítače se zobrazí ve výsledcích hledání vyberte ho.
2. Vytvoření připojení ke vzdálené ploše na *myVmPrivate* virtuálních počítačů tak, že vyberete **Connect**, jak je znázorněno na následujícím obrázku:

    ![Připojení k virtuálnímu počítači ](./media/tutorial-create-route-table-portal/connect-to-virtual-machine.png)  

3. Chcete-li připojit k virtuálnímu počítači, otevřete stažený soubor RDP. Po zobrazení výzvy vyberte **Connect**.
4. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače (budete muset vybrat možnost **další možnosti**, pak **použít jiný účet**, zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače), potom vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Vyberte **Ano** pokračovat v připojení.
6. V pozdější fázi příkaz tracert.exe slouží k otestování, směrování. Tracert používá zprávu protokolu ICMP (Internet Control), který byl odepřen přes bránu Windows Firewall. Povolte protokol ICMP přes bránu Windows firewall tak, že zadáte následující příkaz z prostředí PowerShell:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    I když tracert se používá k testování směrování v tomto článku, což ICMP přes bránu Windows Firewall pro nasazení v produkčním prostředí se nedoporučuje.
7. Jste povolili předávání IP v rámci Azure pro síťové rozhraní Virtuálního počítače v [předávání IP povolit](#enable-ip-forwarding). V rámci virtuálního počítače operační systém nebo aplikace běžící v rámci virtuálního počítače, musí taky umět předávat síťový provoz. Povolení předávání IP v rámci operačního systému *myVmNva* virtuálního počítače provedením následujících kroků z *myVmPrivate* virtuálních počítačů:

    Vzdálenou plochu *myVmNva* pomocí následujícího příkazu z příkazového řádku:

    ``` 
    mstsc /v:myvmnva
    ```
    
    Pokud chcete povolit předávání v operačním systému protokolu IP, zadejte následující příkaz v prostředí PowerShell:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```
    
    Restartujte virtuální počítač, který také odpojí relaci vzdálené plochy.
8. Při stále připojen k *myVmPrivate* virtuálních počítačů, vytvořit relaci vzdálené plochy k *myVmPublic* virtuálních počítačů pomocí následujícího příkazu po *myVmNva* restartování virtuálního počítače:

    ``` 
    mstsc /v:myVmPublic
    ```
    
    Povolte protokol ICMP přes bránu Windows firewall tak, že zadáte následující příkaz z prostředí PowerShell:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

9. K testování směrování síťový provoz *myVmPrivate* virtuální počítač z *myVmPublic* virtuální počítač, z prostředí PowerShell zadejte následující příkaz:

    ```
    tracert myVmPrivate
    ```

    Odpověď je stejný jako v následujícím příkladu:
    
    ```
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:
        
    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4
        
    Trace complete.
    ```
      
    Uvidíte, že prvním skoku je 10.0.2.4, který je hodnocení chyb zabezpečení privátní IP adresu. Druhé směrování je 10.0.1.4, privátní IP adresu *myVmPrivate* virtuálních počítačů. Trasy přidat do *myRouteTablePublic* směrovací tabulky a přidružené k *veřejné* podsíť způsobila Azure pro směrování provozu prostřednictvím hodnocení chyb zabezpečení, nikoli přímo do *privátní* podsítě.
10.  Zavřete relaci vzdálené plochy k *myVmPublic* virtuálních počítačů, což zanechá jste stále připojeni k *myVmPrivate* virtuálních počítačů.
11. K testování směrování síťový provoz *myVmPublic* virtuální počítač z *myVmPrivate* virtuální počítač, zadejte následující příkaz z příkazového řádku:

    ```
    tracert myVmPublic
    ```

    Odpověď je stejný jako v následujícím příkladu:

    ```
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:
    
    1     1 ms     1 ms     1 ms  10.0.0.4
    
    Trace complete.
    ```

    Uvidíte, že provoz se směruje přímo z *myVmPrivate* virtuálního počítače *myVmPublic* virtuálních počítačů. Ve výchozím nastavení Azure trasy provoz přímo mezi podsítěmi.
12. Zavřete relaci vzdálené plochy k *myVmPrivate* virtuálních počítačů.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, odstraňte skupinu prostředků a všechny prostředky, které obsahuje: 

1. Zadejte *myResourceGroup* v **vyhledávání** pole v horní části portálu. Až se zobrazí **myResourceGroup** ve výsledcích hledání vyberte ho.
2. Vyberte **Odstranit skupinu prostředků**.
3. Zadejte *myResourceGroup* pro **název skupiny prostředků typu:** a vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

V tomto článku vytvořit směrovací tabulku a přidružené k podsíti. Můžete vytvořit jednoduché chyb zabezpečení, který směruje provoz z veřejné podsítě privátní podsítě. Nasazení celou řadu předem nakonfigurovaná NVAs, které provádějí síťových funkcí, jako jsou brány firewall a optimalizace sítě WAN z [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Před nasazením směrovací tabulky pro použití v provozním prostředí, doporučujeme, aby důkladně Seznamte se s [směrování v Azure](virtual-networks-udr-overview.md), [spravovat směrovací tabulky](manage-route-table.md), a [Azure omezuje](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).


Když nasadíte mnoho prostředků Azure v rámci virtuální sítě, zdrojů u některých služeb Azure PaaS nelze nasadit do virtuální sítě. Můžete dál omezit přístup k prostředkům některé služby Azure PaaS provoz jenom z podsítě virtuální sítě, když. Přechodu na v dalším kurzu se dozvíte, jak omezit přístup k síti na Azure PaaS prostředky.

> [!div class="nextstepaction"]
> [Omezit přístup k síti na PaaS prostředky](tutorial-restrict-network-access-to-resources.md)
