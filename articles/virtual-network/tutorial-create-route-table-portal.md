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
ms.date: 12/12/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: fbbc624bbc3d20a70a54c50296f5b74634002a67
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409067"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Kurz: Směrování síťového provozu s využitím směrovací tabulky pomocí webu Azure portal

Azure směruje provoz mezi všemi podsítěmi v rámci virtuální sítě, ve výchozím nastavení. Můžete vytvořit vlastní trasy a přepsat tak výchozí směrování Azure. Možnost vytvářet vlastní trasy je užitečná například v případě, že chcete směrovat provoz mezi podsítěmi přes síťové virtuální zařízení. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření směrovací tabulky
> * Vytvoření trasy
> * Vytvoření virtuální sítě s několika podsítěmi
> * Přidružení směrovací tabulky k podsíti
> * Vytvoření síťového virtuálního zařízení, které směruje provoz
> * Nasazení virtuálních počítačů do různých podsítí
> * Směrování provozu z jedné podsítě do jiné přes síťové virtuální zařízení

Pokud dáváte přednost, můžete dokončit tento kurz pomocí [rozhraní příkazového řádku Azure](tutorial-create-route-table-cli.md) nebo [prostředí Azure PowerShell](tutorial-create-route-table-powershell.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="create-a-route-table"></a>Vytvoření směrovací tabulky

1. Na straně levého horního rohu obrazovky vyberte **vytvořit prostředek** > **sítě** > **směrovací tabulka**.

1. V **vytvořit směrovací tabulku**, zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte *myRouteTablePublic*. |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **vytvořit nový**, zadejte *myResourceGroup*a vyberte *OK*. |
    | Umístění | Ponechte výchozí nastavení **USA – východ**.
    | Šíření tras protokolu BGP | Ponechte výchozí nastavení **povoleno**. |

1. Vyberte **Vytvořit**.

## <a name="create-a-route"></a>Vytvoření trasy

1. Do panelu hledání na portálu, zadejte *myRouteTablePublic*.

1. Jakmile se ve výsledcích hledání zobrazí směrovací tabulka **myRouteTablePublic**, vyberte ji.

1. V **myRouteTablePublic** pod **nastavení**vyberte **trasy** > **+ přidat**.

    ![Přidání trasy](./media/tutorial-create-route-table-portal/add-route.png)

1. V **trasy přidat**, zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název trasy | Zadejte *ToPrivateSubnet*. |
    | Předpona adresy | Zadejte *10.0.1.0/24*. |
    | Typ dalšího segmentu | Vyberte **Virtuální zařízení**. |
    | Adresa dalšího segmentu | Zadejte *10.0.2.4*. |

1. Vyberte **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Přidružení směrovací tabulky k podsíti

Než budete moct přidružit směrovací tabulky k podsíti, budete muset vytvořit virtuální síť a podsíť.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. Na straně levého horního rohu obrazovky vyberte **vytvořit prostředek** > **sítě** > **virtuální síť**.

1. V **vytvořit virtuální síť**, zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte *myVirtualNetwork*. |
    | Adresní prostor | Zadejte *10.0.0.0/16*. |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte ***vybrat existující*** > **myResourceGroup**. |
    | Umístění | Ponechte výchozí nastavení **USA – východ**. |
    | Podsíť - Name | Zadejte *veřejné*. |
    | Podsíť – Rozsah adres | Zadejte *10.0.0.0/24*. |

1. Ponechejte zbývající výchozí hodnoty a vyberte **vytvořit**.

### <a name="add-subnets-to-the-virtual-network"></a>Přidání podsítě do virtuální sítě

1. Do panelu hledání na portálu, zadejte *myVirtualNetwork*.

1. Jakmile se ve výsledcích hledání zobrazí virtuální síť **myVirtualNetwork**, vyberte ji.

1. V **myVirtualNetwork**v části **nastavení**vyberte **podsítě** > **+ podsíť**.

    ![Přidání podsítě](./media/tutorial-create-route-table-portal/add-subnet.png)

1. V **přidat podsíť**, zadejte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte *privátní*. |
    | Adresní prostor | Zadejte *10.0.1.0/24*. |

1. Pro ostatní nastavení nechte zvolené výchozí hodnoty a vyberte **OK**.

1. Vyberte **+ podsíť** znovu. Tentokrát, zadejte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte *DMZ*. |
    | Adresní prostor | Zadejte *10.0.2.0/24*. |

1. Čas poslední, jako jsou, ponechejte zbývající výchozí hodnoty a vyberte **OK**.

    Azure ukazuje tři podsítě: **Veřejné**, **privátní**, a **DMZ**.

### <a name="associate-myroutetablepublic-to-your-public-subnet"></a>Přidružit myRouteTablePublic veřejné podsítě

1. Vyberte **veřejné**.

1. V **veřejné**vyberte **směrovací tabulka** > **MyRouteTablePublic** > **Uložit**.

    ![Přidružení směrovací tabulky](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="create-an-nva"></a>Vytvoření síťového virtuálního zařízení

Síťová virtuální zařízení se softwarem Oracle, které pomáhají s síťové funkce, jako je optimalizace směrování a firewallu. Pokud chcete, můžete vybrat jiný operační systém. Tento kurz předpokládá, že používáte **systému Windows Server 2016 Datacenter**.

1. Na straně levého horního rohu obrazovky vyberte **vytvořit prostředek** > **Compute** > **systému Windows Server 2016 Datacenter**.

1. V **vytvořit virtuální počítač – základní informace o**, zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. |
    | **PODROBNOSTI O INSTANCI** |  |
    | Název virtuálního počítače | Zadejte *myVmNva*. |
    | Oblast | Vyberte **USA – východ**. |
    | Možnosti dostupnosti | Ponechte výchozí nastavení **žádné redundance infrastruktury požadované**. |
    | Image | Ponechte výchozí nastavení **systému Windows Server 2016 Datacenter**. |
    | Velikost | Ponechte výchozí nastavení **v2 Standard DS1**. |
    | **ÚČET SPRÁVCE** |  |
    | Uživatelské jméno | Zadejte libovolné uživatelské jméno. |
    | Heslo | Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potvrzení hesla | Zadejte heslo znovu. |
    | **PŘÍCHOZÍ PRAVIDLA PORTŮ** |  |
    | Veřejné příchozí porty | Ponechte výchozí nastavení **žádný**.
    | **UŠETŘETE PENÍZE** |  |
    | Máte už licenci na Windows? | Ponechte výchozí nastavení **ne**. |

1. Vyberte **Další: Disky**.

1. V **vytvoření virtuálního počítače – disky**, vyberte nastavení, které vyhovují vašim potřebám.

1. Vyberte **Další: Sítě**.

1. V **vytvoření virtuálního počítače – sítě**, vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Virtuální síť | Ponechte výchozí nastavení **myVirtualNetwork**. |
    | Podsíť | Vyberte **DMZ (10.0.2.0/24)**. |
    | Veřejná IP adresa | Vyberte **žádný**. Není nutné veřejnou IP adresu. Virtuální počítač se nebudou připojovat přes internet.|

1. Ponechejte zbývající výchozí hodnoty a vyberte **Další: Správa**.

1. V **vytvoření virtuálního počítače – Správa**, pro **účet úložiště diagnostiky**vyberte **vytvořit nový**.

1. V **vytvořit účet úložiště**, zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte *mynvastorageaccount*. |
    | Account kind (Druh účtu) | Ponechte výchozí nastavení **úložiště (verze 1 pro obecné účely)**. |
    | Výkon | Ponechte výchozí nastavení **standardní**. |
    | Replikace | Ponechte výchozí nastavení **místně redundantní úložiště (LRS)**.

1. Vyberte **OK**.

1. Vyberte **Zkontrolovat a vytvořit**. Přejdete tak **revize + vytvořit** stránky a Azure ověří vaši konfiguraci.

1. Pokud uvidíte, že **ověření proběhlo úspěšně.** vyberte **vytvořit**.

    Vytvoření virtuálního počítače trvá několik minut. Není dál pokračovat, dokud Azure nedokončí vytváření virtuálního počítače. **Nasazení probíhá** stránce se zobrazí podrobnosti o nasazení.

1. Pokud je váš virtuální počítač připravený, vyberte **přejít k prostředku**.

## <a name="turn-on-ip-forwarding"></a>Zapnout předávání IP

Zapnout pro předávání IP *myVmNva*. Když Azure odesílá síťový provoz do *myVmNva*, pokud je provoz určený pro jinou IP adresu, předávání IP bude odesílat provoz do správného umístění.

1. Na **myVmNva**v části **nastavení**vyberte **sítě**.

1. Vyberte **myvmnva123**. To je síťové rozhraní, které Azure vytvořilo pro váš virtuální počítač. Bude mít řetězce čísel pro zajištění jeho jedinečnosti za vás.

    ![Sítě virtuálních počítačů](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. V části **nastavení**vyberte **konfigurací protokolu IP**.

1. Na **myvmnva123 – konfigurace protokolu IP**, pro **předávání IP**vyberte **povoleno** a pak vyberte **Uložit**.

    ![Povolení předávání IP](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Vytvoření veřejných a privátních virtuálních počítačů

Vytvoření veřejné virtuální počítač a privátní virtuální počítač ve virtuální síti. Později je budete používat zobrazíte, že Azure směruje *veřejné* provoz podsítě *privátní* podsítě přes síťové virtuální zařízení.

Proveďte kroky 1 až 12 z [vytvoření síťového virtuálního zařízení](#create-an-nva). Používat většinu stejné nastavení. Tyto hodnoty jsou ty, které musí být jiné:

| Nastavení | Hodnota |
| ------- | ----- |
| **VEŘEJNÉ VIRTUÁLNÍ POČÍTAČ** | |
| ZÁKLADY |  |
| Název virtuálního počítače | Zadejte *myVmPublic*. |
| SÍTĚ | |
| Podsíť | Vyberte **veřejné (10.0.0.0/24)**. |
| Veřejná IP adresa | Přijměte výchozí nastavení. |
| Veřejné příchozí porty | Vyberte **povolit vybrané porty**. |
| Vyberte příchozí porty | Vyberte **HTTP** a **RDP**. |
| SPRÁVA | |
| Účet úložiště diagnostiky | Ponechte výchozí nastavení **mynvastorageaccount**. |
| **PRIVÁTNÍ VIRTUÁLNÍ POČÍTAČ** | |
| ZÁKLADY |  |
| Název virtuálního počítače | Zadejte *myVmPrivate*. |
| SÍTĚ | |
| Podsíť | Vyberte **privátní (10.0.1.0/24)**. |
| Veřejná IP adresa | Přijměte výchozí nastavení. |
| Veřejné příchozí porty | Vyberte **povolit vybrané porty**. |
| Vyberte příchozí porty | Vyberte **HTTP** a **RDP**. |
| SPRÁVA | |
| Účet úložiště diagnostiky | Ponechte výchozí nastavení **mynvastorageaccount**. |

Zatímco Azure vytváří virtuální počítač *myVmPublic*, můžete vytvořit virtuální počítač *myVmPrivate*. Nepokračujte zbývající kroky, dokud Azure nedokončí vytváření obou virtuálních počítačů.

## <a name="route-traffic-through-an-nva"></a>Směrování provozu přes síťové virtuální zařízení

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Přihlaste se k myVmPrivate přes vzdálenou plochu

1. Do panelu hledání na portálu, zadejte *myVmPrivate*.

1. Jakmile se ve výsledcích hledání zobrazí virtuální počítač **myVmPrivate**, vyberte ho.

1. Vyberte **připojit** k vytvoření připojení ke vzdálené ploše *myVmPrivate* virtuálního počítače.

1. V **připojit k virtuálnímu počítači**vyberte **stáhnout soubor RDP**. Azure vytvoří Remote Desktop Protocol (*RDP*) soubor a stáhne do vašeho počítače.

1. Otevřete na stažený *RDP* souboru.

    1. Pokud se zobrazí výzva, vyberte **Připojit**.

    1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače privátní.

    1. Budete muset vybrat **víc možností** > **použít jiný účet**, pomocí přihlašovacích údajů privátní virtuální počítač.

1. Vyberte **OK**.

    V procesu může zobrazit upozornění certifikátu během přihlašování.

1. Vyberte **Ano** pro připojení k virtuálnímu počítači.

### <a name="enable-icpm-through-the-windows-firewall"></a>Povolit ICPM přes bránu Windows firewall

V pozdějším kroku použijete nástroj pro trasování tras k otestování směrování. Trasování tras využívá ovládací prvek zpráva ICMP (Internet Protocol), čímž se odmítne brána Windows Firewall ve výchozím nastavení. Povolte průchod protokolu ICMP bránou Windows firewall.

1. Ve vzdálené ploše *myVmPrivate*, otevřete prostředí PowerShell.

1. Zadejte tento příkaz:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    K otestování směrování v tomto kurzu používáte trasování tras. Pro produkční prostředí nedoporučujeme, povolení průchodu protokolu ICMP bránou Windows Firewall.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Zapnout předávání IP v rámci myVmNva

Můžete [zapnuté předávání IP](#turn-on-ip-forwarding) pro síťové rozhraní Virtuálního počítače pomocí Azure. Operační systém Virtuálního počítače má také směrovat síťový provoz. Zapnout pro předávání IP *myVmNva* virtuální počítač operační systém pomocí následujících příkazů.

1. Z příkazového řádku na *myVmPrivate* virtuálního počítače, otevřete relaci vzdálené plochy k *myVmNva* virtuálního počítače:

    ```cmd
    mstsc /v:myvmnva
    ```

1. V Powershellu na *myVmNva*, zadejte tento příkaz Zapnout předávání IP:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Restartujte *myVmNva* virtuálního počítače. Na hlavním panelu vyberte **tlačítko Start** > **tlačítko napájení**, **jiné (plánované)** > **pokračovat**.

    Který se také odpojí relace vzdálené plochy.

1. Po *myVmNva* restartování virtuálního počítače, vytvořte relaci vzdálené plochy k *myVmPublic* virtuálního počítače. Zatímco jste pořád připojený k *myVmPrivate* virtuálního počítače, otevřete příkazový řádek a spusťte tento příkaz:

    ```cmd
    mstsc /v:myVmPublic
    ```
1. Ve vzdálené ploše *myVmPublic*, otevřete prostředí PowerShell.

1. Povolte průchod protokolu ICMP bránou Windows firewall tak, že zadáte tento příkaz:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>Otestování směrování síťového provozu

Nejprve můžeme otestovat směrování síťového provozu z *myVmPublic* virtuálního počítače *myVmPrivate* virtuálního počítače.

1. V Powershellu na *myVmPublic* virtuálního počítače, zadejte tento příkaz:

    ```powershell
    tracert myVmPrivate
    ```

    Odpověď je podobný tomuto příkladu:

    ```powershell
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4

    Trace complete.
    ```

    Uvidíte, že první segment směrování je 10.0.2.4. Je privátní IP adresa síťového virtuálního zařízení. Druhý segment směrování je privátní IP adresu *myVmPrivate* virtuálního počítače: 10.0.1.4. Dříve jste přidali trasy, která má *myRouteTablePublic* směrovací tabulky a přidruženou k *veřejné* podsítě. V důsledku toho Azure odeslání provozu přes síťové virtuální zařízení a ne přímo na *privátní* podsítě.

1. Ukončete relaci vzdálené plochy k virtuálnímu počítači *myVmPublic*. Stále zůstanete připojeni k virtuální síti *myVmPrivate*.

1. Z příkazového řádku na *myVmPrivate* virtuálního počítače, zadejte tento příkaz:

    ```cmd
    tracert myVmPublic
    ```

    Ověřuje směrování síťového provozu z *myVmPrivate* virtuálního počítače *myVmPublic* virtuálního počítače. Odpověď je podobný tomuto příkladu:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Uvidíte Azure směruje provoz přímo z *myVmPrivate* virtuálního počítače *myVmPublic* virtuálního počítače. Azure ve výchozím nastavení směruje provoz přímo mezi podsítěmi.

1. Ukončete relaci vzdálené plochy k virtuálnímu počítači *myVmPrivate*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Do panelu hledání na portálu, zadejte *myResourceGroup*.

1. Jakmile se ve výsledcích hledání zobrazí skupina prostředků **myResourceGroup**, vyberte ji.

1. Vyberte **Odstranit skupinu prostředků**.

1. V části **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ** zadejte *myResourceGroup* a vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili směrovací tabulku a přidružili jste ji k podsíti. Vytvořili jste jednoduché síťové virtuální zařízení, které směrovalo provoz z veřejné podsítě do privátní podsítě. Teď, když víte, jak to provést, můžete nasadit různé předem nakonfigurovaná síťová virtuální zařízení od [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Provádějí mnoho síťových funkcí, které najdete užitečné. Další informace o směrování najdete v tématech [Přehled směrování](virtual-networks-udr-overview.md) a [Správa směrovací tabulky](manage-route-table.md).

Přestože můžete nasadit řadu prostředků Azure v rámci virtuální sítě, Azure nemůže nasazení prostředků u některých služeb PaaS do virtuální sítě. Je možné omezit přístup k prostředkům některých služeb Azure PaaS. Omezení musí však být provoz z podsítě virtuální sítě. Informace o omezení síťového přístupu k prostředkům Azure PaaS najdete v následujícím kurzu.

> [!div class="nextstepaction"]
> [Omezení síťového přístupu k prostředkům PaaS](tutorial-restrict-network-access-to-resources.md)
