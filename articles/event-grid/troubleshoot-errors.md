---
title: Azure Event Grid – Průvodce odstraňováním potíží
description: Tento článek uvádí seznam kódů chyb, chybové zprávy, popisy a doporučené akce.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 79533918ccc6995f459b39f058de9e01091c0958
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94592987"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Řešení chyb Azure Event Grid
Tato příručka pro řešení potíží poskytuje následující informace: 

- Kódy chyb Azure Event Grid
- Chybové zprávy
- Popisy chyb
- Doporučené akce, které byste měli provést po obdržení těchto chyb. 

## <a name="error-code-400"></a>Kód chyby: 400
| Kód chyby | Chybová zpráva | Description | Doporučení |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. důvodu chybného požadavku<br/>400 | Název tématu musí mít délku 3 až 50 znaků. | Název vlastního tématu by měl mít délku 3 až 50 znaků. V názvu tématu jsou povoleny pouze alfanumerické písmena, číslice a znak-. Název by měl také začínat následujícími rezervovanými slovy: <ul><li>Microsoft</li><li>EventGrid</li><li>Souborů</li></ul> | Vyberte jiný název tématu, které dodržuje požadavky na název tématu. |
| HttpStatusCode. důvodu chybného požadavku<br/>400 | Název domény musí mít délku 3 až 50 znaků. | Délka názvu domény by měla být 3 až 50 znaků. V názvu domény jsou povoleny pouze alfanumerické písmena, číslice a znaky-. Název by měl také začínat následujícími rezervovanými slovy:<ul><li>Microsoft</li><li>EventGrid</li><li>Souborů</li> | Vyberte jiný název domény, který splňuje požadavky na název domény. |
| HttpStatusCode. důvodu chybného požadavku<br/>400 | Neplatný čas vypršení platnosti. | Čas vypršení platnosti odběru události Určuje, kdy bude odběr události vyřazení. Tato hodnota by měla být v budoucnu platná hodnota DateTime.| Ujistěte se, že doba vypršení platnosti předplatného události v platném formátu data a času je nastavená na budoucnost. |

## <a name="error-code-409"></a>Kód chyby: 409
| Kód chyby | Chybová zpráva | Description | Doporučená akce |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. konflikt <br/>409 | Téma se zadaným názvem již existuje. Vyberte jiný název tématu.   | Název vlastního tématu by měl být jedinečný v jedné oblasti Azure, aby se zajistila správná operace publikování. Stejný název se dá použít v různých oblastech Azure. | Vyberte jiný název tématu. |
| HttpStatusCode. konflikt <br/> 409 | Doména se zadaným parametrem již existuje. Vyberte jiný název domény. | Název domény by měl být jedinečný v jedné oblasti Azure, aby se zajistila správná operace publikování. Stejný název se dá použít v různých oblastech Azure. | Vyberte jiný název domény. |
| HttpStatusCode. konflikt<br/>409 | Dosáhlo se limitu kvóty. Další informace o těchto omezeních najdete v tématu [omezení Azure Event Grid](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits).  | Každé předplatné Azure má omezení počtu Azure Event Grid prostředků, které může použít. Některá nebo všechna tato kvóta byla překročena a nebylo možné vytvořit žádné další prostředky. |    Projděte si aktuální využití prostředků a odstraňte všechny, které nepotřebujete. Pokud stále potřebujete zvýšit vaši kvótu, pošlete e-mail na [aeg@microsoft.com](mailto:aeg@microsoft.com) přesný počet potřebných prostředků. |

## <a name="error-code-403"></a>Kód chyby: 403

| Kód chyby | Chybová zpráva | Description | Doporučená akce |
| ---------- | ------------- | ----------- | ------------------ |
| HttpStatusCode. Forbidden <br/>403 | Publikování do {tematický/Domain} klientem {IpAddress} bylo zamítnuto z důvodu pravidel filtrování IpAddress. | V tématu nebo doméně jsou nakonfigurovaná pravidla brány firewall protokolu IP a přístup je omezený jenom na nakonfigurované IP adresy. | Přidání IP adresy do pravidel brány firewall protokolu IP najdete v tématu [Konfigurace brány firewall protokolu IP](configure-firewall.md) . |
| HttpStatusCode. Forbidden <br/> 403 | Publikování do aplikace {tematický/Domain} klientem se zamítlo, protože požadavek pochází z privátního koncového bodu a nenašel se žádný souhlas privátního koncového bodu pro daný prostředek. | V tématu nebo doméně jsou privátní koncové body a požadavek na publikování pochází z privátního koncového bodu, který není nakonfigurovaný ani schválen. | Konfigurace privátního koncového bodu pro téma nebo doménu. [Konfigurace privátních koncových bodů](configure-private-endpoints.md) |

Ověřte také, jestli je Webhook za službou Azure Application Gateway nebo bránou firewall webových aplikací. Pokud je, zakažte následující pravidla brány firewall a znovu proveďte příkaz HTTP POST:

- 920300 (žádost neobsahuje hlavičku Accept)
- 942430 (omezené zjištění anomálií znaků SQL (args): počet speciálních znaků překročen (12))
- 920230 (bylo zjištěno více kódování adresy URL)
- 942130 (útok injektáže SQL: byl zjištěn SQL tautology.)
- 931130 (možný útok na vzdálené zahrnutí souborů (RFI) = odkaz mimo doménu/odkaz)



## <a name="next-steps"></a>Další kroky
Pokud potřebujete další pomoc, vystavte svůj problém ve [fóru Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) nebo otevřete [lístek podpory](https://azure.microsoft.com/support/options/). 
