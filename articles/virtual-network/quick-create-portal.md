---
title: Vytvoření virtuální sítě – rychlý start – Azure portal
titlesuffix: Azure Virtual Network
description: V tomto rychlém startu zjistíte, jak vytvořit virtuální síť pomocí webu Azure Portal. Virtuální síť umožňuje prostředkům Azure, jako jsou virtuální počítače, komunikovat soukromě mezi sebou a s Internetem.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 11/30/2018
ms.author: jdial
ms.openlocfilehash: 346299dff8354bfca56a1f348c8f66e90da89632
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54020825"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Rychlý start: Vytvoření virtuální sítě pomocí Portálu Azure

Virtuální síť umožňuje prostředkům Azure, jako jsou virtuální počítače (VM), komunikovat soukromě mezi sebou a s Internetem. V tomto rychlém startu zjistíte, jak vytvořit virtuální síť. Po vytvoření virtuální sítě do ní nasadíte dva virtuální počítače. Potom z Internetu připojit k virtuálním počítačům a komunikovat soukromě mezi dvěma virtuálními počítači.

Pokud ještě nemáte předplatné Azure, vytvořte si teď [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. Na straně levého horního rohu obrazovky vyberte **vytvořit prostředek** > **sítě** > **virtuální síť**.

1. V **vytvořit virtuální síť**, zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte *myVirtualNetwork*. |
    | Adresní prostor | Zadejte *10.1.0.0/16*. |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte **vytvořit nový**, zadejte *myResourceGroup*a pak vyberte **OK**. |
    | Umístění | Vyberte **USA – východ**.|
    | Podsíť - Name | Zadejte *myVirtualSubnet*. |
    | Podsíť – Rozsah adres | Zadejte *10.1.0.0/24*. |

1. Ponechejte zbývající výchozí hodnoty a vyberte **vytvořit**.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte ve virtuální síti dva virtuální počítače:

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

1. Na straně levého horního rohu obrazovky vyberte **vytvořit prostředek** > **Compute** > **systému Windows Server 2016 Datacenter**.

1. V **vytvořit virtuální počítač – základní informace o**, zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **MyResourceGroup**. Jste vytvořili v předchozí části. |
    | **PODROBNOSTI O INSTANCI** |  |
    | Název virtuálního počítače | Zadejte *myVm1*. |
    | Oblast | Vyberte **USA – východ**. |
    | Možnosti dostupnosti | Ponechte výchozí nastavení **žádné redundance infrastruktury požadované**. |
    | Image | Ponechte výchozí nastavení **systému Windows Server 2016 Datacenter**. |
    | Velikost | Ponechte výchozí nastavení **v2 Standard DS1**. |
    | **ÚČET SPRÁVCE** |  |
    | Uživatelské jméno | Zadejte libovolné uživatelské jméno. |
    | Heslo | Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potvrzení hesla | Zadejte heslo znovu. |
    | **PŘÍCHOZÍ PRAVIDLA PORTŮ** |  |
    | Veřejné příchozí porty | Ponechte výchozí nastavení **žádný**. |
    | **UŠETŘETE PENÍZE** |  |
    | Máte už licenci na Windows? | Ponechte výchozí nastavení **ne**. |

1. Vyberte **Další: Disky**.

1. V **vytvoření virtuálního počítače – disky**, ponechte výchozí hodnoty a vyberte **Další: Sítě**.

1. V **vytvoření virtuálního počítače – sítě**, vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Virtuální síť | Ponechte výchozí nastavení **myVirtualNetwork**. |
    | Podsíť | Ponechte výchozí nastavení **myVirtualSubnet (10.1.0.0/24)**. |
    | Veřejná IP adresa | Ponechte výchozí nastavení **(nové) myVm-ip**. |
    | Síťové porty zabezpečení | Vyberte **povolit vybrané porty**. |
    | Vyberte příchozí porty | Vyberte **HTTP** a **RDP**.

1. Vyberte **Další: Správa**.

1. V **vytvoření virtuálního počítače – Správa**, pro **účet úložiště diagnostiky**vyberte **vytvořit nový**.

1. V **vytvořit účet úložiště**, zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte *myvmstorageaccount*. |
    | Account kind (Druh účtu) | Ponechte výchozí nastavení **úložiště (verze 1 pro obecné účely)**. |
    | Výkon | Ponechte výchozí nastavení **standardní**. |
    | Replikace | Ponechte výchozí nastavení **místně redundantní úložiště (LRS)**. |

1. Vyberte **OK**.

1. Vyberte **Zkontrolovat a vytvořit**. Přejdete tak **revize + vytvořit** stránky a Azure ověří vaši konfiguraci.

1. Pokud uvidíte, že **ověření proběhlo úspěšně.** vyberte **vytvořit**.

### <a name="create-the-second-vm"></a>Vytvoření druhého virtuálního počítače

1. Proveďte kroky 1 až 9 z výše.

    > [!NOTE]
    > V kroku 2, pro **název virtuálního počítače**, zadejte *myVm2*.
    >
    > V kroku 7, pro **účet úložiště diagnostiky**, ujistěte se, že vyberete **myvmstorageaccount**.

1. Vyberte **Zkontrolovat a vytvořit**. Přejdete tak **revize + vytvořit** stránky a Azure ověří vaši konfiguraci.

1. Pokud uvidíte, že **ověření proběhlo úspěšně.** vyberte **vytvořit**.

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

Po vytvoření *myVm1*, k němu připojit přes internet.

1. Do panelu hledání na portálu, zadejte *myVm1*.

1. Klikněte na tlačítko **Připojit**.

    ![Připojení k virtuálnímu počítači](./media/quick-create-portal/connect-to-virtual-machine.png)

    Po výběru **připojit** tlačítko **připojit k virtuálnímu počítači** otevře.

1. Vyberte **stáhnout soubor RDP**. Azure vytvoří Remote Desktop Protocol (*RDP*) soubor a stáhne do vašeho počítače.

1. Otevřete na stažený *RDP* souboru.

    1. Pokud se zobrazí výzva, vyberte **Připojit**.

    1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

        > [!NOTE]
        > Budete muset vybrat **víc možností** > **použít jiný účet**, a zadejte přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.

1. Vyberte **OK**.

1. V procesu může zobrazit upozornění certifikátu během přihlašování. Pokud se zobrazí upozornění certifikátu, vyberte **Ano** nebo **pokračovat**.

1. Jakmile se zobrazí na plochu virtuálního počítače, minimalizujte, přejdete zpět na místní pracovní plocha.

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

1. Ve vzdálené ploše *myVm1*, otevřete prostředí PowerShell.

1. Zadejte `ping myVm2`.

    Získáte zpět něco jako tato zpráva:

    ```powershell
    Pinging myVm2.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.clouda
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.1.0.5:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    `ping` Selže, protože `ping` používá ovládací prvek zpráva ICMP (Internet Protocol). Ve výchozím nastavení nemá povolený průchod bránou Windows firewall.

1. Chcete-li povolit *myVm2* příkaz ping *myVm1* v pozdějším kroku, zadejte tento příkaz:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    Že příkaz povolí protokol ICMP příchozí přes bránu Windows firewall:

1. Ukončete připojení ke vzdálené ploše virtuálního počítače *myVm1*.

1. Zopakujte kroky uvedené v části [Připojení k virtuálnímu počítači z internetu](#connect-to-a-vm-from-the-internet), ale připojte se k virtuálnímu počítači *myVm2*.

1. Na příkazovém řádku zadejte `ping myvm1`.

    Získáte zpět něco jako tato zpráva:

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

    Obdržíte odpovědi z virtuálního počítače *myVm1*, protože jste v předchozím kroku povolili průchod protokolu ICMP bránou Windows Firewall na virtuálním počítači *myVm1*.

1. Ukončete připojení ke vzdálené ploše virtuálního počítače *myVm2*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete hotovi s virtuální sítí a virtuální počítače, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Do pole **Hledat** v horní části portálu zadejte *myResourceGroup*.

1. Jakmile se ve výsledcích hledání zobrazí skupina prostředků **myResourceGroup**, vyberte ji.

1. Vyberte **Odstranit skupinu prostředků**.

1. Zadejte *myResourceGroup* pro **zadejte název skupiny prostředků** a vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili výchozí virtuální síť a dva virtuální počítače. Připojení k jednomu virtuálnímu počítači z Internetu a Navázali jste soukromou komunikaci mezi dvěma virtuálními počítači. Další informace o nastavení virtuální sítě najdete v tématu [Správa virtuální sítě](manage-virtual-network.md).

Ve výchozím nastavení Azure umožňuje neomezenou soukromou komunikaci mezi virtuálními počítači. Naopak umožňuje jenom příchozí připojení ke vzdálené ploše pro virtuální počítače s Windows z Internetu. Další informace o konfiguraci různých typů síťové komunikace mezi virtuálním počítači, přejděte [filtrování síťového provozu](tutorial-filter-network-traffic.md) kurzu.