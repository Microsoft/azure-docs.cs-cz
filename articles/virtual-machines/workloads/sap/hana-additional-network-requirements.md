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
ms.openlocfilehash: 8ee89553846309bd96f3cad3648e337488cf07d3
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164702"
---
# <a name="additional-network-requirements"></a>Další síťové požadavky

Někdy mohou mít další síťové požadavky při nasazování velkých instancích HANA. Tento článek ukazuje následující požadavky na síť:
- [Přidejte další IP adresy nebo podsítě ](#adding-more-ip-addresses-or-subnets)
- [Přidání virtuální vetworks](#adding-vnets)
- [Zvětšení šířky pásma okruhu expressroute](#increasing-expressroute-circuit-bandwidth)
- [Přidání okruhu expressroute další](#adding-an-additional-expressroute-circuit)
- [Odstraňuje se podsíť](#deleting-a-subnet)
- [Odstraňuje se virtuální síť](#deleting-a-vnet)
- [Odstraňuje se okruh express směrovače](#deleting-an-expressroute-circuit)


## <a name="adding-more-ip-addresses-or-subnets"></a>Přidejte další IP adresy nebo podsítě

Pomocí webu Azure portal, Powershellu nebo rozhraní příkazového řádku při přidávání dalších IP adres nebo podsítí.

V takovém případě doporučujeme přidat nový rozsah IP adres jako nový rozsah adres do adresního prostoru virtuální sítě, místo aby generovala nové agregované rozsahu. V obou případech musíte odeslat tuto změnu společnosti Microsoft umožňující připojení k mimo tento nový rozsah IP adres s jednotkami velká Instance HANA v klientovi. Můžete otevřít žádost o podporu Azure na získat novou adresu ve virtuální síti místo přidání. Jakmile dostanete potvrzení, proveďte další kroky.

Pokud chcete vytvořit další podsítě z portálu Azure portal, najdete v článku [vytvořit virtuální síť pomocí webu Azure portal](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)a pokud chcete vytvořit i z Powershellu, přečtěte si téma [vytvoření virtuální sítě pomocí Powershellu](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="adding-vnets"></a>Přidání virtuální sítě

Po počáteční připojení jeden nebo více virtuálními sítěmi Azure, můžete chtít přidat další značky, které přistupují k SAP HANA v Azure (velké instance). Nejprve odeslání žádosti o podporu Azure, v rámci požadavku uvést i konkrétní informace, které identifikují konkrétní nasazení v Azure a rozsahy IP adres místo adresního prostoru virtuální sítě Azure. SAP HANA v Azure Service managementu pak poskytuje nezbytné informace, které potřebujete připojit další virtuální sítě a ExpressRoute. Pro každou virtuální síť budete potřebovat jedinečné autorizační klíč k připojení k okruhu ExpressRoute pro velké instance HANA.

Postup přidání nové virtuální sítě Azure:

1. Zobrazit kompletní prvním krokem v procesu připojování _vytváření virtuální sítě Azure_ oddílu.
2. Zobrazit kompletní druhým krokem v procesu připojování _Vytvoření podsítě brány_ oddílu.
3. Připojit další virtuální sítě k okruhu ExpressRoute velké Instance HANA, otevřete žádost o podporu Azure s informacemi o nové sítě VNet a požádat o nový klíč autorizace.
4. Po oznámení dokončení povolení, najdete v článku použití poskytovaný společností Microsoft autorizačních informací dokončete třetí krok v procesu registrace _propojení virtuálních sítí_ oddílu.

## <a name="increasing-expressroute-circuit-bandwidth"></a>Zvýšení šířky pásma okruhu ExpressRoute

Poraďte se se SAP HANA v Azure Service Management. Pokud se doporučuje kvůli zvýšení šířky pásma SAP Hana v okruhu ExpressRoute Azure (velké instance), vytvoření žádosti o podporu Azure. (Můžete si vyžádat navýšení pro šířku pásma jednoho okruhu až do maximálního počtu 10 GB/s.) Pak se zobrazí oznámení po dokončení operace; nemusíte dělat nic dalšího potřebná k povolení vyšší rychlost v Azure.

## <a name="adding-an-additional-expressroute-circuit"></a>Přidání další okruh ExpressRoute

Poraďte se s SAP HANA v Azure Service Management, pokud jste byli informováni, že je potřeba další okruh ExpressRoute, ujistěte se, podpory žádosti o vytvoření nového okruhu ExpressRoute (a získat informace o povolení pro připojení k němu) Azure. Adresní prostor, který se použije na virtuální sítě musí být definovány před provedením požadavku, aby pro SAP HANA v Azure Service Management umožňují ověření.

Po vytvoření nového okruhu a SAP HANA v Azure Service Management configuration byla dokončena, budete dostávat oznámení s informací, že které potřebujete, aby bylo možné pokračovat. Postupujte podle kroků uvedených výše pro vytvoření a připojení k této další okruh nové sítě VNet. Nejste schopni propojení virtuálních sítí Azure pro tento další okruh, pokud již připojeni k jiné SAP HANA v okruhu ExpressRoute Azure (velká Instance) ve stejné oblasti Azure.

## <a name="deleting-a-subnet"></a>Odstraňuje se podsíť

Chcete-li odebrat k podsíti virtuální sítě, je možné webu Azure portal, Powershellu nebo rozhraní příkazového řádku. V případě, že IP adres virtuální sítě Azure adresu rozsahu/Azure adresní prostor virtuální sítě byla agregované rozsah, neexistuje žádný postupujte pro jste s Microsoftem. S tím rozdílem, že virtuální síť je stále šíření adresní prostor trasy protokolu BGP, která zahrnuje odstraněné podsítě. Pokud jste definovali jako více rozsahů IP adres, z nichž jeden byl přiřazen odstraněné podsítě IP adres virtuální sítě Azure adresu rozsahu/Azure adresní prostor virtuální sítě, by měl odstranit, který mimo Adresní prostor vaší virtuální sítě a následně informovat SAP HANA v Azure Service managementu na Odeberte z rozsahů, které může komunikovat se SAP HANA v Azure (velké instance).

Chcete-li odstranit podsíť, naleznete v tématu [odstranění podsítě](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet) Další informace o vytváření podsítí.

## <a name="deleting-a-vnet"></a>Odstraňuje se virtuální sítě

Pokud chcete odstranit virtuální síť, naleznete v tématu [odstranění virtuální sítě](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network). SAP HANA v Azure Service Management Odebere existující autorizace na systému SAP HANA na okruh Azure ExpressRoute (velké instance) a odeberte IP adres virtuální sítě Azure adresu rozsahu/Azure adresní prostor virtuální sítě pro komunikaci s velkých instancích HANA.

Odebraný virtuální síti otevřete žádost o podporu Azure na zadejte rozsahy IP adres místo k odebrání.

K zajištění, že všechno, co Odebereme, odstraňte následující položky:

- Připojení ExpressRoute, brána virtuální sítě, veřejnou IP adresu brány virtuální sítě a virtuální sítě

## <a name="deleting-an-expressroute-circuit"></a>Odstraňuje se okruh ExpressRoute

Odebrat další SAP HANA v okruhu ExpressRoute Azure (velké instance), otevřete žádost o podporu Azure se SAP HANA v Azure Service Management a požadovat, že by se měla odstranit okruh. V rámci předplatného Azure můžete odstranit nebo ponechat virtuální sítě podle potřeby. Nicméně je nutné odstranit propojení okruhu ExpressRoute velkých instancích HANA a propojené brány virtuální sítě.

Pokud chcete odebrat virtuální sítě, postupujte podle pokynů na odstranění virtuální sítě v části výše.

**Další kroky**

- Přečtěte si [instalace a konfigurace SAP HANA (velké instance) v Azure](hana-installation.md).
