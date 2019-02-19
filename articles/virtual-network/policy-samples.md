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
ms.openlocfilehash: b0b3b65ee2360a5cec32235f2ee5bf8d4839cc8b
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342073"
---
# <a name="azure-policy-sample-templates-for-virtual-network"></a>Ukázkové šablony služby Azure Policy pro Virtual Network

Následující tabulka obsahuje odkazy na [Azure Policy](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ukázky. Ukázky jsou součástí [úložiště ukázek služby Azure Policy](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Síť**||
| [NSG X ve všech síťových rozhraních](../governance/policy/samples/nsg-on-nic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Vyžaduje, aby se ve všech virtuálních síťových rozhraních používala konkrétní skupina zabezpečení sítě. Zadejte ID skupiny zabezpečení sítě, která se má použít. |
| [NSG X ve všech podsítích](../governance/policy/samples/nsg-on-subnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Vyžaduje, aby se ve všech virtuálních podsítích používala konkrétní skupina zabezpečení sítě. Zadejte ID skupiny zabezpečení sítě, která se má použít. |
| [Bez směrovací tabulky](../governance/policy/samples/no-user-defined-route-table.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  |Zakazuje nasazení virtuálních sítí se směrovací tabulkou. |
| [Použití schválené podsítě pro síťová rozhraní virtuálních počítačů](../governance/policy/samples/use-approved-subnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Vyžaduje, aby síťová rozhraní používala schválenou podsíť. Zadejte ID schválené podsítě. |
| [Použití schválené virtuální sítě pro síťová rozhraní virtuálních počítačů](../governance/policy/samples/use-approved-vnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Vyžaduje, aby síťová rozhraní používala schválenou virtuální síť. Zadáte ID schválené virtuální sítě. |
|**Monitorování**||
| [Auditování nastavení diagnostiky](../governance/policy/samples/audit-diagnostic-setting.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Audituje, jestli pro zadané typy prostředků nejsou povolená nastavení diagnostiky. Zadáte pole typů prostředků, u kterých se zkontroluje, jestli jsou povolená nastavení diagnostiky. |
|**Zásady vytváření názvů a textů**||
| [Povolení více vzorů pro názvy](../governance/policy/samples/allow-multiple-name-patterns.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Povolíte jeden z mnoha vzorů názvů, který se použije pro prostředky. |
| [Vyžadování vzoru s podmínkou like](../governance/policy/samples/enforce-like-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Zajistí, aby názvy prostředků splňovaly podmínku *like* pro vzor. |
| [Vyžadování shody se vzorem](../governance/policy/samples/enforce-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Zajistí, aby názvy prostředků odpovídaly danému vzoru pro názvy. |
| [Vyžadování shody značky se vzorem](../governance/policy/samples/enforce-tag-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Zajistí, aby hodnota značky odpovídala textovému vzoru. |
|**Značky**||
| [Iniciativa zásad fakturačních značek](../governance/policy/samples/billing-tags-policy-initiative.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Vyžaduje zadání hodnot značek pro nákladové středisko a název produktu. S využitím předdefinovaných zásad používá a vynucuje požadované značky. Pro značky zadáte požadované hodnoty.  |
| [Vynucení značky a její hodnoty ve skupinách prostředků](../governance/policy/samples/enforce-tag-on-resource-groups.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Vyžaduje značku a hodnotu u skupiny prostředků. Zadejte požadovaný název a hodnotu značky.  |
| [Vynucení použití značky a její hodnoty](../governance/policy/samples/enforce-tag-value.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Vyžaduje určený název a hodnotu značky. Zadáte název a hodnotu značky, které se mají vynutit.  |
| [Použití značky a její výchozí hodnoty](../governance/policy/samples/apply-tag-default-value.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Připojí název a hodnotu zadané značky v případě, že se tato značka neposkytne. Zadáte název a hodnotu značky, které se mají použít.  |
|**Obecné**||
| [Povolená umístění](../governance/policy/samples/allowed-locations.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Vyžaduje, aby všechny prostředky byly nasazené do schválených umístění. Zadáte pole schválených umístění.  |
| [Povolené typy prostředků](../governance/policy/samples/allowed-resource-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Zajišťuje, že se nasadí jenom schválené typy prostředků. Zadáte pole typů prostředků, které jsou povoleny.  |
| [Nepovolené typy prostředků](../governance/policy/samples/not-allowed-resource-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Zakáže nasazení zadaných typů prostředků. Zadejte pole typů prostředků, které chcete blokovat.  |