---
title: Z klasického zprostředkovatele brány sítě VPN na při migraci správce prostředků | Microsoft Docs
description: Tato stránka obsahuje přehled portálu VPN Gateway Classic migrace správce prostředků.
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
ms.openlocfilehash: 1164fc24355657af22b6befaad74685ebbc2b5cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23885214"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>Brána sítě VPN classic migrace správce prostředků
Brány sítě VPN lze nyní přenést z klasického modelu nasazení Resource Manager. Další informace o službě Správce prostředků Azure [funkce a výhody](../azure-resource-manager/resource-group-overview.md). V tomto článku jsme podrobnosti migrace z klasického nasazení do novější správce prostředků na základě modelu. 

Brány sítě VPN se migrují jako součást migrace virtuální sítě z classic do Resource Manager. Tato migrace provádí jednu virtuální síť v čase. Neexistuje žádný další požadavek z hlediska nástrojů nebo požadavky na migraci. Kroky migrace jsou identické existující virtuální síť migrace a jsou popsané v [stránka migrace prostředky IaaS](../virtual-machines/windows/migration-classic-resource-manager-ps.md). Neexistuje žádné výpadky cesta dat během migrace a proto by existující úlohy nadále fungovat bez ztráty připojení místní během migrace. Veřejnou IP adresu, které jsou přidružené k bráně VPN nezmění během procesu migrace. To znamená, že nebudete muset znovu nakonfigurovat místní směrovač po dokončení migrace.  

V modelu ve službě Správce prostředků se liší od klasického modelu a se skládá z brány virtuální sítě, brány místní sítě a prostředky připojení. Tyto představují brány sítě VPN, local-site představující na místní adresní prostor a připojení mezi těmito dvěma v uvedeném pořadí. Po dokončení migrace vašich bran nebude k dispozici v klasického modelu a všechny operace správy v brány virtuální sítě, brány místní sítě a objektů připojení musí být provedeno pomocí modelu Resource Manager.

## <a name="supported-scenarios"></a>Podporované scénáře
Obvyklé scénáře, připojení VPN jsou předmětem classic do Resource Manager migrace. Mezi podporované scénáře patří-

* Přejděte na připojení k webu
* Webový server lokality připojení k bráně VPN připojen k na místní umístění
* Virtuální síť VNet připojení mezi oběma virtuálními sítěmi pomocí brány sítě VPN
* Více virtuálních sítí připojené k stejné na místní umístění
* Připojení více lokalit
* Povolit vynucené tunelování virtuální sítě

Scénáře, které nejsou podporovány zahrnují-  

* Virtuální síť, bránu ExpressRoute i bránu VPN není aktuálně podporován.
* Přenosu scénáře, ve kterých jsou rozšíření virtuálního počítače připojené k místní servery. Omezení připojení VPN přenosu jsou podrobně popsány níže.

> [!NOTE]
> Ověření CIDR v modelu Resource Manager je striktní více než jeden v klasického modelu. Před migrací zabezpečovat, že zadané rozsahy classic adres odpovídají platný formát CIDR před zahájením migrace. CIDR může být ověřen pomocí všechny běžné validátory CIDR. Virtuální síť nebo místní lokality s neplatnou rozsahy CIDR při migraci by způsobilo stavu selhání.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>Virtuální síť VNet připojení migrace
Virtuální síť připojení virtuální síť v klasickém bylo dosaženo vytvořením reprezentace místní lokality připojené virtuální sítě. Zákazníci bylo potřeba vytvořit dva místní weby, které reprezentované dvě virtuální sítě, které se musel být propojeny. Potom tyto připojilo k odpovídající virtuální sítě pomocí tunelu IPsec k navázání připojení mezi oběma virtuálními sítěmi. Tento model má problémy spravovatelnosti, protože změny rozsahu adres ve virtuální síti jeden musí lze udržovat také v odpovídající reprezentace místního webu. V modelu Resource Manager se už nepotřebuje toto řešení. Připojení mezi oběma virtuálními sítěmi lze přímo dosáhnout pomocí typ připojení: Vnet2Vnet"v prostředku připojení. 

![Snímek obrazovky z virtuální sítě VNet migrace.](./media/vpn-gateway-migration/migration1.png)

Během migrace virtuální sítě zjistíme, že připojené entity, která má aktuální virtuální síť VPN gateway je jiné virtuální síti a ujistěte se, že po dokončení migrace obě virtuální sítě, by se již nezobrazují dva místní weby představující další virtuální sítě. Klasického modelu dvě brány sítě VPN, dva místní weby a dvě připojení mezi nimi transformována do modelu Resource Manager s dvě brány sítě VPN a dvě připojení typu Vnet2Vnet.

## <a name="transit-vpn-connectivity"></a>Připojení k síti VPN přenosu
Brány sítě VPN můžete konfigurovat v topologii tak, aby místní připojení pro virtuální síť se dosahuje připojení k jiné virtuální síti, která je přímo připojena k místní. Toto je přenosu připojení VPN, kde instance v první sítě VNet připojeni k místním prostředkům prostřednictvím přenosu ke službě VPN gateway v připojené virtuální sítě, která je přímo připojena k místní. K dosažení tohoto konfigurace v modelu nasazení classic, museli byste vytvořit místní lokality, který má agregovat předpony představující obou připojené virtuální sítě a místní adresní prostor. Potom tento representational místní síť připojená k virtuální sítě k dosažení připojení přenosu. Vzhledem k tomu, že všechny změny v místní rozsah adres musí být zachovaná také na webu místní představující agregace virtuální sítě a místní, tento klasického modelu má také podobné možnosti správy problémů. Zavedení podpory protokolu BGP v Resource Manager podporován brány zjednodušuje možnosti správy, protože připojené brány další směrování z místně bez nutnosti ruční úpravy předpon.

![Snímek obrazovky scénář směrování přenosu.](./media/vpn-gateway-migration/migration2.png)

Vzhledem k tomu, že jsme transformace virtuální síť připojení virtuální sítě bez nutnosti místní weby, ztratí scénář přenosu pro virtuální síť, která je nepřímo připojena k místní místní připojení. Ztráty připojení, můžete po dokončení migrace – v následujících dvou způsobů, omezeny. 

* Povolte protokol BGP na VPN brány, které jsou propojeny společně a místně. Povolení protokolu BGP obnoví připojení bez další změny konfigurace, protože trasy se naučili a ohlášené mezi brány virtuální sítě. Všimněte si, že protokol BGP možnost je dostupná jenom na standardní a vyšší SKU.
* Vytvořit explicitní připojení z ovlivněné virtuální sítě k bráně místní sítě reprezentující místní umístění. To by také vyžadovat změnu konfigurace na místní směrovač vytvořit a nakonfigurovat tunelu IPsec.

## <a name="next-steps"></a>Další kroky
Po získání informací o podpora migrace brány sítě VPN, přejděte na [platformy podporované migrace z classic do Resource Manager prostředky infrastruktury](../virtual-machines/windows/migration-classic-resource-manager-ps.md) začít pracovat.

