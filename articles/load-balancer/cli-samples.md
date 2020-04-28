---
title: Ukázky v Azure CLI pro Load Balancer
titleSuffix: Azure Load Balancer
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
ms.openlocfilehash: f35007bc3c47aeec9bcdd8a418983b95f6f20ad6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74225419"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Ukázky v Azure CLI pro Load Balancer

Následující tabulka obsahuje odkazy na skripty Bash vytvořené pomocí Azure CLI.

| | |
|-|-|
| [Vyrovnávání zatížení provozu do virtuálních počítačů pro zajištění vysoké dostupnosti](./scripts/load-balancer-linux-cli-sample-nlb.md) | Vytvoří několik virtuálních počítačů s vysokou dostupností a konfigurací s vyrovnáváním zatížení. |
| [Vyrovnávání zatížení virtuálních počítačů napříč zónami dostupnosti](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md) | Vytvoří tři virtuální počítače v různých zónách dostupnosti v rámci oblasti a Standard Load Balancer s IP adresou redundantní zóny. Tato konfigurace nástroje pro vyrovnávání zatížení pomáhá chránit vaše aplikace a data před nepravděpodobným selháním nebo ztrátou celého datového centra. |
|[Vyrovnávání zatížení virtuálních počítačů v konkrétní zóně dostupnosti](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)|Vytvoří tři virtuální počítače, Standard Load Balancer s oblastí front-endu IP, která pomáhá zarovnat cestu k datům a prostředky v jedné zóně pro danou oblast.|
| [Vyrovnávání zatížení více webů na virtuálních počítačích](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md) | Vytvoří dva virtuální počítače s několika konfiguracemi IP adres, které jsou připojené k sadě dostupnosti Azure přístupné prostřednictvím Azure Load Balancer. |
| | |

