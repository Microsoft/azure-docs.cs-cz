---
title: Směrování síťového provozu – Kurz – Azure Portal | Microsoft Docs
description: V tomto kurzu zjistíte, jak směrovat síťový provoz s využitím směrovací tabulky pomocí webu Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 7254e9336fca14daee2021d5bde4c5538509fe35
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Kurz: Směrování síťového provozu s využitím směrovací tabulky pomocí webu Azure Portal

Azure ve výchozím nastavení automaticky směruje provoz mezi všemi podsítěmi v rámci virtuální sítě. Můžete vytvořit vlastní trasy a přepsat tak výchozí směrování Azure. Možnost vytvářet vlastní trasy je užitečná například v případě, že chcete směrovat provoz mezi podsítěmi přes síťové virtuální zařízení. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření směrovací tabulky
> * Vytvoření trasy
> * Vytvoření virtuální sítě s několika podsítěmi
> * Přidružení směrovací tabulky k podsíti
> * Vytvoření síťového virtuálního zařízení, které směruje provoz
> * Nasazení virtuálních počítačů do různých podsítí
> * Směrování provozu z jedné podsítě do jiné přes síťové virtuální zařízení

Pokud chcete, můžete tento kurz absolvovat s použitím [Azure CLI](tutorial-create-route-table-cli.md) nebo [Azure PowerShellu](tutorial-create-route-table-powershell.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.

## <a name="create-a-route-table"></a>Vytvoření směrovací tabulky

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Vyberte **Sítě** a pak vyberte **Směrovací tabulka**.
3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myRouteTablePublic|
    |Předplatné| Vyberte své předplatné.|
    |Skupina prostředků | Vyberte **Vytvořit novou** a zadejte *myResourceGroup*.|
    |Umístění|Východ USA|
 
    ![Vytvoření směrovací tabulky](./media/tutorial-create-route-table-portal/create-route-table.png) 

## <a name="create-a-route"></a>Vytvoření trasy

1. Do pole *Hledat prostředky, služby a dokumenty* v horní části portálu začněte zadávat *myRouteTablePublic*. Jakmile se ve výsledcích hledání zobrazí směrovací tabulka **myRouteTablePublic**, vyberte ji.
2. V části **NASTAVENÍ** vyberte **Trasy** a pak vyberte **+ Přidat**, jak je znázorněno na následujícím obrázku:

    ![Přidání trasy](./media/tutorial-create-route-table-portal/add-route.png) 
 
3. V části **Přidat trasu** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:

    |Nastavení|Hodnota|
    |---|---|
    |Název trasy|ToPrivateSubnet|
    |Předpona adresy| 10.0.1.0/24|
    |Typ dalšího segmentu | Vyberte **Virtuální zařízení**.|
    |Adresa dalšího segmentu| 10.0.2.4|

## <a name="associate-a-route-table-to-a-subnet"></a>Přidružení směrovací tabulky k podsíti

Nejprve musíte vytvořit virtuální síť a podsíť, a teprve pak můžete směrovací tabulku přidružit k podsíti:

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Vyberte **Sítě** a pak vyberte **Virtuální síť**.
3. V části **Vytvořit virtuální síť** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVirtualNetwork|
    |Adresní prostor| 10.0.0.0/16|
    |Předplatné | Vyberte své předplatné.|
    |Skupina prostředků|Vyberte **Použít existující** a pak vyberte **myResourceGroup**.|
    |Umístění|Vyberte *USA – východ*.|
    |Název podsítě|Public|
    |Rozsah adres|10.0.0.0/24|
    
4. Do pole **Hledat prostředky, služby a dokumenty** v horní části portálu začněte zadávat *myVirtualNetwork*. Jakmile se ve výsledcích hledání zobrazí virtuální síť **myVirtualNetwork**, vyberte ji.
5. V části **NASTAVENÍ** vyberte **Podsítě** a pak vyberte **+ Podsíť**, jak je znázorněno na následujícím obrázku:

    ![Přidání podsítě](./media/tutorial-create-route-table-portal/add-subnet.png) 

6. Vyberte nebo zadejte následující informace a pak vyberte **OK**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|Private|
    |Adresní prostor| 10.0.1.0/24|

7. Zopakujte kroky 5 a 6 a zadejte následující informace:

    |Nastavení|Hodnota|
    |---|---|
    |Název|DMZ|
    |Adresní prostor| 10.0.2.0/24|

8. Po dokončení předchozího kroku se zobrazí okno **myVirtualNetwork – Podsítě**. V části **NASTAVENÍ** vyberte **Podsítě** a pak vyberte **Public**.
9. Jak je znázorněno na následujícím obrázku, vyberte **Směrovací tabulka**, pak **MyRouteTablePublic** a pak vyberte **Uložit**:

    ![Přidružení směrovací tabulky](./media/tutorial-create-route-table-portal/associate-route-table.png) 

## <a name="create-an-nva"></a>Vytvoření síťového virtuálního zařízení

Síťové virtuální zařízení je virtuální počítač, který provádí síťovou funkci, jako je směrování, brána firewall nebo optimalizace sítě WAN.

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**. Můžete vybrat jiný operační systém, ale ve zbývajících krocích se předpokládá, že jste vybrali **Windows Server 2016 Datacenter**. 
3. V části **Základy** vyberte nebo zadejte následující informace a pak vyberte **OK**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVmNva|
    |Uživatelské jméno|Zadejte libovolné uživatelské jméno.|
    |Heslo|Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Skupina prostředků| Vyberte **Použít existující** a pak vyberte *myResourceGroup*.|
    |Umístění|Vyberte **USA – východ**.|
4. V části **Zvolte velikost** vyberte velikost virtuálního počítače.
5. V části **Nastavení** vyberte nebo zadejte následující informace a pak vyberte **OK**:

    |Nastavení|Hodnota|
    |---|---|
    |Virtuální síť|myVirtualNetwork – Pokud ještě není vybraná, vyberte **Virtuální síť** a pak v části **Zvolte virtuální síť** vyberte **myVirtualNetwork**.|
    |Podsíť|Vyberte **Podsíť** a pak v části **Zvolte podsíť** vyberte **DMZ**. |
    |Veřejná IP adresa| Vyberte **Veřejná IP adresa** a v části **Zvolte veřejnou IP adresu** vyberte **Žádná**. K tomuto virtuálnímu počítači není přiřazená žádná veřejná IP adresa, protože se k němu nebude připojovat z internetu.
6. V části **Vytvořit** na kartě **Souhrn** vyberte **Vytvořit** a spusťte nasazování virtuálního počítače.

    Vytvoření virtuálního počítače trvá několik minut. Nepokračujte k dalšímu kroku, dokud Azure nedokončí vytváření virtuálního počítače a neotevře se okno s informacemi o virtuálním počítači.

7. V okně pro virtuální počítač, které se otevřelo po jeho vytvoření, v části **NASTAVENÍ** vyberte **Sítě** a pak vyberte **myvmnva158** (síťové rozhraní, které Azure vytvořilo pro váš virtuální počítač, má za **myvmnva** jiné číslo), jak je znázorněno na následujícím obrázku:

    ![Sítě virtuálních počítačů](./media/tutorial-create-route-table-portal/virtual-machine-networking.png) 

8. Aby síťové rozhraní mohlo směrovat síťový provoz, který se do něj odešle a který není určený pro jeho vlastní IP adresu, musí být pro síťové rozhraní povolené předávání IP. V části **NASTAVENÍ** vyberte **Konfigurace IP**, u možnosti **Předávání IP** vyberte **Povoleno** a pak vyberte **Uložit**, jak je znázorněno na následujícím obrázku:

    ![Povolení předávání IP](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png) 

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte ve virtuální síti dva virtuální počítače, abyste v pozdějším kroku mohli ověřit směrování provozu z podsítě *Public* do podsítě *Private*. Proveďte kroky 1 až 6 z části [Vytvoření síťového virtuálního zařízení](#create-a-network-virtual-appliance). V krocích 3 a 5 použijte stejné nastavení s následujícími změnami:

|Název virtuálního počítače      |Podsíť      | Veřejná IP adresa     |
|--------- | -----------|---------              |
| myVmPublic  | Public     | Přijměte výchozí hodnotu na portálu |
| myVmPrivate | Private    | Přijměte výchozí hodnotu na portálu |

Zatímco Azure vytváří virtuální počítač *myVmPublic*, můžete vytvořit virtuální počítač *myVmPrivate*. Nepokračujte následujícími kroky, dokud Azure nedokončí vytváření obou virtuálních počítačů.

## <a name="route-traffic-through-an-nva"></a>Směrování provozu přes síťové virtuální zařízení

1. Do pole *Hledat* v horní části portálu začněte zadávat *myVmPrivate*. Jakmile se ve výsledcích hledání zobrazí virtuální počítač **myVmPrivate**, vyberte ho.
2. Vyberte **Připojit** a vytvořte připojení ke vzdálené ploše virtuálního počítače *myVmPrivate*, jak je znázorněno na následujícím obrázku:

    ![Připojení k virtuálnímu počítači ](./media/tutorial-create-route-table-portal/connect-to-virtual-machine.png)  

3. Pokud se chcete připojit k virtuálnímu počítači, otevřete stažený soubor RDP. Pokud se zobrazí výzva, vyberte **Připojit**.
4. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače (abyste mohli zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače, možná budete muset vybrat **Další možnosti** a pak **Použít jiný účet**), a pak vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Vyberte **Ano** a pokračujte v připojování.
6. V pozdějším kroku se k otestování směrování použije nástroj pro trasování tras. Trasování tras využívá protokol ICMP (Internet Control Message Protocol), který má ve výchozím nastavení zakázaný průchod bránou Windows Firewall. Povolte průchod protokolu ICMP bránou Windows Firewall zadáním následujícího příkazu v PowerShellu na virtuálním počítači *myVmPrivate*:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    Přestože se k otestování směrování v tomto kurzu používá trasování tras, povolení průchodu protokolu ICMP bránou Windows Firewall v produkčních prostředích se nedoporučuje.
7. V části [Povolení předávání IP](#enable-ip-forwarding) jste povolili předávání IP v rámci Azure pro síťové rozhraní virtuálního počítače. Operační systém nebo aplikace spuštěná v rámci virtuálního počítače musí také být schopné směrovat síťový provoz. Povolte předávání IP v rámci operačního systému virtuálního počítače *myVmNva*:

    Na příkazovém řádku na virtuálním počítači *myVmPrivate* se připojte ke vzdálené ploše virtuálního počítače *myVmNva*:

    ``` 
    mstsc /v:myvmnva
    ```
    
    Pokud chcete povolit předávání IP v rámci operačního systému, v PowerShellu na virtuálním počítači *myVmNva* zadejte následující příkaz:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```
    
    Restartujte virtuální počítač *myVmNva*. Tím se také odpojí relace vzdálené plochy.
8. Zatímco jste stále připojeni k virtuálnímu počítači *myVmPrivate*, po restartování virtuálního počítače *myVmNva* vytvořte relaci vzdálené plochy k virtuálnímu počítači *myVmPublic*:

    ``` 
    mstsc /v:myVmPublic
    ```
    
    Povolte průchod protokolu ICMP bránou Windows Firewall zadáním následujícího příkazu v PowerShellu na virtuálním počítači *myVmPublic*:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

9. Pokud chcete otestovat směrování síťového provozu z virtuálního počítače *myVmPublic* do virtuálního počítače *myVmPrivate*, v PowerShellu na virtuálním počítači *myVmPublic* zadejte následující příkaz:

    ```
    tracert myVmPrivate
    ```

    Odpověď bude podobná jako v následujícím příkladu:
    
    ```
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:
        
    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4
        
    Trace complete.
    ```
      
    Jak vidíte, první segment směrování je 10.0.2.4, což je privátní IP adresa síťového virtuálního zařízení. Druhý segment směrování je 10.0.1.4, což je privátní IP adresa virtuálního počítače *myVmPrivate*. Trasa přidaná do směrovací tabulky *myRouteTablePublic* a přidružená k podsíti *Public* způsobila, že Azure směruje provoz přes síťové virtuální zařízení, a ne přímo do podsítě *Private*.
10.  Ukončete relaci vzdálené plochy k virtuálnímu počítači *myVmPublic*. Stále zůstanete připojeni k virtuální síti *myVmPrivate*.
11. Pokud chcete otestovat směrování síťového provozu z virtuálního počítače *myVmPrivate* do virtuálního počítače *myVmPublic*, na příkazovém řádku na virtuálním počítači *myVmPrivate* zadejte následující příkaz:

    ```
    tracert myVmPublic
    ```

    Odpověď bude podobná jako v následujícím příkladu:

    ```
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:
    
    1     1 ms     1 ms     1 ms  10.0.0.4
    
    Trace complete.
    ```

    Jak vidíte, provoz se směruje přímo z virtuálního počítače *myVmPrivate* do virtuálního počítače *myVmPublic*. Azure ve výchozím nastavení směruje provoz přímo mezi podsítěmi.
12. Ukončete relaci vzdálené plochy k virtuálnímu počítači *myVmPrivate*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků a všechny prostředky, které obsahuje: 

1. Do pole **Hledat** v horní části portálu zadejte *myResourceGroup*. Jakmile se ve výsledcích hledání zobrazí skupina prostředků **myResourceGroup**, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. V části **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ** zadejte *myResourceGroup* a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili směrovací tabulku a přidružili jste ji k podsíti. Vytvořili jste jednoduché síťové virtuální zařízení, které směrovalo provoz z veřejné podsítě do privátní podsítě. Z webu [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking) můžete nasadit různá předem nakonfigurovaná síťová virtuální zařízení, která provádí síťové funkce, jako je brána firewall a optimalizace sítě WAN. Další informace o směrování najdete v tématech [Přehled směrování](virtual-networks-udr-overview.md) a [Správa směrovací tabulky](manage-route-table.md).


Přestože v rámci virtuální sítě můžete nasadit řadu prostředků Azure, prostředky některých služeb Azure PaaS do virtuální sítě nasadit nejde. Přesto můžete omezit přístup k prostředkům některých služeb Azure PaaS pouze pro provoz z podsítě virtuální sítě. Informace o omezení síťového přístupu k prostředkům Azure PaaS najdete v následujícím kurzu.

> [!div class="nextstepaction"]
> [Omezení síťového přístupu k prostředkům PaaS](tutorial-restrict-network-access-to-resources.md)
