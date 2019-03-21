---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2019
ms.openlocfilehash: 200e2dfd2dd4f9aedd9256b307491a0b207ea124
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57964106"
---
Účtují se podle cenové úrovně prostředků Azure používá pro dotazy do kontejneru `<ApiKey>`.

Cognitive Services kontejnery nejsou licencované k používání bez připojení ke koncovému bodu fakturace pro monitorování míry využívání. Zákazníci musí umožňují používání kontejnerů ke komunikaci fakturační údaje s koncovým bodem fakturace za všech okolností. Cognitive Services kontejnery Neodesílat data zákazníků (třeba image nebo text, který je analyzován) společnosti Microsoft. 

### <a name="connecting-to-azure"></a>Připojení k Azure

Kontejner potřebuje fakturační hodnoty argumentů pro spuštění. Tyto hodnoty umožňují, aby kontejneru připojení ke koncovému bodu fakturace. Sestavy využití kontejnerů o každých 10 až 15 minut. Pokud kontejneru není v povolené časové okno k Azure připojit, kontejner bude dál poběží, ale není poskytovat dotazy, dokud fakturační koncový bod obnovení. Dojde k pokusu o připojení 10krát za stejný časový interval 10 až 15 minut. Pokud se nemůže připojit k účtování koncový bod v rámci 10 pokusech, kontejneru se zastaví. 

### <a name="billing-arguments"></a>Argumenty fakturace

Všechny tři z následujících možností musí být zadaný pomocí platné hodnoty, aby `docker run` příkaz ke spuštění kontejneru:

| Možnost | Popis |
|--------|-------------|
| `ApiKey` | Klíč rozhraní API služby Cognitive Services prostředku lze sledovat fakturační údaje.<br/>Hodnota této možnosti musí být nastavená na klíče rozhraní API pro zřízené prostředek určený v `Billing`. |
| `Billing` | Koncový bod služby Cognitive Services prostředek, který používá ke sledování fakturační údaje.<br/>Hodnota této možnosti musí nastavena na identifikátor URI se zřídil prostředek LUIS Azure koncový bod.|
| `Eula` | Označuje, že jste přijali licenci pro kontejner.<br/>Hodnota této možnosti musí být nastavená na `accept`. |


