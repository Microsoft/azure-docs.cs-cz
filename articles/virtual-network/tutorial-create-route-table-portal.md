---
title: Provoz na trase – kurz – portál Azure
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
ms.openlocfilehash: a565aba12f1b10f215d8f6cc7fc0b7247a0441d2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80066351"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Kurz: Směrování síťového provozu s využitím směrovací tabulky pomocí webu Azure Portal

Azure ve výchozím nastavení směruje provoz mezi všemi podsítěmi v rámci virtuální sítě. Můžete vytvořit vlastní trasy a přepsat tak výchozí směrování Azure. Vlastní trasy jsou užitečné, když například chcete směrovat provoz mezi podsítěmi prostřednictvím síťového virtuálního zařízení (NVA). V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření síťového virtuálního zařízení, které směruje provoz
> * Vytvoření směrovací tabulky
> * Vytvoření trasy
> * Přidružení směrovací tabulky k podsíti
> * Nasazení virtuálních počítačů do různých podsítí
> * Směrování provozu z jedné podsítě do jiné přes síťové virtuální zařízení

Tento kurz používá [portál Azure](https://portal.azure.com). Můžete taky použít [Azure CLI](tutorial-create-route-table-cli.md) nebo [Azure PowerShell](tutorial-create-route-table-powershell.md).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="create-an-nva"></a>Vytvoření síťového virtuálního zařízení

Síťová virtuální zařízení (NVA) jsou virtuální počítače, které pomáhají se síťovými funkcemi, jako je směrování a optimalizace brány firewall. Tento kurz předpokládá, že používáte **Windows Server 2016 Datacenter**. Pokud chcete, můžete vybrat jiný operační systém.

1. V nabídce [Portál Azure](https://portal.azure.com) nebo na **domovské** stránce vyberte **Vytvořit prostředek**.

1. Zvolte **Zabezpečení** > **Windows Server 2016 Datacenter**.

    ![Datacentrum Windows Serveru 2016, Vytvoření virtuálního počítače, portál Azure](./media/tutorial-create-route-table-portal/vm-ws2016-datacenter.png)

1. Na stránce **Vytvořit virtuální počítač** v části **Základy**zadejte nebo vyberte tyto informace:

    | Sekce | Nastavení | Akce |
    | ------- | ------- | ----- |
    | **Podrobnosti o projektu** | Předplatné | Zvolte vaše předplatné. |
    | | Skupina prostředků | Vyberte **Vytvořit nový**, zadejte *myResourceGroup*a vyberte **OK**. |
    | **Podrobnosti instance** | Název virtuálního počítače | Zadejte *myVmNva*. |
    | | Region (Oblast) | Zvolte **(USA) – východ USA**. |
    | | Možnosti dostupnosti | Zvolte **Není nutné žádné redundance infrastruktury**. |
    | | Image | Zvolte **Windows Server 2016 Datacenter**. |
    | | Velikost | Zachovat výchozí, **Standardní DS1 v2**. |
    | **Účet správce** | Uživatelské jméno | Zadejte libovolné uživatelské jméno. |
    | | Heslo | Zadejte heslo podle vašeho výběru, které musí mít alespoň 12 znaků a musí splňovat [definované požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). |
    | | Potvrdit heslo | Zadejte heslo znovu. |
    | **Pravidla portů pro příchozí provoz** | Veřejné příchozí porty | Vyberte **žádný**. |
    | **Ušetřete peníze** | Už máte licenci windows serveru? | **Vyskladnění Číslo**. |

    ![Základy, vytvoření virtuálního počítače, portál Azure](./media/tutorial-create-route-table-portal/basics-create-virtual-machine.png)

    Pak vyberte **Další : Disky >**.

1. V části **Disky**vyberte nastavení, která jsou vhodná pro vaše potřeby, a pak vyberte **Další : Síťové >**.

1. V části **Vytváření sítí**:

    1. V **popřípadě Virtuální síť**vyberte Vytvořit **nový**.
    
    1. V dialogovém okně **Vytvořit virtuální síť** zadejte v části **Název** *program myVirtualNetwork*.

    1. V **adresním prostoru**nahraďte existující rozsah adres *10.0.0.0/16*.

    1. V **podsítích**vyberte ikonu **Odstranit,** chcete-li odstranit existující podsíť, a zadejte následující kombinace **názvu podsítě** a **rozsahu adres**. Po zadání platného názvu a rozsahu se pod ním zobrazí nový prázdný řádek.

        | Název podsítě | Rozsah adres |
        | ----------- | ------------- |
        | *Veřejné* | *10.0.0.0/24* |
        | *Soukromé* | *10.0.1.0/24* |
        | *DMZ* | *10.0.2.0/24* |

    1. Chcete-li dialogové okno ukončit, vyberte **OK.**

    1. V **podsíti**zvolte **DMZ (10.0.2.0/24)**.

    1. Ve **veřejné IP ip**zvolte **Žádný**, protože tento virtuální virtuální ms se nebude připojovat přes internet.

    1. Vyberte **další : >správy **.

1. Pod **vedením**:

    1. V **účtu úložiště diagnostiky**vyberte **vytvořit nový**.
    
    1. V dialogovém okně **Vytvořit účet úložiště** zadejte nebo vyberte tyto informace:

        | Nastavení | Hodnota |
        | ------- | ----- |
        | Name (Název) | *mynvastorageaccount* |
        | Account kind (Druh účtu) | **Skladování (pro všeobecné použití v1)** |
        | Výkon | **Standard** |
        | Replikace | **Místně redundantní úložiště (LRS)** |
    
    1. Chcete-li dialogové okno ukončit, vyberte **OK.**

    1. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvoření** a Azure ověří vaši konfiguraci.

1. Když se zobrazí zpráva **Ověření předáno,** vyberte **vytvořit**.

    Vytvoření virtuálního počítače trvá několik minut. Počkejte, až Azure dokončí vytváření virtuálního počítače. Vaše **nasazení probíhá** stránka zobrazuje podrobnosti o nasazení.

1. Až bude virtuální počítač připravený, vyberte **Přejít na prostředek**.

## <a name="create-a-route-table"></a>Vytvoření směrovací tabulky

1. V nabídce [Portál Azure](https://portal.azure.com) nebo na **domovské** stránce vyberte **Vytvořit prostředek**.

2. Do vyhledávacího pole zadejte *tabulku Trasa*. Když se **tabulka Trasa** zobrazí ve výsledcích hledání, vyberte ji.

3. Na stránce **Tabulka trasa** vyberte **Vytvořit**.

4. V **tabulce Vytvořit postup**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | *myRouteTablePublic* |
    | Předplatné | Vaše předplatné |
    | Skupina prostředků | **myResourceGroup** |
    | Umístění | **(USA) Východní USA** |
    | Šíření trasy brány virtuální sítě | **Enabled** (Povoleno) |

    ![Vytvoření tabulky postupu, portálazure](./media/tutorial-create-route-table-portal/create-route-table.png)

5. Vyberte **Vytvořit**.

## <a name="create-a-route"></a>Vytvoření trasy

1. Přejděte na [portál Azure](https://portal.azure.com) a spravujte směrovací tabulku. Vyhledejte a vyberte **možnost Směrovat tabulky**.

1. Vyberte název směrovací tabulky **(myRouteTablePublic).**

1. Zvolte **Přidat trasy** > **.**

    ![Přidání trasy, tabulky tras, portálu Azure](./media/tutorial-create-route-table-portal/add-route.png)

1. Do **položky Přidat trasu**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název trasy | *ToPrivateSubnet* |
    | Předpona adresy | *10.0.1.0/24* (rozsah adres *soukromé* podsítě vytvořené dříve) |
    | Typ dalšího segmentu | **Virtuální zařízení** |
    | Adresa dalšího segmentu | *10.0.2.4* (adresa v rozsahu adres podsítě *DMZ)* |

1. Vyberte **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Přidružení směrovací tabulky k podsíti

1. Přejděte na [portál Azure](https://portal.azure.com) a spravujte svou virtuální síť. Vyhledejte a vyberte **virtuální sítě**.

1. Vyberte název virtuální sítě **(myVirtualNetwork**).

1. V řádku nabídek virtuální sítě zvolte **Podsítě**.

1. V seznamu podsítí virtuální sítě zvolte **Veřejné**.

1. V **tabulce Trasa**vyberte vytvořenou směrovací tabulku **(myRouteTablePublic)** a pak vyberte **Uložit,** chcete-li přiřadit směrovací tabulku k *veřejné* podsíti.

    ![Přidružení směrovací tabulky, seznamu podsítí, virtuální sítě, portálu Azure](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="turn-on-ip-forwarding"></a>Zapnutí předávání IP adres

Dále zapněte předávání IP adres pro váš nový virtuální stroj NVA, *myVmNva*. Když Azure odešle síťový provoz na *myVmNva*, pokud je provoz určen pro jinou IP adresu, předávání IP odešle provoz do správného umístění.

1. Přejděte na [portál Azure](https://portal.azure.com) a spravujte svůj virtuální počítač. Vyhledejte a vyberte **virtuální počítače**.

1. Vyberte název virtuálního počítače **(myVmNva).**

1. V řádku nabídek virtuálního počítače NVA vyberte **možnost Síťování**.

1. Vyberte **myvmnva123**. To je síťové rozhraní, které Azure vytvořil pro váš virtuální počítač. Azure přidává čísla, aby zajistil jedinečný název.

    ![Sítě, virtuální zařízení pro síť (NVA) virtuální počítač (VM), portál Azure](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. V řádku nabídek síťového rozhraní vyberte **konfigurace PROTOKOLU IP**.

1. Na stránce **Konfigurace PROTOKOLU IP** nastavte předávání **IP** adres na **Povoleno**a vyberte **Uložit**.

    ![Povolit předávání IP adres, konfigurace IP adres, síťové rozhraní, virtuální počítač (NVA) síťového virtuálního zařízení (VM), portál Azure](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Vytvoření veřejného a privátního virtuálního počítače

Vytvořte veřejný virtuální počítač a soukromý virtuální počítač ve virtuální síti. Později je použijete k zobrazení, že Azure směruje provoz *veřejné* podsítě do *privátní* podsítě prostřednictvím síťového virtuálního počítače.

Pokud chcete vytvořit veřejný virtuální virtuální a soukromý virtuální virtuální podnik, postupujte podle pokynů [k vytvoření služby NVA](#create-an-nva) dříve. Nemusíte čekat na dokončení nasazení nebo přejít na prostředek virtuálního počítače. Budete používat většinu stejných nastavení, s výjimkou případů popsaných níže.

Než vyberete **Vytvořit** pro vytvoření veřejného nebo soukromého virtuálního virtuálního ms, přejděte na následující dvě podsekce[(veřejný virtuální virtuální honový virtuální a](#public-vm) soukromý [virtuální hod](#private-vm)), které zobrazují hodnoty, které se mají lišit. Můžete pokračovat na další část[(Směrování provozu prostřednictvím nVA](#route-traffic-through-an-nva)) po Azure dokončí nasazení obou virtuálních počítačů.

### <a name="public-vm"></a>Veřejný virtuální virtuální měn

| Karta | Nastavení | Hodnota |
| --- | ------- | ----- |
| Základy | Skupina prostředků | **myResourceGroup** |
| | Název virtuálního počítače | *myVmPublic* |
| | Veřejné příchozí porty | **Povolit vybrané porty** |
| | Výběr příchozích portů | **PROTOKOLY HTTP** a **RDP** |
| Síťové služby | Virtuální síť | **myVirtualNetwork** |
| | Podsíť | **Veřejná otisčná (10.0.0.0/24)** |
| | Veřejná IP adresa | Výchozí hodnota |
| Správa | Účet úložiště diagnostiky | **mynvastorageaccount** |

### <a name="private-vm"></a>Soukromý virtuální virtuální automobil

| Karta | Nastavení | Hodnota |
| --- | ------- | ----- |
| Základy | Skupina prostředků | **myResourceGroup** |
| | Název virtuálního počítače | *myVmPrivate* |
| | Veřejné příchozí porty | **Povolit vybrané porty** |
| | Výběr příchozích portů | **PROTOKOLY HTTP** a **RDP** |
| Síťové služby | Virtuální síť | **myVirtualNetwork** |
| | Podsíť | **Soukromé (10.0.1.0/24)** |
| | Veřejná IP adresa | Výchozí hodnota |
| Správa | Účet úložiště diagnostiky | **mynvastorageaccount** |

## <a name="route-traffic-through-an-nva"></a>Směrování provozu přes síťové virtuální zařízení

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Přihlášení k myVmPrivate přes vzdálenou plochu

1. Přejděte na [portál Azure](https://portal.azure.com) a spravujte svůj soukromý virtuální počítač. Vyhledejte a vyberte **virtuální počítače**.

1. Vyberte název soukromého virtuálního počítače **(myVmPrivate).**

1. Na řádku nabídek virtuálního počítače vyberte **Připojit** a vytvořte připojení ke vzdálené ploše k soukromému virtuálnímu počítači.

1. Na stránce **Připojit pomocí rdp** vyberte **Stáhnout soubor RDP**. Azure vytvoří soubor rdp (Remote Desktop Protocol *)* a stáhne ho do počítače.

1. Otevřete stažený soubor *RDP.* Pokud se zobrazí výzva, vyberte **Připojit**. Vyberte **Další volby:** > **Použijte jiný účet**a zadejte uživatelské jméno a heslo, které jste zadali při vytváření soukromého virtuálního počítače.

1. Vyberte **OK**.

1. Pokud se během procesu přihlášení zobrazí upozornění na certifikát, vyberte **Ano,** chcete-li se připojit k virtuálnímu virtuálnímu síti.

### <a name="enable-icmp-through-the-windows-firewall"></a>Povolení protokolu ICMP prostřednictvím brány firewall systému Windows

V pozdějším kroku použijete nástroj trasování k testování směrování. Trasa trasování používá protokol ICMP (Internet Control Message Protocol), který brána Windows Firewall ve výchozím nastavení popírá. Povolte icmp prostřednictvím brány firewall systému Windows.

1. Na vzdálené ploše *myVmPrivate*otevřete prostředí PowerShell.

1. Zadejte tento příkaz:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Trasování bude v tomto kurzu používat trasování k testování směrování. V produkčních prostředích nedoporučujeme povolit icmp prostřednictvím brány Windows Firewall.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Zapnutí předávání IP adres v rámci myVmNva

[Zapnuli](#turn-on-ip-forwarding) jste předávání IP adres pro síťové rozhraní virtuálního počítače pomocí Azure. Operační systém virtuálního připojení musí také předávat síťový provoz. Zapněte předávání IP adres pro operační systém virtuálního počítači *myVmNva* pomocí těchto příkazů.

1. Z příkazového řádku na virtuálním počítači *myVmPrivate* otevřete vzdálenou plochu virtuálnímu počítači *myVmNva:*

    ```cmd
    mstsc /v:myvmnva
    ```

1. Z prostředí PowerShell na virtuálním počítači *myVmNva* zadejte tento příkaz a zapněte předávání IP adres:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Restartujte virtuální počítač *myVmNva:* Na hlavním panelu vyberte **Spustit** > **napájení**, Ostatní **(plánované)** > **Pokračovat**.

    Tím se také odpojí relace vzdálené plochy.

1. Po restartování virtuálního počítače *myVmNva* vytvořte relaci vzdálené plochy pro virtuální počítač *myVmPublic.* Když jste stále připojeni k virtuálnímu virtuálnímu mněmu *myVmPrivate,* otevřete příkazový řádek a spusťte tento příkaz:

    ```cmd
    mstsc /v:myVmPublic
    ```
1. Na vzdálené ploše *myVmPublic*otevřete prostředí PowerShell.

1. Povolte icmp prostřednictvím brány firewall systému Windows zadáním tohoto příkazu:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>Testování směrování síťového provozu

Nejprve otestujeme směrování síťového provozu z virtuálního virtuálního virtuálního ms *myVmPublic* do virtuálního virtuálního mísu *myVmPrivate.*

1. Z Prostředí PowerShell na virtuálním počítači *myVmPublic* zadejte tento příkaz:

    ```powershell
    tracert myVmPrivate
    ```

    Odpověď je podobná tomuto příkladu:

    ```powershell
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4

    Trace complete.
    ```

    Můžete vidět první směrování je 10.0.2.4, což je privátní IP adresa NVA. Druhý směrování je na privátní IP adresu virtuálního počítačů *myVmPrivate:* 10.0.1.4. Dříve jste přidali trasu do směrovací tabulky *myRouteTablePublic* a přidružili ji k *veřejné* podsíti. V důsledku toho Azure odeslal provoz prostřednictvím síťového virtuálního počítače a není přímo do *soukromé* podsítě.

1. Ukončete relaci vzdálené plochy k virtuálnímu počítači *myVmPublic*. Stále zůstanete připojeni k virtuální síti *myVmPrivate*.

1. Z příkazového řádku na virtuálním počítači *myVmPrivate* zadejte tento příkaz:

    ```cmd
    tracert myVmPublic
    ```

    Tento příkaz testuje směrování síťového provozu z virtuálního virtuálního ms *myVmPrivate* do virtuálního virtuálního mísa *myVmPublic.* Odpověď je podobná tomuto příkladu:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Uvidíte, že Azure směruje provoz přímo z virtuálního počítače *myVmPrivate* do virtuálního počítače *myVmPublic.* Azure ve výchozím nastavení směruje provoz přímo mezi podsítěmi.

1. Ukončete relaci vzdálené plochy k virtuálnímu počítači *myVmPrivate*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud skupina prostředků již není potřeba, odstraňte *myResourceGroup* a všechny prostředky, které má:

1. Přejděte na [portál Azure](https://portal.azure.com) a spravujte svou skupinu prostředků. Vyhledejte a vyberte **skupiny prostředků**.

1. Vyberte název skupiny prostředků **(myResourceGroup**).

1. Vyberte **Odstranit skupinu prostředků**.

1. V potvrzovacím dialogovém okně zadejte *myResourceGroup* pro **typ názvu skupiny prostředků**a pak vyberte **Odstranit**. Azure odstraní *myResourceGroup* a všechny prostředky vázané na tuto skupinu prostředků, včetně trasových tabulek, účtů úložiště, virtuálních sítí, virtuálních počítačů, síťových rozhraní a veřejných IP adres.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili směrovací tabulku a přidružili jste ji k podsíti. Vytvořili jste jednoduché síťové virtuální zařízení, které směrovalo provoz z veřejné podsítě do privátní podsítě. Teď můžete nasadit různé předkonfigurované síťové virtuální zařízení z [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking), které poskytují mnoho užitečných síťových funkcí. Další informace o směrování najdete v tématech [Přehled směrování](virtual-networks-udr-overview.md) a [Správa směrovací tabulky](manage-route-table.md).

Zatímco můžete nasadit mnoho prostředků Azure v rámci virtuální sítě, Azure nemůže nasadit prostředky pro některé služby PaaS do virtuální sítě. Je možné omezit přístup k prostředkům některých služeb Azure PaaS, i když omezení musí být pouze provoz z podsítě virtuální sítě. Informace o tom, jak omezit přístup k síti k prostředkům Azure PaaS, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Omezení síťového přístupu k prostředkům PaaS](tutorial-restrict-network-access-to-resources.md)
