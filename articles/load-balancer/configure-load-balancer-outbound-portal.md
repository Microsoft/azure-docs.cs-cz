---
title: Konfigurace pravidel vyrovnávání zatížení a odchozích pravidel pomocí portálu Azure
titleSuffix: Azure Load Balancer
description: Tento článek ukazuje, jak nakonfigurovat vyrovnávání zatížení a odchozí pravidla ve standardním vyrovnávání zatížení pomocí portálu Azure.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: b75f49155991bfc71f788ad88f166c0bec281841
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77590010"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-the-azure-portal"></a>Konfigurace pravidel vyrovnávání zatížení a odchozích pravidel ve standardním balanceru zatížení pomocí portálu Azure

Tento článek ukazuje, jak nakonfigurovat odchozí pravidla ve standardním vyvažovači zatížení pomocí portálu Azure.  

Prostředek vyrovnávání zatížení obsahuje dva front-endy a jejich přidružená pravidla. Máte jeden front-end pro příchozí provoz a jiný front-end pro odchozí provoz.  

Každý front-end odkazuje na veřejnou IP adresu. V tomto scénáři se veřejná IP adresa pro příchozí provoz liší od adresy pro odchozí provoz.   Pravidlo vyrovnávání zatížení poskytuje pouze příchozí vyrovnávání zatížení. Odchozí pravidlo řídí překlad odchozích síťových adres (NAT) pro virtuální hod.  

Scénář používá dva back-endové fondy: jeden pro příchozí provoz a jeden pro odchozí provoz. Tyto fondy ilustrují schopnosti a poskytují flexibilitu pro scénář.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete. 

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .

## <a name="create-a-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

V této části vytvoříte nástroj pro vyrovnávání zatížení, který bude vyrovnávání zatížení virtuálnípočítače. Můžete vytvořit veřejný systém vyrovnávání zatížení nebo interní systém vyrovnávání zatížení. Při vytváření veřejného zařízení pro vyrovnávání zatížení vytvoříte novou veřejnou IP adresu, která je nakonfigurována jako front-end pro vykladač zatížení. Front-end bude ve výchozím nastavení pojmenován **LoadBalancerFrontEndEnd.**

1. V levém horním rohu obrazovky vyberte Vytvořit nástroje**pro vyrovnávání zatížení****sítě** >  **.** > 
2. Na kartě **Základy** na stránce **Vytvořit vyrovnávání zatížení** zadejte nebo vyberte následující informace:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte své předplatné.    |    
    | Skupina prostředků         | Vyberte **Vytvořit nový** a do textového pole zadejte **myResourceGroupSLB.**|
    | Name (Název)                   | **myLoadBalancer**                                   |
    | Region (Oblast)         | Vyberte **Západní Evropa**.                                        |
    | Typ          | Vyberte **Možnost Veřejné**.                                        |
    | Skladová jednotka (SKU)           | Vyberte **standardní** nebo **základní**. Společnost Microsoft doporučuje standard pro produkční úlohy. |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. Pokud máte existující veřejnou IP adresu, kterou chcete použít, vyberte **Použít existující**.  Existující veřejná IP adresa musí být **standardní** skladová položka.  Základní veřejné IP adresy nejsou kompatibilní se **standardním** výměrem zatížení skladové položky.  |
    | Název veřejné IP adresy              | Do textového pole zadejte **adresu myPublicIP.**|
    | Zóna dostupnosti | Chcete-li vytvořit odolný systém vyrovnávání zatížení, vyberte **redundantní zónu.** Chcete-li vytvořit zonální vyvažovač zatížení, vyberte určitou zónu od 1, 2 nebo 3 |

3. Přijměte výchozí hodnoty pro zbytek konfigurace.
4. Vybrat **zkontrolovat + vytvořit**

    > [!IMPORTANT]
    > Zbytek tohoto rychlého startu předpokládá, že **standardní** skladová položka je vybrána během výše uvedeného procesu výběru skladové položky.

5. Na kartě **Revize + vytvoření** vyberte **Vytvořit**.   

    ![Vytvoření Load Balanceru úrovně Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Vytvoření prostředků nástroje pro vyrovnávání zatížení

V této části nakonfigurujete nastavení nástroje pro vyrovnávání zatížení pro fond back-endových adres a sondu stavu a zadáte pravidlo nástroje pro vyrovnávání zatížení.

### <a name="create-a-backend-pool"></a>Vytvoření back-endového fondu

Fond back-endových adres obsahuje IP adresy virtuálních nic v back-endovém fondu. Vytvořte back-endový fond adres **myBackendPool** tak, aby zahrnoval virtuální počítače pro vyrovnávání zatížení internetového provozu.

1. V yberte **Všechny služby** v levé nabídce, vyberte **Všechny prostředky**a pak vyberte **myLoadBalancer** ze seznamu zdrojů.
2. V části **Nastavení**vyberte **Back-endové fondy**a pak vyberte **Přidat**.
3. Na stránce **Přidat back-endový fond** zadejte název **myBackendPool**jako název back-endového fondu a pak vyberte **Přidat**.

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Ke sledování stavu aplikace se používá sonda stavu. Sonda stavu přidá nebo odebere virtuální chod z vykladače zatížení na základě jejich odpovědi na kontroly stavu. Vytvořte sondu stavu **myHealthProbe** pro monitorování stavu virtuálních počítačů.

1. V yberte **Všechny služby** v levé nabídce, vyberte **Všechny prostředky**a pak vyberte **myLoadBalancer** ze seznamu zdrojů.
2. V části **Nastavení**vyberte **Sondy stavu a**pak vyberte **Přidat**.
    
    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Zadejte **myHealthProbe**. |
    | Protocol (Protokol) | Vyberte **možnost HTTP**. |
    | Port | Zadejte **80**.|
    | Interval | Zadejte **hodnotu 15** pro počet **intervalů** v sekundách mezi pokusy o sondu. |
    | Prahová hodnota pro poškozený stav | Vyberte **2** pro počet **selhání nefunkční prahové hodnoty** nebo po sobě jdoucích selhání sondy, ke kterým musí dojít, než je virtuální virtuální ms považován za nefunkční.|
    | | |
4. Vyberte **OK**.

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení
Pravidlo nástroje pro vyrovnávání zatížení slouží k definování způsobu distribuce provozu do virtuálních počítačů. 

Definujete:
 - Front-end OVÁ KONFIGURACE IP pro příchozí provoz.
 - Fond ip adres back-endu pro příjem provozu.
 - Požadovaný zdrojový a cílový port. 

V následující části vytvoříte:
 - Pravidlo nástroj pro vyrovnávání zatížení **myHTTPRule** pro poslech portu 80.
 - Frontend **LoadBalancerFrontEnd**.
 - Back-end adresa fondu **myBackEndPool** také pomocí portu 80. 

1. V yberte **Všechny služby** v levé nabídce, vyberte **Všechny prostředky**a pak vyberte **myLoadBalancer** ze seznamu zdrojů.
2. V části **Nastavení**vyberte **Pravidla vyrovnávání zatížení**a pak vyberte **Přidat**.
3. Tyto hodnoty slouží ke konfiguraci pravidla vyrovnávání zatížení:
    
    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Zadejte **myHTTPRule**. |
    | Protocol (Protokol) | Vyberte **tcp**. |
    | Port | Zadejte **80**.|
    | Back-endový port | Zadejte **80**. |
    | Back-endový fond | Vyberte **myBackendPool**.|
    | Sonda stavu | Vyberte **myHealthProbe**. |
    | Vytvoření implicitních odchozích pravidel | Vyberte **Ne**. Odchozí pravidla vytvoříme v pozdější části pomocí vyhrazené veřejné IP adresy. |
4. Ponechte zbývající výchozí hodnoty a pak vyberte **OK**.

## <a name="create-outbound-rule-configuration"></a>Vytvořit konfiguraci odchozího pravidla
Odchozí pravidla vyrovnávání zatížení nakonfigurují odchozí SNAT pro virtuální počítače v back-endovém fondu. 

### <a name="create-an-outbound-public-ip-address-and-frontend"></a>Vytvoření odchozí veřejné IP adresy a front-endu

1. V yberte **Všechny služby** v levé nabídce, vyberte **Všechny prostředky**a pak vyberte **myLoadBalancer** ze seznamu zdrojů.

2. V části **Nastavení**vyberte **Front-end ová konfigurace IP**a pak vyberte **Přidat**.

3. Tyto hodnoty slouží ke konfiguraci konfigurace ip adres front-endu pro odchozí:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Zadejte **LoadBalancerFrontEndOutOutOutOut .** |
    | Verze protokolu IP | Vyberte **IPv4**. |
    | Typ IP | Vyberte **adresu IP**.|
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. Do části **Přidat veřejnou IP adresu**zadejte **myPublicIPOutbound**.  Vyberte **OK**. |

4. Vyberte **Přidat**.

### <a name="create-an-outbound-backend-pool"></a>Vytvoření odchozího back-endový fondu

1. V yberte **Všechny služby** v levé nabídce, vyberte **Všechny prostředky**a pak vyberte **myLoadBalancer** ze seznamu zdrojů.

2. V části **Nastavení**vyberte **Back-endové fondy**a pak vyberte **Přidat**.

3. Na stránce **Přidat back-endový fond** zadejte název **myBackendPoolOutbound**jako název back-endového fondu a pak vyberte **Přidat**.

### <a name="create-outbound-rule"></a>Vytvořit odchozí pravidlo

1. V yberte **Všechny služby** v levé nabídce, vyberte **Všechny prostředky**a pak vyberte **myLoadBalancer** ze seznamu zdrojů.

2. V části **Nastavení**vyberte **Odchozí pravidla**a pak vyberte **Přidat**.

3. Pomocí těchto hodnot nakonfigurujte odchozí pravidla:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Zadejte **myOutboundRule**. |
    | IP adresa front-endu | Vyberte **možnost LoadBalancerFrontEndOutOutOut .** |
    | Časový limit nečinnosti (minuty) | Posuňte posuvník na **15 minut.|
    | Obnovení protokolu TCP | Vyberte **Povoleno**.|
    | Back-endový fond | Vybrat **myBackendPoolOutbound** |
    | Přidělení portu – > přidělení portu | Vybrat **ručně zvolit počet odchozích portů** |
    | Odchozí porty -> Vybrat podle | Vybrat **porty pro jednu instanci** |
    | Odchozí porty - > porty na instanci | Zadejte **10 000**. |

4. Vyberte **Přidat**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, nástroj pro vyrovnávání zatížení a všechny související prostředky. Vyberte skupinu prostředků **myResourceGroupSLB,** která obsahuje správce zatížení, a pak vyberte **odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto článku:
 - Vytvořili jste standardní vyrovnávání zatížení.
 - Nakonfigurovaná pravidla provozu příchozích i odchozích pravidel provozu vyvažovače zatížení.
 - Nakonfiguroval sondu stavu pro virtuální počítače v back-endovém fondu. 

Další informace najdete v [kurzech pro Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
