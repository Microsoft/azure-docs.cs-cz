---
author: IEvangelist
ms.author: dapine
ms.date: 02/19/2020
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 2ac93f5aba722eea78267a512999a5581a887b99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77474186"
---
Dotazy na kontejner se účtují na cenové úrovni prostředku Azure, `ApiKey`který se používá pro .

Kontejnery Azure Cognitive Services nejsou licencovány ke spuštění, aniž by byly připojeny ke koncovému bodu měření a fakturace. Je nutné povolit kontejnery ke komunikaci fakturační informace s koncovým bodem fakturace za všech okolností. Kontejnery služeb Cognitive Services neodesílají společnosti Microsoft data zákazníků, například obrázek nebo text, který se analyzuje.

### <a name="connect-to-azure"></a>Připojení k Azure

Kontejner potřebuje hodnoty argumentů fakturace ke spuštění. Tyto hodnoty umožňují kontejneru pro připojení ke koncovému bodu fakturace. Kontejner hlásí využití přibližně každých 10 až 15 minut. Pokud se kontejner nepřipojí k Azure v povoleném časovém okně, kontejner se nadále spouštějí, ale neobsluhuje dotazy, dokud se nezmění koncový bod fakturace. O připojení se pokusí 10krát ve stejném časovém intervalu 10 až 15 minut. Pokud se nemůže připojit ke koncovému bodu fakturace v rámci 10 pokusů, kontejner přestane obsluhovat požadavky.

### <a name="billing-arguments"></a>Argumenty fakturace

<a href="https://docs.docker.com/engine/reference/commandline/run/" target="_blank"> `docker run` Příkaz <span class="docon docon-navigate-external x-hidden-focus"></span> </a> spustí kontejner, pokud jsou k dispozici všechny tři následující možnosti s platnými hodnotami:

| Možnost | Popis |
|--------|-------------|
| `ApiKey` | Klíč rozhraní API prostředku služeb Cognitive Services, který se používá ke sledování fakturačních údajů.<br/>Hodnota této možnosti musí být nastavena na klíč rozhraní API `Billing`pro zřízený prostředek, který je zadán v aplikaci . |
| `Billing` | Koncový bod prostředku služeb Cognitive Services, který se používá ke sledování fakturačních údajů.<br/>Hodnota této možnosti musí být nastavena na identifikátor URI koncového bodu zřízeného prostředku Azure.|
| `Eula` | Označuje, že jste přijali licenci pro kontejner.<br/>Hodnota této možnosti musí být nastavena tak, aby **byla akceptována**. |
