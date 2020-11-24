---
title: 'Kurz: připojení k účtu úložiště pomocí privátního koncového bodu Azure'
titleSuffix: Azure Private Link
description: Začněte s tímto kurzem pomocí privátního koncového bodu Azure a připojovat se k účtu úložiště soukromě.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 9/25/2020
ms.openlocfilehash: 97795b2a693b68d1bd73a00f7b3e5ee3d4679545
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522120"
---
# <a name="tutorial-connect-to-a-storage-account-using-an-azure-private-endpoint"></a>Kurz: připojení k účtu úložiště pomocí privátního koncového bodu Azure

Privátní koncový bod Azure je základním stavebním blokem privátního propojení v Azure. Umožňuje prostředkům Azure, jako jsou virtuální počítače, komunikovat soukromě s prostředky privátního propojení.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte virtuální síť a hostitele bastionu.
> * Vytvoří virtuální počítač.
> * Vytvořte účet úložiště s privátním koncovým bodem.
> * Otestujte připojení k privátnímu koncovému bodu účtu úložiště.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-network-and-bastion-host"></a>Vytvoření virtuální sítě a hostitele bastionu

V této části vytvoříte virtuální síť, podsíť a hostitele bastionu. 

Hostitel bastionu se bude používat k zabezpečenému připojení k virtuálnímu počítači za účelem testování privátního koncového bodu.

1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek > Sítě > Virtuální síť** nebo do vyhledávacího pole zadejte **Virtuální síť**.

2. V části **vytvořit virtuální síť** zadejte nebo vyberte tyto informace na kartě **základy** :

    | **Nastavení**          | **Hodnota**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Podrobnosti o projektu**  |                                                                 |
    | Předplatné     | Vyberte své předplatné Azure.                                  |
    | Skupina prostředků   | Vybrat **myResourceGroup** |
    | **Podrobnosti o instancích** |                                                                 |
    | Name             | Zadejte **myVNet**                                    |
    | Region           | Vyberte **východní USA** |

3. Vyberte kartu **IP adresy** nebo v dolní části stránky vyberte tlačítko **Další: IP adresy** .

4. Na kartě **IP adresy** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Adresní prostor protokolu IPv4 | Zadejte **10.1.0.0/16** |

5. V části **název podsítě** vyberte slovo **výchozí**.

6. V **Upravit podsíť** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Název podsítě | Zadejte **mySubnet** |
    | Rozsah adres podsítě | Zadejte **10.1.0.0/24** |

7. Vyberte **Uložit**.

8. Vyberte kartu **zabezpečení** .

9. V části **BastionHost** vyberte **Povolit**. Zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Název bastionu | Zadejte **myBastionHost** |
    | Adresní prostor AzureBastionSubnet | Zadejte **10.1.1.0/24** |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. </br> Jako **název** zadejte **myBastionIP**. </br> Vyberte **OK**. |


8. Vyberte kartu **Revize + vytvořit** nebo vyberte tlačítko **Revize + vytvořit** .

9. Vyberte **Vytvořit**.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

V této části vytvoříte virtuální počítač, který se použije k otestování privátního koncového bodu.


1. V levé horní části portálu vyberte **vytvořit prostředek**  >  **Compute**  >  **virtuální počítač** COMPUTE nebo ve vyhledávacím poli vyhledejte **virtuální počítač** .
   
2. V části **vytvořit virtuální počítač** zadejte nebo vyberte hodnoty na kartě **základy** :

    | Nastavení | Hodnota                                          |
    |-----------------------|----------------------------------|
    | **Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné Azure. |
    | Skupina prostředků | Vybrat **myResourceGroup** |
    | **Podrobnosti o instancích** |  |
    | Název virtuálního počítače | Zadejte **myVM** |
    | Region | Vyberte **východní USA** |
    | Možnosti dostupnosti | Vyberte možnost **nepožaduje se žádná redundance infrastruktury** . |
    | Image | Vyberte **Windows Server 2019 Datacenter – Gen1** |
    | Instance Azure Spot | Vybrat **ne** |
    | Velikost | Vyberte velikost virtuálního počítače nebo použijte výchozí nastavení. |
    | **Účet správce** |  |
    | Uživatelské jméno | Zadejte uživatelské jméno. |
    | Heslo | Zadat heslo |
    | Potvrzení hesla | Znovu zadejte heslo. |

3. Vyberte kartu **síť** nebo vyberte **Další: disky** a **Další: síť**.
  
4. Na kartě sítě vyberte nebo zadejte:

    | Nastavení | Hodnota |
    |-|-|
    | **Síťové rozhraní** |  |
    | Virtuální síť | **myVNet** |
    | Podsíť | **mySubnet** |
    | Veřejná IP adresa | Vyberte **Žádná**. |
    | Skupina zabezpečení sítě NIC | **Basic**|
    | Veřejné příchozí porty | Vyberte **Žádná**. |
   
5. Vyberte **Zkontrolovat a vytvořit**. 
  
6. Zkontrolujte nastavení a pak vyberte **vytvořit**.

## <a name="create-storage-account-with-a-private-endpoint"></a>Vytvoření účtu úložiště s privátním koncovým bodem

V této části vytvoříte účet úložiště a nakonfigurujete privátní koncový bod.

1. V nabídce vlevo vyberte **vytvořit prostředek**  >  **úložiště**  >  **účet** úložiště nebo ve vyhledávacím poli vyhledejte **účet úložiště** .

2. Na kartě **základy** v části **vytvořit účet úložiště** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota                                          |
    |-----------------------|----------------------------------|
    | **Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné Azure. |
    | Skupina prostředků | Vybrat **myResourceGroup** |
    | **Podrobnosti o instancích** |  |
    | Název účtu úložiště | Zadejte **mystorageaccount**. Pokud není název k dispozici, zadejte jedinečný název. |
    | Umístění | Vyberte **východní USA** |
    | Výkon | Ponechte výchozí **Standard** . |
    | Druh účtu | Ponechte výchozí **úložiště (pro obecné účely v2)** |
    | Replikace| Ponechte výchozí **geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)** . |
   
3. Vyberte kartu **síť** nebo vyberte tlačítko **Další: síťové** .

4. Na kartě **sítě** v části **způsob připojení** vyberte **privátní koncový bod**.

5. V **privátním koncovém bodu** vyberte **+ Přidat**.

6. V části **vytvořit privátní koncový bod** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota                                          |
    |-----------------------|----------------------------------|
    | Předplatné | Vyberte své předplatné Azure. |
    | Skupina prostředků | Vybrat **myResourceGroup** |
    | Umístění | Vyberte **východní USA** |
    | Name | Zadejte **myPrivateEndpoint** |
    | Prostředek úložiště | Ponechte výchozí **objekt BLOB** . |
    | **Sítě** |  |
    | Virtuální síť | Vybrat **myVNet** |
    | Podsíť | Vyberte **mySubnet**. |
    | **Integrace Privátní DNS** |
    | Integrovat s privátní zónou DNS | Ponechat výchozí nastavení **Ano** |
    | Zóna Privátního DNS | Ponechte výchozí (New) privatelink.blob.core.windows.net. |

7. Vyberte **OK**.

8. Vyberte **Zkontrolovat a vytvořit**.

9. Vyberte **Vytvořit**.

10. V levém navigačním podokně vyberte **skupiny prostředků** .

11. Vyberte **myResourceGroup**.

12. Vyberte účet úložiště, který jste vytvořili v předchozích krocích.

13. V části **Nastavení** účtu úložiště vyberte **přístupové klíče**.

14. Vyberte možnost Kopírovat na **připojovací řetězec** pro **klíč1**.

## <a name="test-connectivity-to-private-endpoint"></a>Test připojení k privátnímu koncovému bodu

V této části použijete virtuální počítač, který jste vytvořili v předchozím kroku, abyste se připojili k účtu úložiště v rámci privátního koncového bodu.

1. V levém navigačním podokně vyberte **skupiny prostředků** .

2. Vyberte **myResourceGroup**.

3. Vyberte **myVM**.

4. Na stránce Přehled pro **myVM** vyberte **připojit** a pak **bastionu**.

5. Vyberte tlačítko modrého **použití bastionu** .

6. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

7. Po připojení otevřete Windows PowerShell na serveru.

8. Zadejte `nslookup <storage-account-name>.blob.core.windows.net`. Nahraďte **\<storage-account-name>** názvem účtu úložiště, který jste vytvořili v předchozích krocích.  Zobrazí se zpráva podobná tomu, co se zobrazuje níže:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mystorageaccount8675.privatelink.blob.core.windows.net
    Address:  10.1.0.5
    Aliases:  mystorageaccount8675.blob.core.windows.net
    ```

    Pro název účtu úložiště se vrátí privátní IP adresa **10.1.0.5** .  Tato adresa je v podsíti virtuální sítě, kterou jste vytvořili dříve.

9. Nainstalujte na virtuální počítač [Průzkumník služby Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows&toc=%252fazure%252fstorage%252fblobs%252ftoc.json) .

10. Po instalaci **Průzkumník služby Microsoft Azure Storage** vyberte **Dokončit** .  Ponechte zaškrtnuté políčko pro otevření aplikace.

11. Na obrazovce **připojit k Azure Storage** vyberte **Použít připojovací řetězec**.

12. Vyberte **Další**.

13. Do pole **Zobrazovaný název** zadejte název svého účtu úložiště z předchozích kroků.

14. V poli **připojovací řetězec** vložte připojovací řetězec z účtu úložiště, který jste zkopírovali v předchozích krocích.

15. Vyberte **Další**.

16. Ověřte správnost nastavení v **souhrnu připojení**.  

17. Vyberte **Connect** (Připojit).

18. Ukončete připojení k **myVM**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte virtuální síť, virtuální počítač a účet úložiště pomocí následujících kroků:

1. V nabídce na levé straně vyberte **skupiny prostředků**.

2. Vyberte **myResourceGroup**.

3. Vyberte **Odstranit skupinu prostředků**.

4. Do **pole zadejte název skupiny prostředků** zadejte **myResourceGroup** .

5. Vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Naučte se vytvořit službu privátního propojení:
> [!div class="nextstepaction"]
> [Vytvoření služby privátního propojení](create-private-link-service-portal.md)