---
title: Ukázkové šablony Azure Resource Manageru pro virtuální síť | Microsoft Docs
description: Přečtěte si o různých šablonách Azure Resource Manageru, které můžete nasadit ve virtuálních sítích Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 5971b3251812ea4193e71173943ffc0d48207567
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75975355"
---
# <a name="azure-resource-manager-template-samples-for-virtual-network"></a>Ukázkové šablony Azure Resource Manageru pro virtuální síť

Následující tabulka obsahuje odkazy na ukázkové šablony Azure Resource Manageru. Šablony můžete nasadit pomocí portálu Azure [Portal](../azure-resource-manager/templates/deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), Azure [CLI](../azure-resource-manager/templates/deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo Azure [PowerShellu](../azure-resource-manager/templates/deploy-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud se chcete dozvědět, jak si můžete vytvořit vlastní šablony, přečtěte si články [Vytvoření první šablony](../azure-resource-manager/resource-manager-create-first-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Princip struktury a syntaxe šablon Azure Resource Manageru](../azure-resource-manager/templates/template-syntax.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Informace o syntaxi a vlastnostech JSON pro použití v šablonách naleznete v tématu [typy prostředků Microsoft. Network](/azure/templates/microsoft.network/allversions).


| Úkol | Popis |
|----|----|
|[Vytvoření virtuální sítě se dvěma podsítěmi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)| Vytvoří virtuální síť se dvěma podsítěmi.|
|[Směrování provozu přes síťové virtuální zařízení](https://github.com/Azure/azure-quickstart-templates/tree/master/201-userdefined-routes-appliance)| Vytvoří virtuální síť se třemi podsítěmi. Nasadí virtuální počítač do každé podsítě. Vytvoří směrovací tabulku, která obsahuje trasy pro směrování z jedné podsítě do druhé přes virtuální počítač v třetí podsíti. Přidruží směrovací tabulku k jedné z podsítí.|
|[Vytvoření koncového bodu služby pro virtuální síť pro Azure Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-2subnets-service-endpoints-storage-integration)|Vytvoří novou virtuální síť se dvěma podsítěmi a síťovým rozhraním v každé podsíti. Povolí koncový bod služby na Azure Storage pro jednu z podsítí a zabezpečí nový účet úložiště pro danou podsíť.|
|[Připojení dvou virtuálních sítí](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-to-vnet-peering)| Vytvoří dvě virtuální sítě a partnerský vztah mezi nimi.|
|[Vytvoření virtuálního počítače s několika IP adresami](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-ipconfig)| Vytvoří virtuální počítač Windows nebo Linux s několika IP adresami.|
|[Konfigurovat virtuální síť Dual Stack IPv4 a IPv6](https://github.com/Azure/azure-quickstart-templates/tree/master/ipv6-in-vnet)|Nasadí virtuální síť s duálním zásobníkem (IPv4 + IPv6) se dvěma virtuálními počítači a Load Balancer Azure Basic s IP adresami IPv4 a IPv6. |
