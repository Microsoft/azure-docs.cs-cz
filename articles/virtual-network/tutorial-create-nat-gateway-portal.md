---
title: 'Kurz: Vytvoření brány NAT – Azure Portal'
titlesuffix: Azure Virtual Network NAT
description: V tomto kurzu se dozvíte, jak vytvořit a ověřit bránu NAT pomocí Azure Portal.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: 82b5892b027627871e5492e3c6cd3776a923632b
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553438"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal"></a>Kurz: Vytvoření brány NAT pomocí Azure Portal

V tomto kurzu se dozvíte, jak používat službu Azure Virtual Network NAT. Vytvoříte bránu NAT pro poskytování odchozího připojení pro virtuální počítač v Azure. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte virtuální síť.
> * Vytvoří virtuální počítač.
> * Vytvořte bránu NAT a přidružte ji k virtuální síti.
> * Připojte se k virtuálnímu počítači a ověřte IP adresu NAT.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="virtual-network"></a>Virtuální síť

Než nasadíte virtuální počítač a můžete použít bránu NAT, musíme vytvořit skupinu prostředků a virtuální síť.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

2. V levém horním rohu obrazovky vyberte **Vytvořit prostředek > Sítě > Virtuální síť** nebo do vyhledávacího pole zadejte **Virtuální síť**.

3. Vyberte **Vytvořit**.

4. V části **vytvořit virtuální síť** zadejte nebo vyberte tyto informace na kartě **základy** :

    | **Nastavení**          | **Hodnota**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Podrobnosti o projektu**  |                                                                 |
    | Předplatné     | Vyberte své předplatné Azure.                                  |
    | Skupina prostředků   | Vyberte, že chcete **vytvořit novou** IP adresu. </br> Zadejte **myResourceGroupNAT**. </br> Vyberte **OK**. |
    | **Podrobnosti o instancích** |                                                                 |
    | Name             | Zadejte **myVNet**                                    |
    | Oblast           | Vyberte **(Evropa) západní Evropa** |

5. Vyberte kartu **IP adresy** nebo v dolní části stránky vyberte tlačítko **Další: IP adresy** .

6. Na kartě **IP adresy** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Adresní prostor protokolu IPv4 | Zadejte **10.1.0.0/16** |

7. Vyberte **+ Přidat podsíť**. 

8. V **Upravit podsíť** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Název podsítě | Zadejte **mySubnet** |
    | Rozsah adres podsítě | Zadejte **10.1.0.0/24** |

9. Vyberte **Přidat**.

10. Vyberte kartu **zabezpečení** .

11. V části **BastionHost** vyberte **Povolit**. Zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Název bastionu | Zadejte **myBastionHost** |
    | Adresní prostor AzureBastionSubnet | Zadejte **10.1.1.0/24** |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. </br> Jako **název** zadejte **myBastionIP**. </br> Vyberte **OK**. |

12. Vyberte kartu **Revize + vytvořit** nebo vyberte tlačítko **Revize + vytvořit** .

13. Vyberte **Vytvořit**.

## <a name="nat-gateway"></a>NAT Gateway

Můžete použít jeden nebo více prostředků veřejné IP adresy, předpony veřejných IP adres nebo obojí. Přidáme prostředek veřejné IP adresy a prostředek brány NAT.

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek > síť > NAT Gateway** nebo vyhledejte **bránu NAT** v poli hledání.

2. Vyberte **Vytvořit**. 

3. V části **vytvořit bránu pro překlad síťových adres (NAT)** zadejte nebo vyberte tyto informace na kartě **základy** :

    | **Nastavení**          | **Hodnota**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Podrobnosti o projektu**  |                                                                 |
    | Předplatné     | Vyberte své předplatné Azure.                                  |
    | Skupina prostředků   | Vyberte **myResourceGroupNAT**. |
    | **Podrobnosti o instancích** |                                                                 |
    | Name             | Zadejte **myNATgateway**                                    |
    | Oblast           | Vyberte **(Evropa) západní Evropa**  |
    | Zóna dostupnosti | Vyberte **Žádná**. |
    | Časový limit nečinnosti (minuty) | Zadejte **10**. |

4. Vyberte kartu **odchozí IP adresa** nebo klikněte na tlačítko **Další: odchozí IP adresa** v dolní části stránky.

5. Na kartě **odchozí IP adresa** zadejte nebo vyberte následující informace:

    | **Nastavení** | **Hodnota** |
    | ----------- | --------- |
    | Veřejné IP adresy | Vyberte **vytvořit novou veřejnou IP adresu**. </br> Do **název** zadejte **myPublicIP**. </br> Vyberte **OK**. |

6. Vyberte kartu **podsíť** nebo vyberte tlačítko **Další: podsíť** v dolní části stránky.

7. Na kartě **podsíť** vyberte v rozevíracím seznamu **virtuální síť** možnost **myVNet** .

8. Zaškrtněte políčko vedle **mySubnet**.

9. Vyberte kartu **Revize + vytvořit** nebo v dolní části stránky vyberte tlačítko modrá **recenze + vytvořit** .

10. Vyberte **Vytvořit**.
    
## <a name="virtual-machine"></a>Virtuální počítač

V této části vytvoříte virtuální počítač pro otestování brány NAT a ověříte veřejnou IP adresu odchozího připojení.

1. V levé horní části portálu vyberte **vytvořit prostředek**  >    >  **virtuální počítač** Compute. 

2. Na stránce **vytvořit virtuální počítač** na kartě **základy** zadejte nebo vyberte následující informace:

    | **Nastavení** | **Hodnota** |
    | ----------- | --------- |
    | **Podrobnosti o projektu** |   |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroupNAT**. |
    | **Podrobnosti o instancích** |   |
    | Název virtuálního počítače | Zadejte **myVM**. |
    | Oblast | Vyberte **(Evropa) západní Evropa**. |
    | Možnosti dostupnosti | Nechte výchozí nastavení bez nutnosti redundance. |
    | Image | Vyberte **Windows Server 2019 Datacenter – Gen1**. |
    | Velikost | Vyberte **Standard_DS1_v2**. |
    | **Účet správce** |   |
    | Uživatelské jméno | Zadejte uživatelské jméno pro virtuální počítač. |
    | Heslo | Zadejte heslo. |
    | Potvrzení hesla | Potvrďte heslo. |
    | **Pravidla portů pro příchozí spojení** |    |
    | Veřejné příchozí porty | Vyberte **Žádná**. |

3. Vyberte kartu **disky** nebo vyberte tlačítko **Další: disky** v dolní části stránky.

4. Na kartě **disky** ponechte výchozí hodnotu.

5. Vyberte kartu **síť** nebo vyberte tlačítko **Další: sítě** v dolní části stránky.

6. Na kartě **sítě** zadejte nebo vyberte následující informace:

    | **Nastavení** | **Hodnota** |
    | ----------- | --------- |
    | **Síťové rozhraní** |   |
    | Virtuální síť | Vyberte **myVNet**. |
    | Podsíť | Vyberte **mySubnet**. |
    | Veřejná IP adresa | Vyberte **Žádná**. |
    | Skupina zabezpečení sítě NIC | Vyberte **Basic**. |
    | Veřejné příchozí porty | Vyberte **Žádná**. |

7. Vyberte kartu **Revize + vytvořit** nebo v dolní části stránky vyberte tlačítko modrá **recenze + vytvořit** .

8. Vyberte **Vytvořit**.

## <a name="test-nat-gateway"></a>Test brány NAT

V této části otestujeme bránu NAT. Nejdříve zjistíme veřejnou IP adresu brány NAT. Pak se připojíme k testovacímu virtuálnímu počítači a ověříme odchozí připojení prostřednictvím brány NAT.
    
1. Na obrazovce **Přehled** vyhledejte veřejnou IP adresu pro bránu NAT. V nabídce na levé straně vyberte **všechny služby** a vyberte **všechny prostředky** a pak vyberte **myPublicIP**.

2. Poznamenejte si veřejnou IP adresu:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Zjistit veřejnou IP adresu brány NAT" border="true":::

3. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a potom v seznamu prostředky vyberte **myVM** , která je umístěná ve skupině prostředků **myResourceGroupNAT** .

4. Na stránce **Přehled** vyberte **připojit** a pak **bastionu**.

5. Vyberte tlačítko modrého **použití bastionu** .

6. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

7. Otevřete **Internet Explorer** v **myTestVM**.

8. **https://whatsmyip.com** Do adresního řádku zadejte.

9. Ověřte, že zobrazená IP adresa odpovídá adrese brány NAT, kterou jste si poznamenali v předchozím kroku:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer zobrazující externí odchozí IP adresy" border="true":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte virtuální síť, virtuální počítač a bránu NAT pomocí následujících kroků:

1. V nabídce na levé straně vyberte **skupiny prostředků**.

2. Vyberte skupinu prostředků **myResourceGroupNAT** .

3. Vyberte **Odstranit skupinu prostředků**.

4. Zadejte **myResourceGroupNAT** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Další informace o službě Azure Virtual Network NAT najdete v těchto tématech:
> [!div class="nextstepaction"]
> [Přehled služby Virtual Network NAT](nat-overview.md)
