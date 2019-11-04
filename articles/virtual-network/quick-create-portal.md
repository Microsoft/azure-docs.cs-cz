---
title: Vytvoření virtuální sítě – rychlý Start – Azure Portal
titlesuffix: Azure Virtual Network
description: V tomto rychlém startu zjistíte, jak vytvořit virtuální síť pomocí webu Azure Portal. Virtuální síť umožňuje prostředkům Azure, jako jsou virtuální počítače, bezpečně komunikovat mezi sebou a s internetem.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can securely communicate with each other and with the internet.
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 07/08/2019
ms.author: kumud
ms.openlocfilehash: d8e95f9c345a943eb458800b852640e3f1fde907
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488496"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Rychlý start: Vytvoření virtuální sítě pomocí webu Azure Portal

Virtuální síť je základním stavebním blokem pro vaši privátní síť v Azure. Umožňuje prostředkům Azure, jako jsou virtuální počítače, bezpečně komunikovat mezi sebou a s internetem. V tomto rychlém startu se dozvíte, jak vytvořit virtuální síť pomocí Azure Portal. Pak můžete nasadit dva virtuální počítače do virtuální sítě, bezpečně komunikovat mezi těmito dvěma virtuálními počítači a připojit se k virtuálním počítačům z Internetu.


Pokud ještě nemáte předplatné Azure, vytvořte si teď [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na web [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. V nabídce Azure Portal vyberte **vytvořit prostředek**.

2. Z Azure Marketplace vyberte **síť** > **virtuální síť**.

3. V nástroji **vytvořit virtuální síť**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Zadejte *myVirtualNetwork*. |
    | Adresní prostor | Zadejte *10.1.0.0/16*. |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte **vytvořit nový**, zadejte *myResourceGroup*a pak vyberte **OK**. |
    | Umístění | Vyberte **USA – východ**.|
    | Název podsítě | Zadejte *myVirtualSubnet*. |
    | Podsíť – Rozsah adres | Zadejte *10.1.0.0/24*. |

4. Ponechte REST jako výchozí a vyberte **vytvořit**.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte ve virtuální síti dva virtuální počítače:

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

1. V nabídce Azure Portal vyberte **vytvořit prostředek**.

2. V Azure Marketplace vyberte **compute** > **Windows Server 2019 Datacenter**.

3. V nástroji **vytvořit virtuální počítač základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části. |
    | **PODROBNOSTI INSTANCE** |  |
    | Název virtuálního počítače | Zadejte *myVm1*. |
    | Oblast | Vyberte **USA – východ**. |
    | Možnosti dostupnosti | Nechte výchozí nastavení **bez nutnosti redundance infrastruktury**. |
    | Obrázek | Ponechte výchozí **Windows Server 2019 Datacenter**. |
    | Velikost | Ponechte výchozí hodnotu **Standard DS1 v2**. |
    | **ÚČET SPRÁVCE** |  |
    | Uživatelské jméno | Zadejte uživatelské jméno, které si zvolíte. |
    | Heslo | Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potvrzení hesla | Zadejte znovu heslo. |
    | **PRAVIDLA PORTŮ PRO PŘÍCHOZÍ SPOJENÍ** |  |
    | Veřejné příchozí porty | Nechejte výchozí nastavení **žádné**. |
    | **ÚSPORA PENĚZ** |  |
    | Máte už licenci na Windows? | Ponechte výchozí hodnotu **ne**. |

4. Vyberte **Další: disky**.

5. V části **vytvořit virtuální počítač – disky**ponechte výchozí hodnoty a vyberte **Další: sítě**.

6. V nástroji **vytvořit virtuální počítač – síť**vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Virtuální síť | Ponechte výchozí **myVirtualNetwork**. |
    | Podsíť | Ponechte výchozí **myVirtualSubnet (10.1.0.0/24)** . |
    | Veřejná IP adresa | Ponechte výchozí **(New) myVm-IP**. |
    | Veřejné příchozí porty | Vyberte možnost **Povolení vybraných portů**. |
    | Vybrat příchozí porty | Vyberte **http** a **RDP**.

7. Vyberte **Další: Správa**.

8. V **nástroji vytvořit virtuální počítač**pro **účet úložiště pro diagnostiku**vyberte **vytvořit novou**.

9. V nástroji **vytvořit účet úložiště**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Zadejte *myvmstorageaccount*. Pokud se tento název povede, vytvořte jedinečný název.|
    | Account kind (Druh účtu) | Ponechte výchozí **úložiště (pro obecné účely V1)** . |
    | Výkon | Ponechte výchozí **Standard**. |
    | Replikace | Ponechte výchozí **místně redundantní úložiště (LRS)** . |

10. Vyberte **OK**.

11. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvořit** , kde Azure ověřuje vaši konfiguraci.

12. Když se zobrazí zpráva s **potvrzením ověření** , vyberte **vytvořit**.

### <a name="create-the-second-vm"></a>Vytvoření druhého virtuálního počítače

1. Proveďte kroky 1 a 9 výše.

    > [!NOTE]
    > V kroku 2 zadejte do pole **název virtuálního počítače** *myVm2*.
    >
    > V kroku 7 v případě **diagnostiky účtu úložiště**ověřte, že jste vybrali možnost **myvmstorageaccount**.

2. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvořit** a Azure ověří vaši konfiguraci.

3. Když se zobrazí zpráva s **potvrzením ověření** , vyberte **vytvořit**.

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

Po vytvoření *myVm1*se připojte k Internetu.

1. Na panelu hledání na portálu zadejte *myVm1*.

2. Klikněte na tlačítko **Připojit**.

    ![Připojení k virtuálnímu počítači](./media/quick-create-portal/connect-to-virtual-machine.png)

    Po výběru tlačítka **připojit** se **připojte k virtuálnímu počítači** .

3. Vyberte **Stáhnout soubor RDP**. Azure vytvoří soubor protokol RDP (Remote Desktop Protocol) ( *. RDP*) a stáhne ho do vašeho počítače.

4. Otevřete stažený soubor *. RDP* .

    1. Pokud se zobrazí výzva, vyberte **Připojit**.

    2. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

        > [!NOTE]
        > Možná budete muset vybrat **Další volby** > **použít jiný účet**a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.

5. Vyberte **OK**.

6. Během procesu přihlašování se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění certifikátu, vyberte **Ano** nebo **pokračovat**.

7. Jakmile se zobrazí plocha virtuálního počítače, minimalizujte ji tak, aby se vrátila k místnímu počítači.

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

1. Ve vzdálené ploše *myVm1*otevřete PowerShell.

2. Zadejte `ping myVm2`.

    Zobrazí se zpráva podobná této:

    ```powershell
    Pinging myVm2.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.clouda
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.1.0.5:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    `ping` se nezdařil, protože `ping` používá protokol ICMP (Internet Control Message Protocol). Standardně není ICMP povolený přes bránu Windows Firewall.

3. Pokud chcete, aby se v pozdějším kroku *myVm2* příkaz Test *myVm1* , zadejte tento příkaz:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Tento příkaz umožňuje příchozí protokol ICMP přes bránu Windows Firewall:

4. Ukončete připojení ke vzdálené ploše virtuálního počítače *myVm1*.

5. Zopakujte kroky uvedené v části [Připojení k virtuálnímu počítači z internetu](#connect-to-a-vm-from-the-internet), ale připojte se k virtuálnímu počítači *myVm2*.

6. Na příkazovém řádku zadejte `ping myvm1`.

    Zobrazí se něco jako tato zpráva:

    ```powershell
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

7. Ukončete připojení ke vzdálené ploše virtuálního počítače *myVm2*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s použitím virtuální sítě a virtuálních počítačů hotovi, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Do **vyhledávacího** pole v horní části portálu zadejte *myResourceGroup* a ve výsledcích hledání vyberte **myResourceGroup** .

2. Vyberte **Odstranit skupinu prostředků**.

3. Zadejte *myResourceGroup* pro **typ název skupiny prostředků** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili výchozí virtuální síť a dva virtuální počítače. Připojili jste se k jednomu virtuálnímu počítači z Internetu a bezpečně komunikovali mezi těmito dvěma virtuálními počítači. Další informace o nastavení virtuální sítě najdete v tématu [Správa virtuální sítě](manage-virtual-network.md).

Ve výchozím nastavení Azure umožňuje neomezenou zabezpečenou komunikaci mezi virtuálními počítači. Naopak umožňuje pouze příchozí připojení vzdálené plochy k virtuálním počítačům s Windows z Internetu. Další informace o konfiguraci různých typů síťových komunikací virtuálních počítačů najdete v kurzu [filtrování síťového provozu](tutorial-filter-network-traffic.md) .
