---
title: Zabezpečení veřejných koncových bodů spravované instance Azure SQL
description: Zabezpečené použití veřejných koncových bodů ve spravované instanci Azure SQL
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, sstein
ms.date: 05/08/2019
ms.openlocfilehash: d9c1828732b9a4e0e85c3af2263f097edd54437d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332844"
---
# <a name="use-azure-sql-managed-instance-securely-with-public-endpoints"></a>Zabezpečené použití spravované instance Azure SQL s veřejnými koncovými body
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Spravovaná instance Azure SQL může poskytovat připojení uživatele přes [veřejné koncové body](../../virtual-network/virtual-network-service-endpoints-overview.md). Tento článek vysvětluje, jak zvýšit zabezpečení této konfigurace.

## <a name="scenarios"></a>Scénáře

Spravovaná instance Azure SQL poskytuje privátní koncový bod, který umožňuje připojení zevnitř své virtuální sítě. Výchozí možnost je poskytnout maximální izolaci. Existují však situace, kdy potřebujete zadat připojení ke veřejnému koncovému bodu:

- Spravovaná instance se musí integrovat s nabídkami platforma jako služba (PaaS) s více klienty.
- Potřebujete vyšší propustnost výměny dat, než je možné při používání sítě VPN.
- Zásady společnosti zakazují PaaS v podnikových sítích.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Nasazení spravované instance pro přístup k veřejnému koncovému bodu

I když není povinná, společný model nasazení pro spravovanou instanci s přístupem k veřejnému koncovému bodu je vytvoření instance ve vyhrazené izolované virtuální síti. V této konfiguraci se virtuální síť používá jenom pro izolaci virtuálních clusterů. Nezáleží na tom, jestli se adresní prostor IP adres spravované instance překrývá s adresním prostorem IP adres v podnikové síti.

## <a name="secure-data-in-motion"></a>Zabezpečená data v pohybu

Provoz dat spravované instance SQL je vždycky zašifrovaný, pokud ovladač klienta podporuje šifrování. Data odesílaná mezi spravovanou instancí a dalšími virtuálními počítači Azure nebo službami Azure nikdy neopustí páteřní síť Azure. Pokud existuje připojení mezi spravovanou instancí a místní sítí, doporučujeme použít Azure ExpressRoute. ExpressRoute pomáhá vyhnout se přesouvání dat prostřednictvím veřejného Internetu. U privátního připojení spravované instance se dá použít jenom privátní partnerské vztahy.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Uzamknutí příchozího a odchozího připojení

Následující diagram znázorňuje Doporučené konfigurace zabezpečení:

![Konfigurace zabezpečení pro uzamykání příchozího a odchozího připojení](./media/public-endpoint-overview/managed-instance-vnet.png)

Spravovaná instance má [vyhrazenou adresu veřejného koncového bodu](management-endpoint-find-ip-address.md). V případě odchozí brány firewall na straně klienta a v pravidlech skupiny zabezpečení sítě nastavte tuto IP adresu veřejného koncového bodu tak, aby se omezilo odchozí připojení.

Aby se zajistilo, že provoz do spravované instance pochází z důvěryhodných zdrojů, doporučujeme připojit se ze zdrojů s dobře známými IP adresami. Pomocí skupiny zabezpečení sítě omezte přístup ke veřejnému koncovému bodu spravované instance na portu 3342.

Když klienti potřebují iniciovat připojení z místní sítě, ujistěte se, že je původní adresa přeložená na dobře známou sadu IP adres. Pokud to nemůžete udělat (například mobilní pracovní síly se jedná o typický scénář), doporučujeme použít [připojení VPN typu Point-to-site a soukromý koncový bod](point-to-site-p2s-configure.md).

Pokud se připojení spouští z Azure, doporučujeme, aby provoz pocházejí ze známé přiřazené [virtuální IP adresy](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) (například virtuální počítač). Chcete-li zjednodušit správu virtuálních IP adres (VIP), je vhodné použít [předpony veřejných IP adres](../../virtual-network/public-ip-address-prefix.md).

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak nakonfigurovat veřejný koncový bod pro správu instancí: [Konfigurace veřejného koncového bodu](public-endpoint-configure.md)
