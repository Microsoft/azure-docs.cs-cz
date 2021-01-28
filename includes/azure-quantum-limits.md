---
title: zahrnout soubor
description: zahrnout soubor
author: danielstocker
ms.service: azure-quantum
ms.topic: include
ms.date: 01/08/2021
ms.author: dasto
ms.openlocfilehash: 2106a48a583f120f8b4dde4eb32a30f1a1b1d85b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947503"
---
### <a name="provider-limits--quota"></a>Omezení poskytovatele & kvóty

Služba Azure Service podporuje jak prvního, tak i poskytovatele služeb třetích stran. Poskytovatelé třetích stran vlastní limity a kvóty. Uživatelé můžou při konfiguraci poskytovatelů třetích stran v okně poskytovatele Zobrazit nabídky a omezení v Azure Portal. 

Publikované limity kvót pro poskytovatele řešení optimalizace First stran od Microsoftu najdete níže. 

#### <a name="learn--develop-sku"></a>Informace & vývoj SKU

| Prostředek | Omezení |
| --- | --- |
| Souběžné úlohy založené na procesoru | až 5 souběžných úloh |
| Souběžné úlohy založené na FPGA | až 2 souběžných úloh |
| Hodiny Řešitele založené na procesoru | 20 hodin za měsíc  |
| Hodiny Řešitele založené na FPGA | 1 hodina za měsíc  |

Pokud používáte & pro vývoj SKU, **nemůžete** požádat o zvýšení limitů kvót. Místo toho byste měli přepnout na výkon ve skladové SKU škálování.

#### <a name="performance-at-scale-sku"></a>Výkon v SKU škálování

| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Souběžné úlohy založené na procesoru | až 100 souběžných úloh | stejné jako výchozí omezení |
| Souběžné úlohy založené na FPGA | až 10 souběžných úloh | stejné jako výchozí omezení |
| Hodiny Řešitele | 1 000 hodin za měsíc  | až 50 000 hodin za měsíc |

Pokud potřebujete požádat o zvýšení limitu, obraťte se na podporu Azure. 

Další informace najdete na [stránce s cenami za Azure](https://aka.ms/AQ/Pricing).
Informace o nabídkách třetích stran najdete na stránce relevantního poskytovatele v Azure Portal.
