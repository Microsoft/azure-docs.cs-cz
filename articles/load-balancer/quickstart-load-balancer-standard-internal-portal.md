---
title: 'Rychlý Start: vytvoření interního nástroje pro vyrovnávání zatížení – Azure Portal'
titleSuffix: Azure Load Balancer
description: V tomto rychlém startu se dozvíte, jak vytvořit interní nástroj pro vyrovnávání zatížení pomocí Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a internal load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 14203021846e97a53f59c3bc24a1586774613dec
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704329"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Rychlý Start: vytvoření interního nástroje pro vyrovnávání zatížení virtuálních počítačů pomocí Azure Portal

Začněte s Azure Load Balancer pomocí Azure Portal k vytvoření interního nástroje pro vyrovnávání zatížení a tří virtuálních počítačů.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

---

# <a name="standard-sku"></a>[**Standardní SKU**](#tab/option-1-create-internal-load-balancer-standard)

>[!NOTE]
>Pro produkční úlohy se doporučuje používat nástroj pro vyrovnávání zatížení Standard SKU.  Další informace o SKU najdete v tématu **[Azure Load Balancer SKU](skus.md)**.

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Prostředky služby Load Balancer úrovně Standard vytvořené pro rychlý Start." border="false":::

V této části vytvoříte Nástroj pro vyrovnávání zatížení, který vyrovnává zatížení virtuálních počítačů. 

Při vytváření interního nástroje pro vyrovnávání zatížení je virtuální síť nakonfigurovaná jako síť pro nástroj pro vyrovnávání zatížení. 

Privátní IP adresa ve virtuální síti je ve výchozím nastavení pro nástroj pro vyrovnávání zatížení nakonfigurovaná jako front-end (s názvem jako **LoadBalancerFrontend** ). 

IP adresa front-endu může být **statická** nebo **Dynamická**.

## <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

V této části vytvoříte virtuální síť a podsíť.

1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek > Sítě > Virtuální síť** nebo do vyhledávacího pole zadejte **Virtuální síť**.

2. V části **vytvořit virtuální síť** zadejte nebo vyberte tyto informace na kartě **základy** :

    | **Nastavení**          | **Hodnota**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Podrobnosti o projektu**  |                                                                 |
    | Předplatné     | Vyberte své předplatné Azure.                                  |
    | Skupina prostředků   | Vybrat **CreateIntLBQS-RG** |
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

## <a name="create-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek**  >  **síť**  >  **Load Balancer**.

2. Na kartě **základy** na stránce **vytvořit nástroj pro vyrovnávání zatížení** zadejte nebo vyberte následující informace: 

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte své předplatné.    |    
    | Skupina prostředků         | Vyberte **CreateIntLBQS-RG** vytvořené v předchozím kroku.|
    | Název                   | Zadejte **myLoadBalancer**                                   |
    | Oblast         | Vyberte **Západní Evropa**.                                        |
    | Typ          | Vyberte **interní**.                                        |
    | SKU           | Vybrat **Standard** |
    | Virtuální síť | Vyberte **myVNet** vytvořené v předchozím kroku. |
    | Podsíť  | Vyberte **myBackendSubnet** vytvořené v předchozím kroku. |
    | Přiřazení IP adresy | Vyberte **Dynamická**. |
    | Zóna dostupnosti | Vybrat **zónu – redundantní** |

3. U zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**.

4. Na kartě **Revize + vytvořit** vyberte **vytvořit**.   
    
    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-standard-internal-load-balancer.png" alt-text="Vytvořte standardní interní nástroj pro vyrovnávání zatížení." border="true":::
 
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

>[!NOTE]
>Virtuální počítače ve fondu back-end nebudou mít odchozí připojení k Internetu s touto konfigurací. </br> Další informace o poskytování odchozího připojení najdete v tématech: </br> **[Odchozí připojení v Azure](load-balancer-outbound-connections.md)**</br> Možnosti pro poskytování připojení: </br> **[Konfigurace nástroje pro vyrovnávání zatížení – pouze odchozí](egress-only.md)** </br> **[Co je Virtual Network NAT?](../virtual-network/nat-overview.md)**

## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V této části:

* Vytvořte tři virtuální počítače pro back-end fond nástroje pro vyrovnávání zatížení.
* K otestování nástroje pro vyrovnávání zatížení nainstalujte na virtuálních počítačích službu IIS.

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

V této části vytvoříte tři virtuální počítače (**myVM1**, **myVM2** a **myVM3**).

Tyto virtuální počítače se přidají do back-endového fondu nástroje pro vyrovnávání zatížení, který se vytvořil dříve.

1. V levé horní části portálu vyberte **vytvořit prostředek**  >    >  **virtuální počítač** Compute. 
   
2. V části **vytvořit virtuální počítač** zadejte nebo vyberte hodnoty na kartě **základy** :

    | Nastavení | Hodnota                                          |
    |-----------------------|----------------------------------|
    | **Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné Azure. |
    | Skupina prostředků | Vybrat **CreateIntLBQS-RG** |
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

3. Vyberte kartu **síť** nebo vyberte **Další: disky** a **Další: síť**.
  
4. Na kartě sítě vyberte nebo zadejte:

    | Nastavení | Hodnota |
    |-|-|
    | **Síťové rozhraní** |  |
    | Virtuální síť | **myVNet** |
    | Podsíť | **myBackendSubnet** |
    | Veřejná IP adresa | Vybrat **žádné** |
    | Skupina zabezpečení sítě NIC | Výběr **Možnosti Upřesnit**|
    | Konfigurovat skupinu zabezpečení sítě | Vyberte, že chcete **vytvořit novou** IP adresu. </br> V části **vytvořit skupinu zabezpečení sítě** zadejte **MyNSG** do **pole název**. </br> Vyberte **OK**. |
    | **Vyrovnávání zatížení**  |
    | Umístit tento virtuální počítač za existující řešení vyrovnávání zatížení? | Vyberte **Ano**. |
    | **Nastavení vyrovnávání zatížení** |
    | Možnosti vyrovnávání zatížení | Vybrat **Vyrovnávání zatížení Azure** |
    | Vyberte nástroj pro vyrovnávání zatížení. | Vybrat **myLoadBalancer**  |
    | Vybrat back-end fond | Vybrat **myBackendPool** |
   
5. Vyberte **Zkontrolovat a vytvořit**. 
  
6. Zkontrolujte nastavení a pak vyberte **vytvořit**.

7. Postupujte podle kroků 1 až 8 a vytvořte dva další virtuální počítače s následujícími hodnotami a všechna ostatní nastavení, která jsou stejná jako **myVM1**:

    | Nastavení | VIRTUÁLNÍ POČÍTAČ 2 | VIRTUÁLNÍ POČÍTAČ 3 |
    | ------- | ----- | ---- |
    | Název |  **myVM2** | **myVM3** |
    | Zóna dostupnosti | **2** | **3** |
    | Skupina zabezpečení sítě | Vybrat existující **myNSG**| Vybrat existující **myNSG** |


# <a name="basic-sku"></a>[**Základní SKU**](#tab/option-1-create-internal-load-balancer-basic)

>[!NOTE]
>Pro produkční úlohy se doporučuje používat nástroj pro vyrovnávání zatížení Standard SKU.  Další informace o SKU najdete v tématu **[Azure Load Balancer SKU](skus.md)**.

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Základní prostředky nástroje pro vyrovnávání zatížení vytvořené v rychlém startu." border="false":::

V této části vytvoříte Nástroj pro vyrovnávání zatížení, který vyrovnává zatížení virtuálních počítačů. 

Při vytváření interního nástroje pro vyrovnávání zatížení je virtuální síť nakonfigurovaná jako síť pro nástroj pro vyrovnávání zatížení. 

Privátní IP adresa ve virtuální síti je ve výchozím nastavení pro nástroj pro vyrovnávání zatížení nakonfigurovaná jako front-end (s názvem jako **LoadBalancerFrontend** ). 

IP adresa front-endu může být **statická** nebo **Dynamická**.

## <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

V této části vytvoříte virtuální síť a podsíť.

1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek > Sítě > Virtuální síť** nebo do vyhledávacího pole zadejte **Virtuální síť**.

2. V části **vytvořit virtuální síť** zadejte nebo vyberte tyto informace na kartě **základy** :

    | **Nastavení**          | **Hodnota**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Podrobnosti o projektu**  |                                                                 |
    | Předplatné     | Vyberte své předplatné Azure.                                  |
    | Skupina prostředků   | Vybrat **CreateIntLBQS-RG** |
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

## <a name="create-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek**  >  **síť**  >  **Load Balancer**.

2. Na kartě **základy** na stránce **vytvořit nástroj pro vyrovnávání zatížení** zadejte nebo vyberte následující informace: 

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte své předplatné.    |    
    | Skupina prostředků         | Vyberte **CreateIntLBQS-RG** vytvořené v předchozím kroku.|
    | Název                   | Zadejte **myLoadBalancer**                                   |
    | Oblast         | Vyberte **Západní Evropa**.                                        |
    | Typ          | Vyberte **interní**.                                        |
    | SKU           | Vybrat **základní** |
    | Virtuální síť | Vyberte **myVNet** vytvořené v předchozím kroku. |
    | Podsíť  | Vyberte **myBackendSubnet** vytvořené v předchozím kroku. |
    | Přiřazení IP adresy | Vyberte **Dynamická**. |

3. U zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**.

4. Na kartě **Revize + vytvořit** vyberte **vytvořit**.   

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-basic-internal-load-balancer.png" alt-text="Vytvoří základní interní nástroj pro vyrovnávání zatížení." border="true":::

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

3. Na stránce **Přidat back-end fond** zadejte nebo vyberte:
    
    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte **myBackendPool**. |
    | Virtuální síť | Vyberte **myVNet**. |
    | Přidruženo k | Vybrat **virtuální počítače** |

4. Vyberte **Add** (Přidat).

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

V této části vytvoříte tři virtuální počítače (**myVM1**, **myVM2**, **myVM3**).

Oba virtuální počítače se přidají do skupiny dostupnosti s názvem **myAvailabilitySet**.

Tyto virtuální počítače se přidají do back-endového fondu nástroje pro vyrovnávání zatížení, který se vytvořil dříve.

1. V levé horní části portálu vyberte **vytvořit prostředek**  >    >  **virtuální počítač** Compute. 
   
2. V části **vytvořit virtuální počítač** zadejte nebo vyberte hodnoty na kartě **základy** :

    | Nastavení | Hodnota                                          |
    |-----------------------|----------------------------------|
    | **Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné Azure. |
    | Skupina prostředků | Vybrat **CreateIntLBQS-RG** |
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
    | Konfigurovat skupinu zabezpečení sítě | Vyberte, že chcete **vytvořit novou** IP adresu. </br> V části **vytvořit skupinu zabezpečení sítě** zadejte **MyNSG** do **pole název**. </br> Vyberte **OK**. |
    | **Vyrovnávání zatížení**  |
    | Umístit tento virtuální počítač za existující řešení vyrovnávání zatížení? | Vybrat **ne** |

5. Vyberte **Zkontrolovat a vytvořit**. 
  
6. Zkontrolujte nastavení a pak vyberte **vytvořit**.

7. Postupujte podle kroků 1 až 8 a vytvořte dva další virtuální počítače s následujícími hodnotami a všechna ostatní nastavení, která jsou stejná jako **myVM1**:

    | Nastavení | VIRTUÁLNÍ POČÍTAČ 2 | VIRTUÁLNÍ POČÍTAČ 3 |
    | ------- | ----- | ---- |
    | Název |  **myVM2** | **myVM3** |
    | Skupina dostupnosti | Vybrat **myAvailabilitySet** | Vybrat **myAvailabilitySet** |
    | Skupina zabezpečení sítě | Vybrat existující **myNSG** | Vybrat existující **myNSG** |

### <a name="add-virtual-machines-to-the-backend-pool"></a>Přidání virtuálních počítačů do back-endového fondu

Virtuální počítače vytvořené v předchozích krocích se musí přidat do back-endu fondu **myLoadBalancer**.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a potom v seznamu prostředků vyberte **myLoadBalancer** .

2. V části **Nastavení** vyberte **back-end fondy** a pak vyberte **myBackendPool**.

3. Vyberte **virtuální počítače** v nástroji **přidruženo k**.

4. V části **virtuální počítače** vyberte **+ Přidat**.

5. Zaškrtněte políčka vedle **myVM1**, **myVM2** a **myVM3**.

6. Vyberte **Add** (Přidat).

7. Vyberte **Uložit**.
---

## <a name="create-test-virtual-machine"></a>Vytvořit testovací virtuální počítač

V této části vytvoříte virtuální počítač s názvem **myTestVM**.  Tento virtuální počítač se použije k otestování konfigurace nástroje pro vyrovnávání zatížení.

1. V levé horní části portálu vyberte **vytvořit prostředek**  >    >  **virtuální počítač** Compute. 
   
2. V části **vytvořit virtuální počítač** zadejte nebo vyberte hodnoty na kartě **základy** :

    | Nastavení | Hodnota                                          |
    |-----------------------|----------------------------------|
    | **Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné Azure. |
    | Skupina prostředků | Vybrat **CreateIntLBQS-RG** |
    | **Podrobnosti o instancích** |  |
    | Název virtuálního počítače | Zadejte **myTestVM** |
    | Oblast | Vyberte **západní Evropa** |
    | Možnosti dostupnosti | Vyberte možnost **nepožaduje se žádná redundance infrastruktury** . |
    | Image | Vyberte **Windows Server 2019 Datacenter** |
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
    | Podsíť | **myBackendSubnet** |
    | Veřejná IP adresa | Vyberte **Žádná**. |
    | Skupina zabezpečení sítě NIC | Výběr **Možnosti Upřesnit**|
    | Konfigurovat skupinu zabezpečení sítě | Vyberte **MyNSG** vytvořené v předchozím kroku.|
       
5. Vyberte **Zkontrolovat a vytvořit**. 
  
6. Zkontrolujte nastavení a pak vyberte **vytvořit**.

## <a name="install-iis"></a>Instalace služby IIS

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a potom ze seznamu prostředky vyberte **myVM1** , která je umístěná ve skupině prostředků **CreateIntLBQS-RG** .

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

1. Na obrazovce **Přehled** vyhledejte privátní IP adresu pro nástroj pro vyrovnávání zatížení. V nabídce na levé straně vyberte **všechny služby** a vyberte **všechny prostředky** a pak vyberte **myLoadBalancer**.

2. V **přehledu** **myLoadBalancer** si poznamenejte nebo zkopírujte adresu u pole **privátní IP adresa** .

3. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a potom ze seznamu prostředky vyberte **myTestVM** , která je umístěná ve skupině prostředků **CreateIntLBQS-RG** .

4. Na stránce **Přehled** vyberte **připojit** a pak **bastionu**.

6. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

7. Otevřete **Internet Explorer** v **myTestVM**.

8. Zadejte IP adresu z předchozího kroku do panelu Adresa v prohlížeči. V prohlížeči se zobrazí výchozí stránka webového serveru služby IIS.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Snímek obrazovky s oknem prohlížeče zobrazuje výchozí stránku, podle očekávání." border="true":::
   
Pokud chcete zobrazit distribuci provozu nástroje pro vyrovnávání zatížení napříč virtuálními počítači, můžete přizpůsobit výchozí stránku webového serveru IIS každého virtuálního počítače a potom vynutit aktualizaci webového prohlížeče z klientského počítače.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, nástroj pro vyrovnávání zatížení a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků **CreateIntLBQS-RG** , která obsahuje prostředky, a pak vyberete **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu:

* Vytvořili jste interní nástroj pro vyrovnávání zatížení Azure Standard nebo Basic.
* K nástroji pro vyrovnávání zatížení se připojily 3 virtuální počítače.
* Nakonfigurovali jste pravidlo provozu nástroje pro vyrovnávání zatížení, sondu stavu a pak otestovali Nástroj pro vyrovnávání zatížení. 

Pokud se chcete dozvědět víc o Azure Load Balancer, pokračujte tady:
> [!div class="nextstepaction"]
> [Co je Azure Load Balancer?](load-balancer-overview.md)