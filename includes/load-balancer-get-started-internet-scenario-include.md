---
author: kumudD
ms.service: load-balancer
ms.topic: include
ms.date: 11/09/2018
ms.author: kumud
ms.openlocfilehash: bf3aee30460e052db23cf9090f13e2af3b22628b
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52269625"
---
V tomto scénáři budou provedeny následující úlohy:

* Vytvoření nástroje pro vyrovnávání zatížení, který přijímá síťový provoz na portu 80, a odeslání provozu s vyrovnaným zatížením do virtuálních počítačů „web1“ a „web2“
* Vytvoření pravidel NAT pro přístup ke vzdálené ploše / SSH pro virtuální počítače za nástrojem pro vyrovnávání zatížení
* Vytvoření testů stavu

![Scénář nástroje pro vyrovnávání zatížení](./media/load-balancer-get-started-internet-scenario-include/scenario-classic.png)
