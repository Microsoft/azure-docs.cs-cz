---
author: WenJason
ms.service: load-balancer
ms.topic: include
origin.date: 11/09/2018
ms.date: 12/31/2018
ms.author: v-jay
ms.openlocfilehash: bf3aee30460e052db23cf9090f13e2af3b22628b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60517007"
---
V tomto scénáři budou provedeny následující úlohy:

* Vytvoření nástroje pro vyrovnávání zatížení, který přijímá síťový provoz na portu 80, a odeslání provozu s vyrovnaným zatížením do virtuálních počítačů „web1“ a „web2“
* Vytvoření pravidel NAT pro přístup ke vzdálené ploše / SSH pro virtuální počítače za nástrojem pro vyrovnávání zatížení
* Vytvoření testů stavu

![Scénář nástroje pro vyrovnávání zatížení](./media/load-balancer-get-started-internet-scenario-include/scenario-classic.png)