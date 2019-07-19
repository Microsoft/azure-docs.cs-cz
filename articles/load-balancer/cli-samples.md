---
title: Ukázky Azure CLI pro nástroj pro vyrovnávání zatížení
titlesuffix: Azure Load Balancer
description: Ukázky Azure CLI
services: load-balancer
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: allensu
ms.openlocfilehash: 9567dc1425ea74a9f46912532c5b8e4e4afc2fdb
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275681"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Ukázky Azure CLI pro nástroj pro vyrovnávání zatížení

Následující tabulka obsahuje odkazy na skripty Bash vytvořené pomocí Azure CLI.

| | |
|-|-|
| [Vyrovnávání zatížení provozu do virtuálních počítačů pro zajištění vysoké dostupnosti](./scripts/load-balancer-linux-cli-sample-nlb.md) | Vytvoří několik virtuálních počítačů v s vysokou dostupností a s vyrovnáváním zatížení konfigurace. |
| [Vyrovnávání zatížení virtuálních počítačů napříč zónami dostupnosti](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md) | Vytvoří tři virtuální počítače v různých zón dostupnosti v rámci oblasti a Load balanceru úrovně Standard se zónově redundantních front-endovou IP adresou. Tato konfigurace nástroje pro vyrovnávání zatížení pomáhá chránit vaše aplikace a data z málo pravděpodobným selháním nebo ztrátou celého datového centra. |
|[Vyrovnávání zatížení virtuálních počítačů v konkrétní zóně dostupnosti](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)|Vytvoří tři virtuální počítače, Load balanceru úrovně Standard s IP Adresou oblastmi front-endu, který pomáhá zarovnat cestu k datům a prostředkům v jedné zóně pro příslušnou oblast.|
| [Vyvažovat zatížení u více webů na virtuálních počítačích](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md) | Vytvoří dva virtuální počítače s několika konfigurací IP, připojený k skupině dostupnosti Azure, přístupné prostřednictvím služby Azure Load Balancer. |
| | |

