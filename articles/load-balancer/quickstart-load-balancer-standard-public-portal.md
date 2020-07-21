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
ms.date: 07/17/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: eb23f1e703c2e447c484ccb366914cb4b23c5bf7
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536538"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Rychlý Start: vytvoření služby Vyrovnávání zatížení pro vyrovnávání zatížení virtuálních počítačů pomocí Azure Portal

Začněte s Azure Load Balancer pomocí Azure Portal k vytvoření veřejného nástroje pro vyrovnávání zatížení a tří virtuálních počítačů.

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

---

# <a name="option-1-default-create-a-load-balancer-standard-sku"></a>[Možnost 1 (výchozí): Vytvoření nástroje pro vyrovnávání zatížení (standardní SKU)](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Pro produkční úlohy se doporučuje používat nástroj pro vyrovnávání zatížení Standard SKU.  Další informace o SKU najdete v tématu **[Azure Load Balancer SKU](skus.md)**.

V této části vytvoříte Nástroj pro vyrovnávání zatížení, který vyrovnává zatížení virtuálních počítačů. 

Můžete vytvořit veřejný Nástroj pro vyrovnávání zatížení nebo interní nástroj pro vyrovnávání zatížení. 

Při vytváření veřejného nástroje pro vyrovnávání zatížení vytvoříte novou veřejnou IP adresu, která je ve výchozím nastavení nakonfigurována jako front-end (s názvem jako **LoadBalancerFrontend** ) pro nástroj pro vyrovnávání zatížení.

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek**  >  **síť**  >  **Load Balancer**.

2. Na kartě **základy** na stránce **vytvořit nástroj pro vyrovnávání zatížení** zadejte nebo vyberte následující informace: 

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte předplatné.    |    
    | Skupina prostředků         | Vyberte **vytvořit nový** a do textového pole zadejte **myResourceGroupLB** .|
    | Název                   | Zadejte **myLoadBalancer**                                   |
    | Oblast         | Vyberte **Západní Evropa**.                                        |
    | Typ          | Vyberte **Veřejný**.                                        |
    | Skladová položka           | Vybrat **Standard** |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. Pokud máte existující veřejnou IP adresu, kterou byste chtěli použít, vyberte **použít existující**. |
    | Název veřejné IP adresy | Do textového pole zadejte **myPublicIP** .|
    | Zóna dostupnosti | Chcete-li vytvořit odolný Nástroj pro vyrovnávání zatížení, vyberte **zónu – redundantní** . Pokud chcete vytvořit nástroj pro vyrovnávání zatížení, vyberte konkrétní zónu z 1, 2 nebo 3. |
    | Přidat veřejnou IPv6 adresu | Vyberte **Ne**. </br> Další informace o adresách IPv6 a nástroji pro vyrovnávání zatížení najdete v tématu [co je protokol IPv6 pro Azure Virtual Network?](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)  |

3. U zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**.

4. Na kartě **Revize + vytvořit** vyberte **vytvořit**.   
    
    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png" alt-text="Vytvoření standardního nástroje pro vyrovnávání zatížení" border="true":::
 
## <a name="create-load-balancer-resources"></a>Vytvoření prostředků nástroje pro vyrovnávání zatížení

V této části nakonfigurujete:

* Nastavení nástroje pro vyrovnávání zatížení pro fond back-end adres.
* Sonda stavu.
* Pravidlo nástroje pro vyrovnávání zatížení a automatické odchozí pravidlo.

### <a name="create-a-backend-pool"></a>Vytvoření back-endového fondu

Fond adres back-endu obsahuje IP adresy virtuálních (síťových rozhraní) připojených k nástroji pro vyrovnávání zatížení. 

Vytvořte fond back-end adres **myBackendPool** , který bude zahrnovat virtuální počítače pro internetovou komunikaci s vyrovnáváním zatížení.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředků vyberte **myLoadBalancer** .

2. V části **Nastavení**vyberte **back-end fondy**a pak vyberte **Přidat**.

3. Do pole název na stránce **Přidat fond back-end** serveru zadejte **myBackendPool**jako název vašeho back-end fondu a pak vyberte **Přidat**.

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Nástroj pro vyrovnávání zatížení monitoruje stav vaší aplikace s sondou stavu. 

Sonda stavu přidá nebo odebere virtuální počítače z nástroje pro vyrovnávání zatížení na základě jejich odpovědí na kontroly stavu. 

Vytvořte sondu stavu s názvem **myHealthProbe**, abyste mohli monitorovat stav virtuálních počítačů.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředků vyberte **myLoadBalancer** .

2. V části **Nastavení**vyberte **sondy stavu**a pak vyberte **Přidat**.
    
    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte **myHealthProbe**. |
    | Protokol | Vyberte **http**. |
    | Port | Zadejte **80**.|
    | Interval | Zadejte hodnotu **15** pro **interval** mezi pokusy o sondu v sekundách. |
    | Prahová hodnota pro poškozený stav | Vyberte **2** pro počet chybných **prahových hodnot** nebo po sobě jdoucích selhání sondy, ke kterým musí dojít, aby se virtuální počítač považoval za poškozený.|
    | | |

3. Vyberte **OK**.

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení slouží k definování způsobu distribuce provozu do virtuálních počítačů. Nadefinujete konfiguraci IP adresy front-endu pro příchozí provoz a fond IP adres back-endu pro příjem provozu. Zdrojový a cílový port se definují v pravidle. 

V této části vytvoříte pravidlo nástroje pro vyrovnávání zatížení:

* S názvem **myHTTPRule**.
* Ve front-endu s názvem **LoadBalancerFrontEnd**.
* Naslouchá na **portu 80**.
* Směruje provoz s vyrovnáváním zatížení do back-endu s názvem **myBackendPool** na **portu 80**.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředků vyberte **myLoadBalancer** .

2. V části **Nastavení**vyberte **pravidla vyrovnávání zatížení**a pak vyberte **Přidat**.

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
    | Vytvořit implicitní odchozí pravidla | Vyberte **Ano**. </br> Další informace a pokročilou konfiguraci odchozího pravidla najdete v těchto tématech: </br> [Odchozí připojení v Azure](load-balancer-outbound-connections.md) </br> [Konfigurace vyrovnávání zatížení a odchozích pravidel v Standard Load Balancer pomocí Azure Portal](configure-load-balancer-outbound-portal.md)

4. Ponechte zbytek výchozích hodnot a pak vyberte **OK**.

## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V této části:

* Vytvořte virtuální síť.
* Vytvořte tři virtuální počítače pro back-end fond nástroje pro vyrovnávání zatížení.
* K otestování nástroje pro vyrovnávání zatížení nainstalujte na virtuálních počítačích službu IIS.

## <a name="virtual-network-and-parameters"></a>Virtuální síť a parametry

V této části nahradíte parametry v krocích níže uvedenými informacemi:

| Parametr                   | Hodnota                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Západní Evropa      |
| **\<IPv4-address-space>**   | 10.1.0.0 \ 16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.1.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

SKU veřejných IP adres a SKU nástroje pro vyrovnávání zatížení se musí shodovat. Pro nástroj Load Balancer úrovně Standard použijte virtuální počítače se standardními IP adresami ve fondu back-end. 

V této části vytvoříte tři virtuální počítače (**myVM1**, **myVM2** a **MYVM3**) se standardní veřejnou IP adresou ve třech různých zónách (**zóna 1**, **zóna 2**a **zóna 3**). 

Tyto virtuální počítače se přidají do back-endového fondu nástroje pro vyrovnávání zatížení, který se vytvořil dříve.

1. V levé horní části portálu vyberte **vytvořit prostředek**  >  **Compute**  >  **virtuální počítač**Compute. 
   
2. V části **vytvořit virtuální počítač**zadejte nebo vyberte hodnoty na kartě **základy** :

    | Nastavení | Hodnota                                          |
    |-----------------------|----------------------------------|
    | **Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné Azure. |
    | Skupina prostředků | Vybrat **myResourceGroupLB** |
    | **Podrobnosti instance** |  |
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
    | Potvrzení hesla | Zadejte znovu heslo. |

3. Vyberte kartu **síť** nebo vyberte **Další: disky**a **Další: síť**.
  
4. Na kartě sítě vyberte nebo zadejte:

    | Nastavení | Hodnota |
    |-|-|
    | **Síťové rozhraní** |  |
    | Virtuální síť | **myVNet** |
    | Podsíť | **myBackendSubnet** |
    | Veřejná IP adresa | Přijměte výchozí hodnotu **myVM-IP**. </br> IP adresa bude automaticky standardní IP adresa SKU v Zóna 1. |
    | Skupina zabezpečení sítě NIC | Výběr **Možnosti Upřesnit**|
    | Konfigurovat skupinu zabezpečení sítě | Vyberte, že chcete **vytvořit novou** IP adresu. </br> V části **vytvořit skupinu zabezpečení sítě**zadejte **MyNSG** do **pole název**. </br> V části **příchozí pravidla**vyberte **+ Přidat příchozí pravidlo**. </br> V části **rozsahy cílových portů**zadejte **80**. </br> V části **Priorita**zadejte **100**. </br> Do **název**zadejte **myHTTPRule** </br> Vyberte **Přidat** </br> Vybrat **OK** |
    | **Vyrovnávání zatížení**  |
    | Umístit tento virtuální počítač za existující řešení vyrovnávání zatížení? | Vyberte **Ano** |
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
    | Název |  **myVM2** |**myVM3**|
    | Zóna dostupnosti | **2** |**3**|
    | Skupina zabezpečení sítě | Vybrat existující **myNSG**| Vybrat existující **myNSG**|


# <a name="option-2-create-a-load-balancer-basic-sku"></a>[Možnost 2: Vytvoření nástroje pro vyrovnávání zatížení (základní skladová položka)](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Pro produkční úlohy se doporučuje používat nástroj pro vyrovnávání zatížení Standard SKU.  Další informace o SKU najdete v tématu **[Azure Load Balancer SKU](skus.md)**.

V této části vytvoříte Nástroj pro vyrovnávání zatížení, který vyrovnává zatížení virtuálních počítačů. 

Můžete vytvořit veřejný Nástroj pro vyrovnávání zatížení nebo interní nástroj pro vyrovnávání zatížení. 

Při vytváření veřejného nástroje pro vyrovnávání zatížení vytvoříte novou veřejnou IP adresu, která je ve výchozím nastavení nakonfigurována jako front-end (s názvem jako **LoadBalancerFrontend** ) pro nástroj pro vyrovnávání zatížení.

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek**  >  **síť**  >  **Load Balancer**.

2. Na kartě **základy** na stránce **vytvořit nástroj pro vyrovnávání zatížení** zadejte nebo vyberte následující informace: 

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte předplatné.    |    
    | Skupina prostředků         | Vyberte **vytvořit nový** a do textového pole zadejte **myResourceGroupLB** .|
    | Název                   | Zadejte **myLoadBalancer**                                   |
    | Oblast         | Vyberte **Západní Evropa**.                                        |
    | Typ          | Vyberte **Veřejný**.                                        |
    | Skladová položka           | Vybrat **základní** |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. Pokud máte existující veřejnou IP adresu, kterou byste chtěli použít, vyberte **použít existující**. |
    | Název veřejné IP adresy | Do textového pole zadejte **myPublicIP** .|
    | Přiřazení | Vybrat **dynamický** |
    | Přidat veřejnou IPv6 adresu | Vyberte **Ne**. </br> Další informace o adresách IPv6 a nástroji pro vyrovnávání zatížení najdete v tématu [co je protokol IPv6 pro Azure Virtual Network?](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)  |

3. U zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**.

4. Na kartě **Revize + vytvořit** vyberte **vytvořit**.   

    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-basic-load-balancer.png" alt-text="Vytvoření základního nástroje pro vyrovnávání zatížení" border="true":::

## <a name="create-load-balancer-resources"></a>Vytvoření prostředků nástroje pro vyrovnávání zatížení

V této části nakonfigurujete:

* Vytvořte virtuální síť.
* Nastavení nástroje pro vyrovnávání zatížení pro fond back-end adres.
* Sonda stavu.
* Pravidlo nástroje pro vyrovnávání zatížení.

## <a name="virtual-network-and-parameters"></a>Virtuální síť a parametry

V této části nahradíte parametry v krocích níže uvedenými informacemi:

| Parametr                   | Hodnota                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Západní Evropa      |
| **\<IPv4-address-space>**   | 10.1.0.0 \ 16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.1.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-a-backend-pool"></a>Vytvoření back-endového fondu

Fond adres back-endu obsahuje IP adresy virtuálních (síťových rozhraní) připojených k nástroji pro vyrovnávání zatížení. 

Vytvořte fond back-end adres **myBackendPool** , který bude zahrnovat virtuální počítače pro internetovou komunikaci s vyrovnáváním zatížení.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředků vyberte **myLoadBalancer** .

2. V části **Nastavení**vyberte **back-end fondy**a pak vyberte **Přidat**.

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

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředků vyberte **myLoadBalancer** .

2. V části **Nastavení**vyberte **sondy stavu**a pak vyberte **Přidat**.
    
    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte **myHealthProbe**. |
    | Protokol | Vyberte **http**. |
    | Port | Zadejte **80**.|
    | Cesta | Napište**/** |
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

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředků vyberte **myLoadBalancer** .

2. V části **Nastavení**vyberte **pravidla vyrovnávání zatížení**a pak vyberte **Přidat**.

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
 
4. Ponechte zbytek výchozích hodnot a pak vyberte **OK**.

## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V této části:

* Vytvořte tři virtuální počítače pro back-end fond nástroje pro vyrovnávání zatížení.
* Vytvořte skupinu dostupnosti pro virtuální počítače.
* K otestování nástroje pro vyrovnávání zatížení nainstalujte na virtuálních počítačích službu IIS.

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

SKU veřejných IP adres a SKU nástroje pro vyrovnávání zatížení se musí shodovat. Pro nástroj pro vyrovnávání zatížení Basic použijte virtuální počítače se základními IP adresami ve fondu back-end. 

V této části vytvoříte tři virtuální počítače (**myVM1**, **myVM2**a **MYVM3**) se základní veřejnou IP adresou.  

Tři virtuální počítače se přidají do skupiny dostupnosti s názvem **myAvailabilitySet**.

Tyto virtuální počítače se přidají do back-endového fondu nástroje pro vyrovnávání zatížení, který se vytvořil dříve.

1. V levé horní části portálu vyberte **vytvořit prostředek**  >  **Compute**  >  **virtuální počítač**Compute. 
   
2. V části **vytvořit virtuální počítač**zadejte nebo vyberte hodnoty na kartě **základy** :

    | Nastavení | Hodnota                                          |
    |-----------------------|----------------------------------|
    | **Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné Azure. |
    | Skupina prostředků | Vybrat **myResourceGroupLB** |
    | **Podrobnosti instance** |  |
    | Název virtuálního počítače | Zadejte **myVM1** |
    | Oblast | Vyberte **západní Evropa** |
    | Možnosti dostupnosti | Vyberte **Skupina dostupnosti**. |
    | Skupina dostupnosti | Vyberte, že chcete **vytvořit novou** IP adresu. </br> Jako **název**zadejte **myAvailabilitySet** . </br> Vybrat **OK** |
    | Image | **Windows Server 2019 Datacenter** |
    | Instance Azure Spot | Vybrat **ne** |
    | Velikost | Vyberte velikost virtuálního počítače nebo použijte výchozí nastavení. |
    | **Účet správce** |  |
    | Uživatelské jméno | Zadejte uživatelské jméno. |
    | Heslo | Zadat heslo |
    | Potvrzení hesla | Zadejte znovu heslo. |

3. Vyberte kartu **síť** nebo vyberte **Další: disky**a **Další: síť**.
  
4. Na kartě sítě vyberte nebo zadejte:

    | Nastavení | Hodnota |
    |-|-|
    | **Síťové rozhraní** |  |
    | Virtuální síť | Vybrat **myVNet** |
    | Podsíť | Vybrat **myBackendSubnet** |
    | Veřejná IP adresa | Vyberte **vytvořit novou** . </br> Do názvu zadejte **myVM-IP** . </br> Vybrat **OK** |
    | Skupina zabezpečení sítě NIC | Výběr **Možnosti Upřesnit**|
    | Konfigurovat skupinu zabezpečení sítě | Vyberte, že chcete **vytvořit novou** IP adresu. </br> V části **vytvořit skupinu zabezpečení sítě**zadejte **MyNSG** do **pole název**. </br> V části **příchozí pravidla**vyberte **+ Přidat příchozí pravidlo**. </br> V části **rozsahy cílových portů**zadejte **80**. </br> V části **Priorita**zadejte **100**. </br> Do **název**zadejte **myHTTPRule** </br> Vyberte **Přidat** </br> Vybrat **OK** |
    | **Vyrovnávání zatížení**  |
    | Umístit tento virtuální počítač za existující řešení vyrovnávání zatížení? | Vybrat **ne** |
 
5. Vyberte kartu **Správa** nebo vyberte možnost **Další**  >  **Správa**.

6. Na kartě **Správa** vyberte nebo zadejte:
    | Nastavení | Hodnota |
    |-|-|
    | **Monitorování** | |
    | Diagnostika spouštění | Vybrat **vypnuto** |

7. Vyberte **Zkontrolovat a vytvořit**. 
  
8. Zkontrolujte nastavení a pak vyberte **vytvořit**.

9. Postupujte podle kroků 1 až 8 a vytvořte dva další virtuální počítače s následujícími hodnotami a všechna ostatní nastavení, která jsou stejná jako **myVM1**:

    | Nastavení | VIRTUÁLNÍ POČÍTAČ 2| VIRTUÁLNÍ POČÍTAČ 3|
    | ------- | ----- |---|
    | Název |  **myVM2** |**myVM3**|
    | Skupina dostupnosti| Vybrat **myAvailabilitySet** | Vybrat **myAvailabilitySet**|
    | Skupina zabezpečení sítě | Vybrat existující **myNSG**| Vybrat existující **myNSG**|
---

### <a name="install-iis"></a>Instalace služby IIS

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředky vyberte **myVM1** , která je umístěná ve skupině prostředků **myResourceGroupLB** .

2. Na stránce **Přehled** vyberte **připojit** a Stáhněte soubor RDP pro virtuální počítač.

3. Otevřete soubor RDP.

4. Přihlaste se k virtuálnímu počítači pomocí přihlašovacích údajů, které jste zadali při vytváření tohoto virtuálního počítače.

5. Na ploše serveru přejděte do části **Nástroje pro správu Windows** > **Windows PowerShell**.

6. V okně PowerShellu spusťte následující příkazy pro:

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
7. Zavřete relaci RDP pomocí **myVM1**.

8. Opakováním kroků 1 až 6 nainstalujte službu IIS a aktualizovaný soubor iisstart.htm na **myVM2** a **myVM3**.

## <a name="test-the-load-balancer"></a>Testování Load Balanceru

1. Vyhledejte veřejnou IP adresu nástroje pro vyrovnávání zatížení na obrazovce **Přehled** . V nabídce na levé straně vyberte **všechny služby** a vyberte **všechny prostředky**a pak vyberte **myPublicIP**.

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. V prohlížeči se zobrazí výchozí stránka webového serveru služby IIS.

   ![Webový server služby IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Pokud chcete zobrazit distribuci provozu nástroje pro vyrovnávání zatížení napříč všemi třemi virtuálními počítači, můžete přizpůsobit výchozí stránku webového serveru IIS každého virtuálního počítače a potom vynutit aktualizaci webového prohlížeče z klientského počítače.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, nástroj pro vyrovnávání zatížení a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků **myResourceGroupLB** , která obsahuje prostředky, a pak vyberete **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu:

* Vytvořili Load Balancer Azure Standard nebo Basic.
* K nástroji pro vyrovnávání zatížení se připojily 3 virtuální počítače.
* Nakonfigurovali jste pravidlo provozu nástroje pro vyrovnávání zatížení, sondu stavu a pak otestovali Nástroj pro vyrovnávání zatížení. 

Chcete-li získat další informace o Azure Load Balancer, pokračujte v [Azure Load Balancer?](load-balancer-overview.md) a [Load Balancer Nejčastější dotazy](load-balancer-faqs.md).

Přečtěte si další informace o [Load Balancer a zónách dostupnosti](load-balancer-standard-availability-zones.md).
