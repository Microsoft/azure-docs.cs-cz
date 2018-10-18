---
title: Další síťové požadavky pro SAP HANA v Azure (velké instance) | Dokumentace Microsoftu
description: Další síťové požadavky pro SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2f4c74076f2763addc1f2b971d8ea0ca29ce6fd0
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49392994"
---
# <a name="additional-network-requirements-for-large-instances"></a>Další síťové požadavky pro velké instance

Při nasazování velkých instancí SAP HANA v Azure můžete mít další síťové požadavky.

## <a name="add-more-ip-addresses-or-subnets"></a>Přidat další IP adresy nebo podsítě

Při přidávání více IP adres nebo podsítí, použijte na webu Azure portal, Powershellu nebo rozhraní příkazového řádku Azure.

Přidáte nový rozsah IP adres jako nového rozsahu, který do adresního prostoru virtuální sítě, místo aby generovala nové agregované rozsahu. Odešlete tuto změnu do Microsoftu. To vám umožní připojit se z tohoto nového rozsahu IP adres s jednotkami velká instance HANA v klientovi. Můžete otevřít žádost o podporu Azure získat přidán nový adresní prostor virtuální sítě. Jakmile dostanete potvrzení, proveďte další kroky.

Vytvoření další podsítě z portálu Azure portal najdete v tématu [vytvořit virtuální síť pomocí webu Azure portal](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). Vytvoření jednoho z Powershellu najdete v tématu [vytvoření virtuální sítě pomocí Powershellu](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="add-virtual-networks"></a>Přidat virtuální sítě

Po počátečním připojení jeden nebo více virtuálních sítí Azure, můžete chtít připojit další značky, které přistupují k SAP HANA v Azure (velké instance). Nejprve odeslání žádosti o podporu Azure. V rámci požadavku obsahovala určité informace identifikující konkrétní nasazení v Azure. Zahrnují také místo rozsah IP adres nebo rozsahy adresní prostor virtuální sítě Azure. SAP HANA v Microsoft Service Management pak obsahuje nezbytné informace potřebné k připojení dalších virtuálních sítí a Azure ExpressRoute. Každá virtuální síť budete potřebovat jedinečné autorizační klíč k připojení k okruhu ExpressRoute pro velké instance HANA.

## <a name="increase-expressroute-circuit-bandwidth"></a>Zvětšit šířku pásma okruhu ExpressRoute

Poraďte se se SAP HANA v Microsoft Service Management. Pokud se vám, abyste zvětšit šířku pásma SAP Hana v okruhu ExpressRoute Azure (velké instance), vytvoření žádosti o podporu Azure. (Můžete si vyžádat navýšení pro šířku pásma jednoho okruhu až do maximálního počtu 10 GB/s.) Pak se zobrazí oznámení po dokončení operace; nemusíte dělat nic dalšího povolení vyšší rychlost v Azure.

## <a name="add-an-additional-expressroute-circuit"></a>Přidat další okruh ExpressRoute

Poraďte se se SAP HANA v Microsoft Service Management. Pokud jsou přímo neradí, si můžete přidat další okruh ExpressRoute, vytvoření žádosti o podporu Azure (včetně požadavek na získání informace o ověření pro připojení k nového okruhu). Před provedením požadavku, je nutné definovat adresní prostor, který používá ve virtuálních sítích. SAP HANA v Microsoft Service Management jim pak můžou autorizace.

Při vytvoření nového okruhu a SAP HANA v Microsoft služba Management configuration byla dokončena, obdržíte oznámení s informací, že které potřebujete, aby bylo možné pokračovat. Nemůžete připojit virtuální sítě Azure k tento další okruh, pokud už jsou připojené k jiné SAP HANA v okruhu ExpressRoute Azure (velká instance) ve stejné oblasti Azure.

## <a name="delete-a-subnet"></a>Odstranit podsíť

Chcete-li odebrat podsíť virtuální sítě, můžete na webu Azure portal, Powershellu nebo rozhraní příkazového řádku Azure. Pokud vaše virtuální síť Azure rozsah nebo adresa adresní prostor IP adres byla agregované rozsah, neexistuje žádný postupujte pro jste s Microsoftem. (Mějte na paměti, ale, že virtuální síť je stále šíření adresního prostoru protokolu BGP trasy, která zahrnuje odstraněné podsítě.) Možná jste definovali Azure adresní prostor virtuální sítě rozsah nebo adresa jako více rozsahů IP adres, z nichž jeden byl přiřazen odstraněné podsítě. Nezapomeňte odstranit, který z adresního prostoru virtuální sítě. Potom informujte SAP HANA v Microsoft Service Management ho neodeberete z rozsahů, které může komunikovat se SAP HANA v Azure (velké instance).

Další informace najdete v tématu [odstranění podsítě](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet).

## <a name="delete-a-virtual-network"></a>Odstranění virtuální sítě

Informace najdete v tématu [odstranění virtuální sítě](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network).

SAP HANA v Microsoft Service Management Odebere existující autorizace na systému SAP HANA v okruhu ExpressRoute Azure (velké instance). Zároveň se tím odebere virtuální sítě Azure rozsah nebo adresa adresní prostor IP adres pro komunikaci se službou velkých instancích HANA.

Jakmile odstraníte virtuální sítě, otevřete žádost o podporu Azure k poskytování rozsah IP adres nebo rozsahy, která se má odebrat.

K zajištění, že všechno, co odeberete, odstraňte připojení ExpressRoute, bránu virtuální sítě, veřejná IP brány virtuální sítě a virtuální sítě.

## <a name="delete-an-expressroute-circuit"></a>Odstranit okruh ExpressRoute

Chcete-li odebrat další SAP HANA v okruhu ExpressRoute Azure (velké instance), otevřete žádost o podporu Azure se SAP HANA v Microsoft Service Management. Žádost o odstranit okruh. V rámci předplatného Azure můžete odstranit nebo ponechat virtuální sítě, podle potřeby. Nicméně je nutné odstranit propojení okruhu ExpressRoute velkých instancích HANA a bránu virtuální sítě propojené.

## <a name="next-steps"></a>Další postup

- [Instalace a konfigurace SAP HANA (velké instance) v Azure](hana-installation.md)
