---
title: Přidání pravidel pro Azure Standard Load Balancer a Virtual Machine Scale Sets
titleSuffix: Add rules for Azure Standard Load Balancer and virtual machine scale sets
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
ms.openlocfilehash: 7a2e0531427343a2ec267de54cee05b5eb25889f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592275"
---
# <a name="add-rules-for-azure-load-balancer-with-virtual-machine-scale-sets"></a>Přidání pravidel pro Azure Load Balancer se službou Virtual Machine Scale Sets

Když pracujete se službou Virtual Machine Scale Sets a Azure Load Balancer, vezměte v úvahu následující pokyny.

## <a name="port-forwarding-and-inbound-nat-rules"></a>Pravidla předávání portů a příchozí NAT

Po vytvoření sady škálování se back-end port nedá upravit pro pravidlo vyrovnávání zatížení, které používá sonda stavu nástroje pro vyrovnávání zatížení. Pokud chcete změnit port, odeberte sondu stavu tak, že aktualizujete sadu škálování virtuálního počítače a aktualizujete port. Pak znovu nakonfigurujte test stavu.

Když použijete sadu škálování virtuálního počítače ve fondu back-end nástroje pro vyrovnávání zatížení, automaticky se vytvoří výchozí pravidla příchozího překladu adres (NAT).
  
## <a name="inbound-nat-pool"></a>Příchozí fond NAT

Každá sada škálování virtuálního počítače musí mít aspoň jeden příchozí fond NAT. Příchozí fond NAT je kolekcí pravidel příchozího překladu adres (NAT). Jeden příchozí fond NAT nemůže podporovat víc sad Virtual Machine Scale Sets.

## <a name="load-balancing-rules"></a>Pravidla vyrovnávání zatížení

Když použijete sadu škálování virtuálního počítače ve fondu back-end nástroje pro vyrovnávání zatížení, automaticky se vytvoří výchozí pravidlo vyrovnávání zatížení.
  
## <a name="outbound-rules"></a>Pravidla odchozích přenosů

Chcete-li vytvořit odchozí pravidlo pro fond back-end, na který již odkazuje pravidlo vyrovnávání zatížení, vyberte možnost **ne** v části **vytvořit implicitní odchozí pravidla** v Azure Portal při vytvoření pravidla vyrovnávání zatížení.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="Snímek obrazovky zobrazující vytvoření pravidla vyrovnávání zatížení" border="true":::

Pomocí následujících metod nasaďte sadu škálování virtuálního počítače s existující instancí Load Balancer:

* [Konfigurace sady škálování virtuálních počítačů pomocí existující instance Azure Load Balancer s využitím Azure Portal](./configure-vm-scale-set-portal.md)
* [Konfigurace sady škálování virtuálních počítačů pomocí existující instance Azure Load Balancer s využitím Azure PowerShell](./configure-vm-scale-set-powershell.md)
* [Konfigurace sady škálování virtuálních počítačů s existující instancí Azure Load Balancer pomocí rozhraní příkazového řádku Azure](./configure-vm-scale-set-cli.md)
* [Aktualizace nebo odstranění existující instance Azure Load Balancer používané sadou škálování virtuálního počítače](./update-load-balancer-with-vm-scale-set.md)
