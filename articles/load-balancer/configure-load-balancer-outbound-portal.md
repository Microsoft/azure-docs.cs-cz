---
title: Konfigurace vyrovnávání zatížení a odchozích pravidel pomocí Azure Portal
titleSuffix: Azure Load Balancer
description: V tomto článku se dozvíte, jak nakonfigurovat pravidla vyrovnávání zatížení a odchozí pravidla v Standard Load Balancer pomocí Azure Portal.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 2dff916bf005b307f27264ad7a17864fbba50872
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2020
ms.locfileid: "85367389"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-the-azure-portal"></a>Konfigurace vyrovnávání zatížení a odchozích pravidel v Standard Load Balancer pomocí Azure Portal

V tomto článku se dozvíte, jak nakonfigurovat odchozí pravidla v Standard Load Balancer pomocí Azure Portal.  

Prostředek nástroje pro vyrovnávání zatížení obsahuje dva front-endy a jejich přidružená pravidla. Máte jeden front-end pro příchozí provoz a další front-end pro odchozí provoz.  

Každý front-end odkazuje na veřejnou IP adresu. V tomto scénáři se veřejná IP adresa pro příchozí přenosy liší od adresy pro odchozí provoz.   Pravidlo vyrovnávání zatížení poskytuje pouze příchozí vyrovnávání zatížení. Pravidlo odchozího řízení odchozího překladu adres (NAT) pro virtuální počítač.  

Scénář používá dva fondy back-endu: jeden pro příchozí provoz a jeden pro odchozí provoz. Tyto fondy ilustrují schopnost a poskytují flexibilitu pro scénář.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete. 

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

V této části vytvoříte Nástroj pro vyrovnávání zatížení, který bude vyrovnávat zatížení virtuálních počítačů. Můžete vytvořit veřejný Nástroj pro vyrovnávání zatížení nebo interní nástroj pro vyrovnávání zatížení. Při vytváření veřejného nástroje pro vyrovnávání zatížení vytvoříte novou veřejnou IP adresu, která je nakonfigurovaná jako front-end pro nástroj pro vyrovnávání zatížení. Ve výchozím nastavení bude front-end název **LoadBalancerFrontEnd** .

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek**  >  **síť**  >  **Load Balancer**.
2. Na kartě **základy** na stránce **vytvořit nástroj pro vyrovnávání zatížení** zadejte nebo vyberte následující informace:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte své předplatné.    |    
    | Skupina prostředků         | Vyberte **vytvořit nový** a do textového pole zadejte **myResourceGroupSLB** .|
    | Name                   | **myLoadBalancer**                                   |
    | Oblast         | Vyberte **Západní Evropa**.                                        |
    | Typ          | Vyberte možnost **veřejné**.                                        |
    | SKU           | Vyberte **Standard**. |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. Pokud máte existující veřejnou IP adresu, kterou byste chtěli použít, vyberte **použít existující**.  Stávající veřejná IP adresa musí být **standardní** SKU.  Základní veřejné IP adresy nejsou kompatibilní se službou Load Balancer **Standard** SKU.  |
    | Název veřejné IP adresy              | Do textového pole zadejte **myPublicIP** .|
    | Zóna dostupnosti | Chcete-li vytvořit odolný Load Balancer, vyberte **zónu – redundantní** . Pokud chcete vytvořit oblast Load Balancer, vyberte konkrétní zónu z 1, 2 nebo 3. |

3. Přijměte výchozí hodnoty pro zbytek konfigurace.
4. Vybrat **kontrolu + vytvořit**

    > [!IMPORTANT]
    > Zbytek v tomto rychlém startu předpokládá, že se během výše uvedeného procesu výběru skladové položky vybere **standardní** SKU.

5. Na kartě **Revize + vytvořit** vyberte **vytvořit**.   

    ![Vytvoření Load Balanceru úrovně Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Vytvoření prostředků nástroje pro vyrovnávání zatížení

V této části nakonfigurujete nastavení nástroje pro vyrovnávání zatížení pro fond back-endových adres a sondu stavu a zadáte pravidlo nástroje pro vyrovnávání zatížení.

### <a name="create-a-backend-pool"></a>Vytvoření back-endového fondu

Fond adres back-endu obsahuje IP adresy virtuálních síťových karet ve fondu back-end. Vytvořte fond back-end adres **myBackendPool** , který bude zahrnovat virtuální počítače pro internetovou komunikaci s vyrovnáváním zatížení.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředků vyberte **myLoadBalancer** .
2. V části **Nastavení**vyberte **back-end fondy**a pak vyberte **Přidat**.
3. Do pole název na stránce **Přidat fond back-end** serveru zadejte **myBackendPool**jako název vašeho back-end fondu a pak vyberte **Přidat**.

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Sonda stavu se používá ke sledování stavu vaší aplikace. Sonda stavu přidá nebo odebere virtuální počítače z nástroje pro vyrovnávání zatížení na základě jejich odpovědí na kontroly stavu. Vytvořte sondu stavu **myHealthProbe** pro monitorování stavu virtuálních počítačů.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředků vyberte **myLoadBalancer** .
2. V části **Nastavení**vyberte **sondy stavu**a pak vyberte **Přidat**.
    
    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte **myHealthProbe**. |
    | Protocol (Protokol) | Vyberte **http**. |
    | Port | Zadejte **80**.|
    | Interval | Zadejte hodnotu **15** pro **interval** mezi pokusy o sondu v sekundách. |
    | Prahová hodnota pro poškozený stav | Vyberte **2** pro počet chybných **prahových hodnot** nebo po sobě jdoucích selhání sondy, ke kterým musí dojít, aby se virtuální počítač považoval za poškozený.|
    | | |
4. Vyberte **OK**.

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení
Pravidlo nástroje pro vyrovnávání zatížení slouží k definování způsobu distribuce provozu do virtuálních počítačů. 

Definujete:
 - Konfigurace IP adresy front-endu pro příchozí provoz.
 - Fond IP adres back-endu pro příjem provozu.
 - Požadovaný zdrojový a cílový port. 

V následující části vytvoříte:
 - Pravidlo nástroje pro vyrovnávání zatížení **myHTTPRule** pro naslouchání portu 80.
 - Front-end **LoadBalancerFrontEnd**.
 - Back-end adresový fond **myBackEndPool** také pomocí portu 80. 

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředků vyberte **myLoadBalancer** .
2. V části **Nastavení**vyberte **pravidla vyrovnávání zatížení**a pak vyberte **Přidat**.
3. Pomocí těchto hodnot můžete nakonfigurovat pravidlo vyrovnávání zatížení:
    
    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte **myHTTPRule**. |
    | Protocol (Protokol) | Vyberte **TCP**. |
    | Port | Zadejte **80**.|
    | Port back-endu | Zadejte **80**. |
    | Back-endový fond | Vyberte **myBackendPool**.|
    | Sonda stavu | Vyberte **myHealthProbe**. |
    | Vytvořit implicitní odchozí pravidla | Vyberte **Ne**. V pozdější části vytvoříme odchozí pravidla pomocí vyhrazené veřejné IP adresy. |
4. Ponechte zbytek výchozích hodnot a pak vyberte **OK**.

## <a name="create-outbound-rule-configuration"></a>Vytvořit konfiguraci odchozího pravidla
Odchozí pravidla nástroje pro vyrovnávání zatížení konfigurují odchozí SNAT pro virtuální počítače ve fondu back-endu. 

### <a name="create-an-outbound-public-ip-address-and-frontend"></a>Vytvoření odchozí veřejné IP adresy a front-endu

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředků vyberte **myLoadBalancer** .

2. V části **Nastavení**vyberte **Konfigurace IP adresy front-endu**a pak vyberte **Přidat**.

3. Pomocí těchto hodnot nakonfigurujte konfiguraci IP adresy front-endu pro odchozí připojení:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte **LoadBalancerFrontEndOutbound**. |
    | Verze protokolu IP | Vyberte **IPv4**. |
    | Typ IP adresy | Vyberte **IP adresa**.|
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. Do pole **Přidat veřejnou IP adresu**zadejte **myPublicIPOutbound**.  Vyberte **OK**. |

4. Vyberte možnost **Přidat**.

### <a name="create-an-outbound-backend-pool"></a>Vytvořit odchozí back-end fond

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředků vyberte **myLoadBalancer** .

2. V části **Nastavení**vyberte **back-end fondy**a pak vyberte **Přidat**.

3. Do pole název na stránce **Přidat fond back-end** serveru zadejte **myBackendPoolOutbound**jako název vašeho back-end fondu a pak vyberte **Přidat**.

### <a name="create-outbound-rule"></a>Vytvořit odchozí pravidlo

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředků vyberte **myLoadBalancer** .

2. V části **Nastavení**vyberte **odchozí pravidla**a pak vyberte **Přidat**.

3. Pomocí těchto hodnot nakonfigurujte odchozí pravidla:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte **myOutboundRule**. |
    | IP adresa front-endu | Vyberte **LoadBalancerFrontEndOutbound**. |
    | Časový limit nečinnosti (minuty) | Přesuňte posuvník na * * 15 minut.|
    | Resetování protokolu TCP | Vyberte **Povoleno**.|
    | Back-endový fond | Vybrat **myBackendPoolOutbound** |
    | Přidělování portů-> přidělování portů | Vyberte možnost **ručně vybrat počet odchozích portů** . |
    | Odchozí porty – > zvolit podle | Vybrat **porty na instanci** |
    | Odchozí porty – > porty na instanci | Zadejte **10 000**. |

4. Vyberte možnost **Přidat**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, nástroj pro vyrovnávání zatížení a všechny související prostředky. Vyberte skupinu prostředků **myResourceGroupSLB** , která obsahuje nástroj pro vyrovnávání zatížení, a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto článku:
 - Vytvořili jste standardní nástroj pro vyrovnávání zatížení.
 - Konfigurují se pravidla provozování příchozích i odchozích vyrovnávání zatížení.
 - Nakonfigurovali jste sondu stavu pro virtuální počítače ve fondu back-end. 

Pokud se chcete dozvědět víc, přejděte k [kurzům Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
