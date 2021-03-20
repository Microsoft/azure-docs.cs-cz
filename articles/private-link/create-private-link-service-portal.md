---
title: Rychlý Start – vytvoření služby privátního propojení pomocí Azure Portal
titlesuffix: Azure Private Link
description: Naučte se vytvořit službu privátního propojení pomocí Azure Portal v tomto rychlém startu.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/18/2021
ms.author: allensu
ms.openlocfilehash: d394a475c5121607f70c03437382e104a5d0cbee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98746403"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Rychlý Start: vytvoření služby privátního propojení pomocí Azure Portal

Začněte vytvářet službu privátního propojení, která odkazuje na vaši službu.  Poskytněte soukromému odkazu přístup k vaší službě nebo prostředku nasazenému za službou Azure Standard Load Balancer.  Uživatelé vaší služby mají privátní přístup ze své virtuální sítě.

## <a name="prerequisites"></a>Předpoklady

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Vytvořte interní nástroj pro vyrovnávání zatížení.

V této části vytvoříte virtuální síť a interní Azure Load Balancer.

### <a name="virtual-network"></a>Virtuální síť

V této části vytvoříte virtuální síť a podsíť pro hostování nástroje pro vyrovnávání zatížení, který přistupuje ke službě privátního propojení.

1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek > Sítě > Virtuální síť** nebo do vyhledávacího pole zadejte **Virtuální síť**.

2. V části **vytvořit virtuální síť** zadejte nebo vyberte tyto informace na kartě **základy** :

    | **Nastavení**          | **Hodnota**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Podrobnosti o projektu**  |                                                                 |
    | Předplatné     | Vyberte své předplatné Azure.                                  |
    | Skupina prostředků   | Vybrat **CreatePrivLinkService-RG** |
    | **Podrobnosti o instancích** |                                                                 |
    | Name             | Zadejte **myVNet**                                    |
    | Oblast           | Vyberte **východní USA 2** |

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

8. Vyberte kartu **Revize + vytvořit** nebo vyberte tlačítko **Revize + vytvořit** .

9. Vyberte **Vytvořit**.

### <a name="create-a-standard-load-balancer"></a>Vytvoření standardního nástroje pro vyrovnávání zatížení

Pomocí portálu vytvořte standardní interní nástroj pro vyrovnávání zatížení. 

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek**  >  **síť**  >  **Load Balancer**.

2. Na kartě **základy** na stránce **vytvořit nástroj pro vyrovnávání zatížení** zadejte nebo vyberte následující informace: 

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte své předplatné.    |    
    | Skupina prostředků         | Vyberte **CreatePrivLinkService-RG** vytvořené v předchozím kroku.|
    | Name                   | Zadejte **myLoadBalancer**                                   |
    | Oblast         | Vyberte **USA – východ 2**.                                        |
    | Typ          | Vyberte **interní**.                                        |
    | SKU           | Vybrat **Standard** |
    | Virtuální síť | Vyberte **myVNet** vytvořené v předchozím kroku. |
    | Podsíť  | Vyberte **mySubnet** vytvořené v předchozím kroku. |
    | Přiřazení IP adresy | Vyberte **Dynamická**. |
    | Zóna dostupnosti | Vybrat **zónu – redundantní** |

3. U zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**.

4. Na kartě **Revize + vytvořit** vyberte **vytvořit**.   

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

4. Ponechte zbytek výchozích hodnot a pak vyberte **OK**.

## <a name="create-a-private-link-service"></a>Vytvoření služby privátního propojení

V této části vytvoříte službu privátního propojení za standardním nástrojem pro vyrovnávání zatížení.

1. V levé horní části stránky v Azure Portal vyberte **vytvořit prostředek**.

2. V poli **Hledat na webu Marketplace** vyhledejte **privátní odkaz** .

3. Vyberte **Vytvořit**.

4. V části **Přehled** v části **centrum privátních odkazů** vyberte tlačítko **služby Blue vytvořit privátní Link** .

5. Na kartě **základy** v části **vytvořit službu privátního propojení** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **CreatePrivLinkService-RG**. |
    | **Podrobnosti o instancích** |  |
    | Name | Zadejte **myPrivateLinkService**. |
    | Oblast | Vyberte **USA – východ 2**. |

6. Vyberte kartu **odchozí nastavení** nebo vyberte **Další: odchozí nastavení** v dolní části stránky.

7. Na kartě **odchozí nastavení** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Nástroj pro vyrovnávání zatížení | Vyberte **myLoadBalancer**. |
    | IP adresa front-endu služby Load Balancer | Vyberte **LoadBalancerFrontEnd (10.1.0.4)**. |
    | Zdrojová podsíť NAT | Vyberte **mySubnet (10.1.0.0/24)**. |
    | Povolit proxy server TCP v2 | Ponechte výchozí hodnotu **ne**. </br> Pokud vaše aplikace očekává hlavičku proxy serveru TCP v2, vyberte **Ano**. |
    | **Nastavení privátní IP adresy** |  |
    | Ponechte výchozí nastavení. |  |

8. Vyberte kartu **zabezpečení přístupu** nebo vyberte **Další: přístup k zabezpečení** v dolní části stránky.

9. Výchozí **řízení přístupu na základě role** ponechte jenom na kartě **zabezpečení přístupu** .

10. Vyberte kartu **značky** nebo vyberte **Další: značky** v dolní části stránky.

11. Vyberte kartu **Revize + vytvořit** nebo vyberte **Další:** v dolní části stránky klikněte na tlačítko Zobrazit a vytvořit.

12. Na kartě **Revize + vytvořit** vyberte **vytvořit** .

Vaše služba privátního propojení se vytvoří a může přijímat provoz. Pokud chcete zobrazit přenosové toky, nakonfigurujte svoji aplikaci za vaším standardním nástrojem pro vyrovnávání zatížení.


## <a name="create-private-endpoint"></a>Vytvořit privátní koncový bod

V této části namapujete službu privátního propojení na soukromý koncový bod. Virtuální síť obsahuje privátní koncový bod pro službu privátního propojení. Tato virtuální síť obsahuje prostředky, které budou mít přístup ke službě privátního propojení.

### <a name="create-private-endpoint-virtual-network"></a>Vytvořit virtuální síť privátního koncového bodu

1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek > Sítě > Virtuální síť** nebo do vyhledávacího pole zadejte **Virtuální síť**.

2. V části **vytvořit virtuální síť** zadejte nebo vyberte tyto informace na kartě **základy** :

    | **Nastavení**          | **Hodnota**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Podrobnosti o projektu**  |                                                                 |
    | Předplatné     | Vyberte své předplatné Azure.                                  |
    | Skupina prostředků   | Vybrat **CreatePrivLinkService-RG** |
    | **Podrobnosti o instancích** |                                                                 |
    | Name             | Zadejte **myVNetPE**                                    |
    | Oblast           | Vyberte **východní USA 2** |

3. Vyberte kartu **IP adresy** nebo v dolní části stránky vyberte tlačítko **Další: IP adresy** .

4. Na kartě **IP adresy** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Adresní prostor protokolu IPv4 | Zadejte **11.1.0.0/16** |

5. V části **název podsítě** vyberte slovo **výchozí**.

6. V **Upravit podsíť** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Název podsítě | Zadejte **mySubnetPE** |
    | Rozsah adres podsítě | Zadejte **11.1.0.0/24** |

7. Vyberte **Uložit**.

8. Vyberte kartu **Revize + vytvořit** nebo vyberte tlačítko **Revize + vytvořit** .

9. Vyberte **Vytvořit**.

### <a name="create-private-endpoint"></a>Vytvořit privátní koncový bod

1. V levé horní části obrazovky na portálu vyberte vytvořit privátní síťové připojení **k prostředkům**  >    >  nebo zadejte do vyhledávacího pole **privátní odkaz**.

2. Vyberte **Vytvořit**.

3. V **centru privátních odkazů** vyberte v nabídce vlevo možnost **privátní koncové body** .

4. V **privátních koncových bodech** vyberte **+ Přidat**.

5. Na kartě **základy** pro **Vytvoření privátního koncového bodu** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **CreatePrivLinkService-RG**. Tuto skupinu prostředků jste vytvořili v předchozí části.|
    | **Podrobnosti o instancích** |  |
    | Name  | Zadejte **myPrivateEndpoint**. |
    | Oblast | Vyberte **USA – východ 2**. |

6. Vyberte kartu **prostředek** nebo tlačítko **Další: prostředek** ve spodní části stránky.
    
7. V **prostředku** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Způsob připojení | **V adresáři vyberte připojit k prostředku Azure**. |
    | Předplatné | Vyberte své předplatné. |
    | Typ prostředku | Vyberte **Microsoft. Network/privateLinkServices**. |
    | Prostředek | Vyberte **myPrivateLinkService**. |

8. V dolní části obrazovky vyberte kartu **Konfigurace** nebo tlačítko **Další: Konfigurace** .

9. V **konfiguraci** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Sítě** |  |
    | Virtual Network | Vyberte **myVNetPE**. |
    | Podsíť | Vyberte **mySubnetPE**. |

10. Vyberte kartu **Revize + vytvořit** nebo klikněte na tlačítko **Revize + vytvořit** v dolní části obrazovky.

11. Vyberte **Vytvořit**.

### <a name="ip-address-of-private-endpoint"></a>IP adresa privátního koncového bodu

V této části najdete IP adresu privátního koncového bodu, který odpovídá nástroji pro vyrovnávání zatížení a ke službě privátního propojení.

1. V levém sloupci Azure Portal vyberte **skupiny prostředků**.

2. Vyberte skupinu prostředků **CreatePrivLinkService-RG** .

3. Ve skupině prostředků **CreatePrivLinkService-RG** vyberte **myPrivateEndpoint**.

4. Na stránce **Přehled** v **myPrivateEndpoint** vyberte název síťového rozhraní přidruženého k privátnímu koncovému bodu.  Název síťového rozhraní začíná řetězcem **myPrivateEndpoint. nic**.

5. Na stránce **Přehled** karty privátního koncového bodu se IP adresa koncového bodu zobrazuje v **privátní IP adrese**.
    

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s použitím služby privátního propojení hotovi, odstraňte skupinu prostředků, abyste mohli vyčistit prostředky používané v tomto rychlém startu.

1. Do vyhledávacího pole v horní části portálu zadejte **CreatePrivLinkService-RG** a z výsledků hledání vyberte **CreatePrivLinkService-RG** .
1. Vyberte **Odstranit skupinu prostředků**.
1. Do pole **Zadejte název skupiny prostředků** zadejte **CreatePrivLinkService-RG**.
1. Vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu:

* Vytvořila se virtuální síť a interní Azure Load Balancer.
* Byla vytvořena služba privátního propojení.
* Byla vytvořena virtuální síť a privátní koncový bod pro službu privátního propojení.

Pokud chcete získat další informace o privátním koncovém bodu Azure, přejděte na:
> [!div class="nextstepaction"]
> [Rychlý Start: Vytvoření privátního koncového bodu pomocí Azure Portal](create-private-endpoint-portal.md)