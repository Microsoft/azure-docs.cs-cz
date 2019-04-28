---
author: WenJason
ms.service: load-balancer
ms.topic: include
origin.date: 11/09/2018
ms.date: 12/31/2018
ms.author: v-jay
ms.openlocfilehash: f0e575af51f952a80fe42102b033727713c75cf9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60398766"
---
## <a name="configuration-scenario"></a>Scénář konfigurace

V tomto scénáři vytváříme interní nástroj pro vyrovnávání zatížení ve virtuální síti, jak je znázorněno na následujícím obrázku:

![Scénář interního nástroje pro vyrovnávání zatížení](./media/load-balancer-get-started-ilb-scenario-include/figure1.png)

Konfigurace pro náš scénář je následující:

* Dva virtuální počítače pojmenované **DB1** a **DB2**
* Koncové body pro interní nástroj pro vyrovnávání zatížení
* Interní nástroj pro vyrovnávání zatížení
