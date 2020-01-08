---
title: Azure Event Grid – Průvodce odstraňováním potíží
description: Tento článek uvádí seznam kódů chyb, chybové zprávy, popisy a doporučené akce.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 1ab9aeac0bde21e229fdb57b7ad02d5d48471551
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645068"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Řešení chyb Azure Event Grid
Tato příručka pro řešení potíží poskytuje seznam chybových kódů Azure Event Grid, chybové zprávy, jejich popisy a doporučené akce, které byste měli provést při obdržení těchto chyb. 

## <a name="error-code-400"></a>Kód chyby: 400
| Kód chyby | Chybová zpráva | Popis | Doporučení |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. důvodu chybného požadavku<br/>400 | Název tématu musí mít délku 3 až 50 znaků. | Název vlastního tématu by měl mít délku 3 až 50 znaků. V názvu tématu jsou povoleny pouze alfanumerické písmena, číslice a znak-. Název by měl také začínat následujícími rezervovanými slovy: <ul><li>Microsoft</li><li>EventGrid</li><li>Systém</li></ul> | Vyberte jiný název tématu, které dodržuje požadavky na název tématu. |
| HttpStatusCode. důvodu chybného požadavku<br/>400 | Název domény musí mít délku 3 až 50 znaků. | Délka názvu domény by měla být 3 až 50 znaků. V názvu tématu jsou povoleny pouze alfanumerické písmena, číslice a znak-. Název by měl také začínat následujícími rezervovanými slovy:<ul><li>Microsoft</li><li>EventGrid</li><li>Systém</li> | Vyberte jiný název domény, který splňuje požadavky na název domény. |
| HttpStatusCode. důvodu chybného požadavku<br/>400 | Neplatný čas vypršení platnosti. | Čas vypršení platnosti odběru události Určuje, kdy bude odběr události vyřazení. Tato hodnota by měla být v budoucnu platná hodnota DateTime.| Ujistěte se, že doba vypršení platnosti předplatného události v platném formátu data a času je nastavená na budoucnost. |

## <a name="error-code-409"></a>Kód chyby: 409
| Kód chyby | Chybová zpráva | Popis | Doporučená akce |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. konflikt <br/>409 | Téma se zadaným názvem již existuje. Zvolte jiný název tématu.   | Název vlastního tématu by měl být jedinečný v jedné oblasti Azure, aby se zajistila správná operace publikování. Stejný název se dá použít v různých oblastech Azure. | Vyberte jiný název tématu. |
| HttpStatusCode. konflikt <br/> 409 | Doména se zadaným parametrem již existuje. Vyberte jiný název domény. | Název domény by měl být jedinečný v jedné oblasti Azure, aby se zajistila správná operace publikování. Stejný název se dá použít v různých oblastech Azure. | Vyberte jiný název domény. |
| HttpStatusCode. konflikt<br/>409 | Dosáhlo se limitu kvóty. Další informace o těchto omezeních najdete v tématu [omezení Azure Event Grid](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits).  | Každé předplatné Azure má omezení počtu Azure Event Grid prostředků, které může použít. Některá nebo všechna tato kvóta byla překročena a nebylo možné vytvořit žádné další prostředky. |    Projděte si aktuální využití prostředků a odstraňte všechny, které nepotřebujete. Pokud stále potřebujete zvýšit vaši kvótu, pošlete e-mail [aeg@microsoft.com](mailto:aeg@microsoft.com) s přesným počtem potřebných prostředků. |


## <a name="next-steps"></a>Další kroky
Pokud potřebujete další pomoc, vystavte svůj problém ve [fóru Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) nebo otevřete [lístek podpory](https://azure.microsoft.com/support/options/). 
