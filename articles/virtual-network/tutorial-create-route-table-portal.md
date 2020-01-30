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
ms.date: 01/22/2019
ms.author: kumud
ms.openlocfilehash: 96b6788e48b845ef7f0add11767eb36b47cac36b
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775281"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Kurz: Směrování síťového provozu s využitím směrovací tabulky pomocí webu Azure Portal

Azure ve výchozím nastavení směruje provoz mezi všemi podsítěmi v rámci virtuální sítě. Můžete vytvořit vlastní trasy a přepsat tak výchozí směrování Azure. Možnost vytvářet vlastní trasy je užitečná například v případě, že chcete směrovat provoz mezi podsítěmi přes síťové virtuální zařízení. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření směrovací tabulky
> * Vytvoření trasy
> * Vytvoření virtuální sítě s několika podsítěmi
> * Přidružení směrovací tabulky k podsíti
> * Vytvoření síťového virtuálního zařízení, které směruje provoz
> * Nasazení virtuálních počítačů do různých podsítí
> * Směrování provozu z jedné podsítě do jiné přes síťové virtuální zařízení

Pokud budete chtít, můžete tento kurz dokončit pomocí [Azure CLI](tutorial-create-route-table-cli.md) nebo [Azure PowerShell](tutorial-create-route-table-powershell.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na web [Azure Portal](https://portal.azure.com).

## <a name="create-a-route-table"></a>Vytvoření směrovací tabulky

1. V nabídce Azure Portal vyberte **vytvořit prostředek**.
2. Do vyhledávacího pole zadejte *směrovací tabulku*. Pokud se ve výsledcích hledání zobrazí **tabulka směrování** , vyberte ji.
3. Na stránce **směrovací tabulka** vyberte **vytvořit**.
4. V **tabulce vytvořit směrovací tabulku**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Zadejte *myRouteTablePublic*. |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **vytvořit novou**, zadejte *MyResourceGroup*a vyberte *OK*. |
    | Umístění | Vyberte **USA – východ**.
    | Šíření tras Virtual Network brány | Ponechte výchozí **povolenou**možnost. |
5. Vyberte **Vytvořit**.

## <a name="create-a-route"></a>Vytvoření trasy

1. Na panelu hledání na portálu zadejte *myRouteTablePublic*.

1. Jakmile se ve výsledcích hledání zobrazí směrovací tabulka **myRouteTablePublic**, vyberte ji.

1. V **myRouteTablePublic** v části **nastavení**vyberte **trasy** >  **+ Přidat**.

    ![Přidání trasy](./media/tutorial-create-route-table-portal/add-route.png)

1. Do **Přidat trasu**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název trasy | Zadejte *ToPrivateSubnet*. |
    | Předpona adresy | Zadejte *10.0.1.0/24*. |
    | Typ dalšího segmentu | Vyberte **Virtuální zařízení**. |
    | Adresa dalšího segmentu | Zadejte *10.0.2.4*. |

1. Vyberte **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Přidružení směrovací tabulky k podsíti

Předtím, než budete moci přidružit směrovací tabulku k podsíti, je nutné vytvořit virtuální síť a podsíť.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. V levé horní části obrazovky vyberte **vytvořit prostředek** > **síť** > **virtuální síť**.

1. V nástroji **vytvořit virtuální síť**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Enter *myVirtualNetwork*. |
    | Adresní prostor | Zadejte *10.0.0.0/16*. |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte ***Vybrat existující*** > **myResourceGroup**. |
    | Umístění | Ponechte výchozí **východní USA**. |
    | Název podsítě | Zadejte *Public*. |
    | Podsíť – Rozsah adres | Zadejte *10.0.0.0/24*. |

1. Ponechte zbytek výchozích hodnot a vyberte **vytvořit**.

### <a name="add-subnets-to-the-virtual-network"></a>Přidání podsítí do virtuální sítě

1. Na panelu hledání na portálu zadejte *myVirtualNetwork*.

1. Jakmile se ve výsledcích hledání zobrazí virtuální síť **myVirtualNetwork**, vyberte ji.

1. V **myVirtualNetwork**v části **nastavení**vyberte **podsítě** >  **+ podsíť**.

    ![Přidání podsítě](./media/tutorial-create-route-table-portal/add-subnet.png)

1. Do **Přidat podsíť**zadejte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Zadejte *Private*. |
    | Adresní prostor | Zadejte *10.0.1.0/24*. |

1. Pro ostatní nastavení nechte zvolené výchozí hodnoty a vyberte **OK**.

1. Vyberte **+ podsíť** znovu. Tentokrát zadejte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Zadejte *DMZ*. |
    | Adresní prostor | Zadejte *10.0.2.0/24*. |

1. Jako poslední ponechte zbytek výchozích hodnot a vyberte **OK**.

    Azure zobrazuje tři podsítě: **Public**, **Private**a **DMZ**.

### <a name="associate-myroutetablepublic-to-your-public-subnet"></a>Přidružte myRouteTablePublic k vaší veřejné podsíti.

1. Vyberte možnost **veřejné**.

1. V seznamu **veřejné**vyberte možnost **směrovací tabulka** > **MyRouteTablePublic** > **Uložit**.

    ![Přidružení směrovací tabulky](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="create-an-nva"></a>Vytvoření síťového virtuálního zařízení

Síťová virtuální zařízení jsou virtuální počítače, které vám pomůžou s funkcemi sítě, jako je například směrování a optimalizace brány firewall. Pokud chcete, můžete vybrat jiný operační systém. V tomto kurzu se předpokládá, že používáte **Windows Server 2016 Datacenter**.

1. V levé horní části obrazovky vyberte **vytvořit prostředek** > **COMPUTE** > **Windows Server 2016 Datacenter**.

1. V nástroji **vytvořit virtuální počítač základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. |
    | **PODROBNOSTI INSTANCE** |  |
    | Název virtuálního počítače | Enter *myVmNva*. |
    | Region (Oblast) | Vyberte **USA – východ**. |
    | Možnosti dostupnosti | Nechte výchozí nastavení **bez nutnosti redundance infrastruktury**. |
    | Obrázek | Ponechte výchozí **Windows Server 2016 Datacenter**. |
    | Velikost | Ponechte výchozí hodnotu **Standard DS1 v2**. |
    | **ÚČET SPRÁVCE** |  |
    | Uživatelské jméno | Zadejte libovolné uživatelské jméno. |
    | Heslo | Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potvrdit heslo | Zadejte znovu heslo. |
    | **PRAVIDLA PORTŮ PRO PŘÍCHOZÍ SPOJENÍ** |  |
    | Veřejné příchozí porty | Vyberte **žádné**.
    | **ÚSPORA PENĚZ** |  |
    | Máte už licenci na Windows? | Ponechte výchozí hodnotu **ne**. |

1. Vyberte **Další: disky**.

1. V části **vytvořit virtuální počítač – disky**vyberte nastavení, která jsou vhodná pro vaše potřeby.

1. Vyberte **Další: sítě**.

1. V nástroji **vytvořit virtuální počítač – síť**vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Virtuální síť | Ponechte výchozí **myVirtualNetwork**. |
    | Podsíť | Vyberte **DMZ (10.0.2.0/24)** . |
    | Veřejná IP adresa | Vyberte **žádné**. Nepotřebujete veřejnou IP adresu. Virtuální počítač se nebude připojovat přes Internet.|

1. Ponechte zbytek výchozích hodnot a vyberte **Další: Správa**.

1. V **nástroji vytvořit virtuální počítač**pro **účet úložiště pro diagnostiku**vyberte **vytvořit novou**.

1. V nástroji **vytvořit účet úložiště**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Zadejte *mynvastorageaccount*. |
    | Account kind (Druh účtu) | Ponechte výchozí **úložiště (pro obecné účely V1)** . |
    | Výkon | Ponechte výchozí **Standard**. |
    | Replikace | Ponechte výchozí **místně redundantní úložiště (LRS)** .

1. Vyberte **OK**.

1. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvořit** a Azure ověří vaši konfiguraci.

1. Po zobrazení, že bylo **úspěšně provedeno ověření**, vyberte **vytvořit**.

    Vytvoření virtuálního počítače trvá několik minut. Nedělejte si pokračovat, dokud Azure nedokončí vytváření virtuálního počítače. Na stránce **vaše nasazení se** zobrazí podrobnosti o nasazení.

1. Až bude váš virtuální počítač připravený, vyberte **Přejít k prostředku**.

## <a name="turn-on-ip-forwarding"></a>Zapnout předávání IP

Zapněte předávání IP adres pro *myVmNva*. Když Azure posílá síťový provoz do *myVmNva*, pokud je provoz určený pro jinou IP adresu, předávání IP adres odešle do správného umístění.

1. V **myVmNva**v části **Nastavení**vyberte **sítě**.

1. Vyberte **myvmnva123**. Toto je síťové rozhraní, které Azure vytvořil pro váš virtuální počítač. Bude mít řetězec čísel, který bude pro vás jedinečný.

    ![Sítě virtuálních počítačů](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. V části **Nastavení**vyberte **Konfigurace protokolu IP**.

1. V **myvmnva123 – konfigurace protokolu IP**pro **předávání IP**vyberte **Povolit** a pak vyberte **Uložit**.

    ![Povolení předávání IP](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Vytváření veřejných a privátních virtuálních počítačů

Vytvořte veřejný virtuální počítač a privátní virtuální počítač ve virtuální síti. Později je budete používat k zobrazení, že Azure směruje provoz *veřejné* podsítě do *privátní* podsítě prostřednictvím rozhraní síťové virtuální zařízení.

Proveďte kroky 1-12 z části [Vytvoření síťové virtuální zařízení](#create-an-nva). Použijte většinu stejných nastavení. Tyto hodnoty jsou ty, které se musí lišit:

| Nastavení | Hodnota |
| ------- | ----- |
| **VEŘEJNÝ VIRTUÁLNÍ POČÍTAČ** | |
| PRÁCE |  |
| Název virtuálního počítače | Zadejte *myVmPublic*. |
| SÍTĚ | |
| Podsíť | Vyberte **veřejné (10.0.0.0/24)** . |
| Veřejná IP adresa | Přijměte výchozí hodnotu. |
| Veřejné příchozí porty | Vyberte možnost **Povolení vybraných portů**. |
| Vyberte příchozí porty | Vyberte **http** a **RDP**. |
| SPRÁVU | |
| Účet úložiště diagnostiky | Ponechte výchozí **mynvastorageaccount**. |
| **PRIVÁTNÍ VIRTUÁLNÍ POČÍTAČ** | |
| PRÁCE |  |
| Název virtuálního počítače | Zadejte *myVmPrivate*. |
| SÍTĚ | |
| Podsíť | Vyberte **Private (10.0.1.0/24)** . |
| Veřejná IP adresa | Přijměte výchozí hodnotu. |
| Veřejné příchozí porty | Vyberte možnost **Povolení vybraných portů**. |
| Vyberte příchozí porty | Vyberte **http** a **RDP**. |
| SPRÁVU | |
| Účet úložiště diagnostiky | Ponechte výchozí **mynvastorageaccount**. |

Zatímco Azure vytváří virtuální počítač *myVmPublic*, můžete vytvořit virtuální počítač *myVmPrivate*. Nepokračujte ve zbývajících krocích, dokud Azure nedokončí vytváření obou virtuálních počítačů.

## <a name="route-traffic-through-an-nva"></a>Směrování provozu přes síťové virtuální zařízení

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Přihlášení k myVmPrivate prostřednictvím vzdálené plochy

1. Na panelu hledání na portálu zadejte *myVmPrivate*.

1. Jakmile se ve výsledcích hledání zobrazí virtuální počítač **myVmPrivate**, vyberte ho.

1. Vyberte **připojit** a vytvořte připojení ke vzdálené ploše virtuálního počítače s *myVmPrivate* .

1. V **připojení k virtuálnímu počítači**vyberte **Stáhnout soubor RDP**. Azure vytvoří soubor protokol RDP (Remote Desktop Protocol) ( *. RDP*) a stáhne ho do vašeho počítače.

1. Otevřete stažený soubor *. RDP* .

    1. Pokud se zobrazí výzva, vyberte **Připojit**.

    1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření privátního virtuálního počítače.

    1. Možná budete muset vybrat **Další volby** > **použít jiný účet**a použít přihlašovací údaje privátního virtuálního počítače.

1. Vyberte **OK**.

    Během procesu přihlašování se může zobrazit upozornění certifikátu.

1. Pokud se chcete připojit k virtuálnímu počítači, vyberte **Ano** .

### <a name="enable-icmp-through-the-windows-firewall"></a>Povolení protokolu ICMP přes bránu Windows Firewall

V pozdějším kroku použijete nástroj Trace Route k otestování směrování. Trasování tras používá protokol ICMP (Internet Control Message Protocol), který ve výchozím nastavení zakáže bránu Windows Firewall. Povolte protokol ICMP přes bránu Windows Firewall.

1. Ve vzdálené ploše *myVmPrivate*otevřete PowerShell.

1. Zadejte tento příkaz:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Trasu trasování používáte k testování směrování v tomto kurzu. V produkčních prostředích nedoporučujeme povolit protokol ICMP prostřednictvím brány Windows Firewall.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Zapnutí předávání IP v rámci myVmNva

[Zapnuli jste předávání IP](#turn-on-ip-forwarding) pro síťové rozhraní virtuálního počítače pomocí Azure. Operační systém virtuálního počítače taky musí přesílat síťový provoz. Pomocí těchto příkazů zapněte předávání IP pro operační systém virtuálního počítače s *myVmNva* .

1. Z příkazového řádku na virtuálním počítači s *myVmPrivate* otevřete na virtuálním počítači *myVmNva* vzdálenou plochu:

    ```cmd
    mstsc /v:myvmnva
    ```

1. V prostředí PowerShell na *myVmNva*zadejte tento příkaz, abyste zapnuli předávání IP:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Restartujte virtuální počítač *myVmNva* . Na hlavním panelu vyberte **tlačítko Start** > tlačítko **napájení**, **jiné (plánované)**  > **pokračovat**.

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

    Můžete vidět, že se první segment směrování 10.0.2.4. Je to NVA's privátní IP adresa. Druhým směrováním je privátní IP adresa virtuálního počítače *myVmPrivate* : 10.0.1.4. Dříve jste přidali trasu do tabulky směrování *myRouteTablePublic* a přidružíte ji k *veřejné* podsíti. V důsledku toho Azure odeslal přenos prostřednictvím síťové virtuální zařízení a nepřímo do *privátní* podsítě.

1. Ukončete relaci vzdálené plochy k virtuálnímu počítači *myVmPublic*. Stále zůstanete připojeni k virtuální síti *myVmPrivate*.

1. Z příkazového řádku na virtuálním počítači *myVmPrivate* zadejte tento příkaz:

    ```cmd
    tracert myVmPublic
    ```

    Testuje směrování síťového provozu z virtuálního počítače *myVmPrivate* do virtuálního počítače *myVmPublic* . Odpověď je podobná jako v tomto příkladu:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Azure směruje provoz přímo z virtuálního počítače *myVmPrivate* do virtuálního počítače *myVmPublic* . Azure ve výchozím nastavení směruje provoz přímo mezi podsítěmi.

1. Ukončete relaci vzdálené plochy k virtuálnímu počítači *myVmPrivate*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Na panelu hledání na portálu zadejte *myResourceGroup*.

1. Jakmile se ve výsledcích hledání zobrazí skupina prostředků **myResourceGroup**, vyberte ji.

1. Vyberte **Odstranit skupinu prostředků**.

1. V části **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ** zadejte *myResourceGroup* a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili směrovací tabulku a přidružili jste ji k podsíti. Vytvořili jste jednoduché síťové virtuální zařízení, které směrovalo provoz z veřejné podsítě do privátní podsítě. Teď, když už víte, jak to udělat, můžete nasadit jinou předem nakonfigurovanou síťová virtuální zařízení z [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Provádějí mnoho síťových funkcí, které vám pomohou. Další informace o směrování najdete v tématech [Přehled směrování](virtual-networks-udr-overview.md) a [Správa směrovací tabulky](manage-route-table.md).

I když můžete nasadit mnoho prostředků Azure v rámci virtuální sítě, Azure nemůže nasadit prostředky pro některé služby PaaS do virtuální sítě. Je možné omezit přístup k prostředkům některých služeb Azure PaaS. Omezení musí být jenom provozu z podsítě virtuální sítě, i když. Informace o omezení síťového přístupu k prostředkům Azure PaaS najdete v následujícím kurzu.

> [!div class="nextstepaction"]
> [Omezení síťového přístupu k prostředkům PaaS](tutorial-restrict-network-access-to-resources.md)
