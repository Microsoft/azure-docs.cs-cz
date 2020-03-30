---
title: Ukázky příkazového příkazového příkazu Azure pro vykladač zatížení
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225419"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Ukázky příkazového příkazového příkazu Azure pro vykladač zatížení

Následující tabulka obsahuje odkazy na skripty Bash vytvořené pomocí Azure CLI.

| | |
|-|-|
| [Provoz vyvažuje zatížení na virtuální chod pro vysokou dostupnost](./scripts/load-balancer-linux-cli-sample-nlb.md) | Vytvoří několik virtuálních počítačů ve vysoce dostupné konfiguraci s vyrovnáváním zatížení. |
| [Vyrovnávání zatížení virtuálních počítačů napříč zónami dostupnosti](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md) | Vytvoří tři virtuální virtuální ženy v různých zónách dostupnosti v rámci oblasti a standardní vyrovnávání zatížení s zónovou redundantní ip adresou front-endu. Tato konfigurace nástroje pro vyrovnávání zatížení pomáhá chránit vaše aplikace a data před nepravděpodobným selháním nebo ztrátou celého datového centra. |
|[Vyrovnávání zatížení virtuálních počítačů v konkrétní zóně dostupnosti](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)|Vytvoří tři virtuální počítače, standardní vyrovnávání zatížení s zónovou front-end IP, která pomáhá zarovnat cestu dat a prostředky v jedné zóně pro danou oblast.|
| [Vyrovnávání zatížení více webů na virtuálních počítačích](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md) | Vytvoří dva virtuální počítače s více konfiguracemi IP adres, které jsou připojeny k azure dostupnosti, přístupné prostřednictvím nástroje pro vyrovnávání zatížení Azure. |
| | |

