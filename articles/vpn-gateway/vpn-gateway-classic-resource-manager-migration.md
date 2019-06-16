---
title: Brány VPN z modelu Classic do Resource Manageru migrace | Dokumentace Microsoftu
description: Tato stránka poskytuje přehled VPN Gateway Classic při migraci správce prostředků.
documentationcenter: na
services: vpn-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: caa8eb19-825a-4031-8b49-18fbf3ebc04e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: amsriva
ms.openlocfilehash: b65b47389611bcc0e5acb3c7ebff672f72a87581
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60761564"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>Brána VPN z modelu classic do Resource Manageru migrace
VPN Gateway je nyní migrovat z klasického modelu nasazení Resource Manageru. Další informace o Azure Resource Manageru [funkce a výhody](../azure-resource-manager/resource-group-overview.md). V tomto článku se budeme podrobně popisují, jak migrovat z klasického nasazení do novější modelu založené na Resource Manageru. 

VPN Gateway se migrují v rámci migrace virtuální sítě z modelu nasazení classic do Resource Manageru. Tato migrace probíhá jedné virtuální síti současně. Neexistuje žádné další požadavky z hlediska nástrojů nebo požadavky na migraci. Kroky migrace jsou shodné s existující migraci virtuální sítě a jsou zdokumentované v [stránky migrace prostředků IaaS](../virtual-machines/windows/migration-classic-resource-manager-ps.md). Neexistuje žádný výpadek cesty dat během migrace, a proto by stávající úlohy nadále fungovat bez ztráty připojení s místními během migrace. Veřejná IP adresa přidružená k bráně VPN nezmění během procesu migrace. Z toho vyplývá, že nebudete muset znovu nakonfigurovat místní směrovač po dokončení migrace.  

Model ve službě Správce prostředků se liší od modelu classic a se skládá z brány virtuální sítě, místní síťové brány a připojení prostředků. Představují brány sítě VPN, lokalitě místní představující na místní adresní prostor a připojení mezi těmito dvěma v uvedeném pořadí. Po dokončení migrace nebude k dispozici v modelu classic bran a všechny operace správy ve službě brány virtuální sítě, místní síťové brány a objektů připojení musí být provedeno pomocí modelu Resource Manager.

## <a name="supported-scenarios"></a>Podporované scénáře
Nejběžnější scénáře připojení VPN jsou zahrnuté do modelu classic do Resource Manageru migrace. Mezi podporované scénáře patří-

* Přejděte na připojení k serveru
* Server lokality připojení k bráně VPN připojení na umístění v místním prostředí
* Virtuální síť k připojení k virtuální síti mezi dvěma virtuálními sítěmi pomocí bran VPN Gateway
* Více virtuálních sítí připojené do stejné na umístění v místním prostředí
* Připojení více lokalit
* Vynucené tunelování povolené virtuální sítě

Scénáře, které nejsou podporovány zahrnují-  

* Virtuální síť, bránu ExpressRoute i VPN Gateway se momentálně nepodporuje.
* Tranzitní scénáře, ve kterém jsou rozšíření virtuálních počítačů připojený k na místních serverech. Omezení přenosu VPN připojení je podrobně popsaný níže.

> [!NOTE]
> Ověření CIDR v modelu Resource Manager je přísnější než jedna v klasickém modelu. Před migrací Ujistěte se, že zadané rozsahy classic adres odpovídají platný formát CIDR před zahájením migrace. CIDR může být ověřen pomocí žádné společné validátory CIDR. Virtuální síť nebo místní sítí pomocí neplatné rozsahy CIDR při migraci způsobovaly v chybovém stavu.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>Virtuální síť k připojení k migraci virtuální sítě
Virtuální síť k připojení virtuálních sítí v modelu classic se dosahuje vytvořením místní lokalita reprezentace připojené virtuální sítě. Zákazníci bylo potřeba vytvořit dvě místní lokality, které reprezentován dvěma virtuálními sítěmi, které společně připojený k Internetu potřeba. Pak byli připojeni k odpovídajícím virtuálním sítím pomocí tunelu IPsec k navázání připojení mezi dvěma virtuálními sítěmi. Tento model má problémy spravovatelnosti, protože změny rozsah adres v jedné virtuální síti musí lze udržovat také v odpovídající reprezentaci místní lokality. V modelu Resource Manager je už nejsou potřebná řešení. Připojení mezi dvěma virtuálními sítěmi jde přímo dosáhnout použitím typu připojení: Vnet2Vnet"v prostředku připojení. 

![Snímek obrazovky z virtuální sítě pro migraci virtuální sítě.](./media/vpn-gateway-migration/migration1.png)

Při migraci virtuální sítě zjistíme, že připojené entita, která má aktuální VNet VPN gateway je jiné virtuální síti a ujistěte se, že po dokončení migrace v obou virtuálních sítích už uvidíte dvě místní lokality představuje jinou virtuální sítí. Klasického modelu, dvě brány sítě VPN, dvě místní sítě a dvě spojení mezi nimi se transformuje na model Resource Manager pomocí dvě brány virtuální privátní sítě a dvě spojení typu Vnet2Vnet.

## <a name="transit-vpn-connectivity"></a>Připojení k síti VPN přenosu
VPN Gateway v topologii můžete nakonfigurovat tak, aby místní připojení pro virtuální síť můžete vytvořit připojení k jiné virtuální síti, která je přímo připojený k místnímu. Toto je přenosu připojení k síti VPN, ve kterých jsou instance v první virtuální sítě připojený k místním prostředkům prostřednictvím přenosu ke službě VPN gateway v připojené virtuální sítě, která je přímo připojený k místnímu. K dosažení této konfiguraci v modelu nasazení classic, je třeba vytvořit místní lokality, který má agregovat předpony představující obě připojené virtuální sítě a místní adresní prostor. Tato representational místní lokality je připojen k virtuální síti k dosažení připojení přenosu. Tento model classic má také podobné problémy spravovatelnosti vzhledem k tomu žádné změny v místní rozsah adres musí být udržovány také na místní lokalita představuje agregace virtuální sítí a místní. Zavedení podpory protokolu BGP v Resource Manageru nepodporuje brány zjednodušuje možnosti správy od připojení brány další trasy z místně bez nutnosti ruční úpravy předpon.

![Snímek obrazovky scénář směrování přenosu.](./media/vpn-gateway-migration/migration2.png)

Protože transformujeme virtuální sítě pro připojení k virtuální síti bez nutnosti místní weby, ztratí scénáři přenosu místního připojení pro virtuální síť, která je nepřímo připojené k místní. Můžete minimalizovat ztráty připojení v následujících dvou způsobů, po dokončení migrace- 

* O branách VPN Gateway, které jsou připojené dohromady a v místním povolte protokol BGP. Povoluje se protokol BGP obnovení připojení bez jakékoli další změny konfigurace, protože jsou zjistili a inzerované bran virtuální sítě trasy. Všimněte si, že možnost protokolu BGP je dostupný jenom u skladové položky Standard nebo vyšší.
* Vytvořit explicitní připojení z ovlivněné virtuální sítě k místní síťové brány reprezentující místní umístění. Bude také vyžadovat změnu konfigurace na místní směrovač vytvořit a nakonfigurovat tunel IPsec.

## <a name="next-steps"></a>Další postup
Po získání informací o podpoře migrace brány sítě VPN, přejděte na [platformou podporované migraci prostředků IaaS z modelu nasazení classic do Resource Manageru](../virtual-machines/windows/migration-classic-resource-manager-ps.md) začít.

