---
title: 'Rychlý Start: vytvoření virtuální sítě – Azure Portal'
titleSuffix: Azure Virtual Network
description: V tomto rychlém startu se dozvíte, jak vytvořit virtuální síť pomocí Azure Portal.
author: KumudD
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/17/2021
ms.author: kumud
ms.openlocfilehash: 8af5b302e3ec790b6ee9356aca0699d0edcd284e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606057"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Rychlý start: Vytvoření virtuální sítě pomocí webu Azure Portal

V tomto rychlém startu se dozvíte, jak vytvořit virtuální síť pomocí Azure Portal. Nasadíte dva virtuální počítače (VM). Dále zabezpečeně komunikujete mezi virtuálními počítači a připojením k virtuálním počítačům z Internetu. Virtuální síť je základním stavebním blokem pro vaši privátní síť v Azure. Umožňuje prostředkům Azure, jako jsou virtuální počítače, bezpečně komunikovat mezi sebou a s internetem.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. V levém horním rohu portálu vyberte **vytvořit prostředek** .

2. Do vyhledávacího pole zadejte **Virtual Network**. Ve výsledcích hledání vyberte **Virtual Network** .

3. Na stránce **Virtual Network** vyberte **vytvořit**.

4. V části **vytvořit virtuální síť** zadejte nebo vyberte tyto informace na kartě **základy** :

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** |   |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte, že chcete **vytvořit novou** IP adresu.  </br> Zadejte **myResourceGroup**. </br> Vyberte **OK**. |
    | **Podrobnosti o instancích** |   |
    | Name | Zadejte **myVNet**. |
    | Oblast | Vyberte **(US) východní USA**. |

    :::image type="content" source="./media/quick-create-portal/create-virtual-network.png" alt-text="Vytvořit Azure Portal virtuální sítě" border="true":::

5. Vyberte kartu **IP adresy** nebo klikněte na tlačítko **Další: IP adresy** v dolní části stránky.

6. V části **adresní prostor IPv4** vyberte existující adresní prostor a změňte jej na **10.1.0.0/16**.

7. Vyberte **+ Přidat podsíť** a pak jako **název podsítě** zadejte **MySubnet** a **10.1.0.0/24** pro **Rozsah adres podsítě**.

8. Vyberte **Přidat**.

9. Vyberte kartu **zabezpečení** nebo v dolní části stránky vyberte tlačítko **Další: zabezpečení** .

10. V části **BastionHost** vyberte **Povolit**. Zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Název bastionu | Zadejte **myBastionHost** |
    | Adresní prostor AzureBastionSubnet | Zadejte **10.1.1.0/24** |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. </br> Jako **název** zadejte **myBastionIP**. </br> Vyberte **OK**. |

11. Vyberte kartu **Revize + vytvořit** nebo vyberte tlačítko **Revize + vytvořit** .

12. Vyberte **Vytvořit**.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte ve virtuální síti dva virtuální počítače:

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

1. V levé horní části portálu vyberte **vytvořit prostředek**  >    >  **virtuální počítač** Compute. 
   
2. V části **vytvořit virtuální počítač** zadejte nebo vyberte hodnoty na kartě **základy** :

    | Nastavení | Hodnota                                          |
    |-----------------------|----------------------------------|
    | **Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné Azure. |
    | Skupina prostředků | Vybrat **myResourceGroup** |
    | **Podrobnosti o instancích** |  |
    | Název virtuálního počítače | Zadejte **myVM1** |
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
    | Virtuální síť | Vyberte **myVNet**. |
    | Podsíť | Vyberte **mySubnet**. |
    | Veřejná IP adresa | Vybrat **žádné** |
    | Skupina zabezpečení sítě NIC | Vybrat **základní**|
    | Síť veřejných příchozích portů | Vyberte **Žádná**. |
   
5. Vyberte kartu **Revize + vytvořit** nebo v dolní části stránky vyberte tlačítko modrá **recenze + vytvořit** .
  
6. Zkontrolujte nastavení a pak vyberte **vytvořit**.

### <a name="create-the-second-vm"></a>Vytvoření druhého virtuálního počítače

1. V levé horní části portálu vyberte **vytvořit prostředek**  >    >  **virtuální počítač** Compute. 
   
2. V části **vytvořit virtuální počítač** zadejte nebo vyberte hodnoty na kartě **základy** :

    | Nastavení | Hodnota                                          |
    |-----------------------|----------------------------------|
    | **Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné Azure. |
    | Skupina prostředků | Vybrat **myResourceGroup** |
    | **Podrobnosti o instancích** |  |
    | Název virtuálního počítače | Zadejte **myVM2** |
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
    | Virtuální síť | Vyberte **myVNet**. |
    | Podsíť | Vyberte **mySubnet**. |
    | Veřejná IP adresa | Vybrat **žádné** |
    | Skupina zabezpečení sítě NIC | Vybrat **základní**|
    | Síť veřejných příchozích portů | Vyberte **Žádná**. |
   
5. Vyberte kartu **Revize + vytvořit** nebo v dolní části stránky vyberte tlačítko modrá **recenze + vytvořit** .
  
6. Zkontrolujte nastavení a pak vyberte **vytvořit**.

## <a name="connect-to-myvm1"></a>Připojení k myVM1

1. Pokud chcete spravovat privátní virtuální počítač, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **virtuální počítače**.

2. Vyberte název privátního virtuálního počítače **myVM1**.

3. V řádku nabídek virtuálního počítače vyberte **připojit** a pak vyberte **bastionu**.

    :::image type="content" source="./media/quick-create-portal/connect-to-virtual-machine.png" alt-text="Připojení k myVM1 pomocí Azure bastionu" border="true":::

4. Na stránce **připojit** vyberte tlačítko modré **použití bastionu** .

5. Na stránce **bastionu** zadejte uživatelské jméno a heslo, které jste dříve vytvořili pro virtuální počítač.

6. Vyberte **Connect** (Připojit).

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

1. V bastionu připojení **myVM1** otevřete PowerShell.

2. Zadejte `ping myvm2`.

    Zobrazí se zpráva podobná tomuto výstupu:

    ```powershell
    Pinging myvm2.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.5] with 32 bytes of data:
    Reply from 10.1.0.5: bytes=32 time=3ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.5:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 3ms, Average = 1ms
    ```

3. Zavřete připojení bastionu k **myVM1**.

4. Proveďte kroky v části [připojení k myVM1](#connect-to-myvm1), ale Připojte se k **myVM2**.

5. Otevřete PowerShell v **myVM2**, zadejte `ping myvm1` .

    Zobrazí se zpráva podobná této:

    ```powershell
    Pinging myvm1.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 1ms, Average = 1ms
    ```

7. Zavřete připojení bastionu k **myVM2**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V tomto rychlém startu jste vytvořili výchozí virtuální síť a dva virtuální počítače. 

Připojili jste se k jednomu virtuálnímu počítači z Internetu a bezpečně komunikovali mezi těmito dvěma virtuálními počítači.

Až budete s použitím virtuální sítě a virtuálních počítačů hotovi, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Vyhledejte a vyberte **myResourceGroup**.

1. Vyberte **Odstranit skupinu prostředků**.

1. Zadejte **myResourceGroup** pro **typ název skupiny prostředků** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Další informace o nastavení virtuální sítě najdete v tématu [Vytvoření, změna nebo odstranění virtuální sítě](manage-virtual-network.md).

Další informace o typech komunikace sítě virtuálních počítačů najdete v tématu [filtrování síťového provozu](tutorial-filter-network-traffic.md).
