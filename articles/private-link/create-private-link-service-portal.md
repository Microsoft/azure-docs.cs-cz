---
title: Úvodní příručka – vytvoření služby Private Link pomocí portálu Azure
titlesuffix: Azure Private Link
description: Zjistěte, jak vytvořit službu Private Link pomocí portálu Azure v tomto rychlém startu
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: allensu
ms.openlocfilehash: f21b440ee0e2c53d9824300e85b683629c1575da
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78252550"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Úvodní příručka: Vytvoření služby Private Link pomocí portálu Azure

Služba Azure Private Link odkazuje na vlastní službu, kterou spravuje privátní link. Privátní propojení můžete udělit přístup ke službě nebo prostředku, který funguje za Azure Standard Load Balancer. Spotřebitelé vaší služby k ní mají přístup soukromě ze svých vlastních virtuálních sítí. V tomto rychlém startu se dozvíte, jak vytvořit službu Private Link pomocí portálu Azure.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Vytvořte interní nástroj pro vyrovnávání zatížení.

Nejprve vytvořte virtuální síť. Dále vytvořte interní vyrovnávání zatížení pro použití se službou Private Link.

## <a name="virtual-network-and-parameters"></a>Virtuální síť a parametry

V této části vytvoříte virtuální síť. Můžete také vytvořit podsíť pro hostování vyrovnávání zatížení, který přistupuje k vaší službě Private Link.

V této části budete muset nahradit následující parametry v krocích s níže uvedenými informacemi:

| Parametr                   | Hodnota                |
|-----------------------------|----------------------|
| **\<>název skupiny prostředků**  | myResourceGroupLB |
| **\<>názvů virtuálních sítí** | myVNet          |
| **\<>názvu oblasti**          | USA – východ 2      |
| **\<>adresního prostoru IPv4**   | 10.3.0.0\16          |
| **\<>názvu podsítě**          | myBackendSubnet        |
| **\<>rozsah emitované sítě** | 10.3.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-a-standard-load-balancer"></a>Vytvoření standardního nástroje pro vyrovnávání zatížení

Pomocí portálu vytvořte standardní interní systém vyrovnávání zatížení. Název a adresa IP, které zadáte, jsou automaticky konfigurovány jako front-end vykladače zatížení.

1. Na levé horní straně portálu vyberte Vytvořit nástroje**pro vyrovnávání zatížení****sítě** >  **.** > 

1. Na kartě **Základy** na stránce **Vytvořit vyrovnávání zatížení** zadejte nebo vyberte následující informace:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | **Předplatné**               | Vyberte své předplatné.    |
    | **Skupina prostředků**         | V poli vyberte **myResourceGroupLB.**|
    | **Název**                   | Zadejte **myLoadBalancer**.                                   |
    | **Oblasti**         | Vyberte **USA – východ 2**.                                        |
    | **Typ**          | Vyberte **interní**.                                        |
    | **Sku**           | Vyberte **standardní**.                          |
    | **Virtuální síť**           | Vyberte **myVNet**.                          |
    | **Přiřazení IP adresy**              | Vyberte **možnost Statické**.   |
    | **Privátní IP adresa**|Zadejte adresu, která je v adresním prostoru virtuální sítě a podsítě. Příkladem je 10.3.0.7.  |

1. Přijměte výchozí hodnoty pro zbývající nastavení a pak vyberte **Zkontrolovat + vytvořit**

1. Na kartě **Revize + vytvoření** vyberte **Vytvořit**.

### <a name="create-standard-load-balancer-resources"></a>Vytvoření standardních prostředků pro vyrovnávání zatížení

V této části nakonfigurujete nastavení nástroje pro vyrovnávání zatížení pro fond back-endových adres a sondu stavu. Můžete také zadat pravidla vyrovnávání zatížení.

#### <a name="create-a-back-end-pool"></a>Vytvoření back-endového fondu

Fond adres back-end obsahuje IP adresy virtuálních nic o připojení k ninic připojených k nástroji pro vyrovnávání zatížení. Tento fond umožňuje distribuovat provoz na vaše prostředky. Vytvořte fond adres back-end s názvem **myBackendPool** tak, aby zahrnoval prostředky, které provoz vyrovnávání zatížení.

1. V nabídce zcela vlevo vyberte **Všechny služby.**
1. Vyberte **Všechny prostředky**a pak vyberte **myLoadBalancer** ze seznamu zdrojů.
1. V **Nastavení** vyberte **Back-endové fondy** a potom vyberte **Přidat**.
1. Na stránce **Přidat back-endový fond** zadejte **myBackendPool** jako název back-endového fondu a pak vyberte **Přidat**.

#### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Pomocí sondy stavu nechte nástroj pro vyrovnávání zatížení sledovat stav prostředku. Na základě reakce prostředků na kontroly stavu sonda stavu dynamicky přidává nebo odebere prostředky z rotace vykladače zatížení.

Chcete-li vytvořit sondu stavu pro sledování stavu prostředků:

1. Vyberte **všechny prostředky** v nabídce zcela vlevo a pak vyberte **myLoadBalancer** ze seznamu zdrojů.

1. V **Nastavení** vyberte **Sondy stavu** a potom vyberte **Přidat**.

1. Na stránce **Přidat sondu stavu** zadejte nebo vyberte následující hodnoty:

   - **Název**: Zadejte **myHealthProbe**.
   - **Protokol**: Vyberte **tcp**.
   - **Port**: Zadejte **80**.
   - **Interval**: Zadejte **15**. Tato hodnota je počet sekund mezi pokusy o sondu.
   - **Prahová hodnota není v pořádku**: Zadejte **2**. Tato hodnota je počet po sobě jdoucích selhání sondy, ke kterým dochází před virtuální počítač je považován za není v pořádku.

1. Vyberte **OK**.

#### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo vyrovnávání zatížení definuje způsob distribuce provozu k prostředkům. Pravidlo definuje:

- Konfigurace IP adres front-endu pro příchozí přenosy.
- Back-end fond IP přijímat provoz.
- Požadované zdrojové a cílové porty.

Pravidlo pro vyrovnávání zatížení s názvem **myLoadBalancerRule** naslouchá portu 80 v front-endu **LoadBalancerFrontEnd.** Pravidlo odesílá síťový provoz do fondu back-endových adres **myBackendPool** na stejném portu 80.

Vytvoření pravidla pro vyrovnávání zatížení:

1. Vyberte **všechny prostředky** v nabídce zcela vlevo a pak vyberte **myLoadBalancer** ze seznamu zdrojů.

1. V části **Nastavení**vyberte **Pravidla vyrovnávání zatížení**a pak vyberte **Přidat**.

1. Na stránce **Přidat pravidlo vyrovnávání zatížení** zadejte nebo vyberte následující hodnoty, pokud ještě nejsou k dispozici:

   - **Název**: Zadejte **myLoadBalancerRule**.
   - **Ip adresa front-endu:** Zadejte **LoadBalancerFrontEndEnd**.
   - **Protokol**: Vyberte **tcp**.
   - **Port**: Zadejte **80**.
   - **Back-end port**: Zadejte **80**.
   - **Back-end ový fond**: Vyberte **myBackendPool**.
   - **Zdravotní sonda**: Vyberte **myHealthProbe**. 

1. Vyberte **OK**.

## <a name="create-a-private-link-service"></a>Vytvoření služby privátního propojení

V této části vytvoříte službu Private Link za standardním vyrovnáváním zatížení.

1. V levé horní části stránky na webu Azure Portal vyberte Vytvořit**Networking** > **privátní centrum připojení k** **prostředkům** > (Preview). Můžete také použít vyhledávací pole portálu k vyhledání soukromého odkazu.

1. V **Centru soukromých odkazů – přehled** > **Zpřístupněte vlastní službu, aby se ostatní mohli připojit**, vyberte možnost **Start**.

1. V části **Vytvořit službu soukromého propojení – základy**zadejte nebo vyberte tyto informace:

    | Nastavení           | Hodnota                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | Podrobnosti o projektu:  |                                                                              |
    | **Předplatné**      | Vyberte své předplatné.                                                     |
    | **Skupina prostředků**    | Vyberte **položku myResourceGroupLB**.                                                    |
    | Podrobnosti o instanci: |                                                                              |
    | **Název**              | Zadejte **myPrivateLinkService**. |
    | **Oblasti**            | Vyberte **USA – východ 2**.                                                        |

1. Vyberte **další: Odchozí nastavení**.

1. V části **Vytvořit soukromou službu propojení – Nastavení odchozích služeb**zadejte nebo vyberte tyto informace:

    | Nastavení                           | Hodnota                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | **Load Balancer**                     | Vyberte **myLoadBalancer**.                                                           |
    | **Ip adresa front-endu pro vyrovnávání zatížení** | Vyberte front-endIP adresu **myLoadBalancer**.                                |
    | **Zdroj virtuální sítě NAT**        | Vyberte **myVNet**.                                                                   |
    | **Zdrojová podsíť NAT**                 | Vyberte **možnost myBackendSubnet**.                                                          |
    | **Povolení serveru Proxy TCP v2**               | Vyberte **ANO** nebo **NE** v závislosti na tom, zda vaše aplikace očekává hlavičku proxy TCP v2. |
    | **Nastavení privátní IP adresy**       | Nakonfigurujte metodu přidělení a adresu IP pro každou adresu IP.                  |

1. Vyberte **další: Přístup k zabezpečení**.

1. V části **Vytvořit soukromou službu propojení – zabezpečení přístupu**vyberte **Visibility**a pak zvolte **pouze řízení přístupu na základě rolí**.
  
1. Buď vyberte **Další: Značky** > **Revize + vytvoření** nebo zvolte kartu Revize + **vytvoření** v horní části stránky.

1. Zkontrolujte informace a vyberte **Vytvořit**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení používání služby Private Link odstraňte skupinu prostředků a vyčistěte prostředky použité v tomto rychlém startu.

1. Zadejte **myResourceGroupLB** do vyhledávacího pole v horní části portálu a z výsledků hledání vyberte **myResourceGroupLB.**
1. Vyberte **Odstranit skupinu prostředků**.
1. Do **pole ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ**zadejte **myResourceGroup**.
1. Vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili interní azure vyrovnávání zatížení a službu Private Link. Můžete se také dozvědět, jak [vytvořit soukromý koncový bod pomocí portálu Azure](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal).
