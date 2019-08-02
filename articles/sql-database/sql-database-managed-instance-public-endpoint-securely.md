---
title: Veřejné koncové body zabezpečené spravované instance – Azure SQL Database spravovanou instanci | Microsoft Docs
description: Bezpečné použití veřejných koncových bodů v Azure se spravovanou instancí
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/08/2019
ms.openlocfilehash: c7f57a636e95bb137dd4285b8f9ce8343b27d2a0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567375"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>Bezpečné použití spravované instance Azure SQL Database s veřejnými koncovými body

Azure SQL Database spravované instance můžou poskytovat připojení uživatele přes [veřejné koncové body](../virtual-network/virtual-network-service-endpoints-overview.md). Tento článek vysvětluje, jak zvýšit zabezpečení této konfigurace.

## <a name="scenarios"></a>Scénáře

SQL Database Managed instance poskytuje privátní koncový bod, který umožňuje připojení zevnitř své virtuální sítě. Výchozí možnost je poskytnout maximální izolaci. Existují však situace, kdy potřebujete zadat připojení ke veřejnému koncovému bodu:

- Spravovaná instance se musí integrovat s nabídkami platforma jako služba (PaaS) s více klienty.
- Potřebujete vyšší propustnost výměny dat, než je možné při používání sítě VPN.
- Zásady společnosti zakazují PaaS v podnikových sítích.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Nasazení spravované instance pro přístup k veřejnému koncovému bodu

I když není povinná, společný model nasazení pro spravovanou instanci s přístupem k veřejnému koncovému bodu je vytvoření instance ve vyhrazené izolované virtuální síti. V této konfiguraci se virtuální síť používá jenom pro izolaci virtuálních clusterů. Nezáleží na tom, jestli se adresní prostor IP adres spravované instance překrývá s adresním prostorem IP adres v podnikové síti.

## <a name="secure-data-in-motion"></a>Zabezpečená data v pohybu

Provoz dat spravované instance je vždycky zašifrovaný, pokud ovladač klienta podporuje šifrování. Data odesílaná mezi spravovanou instancí a dalšími virtuálními počítači Azure nebo službami Azure nikdy neopustí páteřní síť Azure. Pokud existuje připojení mezi spravovanou instancí a místní sítí, doporučujeme použít Azure ExpressRoute s partnerským vztahem Microsoftu. ExpressRoute pomáhá vyhnout se přesouvání dat prostřednictvím veřejného Internetu. U privátního připojení spravované instance se dá použít jenom privátní partnerské vztahy.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Uzamknutí příchozího a odchozího připojení

Následující diagram znázorňuje Doporučené konfigurace zabezpečení:

![Konfigurace zabezpečení pro uzamykání příchozího a odchozího připojení](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

Spravovaná instance má vyhrazenou [adresu veřejného koncového bodu](sql-database-managed-instance-find-management-endpoint-ip-address.md). V případě odchozí brány firewall na straně klienta a v pravidlech skupiny zabezpečení sítě nastavte tuto IP adresu veřejného koncového bodu tak, aby se omezilo odchozí připojení.

Aby se zajistilo, že provoz do spravované instance pochází z důvěryhodných zdrojů, doporučujeme připojit se ze zdrojů s dobře známými IP adresami. Pomocí skupiny zabezpečení sítě omezte přístup ke veřejnému koncovému bodu spravované instance na portu 3342.

Když klienti potřebují iniciovat připojení z místní sítě, ujistěte se, že je původní adresa přeložená na dobře známou sadu IP adres. Pokud to nemůžete udělat (například mobilní pracovní síly se jedná o typický scénář), doporučujeme použít [připojení VPN typu Point-to-site a soukromý koncový bod](sql-database-managed-instance-configure-p2s.md).

Pokud se připojení spouští z Azure, doporučujeme, aby provoz pocházejí ze známé přiřazené [virtuální IP adresy](../virtual-network/virtual-networks-reserved-public-ip.md) (například virtuální počítač). Chcete-li zjednodušit správu virtuálních IP adres (VIP), je vhodné použít [předpony veřejných IP adres](../virtual-network/public-ip-address-prefix.md).

## <a name="next-steps"></a>Další postup

- Zjistěte, jak nakonfigurovat veřejný koncový bod pro správu instancí: [Konfigurace veřejného koncového bodu](sql-database-managed-instance-public-endpoint-configure.md)
