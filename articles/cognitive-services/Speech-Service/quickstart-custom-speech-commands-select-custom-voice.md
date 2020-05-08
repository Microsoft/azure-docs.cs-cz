---
title: 'Rychlý Start: použití vlastních příkazů s vlastním hlasem (ve verzi Preview) – služba Speech'
titleSuffix: Azure Cognitive Services
description: V tomto článku určíte výstupní hlas vlastní aplikace příkazů.
services: cognitive-services
author: anhoang
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: anhoang
ms.openlocfilehash: c2b9b4d51e89975d988ed94bf85695bd8a1cc770
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872463"
---
# <a name="quickstart-use-custom-commands-with-custom-voice-preview"></a>Rychlý Start: použití vlastních příkazů s vlastním hlasem (Preview)

V [předchozím článku](./quickstart-custom-speech-commands-create-parameters.md)jsme vytvořili nový projekt Custom Commands, který bude reagovat na příkazy s parametry.

V tomto článku vybereme vlastní výstupní hlas pro aplikaci, kterou jsme vytvořili.

## <a name="select-a-custom-voice"></a>Vyberte vlastní hlas.

1. Otevřete projekt, který [jsme vytvořili dříve](./quickstart-custom-speech-commands-create-parameters.md) .
1. V levém podokně vyberte **Nastavení** .
1. V prostředním podokně vyberte **vlastní hlas** .
1. Výběr požadovaného vlastního nebo veřejného hlasu z tabulky
1. Vybrat **Uložit**

> [!div class="mx-imgBorder"]
> ![Ukázky vět s parametry](media/custom-speech-commands/select-custom-voice.png)

> [!NOTE]
> - Pro **veřejné hlasy**jsou **typy neuronové** dostupné jenom pro konkrétní oblasti. Pokud chcete ověřit dostupnost, přečtěte si téma [standardní a neuronové hlasy podle oblasti nebo koncového bodu](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).
> - **Vlastní hlasy**si můžete vytvořit na stránce vlastní hlasového projektu. Viz Začínáme [s vlastním hlasem](./how-to-custom-voice.md).

Aplikace teď bude ve vybraném hlasu reagovat místo na výchozí hlas.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Rychlý Start: připojení k vlastnímu příkazu aplikace pomocí sady Speech SDK (Preview)](./quickstart-custom-speech-commands-speech-sdk.md)

