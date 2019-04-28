---
title: Zabezpečení Azure SQL Database spravovanou instanci veřejné koncové body – spravovaná instance | Dokumentace Microsoftu
description: Bezpečně použijte veřejných koncových bodů v Azure pomocí spravované instance
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: vanto, carlrab
manager: digimobile
origin.date: 04/16/2019
ms.date: 04/29/2019
ms.openlocfilehash: 9d5a3d18e8a7d3c5a6cb08e16e74dd4fbda9ca31
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61315043"
---
# <a name="using-azure-sql-database-managed-instance-securely-with-public-endpoint"></a>Využívající Azure SQL Database managed instance bezpečně s veřejným koncovým bodem

Azure SQL Database, spravované instance můžete povolit a tím zajistit připojení uživatele přes [veřejný koncový bod](../virtual-network/virtual-network-service-endpoints-overview.md). Tento článek obsahuje pokyny, jak se tato konfigurace bezpečnější.

## <a name="scenarios"></a>Scénáře

Mi poskytuje privátního koncového bodu umožňující připojení v rámci příslušné virtuální síti. Výchozí možnost je k zajištění maximální izolace. Existují ale scénáře, kde je potřeba veřejný koncový bod připojení:

- Integrace s více tenanty pouze nabídek PaaS.
- Vyšší propustnost výměny dat než při použití sítě VPN.
- Firemní zásady zakazují PaaS uvnitř podnikové sítě.

## <a name="deploying-managed-instance-for-public-endpoint-access"></a>Nasazení spravované instance pro veřejný koncový bod přístupu

I když není povinné, vytvořte instanci ve vyhrazené izolované virtuální sítě je společný model nasazení pro spravovanou instanci s přístupem k veřejnému koncovému bodu. V této konfiguraci virtuální sítě slouží jenom k izolaci virtuálních clusterů. Není relevantní, pokud se adresní prostor IP adres spravovaných instancí překrývá s prostoru IP adres podnikové sítě.

## <a name="securing-data-in-motion"></a>Zabezpečení dat v pohybu

Přenos dat spravované instance se vždy šifrují, pokud ovladač klienta podporuje šifrování. Data mezi spravovanou instanci a jiné virtuální počítače Azure a služeb Azure nikdy neopustí páteřní síti Azure. Pokud je managed instance pro připojení k místní síti, doporučujeme použít Expressroute s využitím partnerského vztahu Microsoftu. Expressroute vám pomůže vyhnuli přenášení dat přes veřejný Internet (pro spravovanou instanci privátní připojení, pouze soukromého partnerského vztahu můžete použít).

## <a name="locking-down-inbound-and-outbound-connectivity"></a>Uzamčením příchozího a odchozího připojení

Následující diagram znázorňuje doporučené možnosti konfigurace.

![managed-instance-vnet.png](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

Managed Instance má [vyhrazená adresa veřejného koncového bodu](sql-database-managed-instance-find-management-endpoint-ip-address.md). Tato IP adresa musí být nastaveno v odchozí brány firewall na straně klienta a pravidla skupiny zabezpečení sítě pro omezení odchozího připojení.

K zajištění, že se provoz do spravované instance pochází z důvěryhodného zdroje, doporučuje se připojit z zdroje s dobře známé IP adresy. Omezit přístup k veřejnému koncovému bodu spravovanou instanci na portu 3342 pomocí skupiny zabezpečení sítě.

Když klienti potřebovat k navázání připojení z místní sítě, zkontrolujte, zda že zdrojovou adresu se přeloží na dobře známé sady IP adres. Pokud, který není možné dosáhnout (třeba mobilní pracovní síly je typický scénář), doporučuje se použít [Point-to-site VPN připojení a privátního koncového bodu](sql-database-managed-instance-configure-p2s.md).

Pokud připojení jsou spuštěny z Azure, doporučuje se, že provoz pochází z dobře známé přiřazené [virtuálních IP adres](../virtual-network/virtual-networks-reserved-public-ip.md) (například virtuální počítače). Pro usnadnění správy virtuální IP adresy, zákazníci zvážit použití [předponu veřejné IP adresy](../virtual-network/public-ip-address-prefix.md).