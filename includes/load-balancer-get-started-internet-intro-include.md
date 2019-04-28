---
author: WenJason
ms.service: load-balancer
ms.topic: include
origin.date: 11/09/2018
ms.date: 12/31/2018
ms.author: v-jay
ms.openlocfilehash: 459c99d1b45af9c98cc1a6d0d7dd7a9a04c824ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60517016"
---
Azure Load Balancer je nástroj pro vyrovnávání zatížení úrovně 4 (TCP, UDP). Nástroj pro vyrovnávání zatížení poskytuje vysokou dostupnost díky distribuci příchozích přenosů mezi instance služeb, které jsou v pořádku, v cloudových službách nebo virtuálních počítačích v sadě nástroje pro vyrovnávání zatížení. Azure Load Balancer můžete také tyto služby prezentovat na více portech, více IP adresách nebo obojím.

Nástroj pro vyrovnávání zatížení můžete konfigurovat k těmto činnostem:

* Vyrovnávání zatížení příchozích přenosů z internetu do virtuálních počítačů. Nástroj pro vyrovnávání zatížení v tomto scénáři označujeme jako [internetový nástroj pro vyrovnávání zatížení](../articles/load-balancer/load-balancer-internet-overview.md).
* Vyrovnávání zatížení přenosů mezi virtuálními počítači ve virtuální síti, mezi virtuálními počítači v cloudových službách nebo mezi místními počítači a virtuálními počítači ve virtuální síti mezi různými místy. Nástroj pro vyrovnávání zatížení v tomto scénáři označujeme jako [interní nástroj pro vyrovnávání zatížení](../articles/load-balancer/load-balancer-internal-overview.md).
* Předávání externích přenosů konkrétní instanci virtuálního počítače