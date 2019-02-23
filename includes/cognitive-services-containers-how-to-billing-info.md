---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 002/08/2019
ms.openlocfilehash: 12c86ca71b7421678b68684cccca86411d604d61
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56741513"
---
Cognitive Services kontejnery nejsou licencované k používání bez připojení k Azure pro monitorování míry využívání. Zákazníci musí umožňují používání kontejnerů ke komunikaci fakturační údaje ke službě monitorování míry využití po celou dobu. Cognitive Services kontejnery Neodesílat data zákazníků (třeba image nebo text, který je analyzován) společnosti Microsoft. Sestavy využití kontejnerů o každých 10 až 15 minut.

`docker run` Používá následující argumenty pro účely fakturace:

| Možnost | Popis |
|--------|-------------|
| `ApiKey` | Klíč rozhraní API služby Cognitive Services prostředku lze sledovat fakturační údaje.<br/>Hodnota této možnosti musí být nastavená na klíče rozhraní API pro zřízené prostředek určený v `Billing`. |
| `Billing` | Koncový bod služby Cognitive Services prostředek, který používá ke sledování fakturační údaje.<br/>Hodnota této možnosti musí nastavena na identifikátor URI se zřídil prostředek LUIS Azure koncový bod.|
| `Eula` | Označuje, že jste přijali licenci pro kontejner.<br/>Hodnota této možnosti musí být nastavená na `accept`. |

> [!IMPORTANT]
> Všechny tři možnosti je nutné zadat platnou hodnotou nebo kontejneru se nespustí.
