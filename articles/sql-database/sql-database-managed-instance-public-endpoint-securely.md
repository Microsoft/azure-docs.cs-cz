---
title: Zabezpečení Azure SQL Database spravovanou instanci veřejné koncové body – spravovaná instance | Dokumentace Microsoftu
description: Bezpečně použijte veřejných koncových bodů v Azure pomocí spravované instance
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 05/08/2019
ms.openlocfilehash: f06677b66c8f6586fec8cc5dfe97b1515b741e9c
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65470284"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>Bezpečněji používat spravovanou instanci Azure SQL Database s veřejnými koncovými body

Azure SQL Database, spravované instance můžete zadat možnosti připojení uživatelů nad [veřejné koncové body](../virtual-network/virtual-network-service-endpoints-overview.md). Tento článek vysvětluje, jak zabezpečit tuto konfiguraci.

## <a name="scenarios"></a>Scénáře

Spravovaná instance SQL Database poskytuje privátního koncového bodu umožňující připojení k v rámci příslušné virtuální síti. Výchozí možnost je k zajištění maximální izolace. Existují ale scénáře, ve kterém budete muset zadat veřejný koncový bod připojení:

- Spravovaná instance musí integrovat s více-tenant jen nabídky platforma jako služba (PaaS).
- Budete potřebovat větší propustnost dat serveru Exchange, než je možné, pokud používáte síť VPN.
- Firemní zásady zakazují PaaS uvnitř podnikové sítě.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Nasaďte managed instance pro přístup k veřejnému koncovému bodu

I když není povinné, vytvořte instanci ve vyhrazené izolované virtuální sítě je společný model nasazení pro spravovanou instanci s přístupem k veřejnému koncovému bodu. V této konfiguraci virtuální sítě slouží pouze k izolaci virtuálních clusterů. Nebude vadit, když spravovanou instanci adresní prostor IP adres se překrývá s podnikovou síť adresní prostor IP adres.

## <a name="secure-data-in-motion"></a>Zabezpečení dat v pohybu

Přenos dat spravované instance se vždy šifrují, pokud ovladač klienta podporuje šifrování. Data odesílaná mezi spravovanou instanci a jiné virtuální počítače Azure nebo služeb Azure nikdy neopustí páteřní síti Azure. Pokud je připojení mezi spravovanou instanci a v místní síti, doporučujeme že použít Azure ExpressRoute se partnerský vztah Microsoftu. ExpressRoute pomáhá vám vyhnuli přenášení dat přes veřejný internet. Pro spravovanou instanci privátní připojení pouze soukromého partnerského vztahu můžete použít.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Zamknout příchozí a odchozí připojení

Následující diagram znázorňuje doporučené možnosti konfigurace:

![Konfigurace zabezpečení pro uzamčení příchozí a odchozí připojení](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

Managed instance má [vyhrazená adresa veřejného koncového bodu](sql-database-managed-instance-find-management-endpoint-ip-address.md). V odchozí brány firewall na straně klienta a pravidla skupiny zabezpečení sítě nastavte tato IP adresa veřejného koncového bodu omezit odchozí připojení.

Zajistěte, aby provoz do spravované instance pochází z důvěryhodného zdroje, doporučujeme připojení ze zdroje s dobře známé IP adresy. Pomocí skupiny zabezpečení sítě můžete omezit přístup k veřejnému koncovému bodu spravovanou instanci na portu 3342.

Když klienti potřebovat k zahájení připojení z místní sítě, ujistěte se, že původní adresa je přeložen na sadu známých IP adres. Pokud to není možné tak (například mobilní pracovní síly je typický scénář), doporučujeme, abyste použili [připojení VPN typu point-to-site a privátního koncového bodu](sql-database-managed-instance-configure-p2s.md).

Pokud připojení jsou spuštěny z Azure, doporučujeme vám, že provoz pochází známé přiřazené [virtuální IP adresu](../virtual-network/virtual-networks-reserved-public-ip.md) (například virtuální počítač). Aby Správa virtuálních IP (VIP) adres jednodušší, můžete chtít použít [předpony veřejných IP adres](../virtual-network/public-ip-address-prefix.md).

## <a name="next-steps"></a>Další postup

- Zjistěte, jak nakonfigurovat veřejný koncový bod pro instance spravovat: [Konfigurace veřejného koncového bodu](sql-database-managed-instance-public-endpoint-configure.md)
