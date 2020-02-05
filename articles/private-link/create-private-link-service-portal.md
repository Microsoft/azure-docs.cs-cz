---
title: Rychlý Start – vytvoření služby privátního propojení pomocí Azure Portal
titlesuffix: Azure Private Link
description: Naučte se vytvořit službu privátního propojení pomocí Azure Portal v tomto rychlém startu.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: allensu
ms.openlocfilehash: 4dd27d3fb4321c2eecf222136376bfd4b49790bb
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027773"
---
# <a name="quickstart-create-a-private-link-service-using-the-azure-portal"></a>Rychlý Start: vytvoření služby privátního propojení pomocí Azure Portal

Služba privátního propojení Azure je odkazem na vlastní službu, která využívá privátní propojení Azure. Služba nebo prostředek, který pracuje za Azure Load Balancer, je možné povolit pro přístup k privátním linkám. Příjemci vaší služby mají k této službě přístup soukromě ze svých vlastních virtuální sítě. V tomto rychlém startu se dozvíte, jak vytvořit službu privátního propojení pomocí Azure Portal.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Vytvoření interního nástroje pro vyrovnávání zatížení

Nejdřív vytvořte virtuální síť a pak interní nástroj pro vyrovnávání zatížení, který se použije ve službě Azure Private Link.

### <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

V této části vytvoříte Virtual Network a podsíť pro hostování nástroje pro vyrovnávání zatížení, který se používá pro přístup ke službě privátních odkazů.


1. Na straně levého horního rohu portálu, vyberte **vytvořit prostředek** > **sítě** > **virtuální síť**.
   
1. V **vytvořit virtuální síť** podokně, zadejte nebo vyberte tyto hodnoty:
   
   - **Název**: typ **MyVNet**.
   - **Skupina prostředků**: vyberte **vytvořit nový**, zadejte **MyResourceGroupLB**a vyberte **OK**. 
   - **Podsíť** > **název**: typ **MyBackendSubnet**.
   
1. Vyberte **Vytvořit**.

   ![Vytvoření virtuální sítě](../load-balancer/media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-a-standard-load-balancer"></a>Vytvoření standardního nástroje pro vyrovnávání zatížení

Pomocí portálu vytvořte standardní interní nástroj pro vyrovnávání zatížení. Název a IP adresu, kterou vytvoříte, se automaticky nakonfiguruje jako front-endu nástroje pro vyrovnávání zatížení.

1. V levém horním rohu webu Azure Portal klikněte na **Vytvořit prostředek** > **Sítě** > **Nástroj pro vyrovnávání zatížení**.
   
2. Na kartě **základy** na stránce **vytvořit službu Vyrovnávání zatížení** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte své předplatné.    |    
    | Skupina prostředků         | V rozevíracím seznamu vyberte *MyResourceGroupLB* .|
    | Name (Název)                   | *myLoadBalancer*                                   |
    | Region (Oblast)         | Vyberte **Východní USA 2**.                                        |
    | Typ          | Vyberte **interní**.                                        |
    | Skladová položka           | Vyberte **Standard**.                          |
    | Virtuální síť           | Vyberte *MyVNet*.                          |    
    | Přiřazení IP adresy              | Vyberte možnost **static**.   |
    | Privátní IP adresa|Zadejte adresu, která se nachází v adresním prostoru virtuální sítě a podsítě, například *10.3.0.7*.  |

3. Na kartě **Revize + vytvořit** klikněte na **vytvořit**. 
   

### <a name="create-standard-load-balancer-resources"></a>Vytvoření prostředků standardního nástroje pro vyrovnávání zatížení

V této části nakonfigurujete nastavení nástroje pro vyrovnávání zatížení pro fond back-end adres a sondu stavu a zadáte pravidla nástroje pro vyrovnávání zatížení.

#### <a name="create-a-backend-pool"></a>Vytvoření fondu back-endu

Pro distribuci provozu do prostředků obsahuje fond back-end adres IP adresy virtuálních síťových adaptérů (nic) připojených k Load Balancer. Vytvořte fond back-end adres *myBackendPool* , který bude zahrnovat prostředky pro provoz vyrovnávání zatížení.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředků vyberte **myLoadBalancer** .
2. V části **Nastavení**vyberte **back-end fondy**a pak vyberte **Přidat**.
3. Do pole název na stránce **Přidat fond back-end** serveru zadejte *myBackendPool*jako název vašeho back-end fondu a pak vyberte **Přidat**.



#### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Pokud chcete, aby nástroj pro vyrovnávání zatížení mohl monitorovat stav prostředků, použijte sondu stavu. Sonda stavu dynamicky přidává nebo odebírá prostředky z rotace nástroje pro vyrovnávání zatížení na základě jejich odpovědí na kontroly stavu. 

**Vytvoření sondy stavu pro monitorování stavu prostředků:**

1. Vyberte **všechny prostředky** v nabídce vlevo a pak vyberte **MyLoadBalancer** ze seznamu prostředků.
   
1. V **Nastavení** vyberte **Sondy stavu** a potom vyberte **Přidat**.
   
1. Na **přidat sondu stavu** stránky zadejte nebo vyberte následující hodnoty:
   
   - **Název**: typ **MyHealthProbe**.
   - **Protokol**: rozevírací seznam a výběr **TCP**. 
   - **Port**: typ **80**. 
   - **Interval**: typ **15**. Interval je počet sekund mezi pokusy o testování.
   - **Prahová hodnota špatného stavu**: typ **2**. Tato hodnota je počet chyb po sobě jdoucích sondování, ke kterým dojde před virtuální počítač považoval za poškozený.
   
1. Vyberte **OK**.

#### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje způsob distribuce provozu do prostředků. Pravidlo definuje konfiguraci front-end IP adresy pro příchozí provoz, back endového fondu IP pro příjem provozu a požadované zdrojových a cílových portů. 

Pravidlo nástroje pro vyrovnávání zatížení s názvem **MyLoadBalancerRule** naslouchá na portu 80 ve front-endu **LoadBalancerFrontEnd**. Toto pravidlo automaticky odesílá síťový provoz do fondu back endových adres **MyBackendPool**, rovněž na portu 80. 

**Vytvořte pravidlo nástroje pro vyrovnávání zatížení:**

1. Vyberte **všechny prostředky** v nabídce vlevo a pak vyberte **MyLoadBalancer** ze seznamu prostředků.
   
1. V části **Nastavení**vyberte **pravidla vyrovnávání zatížení**a pak vyberte **Přidat**.
   
1. Na stránce **Přidat pravidlo vyrovnávání zatížení** zadejte nebo vyberte následující hodnoty (Pokud ještě nejsou přítomny):
   
   - **Název**: typ **MyLoadBalancerRule**.
   - **Front-endovou IP adresu:** typ **LoadBalancerFrontEnd** Pokud není k dispozici.
   - **Protokol**: vyberte **TCP**.
   - **Port**: typ **80**.
   - **Back-endový port**: typ **80**.
   - **Back-endový fond**: vyberte **MyBackendPool**.
   - **Sonda stavu**: vyberte **MyHealthProbe**. 
   
1. Vyberte **OK**.
   
## <a name="create-a-private-link-service"></a>Vytvoření služby privátního propojení

V této části vytvoříte službu privátního propojení za standardním nástrojem pro vyrovnávání zatížení.

1. V levém horním rohu obrazovky v Azure Portal vyberte **vytvořit prostředek** > **sítě** > **privátní Link Center (Preview)** . Můžete také vyhledat privátní odkaz prostřednictvím vyhledávání na portálu.

2. V **centru privátních odkazů – přehled**, na možnost **"vystavení vlastní služby, aby se ostatní mohli připojit"** vyberte spustit.

3. V nástroji **vytvořit služby privátního propojení – základy**zadejte nebo vyberte tyto informace:

    | Nastavení           | Hodnota                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | **Podrobnosti o projektu:**  |                                                                              |
    | Předplatné      | Vyberte své předplatné.                                                     |
    | Skupina prostředků    | Vybrat *MyResourceGroupLB*                                                     |
    | **PODROBNOSTI INSTANCE:** |                                                                              |
    | Name (Název)              | Zadejte *myPrivateLinkService* |
    | Region (Oblast)            | Vyberte *východní USA 2*                                                         |

4. Vyberte **Další: odchozí nastavení**.

5. V **možnosti vytvořit službu privátního propojení – odchozí nastavení**zadejte nebo vyberte tyto informace:


    | Nastavení                           | Hodnota                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | Load Balancer                     | Vybrat *MyLoadBalancer*                                                           |
    | Load Balancer IP adresa front-endu | Vyberte front-end IP adresu MyLoadBalancer.                                |
    | Zdrojová virtuální síť NAT        | Vybrat *myVNET*                                                                   |
    | Zdrojová podsíť NAT                 | Vybrat *myBackendSubnet*                                                          |
    | Povolit proxy server TCP v2               | V závislosti na tom, jestli má vaše aplikace očekávanou hlavičku protokolu TCP v2, vyberte Ano/ne. |
    | Nastavení privátní IP adresy       | Pro každou IP adresu NAT nakonfigurujte metodu přidělování a IP adresu.                  |

6. Vyberte **Další: přístup k zabezpečení**.

7. V nástroji **vytvořit privátní Link Service – přístup k zabezpečení**zadejte nebo vyberte tyto informace:

    | Nastavení                                     | Hodnota                                                             |
    |---------------------------------------------|-------------------------------------------------------------------|
    | Viditelnost                                  | Výběr *pouze řízení přístupu na základě role*         |
  
8. Vyberte **Další: značky**a potom **Zkontrolujte + vytvořit** nebo zvolte **kartu zkontrolovat + vytvořit** v horní části stránky.

9. Zkontrolujte své údaje a vyberte **vytvořit**.

## <a name="clean-up-resources"></a>Vyčištění prostředků 
Až budete s použitím služby privátního propojení hotovi, odstraňte skupinu prostředků, abyste mohli vyčistit prostředky používané v tomto rychlém startu:

1. Do **vyhledávacího** pole v horní části portálu zadejte *myResourceGroupLB* a ve výsledcích hledání vyberte *myResourceGroupLB* . 
2. Vyberte **Odstranit skupinu prostředků**. 
3. Zadejte myResourceGroup pro **typ název skupiny prostředků** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili interní nástroj pro vyrovnávání zatížení Azure a službu privátního propojení. Další informace o vytváření privátních koncových bodů najdete v tématu [Vytvoření privátních koncových bodů pomocí Azure Portal](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal).
