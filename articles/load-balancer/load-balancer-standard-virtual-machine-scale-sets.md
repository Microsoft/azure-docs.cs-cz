---
title: Azure Standard Load Balancer a škálovací sady virtuálních počítačů
titleSuffix: Azure Standard Load Balancer and Virtual Machine Scale Sets
description: Pomocí této cesty výukového programu Začínáme s Azure Standard Load Balancer a Virtual Machine Scale Sets.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: 7e1df754a4a4ca5878d93d53282fd39191313b54
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97883159"
---
# <a name="azure-load-balancer-with-azure-virtual-machine-scale-sets"></a>Azure Load Balancer se službou Azure Virtual Machine Scale Sets

Při práci se službou Virtual Machine Scale Sets a nástrojem pro vyrovnávání zatížení je třeba vzít v úvahu následující pokyny:

## <a name="port-forwarding-and-inbound-nat-rules"></a>Pravidla předávání portů a příchozí NAT:
  * Po vytvoření sady škálování se port back-endu nedá změnit pro pravidlo vyrovnávání zatížení používané sondou stavu nástroje pro vyrovnávání zatížení. Pokud chcete změnit port, můžete odstranit sondu stavu tak, že aktualizujete sadu škálování virtuálního počítače Azure, aktualizujete port a pak znovu nakonfigurujete test stavu.
  * Při použití sady škálování virtuálních počítačů ve fondu back-end nástroje pro vyrovnávání zatížení se automaticky vytvoří výchozí pravidla příchozího překladu adres (NAT).
  
## <a name="inbound-nat-pool"></a>Příchozí fond NAT:
  * Každá sada škálování virtuálního počítače musí mít aspoň jeden příchozí fond NAT. 
  * Příchozí fond NAT je kolekcí pravidel příchozího překladu adres (NAT). Jeden příchozí fond NAT nepodporuje více sad škálování virtuálních počítačů.

## <a name="load-balancing-rules"></a>Pravidla vyrovnávání zatížení:
  * Při použití sady škálování virtuálních počítačů ve fondu back-end nástroje pro vyrovnávání zatížení se automaticky vytvoří výchozí pravidlo vyrovnávání zatížení.
  
## <a name="outbound-rules"></a>Odchozí pravidla:
  *  Pokud chcete vytvořit odchozí pravidlo pro back-end fond, na který se už odkazuje pravidlo vyrovnávání zatížení, musíte **nejdřív na portálu** při vytváření pravidla vyrovnávání zatížení označit **možnost vytvořit implicitní odchozí pravidla** .

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="Vytvoření pravidla vyrovnávání zatížení" border="true":::

K nasazení sady škálování virtuálního počítače s existujícím nástrojem pro vyrovnávání zatížení Azure můžete použít následující metody.

* [Nakonfigurujte sadu škálování virtuálního počítače s existujícím Azure Load Balancer pomocí Azure Portal](./configure-vm-scale-set-portal.md).
* [Nakonfigurujte sadu škálování virtuálního počítače s existujícím Azure Load Balancer pomocí Azure PowerShell](./configure-vm-scale-set-powershell.md).
* [Nakonfigurujte sadu škálování virtuálního počítače s existujícím Azure Load Balancer pomocí Azure CLI](./configure-vm-scale-set-cli.md).
* [Aktualizovat nebo odstranit existující Azure Load Balancer používané sadou škálování virtuálních počítačů](./update-load-balancer-with-vm-scale-set.md)
