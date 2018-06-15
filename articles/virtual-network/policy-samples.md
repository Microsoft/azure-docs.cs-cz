---
title: Ukázkové šablony zásad | Microsoft Docs
description: Ukázkové šablony služby Azure Policy pro Virtual Network
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 05/02/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 2b8766a5353b015030872176e9032034afb7cb9d
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
ms.locfileid: "32779530"
---
# <a name="azure-policy-sample-templates-for-virtual-network"></a>Ukázkové šablony služby Azure Policy pro Virtual Network

Následující tabulka obsahuje odkazy na ukázkové šablony služby [Azure Policy](../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ukázky jsou součástí [úložiště ukázek služby Azure Policy](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Síť**||
| [NSG X ve všech síťových rozhraních](../azure-policy/scripts/nsg-on-nic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Vyžaduje, aby se ve všech virtuálních síťových rozhraních používala konkrétní skupina zabezpečení sítě. Zadejte ID skupiny zabezpečení sítě, která se má použít. |
| [NSG X ve všech podsítích](../azure-policy/scripts/nsg-on-subnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Vyžaduje, aby se ve všech virtuálních podsítích používala konkrétní skupina zabezpečení sítě. Zadejte ID skupiny zabezpečení sítě, která se má použít. |
| [Bez směrovací tabulky](../azure-policy/scripts/no-user-def-route-table.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  |Zakazuje nasazení virtuálních sítí se směrovací tabulkou. |
| [Použití schválené podsítě pro síťová rozhraní virtuálních počítačů](../azure-policy/scripts/use-approved-subnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Vyžaduje, aby síťová rozhraní používala schválenou podsíť. Zadejte ID schválené podsítě. |
| [Použití schválené virtuální sítě pro síťová rozhraní virtuálních počítačů](../azure-policy/scripts/use-approved-vnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Vyžaduje, aby síťová rozhraní používala schválenou virtuální síť. Zadáte ID schválené virtuální sítě. |
|**Monitorování**||
| [Auditování nastavení diagnostiky](../azure-policy/scripts/audit-diag-setting.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Audituje, jestli pro zadané typy prostředků nejsou povolená nastavení diagnostiky. Zadáte pole typů prostředků, u kterých se zkontroluje, jestli jsou povolená nastavení diagnostiky. |
|**Zásady vytváření názvů a textů**||
| [Povolení více vzorů pro názvy](../azure-policy/scripts/allow-multiple-name-patterns.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Povolíte jeden z mnoha vzorů názvů, který se použije pro prostředky. |
| [Vyžadování vzoru s podmínkou like](../azure-policy/scripts/enforce-like-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Zajistí, aby názvy prostředků splňovaly podmínku *like* pro vzor. |
| [Vyžadování shody se vzorem](../azure-policy/scripts/enforce-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Zajistí, aby názvy prostředků odpovídaly danému vzoru pro názvy. |
| [Vyžadování shody značky se vzorem](../azure-policy/scripts/enforce-tag-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Zajistí, aby hodnota značky odpovídala textovému vzoru. |
|**Značky**||
| [Iniciativa zásad fakturačních značek](../azure-policy/scripts/billing-tags-policy-init.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Vyžaduje zadání hodnot značek pro nákladové středisko a název produktu. S využitím předdefinovaných zásad používá a vynucuje požadované značky. Pro značky zadáte požadované hodnoty.  |
| [Vynucení značky a její hodnoty ve skupinách prostředků](../azure-policy/scripts/enforce-tag-rg.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Vyžaduje značku a hodnotu u skupiny prostředků. Zadejte požadovaný název a hodnotu značky.  |
| [Vynucení použití značky a její hodnoty](../azure-policy/scripts/enforce-tag-val.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Vyžaduje určený název a hodnotu značky. Zadáte název a hodnotu značky, které se mají vynutit.  |
| [Použití značky a její výchozí hodnoty](../azure-policy/scripts/apply-tag-def-val.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Připojí název a hodnotu zadané značky v případě, že se tato značka neposkytne. Zadáte název a hodnotu značky, které se mají použít.  |
|**Obecné**||
| [Povolená umístění](../azure-policy/scripts/allowed-locs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Vyžaduje, aby všechny prostředky byly nasazené do schválených umístění. Zadáte pole schválených umístění.  |
| [Povolené typy prostředků](../azure-policy/scripts/allowed-res-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Zajišťuje, že se nasadí jenom schválené typy prostředků. Zadáte pole typů prostředků, které jsou povoleny.  |
| [Nepovolené typy prostředků](../azure-policy/scripts/not-allowed-res-type.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Zakáže nasazení zadaných typů prostředků. Zadejte pole typů prostředků, které chcete blokovat.  |