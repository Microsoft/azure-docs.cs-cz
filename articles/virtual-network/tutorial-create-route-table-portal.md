---
title: Směrování síťového provozu – kurz – Azure Portal
titlesuffix: Azure Virtual Network
description: V tomto kurzu zjistíte, jak směrovat síťový provoz s využitím směrovací tabulky pomocí webu Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/16/2021
ms.author: kumud
ms.openlocfilehash: f8090ea9c0d307d1bd290c4cf4dac9bfaabf7c4b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104576313"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Kurz: Směrování síťového provozu s využitím směrovací tabulky pomocí webu Azure Portal

Azure ve výchozím nastavení směruje provoz mezi všemi podsítěmi v rámci virtuální sítě. Můžete vytvořit vlastní trasy a přepsat tak výchozí směrování Azure. Vlastní trasy jsou užitečné, když například chcete směrovat provoz mezi podsítěmi přes síťové virtuální zařízení (síťové virtuální zařízení). V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření síťového virtuálního zařízení, které směruje provoz
> * Vytvoření směrovací tabulky
> * Vytvoření trasy
> * Přidružení směrovací tabulky k podsíti
> * Nasazení virtuálních počítačů do různých podsítí
> * Směrování provozu z jedné podsítě do jiné přes síťové virtuální zařízení

V tomto kurzu se používá [Azure Portal](https://portal.azure.com). Můžete použít také rozhraní příkazového [řádku Azure](tutorial-create-route-table-cli.md) nebo [Azure PowerShell](tutorial-create-route-table-powershell.md).

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. V nabídce webu Azure Portal vyberte **Vytvořit prostředek**. Z Azure Marketplace vyberte **síť**  >  **virtuální síť** nebo vyhledejte **Virtual Network** do vyhledávacího pole.

2. Vyberte **Vytvořit**.

2. V nástroji **vytvořit virtuální síť** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte **vytvořit nový**, zadejte **myResourceGroup**. </br> Vyberte **OK**. |
    | Name | Zadejte **myVirtualNetwork**. |
    | Umístění | Vyberte **(US) východní USA**.|

3. Vyberte kartu **IP adresy** nebo klikněte na tlačítko **Další: IP adresy** v dolní části stránky.

4. V části **adresní prostor IPv4** vyberte existující adresní prostor a změňte ho na **10.0.0.0/16**.

4. Vyberte **+ Přidat podsíť** a potom zadejte **Public** pro **název podsítě** a **10.0.0.0/24** pro **Rozsah adres podsítě**.

5. Vyberte **Přidat**.

6. Vyberte **+ Přidat podsíť** a potom zadejte **Private** pro **název podsítě** a **10.0.1.0/24** pro **Rozsah adres podsítě**.

7. Vyberte **Přidat**.

8. Vyberte **+ Přidat podsíť** a pak jako **název podsítě** zadejte **DMZ** a **10.0.2.0/24** pro **Rozsah adres podsítě**.

9. Vyberte **Přidat**.

10. Vyberte kartu **zabezpečení** nebo v dolní části stránky vyberte tlačítko **Další: zabezpečení** .

11. V části **BastionHost** vyberte **Povolit**. Zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Název bastionu | Zadejte **myBastionHost** |
    | Adresní prostor AzureBastionSubnet | Zadejte **10.0.3.0/24** |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. </br> Jako **název** zadejte **myBastionIP**. </br> Vyberte **OK**. |

12. Vyberte kartu **Revize + vytvořit** nebo vyberte tlačítko **Revize + vytvořit** .

13. Vyberte **Vytvořit**.

## <a name="create-an-nva"></a>Vytvoření síťového virtuálního zařízení

Síťová virtuální zařízení (síťová virtuální zařízení) jsou virtuální počítače, které vám pomůžou se síťovými funkcemi, jako je například směrování a optimalizace brány firewall. V tomto kurzu se předpokládá, že používáte **Windows Server 2019 Datacenter**. Pokud chcete, můžete vybrat jiný operační systém.

1. V levé horní části portálu vyberte **vytvořit prostředek**  >    >  **virtuální počítač** Compute. 
   
2. V části **vytvořit virtuální počítač** zadejte nebo vyberte hodnoty na kartě **základy** :

    | Nastavení | Hodnota                                          |
    |-----------------------|----------------------------------|
    | **Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné Azure. |
    | Skupina prostředků | Vybrat **myResourceGroup** |
    | **Podrobnosti o instancích** |  |
    | Název virtuálního počítače | Zadejte **myVMNVA** |
    | Oblast | Vyberte **(US) východní USA** |
    | Možnosti dostupnosti | Vyberte možnost **nepožaduje se žádná redundance infrastruktury** . |
    | Image | Vyberte **Windows Server 2019 Datacenter** |
    | Instance Azure Spot | Vybrat **ne** |
    | Velikost | Vyberte velikost virtuálního počítače nebo použijte výchozí nastavení. |
    | **Účet správce** |  |
    | Uživatelské jméno | Zadejte uživatelské jméno. |
    | Heslo | Zadat heslo |
    | Potvrzení hesla | Znovu zadejte heslo. |
    | **Pravidla portů pro příchozí spojení** |    |
    | Veřejné příchozí porty | Vyberte **Žádná**. |
    |

3. Vyberte kartu **síť** nebo vyberte **Další: disky** a **Další: síť**.
  
4. Na kartě sítě vyberte nebo zadejte:

    | Nastavení | Hodnota |
    |-|-|
    | **Síťové rozhraní** |  |
    | Virtuální síť | Vyberte **myVirtualNetwork**. |
    | Podsíť | Vybrat **DMZ** |
    | Veřejná IP adresa | Vybrat **žádné** |
    | Skupina zabezpečení sítě NIC | Vybrat **základní**|
    | Síť veřejných příchozích portů | Vyberte **Žádná**. |
   
5. Vyberte kartu **Revize + vytvořit** nebo v dolní části stránky vyberte tlačítko modrá **recenze + vytvořit** .
  
6. Zkontrolujte nastavení a pak vyberte **vytvořit**.

## <a name="create-a-route-table"></a>Vytvoření směrovací tabulky

1. V nabídce webu [Azure Portal](https://portal.azure.com) nebo na **domovské stránce** vyberte **Vytvořit prostředek**.

2. Do vyhledávacího pole zadejte **směrovací tabulku**. Pokud se ve výsledcích hledání zobrazí **tabulka směrování** , vyberte ji.

3. Na stránce **směrovací tabulka** vyberte **vytvořit**.

4. V části **vytvořit tabulku směrování** na kartě **základy** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** |   |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte **myResourceGroup**. |
    | **Podrobnosti o instancích** |    |
    | Oblast | Vyberte **USA – východ**. |
    | Name | Zadejte **myRouteTablePublic**. |
    | Šíření tras brány | Vyberte **Ano**. |

    :::image type="content" source="./media/tutorial-create-route-table-portal/create-route-table.png" alt-text="Vytvoření směrovací tabulky Azure Portal." border="true":::

5. Vyberte kartu **Revize + vytvořit** nebo v dolní části stránky vyberte tlačítko modrá **recenze + vytvořit** .

## <a name="create-a-route"></a>Vytvoření trasy

1. Chcete-li spravovat směrovací tabulku, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **směrovací tabulky**.

2. Vyberte název směrovací tabulky **myRouteTablePublic**.

3. Na stránce **myRouteTablePublic** v části **Nastavení** vyberte **trasy**.

4. Na stránce trasy vyberte tlačítko **+ Přidat** .

5. Do **Přidat trasu** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název trasy | Zadejte **ToPrivateSubnet** |
    | Předpona adresy | Zadejte **10.0.1.0/24** (rozsah adres **privátní** podsítě, kterou jste vytvořili dříve). |
    | Typ dalšího přesměrování | Vyberte **Virtuální zařízení**. |
    | Adresa dalšího segmentu | Zadejte **10.0.2.4** (adresu v rozsahu adres podsítě **DMZ** ). |

6. Vyberte **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Přidružení směrovací tabulky k podsíti

1. Pokud chcete spravovat svoji virtuální síť, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte možnost **virtuální sítě**.

2. Vyberte název vaší virtuální sítě **myVirtualNetwork**.

3. Na stránce **myVirtualNetwork** v části **Nastavení** vyberte **podsítě**.

4. V seznamu podsíť virtuální sítě vyberte možnost **veřejné**.

5. V **tabulce směrování** vyberte směrovací tabulku, kterou jste vytvořili **myRouteTablePublic**. 

6. Vyberte **Uložit** a přidružte tabulku směrování k **veřejné** podsíti.

    :::image type="content" source="./media/tutorial-create-route-table-portal/associate-route-table.png" alt-text="Přidružte tabulku směrování, seznam podsítí, virtuální síť Azure Portal." border="true":::

## <a name="turn-on-ip-forwarding"></a>Zapnout předávání IP

Pak zapněte předávání IP pro nový virtuální počítač s síťové virtuální zařízení, **myVMNVA**. Když Azure posílá síťový provoz do **myVMNVA**, pokud je provoz určený pro jinou IP adresu, předávání IP adres odešle do správného umístění.

1. Pokud chcete spravovat svůj virtuální počítač, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **virtuální počítače**.

2. Vyberte název vašeho virtuálního počítače **myVMNVA**.

3. Na stránce Přehled **myVMNVA** v části **Nastavení** vyberte **sítě**.

4. Na stránce **síť** v **myVMNVA** vyberte síťové rozhraní vedle **síťového rozhraní**.  Název rozhraní bude začínat na **myvmnva**.

    :::image type="content" source="./media/tutorial-create-route-table-portal/virtual-machine-networking.png" alt-text="Sítě, virtuální počítač (síťové virtuální zařízení) síťového virtuálního zařízení (VM), Azure Portal" border="true":::

5. Na stránce Přehled rozhraní sítě v části **Nastavení** vyberte **Konfigurace protokolu IP**.

6. Na stránce **Konfigurace protokolu IP** nastavte **předávání IP** na **povoleno** a pak vyberte **Uložit**.

    :::image type="content" source="./media/tutorial-create-route-table-portal/enable-ip-forwarding.png" alt-text="Povolení předávání IP" border="true":::

## <a name="create-public-and-private-virtual-machines"></a>Vytvoření veřejného a privátního virtuálního počítače

Vytvořte veřejný virtuální počítač a privátní virtuální počítač ve virtuální síti. Později je budete používat k zobrazení, že Azure směruje provoz **veřejné** podsítě do **privátní** podsítě prostřednictvím rozhraní síťové virtuální zařízení.


### <a name="public-vm"></a>Veřejný virtuální počítač

1. V levé horní části portálu vyberte **vytvořit prostředek**  >    >  **virtuální počítač** Compute. 
   
2. V části **vytvořit virtuální počítač** zadejte nebo vyberte hodnoty na kartě **základy** :

    | Nastavení | Hodnota                                          |
    |-----------------------|----------------------------------|
    | **Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné Azure. |
    | Skupina prostředků | Vybrat **myResourceGroup** |
    | **Podrobnosti o instancích** |  |
    | Název virtuálního počítače | Zadejte **myVMPublic** |
    | Oblast | Vyberte **(US) východní USA** |
    | Možnosti dostupnosti | Vyberte možnost **nepožaduje se žádná redundance infrastruktury** . |
    | Image | Vyberte **Windows Server 2019 Datacenter** |
    | Instance Azure Spot | Vybrat **ne** |
    | Velikost | Vyberte velikost virtuálního počítače nebo použijte výchozí nastavení. |
    | **Účet správce** |  |
    | Uživatelské jméno | Zadejte uživatelské jméno. |
    | Heslo | Zadat heslo |
    | Potvrzení hesla | Znovu zadejte heslo. |
    | **Pravidla portů pro příchozí spojení** |    |
    | Veřejné příchozí porty | Vyberte **Žádná**. |
    |

3. Vyberte kartu **síť** nebo vyberte **Další: disky** a **Další: síť**.
  
4. Na kartě sítě vyberte nebo zadejte:

    | Nastavení | Hodnota |
    |-|-|
    | **Síťové rozhraní** |  |
    | Virtuální síť | Vyberte **myVirtualNetwork**. |
    | Podsíť | Vybrat **veřejné** |
    | Veřejná IP adresa | Vybrat **žádné** |
    | Skupina zabezpečení sítě NIC | Vybrat **základní**|
    | Síť veřejných příchozích portů | Vyberte **Žádná**. |
   
5. Vyberte kartu **Revize + vytvořit** nebo v dolní části stránky vyberte tlačítko modrá **recenze + vytvořit** .
  
6. Zkontrolujte nastavení a pak vyberte **vytvořit**.

### <a name="private-vm"></a>Privátní virtuální počítač

1. V levé horní části portálu vyberte **vytvořit prostředek**  >    >  **virtuální počítač** Compute. 
   
2. V části **vytvořit virtuální počítač** zadejte nebo vyberte hodnoty na kartě **základy** :

    | Nastavení | Hodnota                                          |
    |-----------------------|----------------------------------|
    | **Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné Azure. |
    | Skupina prostředků | Vybrat **myResourceGroup** |
    | **Podrobnosti o instancích** |  |
    | Název virtuálního počítače | Zadejte **myVMPrivate** |
    | Oblast | Vyberte **(US) východní USA** |
    | Možnosti dostupnosti | Vyberte možnost **nepožaduje se žádná redundance infrastruktury** . |
    | Image | Vyberte **Windows Server 2019 Datacenter** |
    | Instance Azure Spot | Vybrat **ne** |
    | Velikost | Vyberte velikost virtuálního počítače nebo použijte výchozí nastavení. |
    | **Účet správce** |  |
    | Uživatelské jméno | Zadejte uživatelské jméno. |
    | Heslo | Zadat heslo |
    | Potvrzení hesla | Znovu zadejte heslo. |
    | **Pravidla portů pro příchozí spojení** |    |
    | Veřejné příchozí porty | Vyberte **Žádná**. |
    |

3. Vyberte kartu **síť** nebo vyberte **Další: disky** a **Další: síť**.
  
4. Na kartě sítě vyberte nebo zadejte:

    | Nastavení | Hodnota |
    |-|-|
    | **Síťové rozhraní** |  |
    | Virtuální síť | Vyberte **myVirtualNetwork**. |
    | Podsíť | Vybrat **soukromou** |
    | Veřejná IP adresa | Vybrat **žádné** |
    | Skupina zabezpečení sítě NIC | Vybrat **základní**|
    | Síť veřejných příchozích portů | Vyberte **Žádná**. |
   
5. Vyberte kartu **Revize + vytvořit** nebo v dolní části stránky vyberte tlačítko modrá **recenze + vytvořit** .
  
6. Zkontrolujte nastavení a pak vyberte **vytvořit**.

## <a name="route-traffic-through-an-nva"></a>Směrování provozu přes síťové virtuální zařízení

### <a name="sign-in-to-private-vm"></a>Přihlášení k privátnímu virtuálnímu počítači

1. Pokud chcete spravovat privátní virtuální počítač, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **virtuální počítače**.

2. Vyberte název privátního virtuálního počítače **myVmPrivate**.

3. V řádku nabídek virtuálního počítače vyberte **připojit** a pak vyberte **bastionu**.

4. Na stránce **připojit** vyberte tlačítko modré **použití bastionu** .

5. Na stránce **bastionu** zadejte uživatelské jméno a heslo, které jste dříve vytvořili pro virtuální počítač.

6. Vyberte **Connect** (Připojit).

### <a name="configure-firewall"></a>Konfigurace brány firewall

V pozdějším kroku použijete nástroj Trace Route k otestování směrování. Trasování tras používá protokol ICMP (Internet Control Message Protocol), který ve výchozím nastavení zakáže bránu Windows Firewall. 

Povolte protokol ICMP přes bránu Windows Firewall.

1. V bastionu připojení **myVMPrivate** otevřete PowerShell s oprávněními správce.

2. Zadejte tento příkaz:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    K otestování směrování v tomto kurzu budete používat trasu trasování. V produkčních prostředích nedoporučujeme povolit protokol ICMP prostřednictvím brány Windows Firewall.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Zapnutí předávání IP v rámci myVMNVA

[Zapnuli jste předávání IP](#turn-on-ip-forwarding) pro síťové rozhraní virtuálního počítače pomocí Azure. Operační systém virtuálního počítače taky musí přesměrování provozu v síti. 

Pomocí těchto příkazů zapněte předávání IP pro **myVMNVA** .

1. Z PowerShellu na virtuálním počítači s **myVMPrivate** otevřete vzdálenou plochu na virtuální počítač **myVMNVA** :

    ```powershell
    mstsc /v:myvmnva
    ```

2. V PowerShellu na virtuálním počítači **myVMNVA** zadejte tento příkaz, abyste zapnuli předávání IP:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

3. Restartujte **myVMNVA**.

    ```powershell
    Restart-Computer
    ```

4. Po restartování **myVMNVA** vytvořit relaci vzdálené plochy k **myVMPublic**. 
    
    I když jste připojení k **myVMPrivate**, otevřete PowerShell a spusťte tento příkaz:

    ```powershell
    mstsc /v:myvmpublic
    ```
5. Ve vzdálené ploše **myVMPublic** otevřete PowerShell.

6. Povolte protokol ICMP přes bránu Windows Firewall zadáním tohoto příkazu:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>Testování směrování síťového provozu

Nejprve otestujeme směrování síťového provozu z **myVMPublic** do **myVMPrivate**.

1. V prostředí PowerShell v **myVMPublic** zadejte tento příkaz:

    ```powershell
    tracert myvmprivate
    ```

    Odpověď je podobná jako v tomto příkladu:

    ```powershell
    Tracing route to myvmprivate.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

      1     1 ms     *        2 ms  myvmnva.internal.cloudapp.net [10.0.2.4]
      2     2 ms     1 ms     1 ms  myvmprivate.internal.cloudapp.net [10.0.1.4]

    Trace complete.
    ```

    * První směrování si můžete prohlédnout na 10.0.2.4, což je privátní IP adresa **myVMNVA** . 

    * Druhým směrováním je privátní IP adresa **myVMPrivate**: 10.0.1.4. 

    Dříve jste přidali trasu do tabulky směrování **myRouteTablePublic** a přidružíte ji k *veřejné* podsíti. Azure odeslal přenos přes síťové virtuální zařízení, a ne přímo do *privátní* podsítě.

2. Zavřete relaci vzdálené plochy do **myVMPublic**, která vám zůstane pořád připojená k **myVMPrivate**.

3. Otevřete PowerShell v **myVMPrivate**, zadejte tento příkaz:

    ```powershell
    tracert myvmpublic
    ```

    Tento příkaz testuje směrování síťového provozu z virtuálního počítače *myVmPrivate* do virtuálního počítače *myVmPublic* . Odpověď je podobná jako v tomto příkladu:

    ```cmd
    Tracing route to myvmpublic.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

      1     1 ms     1 ms     1 ms  myvmpublic.internal.cloudapp.net [10.0.0.4]

    Trace complete.
    ```

    Můžete vidět, že Azure směruje provoz přímo z *myVMPrivate* do *myVMPublic*. Azure ve výchozím nastavení směruje provoz přímo mezi podsítěmi.

4. Zavřete relaci bastionu do **myVMPrivate**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už skupinu prostředků nepotřebujete, odstraňte **myResourceGroup** a všechny prostředky, které obsahuje:

1. Pokud chcete spravovat skupinu prostředků, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **skupiny prostředků**.

2. Vyberte název skupiny prostředků **myResourceGroup**.

3. Vyberte **Odstranit skupinu prostředků**.

4. V potvrzovacím dialogovém okně zadejte **myResourceGroup** pro **Zadejte název skupiny prostředků** a pak vyberte **Odstranit**. 


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

* Byla vytvořena směrovací tabulka a přidružená k podsíti.
* Vytvořili jste jednoduché síťové virtuální zařízení, který směruje provoz z veřejné podsítě do privátní podsítě. 

Můžete nasadit jinou předem nakonfigurovanou síťová virtuální zařízení z [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking), která poskytuje mnoho užitečných síťových funkcí. 

Další informace o směrování najdete v tématech [Přehled směrování](virtual-networks-udr-overview.md) a [Správa směrovací tabulky](manage-route-table.md).

Postup filtrování síťového provozu ve virtuální síti najdete v těchto tématech:
> [!div class="nextstepaction"]
> [Kurz: filtrování síťového provozu pomocí skupiny zabezpečení sítě pomocí Azure Portal](tutorial-filter-network-traffic.md)
