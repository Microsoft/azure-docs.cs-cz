---
author: kumudD
ms.service: load-balancer
ms.topic: include
ms.date: 11/09/2018
ms.author: kumud
ms.openlocfilehash: bf3aee30460e052db23cf9090f13e2af3b22628b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66122164"
---
V tomto scénáři budou provedeny následující úlohy:

* Vytvoření nástroje pro vyrovnávání zatížení, který přijímá síťový provoz na portu 80, a odeslání provozu s vyrovnaným zatížením do virtuálních počítačů „web1“ a „web2“
* Vytvoření pravidel NAT pro přístup ke vzdálené ploše / SSH pro virtuální počítače za nástrojem pro vyrovnávání zatížení
* Vytvoření testů stavu

![Scénář nástroje pro vyrovnávání zatížení](./media/load-balancer-get-started-internet-scenario-include/scenario-classic.png)
