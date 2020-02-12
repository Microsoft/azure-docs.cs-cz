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
ms.openlocfilehash: f62adbaea8d6549af0137f49542ee89e7531b9ef
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77136171"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Rychlý Start: vytvoření služby privátního propojení pomocí Azure Portal

Služba privátního propojení Azure odkazuje na vaši vlastní službu, která je spravovaná pomocí privátního propojení. Přístup ke službě nebo prostředku, který funguje za Azure Load Balancer, můžete udělit privátním odkazem. Příjemci vaší služby můžou k němu přistupovat soukromě z vlastních virtuálních sítí. V tomto rychlém startu se dozvíte, jak vytvořit službu privátního propojení pomocí Azure Portal.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Vytvořte interní nástroj pro vyrovnávání zatížení.

Nejdřív vytvořte virtuální síť. Dále vytvořte interní nástroj pro vyrovnávání zatížení, který bude použit ve službě privátního propojení.

### <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

V této části vytvoříte virtuální síť. Také vytvoříte podsíť pro hostování nástroje pro vyrovnávání zatížení, který přistupuje ke službě privátního propojení.

1. V levé horní části portálu vyberte **vytvořit prostředek** > **síť** > **virtuální síť**.

1. V podokně **vytvořit virtuální síť** zadejte nebo vyberte tyto hodnoty:

   - **Název**: zadejte **MyVNet**.
   - **Zdroj dat**: vyberte **vytvořit novou**, zadejte **MyResourceGroupLB**a vyberte **OK**.
   - **Název** > **podsítě** : zadejte **MyBackendSubnet**.

1. Vyberte **Create** (Vytvořit).

   ![Vytvoření virtuální sítě](../load-balancer/media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-a-standard-load-balancer"></a>Vytvoření standardního nástroje pro vyrovnávání zatížení

Pomocí portálu vytvořte standardní interní nástroj pro vyrovnávání zatížení. Název a IP adresa, které zadáte, se automaticky nakonfigurují jako front-end nástroje pro vyrovnávání zatížení.

1. V levém horním rohu webu Azure Portal klikněte na **Vytvořit prostředek** > **Sítě** > **Nástroj pro vyrovnávání zatížení**.

1. Na kartě **základy** na stránce **vytvořit nástroj pro vyrovnávání zatížení** zadejte nebo vyberte následující informace:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | **Předplatné**               | Vyberte své předplatné.    |
    | **Skupina prostředků**         | V poli vyberte **MyResourceGroupLB** .|
    | **Název**                   | Zadejte **myLoadBalancer**.                                   |
    | **Oblast**         | Vyberte **Východní USA 2**.                                        |
    | **Typ**          | Vyberte **interní**.                                        |
    | **SKU**           | Vyberte **Standard**.                          |
    | **Virtuální síť**           | Vyberte **MyVNet**.                          |
    | **Přiřazení IP adresy**              | Vyberte možnost **static**.   |
    | **Privátní IP adresa**|Zadejte adresu, která se nachází v adresním prostoru virtuální sítě a podsítě. Příkladem je 10.3.0.7.  |

1. U zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit** .

1. Na kartě **Revize + vytvořit** vyberte **vytvořit**.

### <a name="create-standard-load-balancer-resources"></a>Vytvoření prostředků standardního nástroje pro vyrovnávání zatížení

V této části nakonfigurujete nastavení nástroje pro vyrovnávání zatížení pro fond back-endových adres a sondu stavu. Také zadáte pravidla nástroje pro vyrovnávání zatížení.

#### <a name="create-a-back-end-pool"></a>Vytvoření fondu back-endu

Fond adres back-endu obsahuje IP adresy virtuálních síťových karet připojených k nástroji pro vyrovnávání zatížení. Tento fond vám umožní distribuovat provoz do vašich prostředků. Vytvořte fond back-endové adresy s názvem **myBackendPool** , který bude zahrnovat prostředky vyrovnávající zatížení.

1. V nabídce úplně vlevo vyberte **všechny služby** .
1. Vyberte **všechny prostředky**a potom v seznamu prostředků vyberte **myLoadBalancer** .
1. V **Nastavení** vyberte **Back-endové fondy** a potom vyberte **Přidat**.
1. Na stránce **Přidat fond back-endu** jako název svého fondu back-end zadejte **myBackendPool** a pak vyberte **Přidat**.

#### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Použijte sondu stavu, aby nástroj pro vyrovnávání zatížení mohl monitorovat stav prostředků. Test stavu založený na reakci prostředků na kontroly stavu dynamicky přidává nebo odebírá prostředky z rotace nástroje pro vyrovnávání zatížení.

Vytvoření sondy stavu pro monitorování stavu prostředků:

1. V nabídce vlevo vyberte **všechny prostředky** a v seznamu prostředků vyberte **MyLoadBalancer** .

1. V **Nastavení** vyberte **Sondy stavu** a potom vyberte **Přidat**.

1. Na stránce **Přidat sondu stavu** zadejte nebo vyberte následující hodnoty:

   - **Název**: zadejte **MyHealthProbe**.
   - **Protokol**: vyberte **TCP**.
   - **Port**: zadejte **80**.
   - **Interval**: zadejte **15**. Tato hodnota je počet sekund mezi pokusy o testování.
   - **Prahová hodnota chybného stavu**: zadejte **2**. Tato hodnota představuje počet po sobě jdoucích selhání testu, ke kterým dojde předtím, než se virtuální počítač považuje za poškozený.

1. Vyberte **OK**.

#### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje způsob distribuce provozu do prostředků. Pravidlo definuje:

- Konfigurace front-endové IP adresy pro příchozí provoz.
- Fond back-end IP adres pro příjem provozu.
- Požadované zdrojové a cílové porty.

Pravidlo nástroje pro vyrovnávání zatížení s názvem **MyLoadBalancerRule** naslouchá na portu 80 ve front-endu **LoadBalancerFrontEnd** . Pravidlo odesílá síťový provoz do fondu back-end adres **MyBackendPool** na stejném portu 80.

Vytvoření pravidla nástroje pro vyrovnávání zatížení:

1. V nabídce vlevo vyberte **všechny prostředky** a v seznamu prostředků vyberte **MyLoadBalancer** .

1. V části **Nastavení**vyberte **pravidla vyrovnávání zatížení**a pak vyberte **Přidat**.

1. Na stránce **Přidat pravidlo vyrovnávání zatížení** zadejte nebo vyberte následující hodnoty, pokud ještě nejsou k dispozici:

   - **Název**: zadejte **MyLoadBalancerRule**.
   - **IP adresa front-endu:** Zadejte **LoadBalancerFrontEnd**.
   - **Protokol**: vyberte **TCP**.
   - **Port**: zadejte **80**.
   - **Back-end port**: zadejte **80**.
   - **Back-end fond**: vyberte **MyBackendPool**.
   - **Sonda stavu**: vyberte **MyHealthProbe**. 

1. Vyberte **OK**.

## <a name="create-a-private-link-service"></a>Vytvoření služby privátního propojení

V této části vytvoříte službu privátního propojení za standardním nástrojem pro vyrovnávání zatížení.

1. V levé horní části stránky v Azure Portal vyberte **vytvořit prostředek** > **síti** > **soukromém centru odkazů (Preview)** . Můžete také použít vyhledávací pole na portálu k vyhledání privátního odkazu.

1. V **centru privátních odkazů – přehled** > **zveřejnění vlastní služby, aby se ostatní mohli připojit**, vyberte **Spustit**.

1. V části **vytvořit základní službu privátního propojení**zadejte nebo vyberte tyto informace:

    | Nastavení           | Hodnota                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | Podrobnosti o projektu:  |                                                                              |
    | **Předplatné**      | Vyberte své předplatné.                                                     |
    | **Skupina prostředků**    | Vyberte **MyResourceGroupLB**.                                                    |
    | Podrobnosti instance: |                                                                              |
    | **Název**              | Zadejte **myPrivateLinkService**. |
    | **Oblast**            | Vyberte **Východní USA 2**.                                                        |

1. Vyberte **Další: odchozí nastavení**.

1. V části **vytvořit službu privátního propojení – odchozí nastavení**zadejte nebo vyberte tyto informace:

    | Nastavení                           | Hodnota                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | **Load Balancer**                     | Vyberte **MyLoadBalancer**.                                                           |
    | **Load Balancer IP adresa front-endu** | Vyberte front-end IP adresu **MyLoadBalancer**.                                |
    | **Zdrojová virtuální síť NAT**        | Vyberte **myVNET**.                                                                   |
    | **Zdrojová podsíť NAT**                 | Vyberte **myBackendSubnet**.                                                          |
    | **Povolit proxy server TCP v2**               | Vyberte **Ano** nebo **ne** v závislosti na tom, jestli vaše aplikace očekává hlavičku protokolu TCP proxy v2. |
    | **Nastavení privátní IP adresy**       | Pro každou IP adresu NAT nakonfigurujte metodu přidělování a IP adresu.                  |

1. Vyberte **Další: přístup k zabezpečení**.

1. V části **vytvořit zabezpečení přístupu ke službě privátního propojení**vyberte možnost **viditelnost**a pak zvolte možnost **řízení přístupu na základě role**.
  
1. Vyberte **Další: značky** > **zkontrolovat + vytvořit** nebo zvolte kartu **zkontrolovat + vytvořit** v horní části stránky.

1. Zkontrolujte své údaje a vyberte **vytvořit**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s použitím služby privátního propojení hotovi, odstraňte skupinu prostředků, abyste mohli vyčistit prostředky používané v tomto rychlém startu.

1. Do vyhledávacího pole v horní části portálu zadejte **myResourceGroupLB** a ve výsledcích hledání vyberte **myResourceGroupLB** .
1. Vyberte **Odstranit skupinu prostředků**.
1. Do pole **Zadejte název skupiny prostředků**zadejte **myResourceGroup**.
1. Vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili interní nástroj pro vyrovnávání zatížení Azure a službu privátního propojení. Můžete se také dozvědět, jak [vytvořit privátní koncový bod pomocí Azure Portal](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal).
