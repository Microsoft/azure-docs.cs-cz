---
title: Migrace VPN Gateway Classic na Správce prostředků | Microsoft Docs
description: Tato stránka poskytuje přehled Správce prostředků migrace VPN Gateway Classic.
documentationcenter: na
services: vpn-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: caa8eb19-825a-4031-8b49-18fbf3ebc04e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/06/2020
ms.author: amsriva
ms.openlocfilehash: c9d7fb8be1894ffa5f8c35e16e1ed3aa0949b3ff
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488200"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>Migrace VPN Gateway Classic na Správce prostředků
Brány VPN se teď dají migrovat z modelu nasazení Classic na Správce prostředků. Další informace o [funkcích Azure Resource Manager a výhodách](../azure-resource-manager/management/overview.md)najdete v článku. V tomto článku najdete podrobné informace o tom, jak migrovat z klasických nasazení na novější model založený Správce prostředků. 

Brány VPN se migrují v rámci migrace virtuální sítě z klasického na Správce prostředků. Tato migrace se provádí po jedné virtuální síti v jednom okamžiku. Neexistují žádné další požadavky na nástroje nebo předpoklady pro migraci. Kroky migrace jsou stejné jako stávající migrace virtuální sítě a jsou zdokumentovány na [stránce migrace prostředků IaaS](../virtual-machines/migration-classic-resource-manager-ps.md). Během migrace nedochází k žádnému výpadku dat, takže stávající úlohy by během migrace nadále fungovaly bez ztráty místního připojení. Veřejná IP adresa přidružená k bráně VPN se během procesu migrace nemění. To znamená, že po dokončení migrace nebudete muset znovu nakonfigurovat místní směrovač.  

Model v Správce prostředků se liší od modelu Classic a skládá se z bran virtuální sítě, místních síťových bran a prostředků připojení. Tyto představují samotnou bránu VPN, místní lokalitu, která představuje místní adresní prostor a připojení mezi dvěma v uvedeném pořadí. Po dokončení migrace nebudou brány k dispozici v klasickém modelu a všechny operace správy u bran virtuální sítě, místních síťových bran a objektů připojení se musí provádět pomocí modelu Správce prostředků.

## <a name="supported-scenarios"></a>Podporované scénáře
K většině běžných scénářů připojení k síti VPN se Správce prostředků migrace vztahuje Classic. Mezi podporované scénáře patří:

* Ukázat na připojení k síti
* Připojení lokality k pracovišti s VPN Gateway připojená k místnímu umístění
* Připojení virtuální sítě k virtuální síti mezi dvěma virtuální sítě pomocí bran VPN
* Několik virtuální sítě připojených ke stejnému místnímu umístění
* Připojení k více lokalitám
* Virtuální sítě povolené vynucené tunelování

Nepodporované scénáře include-  

* Virtuální síť s bránou ExpressRoute a VPN Gateway se momentálně nepodporuje.
* Scénáře přenosu, kde jsou rozšíření virtuálních počítačů připojená k místním serverům. Omezení přenosu připojení k síti VPN jsou popsána níže.

> [!NOTE]
> Ověřování CIDR v modelu Správce prostředků je více striktní než v klasickém modelu. Před migrací zajistěte, aby dané klasické rozsahy adres odpovídaly platnému formátu CIDR před zahájením migrace. CIDR lze ověřit pomocí všech běžných validátorů CIDR. Virtuální síť nebo místní lokality s neplatnými rozsahy CIDR v případě, že by migrace způsobily stav selhání.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>Migrace připojení virtuální sítě k virtuální síti
Připojení virtuální sítě k virtuální síti v klasickém prostředí bylo dosaženo vytvořením místní reprezentace připojené virtuální sítě. Zákazníci byli požádáni o vytvoření dvou místních webů, které představovaly dva virtuální sítěy, které jsou potřeba k vzájemnému propojení. Ty se pak připojily k odpovídajícímu virtuální sítě pomocí tunelu IPsec k navázání připojení mezi dvěma virtuální sítě. Tento model má problémy se správou, protože všechny změny rozsahu adres v jedné virtuální síti musí být zachovány i v odpovídajícím zastoupení místní lokality. V Správce prostředků modelu toto řešení už není potřeba. Připojení mezi dvěma virtuální sítě se dá přímo dosáhnout pomocí typu připojení Vnet2Vnet v prostředku připojení. 

![Snímek virtuální sítě s migrací virtuální sítě](./media/vpn-gateway-migration/migration1.png)

Při migraci virtuální sítě zjistíme, že připojená entita ke stávající virtuální síti VPN je jinou virtuální síť a že po dokončení migrace obou virtuální sítě už neuvidíte dvě místní lokality, které představují jinou virtuální síť. Klasický model dvou bran VPN Gateway, dvě místní lokality a dvě připojení mezi nimi jsou transformované na Správce prostředků model se dvěma branami VPN a dvěma připojeními typu Vnet2Vnet.

## <a name="transit-vpn-connectivity"></a>Přenos připojení VPN
Brány VPN můžete v topologii nakonfigurovat tak, aby se místní připojení k virtuální síti dosáhlo připojením k jiné virtuální síti, která je přímo připojená k místní síti. Toto je přenosová konektivita VPN, kde se instance v první virtuální síti připojují k místním prostředkům prostřednictvím přenosu do brány VPN v připojené virtuální síti, která je přímo připojená k místnímu prostředí. Pokud chcete tuto konfiguraci dosáhnout v modelu nasazení Classic, budete muset vytvořit místní lokalitu, která má agregované předpony, které představují připojenou virtuální síť i místní adresní prostor. Tato znovu prezentační místní lokalita je pak připojená k virtuální síti, aby bylo možné dosáhnout možností přenosu. Tento klasický model má také podobné výzvy ke spravovatelnosti, protože kterákoli změna v rozsahu místních adres musí být také udržována v místní lokalitě, která představuje agregaci virtuální sítě a místní. Zavedení podpory protokolu BGP v Správce prostředků podporovaných branách zjednodušuje správu, protože připojené brány se mohou naučit trasy z místního prostředí bez ručních úprav předpon.

![Snímek obrazovky se scénářem směrování přenosu.](./media/vpn-gateway-migration/migration2.png)

Vzhledem k tomu, že transformuje připojení VNet-to-VNet bez nutnosti místních lokalit, scénář přenosu ztratí místní připojení pro virtuální síť, která je nepřímo připojená k místní síti. Po dokončení migrace se může ztráta připojení zmírnit následujícími dvěma způsoby: 

* Povolte protokol BGP u bran VPN, které jsou propojené společně a místně. Povolení protokolu BGP obnoví připojení bez jakékoli jiné změny konfigurace od chvíle, kdy se trasy procházejí a inzerují mezi bránami virtuální sítě. Upozorňujeme, že možnost BGP je dostupná jenom pro SKU Standard a vyšší.
* Vytvořte explicitní připojení ze zasažené virtuální sítě k místní síťové bráně, která představuje místní umístění. To by taky vyžadovalo změnu konfigurace na místním směrovači a vytvoření a konfiguraci tunelu IPsec.

## <a name="next-steps"></a>Další kroky
Po získání informací o podpoře migrace brány VPN můžete začít tím, že přejdete na [migraci prostředků IaaS podporovaných platformou z klasických na správce prostředků](../virtual-machines/migration-classic-resource-manager-ps.md) .