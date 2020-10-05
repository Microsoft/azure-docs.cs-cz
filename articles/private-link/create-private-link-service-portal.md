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
ms.openlocfilehash: 0d873401d377a03581a319769604f3d976f365be
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87927234"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Rychlý Start: vytvoření služby privátního propojení pomocí Azure Portal

Služba privátního propojení Azure odkazuje na vaši vlastní službu, která je spravovaná pomocí privátního propojení. Přístup ke službě nebo prostředku, který funguje za Azure Standard Load Balancer, můžete udělit privátním odkazem. Příjemci vaší služby můžou k němu přistupovat soukromě z vlastních virtuálních sítí. V tomto rychlém startu se dozvíte, jak vytvořit službu privátního propojení pomocí Azure Portal.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Vytvořte interní nástroj pro vyrovnávání zatížení.

Nejdřív vytvořte virtuální síť. Dále vytvořte interní nástroj pro vyrovnávání zatížení, který bude použit ve službě privátního propojení.

## <a name="virtual-network-and-parameters"></a>Virtuální síť a parametry

V této části vytvoříte virtuální síť. Také vytvoříte podsíť pro hostování nástroje pro vyrovnávání zatížení, který přistupuje ke službě privátního propojení.

V této části budete muset v následujících krocích nahradit následující parametry následujícími informacemi:

| Parametr                   | Hodnota                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | USA – východ 2      |
| **\<IPv4-address-space>**   | 10.3.0.0/16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.3.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-a-standard-load-balancer"></a>Vytvoření standardního nástroje pro vyrovnávání zatížení

Pomocí portálu vytvořte standardní interní nástroj pro vyrovnávání zatížení. Název a IP adresa, které zadáte, se automaticky nakonfigurují jako front-end nástroje pro vyrovnávání zatížení.

1. V levé horní části portálu vyberte **vytvořit prostředek**  >  **síť**  >  **Load Balancer**.

1. Na kartě **základy** na stránce **vytvořit nástroj pro vyrovnávání zatížení** zadejte nebo vyberte následující informace:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | **Předplatné**               | Vyberte předplatné.    |
    | **Skupina prostředků**         | V poli vyberte **myResourceGroupLB** .|
    | **Název**                   | Zadejte **myLoadBalancer**.                                   |
    | **Oblast**         | Vyberte **USA – východ 2**.                                        |
    | **Typ**          | Vyberte **interní**.                                        |
    | **Skladová jednotka (SKU)**           | Vyberte **Standard**.                          |
    | **Virtuální síť**           | Vyberte **myVNet**.                          |
    | **Přiřazení IP adresy**              | Vyberte **Statické**.   |
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

1. V nabídce vlevo vyberte **všechny prostředky** a v seznamu prostředků vyberte **myLoadBalancer** .

1. V **Nastavení** vyberte **Sondy stavu** a potom vyberte **Přidat**.

1. Na stránce **Přidat sondu stavu** zadejte nebo vyberte následující hodnoty:

   - **Název**: zadejte **myHealthProbe**.
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

Pravidlo nástroje pro vyrovnávání zatížení s názvem **myLoadBalancerRule** naslouchá na portu 80 ve front-endu **LoadBalancerFrontEnd** . Pravidlo odesílá síťový provoz do fondu back-end adres **myBackendPool** na stejném portu 80.

Vytvoření pravidla nástroje pro vyrovnávání zatížení:

1. V nabídce vlevo vyberte **všechny prostředky** a v seznamu prostředků vyberte **myLoadBalancer** .

1. V části **Nastavení**vyberte **pravidla vyrovnávání zatížení**a pak vyberte **Přidat**.

1. Na stránce **Přidat pravidlo vyrovnávání zatížení** zadejte nebo vyberte následující hodnoty, pokud ještě nejsou k dispozici:

   - **Název**: zadejte **myLoadBalancerRule**.
   - **IP adresa front-endu:** Zadejte **LoadBalancerFrontEnd**.
   - **Protokol**: vyberte **TCP**.
   - **Port**: zadejte **80**.
   - **Back-end port**: zadejte **80**.
   - **Back-end fond**: vyberte **myBackendPool**.
   - **Sonda stavu**: vyberte **myHealthProbe**. 

1. Vyberte **OK**.

## <a name="create-a-private-link-service"></a>Vytvoření služby privátního propojení

V této části vytvoříte službu privátního propojení za standardním nástrojem pro vyrovnávání zatížení.

1. V levé horní části stránky v Azure Portal vyberte **vytvořit prostředek**  >  **sítě**  >  **privátní Link Center (Preview)**. Můžete také použít vyhledávací pole na portálu k vyhledání privátního odkazu.

1. V **centru privátních odkazů – přehled**  >  **vystavení vlastní služby, aby se ostatní mohli připojit**, vyberte **Spustit**.

1. V části **vytvořit základní službu privátního propojení**zadejte nebo vyberte tyto informace:

    | Nastavení           | Hodnota                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | Podrobnosti o projektu:  |                                                                              |
    | **Předplatné**      | Vyberte předplatné.                                                     |
    | **Skupina prostředků**    | Vyberte **myResourceGroupLB**.                                                    |
    | Podrobnosti instance: |                                                                              |
    | **Název**              | Zadejte **myPrivateLinkService**. |
    | **Oblast**            | Vyberte **USA – východ 2**.                                                        |

1. Vyberte **Další: odchozí nastavení**.

1. V části **vytvořit službu privátního propojení – odchozí nastavení**zadejte nebo vyberte tyto informace:

    | Nastavení                           | Hodnota                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | **Load Balancer**                     | Vyberte **myLoadBalancer**.                                                           |
    | **Load Balancer IP adresa front-endu** | Vyberte front-end IP adresu **myLoadBalancer**.                                |
    | **Zdrojová virtuální síť NAT**        | Vyberte **myVNet**.                                                                   |
    | **Zdrojová podsíť NAT**                 | Vyberte **myBackendSubnet**.                                                          |
    | **Povolit proxy server TCP v2**               | Vyberte **Ano** nebo **ne** v závislosti na tom, jestli vaše aplikace očekává hlavičku protokolu TCP proxy v2. |
    | **Nastavení privátní IP adresy**       | Pro každou IP adresu NAT nakonfigurujte metodu přidělování a IP adresu.                  |

1. Vyberte **Další: přístup k zabezpečení**.

1. V části **vytvořit zabezpečení přístupu ke službě privátního propojení**vyberte možnost **viditelnost**a pak zvolte možnost **řízení přístupu na základě role**.
  
1. Vyberte možnost **Další:**  >  **Revize značek + vytvořit** nebo zvolte kartu **zkontrolovat + vytvořit** v horní části stránky.

1. Zkontrolujte své údaje a vyberte **vytvořit**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení používání služby privátního propojení odstraňte skupinu prostředků, abyste mohli vyčistit prostředky používané v tomto rychlém startu.

1. Do vyhledávacího pole v horní části portálu zadejte **myResourceGroupLB** a ve výsledcích hledání vyberte **myResourceGroupLB** .
1. Vyberte **Odstranit skupinu prostředků**.
1. Do pole **Zadejte název skupiny prostředků**zadejte **myResourceGroup**.
1. Vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili interní nástroj pro vyrovnávání zatížení Azure a službu privátního propojení. Můžete se také dozvědět, jak [vytvořit privátní koncový bod pomocí Azure Portal](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal).
