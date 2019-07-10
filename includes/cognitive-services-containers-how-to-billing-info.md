---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a24300958c27daaaf49cc3045a5e99d77c938ab7
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704249"
---
Účtují se podle cenové úrovně prostředků Azure, který se používá pro dotazy do kontejneru `<ApiKey>`.

Kontejnery služby Azure Cognitive Services nejsou licencované k používání bez připojení ke koncovému bodu fakturace pro monitorování míry využívání. Je nutné povolit kontejnery pro komunikaci fakturační údaje s koncovým bodem fakturace za všech okolností. Kontejnery služby cognitive Services není zákaznická data, jako je image nebo text, který se právě analyzuje, odeslat společnosti Microsoft. 

### <a name="connect-to-azure"></a>Připojení k Azure

Kontejner potřebuje fakturační hodnoty argumentů pro spuštění. Tyto hodnoty umožňují, aby kontejneru pro připojení ke koncovému bodu fakturace. Sestavy využití kontejnerů o každých 10 až 15 minut. Pokud kontejneru nemá připojení k Azure v rámci povolený časový interval, kontejner zůstane spuštěný ale bariéru dotazy, dokud fakturační koncový bod obnovení. Dojde k pokusu o připojení 10krát za stejný časový interval 10 až 15 minut. Pokud se nemůže připojit k účtování koncový bod v rámci 10 pokusí, kontejneru se zastaví. 

### <a name="billing-arguments"></a>Argumenty fakturace

Pro `docker run` příkaz ke spuštění kontejneru, všechny tři z následujících možností musí být zadaný pomocí platné hodnoty:

| Možnost | Popis |
|--------|-------------|
| `ApiKey` | Klíč rozhraní API služeb Cognitive Services prostředku, který se používá ke sledování fakturační údaje.<br/>Hodnota této možnosti musí být nastavená na klíče rozhraní API pro se zřídil prostředek, který je zadán v `Billing`. |
| `Billing` | Koncový bod prostředku služeb Cognitive Services, který se používá ke sledování fakturační údaje.<br/>Hodnota této možnosti musíte ke koncovému bodu identifikátor URI se zřídil prostředek Azure.|
| `Eula` | Označuje, že jste přijali licenční pro kontejner.<br/>Hodnota této možnosti musí být nastavená na **přijmout**. |


