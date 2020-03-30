---
title: Další požadavky na síť pro SAP HANA v Azure (velké instance) | Dokumenty společnosti Microsoft
description: Další požadavky na síť pro SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 66bbd8b462ac35756be0fae6eba940ba0aba6c4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614569"
---
# <a name="additional-network-requirements-for-large-instances"></a>Další požadavky na síť pro velké instance

Můžete mít další požadavky na síť jako součást nasazení velkých instancí SAP HANA v Azure.

## <a name="add-more-ip-addresses-or-subnets"></a>Přidání dalších adres IP nebo podsítí

Při přidání dalších IP adres nebo podsítí použijte portál Azure, PowerShell nebo rozhraní příkazového příkazu k Síti Azure.

Přidejte nový rozsah IP adres jako nový rozsah do adresního prostoru virtuální sítě namísto generování nového agregovaného rozsahu. Odešlete tuto změnu společnosti Microsoft. To umožňuje připojit z tohoto nového rozsahu IP adres k jednotkám velkých instancí HANA ve vašem klientovi. Můžete otevřít žádost o podporu Azure, abyste získali nový adresní prostor virtuální sítě. Po obdržení potvrzení proveďte další kroky.

Pokud chcete vytvořit další podsíť z portálu Azure, přečtěte si, že [najdete v tématu Vytvoření virtuální sítě pomocí portálu Azure](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). Pokud chcete ho vytvořit z PowerShellu, přečtěte [si tématu Vytvoření virtuální sítě pomocí PowerShellu](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="add-virtual-networks"></a>Přidání virtuálních sítí

Po počátečním připojení jedné nebo více virtuálních sítí Azure můžete chtít připojit další, které přistupují k SAP HANA v Azure (velké instance). Nejprve odešlete žádost o podporu Azure. Do tohoto požadavku uveďte konkrétní informace identifikující konkrétní nasazení Azure. Zahrňte také rozsah adresního prostoru IP nebo rozsahy adresního prostoru virtuální sítě Azure. SAP HANA na Microsoft Service Management pak poskytuje potřebné informace, které potřebujete k připojení dalších virtuálních sítí a Azure ExpressRoute. Pro každou virtuální síť potřebujete jedinečný autorizační klíč pro připojení k okruhu ExpressRoute k velkým instancím HANA.

## <a name="increase-expressroute-circuit-bandwidth"></a>Zvýšení šířky pásma okruhu ExpressRoute

Poraďte se se společností SAP HANA o správě služeb společnosti Microsoft. Pokud vám poradí zvýšit šířku pásma okruhu ExpressRoute SAP HANA (velké instance), vytvořte žádost o podporu Azure. (Můžete požádat o zvýšení šířky pásma jednoho okruhu až na maximálně 10 Gb/s.) Poté obdržíte oznámení po dokončení operace. nemusíte dělat nic jiného, abyste tuto vyšší rychlost v Azure povolili.

## <a name="add-an-additional-expressroute-circuit"></a>Přidání dalšího okruhu ExpressRoute

Poraďte se se společností SAP HANA o správě služeb společnosti Microsoft. Pokud vám poradí přidat další okruh ExpressRoute, vytvořte žádost o podporu Azure (včetně žádosti o získání informací o autorizaci pro připojení k novému okruhu). Před podáním žádosti je nutné definovat adresní prostor používaný ve virtuálních sítích. SAP HANA na Microsoft Service Management pak můžete poskytnout autorizaci.

Po vytvoření nového okruhu a sap hana na konfiguraci Microsoft Service Management je dokončena, obdržíte oznámení s informacemi, které je třeba pokračovat. Virtuální sítě Azure nelze připojit k tomuto dodatečnému okruhu, pokud jsou už připojené k jinému okruhu SAP HANA v Azure (velká instance) ExpressRoute ve stejné oblasti Azure.

## <a name="delete-a-subnet"></a>Odstranění podsítě

Pokud chcete odebrat podsíť virtuální sítě, můžete použít portál Azure, PowerShell nebo rozhraní příkazového příkazu Konzi. Pokud váš rozsah IP adres virtuální sítě Azure nebo adresní prostor byl agregovaný rozsah, neexistuje žádné zpracování pro vás s Microsoftem. (Všimněte si však, že virtuální síť je stále šíří směrový prostor Protokolu BGP, který zahrnuje odstraněnou podsíť.) Je možné, že jste definovali rozsah adres virtuální sítě Azure nebo adresní prostor jako více rozsahů IP adres, z nichž jeden byl přiřazen k odstraněné podsíti. Nezapomeňte to odstranit z adresního prostoru virtuální sítě. Pak informujte SAP HANA na Microsoft Service Management odebrat z rozsahů, které SAP HANA v Azure (velké instance) je povoleno komunikovat s.

Další informace naleznete [v tématu Odstranění podsítě](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet).

## <a name="delete-a-virtual-network"></a>Odstranění virtuální sítě

Další informace naleznete [v tématu Odstranění virtuální sítě](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network).

SAP HANA na Microsoft Service Management odebere existující autorizace na SAP HANA v Azure (velké instance) ExpressRoute okruhu. Také odebere rozsah IP adres virtuální sítě Azure nebo adresní prostor pro komunikaci s velkými instancemi HANA.

Po odebrání virtuální sítě otevřete žádost o podporu Azure a poskytněte rozsah prostoru IP adres nebo rozsahy, které mají být odebrány.

Chcete-li zajistit odebrání všeho, odstraňte připojení ExpressRoute, bránu virtuální sítě, veřejnou IP adresu brány virtuální sítě a virtuální síť.

## <a name="delete-an-expressroute-circuit"></a>Odstranění okruhu ExpressRoute

Chcete-li odebrat další okruh ExpressRoute SAP HANA v Azure (velké instance), otevřete žádost o podporu Azure pomocí SAP HANA ve službě Microsoft Service Management. Požádejte o odstranění okruhu. V rámci předplatného Azure můžete virtuální síť podle potřeby odstranit nebo zachovat. Je však nutné odstranit připojení mezi okruhem expressroute velkých instancí HANA a propojenou bránou virtuální sítě.

## <a name="next-steps"></a>Další kroky

- [Instalace a konfigurace SAP HANA (velké instance) v Azure](hana-installation.md)
