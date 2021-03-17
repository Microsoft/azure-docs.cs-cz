---
title: Vytvoření virtuální sítě – rychlý Start – Azure Portal
titleSuffix: Azure Virtual Network
description: 'Rychlý Start: vytvoření virtuální sítě v Azure Portal. Tyto sítě umožňují zabezpečeně komunikovat s prostředky Azure, jako jsou virtuální počítače, mezi sebou a s internetem.'
author: KumudD
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2020
ms.author: kumud
ms.openlocfilehash: cc3ba3a0519400368e0cbfec7abe2d9bd1731b34
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98217630"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Rychlý start: Vytvoření virtuální sítě pomocí webu Azure Portal

V tomto rychlém startu se dozvíte, jak vytvořit virtuální síť pomocí Azure Portal. Nasadíte dva virtuální počítače (VM). Dále zabezpečeně komunikujete mezi virtuálními počítači a připojením k virtuálním počítačům z Internetu. Virtuální síť je základním stavebním blokem pro vaši privátní síť v Azure. Umožňuje prostředkům Azure, jako jsou virtuální počítače, bezpečně komunikovat mezi sebou a s internetem.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na web [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. V nabídce webu Azure Portal vyberte **Vytvořit prostředek**. Z Azure Marketplace vyberte **síť**  >  **virtuální síť**.

1. V nástroji **vytvořit virtuální síť** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte **vytvořit nový**, zadejte *myResourceGroup* a pak vyberte **OK**. |
    | Name | Zadejte *myVirtualNetwork*. |
    | Umístění | Vyberte **USA – východ**.|

1. Vyberte **Další: IP adresy** a pro **adresní prostor IPv4** zadejte *10.1.0.0/16*.

1. Vyberte **Přidat podsíť** a pak jako **název podsítě** zadejte *myVirtualSubnet* a *10.1.0.0/24* pro **Rozsah adres podsítě**.

1. Vyberte **Přidat** a pak vybrat **zkontrolovat + vytvořit**. Ponechte REST jako výchozí a vyberte **vytvořit**.

1. V nástroji **vytvořit virtuální síť** vyberte **vytvořit**.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte ve virtuální síti dva virtuální počítače:

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

1. V nabídce webu Azure Portal vyberte **Vytvořit prostředek**.

1. V Azure Marketplace vyberte **COMPUTE**  >  **Windows Server 2019 Datacenter**. Vyberte **Vytvořit**.

1. V nástroji **vytvořit virtuální počítač základy** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Tuto skupinu prostředků jste vytvořili v předchozí části. |
    | **Podrobnosti o instancích** |  |
    | Název virtuálního počítače | Zadejte *myVm1*. |
    | Oblast | Vyberte **USA – východ**. |
    | Možnosti dostupnosti | Ve výchozím nastavení **není nutná žádná redundance infrastruktury**. |
    | Image | Ve výchozím nastavení se jedná o **Windows Server 2019 Datacenter**. |
    | Velikost | Výchozí hodnota je **Standard DS1 v2**. |
    | **Účet správce** |  |
    | Uživatelské jméno | Zadejte uživatelské jméno, které si zvolíte. |
    | Heslo | Zadejte libovolné heslo. Heslo musí mít délku aspoň 12 znaků a musí splňovat [definované požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potvrdit heslo | Zadejte znovu heslo. |
    | **Pravidla portů pro příchozí spojení** |  |
    | Veřejné příchozí porty | Vyberte možnost **Povolení vybraných portů**. |
    | Vyberte příchozí porty | Zadejte *http (80)* a *RDP (3389)*. |
    | **Úspora peněz** |  |
    | Máte už licenci na Windows? | Výchozí hodnota je **ne**. |

1. Vyberte **Další: disky**.

1. V části **vytvořit virtuální počítač – disky** ponechte výchozí hodnoty a vyberte **Další: sítě**.

1. V nástroji **vytvořit virtuální počítač – síť** vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Virtuální síť | Výchozí hodnota je **myVirtualNetwork**. |
    | Podsíť | Výchozí hodnota je **myVirtualSubnet (10.1.0.0/24)**. |
    | Veřejná IP adresa | Výchozí hodnota je **(New) myVm-IP**. |
    | Skupina zabezpečení sítě NIC | Výchozí hodnota je **Basic**. |
    | Veřejné příchozí porty | Výchozí **povolí vybrané porty**. |
    | Vyberte příchozí porty | Výchozí hodnota je **http** a **RDP**.

1. Vyberte **Další: Správa**.

1. V **nástroji vytvořit virtuální počítač** pro **účet úložiště pro diagnostiku** vyberte **vytvořit novou**.

1. V nástroji **vytvořit účet úložiště** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte *myvmstorageaccount*. Pokud se tento název povede, vytvořte jedinečný název.|
    | Druh účtu | Výchozí hodnota pro **Storage (pro obecné účely V1)**. |
    | Výkon | Výchozí hodnota je **Standard**. |
    | Replikace | Výchozí pro **místně redundantní úložiště (LRS)**. |

1. Vyberte **OK** a pak vyberte **zkontrolovat + vytvořit**. Budete přesměrováni na stránku **Zkontrolovat a vytvořit**, kde Azure ověří konfiguraci.

1. Jakmile se zobrazí zpráva **Ověření proběhlo úspěšně**, vyberte **Vytvořit**.

### <a name="create-the-second-vm"></a>Vytvoření druhého virtuálního počítače

Zopakováním postupu v předchozí části vytvořte další virtuální počítač.

> [!IMPORTANT]
> Jako **název virtuálního počítače** zadejte *myVm2*.
>
> V případě **účtu úložiště pro diagnostiku** se ujistěte, že jste vybrali možnost **myvmstorageaccount** namísto vytvoření.

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

Po vytvoření *myVm1* se připojte k Internetu.

1. V Azure Portal vyhledejte a vyberte *myVm1*.

1. Vyberte **připojit** a pak **RDP**.

    ![Připojení k virtuálnímu počítači](./media/quick-create-portal/connect-to-virtual-machine.png)

    Otevře se stránka **připojit** .

1. Vyberte **Stáhnout soubor RDP**. Azure vytvoří soubor protokol RDP (Remote Desktop Protocol) (*. RDP*) a stáhne ho do vašeho počítače.

1. Otevřete soubor RDP. Pokud se zobrazí výzva, vyberte **Připojit**.

1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

    > [!NOTE]
    > Možná budete muset vybrat **Další volby**  >  **použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.

1. Vyberte **OK**.

1. Při přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění na certifikát, vyberte **Ano** nebo **Pokračovat**.

1. Jakmile se zobrazí plocha virtuálního počítače, minimalizujte ji tak, aby se vrátila k místnímu počítači.

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

1. Ve vzdálené ploše *myVm1* otevřete PowerShell.

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

    `ping`Služba se nezdařila, protože `ping` používá protokol ICMP (Internet Control Message Protocol). Standardně není ICMP povolený přes bránu Windows Firewall.

1. Pokud chcete, aby se v pozdějším kroku *myVm2* příkaz Test *myVm1* , zadejte tento příkaz:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Tento příkaz umožňuje příchozí protokol ICMP přes bránu Windows Firewall:

1. Ukončete připojení ke vzdálené ploše virtuálního počítače *myVm1*.

1. Zopakujte kroky uvedené v části [Připojení k virtuálnímu počítači z internetu](#connect-to-a-vm-from-the-internet), ale připojte se k virtuálnímu počítači *myVm2*.

1. Na příkazovém řádku zadejte `ping myvm1`.

    Zobrazí se něco jako tato zpráva:

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

    Dostanete odpovědi z *myVm1*, protože jste povolili protokol ICMP prostřednictvím brány Windows Firewall na virtuálním počítači s *myVm1* v kroku 3.

1. Ukončete připojení ke vzdálené ploše virtuálního počítače *myVm2*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V tomto rychlém startu jste vytvořili výchozí virtuální síť a dva virtuální počítače. Připojili jste se k jednomu virtuálnímu počítači z Internetu a bezpečně komunikovali mezi těmito dvěma virtuálními počítači.

Až budete s použitím virtuální sítě a virtuálních počítačů hotovi, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Vyhledejte a vyberte *myResourceGroup*.

1. Vyberte **Odstranit skupinu prostředků**.

1. Zadejte *myResourceGroup* pro **typ název skupiny prostředků** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Další informace o nastavení virtuální sítě najdete v tématu [Vytvoření, změna nebo odstranění virtuální sítě](manage-virtual-network.md).

Ve výchozím nastavení umožňuje Azure zabezpečenou komunikaci mezi virtuálními počítači. Azure umožňuje příchozí připojení vzdálené plochy k virtuálním počítačům s Windows z Internetu. Další informace o typech komunikace sítě virtuálních počítačů najdete v tématu [filtrování síťového provozu](tutorial-filter-network-traffic.md).

> [!NOTE] 
> Náklady na služby Azure Services. Azure Cost Management vám pomůže nastavit rozpočty a nakonfigurovat výstrahy, aby zůstaly pod kontrolou. Pomocí Cost Management Analyzujte, spravujte a optimalizujte náklady na Azure. Další informace najdete v [rychlém startu při analýze vašich nákladů](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).