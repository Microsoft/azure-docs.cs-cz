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
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: d630a41f9b83a852605ffad2a85ad6dd14bbac73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86079645"
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

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-nva"></a>Vytvoření síťového virtuálního zařízení

Síťová virtuální zařízení (síťová virtuální zařízení) jsou virtuální počítače, které vám pomůžou se síťovými funkcemi, jako je například směrování a optimalizace brány firewall. V tomto kurzu se předpokládá, že používáte **Windows Server 2016 Datacenter**. Pokud chcete, můžete vybrat jiný operační systém.

1. V nabídce webu [Azure Portal](https://portal.azure.com) nebo na **domovské stránce** vyberte **Vytvořit prostředek**.

1. Vyberte **zabezpečení**  >  **Windows serveru 2016 Datacenter**.

    ![Windows Server 2016 Datacenter, vytvoření virtuálního počítače, Azure Portal](./media/tutorial-create-route-table-portal/vm-ws2016-datacenter.png)

1. Na stránce **vytvořit virtuální počítač** v části **základy**zadejte nebo vyberte tyto informace:

    | Sekce | Nastavení | Akce |
    | ------- | ------- | ----- |
    | **Podrobnosti o projektu** | Předplatné | Zvolte vaše předplatné. |
    | | Skupina prostředků | Vyberte **vytvořit novou**, zadejte *MyResourceGroup*a vyberte **OK**. |
    | **Podrobnosti o instancích** | Název virtuálního počítače | Zadejte *myVmNva*. |
    | | Oblast | Vyberte **východní USA (US)**. |
    | | Možnosti dostupnosti | Vyberte možnost **nepožaduje se žádná redundance infrastruktury**. |
    | | Image | Vyberte **Windows Server 2016 Datacenter**. |
    | | Velikost | Ponechte výchozí hodnotu **Standard DS1 v2**. |
    | **Účet správce** | Uživatelské jméno | Zadejte libovolné uživatelské jméno. |
    | | Heslo | Zadejte heslo, které chcete zvolit. musí mít aspoň 12 znaků a musí splňovat [definované požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). |
    | | Potvrdit heslo | Znovu zadejte heslo. |
    | **Pravidla portů pro příchozí spojení** | Veřejné příchozí porty | Vyberte **žádný**. |
    | **Úspora peněz** | Máte už licenci k Windows serveru? | Vyberte **ne**. |

    ![Základy, vytváření virtuálních počítačů, Azure Portal](./media/tutorial-create-route-table-portal/basics-create-virtual-machine.png)

    Pak vyberte **Další: disky >**.

1. V části **disky**vyberte nastavení, která jsou pro vaše potřeby vhodná, a potom vyberte **další: síťové >**.

1. V části **síť**:

    1. V případě **virtuální sítě**vyberte **vytvořit novou**.
    
    1. V dialogovém okně **vytvořit virtuální síť** zadejte do pole **název** *myVirtualNetwork*.

    1. V **adresním prostoru**nahraďte stávající rozsah adres *10.0.0.0/16*.

    1. V části **podsítě**výběrem ikony **Odstranit** odstraňte existující podsíť a potom zadejte následující kombinace **názvu podsítě** a **rozsahu adres**. Po zadání platného názvu a rozsahu se pod ním zobrazí nový prázdný řádek.

        | Název podsítě | Rozsah adres |
        | ----------- | ------------- |
        | *Republik* | *10.0.0.0/24* |
        | *Hlášen* | *10.0.1.0/24* |
        | *DMZ* | *10.0.2.0/24* |

    1. Kliknutím na **tlačítko OK** zavřete dialogové okno.

    1. V možnosti **podsíť**vyberte **DMZ (10.0.2.0/24)**.

    1. Ve **veřejné IP adrese**vyberte **žádné**, protože se tento virtuální počítač nebude připojovat přes Internet.

    1. Vyberte **Další: >správy **.

1. Pod položkou **Správa**:

    1. V **účtu úložiště pro diagnostiku**vyberte **vytvořit novou**.
    
    1. V dialogovém okně **vytvořit účet úložiště** zadejte nebo vyberte tyto informace:

        | Nastavení | Hodnota |
        | ------- | ----- |
        | Název | *mynvastorageaccount* |
        | Druh účtu | **Storage (pro obecné účely V1)** |
        | Výkon | **Standard** |
        | Replikace | **Místně redundantní úložiště (LRS)** |
    
    1. Kliknutím na **tlačítko OK** zavřete dialogové okno.

    1. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvořit** a Azure ověří vaši konfiguraci.

1. Jakmile se zobrazí zpráva **Ověření proběhlo úspěšně**, vyberte **Vytvořit**.

    Vytvoření virtuálního počítače trvá několik minut. Počkejte, až Azure dokončí vytváření virtuálního počítače. Na stránce **vaše nasazení se** zobrazí podrobnosti o nasazení.

1. Až bude váš virtuální počítač připravený, vyberte **Přejít k prostředku**.

## <a name="create-a-route-table"></a>Vytvoření směrovací tabulky

1. V nabídce webu [Azure Portal](https://portal.azure.com) nebo na **domovské stránce** vyberte **Vytvořit prostředek**.

2. Do vyhledávacího pole zadejte *směrovací tabulku*. Pokud se ve výsledcích hledání zobrazí **tabulka směrování** , vyberte ji.

3. Na stránce **směrovací tabulka** vyberte **vytvořit**.

4. V **tabulce vytvořit směrovací tabulku**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | *myRouteTablePublic* |
    | Předplatné | Vaše předplatné |
    | Skupina prostředků | **myResourceGroup** |
    | Umístění | **(USA) USA – východ** |
    | Šíření tras brány virtuální sítě | **Povoleno** |

    ![Vytvoření směrovací tabulky, Azure Portal](./media/tutorial-create-route-table-portal/create-route-table.png)

5. Vyberte **Vytvořit**.

## <a name="create-a-route"></a>Vytvoření trasy

1. Chcete-li spravovat směrovací tabulku, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **směrovací tabulky**.

1. Vyberte název směrovací tabulky (**myRouteTablePublic**).

1. Vyberte **trasy**  >  **Přidat**.

    ![Přidat trasu, směrovací tabulku, Azure Portal](./media/tutorial-create-route-table-portal/add-route.png)

1. Do **Přidat trasu**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název trasy | *ToPrivateSubnet* |
    | Předpona adresy | *10.0.1.0/24* (rozsah adres *privátní* podsítě vytvořené dříve) |
    | Typ dalšího segmentu směrování | **Virtuální zařízení** |
    | Adresa dalšího segmentu | *10.0.2.4* (adresa v rozsahu adres podsítě *DMZ* ) |

1. Vyberte **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Přidružení směrovací tabulky k podsíti

1. Pokud chcete spravovat svoji virtuální síť, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte možnost **virtuální sítě**.

1. Vyberte název virtuální sítě (**myVirtualNetwork**).

1. V řádku nabídek virtuální sítě vyberte **podsítě**.

1. V seznamu podsíť virtuální sítě vyberte možnost **veřejné**.

1. V **tabulce směrování**zvolte směrovací tabulku, kterou jste vytvořili (**myRouteTablePublic**), a pak vyberte **Uložit** a přidružte tabulku směrování k *veřejné* podsíti.

    ![Přidružit směrovací tabulku, seznam podsítí, virtuální síť, Azure Portal](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="turn-on-ip-forwarding"></a>Zapnout předávání IP

Pak zapněte předávání IP pro nový virtuální počítač s síťové virtuální zařízení, *myVmNva*. Když Azure posílá síťový provoz do *myVmNva*, pokud je provoz určený pro jinou IP adresu, předávání IP adres odešle do správného umístění.

1. Pokud chcete spravovat svůj virtuální počítač, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **virtuální počítače**.

1. Vyberte název vašeho virtuálního počítače (**myVmNva**).

1. V řádku nabídek virtuálního počítače s síťové virtuální zařízení vyberte **sítě**.

1. Vyberte **myvmnva123**. Toto je síťové rozhraní, které Azure vytvořil pro váš virtuální počítač. Azure přidává čísla, aby se zajistil jedinečný název.

    ![Sítě, virtuální počítač (síťové virtuální zařízení) síťového virtuálního zařízení (VM), Azure Portal](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. V panelu nabídek síťové rozhraní vyberte **Konfigurace protokolu IP**.

1. Na stránce **Konfigurace protokolu IP** nastavte **předávání IP** na **povoleno**a vyberte **Uložit**.

    ![Povolit předávání IP, konfigurace IP, síťové rozhraní, virtuální počítač síťové virtuální zařízení (Network Virtual zařízení), Azure Portal](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Vytvoření veřejného a privátního virtuálního počítače

Vytvořte veřejný virtuální počítač a privátní virtuální počítač ve virtuální síti. Později je budete používat k zobrazení, že Azure směruje provoz *veřejné* podsítě do *privátní* podsítě prostřednictvím rozhraní síťové virtuální zařízení.

Pokud chcete vytvořit veřejný virtuální počítač a privátní virtuální počítač, postupujte podle kroků v části [Vytvoření síťové virtuální zařízení](#create-an-nva) výše. Nemusíte čekat na dokončení nasazení nebo přejít na prostředek virtuálního počítače. Použijete většinu stejných nastavení, s výjimkou popsaných níže.

Než vyberete **vytvořit** , abyste vytvořili veřejný nebo privátní virtuální počítač, Projděte si následující dvě pododdíly ([veřejný virtuální počítač](#public-vm) a [privátní virtuální počítač](#private-vm)), které zobrazují hodnoty, které se musí lišit. Až Azure dokončí nasazení obou virtuálních počítačů, můžete pokračovat k další části ([směrování provozu přes síťové virtuální zařízení](#route-traffic-through-an-nva)).

### <a name="public-vm"></a>Veřejný virtuální počítač

| Karta | Nastavení | Hodnota |
| --- | ------- | ----- |
| Základy | Skupina prostředků | **myResourceGroup** |
| | Název virtuálního počítače | *myVmPublic* |
| | Veřejné příchozí porty | **Povolení vybraných portů** |
| | Vyberte příchozí porty | **Protokol RDP** |
| Sítě | Virtuální síť | **myVirtualNetwork** |
| | Podsíť | **Veřejné (10.0.0.0/24)** |
| | Veřejná IP adresa | Výchozí hodnota |
| Správa | Účet úložiště diagnostiky | **mynvastorageaccount** |

### <a name="private-vm"></a>Privátní virtuální počítač

| Karta | Nastavení | Hodnota |
| --- | ------- | ----- |
| Základy | Skupina prostředků | **myResourceGroup** |
| | Název virtuálního počítače | *myVmPrivate* |
| | Veřejné příchozí porty | **Povolení vybraných portů** |
| | Vyberte příchozí porty | **Protokol RDP** |
| Sítě | Virtuální síť | **myVirtualNetwork** |
| | Podsíť | **Privátní (10.0.1.0/24)** |
| | Veřejná IP adresa | Výchozí hodnota |
| Správa | Účet úložiště diagnostiky | **mynvastorageaccount** |

## <a name="route-traffic-through-an-nva"></a>Směrování provozu přes síťové virtuální zařízení

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Přihlášení k myVmPrivate prostřednictvím vzdálené plochy

1. Pokud chcete spravovat privátní virtuální počítač, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **virtuální počítače**.

1. Vyberte název privátního virtuálního počítače (**myVmPrivate**).

1. Na řádku nabídek virtuálního počítače vyberte **připojit** a vytvořte připojení ke vzdálené ploše k PRIVÁTNÍmu virtuálnímu počítači.

1. Na stránce **připojit se pomocí protokolu RDP** vyberte **Stáhnout soubor RDP**. Azure vytvoří soubor protokol RDP (Remote Desktop Protocol) (*. RDP*) a stáhne ho do vašeho počítače.

1. Otevřete stažený soubor *. RDP* . Pokud se zobrazí výzva, vyberte **Připojit**. Vyberte **Další možnosti**  >  **použít jiný účet**a pak zadejte uživatelské jméno a heslo, které jste zadali při vytváření privátního virtuálního počítače.

1. Vyberte **OK**.

1. Pokud se během procesu přihlašování zobrazí upozornění certifikátu, vyberte **Ano** a připojte se k virtuálnímu počítači.

### <a name="enable-icmp-through-the-windows-firewall"></a>Povolení protokolu ICMP přes bránu Windows Firewall

V pozdějším kroku použijete nástroj Trace Route k otestování směrování. Trasování tras používá protokol ICMP (Internet Control Message Protocol), který ve výchozím nastavení zakáže bránu Windows Firewall. Povolte protokol ICMP přes bránu Windows Firewall.

1. Ve vzdálené ploše *myVmPrivate*otevřete PowerShell.

1. Zadejte tento příkaz:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    K otestování směrování v tomto kurzu budete používat trasu trasování. V produkčních prostředích nedoporučujeme povolit protokol ICMP prostřednictvím brány Windows Firewall.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Zapnutí předávání IP v rámci myVmNva

[Zapnuli jste předávání IP](#turn-on-ip-forwarding) pro síťové rozhraní virtuálního počítače pomocí Azure. Operační systém virtuálního počítače taky musí přesílat síťový provoz. Pomocí těchto příkazů zapněte předávání IP pro operační systém virtuálního počítače s *myVmNva* .

1. Z příkazového řádku na virtuálním počítači s *myVmPrivate* otevřete na virtuálním počítači *myVmNva* vzdálenou plochu:

    ```cmd
    mstsc /v:myvmnva
    ```

1. V PowerShellu na virtuálním počítači *myVmNva* zadejte tento příkaz, abyste zapnuli předávání IP:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Restartujte virtuální počítač *myVmNva* : na hlavním panelu vyberte **Spustit**  >  **napájení**, **ostatní (plánované)**  >  **pokračovat**.

    Tím se také odpojí relace vzdálené plochy.

1. Po restartování virtuálního počítače *myVmNva* vytvořte relaci vzdálené plochy k virtuálnímu počítači s *myVmPublic* . I když se stále připojíte k virtuálnímu počítači s *myVmPrivate* , otevřete příkazový řádek a spusťte tento příkaz:

    ```cmd
    mstsc /v:myVmPublic
    ```
1. Ve vzdálené ploše *myVmPublic*otevřete PowerShell.

1. Povolte protokol ICMP přes bránu Windows Firewall zadáním tohoto příkazu:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>Testování směrování síťového provozu

Nejprve otestujeme směrování síťového provozu z virtuálního počítače *myVmPublic* do virtuálního počítače *myVmPrivate* .

1. V PowerShellu na virtuálním počítači *myVmPublic* zadejte tento příkaz:

    ```powershell
    tracert myVmPrivate
    ```

    Odpověď je podobná jako v tomto příkladu:

    ```powershell
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4

    Trace complete.
    ```

    První směrování si můžete prohlédnout na 10.0.2.4, což je privátní IP adresa síťové virtuální zařízení. Druhým směrováním je privátní IP adresa virtuálního počítače *myVmPrivate* : 10.0.1.4. Dříve jste přidali trasu do tabulky směrování *myRouteTablePublic* a přidružíte ji k *veřejné* podsíti. V důsledku toho Azure odeslal přenos prostřednictvím síťové virtuální zařízení a nepřímo do *privátní* podsítě.

1. Ukončete relaci vzdálené plochy k virtuálnímu počítači *myVmPublic*. Stále zůstanete připojeni k virtuální síti *myVmPrivate*.

1. Z příkazového řádku na virtuálním počítači *myVmPrivate* zadejte tento příkaz:

    ```cmd
    tracert myVmPublic
    ```

    Tento příkaz testuje směrování síťového provozu z virtuálního počítače *myVmPrivate* do virtuálního počítače *myVmPublic* . Odpověď je podobná jako v tomto příkladu:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Můžete vidět, že Azure směruje provoz přímo z virtuálního počítače *myVmPrivate* do virtuálního počítače *myVmPublic* . Azure ve výchozím nastavení směruje provoz přímo mezi podsítěmi.

1. Ukončete relaci vzdálené plochy k virtuálnímu počítači *myVmPrivate*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už skupinu prostředků nepotřebujete, odstraňte *myResourceGroup* a všechny prostředky, které obsahuje:

1. Pokud chcete spravovat skupinu prostředků, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **skupiny prostředků**.

1. Vyberte název vaší skupiny prostředků (**myResourceGroup**).

1. Vyberte **Odstranit skupinu prostředků**.

1. V potvrzovacím dialogovém okně zadejte *myResourceGroup* pro **Zadejte název skupiny prostředků**a pak vyberte **Odstranit**. Azure odstraní *myResourceGroup* a všechny prostředky, které jsou svázané s touto skupinou prostředků, včetně směrovacích tabulek, účtů úložiště, virtuálních sítí, virtuálních počítačů, síťových rozhraní a veřejných IP adres.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili směrovací tabulku a přidružili jste ji k podsíti. Vytvořili jste jednoduché síťové virtuální zařízení, které směrovalo provoz z veřejné podsítě do privátní podsítě. Nyní můžete z [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking)nasadit různé předem nakonfigurované síťová virtuální zařízení, které poskytují mnoho užitečných síťových funkcí. Další informace o směrování najdete v tématech [Přehled směrování](virtual-networks-udr-overview.md) a [Správa směrovací tabulky](manage-route-table.md).

I když můžete nasadit mnoho prostředků Azure v rámci virtuální sítě, Azure nemůže nasadit prostředky pro některé služby PaaS do virtuální sítě. Je možné omezit přístup k prostředkům některých služeb Azure PaaS, i když omezení musí být jenom provozu z podsítě virtuální sítě. Další informace o tom, jak omezit síťový přístup k prostředkům Azure PaaS, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Omezení síťového přístupu k prostředkům PaaS](tutorial-restrict-network-access-to-resources.md)

> [!NOTE] 
> Náklady na služby Azure Services. Azure Cost Management vám pomůže nastavit rozpočty a nakonfigurovat výstrahy, aby zůstaly pod kontrolou. Pomocí Cost Management Analyzujte, spravujte a optimalizujte náklady na Azure. Další informace najdete v [rychlém startu při analýze vašich nákladů](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).