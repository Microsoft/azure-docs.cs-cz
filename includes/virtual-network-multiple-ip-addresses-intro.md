---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-network
author: jimdial
ms.service: virtual-network
ms.topic: include
ms.date: 04/09/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: 68ce927c05f7179cca0aa2833460b9550f0a82d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96028218"
---
> [!div class="op_single_selector"]
> * [Azure Portal](../articles/virtual-network/virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](../articles/virtual-network/virtual-network-multiple-ip-addresses-powershell.md)
> * [Azure CLI](../articles/virtual-network/virtual-network-multiple-ip-addresses-cli.md)
>

Virtuální počítač (VM) Azure má připojené jedno nebo několik síťových rozhraní (NIC). Ke každému síťovému rozhraní může být přiřazená jedna nebo několik statických nebo dynamických veřejných a privátních IP adres. Možnost přiřadit virtuálnímu počítači několik IP adres umožňuje:

* Hostovat několik webů nebo služeb s různými IP adresami a certifikáty SSL na jednom serveru.
* Využívat počítač jako virtuální síťové zařízení, jako je třeba brána firewall nebo nástroj pro vyrovnávání zatížení.
* Přidat libovolnou IP adresu pro libovolné síťové rozhraní do back-endového fondu služby Azure Load Balancer. V minulosti bylo možné do back-endového fondu přidávat jenom primární IP adresy pro primární síťové rozhraní. Další informace o vyrovnávání zatížení u konfigurací s několika IP adresami najdete v článku věnovaném [vyrovnávání zatížení u konfigurací s několika IP adresami](../articles/load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Každé síťové rozhraní připojené k virtuálnímu počítači má přidruženu jednu nebo několik konfigurací IP. Každá konfigurace má přiřazenou jednu statickou nebo dynamickou privátní IP adresu. Každá konfigurace také může mít přiřazen jeden prostředek veřejné IP adresy. Prostředek veřejné IP adresy má přiřazenou buď dynamickou, nebo statickou veřejnou IP adresu. Další informace o IP adresách v Azure najdete v článku [IP adresy v Azure](../articles/virtual-network/public-ip-addresses.md). 

Existuje omezení, kolik privátních IP adres může být přiřazeno síťovému rozhraní. K dispozici je také limit počtu veřejných IP adres, které lze použít v rámci předplatného Azure. Podrobnosti najdete v článku o [omezeních Azure](../articles/azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).