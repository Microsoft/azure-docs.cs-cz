---
title: Privátní brána VPN Classic do migrace Správce prostředků | Dokumenty společnosti Microsoft
description: Tato stránka poskytuje přehled migrace brány VPN Classic do správce prostředků.
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
ms.date: 02/06/2020
ms.author: amsriva
ms.openlocfilehash: c1a75630c6419816b048495ee87d24c81979af16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064763"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>Klasická brána VPN do migrace Správce prostředků
Brány VPN lze nyní migrovat z klasického modelu nasazení Resource Manageru. Další informace o [funkcích a výhodách](../azure-resource-manager/management/overview.md)Azure Resource Manageru najdete v dalších informacích . V tomto článku podrobně popisujeme, jak migrovat z klasických nasazení do novějšího modelu založeného na Správci prostředků. 

Brány VPN se migrují jako součást migrace virtuální sítě z klasického do Správce prostředků. Tato migrace se provádí po jedné virtuální síti. Neexistuje žádný další požadavek, pokud jde o nástroje nebo předpoklady pro migraci. Kroky migrace jsou shodné s existující migrací virtuální sítě a jsou zdokumentovány na [stránce migrace prostředků IaaS](../virtual-machines/windows/migration-classic-resource-manager-ps.md). Neexistuje žádné prostoje cesty dat během migrace, a proto existující úlohy by i nadále fungovat bez ztráty místnípřipojení během migrace. Veřejná IP adresa přidružená k bráně VPN se během procesu migrace nezmění. To znamená, že po dokončení migrace nebudete muset překonfigurovat místní směrovač.  

Model ve Správci prostředků se liší od klasického modelu a skládá se z bran virtuální sítě, místních síťových bran a prostředků připojení. Ty představují samotnou bránu VPN, místní lokalitu představující místní adresní prostor a připojení mezi nimi. Po dokončení migrace by vaše brány nebyly k dispozici v klasickém modelu a všechny operace správy na bránách virtuální sítě, místních síťových branách a objektech připojení musí být provedeny pomocí modelu Resource Manager.

## <a name="supported-scenarios"></a>Podporované scénáře
Nejběžnější scénáře připojení k síti VPN jsou pokryty klasickou migrací správce prostředků. Podporované scénáře zahrnují -

* Připojení přejděte na lokalitu
* Připojení k webu k webu s bránou VPN připojenou k místnímu umístění
* Připojení virtuální sítě k virtuální síti mezi dvěma virtuálními sítěmi pomocí bran VPN
* Více virtuálních sítí připojených ke stejnému umístění v místním umístění
* Připojení k více sítím
* Vynucené tunelové propojení povolilo virtuální sítě

Mezi scénáře, které nejsou podporovány, patří  

* Virtuální síť s bránou ExpressRoute a bránou VPN není momentálně podporovaná.
* Scénáře přenosu, kde jsou rozšíření virtuálních počítačů připojena k místním serverům. Omezení připojení k síti VPN pro přenos jsou podrobně popsána níže.

> [!NOTE]
> OVĚŘENÍ CIDR v modelu Resource Manager je přísnější než v klasickém modelu. Před migrací se před zahájením migrace ujistěte, že klasické rozsahy adres odpovídají platnému formátu CIDR. CIDR lze ověřit pomocí běžných validátorů CIDR. Virtuální síť nebo místní weby s neplatnými rozsahy CIDR při migraci by měly za následek selhání stavu.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>Migrace připojení virtuální sítě k virtuální síti
Připojení virtuální sítě k virtuální síti v klasickém prostředí bylo dosaženo vytvořením reprezentace místní sítě připojené virtuální sítě. Zákazníci museli vytvořit dvě místní lokality, které představovaly dvě virtuální sítě, které bylo potřeba propojit. Ty pak byly připojeny k odpovídajícím virtuálním sítím pomocí tunelového propojení IPsec k navázání připojení mezi dvěma virtuálními sítěmi. Tento model má problémy se správou, protože všechny změny rozsahu adres v jedné virtuální síti musí být také udržovány v odpovídající reprezentaci místní lokality. V modelu Správce prostředků toto řešení již není potřeba. Připojení mezi dvěma virtuálními sítěmi lze přímo dosáhnout pomocí typu připojení "Vnet2Vnet" v prostředku připojení. 

![Snímek obrazovky s migrací virtuální sítě na virtuální síť](./media/vpn-gateway-migration/migration1.png)

Během migrace virtuální sítě zjistíme, že připojená entita k aktuální bráně VPN virtuální sítě je další virtuální síť a zajistíme, že po dokončení migrace obou virtuálních sítí už neuvidíte dvě místní lokality představující druhou virtuální síť. Klasický model dvou bran VPN, dvou místních lokalit a dvou připojení mezi nimi je transformován na model Resource Manager se dvěma branami VPN a dvěma připojeními typu Vnet2Vnet.

## <a name="transit-vpn-connectivity"></a>Tranzitní připojení VPN
Brány VPN můžete nakonfigurovat v topologii tak, aby místní připojení pro virtuální síť bylo dosaženo připojením k jiné virtuální síti, která je přímo připojená k místnímu. Jedná se o připojení k síti VPN pro přenos, kde jsou instance v první virtuální síti připojené k místním prostředkům prostřednictvím přenosu do brány VPN v připojené virtuální síti, která je přímo připojená k místnímu. K dosažení této konfigurace v modelu klasického nasazení byste museli vytvořit místní lokalitu, která má agregované předpony představující připojený virtuální síť i místní adresní prostor. Tato reprezentační místní lokalita se pak připojí k virtuální síti, aby bylo dosaženo tranzitního připojení. Tento klasický model má také podobné problémy s možností správy, protože všechny změny v rozsahu místních adres musí být také udržovány v místní lokalitě představující agregaci virtuální sítě a místní. Zavedení podpory protokolu BGP ve Správci prostředků podporuje brány zjednodušuje správu, protože připojené brány mohou naučovat trasy z místních prostor bez ruční úpravy předpon.

![Snímek obrazovky se scénářem tranzitního směrování](./media/vpn-gateway-migration/migration2.png)

Vzhledem k tomu, že transformujeme virtuální síť na připojení virtuální sítě bez nutnosti místních lokalit, scénář přenosu ztratí místní připojení pro virtuální síť, která je nepřímo připojena k místnímu. Ztrátu připojení lze zmírnit následujícími dvěma způsoby, po dokončení migrace 

* Povolte protokol BGP na privátní bráně, které jsou propojeny a k místnímu. Povolení protokolu BGP obnoví připojení bez jakékoli jiné změny konfigurace, protože trasy se našetkají a inzerují mezi branami virtuální sítě. Všimněte si, že možnost BGP je k dispozici pouze u standardních a vyšších skum.
* Navázání explicitního připojení z ovlivněné virtuální sítě k bráně místní sítě představující místní umístění. To by také vyžadovalo změnu konfigurace v místním směrovači pro vytvoření a konfiguraci tunelového propojení IPsec.

## <a name="next-steps"></a>Další kroky
Po zjištění o podpoře migrace brány VPN přejděte na [migraci prostředků IaaS podporovanou platformou z klasického na Správce prostředků,](../virtual-machines/windows/migration-classic-resource-manager-ps.md) abyste mohli začít.

