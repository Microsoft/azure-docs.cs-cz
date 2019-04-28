---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2019
ms.openlocfilehash: e92d1c65d9601c23e7e785f07e2de3e43ea6612b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598914"
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
| `Billing` | Koncový bod služby Cognitive Services prostředek, který používá ke sledování fakturační údaje.<br/>Hodnota této možnosti musíte ke koncovému bodu identifikátor URI se zřídil prostředek Azure.|
| `Eula` | Označuje, že jste přijali licenci pro kontejner.<br/>Hodnota této možnosti musí být nastavená na `accept`. |


