---
title: 'Kurz: Integrace brány NAT pomocí veřejného nástroje pro vyrovnávání zatížení – Azure Portal'
titleSuffix: Virtual Network NAT
description: V tomto kurzu se dozvíte, jak integrovat Virtual Network bránu NAT s veřejným nástrojem pro vyrovnávání zatížení pomocí Azure Portal.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/19/2021
ms.custom: template-tutorial
ms.openlocfilehash: 8382dd10536a8c0475444d0cdff30340ad124e9c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722799"
---
# <a name="tutorial-integrate-a-nat-gateway-with-a-public-load-balancer-using-the-azure-portal"></a>Kurz: Integrace brány NAT s veřejným nástrojem pro vyrovnávání zatížení pomocí Azure Portal

V tomto kurzu se dozvíte, jak integrovat bránu NAT pomocí veřejného nástroje pro vyrovnávání zatížení.

Ve výchozím nastavení je služba Azure Standard Load Balancer zabezpečená. Odchozí připojení je explicitně definováno povolením odchozích SNAT (zdrojový překlad adres). SNAT je povoleno v pravidle vyrovnávání zatížení nebo v odchozích pravidlech. 

Integrace brány NAT nahrazuje nepotřebnou odchozí pravidla pro back-end fond odchozích SNAT. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření Azure Load Balancer
> * Vytvořte dva virtuální počítače pro back-end fond Azure Load Balancer.
> * Vytvoření brány NAT
> * Ověřte odchozí připojení virtuálních počítačů ve fondu back-end nástroje pro vyrovnávání zatížení.

## <a name="prerequisites"></a>Předpoklady

Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

V této části vytvoříte standardní Azure Load Balancer. 

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Vyberte **Vytvořit prostředek**. 
3. Do vyhledávacího pole zadejte **Nástroj pro vyrovnávání zatížení**. Ve výsledcích hledání vyberte **Nástroj pro vyrovnávání zatížení** .
4. Na stránce **Vyrovnávání zatížení** vyberte **vytvořit**.
5. Na stránce **vytvořit nástroj pro vyrovnávání zatížení** zadejte nebo vyberte následující informace: 

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | **Podrobnosti o projektu** |   |
    | Předplatné               | Vyberte své předplatné.    |    
    | Skupina prostředků         | Vyberte **vytvořit novou** a do textového pole zadejte **TutorPubLBNAT-RG** . </br> Vyberte **OK**.|
    | **Podrobnosti o instancích** |   |
    | Name                   | Zadejte **myLoadBalancer**                                   |
    | Oblast         | Vyberte **(US) východní USA**.                                        |
    | Typ          | Vyberte **Veřejný**.                                        |
    | SKU           | Ponechte výchozí **Standard**. |
    | Úroveň          | Ponechte výchozí **oblast**. |
    | **Veřejná IP adresa** |   |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. </br> Pokud máte existující veřejnou IP adresu, kterou byste chtěli použít, vyberte **použít existující**. |
    | Název veřejné IP adresy | Do textového pole zadejte **myPublicIP-9,1** .|
    | Zóna dostupnosti | Chcete-li vytvořit odolný Nástroj pro vyrovnávání zatížení, vyberte **zónu – redundantní** . Pokud chcete vytvořit nástroj pro vyrovnávání zatížení, vyberte konkrétní zónu z 1, 2 nebo 3. |
    | Přidat veřejnou IPv6 adresu | Vyberte **Ne**. </br> Další informace o adresách IPv6 a nástroji pro vyrovnávání zatížení najdete v tématu [co je protokol IPv6 pro Azure Virtual Network?](../virtual-network/ipv6-overview.md)  |
    | Předvolba směrování | Ponechte výchozí **síť Microsoft**. </br> Další informace o předvolbách směrování najdete v tématu [co je předvolby směrování (Preview)?](../virtual-network/routing-preference-overview.md). |

6. U zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**.

7. Na kartě **Revize + vytvořit** vyberte **vytvořit**.

## <a name="create-load-balancer-resources"></a>Vytvoření prostředků nástroje pro vyrovnávání zatížení

V této části nakonfigurujete:

* Nastavení nástroje pro vyrovnávání zatížení pro fond back-end adres.
* Sonda stavu.
* Pravidlo nástroje pro vyrovnávání zatížení.

### <a name="create-a-backend-pool"></a>Vytvoření back-endového fondu

Fond adres back-endu obsahuje IP adresy virtuálních (síťových rozhraní) připojených k nástroji pro vyrovnávání zatížení. 

Vytvořte fond back-end adres **myBackendPool** , který bude zahrnovat virtuální počítače pro internetovou komunikaci s vyrovnáváním zatížení.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a potom v seznamu prostředků vyberte **myLoadBalancer** .

2. V části **Nastavení** vyberte **back-end fondy** a pak vyberte **Přidat**.

3. Do pole název na stránce **Přidat fond back-end** serveru zadejte **myBackendPool** jako název vašeho back-end fondu a pak vyberte **Přidat**.

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Nástroj pro vyrovnávání zatížení monitoruje stav vaší aplikace s sondou stavu. 

Sonda stavu přidá nebo odebere virtuální počítače z nástroje pro vyrovnávání zatížení na základě jejich odpovědí na kontroly stavu. 

Vytvořte sondu stavu s názvem **myHealthProbe**, abyste mohli monitorovat stav virtuálních počítačů.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a potom v seznamu prostředků vyberte **myLoadBalancer** .

2. V části **Nastavení** vyberte **sondy stavu** a pak vyberte **Přidat**.
    
    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte **myHealthProbe**. |
    | Protokol | Vyberte **TCP**. |
    | Port | Zadejte **80**.|
    | Interval | Zadejte hodnotu **15** pro **interval** mezi pokusy o sondu v sekundách. |
    | Prahová hodnota pro poškozený stav | Vyberte **2** pro počet chybných **prahových hodnot** nebo po sobě jdoucích selhání sondy, ke kterým musí dojít, aby se virtuální počítač považoval za poškozený.|
   

3. Ponechte zbytek výchozí hodnoty a vyberte **Přidat**.

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení slouží k definování způsobu distribuce provozu do virtuálních počítačů. Nadefinujete konfiguraci IP adresy front-endu pro příchozí provoz a fond IP adres back-endu pro příjem provozu. Zdrojový a cílový port se definují v pravidle. 

V této části vytvoříte pravidlo nástroje pro vyrovnávání zatížení:

* S názvem **myHTTPRule**.
* Ve front-endu s názvem **LoadBalancerFrontEnd**.
* Naslouchá na **portu 80**.
* Směruje provoz s vyrovnáváním zatížení do back-endu s názvem **myBackendPool** na **portu 80**.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a potom v seznamu prostředků vyberte **myLoadBalancer** .

2. V části **Nastavení** vyberte **pravidla vyrovnávání zatížení** a pak vyberte **Přidat**.

3. Pomocí těchto hodnot můžete nakonfigurovat pravidlo vyrovnávání zatížení:
    
    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte **myHTTPRule**. |
    | Verze protokolu IP | Vybrat **IPv4** |
    | IP adresa front-endu | Vybrat **LoadBalancerFrontEnd** |
    | Protokol | Vyberte **TCP**. |
    | Port | Zadejte **80**.|
    | Back-endový port | Zadejte **80**. |
    | Back-endový fond | Vyberte **myBackendPool**.|
    | Sonda stavu | Vyberte **myHealthProbe**. |
    | Časový limit nečinnosti (minuty) | Zadejte **15** minut. |
    | Resetování protokolu TCP | Vyberte **Povoleno**. |
    | Překlad odchozích adres zdrojové sítě (SNAT) | Vybrat **(doporučeno) použít odchozí pravidla pro poskytování back-end členů fondu pro přístup k Internetu.** |

4. Ponechte zbytek výchozích hodnot a pak vyberte **OK**.

## <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

V této části vytvoříte virtuální síť a podsíť.

1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek > Sítě > Virtuální síť** nebo do vyhledávacího pole zadejte **Virtuální síť**.

2. Vyberte **Vytvořit**. 

3. V části **vytvořit virtuální síť** zadejte nebo vyberte tyto informace na kartě **základy** :

    | **Nastavení**          | **Hodnota**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Podrobnosti o projektu**  |                                                                 |
    | Předplatné     | Vyberte své předplatné Azure.                                  |
    | Skupina prostředků   | Vybrat **TutorPubLBNAT-RG** |
    | **Podrobnosti o instancích** |                                                                 |
    | Name             | Zadejte **myVNet**                                    |
    | Oblast           | Vyberte **východní USA** |

4. Vyberte kartu **IP adresy** nebo v dolní části stránky vyberte tlačítko **Další: IP adresy** .

5. Na kartě **IP adresy** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Adresní prostor protokolu IPv4 | Zadejte **10.1.0.0/16** |

6. V části **název podsítě** vyberte slovo **výchozí**.

7. V **Upravit podsíť** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Název podsítě | Zadejte **myBackendSubnet** |
    | Rozsah adres podsítě | Zadejte **10.1.0.0/24** |

8. Vyberte **Uložit**.

9. Vyberte kartu **zabezpečení** .

10. V části **BastionHost** vyberte **Povolit**. Zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Název bastionu | Zadejte **myBastionHost** |
    | Adresní prostor AzureBastionSubnet | Zadejte **10.1.1.0/24** |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. </br> Jako **název** zadejte **myBastionIP**. </br> Vyberte **OK**. |


11. Vyberte kartu **Revize + vytvořit** nebo vyberte tlačítko **Revize + vytvořit** .

12. Vyberte **Vytvořit**.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

V této části vytvoříte dva virtuální počítače (**myVM1** a **myVM2**) ve dvou různých zónách (**zóna 1** a **zóna 2**).

Tyto virtuální počítače se přidají do back-endového fondu nástroje pro vyrovnávání zatížení, který se vytvořil dříve.

1. V levé horní části portálu vyberte **vytvořit prostředek**  >    >  **virtuální počítač** Compute. 
   
2. V části **vytvořit virtuální počítač** zadejte nebo vyberte hodnoty na kartě **základy** :

    | Nastavení | Hodnota                                          |
    |-----------------------|----------------------------------|
    | **Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné Azure. |
    | Skupina prostředků | Vybrat **TutorPubLBNAT-RG** |
    | **Podrobnosti o instancích** |  |
    | Název virtuálního počítače | Zadejte **myVM1** |
    | Oblast | Vyberte **východní USA** |
    | Možnosti dostupnosti | Vybrat **zóny dostupnosti** |
    | Zóna dostupnosti | Vyberte **1** |
    | Image | Vyberte **Windows Server 2019 Datacenter** |
    | Instance Azure Spot | Ponechat výchozí |
    | Velikost | Vyberte velikost virtuálního počítače nebo použijte výchozí nastavení. |
    | **Účet správce** |  |
    | Uživatelské jméno | Zadejte uživatelské jméno. |
    | Heslo | Zadat heslo |
    | Potvrzení hesla | Znovu zadejte heslo. |
    | **Pravidla portů pro příchozí spojení** |  |
    | Veřejné příchozí porty | Vybrat **žádné** |

3. Vyberte kartu **síť** nebo vyberte **Další: disky** a **Další: síť**.
  
4. Na kartě sítě vyberte nebo zadejte:

    | Nastavení | Hodnota |
    |-|-|
    | **Síťové rozhraní** |  |
    | Virtuální síť | **myVNet** |
    | Podsíť | **myBackendSubnet** |
    | Veřejná IP adresa | Vyberte **Žádná**. |
    | Skupina zabezpečení sítě NIC | Výběr **Možnosti Upřesnit**|
    | Konfigurovat skupinu zabezpečení sítě | Vyberte, že chcete **vytvořit novou** IP adresu. </br> V části **vytvořit skupinu zabezpečení sítě** zadejte **MyNSG** do **pole název**. </br> V části **příchozí pravidla** vyberte **+ Přidat příchozí pravidlo**. </br> V části  **rozsahy cílových portů** zadejte **80**. </br> V části **Priorita** zadejte **100**. </br> Do **název** zadejte **myHTTPRule** </br> Vyberte **Přidat**. </br> Vyberte **OK**. |
    | **Vyrovnávání zatížení**  |
    | Umístit tento virtuální počítač za stávající řešení vyrovnávání zatížení? | Zaškrtněte toto políčko.|
    | **Nastavení vyrovnávání zatížení** |
    | Možnosti vyrovnávání zatížení | Výběr **služby Azure Load Balancer** |
    | Vyberte nástroj pro vyrovnávání zatížení. | Vybrat **myLoadBalancer**  |
    | Vybrat back-end fond | Vybrat **myBackendPool** |
   
5. Vyberte **Zkontrolovat a vytvořit**. 
  
6. Zkontrolujte nastavení a pak vyberte **vytvořit**.

7. Pokud chcete vytvořit virtuální počítač s následujícími hodnotami a všechna ostatní nastavení stejné jako **myVM1**, postupujte podle kroků 1 až 7:

    | Nastavení | VIRTUÁLNÍ POČÍTAČ 2 |
    | ------- | ----- |
    | Name |  **myVM2** |
    | Zóna dostupnosti | **2** |
    | Skupina zabezpečení sítě | Vybrat existující **myNSG**| 

## <a name="create-nat-gateway"></a>Vytvoření brány NAT

V této části vytvoříte bránu NAT a přiřadíte ji k podsíti ve virtuální síti, kterou jste předtím vytvořili.

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek > síť > NAT Gateway** nebo vyhledejte **bránu NAT** v poli hledání.

2. Vyberte **Vytvořit**. 

3. V části **vytvořit bránu pro překlad síťových adres (NAT)** zadejte nebo vyberte tyto informace na kartě **základy** :

    | **Nastavení**          | **Hodnota**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Podrobnosti o projektu**  |                                                                 |
    | Předplatné     | Vyberte své předplatné Azure.                                  |
    | Skupina prostředků   | Vyberte **TutorPubLBNAT-RG**. |
    | **Podrobnosti o instancích** |                                                                 |
    | Name             | Zadejte **myNATGateway**                                    |
    | Oblast           | Vyberte **(US) východní USA**  |
    | Zóna dostupnosti | Vyberte **Žádná**. |
    | Časový limit nečinnosti (minuty) | Zadejte **10**. |

4. Vyberte kartu **odchozí IP adresa** nebo klikněte na tlačítko **Další: odchozí IP adresa** v dolní části stránky.

5. Na kartě **odchozí IP adresa** zadejte nebo vyberte následující informace:

    | **Nastavení** | **Hodnota** |
    | ----------- | --------- |
    | Veřejné IP adresy | Vyberte **vytvořit novou veřejnou IP adresu**. </br> Do **název** zadejte **myPublicIP-NAT**. </br> Vyberte **OK**. |

6. Vyberte kartu **podsíť** nebo vyberte tlačítko **Další: podsíť** v dolní části stránky.

7. Na kartě **podsíť** vyberte v rozevíracím seznamu **virtuální síť** možnost **myVNet** .

8. Zaškrtněte políčko vedle **myBackendSubnet**.

9. Vyberte kartu **Revize + vytvořit** nebo v dolní části stránky vyberte tlačítko modrá **recenze + vytvořit** .

10. Vyberte **Vytvořit**.

## <a name="test-nat-gateway"></a>Test brány NAT

V této části otestujeme bránu NAT. Nejdříve zjistíme veřejnou IP adresu brány NAT. Pak se připojíme k testovacímu virtuálnímu počítači a ověříme odchozí připojení prostřednictvím brány NAT.
    
1. Na obrazovce **Přehled** vyhledejte veřejnou IP adresu pro bránu NAT. V nabídce na levé straně vyberte **všechny služby** a vyberte **všechny prostředky** a pak vyberte **myPublicIP**.

2. Poznamenejte si veřejnou IP adresu:

    :::image type="content" source="./media/tutorial-nat-gateway-load-balancer-public-portal/find-public-ip.png" alt-text="Snímek obrazovky se zjišťováním veřejné IP adresy brány NAT." border="true":::

3. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a potom ze seznamu prostředky vyberte **myVM1** , která je umístěná ve skupině prostředků **TutorPubLBNAT-RG** .

4. Na stránce **Přehled** vyberte **připojit** a pak **bastionu**.

5. Vyberte tlačítko modrého **použití bastionu** .

6. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

7. Otevřete **Internet Explorer** v **myVM1**.

8. **https://whatsmyip.com** Do adresního řádku zadejte.

9. Ověřte, že zobrazená IP adresa odpovídá adrese brány NAT, kterou jste si poznamenali v předchozím kroku:

    :::image type="content" source="./media/tutorial-nat-gateway-load-balancer-public-portal/my-ip.png" alt-text="Snímek obrazovky s Internet Explorerem, na kterém se zobrazuje externí odchozí IP adresa" border="true":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte virtuální síť, virtuální počítač a bránu NAT pomocí následujících kroků:

1. V nabídce na levé straně vyberte **skupiny prostředků**.

2. Vyberte skupinu prostředků **TutorPubLBNAT-RG** .

3. Vyberte **Odstranit skupinu prostředků**.

4. Zadejte **TutorPubLBNAT-RG** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Další informace o službě Azure Virtual Network NAT najdete v těchto tématech:
> [!div class="nextstepaction"]
> [Přehled služby Virtual Network NAT](nat-overview.md)
