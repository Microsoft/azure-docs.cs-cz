---
title: Vytvoření sítě VPN mezi Azure a AWS pomocí spravovaných řešení
description: Jak vytvořit připojení VPN mezi Azure a AWS pomocí spravovaných řešení, nikoli virtuálních počítačů nebo zařízení.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: ricmmartins
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 01/15/2021
ms.author: ricmart
ms.openlocfilehash: 3b9e60eb037182318e9d1ef7336565908a9c8f32
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664779"
---
# <a name="create-a-vpn-connection-between-azure-and-aws-using-managed-solutions"></a>Vytvoření připojení VPN mezi Azure a AWS pomocí spravovaných řešení

Pomocí spravovaných řešení můžete navázat spojení mezi Azure a AWS. Dříve jste museli použít zařízení nebo virtuální počítač fungující jako respondér. Nyní můžete virtuální privátní bránu AWS připojit k Azure VPN Gateway přímo, aniž byste se museli starat o správu prostředků IaaS, jako jsou virtuální počítače. Tento článek vám pomůže vytvořit připojení VPN mezi Azure a AWS jenom pomocí spravovaných řešení.

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/diagram.png" alt-text="Diagram architektury":::

## <a name="configure-azure"></a>Konfigurace Azure

### <a name="configure-a-virtual-network"></a>Konfigurace virtuální sítě

Nakonfigurujte virtuální síť. Pokyny najdete v tématu [rychlý start Virtual Network](../virtual-network/quick-create-portal.md).

V tomto článku se používají následující příklady hodnot:

* **Skupina prostředků:** RG – Azure-AWS
* **Oblast:** Východní USA
* **Název virtuální sítě:** virtuální síť – Azure
* **Adresní prostor IPv4:** 172.10.0.0/16
* **Název podsítě:** podsíť-01
* **Rozsah adres podsítě:** 172.10.1.0/24

### <a name="create-a-vpn-gateway"></a>Vytvoření brány VPN

Vytvořte bránu VPN pro virtuální síť. Pokyny najdete v tématu [kurz: vytvoření a Správa služby VPN Gateway](tutorial-create-gateway-portal.md).

V tomto článku se používají následující příklady hodnot a nastavení:

* **Typ brány:** S2S
* **Typ sítě VPN:** Založené na trasách
* **SKU:** VpnGw1
* **Generování:** Generace 1
* **Virtuální síť:** Musí se jednat o virtuální síť, pro kterou chcete vytvořit bránu.
* **Rozsah adres podsítě brány:** 172.10.0.0/27
* **Veřejná IP adresa:** Vytvořit nový
* **Název veřejné IP adresy:** PIP-VPN-Azure-AWS
* **Povolit režim aktivní-aktivní:** Dezaktivovat
* **Konfigurace protokolu BGP:** Dezaktivovat

Příklad:

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/summary.png" alt-text="Souhrn brány virtuální sítě":::

## <a name="configure-aws"></a>Konfigurace AWS

1. Vytvořte virtuální privátní cloud (VPC).

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpc.png" alt-text="Vytvořit VPC informace":::

1. Vytvořte podsíť uvnitř VPC (virtuální síť).

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-subnet-vpc.png" alt-text="Vytvoření podsítě":::

1. Vytvořte zákaznickou bránu, která odkazuje na veřejnou IP adresu Azure VPN Gateway. **Brána zákazníka** je prostředek AWS, který obsahuje informace pro AWS o zařízení brány zákazníka, což je v tomto případě VPN Gateway Azure.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-customer-gw.png" alt-text="Vytvořit zákaznickou bránu":::

1. Vytvořte virtuální privátní bránu.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpg.png" alt-text="Vytvořit virtuální privátní bránu":::

1. Připojte virtuální privátní bránu ke službě VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-vpg.png" alt-text="Připojit VPG k VPC":::

1. Vyberte VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attaching-vpg.png" alt-text="Připojit":::

1. Vytvořte připojení VPN typu Site-to-site.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpn-connection.png" alt-text="Vytvořit připojení k síti VPN":::

1. Nastavte možnost směrování na **statickou** a najeďte na předponu podsítě Azure-01 **(172.10.1.0/24).**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/set-static-route.png" alt-text="Nastavení statické trasy":::

1. Po vyplnění možností **vytvořte** připojení.

1. Stáhněte si konfigurační soubor. Pokud chcete stáhnout správnou konfiguraci, změňte dodavatele, platformu a software na **Obecné**, protože Azure není platná možnost.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/download-config.png" alt-text="Stáhnout konfiguraci":::

1. Konfigurační soubor obsahuje předsdílený klíč a veřejnou IP adresu pro každé ze dvou tunelů IPsec vytvořených pomocí AWS.

   **Tunel 1**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1.png" alt-text="Tunel 1":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1-config.png" alt-text="Konfigurace tunelového propojení 1":::

   **Tunel 2**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2.png" alt-text="Tunel 2":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2-config.png" alt-text="Konfigurace tunelu 2":::

1. Po vytvoření tunelových propojení se v tomto příkladu zobrazí něco podobného.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-details.png" alt-text="Podrobnosti o připojení VPN AWS":::

## <a name="create-local-network-gateway"></a>Vytvořit bránu místní sítě

V Azure je brána místní sítě prostředek Azure, který obvykle představuje místní umístění. Vyplní se informace, které slouží k připojení k místnímu zařízení VPN. V této konfiguraci se ale vytvoří brána místní sítě a naplní se informace o připojení virtuální privátní brány AWS. Další informace o branách místní sítě Azure najdete v tématu [Nastavení azure VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#lng).

Vytvořte bránu místní sítě v Azure. Postup najdete v tématu [Vytvoření brány místní sítě](tutorial-site-to-site-portal.md#LocalNetworkGateway).

Zadejte následující hodnoty:

* **Název:** V tomto příkladu používáme LNG-Azure-AWS.
* **Koncový bod:** IP adresa
* **IP adresa:** Veřejná IP adresa z virtuální privátní brány AWS a předpona CIDR VPC. Veřejnou IP adresu najdete v konfiguračním souboru, který jste předtím stáhli.

AWS vytvoří dva tunely IPsec pro účely vysoké dostupnosti. Následující příklad ukazuje veřejnou IP adresu z #1 tunelu IPsec.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/local-network-gateway.png" alt-text="Brána místní sítě":::

## <a name="create-vpn-connection"></a>Vytvořit připojení k síti VPN

V této části vytvoříte připojení VPN mezi bránou virtuální sítě Azure a bránou AWS.

1. Vytvořte připojení Azure. Postup vytvoření připojení najdete v tématu [vytvoření připojení k síti VPN](tutorial-site-to-site-portal.md#CreateConnection).

   V následujícím příkladu se sdílený klíč získal z konfiguračního souboru, který jste si stáhli dříve. V tomto příkladu používáme hodnoty pro tunelové propojení IPsec #1 vytvořené nástrojem AWS a popsané v konfiguračním souboru.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection.png" alt-text="Objekt připojení Azure":::

1. Zobrazte připojení. Po několika minutách se připojení naváže.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/connection-established.png" alt-text="Pracovní připojení":::

1. Ověřte, že #1 tunelu protokolu IPsec v **AWS.**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-established.png" alt-text="Ověření, že je tunel AWS zapnutý":::

1. Upravte směrovací tabulku přidruženou k VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/edit-aws-route.png" alt-text="Upravit trasu":::

1. Přidejte trasu do podsítě Azure. Tato trasa bude cestovat prostřednictvím brány VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/save-aws-route.png" alt-text="Uložit konfiguraci trasy":::

## <a name="configure-second-connection"></a>Konfigurovat druhé připojení

V této části vytvoříte druhé připojení, abyste zajistili vysokou dostupnost.

1. Vytvořte jinou bránu místní sítě, která odkazuje na veřejnou IP adresu tunelového propojení IPsec #2 v AWS. Jedná se o pohotovostní bránu.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-lng-standby.png" alt-text="Vytvoření brány místní sítě":::

1. Vytvořte druhé připojení VPN z Azure do AWS.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection-standby.png" alt-text="Vytvoření připojení brány místní sítě v pohotovostním režimu":::

1. Zobrazit připojení služby Azure VPN Gateway.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-tunnels.png" alt-text="Stav připojení Azure":::

1. Zobrazení připojení AWS

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-tunnels.png" alt-text="Stav připojení AWS":::

Připojení jsou nyní navázána.

## <a name="test-connections"></a>Test připojení

1. Přidejte internetovou bránu do VPC na AWS. Internetová brána je logické připojení mezi serverem Amazon VPN a internetem. Tento prostředek vám umožňuje přes Internet připojit testovací virtuální počítač ze veřejné IP adresy AWS. Tento prostředek není pro připojení VPN vyžadován. Používáme ho pouze k testování.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-igw.png" alt-text="Vytvoření internetové brány":::

1. Vyberte **připojit k VPC**.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw.png" alt-text="Připojení internetové brány k VPC":::

1. Vyberte VPC a **Připojte internetovou bránu**.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw-2.png" alt-text="Připojte bránu.":::

1. Vytvořte trasu, která umožňuje připojení k síti **0.0.0.0/0** (Internet) přes internetovou bránu.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/allow-internet-igw.png" alt-text="Konfigurace trasy přes bránu":::

1. V Azure se trasa automaticky vytvoří. Trasu z virtuálního počítače Azure můžete ověřit tak, že vyberete **virtuální počítač > síť > síťové rozhraní > efektivní trasy**. Zobrazí se 2 trasy, 1 trasa za jedno připojení.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-effective-routes.png" alt-text="Kontrolovat efektivní trasy":::

1. Z virtuálního počítače se systémem Linux v Azure je prostředí podobné jako v následujícím příkladu.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-overview.png" alt-text="Přehled Azure z virtuálního počítače se systémem Linux":::

1. Z virtuálního počítače se systémem Linux v AWS je prostředí podobné jako v následujícím příkladu.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-overview.png" alt-text="Přehled AWS z virtuálního počítače se systémem Linux":::

1. Otestujte připojení z virtuálního počítače Azure.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-ping.png" alt-text="Test z Azure pomocí testu":::

1. Otestujte připojení z virtuálního počítače AWS.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-ping.png" alt-text="Test pomocí testu z AWS":::

## <a name="next-steps"></a>Další kroky

Další informace o podpoře AWS pro IKEv2 najdete v [článku AWS](https://aws.amazon.com/about-aws/whats-new/2019/02/aws-site-to-site-vpn-now-supports-ikev2/).
