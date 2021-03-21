---
author: aahill
ms.author: aahi
ms.date: 03/02/2021
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: d61813e723992f4381c5ea82121da8bbb70016dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102032924"
---
Dotazy na kontejner se účtují v cenové úrovni prostředku Azure, který se používá pro `ApiKey` .

U kontejnerů Azure Cognitive Services není licencováno běžet bez připojení ke koncovému bodu měření/fakturace. Kontejnerům musíte povolit, aby informace o fakturaci komunikovaly s koncovým bodem fakturace. Kontejnery Cognitive Services neodesílají zákaznická data, jako je například analyzovaný obrázek nebo text, do společnosti Microsoft.

### <a name="connect-to-azure"></a>Připojení k Azure

Kontejner potřebuje ke spuštění hodnoty argumentu fakturace. Tyto hodnoty umožní kontejneru připojení ke koncovému bodu fakturace. Kontejner hlásí využití každých 10 až 15 minut. Pokud se kontejner nepřipojí k Azure v rámci povoleného časového okna, kontejner pokračuje v běhu, ale neobsluhuje dotazy, dokud se neobnoví koncový bod fakturace. Pokus o připojení se desetkrát vyzkouší v intervalu 10 až 15 minut. Pokud se nemůže připojit ke koncovému bodu fakturace v rámci 10 pokusů, kontejner přestane obsluhovat požadavky. Příklad informací odesílaných společnosti Microsoft pro účely fakturace najdete v tématu [Cognitive Services kontejneru – Nejčastější dotazy](../articles/cognitive-services/containers/container-faq.yml#how-does-billing-work) .

### <a name="billing-arguments"></a>Argumenty fakturace

Příkaz spustí kontejner, pokud jsou k dispozici všechny tři z následujících možností s platnými hodnotami: <a href="https://docs.docker.com/engine/reference/commandline/run/" target="_blank"> `docker run` <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

| Možnost | Popis |
|--------|-------------|
| `ApiKey` | Klíč rozhraní API prostředku Cognitive Services, který se používá ke sledování fakturačních informací.<br/>Hodnota této možnosti musí být nastavená na klíč rozhraní API pro zřízený prostředek, který je zadaný v `Billing` . |
| `Billing` | Koncový bod prostředku Cognitive Services, který se používá ke sledování fakturačních informací.<br/>Hodnota této možnosti musí být nastavená na identifikátor URI koncového bodu zřízeného prostředku Azure.|
| `Eula` | Označuje, že jste přijali licenci pro kontejner.<br/>Hodnota této možnosti musí být nastavena na hodnotu **přijmout**. |
