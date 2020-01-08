---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a24300958c27daaaf49cc3045a5e99d77c938ab7
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "67704249"
---
Dotazy na kontejner se účtují v cenové úrovni prostředku Azure, který se používá pro `<ApiKey>`.

U kontejnerů Azure Cognitive Services není licencováno běžet bez připojení ke koncovému bodu fakturace pro měření. Kontejnerům musíte povolit, aby informace o fakturaci komunikovaly s koncovým bodem fakturace. Kontejnery Cognitive Services neodesílají zákaznická data, jako je například analyzovaný obrázek nebo text, do společnosti Microsoft. 

### <a name="connect-to-azure"></a>Připojení k Azure

Kontejner potřebuje ke spuštění hodnoty argumentu fakturace. Tyto hodnoty umožní kontejneru připojení ke koncovému bodu fakturace. Kontejner hlásí využití každých 10 až 15 minut. Pokud se kontejner nepřipojí k Azure v rámci povoleného časového okna, kontejner pokračuje v běhu, ale neobsluhuje dotazy, dokud se neobnoví koncový bod fakturace. Pokus o připojení se desetkrát vyzkouší v intervalu 10 až 15 minut. Pokud se nemůže připojit ke koncovému bodu fakturace v rámci 10 pokusů, kontejner přestane běžet. 

### <a name="billing-arguments"></a>Argumenty fakturace

Aby příkaz `docker run` spustil kontejner, musí být všechny tři z následujících možností zadány s platnými hodnotami:

| Možnost | Popis |
|--------|-------------|
| `ApiKey` | Klíč rozhraní API prostředku Cognitive Services, který se používá ke sledování fakturačních informací.<br/>Hodnota této možnosti musí být nastavená na klíč rozhraní API pro zřízený prostředek, který je zadaný v `Billing`. |
| `Billing` | Koncový bod prostředku Cognitive Services, který se používá ke sledování fakturačních informací.<br/>Hodnota této možnosti musí být nastavená na identifikátor URI koncového bodu zřízeného prostředku Azure.|
| `Eula` | Označuje, že jste přijali licenci pro kontejner.<br/>Hodnota této možnosti musí být nastavena na hodnotu **přijmout**. |


