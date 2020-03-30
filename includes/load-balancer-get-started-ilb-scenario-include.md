---
author: kumudD
ms.service: load-balancer
ms.topic: include
ms.date: 11/09/2018
ms.author: kumud
ms.openlocfilehash: f0e575af51f952a80fe42102b033727713c75cf9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175432"
---
## <a name="configuration-scenario"></a>Scénář konfigurace

V tomto scénáři vytváříme interní nástroj pro vyrovnávání zatížení ve virtuální síti, jak je znázorněno na následujícím obrázku:

![Scénář interního nástroje pro vyrovnávání zatížení](./media/load-balancer-get-started-ilb-scenario-include/figure1.png)

Konfigurace pro náš scénář je následující:

* Dva virtuální počítače pojmenované **DB1** a **DB2**
* Koncové body pro interní nástroj pro vyrovnávání zatížení
* Interní nástroj pro vyrovnávání zatížení
