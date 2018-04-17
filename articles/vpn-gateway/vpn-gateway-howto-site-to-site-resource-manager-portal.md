---
title: 'Připojení místní sítě k virtuální síti Azure: Síť VPN typu Site-to-Site: Portál | Dokumentace Microsoftu'
description: Postup vytvoření připojení IPsec z vaší místní sítě k virtuální síti Azure přes veřejný internet. Tyto kroky vám pomůžou vytvořit připojení VPN Gateway typu Site-to-Site mezi různými místy pomocí portálu.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 827a4db7-7fa5-4eaf-b7e1-e1518c51c815
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2018
ms.author: cherylmc
ms.openlocfilehash: 42aa80013edd7dcd0f78744e03e0f5da64bfeafc
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Vytvoření připojení typu Site-to-Site na webu Azure Portal

Tento článek ukazuje, jak pomocí webu Azure Portal vytvořit připojení brány VPN typu Site-to-Site z místní sítě k virtuální síti. Postupy v tomto článku se týkají modelu nasazení Resource Manager. Tuto konfiguraci můžete vytvořit také pomocí jiného nástroje nasazení nebo pro jiný model nasazení, a to výběrem jiné možnosti z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Rozhraní příkazového řádku](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal (Classic)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

Připojení brány VPN typu Site-to-Site slouží k připojení místní sítě k virtuální síti Azure přes tunel VPN IPsec/IKE (IKEv1 nebo IKEv2). Tento typ připojení vyžaduje místní zařízení VPN, které má přiřazenou veřejnou IP adresu. Další informace o bránách VPN najdete v tématu [Informace o službě VPN Gateway](vpn-gateway-about-vpngateways.md).

![Diagram připojení VPN Gateway typu Site-to-Site mezi různými místy](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace ověřte, že splňujete následující kritéria:

* Ujistěte se, že máte kompatibilní zařízení VPN a někoho, kdo jej umí nakonfigurovat. Další informace o kompatibilních zařízeních VPN a konfiguraci zařízení najdete v tématu [Informace o zařízeních VPN](vpn-gateway-about-vpn-devices.md).
* Ověřte, že máte veřejnou IPv4 adresu pro vaše zařízení VPN. Tato IP adresa nesmí být umístěná za překladem adres (NAT).
* Pokud neznáte rozsahy IP adres v konfiguraci vaší místní sítě, budete se muset spojit s někým, kdo vám s tím pomůže. Při vytváření této konfigurace musíte zadat předpony rozsahu IP adres, které bude Azure směrovat do vašeho místního umístění. Žádná z podsítí vaší místní sítě se nesmí překrývat s podsítěmi virtuální sítě, ke kterým se chcete připojit. 

### <a name="values"></a>Příklady hodnot

V příkladech v tomto článku se používají následující hodnoty. Tyto hodnoty můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů v tomto článku. Další celkové informace o nastavení VPN Gateway najdete v tématu [Informace o nastavení služby VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md).

* **Název virtuální sítě:** TestVNet1
* **Adresní prostor:** 10.1.0.0/16
* **Předplatné:** Vyberte předplatné, které chcete použít.
* **Skupina prostředků:** TestRG1
* **Umístění:** Východní USA
* **Podsíť:** Front-end: 10.1.0.0/24, Back-end: 10.1.1.0/24 (volitelné pro toto cvičení)
* **Název podsítě brány:** GatewaySubnet (na portálu se sám vyplní)
* **Rozsah adres podsítě brány:** 10.1.255.0/27
* **Server DNS:** 8.8.8.8 – Volitelné IP adresa vašeho serveru DNS
* **Název brány virtuální sítě:** VNet1GW
* **Veřejná IP adresa:** VNet1GWIP
* **Typ sítě VPN:** Trasová
* **Typ připojení:** Site-to-Site (protokol IPsec)
* **Typ brány:** Síť VPN
* **Název brány místní sítě:** Site1
* **Název připojení:** VNet1toSite1
* **Sdílený klíč:** V tomto příkladu použijeme abc123. Můžete ale použít cokoli, co je kompatibilní s hardwarem sítě VPN. Důležité je, že si tyto hodnoty odpovídají na obou stranách připojení.

## <a name="CreatVNet"></a>1. Vytvoření virtuální sítě

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-create-virtual-network-portal-include.md)]

## <a name="dns"></a>2. Určení serveru DNS

DNS se k vytvoření připojení Site-to-Site nevyžaduje. Pokud ale chcete umožnit překlad IP adres pro prostředky nasazované do vaší virtuální sítě, měli byste určit server DNS. Toto nastavení umožňuje určit server DNS, který chcete použít pro překlad IP adres pro tuto virtuální síť. Neslouží k vytvoření serveru DNS. Další informace o překladu IP adres najdete v tématu [Překlad názvů pro instance rolí a VM](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

[!INCLUDE [Specify a dns server - optional](../../includes/vpn-gateway-specify-dns-portal-include.md)]

## <a name="gatewaysubnet"></a>3. Vytvoření podsítě brány

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

[!INCLUDE [Add a gateway subnet](../../includes/vpn-gateway-add-gateway-subnet-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="VNetGateway"></a>4. Vytvoření brány VPN

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gateway-portal-include.md)]

## <a name="LocalNetworkGateway"></a>5. Vytvoření brány místní sítě

Brána místní sítě obvykle odkazuje na vaše místní umístění. Pro toto umístění určíte název, podle kterého na něj bude Azure odkazovat, a pak zadáte IP adresu místního zařízení VPN, ke kterému vytvoříte připojení. Zadáte také předpony IP adres, které se budou přes bránu VPN směrovat do zařízení VPN. Předpony adres, které zadáte, jsou předpony ve vaší místní síti. Pokud se změní vaše místní síť nebo potřebujete změnit veřejnou IP adresu pro zařízení VPN, můžete hodnoty snadno aktualizovat později.

[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="VPNDevice"></a>6. Konfigurace zařízení VPN

Připojení Site-to-Site k místní síti vyžadují zařízení VPN. V tomto kroku nakonfigurujete zařízení VPN. Při konfiguraci zařízení VPN potřebujete následující:

- Sdílený klíč. Jedná se o stejný sdílený klíč, který zadáváte při vytváření připojení VPN Site-to-Site. V našich ukázkách používáme základní sdílený klíč. Doporučujeme, abyste pro použití vygenerovali složitější klíč.
- Veřejnou IP adresu vaší brány virtuální sítě. Veřejnou IP adresu můžete zobrazit pomocí webu Azure Portal, PowerShellu nebo rozhraní příkazového řádku. Pokud chcete zjistit veřejnou IP adresu brány VPN pomocí webu Azure Portal, přejděte na **Brány virtuální sítě** a klikněte na název brány.

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="CreateConnection"></a>7. Vytvoření připojení VPN

Vytvořte připojení VPN typu Site-to-Site mezi bránou virtuální sítě a místním zařízením VPN.

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="VerifyConnection"></a>8. Ověření připojení VPN

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="connectVM"></a>Připojení k virtuálnímu počítači

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]

## <a name="reset"></a>Resetování brány VPN

Resetování brány Azure VPN je užitečné v případě ztráty připojení VPN mezi lokalitami na jednom nebo více tunelech VPN typu Site-to-Site. V této situaci vaše místní zařízení VPN fungují správně, ale nejsou schopná vytvořit tunelová propojení prostřednictvím protokolu IPsec s branami Azure VPN. Pokyny najdete v tématu [Resetování brány VPN](vpn-gateway-resetgw-classic.md).

## <a name="resize"></a>Změna skladové položky brány (změna velikosti brány)

Postup pro změnu skladové položky brány najdete v tématu popisujícím [Skladové položky brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

## <a name="addconnect"></a>Postup přidání dalšího připojení k bráně VPN

Další připojení můžete přidat za předpokladu, že se žádné z adresních prostorů mezi připojeními nepřekrývají.

1. Pokud chcete přidat další připojení, přejděte k bráně VPN a potom kliknutím na **Připojení** otevřete stránku Připojení.
2. Kliknutím na **+Přidat** přidáte vaše připojení. Upravit typ připojení tak, aby odpovídal typu VNet-to-VNet (pokud se připojujete k jiné bráně VNet), nebo Site-to-Site.
3. Pokud se připojujete pomocí typu Site-to-Site a ještě jste nevytvořili bránu místní sítě pro lokalitu, ke které se chcete připojit, můžete vytvořit novou.
4. Zadejte sdílený klíč, který chcete použít, a potom kliknutím na **OK** vytvořte připojení.

## <a name="next-steps"></a>Další kroky

* Informace o protokolu BGP najdete v tématech [Přehled protokolu BGP](vpn-gateway-bgp-overview.md) a [Postup při konfiguraci protokolu BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Informace o vynuceném tunelování najdete v tématu [Informace o vynuceném tunelování](vpn-gateway-forced-tunneling-rm.md).
* Informace o vysoce dostupných připojeních typu aktivní-aktivní najdete v tématu [Připojení s vysokou dostupností mezi jednotlivými místy a VNet-to-VNet](vpn-gateway-highlyavailable.md).
* Informace o postupu při omezení síťového provozu směřujícího do prostředků ve virtuální síti najdete v tématu [Zabezpečení sítě](../virtual-network/security-overview.md).
* Informace o tom, jak Azure směruje provoz mezi Azure, místním prostředím a internetovými prostředky, najdete v tématu [Směrování provozu virtuální sítě](../virtual-network/virtual-networks-udr-overview.md).
* Informace o vytvoření připojení VPN typu Site-to-Site pomocí šablony Azure Resource Manageru najdete v tématu [Vytvoření připojení VPN typu Site-to-Site](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/).
* Informace o vytvoření připojení VPN typu VNet-to-VNet pomocí šablony Azure Resource Manageru najdete v tématu [Nasazení geografické replikace HBase](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).