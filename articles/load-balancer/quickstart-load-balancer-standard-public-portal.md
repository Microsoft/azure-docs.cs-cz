---
title: 'Rychlý Start: vytvoření veřejného nástroje pro vyrovnávání zatížení – Azure Portal'
titleSuffix: Azure Load Balancer
description: V tomto rychlém startu se dozvíte, jak vytvořit nástroj pro vyrovnávání zatížení pomocí Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 634f09c7862f6e3e2f147094503f5a574476ef91
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034383"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Rychlý Start: vytvoření veřejného nástroje pro vyrovnávání zatížení virtuálních počítačů pomocí Azure Portal

Začněte s Azure Load Balancer pomocí Azure Portal k vytvoření veřejného nástroje pro vyrovnávání zatížení a tří virtuálních počítačů.

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

---

# <a name="standard-sku"></a>[**Standardní SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Pro produkční úlohy se doporučuje používat nástroj pro vyrovnávání zatížení Standard SKU.  Další informace o SKU najdete v tématu **[Azure Load Balancer SKU](skus.md)**.

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram.png" alt-text="Prostředky služby Load Balancer úrovně Standard vytvořené pro rychlý Start." border="false":::

*Obrázek: prostředky vytvořené v rychlém startu.*

V této části vytvoříte Nástroj pro vyrovnávání zatížení, který vyrovnává zatížení virtuálních počítačů. 

Při vytváření veřejného nástroje pro vyrovnávání zatížení vytvoříte novou veřejnou IP adresu, která je ve výchozím nastavení nakonfigurována jako front-end (s názvem jako **LoadBalancerFrontend** ) pro nástroj pro vyrovnávání zatížení.

1. Vyberte **Vytvořit prostředek**. 
2. Do vyhledávacího pole zadejte **Nástroj pro vyrovnávání zatížení**. Ve výsledcích hledání vyberte **Nástroj pro vyrovnávání zatížení** .
3. Na stránce **Vyrovnávání zatížení** vyberte **vytvořit**.
4. Na stránce **vytvořit nástroj pro vyrovnávání zatížení** zadejte nebo vyberte následující informace: 

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte své předplatné.    |    
    | Skupina prostředků         | Vyberte **vytvořit novou** a do textového pole zadejte **CreatePubLBQS-RG** .|
    | Name                   | Zadejte **myLoadBalancer**                                   |
    | Oblast         | Vyberte **(Evropa) západní Evropa**.                                        |
    | Typ          | Vyberte **Veřejný**.                                        |
    | SKU           | Ponechte výchozí **Standard**. |
    | Úroveň          | Ponechte výchozí **oblast**. |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. Pokud máte existující veřejnou IP adresu, kterou byste chtěli použít, vyberte **použít existující**. |
    | Název veřejné IP adresy | Do textového pole zadejte **myPublicIP** .|
    | Zóna dostupnosti | Chcete-li vytvořit odolný Nástroj pro vyrovnávání zatížení, vyberte **zónu – redundantní** . Pokud chcete vytvořit nástroj pro vyrovnávání zatížení, vyberte konkrétní zónu z 1, 2 nebo 3. |
    | Přidat veřejnou IPv6 adresu | Vyberte **Ne**. </br> Další informace o adresách IPv6 a nástroji pro vyrovnávání zatížení najdete v tématu [co je protokol IPv6 pro Azure Virtual Network?](../virtual-network/ipv6-overview.md)  |
    | Předvolba směrování | Ponechte výchozí **síť Microsoft**. </br> Další informace o předvolbách směrování najdete v tématu [co je předvolby směrování (Preview)?](../virtual-network/routing-preference-overview.md). |

5. U zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**.

6. Na kartě **Revize + vytvořit** vyberte **vytvořit**.   
    
    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png" alt-text="Vytvoření standardního nástroje pro vyrovnávání zatížení" border="true":::
 
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
    | Protokol | Vyberte **http**. |
    | Port | Zadejte **80**.|
    | Interval | Zadejte hodnotu **15** pro **interval** mezi pokusy o sondu v sekundách. |
    | Prahová hodnota pro poškozený stav | Vyberte **2** pro počet chybných **prahových hodnot** nebo po sobě jdoucích selhání sondy, ke kterým musí dojít, aby se virtuální počítač považoval za poškozený.|
    | | |

3. Ponechte zbytek výchozí hodnoty a vyberte **OK**.

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
    | Časový limit nečinnosti (minuty) | Přesuňte posuvník na **15** minut. |
    | Resetování protokolu TCP | Vyberte **Povoleno**. |
    | Překlad odchozích adres zdrojové sítě (SNAT) | Vybrat **(doporučeno) použít odchozí pravidla pro poskytování back-end členů fondu pro přístup k Internetu.** |

4. Ponechte zbytek výchozích hodnot a pak vyberte **OK**.

## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V této části:

* Vytvořte virtuální síť.
* Vytvořte tři virtuální počítače pro back-end fond nástroje pro vyrovnávání zatížení.
* K otestování nástroje pro vyrovnávání zatížení nainstalujte na virtuálních počítačích službu IIS.

## <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

V této části vytvoříte virtuální síť a podsíť.

1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek > Sítě > Virtuální síť** nebo do vyhledávacího pole zadejte **Virtuální síť**.

2. V části **vytvořit virtuální síť** zadejte nebo vyberte tyto informace na kartě **základy** :

    | **Nastavení**          | **Hodnota**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Podrobnosti o projektu**  |                                                                 |
    | Předplatné     | Vyberte své předplatné Azure.                                  |
    | Skupina prostředků   | Vybrat **CreatePubLBQS-RG** |
    | **Podrobnosti o instancích** |                                                                 |
    | Name             | Zadejte **myVNet**                                    |
    | Oblast           | Vyberte **západní Evropa** |

3. Vyberte kartu **IP adresy** nebo v dolní části stránky vyberte tlačítko **Další: IP adresy** .

4. Na kartě **IP adresy** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Adresní prostor protokolu IPv4 | Zadejte **10.1.0.0/16** |

5. V části **název podsítě** vyberte slovo **výchozí**.

6. V **Upravit podsíť** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Název podsítě | Zadejte **myBackendSubnet** |
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

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

V této části vytvoříte tři virtuální počítače (**myVM1**, **myVM2** a **myVM3**) ve třech různých zónách (**zóna 1**, **zóna 2** a **zóna 3**). 

Tyto virtuální počítače se přidají do back-endového fondu nástroje pro vyrovnávání zatížení, který se vytvořil dříve.

1. V levé horní části portálu vyberte **vytvořit prostředek**  >    >  **virtuální počítač** Compute. 
   
2. V části **vytvořit virtuální počítač** zadejte nebo vyberte hodnoty na kartě **základy** :

    | Nastavení | Hodnota                                          |
    |-----------------------|----------------------------------|
    | **Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné Azure. |
    | Skupina prostředků | Vybrat **CreatePubLBQS-RG** |
    | **Podrobnosti o instancích** |  |
    | Název virtuálního počítače | Zadejte **myVM1** |
    | Oblast | Vyberte **západní Evropa** |
    | Možnosti dostupnosti | Vybrat **zóny dostupnosti** |
    | Zóna dostupnosti | Vyberte **1** |
    | Image | Vyberte **Windows Server 2019 Datacenter** |
    | Instance Azure Spot | Vybrat **ne** |
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
    | Umístit tento virtuální počítač za existující řešení vyrovnávání zatížení? | Vyberte **Ano**. |
    | **Nastavení vyrovnávání zatížení** |
    | Možnosti vyrovnávání zatížení | Vybrat **Vyrovnávání zatížení Azure** |
    | Vyberte nástroj pro vyrovnávání zatížení. | Vybrat **myLoadBalancer**  |
    | Vybrat back-end fond | Vybrat **myBackendPool** |

5. Vyberte kartu **Správa** nebo vyberte možnost **Další**  >  **Správa**.

6. Na kartě **Správa** vyberte nebo zadejte:
    
    | Nastavení | Hodnota |
    |-|-|
    | **Monitorování** |  |
    | Diagnostika spouštění | Vybrat **vypnuto** |
   
7. Vyberte **Zkontrolovat a vytvořit**. 
  
8. Zkontrolujte nastavení a pak vyberte **vytvořit**.

9. Postupujte podle kroků 1 až 8 a vytvořte dva další virtuální počítače s následujícími hodnotami a všechna ostatní nastavení, která jsou stejná jako **myVM1**:

    | Nastavení | VIRTUÁLNÍ POČÍTAČ 2| VIRTUÁLNÍ POČÍTAČ 3|
    | ------- | ----- |---|
    | Name |  **myVM2** |**myVM3**|
    | Zóna dostupnosti | **2** |**3**|
    | Skupina zabezpečení sítě | Vybrat existující **myNSG**| Vybrat existující **myNSG**|

## <a name="create-outbound-rule-configuration"></a>Vytvořit konfiguraci odchozího pravidla
Odchozí pravidla nástroje pro vyrovnávání zatížení konfigurují odchozí SNAT pro virtuální počítače ve fondu back-endu. 

Další informace o odchozích připojeních najdete v tématu [odchozí připojení v Azure](load-balancer-outbound-connections.md).

### <a name="create-outbound-rule"></a>Vytvořit odchozí pravidlo

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a potom v seznamu prostředků vyberte **myLoadBalancer** .

2. V části **Nastavení** vyberte **odchozí pravidla** a pak vyberte **Přidat**.

3. Pomocí těchto hodnot nakonfigurujte odchozí pravidla:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte **myOutboundRule**. |
    | IP adresa front-endu | Vyberte, že chcete **vytvořit novou** IP adresu. </br> Do **název** zadejte **LoadBalancerFrontEndOutbound**. </br> Vyberte **IP adresu** nebo **předponu IP** adresy. </br> V části **Veřejná IP adresa** nebo **předpona veřejné IP** adresy vyberte **vytvořit novou** . </br> Jako název zadejte  **myPublicIPOutbound** nebo **myPublicIPPrefixOutbound**. </br> Vyberte **Přidat**.|
    | Časový limit nečinnosti (minuty) | Přesuňte posuvník na **15 minut**.|
    | Resetování protokolu TCP | Vyberte **Povoleno**.|
    | Back-endový fond | Vyberte, že chcete **vytvořit novou** IP adresu. </br> Jako **název** zadejte **myBackendPoolOutbound** . </br> Vyberte **Přidat**. |
    | Přidělování portů-> přidělování portů | Vyberte možnost **ručně vybrat počet odchozích portů** . |
    | Odchozí porty – > zvolit podle | Vybrat **porty na instanci** |
    | Odchozí porty – > porty na instanci | Zadejte **10000**. |

4. Vyberte **Přidat**.

### <a name="add-virtual-machines-to-outbound-pool"></a>Přidat virtuální počítače do odchozího fondu

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a potom v seznamu prostředků vyberte **myLoadBalancer** .

2. V části **Nastavení** vyberte **back-endové fondy**.

3. Vyberte **myBackendPoolOutbound**.

4. Ve **virtuální síti** vyberte **myVNet**.

5. Na **virtuální počítače** vyberte **+ Přidat**.

6. Zaškrtněte políčka vedle **myVM1**, **myVM2** a **myVM3**. 

7. Vyberte **Přidat**.

8. Vyberte **Uložit**.

# <a name="basic-sku"></a>[**Základní SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Pro produkční úlohy se doporučuje používat nástroj pro vyrovnávání zatížení Standard SKU.  Další informace o SKU najdete v tématu **[Azure Load Balancer SKU](skus.md)**.

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram-basic.png" alt-text="Základní prostředky nástroje pro vyrovnávání zatížení vytvořené v rychlém startu." border="false":::

*Obrázek: prostředky vytvořené v rychlém startu.*

V této části vytvoříte Nástroj pro vyrovnávání zatížení, který vyrovnává zatížení virtuálních počítačů. 

Při vytváření veřejného nástroje pro vyrovnávání zatížení vytvoříte novou veřejnou IP adresu, která je ve výchozím nastavení nakonfigurována jako front-end (s názvem jako **LoadBalancerFrontend** ) pro nástroj pro vyrovnávání zatížení.

1. Vyberte **Vytvořit prostředek**. 
2. Do vyhledávacího pole zadejte **Nástroj pro vyrovnávání zatížení**. Ve výsledcích hledání vyberte **Nástroj pro vyrovnávání zatížení** .
3. Na stránce **Vyrovnávání zatížení** vyberte **vytvořit**.
4. Na stránce **vytvořit nástroj pro vyrovnávání zatížení** zadejte nebo vyberte následující informace: 

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte své předplatné.    |    
    | Skupina prostředků         | Vyberte **vytvořit nové** a do textového pole zadejte **CreatePubLBQS-RG** .|
    | Name                   | Zadejte **myLoadBalancer**                                   |
    | Oblast         | Vyberte **Západní Evropa**.                                        |
    | Typ          | Vyberte **Veřejný**.                                        |
    | SKU           | Vybrat **základní** |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. Pokud máte existující veřejnou IP adresu, kterou byste chtěli použít, vyberte **použít existující**. |
    | Název veřejné IP adresy | Do textového pole zadejte **myPublicIP** .|
    | Přiřazení | Vybrat **dynamický** |
    | Přidat veřejnou IPv6 adresu | Vyberte **Ne**. </br> Další informace o adresách IPv6 a nástroji pro vyrovnávání zatížení najdete v tématu [co je protokol IPv6 pro Azure Virtual Network?](../virtual-network/ipv6-overview.md)  |

5. U zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**.

6. Na kartě **Revize + vytvořit** vyberte **vytvořit**.   

    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-basic-load-balancer.png" alt-text="Vytvoření základního nástroje pro vyrovnávání zatížení" border="true":::

## <a name="create-load-balancer-resources"></a>Vytvoření prostředků nástroje pro vyrovnávání zatížení

V této části nakonfigurujete:

* Vytvořte virtuální síť.
* Nastavení nástroje pro vyrovnávání zatížení pro fond back-end adres.
* Sonda stavu.
* Pravidlo nástroje pro vyrovnávání zatížení.

## <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

V této části vytvoříte virtuální síť a podsíť.

1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek > Sítě > Virtuální síť** nebo do vyhledávacího pole zadejte **Virtuální síť**.

2. V části **vytvořit virtuální síť** zadejte nebo vyberte tyto informace na kartě **základy** :

    | **Nastavení**          | **Hodnota**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Podrobnosti o projektu**  |                                                                 |
    | Předplatné     | Vyberte své předplatné Azure.                                  |
    | Skupina prostředků   | Vybrat **CreatePubLBQS-RG** |
    | **Podrobnosti o instancích** |                                                                 |
    | Name             | Zadejte **myVNet**                                    |
    | Oblast           | Vyberte **západní Evropa** |

3. Vyberte kartu **IP adresy** nebo v dolní části stránky vyberte tlačítko **Další: IP adresy** .

4. Na kartě **IP adresy** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Adresní prostor protokolu IPv4 | Zadejte **10.1.0.0/16** |

5. V části **název podsítě** vyberte slovo **výchozí**.

6. V **Upravit podsíť** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Název podsítě | Zadejte **myBackendSubnet** |
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
### <a name="create-a-backend-pool"></a>Vytvoření back-endového fondu

Fond adres back-endu obsahuje IP adresy virtuálních (síťových rozhraní) připojených k nástroji pro vyrovnávání zatížení. 

Vytvořte fond back-end adres **myBackendPool** , který bude zahrnovat virtuální počítače pro internetovou komunikaci s vyrovnáváním zatížení.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a potom v seznamu prostředků vyberte **myLoadBalancer** .

2. V části **Nastavení** vyberte **back-end fondy** a pak vyberte **Přidat**.

3. Na stránce **Přidat back-end fond** zadejte nebo vyberte:
    
    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte **myBackendPool**. |
    | Virtuální síť | Vyberte **myVNet**. |
    | Přidruženo k | Vybrat **virtuální počítače** |

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Nástroj pro vyrovnávání zatížení monitoruje stav vaší aplikace s sondou stavu. 

Sonda stavu přidá nebo odebere virtuální počítače z nástroje pro vyrovnávání zatížení na základě jejich odpovědí na kontroly stavu. 

Vytvořte sondu stavu s názvem **myHealthProbe**, abyste mohli monitorovat stav virtuálních počítačů.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a potom v seznamu prostředků vyberte **myLoadBalancer** .

2. V části **Nastavení** vyberte **sondy stavu** a pak vyberte **Přidat**.
    
    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte **myHealthProbe**. |
    | Protokol | Vyberte **http**. |
    | Port | Zadejte **80**.|
    | Cesta | Napište **/** |
    | Interval | Zadejte hodnotu **15** pro **interval** mezi pokusy o sondu v sekundách. |
    | Prahová hodnota pro poškozený stav | Vyberte **2** pro počet chybných **prahových hodnot** nebo po sobě jdoucích selhání sondy, ke kterým musí dojít, aby se virtuální počítač považoval za poškozený.|

3. Vyberte **OK**.

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
    | Časový limit nečinnosti (minuty) | Přesuňte posuvník na **15** minut. |
 
4. Ponechte zbytek výchozích hodnot a pak vyberte **OK**.

## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V této části:

* Vytvořte tři virtuální počítače pro back-end fond nástroje pro vyrovnávání zatížení.
* Vytvořte skupinu dostupnosti pro virtuální počítače.
* K otestování nástroje pro vyrovnávání zatížení nainstalujte na virtuálních počítačích službu IIS.

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

V této části vytvoříte tři virtuální počítače (**myVM1**, **myVM2** a **MYVM3**) se základní veřejnou IP adresou.  

Tři virtuální počítače se přidají do skupiny dostupnosti s názvem **myAvailabilitySet**.

Tyto virtuální počítače se přidají do back-endového fondu nástroje pro vyrovnávání zatížení, který se vytvořil dříve.

1. V levé horní části portálu vyberte **vytvořit prostředek**  >    >  **virtuální počítač** Compute. 
   
2. V části **vytvořit virtuální počítač** zadejte nebo vyberte hodnoty na kartě **základy** :

    | Nastavení | Hodnota                                          |
    |-----------------------|----------------------------------|
    | **Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné Azure. |
    | Skupina prostředků | Vybrat **CreatePubLBQS-RG** |
    | **Podrobnosti o instancích** |  |
    | Název virtuálního počítače | Zadejte **myVM1** |
    | Oblast | Vyberte **západní Evropa** |
    | Možnosti dostupnosti | Vyberte **Skupina dostupnosti**. |
    | Skupina dostupnosti | Vyberte, že chcete **vytvořit novou** IP adresu. </br> Jako **název** zadejte **myAvailabilitySet** . </br> Vyberte **OK**. |
    | Image | **Windows Server 2019 Datacenter** |
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
    | Virtuální síť | Vybrat **myVNet** |
    | Podsíť | Vybrat **myBackendSubnet** |
    | Veřejná IP adresa | Vybrat **žádné** |
    | Skupina zabezpečení sítě NIC | Výběr **Možnosti Upřesnit**|
    | Konfigurovat skupinu zabezpečení sítě | Vyberte, že chcete **vytvořit novou** IP adresu. </br> V části **vytvořit skupinu zabezpečení sítě** zadejte **MyNSG** do **pole název**. </br> V části **příchozí pravidla** vyberte **+ Přidat příchozí pravidlo**. </br> V části  **rozsahy cílových portů** zadejte **80**. </br> V části **Priorita** zadejte **100**. </br> Do **název** zadejte **myHTTPRule** </br> Vyberte **Přidat**. </br> Vyberte **OK**. |
    | **Vyrovnávání zatížení**  |
    | Umístit tento virtuální počítač za existující řešení vyrovnávání zatížení? | Vybrat **ne** |
 
5. Vyberte kartu **Správa** nebo vyberte možnost **Další**  >  **Správa**.

6. Na kartě **Správa** vyberte nebo zadejte:
    
    | Nastavení | Hodnota |
    |---|---|
    | **Monitorování** | |
    | Diagnostika spouštění | Vybrat **vypnuto** |

7. Vyberte **Zkontrolovat a vytvořit**. 
  
8. Zkontrolujte nastavení a pak vyberte **vytvořit**.

9. Postupujte podle kroků 1 až 8 a vytvořte dva další virtuální počítače s následujícími hodnotami a všechna ostatní nastavení, která jsou stejná jako **myVM1**:

    | Nastavení | VIRTUÁLNÍ POČÍTAČ 2| VIRTUÁLNÍ POČÍTAČ 3|
    | ------- | ----- |---|
    | Name |  **myVM2** |**myVM3**|
    | Skupina dostupnosti| Vybrat **myAvailabilitySet** | Vybrat **myAvailabilitySet**|
    | Skupina zabezpečení sítě | Vybrat existující **myNSG**| Vybrat existující **myNSG**|

### <a name="add-virtual-machines-to-the-backend-pool"></a>Přidání virtuálních počítačů do back-endového fondu

Virtuální počítače vytvořené v předchozích krocích se musí přidat do back-endu fondu **myLoadBalancer**.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a potom v seznamu prostředků vyberte **myLoadBalancer** .

2. V části **Nastavení** vyberte **back-end fondy** a pak vyberte **myBackendPool**.

3. Vyberte **virtuální počítače** v nástroji **přidruženo k**.

4. V části **virtuální počítače** vyberte **+ Přidat**.

5. Zaškrtněte políčka vedle **myVM1**, **myVM2** a **myVM3**.

6. Vyberte **Přidat**.

7. Vyberte **Uložit**.

---

## <a name="install-iis"></a>Instalace služby IIS

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a potom ze seznamu prostředky vyberte **myVM1** , která je umístěná ve skupině prostředků **CreatePubLBQS-RG** .

2. Na stránce **Přehled** vyberte **připojit** a pak **bastionu**.

4. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

5. Vyberte **Connect** (Připojit).

6. Na ploše serveru přejděte do části **Nástroje pro správu Windows**  >  **Windows PowerShell**.

7. V okně PowerShellu spusťte následující příkazy pro:

    * Instalace serveru služby IIS
    * Odebrat výchozí soubor iisstart.htm
    * Přidejte nový soubor iisstart.htm, který zobrazuje název virtuálního počítače:

   ```powershell
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
8. Zavřete relaci bastionu s **myVM1**.

9. Opakováním kroků 1 až 6 nainstalujte službu IIS a aktualizovaný soubor iisstart.htm na **myVM2** a **myVM3**.

## <a name="test-the-load-balancer"></a>Testování Load Balanceru

1. Vyhledejte veřejnou IP adresu nástroje pro vyrovnávání zatížení na obrazovce **Přehled** . V nabídce na levé straně vyberte **všechny služby** a vyberte **všechny prostředky** a pak vyberte **myPublicIP**.

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. V prohlížeči se zobrazí výchozí stránka webového serveru služby IIS.

   ![Webový server služby IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Pokud chcete zobrazit distribuci provozu nástroje pro vyrovnávání zatížení napříč všemi třemi virtuálními počítači, můžete přizpůsobit výchozí stránku webového serveru IIS každého virtuálního počítače a potom vynutit aktualizaci webového prohlížeče z klientského počítače.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, nástroj pro vyrovnávání zatížení a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků **CreatePubLBQS-RG** , která obsahuje prostředky, a pak vyberete **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu:

* Vytvořili Load Balancer Azure Standard nebo Basic.
* K nástroji pro vyrovnávání zatížení se připojily 3 virtuální počítače.
* Nakonfigurovali jste pravidlo provozu nástroje pro vyrovnávání zatížení, sondu stavu a pak otestovali Nástroj pro vyrovnávání zatížení. 

Pokud se chcete dozvědět víc o Azure Load Balancer, pokračujte tady:
> [!div class="nextstepaction"]
> [Co je Azure Load Balancer?](load-balancer-overview.md)