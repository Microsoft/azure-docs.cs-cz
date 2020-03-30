---
title: Vytvoření virtuální sítě – rychlý start – portál Azure
titleSuffix: Azure Virtual Network
description: 'Úvodní příručka: Vytvořte virtuální síť na webu Azure Portal. Tyto sítě umožňují prostředkům Azure, jako jsou virtuální počítače, bezpečně komunikovat mezi sebou navzájem a přes internet.'
author: KumudD
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2020
ms.author: kumud
ms.openlocfilehash: 1a1593566b8bdb72f322d64c1ee99c7018f49329
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240069"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Rychlý start: Vytvoření virtuální sítě pomocí webu Azure Portal

V tomto rychlém startu se dozvíte, jak vytvořit virtuální síť pomocí portálu Azure. Nasadíte dva virtuální počítače (virtuální počítače). Dále můžete bezpečně komunikovat mezi virtuálními zařízeními a připojit se k virtuálním kvotivám z internetu. Virtuální síť je základním stavebním kamenem pro vaši privátní síť v Azure. Umožňuje prostředkům Azure, jako jsou virtuální počítače, bezpečně komunikovat mezi sebou navzájem a s internetem.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [portálu Azure](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. V nabídce webu Azure Portal vyberte **Vytvořit prostředek**. Na webu Azure Marketplace vyberte**Virtuální síť** **ová síť** > .

1. V **možnosti Vytvořit virtuální síť**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte **Vytvořit nový**, zadejte *myResourceGroup*a pak vyberte **OK**. |
    | Name (Název) | Zadejte *myVirtualNetwork*. |
    | Umístění | Vyberte **USA – východ**.|

1. Vyberte **další: Adresy IP**a pro **adresní prostor IPv4**zadejte *hodnotu 10.1.0.0/16*.

1. Vyberte **Přidat podsíť**, zadejte *myVirtualSubnet* pro **název podsítě** a *10.1.0.0/24* pro **rozsah adres podsítě**.

1. Vyberte **Přidat**, pak vyberte **Zkontrolovat + vytvořit**. Zbytek ponechejte jako výchozí a vyberte **Vytvořit**.

1. V **možnosti Vytvořit virtuální síť**vyberte **Vytvořit**.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte ve virtuální síti dva virtuální počítače:

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

1. V nabídce webu Azure Portal vyberte **Vytvořit prostředek**.

1. Na Azure Marketplace vyberte **Compute** > **Windows Server 2019 Datacenter**. Vyberte **Vytvořit**.

1. V **části Vytvořit virtuální počítač – základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **položku myResourceGroup**. Tuto skupinu prostředků jste vytvořili v předchozí části. |
    | **Podrobnosti instance** |  |
    | Název virtuálního počítače | Zadejte *myVm1*. |
    | Region (Oblast) | Vyberte **USA – východ**. |
    | Možnosti dostupnosti | Výchozí hodnota **není vyžadována žádná redundance infrastruktury**. |
    | Image | Výchozí nastavení **datového centra Windows Server 2019**. |
    | Velikost | Výchozí standardní **DS1 v2**. |
    | **Účet správce** |  |
    | Uživatelské jméno | Zadejte uživatelské jméno podle svého výběru. |
    | Heslo | Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potvrdit heslo | Znovu zadejte heslo. |
    | **Pravidla portů pro příchozí provoz** |  |
    | Veřejné příchozí porty | Vyberte **Povolit vybrané porty**. |
    | Výběr příchozích portů | Zadejte *HTTP (80)* a *RDP (3389).* |
    | **Ušetřete peníze** |  |
    | Máte už licenci na Windows? | Výchozí hodnota **je ne**. |

1. Vyberte **další: Disky**.

1. V **části Vytvoření virtuálního počítače – disky**ponechte výchozí hodnoty a vyberte **Další: Síť**.

1. V **panelu Vytvořit virtuální počítač – Vytváření sítí**vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Virtuální síť | Výchozí hodnota **myVirtualNetwork**. |
    | Podsíť | Výchozí hodnota **myVirtualSubnet (10.1.0.0/24)**. |
    | Veřejná IP adresa | Výchozí **hodnota (nové) myVm-ip**. |
    | Skupina zabezpečení sítě NIC | Výchozí **základní**. |
    | Veřejné příchozí porty | Výchozí **nastavení: Povolit vybrané porty**. |
    | Výběr příchozích portů | Výchozí hodnota **pro PROTOKOLY HTTP** a **RDP**.

1. Vyberte **další: Správa**.

1. V **části Vytvořit virtuální počítač – správa**vyberte v části Účet úložiště **Diagnostika** **možnost Vytvořit nový**.

1. V **možnosti Vytvořit účet úložiště**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Zadejte *myvmstorageaccount*. Pokud je tento název přijat, vytvořte jedinečný název.|
    | Account kind (Druh účtu) | Výchozí **úložiště (obecné účely v1)**. |
    | Výkon | Výchozí **standardní**. |
    | Replikace | Výchozí **místně redundantní úložiště (LRS)**. |

1. Vyberte **OK**, pak vyberte **Zkontrolovat + vytvořit**. Přejdete na stránku **Revize + vytvoření,** kde Azure ověřuje vaši konfiguraci.

1. Když se zobrazí zpráva **Ověření předáno,** vyberte **vytvořit**.

### <a name="create-the-second-vm"></a>Vytvoření druhého virtuálního počítače

Opakováním postupu v předchozí části vytvořte jiný virtuální počítač.

> [!IMPORTANT]
> Pro **název virtuálního počítače**zadejte *myVm2*.
>
> Pro **účet úložiště diagnostiky**, ujistěte se, že vyberete **myvmstorageaccount**, namísto vytvoření jednoho.

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

Po vytvoření *myVm1*se připojte k internetu.

1. Na webu Azure Portal vyhledejte a vyberte *myVm1*.

1. Vyberte **připojit**, potom **rdp**.

    ![Připojení k virtuálnímu počítači](./media/quick-create-portal/connect-to-virtual-machine.png)

    Otevře se stránka **Připojit.**

1. Vyberte **stáhnout soubor RDP**. Azure vytvoří soubor rdp (Remote Desktop Protocol *)* a stáhne ho do počítače.

1. Otevřete soubor RDP. Pokud se zobrazí výzva, vyberte **Připojit**.

1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

    > [!NOTE]
    > Možná budete muset vybrat **Další volby:** > K zadání pověření, která jste zadali při vytváření virtuálního vztahu,**můžete zadat jiný účet**.

1. Vyberte **OK**.

1. Při přihlášení se může zobrazit upozornění na certifikát. Pokud se zobrazí upozornění na certifikát, vyberte **ano** nebo **pokračovat**.

1. Jakmile se zobrazí plocha virtuálního počítače, minimalizujte ji a vraťte se na místní plochu.

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

1. Na vzdálené ploše *myVm1*otevřete prostředí PowerShell.

1. Zadejte `ping myVm2`.

    Zobrazí se zpráva podobná tomuto výstupu:

    ```output
    Pinging myVm2.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.clouda
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.1.0.5:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    Selže, `ping` protože `ping` používá protokol ICMP (Internet Control Message Protocol). Ve výchozím nastavení není icmp povoleno prostřednictvím brány firewall systému Windows.

1. Chcete-li povolit *příkaz myVm2* ping *myVm1* v pozdějším kroku, zadejte tento příkaz:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Tento příkaz umožňuje příchozí protokolu ICMP bránou firewall systému Windows:

1. Ukončete připojení ke vzdálené ploše virtuálního počítače *myVm1*.

1. Zopakujte kroky uvedené v části [Připojení k virtuálnímu počítači z internetu](#connect-to-a-vm-from-the-internet), ale připojte se k virtuálnímu počítači *myVm2*.

1. Na příkazovém řádku zadejte `ping myvm1`.

    Dostanete zpět něco jako tato zpráva:

    ```output
    Pinging myVm1.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 1ms, Average = 0ms
    ```

    Obdržíte odpovědi z *myVm1*, protože jste povolili ICMP přes bránu firewall systému Windows na *myVm1* VM v kroku 3.

1. Ukončete připojení ke vzdálené ploše virtuálního počítače *myVm2*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V tomto rychlém startu jste vytvořili výchozí virtuální síť a dva virtuální počítače. Připojili jste se k jednomu virtuálnímu virtuálnímu síti z internetu a bezpečně komunikovali mezi dvěma virtuálními aplikacemi.

Až budete hotovi s virtuální sítí a virtuálními počítači, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Vyhledejte a vyberte *myResourceGroup*.

1. Vyberte **Odstranit skupinu prostředků**.

1. Zadejte *myResourceGroup* pro **typ názvu skupiny prostředků** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Další informace o nastavení virtuální sítě najdete v [tématu Vytvoření, změna nebo odstranění virtuální sítě](manage-virtual-network.md).

Ve výchozím nastavení Azure umožňuje zabezpečenou komunikaci mezi virtuálními počítači. Azure povoluje jenom příchozí připojení ke vzdálené ploše k virtuálním počítačům s Windows z internetu. Další informace o typech síťové komunikace virtuálních počítačů najdete v [tématu Filtrování síťového provozu](tutorial-filter-network-traffic.md).
