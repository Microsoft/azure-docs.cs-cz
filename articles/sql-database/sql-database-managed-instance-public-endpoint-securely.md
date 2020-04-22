---
title: Zabezpečené veřejné koncové body spravované instance
description: Bezpečné používání veřejných koncových bodů v Azure se spravovanou instancí
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/08/2019
ms.openlocfilehash: f11c19ba33ee2fbae0fef265371bedad2fe29cb7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684956"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>Bezpečné použití spravované instance Azure SQL Database s veřejnými koncovými body

Instance spravované databází Azure SQL Database můžou poskytovat připojení uživatelů přes [veřejné koncové body](../virtual-network/virtual-network-service-endpoints-overview.md). Tento článek vysvětluje, jak tuto konfiguraci zabezpečit.

## <a name="scenarios"></a>Scénáře

Instance spravované sql database poskytuje soukromý koncový bod pro povolení připojení z uvnitř své virtuální sítě. Výchozí možností je poskytnout maximální izolaci. Existují však scénáře, kde je třeba zadat připojení veřejného koncového bodu:

- Spravovaná instance musí být integrována s nabídkami platformy jako služby (PaaS) pouze pro více klientů.
- Potřebujete vyšší propustnost výměny dat, než je možné při používání sítě VPN.
- Zásady společnosti zakazují PaaS uvnitř podnikových sítí.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Nasazení spravované instance pro přístup k veřejnému koncovému bodu

Ačkoli není povinné, společný model nasazení pro spravovanou instanci s přístupem veřejného koncového bodu je vytvořit instanci ve vyhrazené izolované virtuální síti. V této konfiguraci se virtuální síť používá pouze pro izolaci virtuálního clusteru. Nezáleží na tom, zda se adresní prostor IP spravované instance překrývá s adresním prostorem IP podnikové sítě.

## <a name="secure-data-in-motion"></a>Bezpečná data v pohybu

Datový provoz spravované instance je vždy šifrován, pokud ovladač klienta podporuje šifrování. Data odeslaná mezi spravovanou instancí a jinými virtuálními počítači Azure nebo službami Azure nikdy neopustí páteř Azure. Pokud existuje připojení mezi spravovanou instancí a místní sítí, doporučujeme použít Azure ExpressRoute. ExpressRoute pomáhá vyhnout se přesouvání dat přes veřejný internet. Pro soukromé připojení spravované instance lze použít pouze privátní partnerský vztah.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Uzamknutí příchozího a odchozího připojení

Následující diagram znázorňuje doporučené konfigurace zabezpečení:

![Konfigurace zabezpečení pro uzamčení příchozího a odchozího připojení](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

Spravovaná instance má [vyhrazenou adresu veřejného koncového bodu](sql-database-managed-instance-find-management-endpoint-ip-address.md). V odchozí bráně firewall na straně klienta a v pravidlech skupiny zabezpečení sítě nastavte tuto veřejnou ip adresu koncového bodu tak, aby omezovala odchozí připojení.

Chcete-li zajistit, aby provoz spravované instance pocházel z důvěryhodných zdrojů, doporučujeme připojení ze zdrojů s dobře známými IP adresami. Pomocí skupiny zabezpečení sítě můžete omezit přístup k veřejnému koncovému bodu spravované instance na portu 3342.

Pokud klienti potřebují iniciovat připojení z místní sítě, ujistěte se, že původní adresa je přeložena na známou sadu adres IP. Pokud to nemůžete udělat (například mobilní pracovní síla je typický scénář), doporučujeme použít [připojení VPN z bodu na web a soukromý koncový bod](sql-database-managed-instance-configure-p2s.md).

Pokud se připojení schytovávají z Azure, doporučujeme, aby provoz pocházel ze známé přiřazené [virtuální IP adresy](../virtual-network/virtual-networks-reserved-public-ip.md) (například virtuálního počítače). Chcete-li usnadnit správu virtuálních IP adres (VIP), můžete použít [předpony veřejných IP adres](../virtual-network/public-ip-address-prefix.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak nakonfigurovat veřejný koncový bod pro správu instancí: [Konfigurace veřejného koncového bodu](sql-database-managed-instance-public-endpoint-configure.md)
