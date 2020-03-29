---
title: Azure Event Grid – průvodce odstraňováním potíží
description: Tento článek obsahuje seznam kódů chyb, chybových zpráv, popisů a doporučených akcí.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 1ab9aeac0bde21e229fdb57b7ad02d5d48471551
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645068"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Poradce při potížích s chybami služby Azure Event Grid
Tento průvodce odstraňováním potíží poskytuje seznam kódů chyb služby Azure Event Grid, chybových zpráv, jejich popisů a doporučených akcí, které byste měli provést, když se zobrazí tyto chyby. 

## <a name="error-code-400"></a>Kód chyby: 400
| Kód chyby | Chybová zpráva | Popis | Doporučení |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.BadRequest<br/>400 | Název tématu musí mít délku 3 až 50 znaků. | Délka názvu vlastního tématu by měla být dlouhá 3 až 50 znaků. V názvu tématu jsou povolena pouze alfanumerická písmena, číslice a znak '-'. Název by také neměl začínat následujícími vyhrazenými slovy: <ul><li>Microsoft</li><li>EventGrid</li><li>Systém</li></ul> | Zvolte jiný název tématu, který splňuje požadavky na název tématu. |
| HttpStatusCode.BadRequest<br/>400 | Název domény musí mít délku 3 až 50 znaků. | Délka názvu domény by měla být dlouhá 3 až 50 znaků. V názvu tématu jsou povolena pouze alfanumerická písmena, číslice a znak '-'. Název by také neměl začínat následujícími vyhrazenými slovy:<ul><li>Microsoft</li><li>EventGrid</li><li>Systém</li> | Zvolte jiný název domény, který splňuje požadavky na název domény. |
| HttpStatusCode.BadRequest<br/>400 | Neplatná doba vypršení platnosti. | Doba vypršení platnosti předplatného události určuje, kdy bude předplatné události vyřazeno. Tato hodnota by měla být platná DateTime hodnotu v budoucnu.| Ujistěte se, že čas vypršení platnosti odběru událostí v platném formátu DateTime a je nastavena na budoucnost. |

## <a name="error-code-409"></a>Kód chyby: 409
| Kód chyby | Chybová zpráva | Popis | Doporučená akce |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.Conflict <br/>409 | Téma se zadaným názvem již existuje. Zvolte jiný název tématu.   | Vlastní název tématu by měl být jedinečný v jedné oblasti Azure, aby byla zajištěna správná operace publikování. Stejný název lze použít v různých oblastech Azure. | Zvolte jiný název tématu. |
| HttpStatusCode.Conflict <br/> 409 | Doména se zadaným již existuje. Zvolte jiný název domény. | Název domény by měl být jedinečný v jedné oblasti Azure, aby byla zajištěna správná operace publikování. Stejný název lze použít v různých oblastech Azure. | Zvolte jiný název domény. |
| HttpStatusCode.Conflict<br/>409 | Bylo dosaženo limitu kvóty. Další informace o těchto omezeních najdete v [tématu omezení služby Azure Event Grid](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits).  | Každé předplatné Azure má limit na počet prostředků Azure Event Grid, které může používat. Některé nebo všechny tyto kvóty byly překročeny a nebylo možné vytvořit žádné další zdroje. |    Zkontrolujte aktuální využití prostředků a odstraňte všechny, které nejsou potřeba. Pokud stále potřebujete zvýšit kvótu, pošlete [aeg@microsoft.com](mailto:aeg@microsoft.com) e-mail s přesným počtem potřebných zdrojů. |


## <a name="next-steps"></a>Další kroky
Pokud potřebujete další pomoc, zveřejněte svůj problém ve [fóru Přetečení zásobníku](https://stackoverflow.com/questions/tagged/azure-eventgrid) nebo otevřete [lístek podpory](https://azure.microsoft.com/support/options/). 
