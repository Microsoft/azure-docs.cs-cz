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
ms.openlocfilehash: c553e3f2de963abb4a3c23a4114eecfa33d0aa61
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735337"
---
# <a name="azure-resource-manager-template-samples-for-virtual-network"></a>Ukázkové šablony Azure Resource Manageru pro virtuální síť

Následující tabulka obsahuje odkazy na ukázkové šablony Azure Resource Manageru. Šablony můžete nasadit pomocí portálu Azure [Portal](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), Azure [CLI](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo Azure [PowerShellu](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud se chcete dozvědět, jak si můžete vytvořit vlastní šablony, přečtěte si články [Vytvoření první šablony](../azure-resource-manager/resource-manager-create-first-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Princip struktury a syntaxe šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Syntaxi JSON a vlastnosti, které chcete použít v šablonách najdete v tématu [typy prostředků Microsoft.Network](/azure/templates/microsoft.network/allversions).


| Úkol | Popis |
|----|----|
|[Vytvoření virtuální sítě se dvěma podsítěmi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)| Vytvoří virtuální síť se dvěma podsítěmi.|
|[Směrování provozu přes síťové virtuální zařízení](https://github.com/Azure/azure-quickstart-templates/tree/master/201-userdefined-routes-appliance)| Vytvoří virtuální síť se třemi podsítěmi. Nasadí virtuální počítač do každé podsítě. Vytvoří směrovací tabulku, která obsahuje trasy pro směrování z jedné podsítě do druhé přes virtuální počítač v třetí podsíti. Přidruží směrovací tabulku k jedné z podsítí.|
|[Vytvoření koncového bodu služby pro virtuální síť pro Azure Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-2subnets-service-endpoints-storage-integration)|Vytvoří novou virtuální síť se dvěma podsítěmi a síťovým rozhraním v každé podsíti. Povolí koncový bod služby na Azure Storage pro jednu z podsítí a zabezpečí nový účet úložiště pro danou podsíť.|
|[Připojení dvou virtuálních sítí](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-to-vnet-peering)| Vytvoří dvě virtuální sítě a partnerský vztah mezi nimi.|
|[Vytvoření virtuálního počítače s několika IP adresami](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-ipconfig)| Vytvoří virtuální počítač Windows nebo Linux s několika IP adresami.|
|[Konfigurace protokolu IPv4 + IPv6 duální zásobník virtuální sítě](https://github.com/Azure/azure-quickstart-templates/tree/master/ipv6-in-vnet)|Nasadí virtuální síť duální sadou protokolů (IPv4 + IPv6) s dva virtuální počítače a Azure Load balancer úrovně Basic s protokoly IPv4 a IPv6 veřejné IP adresy. |
