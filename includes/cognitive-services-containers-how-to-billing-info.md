---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2019
ms.openlocfilehash: 05961f8dd2788179a15e6bfe094484cf4770067b
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65885033"
---
Účtují se podle cenové úrovně prostředků Azure používá pro dotazy do kontejneru `<ApiKey>`.

Kontejnery služby Azure Cognitive Services nejsou licencované k používání bez připojení ke koncovému bodu fakturace pro monitorování míry využívání. Je nutné povolit kontejnery pro komunikaci fakturační údaje s koncovým bodem fakturace za všech okolností. Kontejnery služby cognitive Services není zákaznická data, jako je image nebo text, který se právě analyzuje, odeslat společnosti Microsoft. 

### <a name="connect-to-azure"></a>Připojení k Azure

Kontejner potřebuje fakturační hodnoty argumentů pro spuštění. Tyto hodnoty umožňují, aby kontejneru pro připojení ke koncovému bodu fakturace. Sestavy využití kontejnerů o každých 10 až 15 minut. Pokud kontejneru nemá připojení k Azure v rámci povolený časový interval, kontejner zůstane spuštěný ale bariéru dotazy, dokud fakturační koncový bod obnovení. Dojde k pokusu o připojení 10krát za stejný časový interval 10 až 15 minut. Pokud se nemůže připojit k účtování koncový bod v rámci 10 pokusí, kontejneru se zastaví. 

### <a name="billing-arguments"></a>Argumenty fakturace

Všechny tři z následujících možností musí být zadaný pomocí platné hodnoty, aby `docker run` příkaz ke spuštění kontejneru.

| Možnost | Popis |
|--------|-------------|
| `ApiKey` | Klíč rozhraní API služeb Cognitive Services prostředku lze sledovat fakturační údaje.<br/>Hodnota této možnosti musí být nastavená na klíče rozhraní API pro zřízené prostředek určený v `Billing`. |
| `Billing` | Koncový bod prostředku služeb Cognitive Services lze sledovat fakturační údaje.<br/>Hodnota této možnosti musíte ke koncovému bodu identifikátor URI se zřídil prostředek Azure.|
| `Eula` | Označuje, že jste přijali licenční pro kontejner.<br/>Hodnota této možnosti musí být nastavená na `accept`. |


