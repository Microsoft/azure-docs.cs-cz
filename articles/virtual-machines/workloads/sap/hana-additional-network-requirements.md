---
title: Další požadavky na síť pro SAP HANA v Azure (velké instance) | Microsoft Docs
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77614569"
---
# <a name="additional-network-requirements-for-large-instances"></a>Další požadavky na síť pro velké instance

V rámci nasazení velkých instancí SAP HANA v Azure můžete mít další požadavky na síť.

## <a name="add-more-ip-addresses-or-subnets"></a>Přidat další IP adresy nebo podsítě

Při přidávání dalších IP adres nebo podsítí použijte Azure Portal, PowerShell nebo rozhraní příkazového řádku Azure.

Místo generování nového agregovaného rozsahu přidejte nový rozsah IP adres jako nový rozsah do adresního prostoru virtuální sítě. Odešlete tuto změnu do Microsoftu. To vám umožní připojit se z tohoto nového rozsahu IP adres k jednotkám velkých instancí HANA ve vašem klientovi. K získání nového přidaného adresního prostoru virtuální sítě můžete otevřít žádost o podporu Azure. Po obdržení potvrzení proveďte následující kroky.

Pokud chcete vytvořit další podsíť z Azure Portal, přečtěte si téma [vytvoření virtuální sítě pomocí Azure Portal](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). Pokud ho chcete vytvořit z PowerShellu, přečtěte si téma [vytvoření virtuální sítě pomocí PowerShellu](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="add-virtual-networks"></a>Přidat virtuální sítě

Po počátečním připojení k jedné nebo více virtuálním sítím Azure budete možná chtít připojit další ty, které mají přístup k SAP HANA v Azure (velké instance). Nejdřív odešlete žádost o podporu Azure. V tomto požadavku Zahrňte konkrétní informace identifikující konkrétní nasazení Azure. Také zahrňte rozsah adres IP adres nebo rozsah adresního prostoru virtuální sítě Azure. SAP HANA ve službě Microsoft Service Management pak poskytuje potřebné informace, které potřebujete k propojení dalších virtuálních sítí a Azure ExpressRoute. Pro každou virtuální síť potřebujete jedinečný autorizační klíč pro připojení k okruhu ExpressRoute k velkým instancím HANA.

## <a name="increase-expressroute-circuit-bandwidth"></a>Zvýšit šířku pásma okruhu ExpressRoute

Projděte si SAP HANA na webu Microsoft Service Management. Pokud vás poradí, abyste zvýšili šířku pásma SAP HANA v Azure (velkých instancích) okruhu ExpressRoute, vytvořte žádost o podporu Azure. (Můžete požádat o zvýšení šířky pásma jednoho okruhu až na maximálně 10 GB/s.) Až se operace dokončí, dostanete oznámení. abyste tuto vyšší rychlost v Azure mohli povolit, nemusíte dělat nic dalšího.

## <a name="add-an-additional-expressroute-circuit"></a>Přidat další okruh ExpressRoute

Projděte si SAP HANA na webu Microsoft Service Management. Pokud vám doporučuje přidat další okruh ExpressRoute, vytvořte žádost o podporu Azure (včetně žádosti o získání autorizačních informací pro připojení k novému okruhu). Před vytvořením žádosti musíte definovat adresní prostor, který se používá ve virtuálních sítích. SAP HANA ve službě Microsoft Service Management pak může poskytnout autorizaci.

Když se vytvoří nový okruh a SAP HANA v konfiguraci služby Microsoft Service Management, obdržíte oznámení s informacemi, které potřebujete k pokračování provést. K tomuto dalšímu okruhu nemůžete připojit virtuální sítě Azure, pokud jsou už připojené k jiné SAP HANA v Azure (Velká instance) okruh ExpressRoute ve stejné oblasti Azure.

## <a name="delete-a-subnet"></a>Odstranění podsítě

Pokud chcete odebrat podsíť virtuální sítě, můžete použít Azure Portal, PowerShell nebo rozhraní příkazového řádku Azure CLI. Pokud je rozsah IP adres vaší virtuální sítě Azure nebo adresní prostor agregovaný rozsah, nebudete u vás od Microsoftu nic sledovat. (Upozorňujeme ale, že virtuální síť pořád šíří adresní prostor trasy protokolu BGP, který obsahuje odstraněnou podsíť.) Možná jste definovali rozsah adres virtuální sítě Azure nebo adresní prostor jako více rozsahů IP adres, z nichž jedna byla přiřazena k odstraněné podsíti. Nezapomeňte ho odstranit z adresního prostoru virtuální sítě. Pak informujte SAP HANA o správě služeb Microsoftu, abyste ho odebrali z rozsahů, které SAP HANA na Azure (velké instance) můžou komunikovat s.

Další informace najdete v tématu [odstranění podsítě](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet).

## <a name="delete-a-virtual-network"></a>Odstranění virtuální sítě

Informace najdete v tématu [odstranění virtuální sítě](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network).

SAP HANA ve službě Microsoft Service Management odstraní existující autorizaci v SAP HANA na Azure (velké instance) okruh ExpressRoute. Také odebere rozsah IP adres virtuální sítě Azure nebo adresní prostor pro komunikaci s velkými instancemi HANA.

Po odebrání virtuální sítě otevřete žádost o podporu Azure, která poskytne rozsah adresního prostoru IP adres nebo rozsahů, které se mají odebrat.

Abyste se ujistili, že jste odebrali všechno, odstraňte připojení ExpressRoute, bránu virtuální sítě, veřejnou IP adresu brány virtuální sítě a virtuální síť.

## <a name="delete-an-expressroute-circuit"></a>Odstranit okruh ExpressRoute

Pokud chcete odebrat další SAP HANA v Azure (velkých instancích) okruh ExpressRoute, otevřete žádost o podporu Azure s SAP HANA na webu Microsoft Service Management. Požadavek na odstranění okruhu V rámci předplatného Azure můžete v případě potřeby virtuální síť odstranit nebo ponechat. Je však nutné odstranit připojení mezi ExpressRoute okruhem HANA a propojenou bránou virtuální sítě.

## <a name="next-steps"></a>Další kroky

- [Instalace a konfigurace SAP HANA (velké instance) v Azure](hana-installation.md)
