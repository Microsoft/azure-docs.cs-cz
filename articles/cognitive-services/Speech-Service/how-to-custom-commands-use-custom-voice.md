---
title: 'Postupy: použití vlastních příkazů s vlastní službou Voice-Speech'
titleSuffix: Azure Cognitive Services
description: V tomto článku určíte výstupní hlas vlastní aplikace příkazů.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: sausin
ms.openlocfilehash: b1e488653f210089ee5ed6757fb2f3d1e4fb0fe4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87294119"
---
# <a name="use-custom-commands-with-custom-voice"></a>Použití aplikace Vlastní příkazy se službou Vlastní hlas

V tomto článku se dozvíte, jak vybrat vlastní výstupní hlas pro vlastní aplikaci příkazů.

## <a name="select-a-custom-voice"></a>Vyberte vlastní hlas.

1. V aplikaci Custom Commands (vlastní příkazy) v levém podokně vyberte **Nastavení** .
1. V prostředním podokně vyberte **vlastní hlas** .
1. V tabulce vyberte požadovaný vlastní nebo veřejný hlas.
1. Vyberte **Uložit**.

> [!div class="mx-imgBorder"]
> ![Ukázky vět s parametry](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - Pro **veřejné hlasy**jsou **typy neuronové** dostupné jenom pro konkrétní oblasti. Pokud chcete ověřit dostupnost, přečtěte si téma [standardní a neuronové hlasy podle oblasti nebo koncového bodu](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).
> - **Vlastní hlasy**si můžete vytvořit na stránce vlastní hlasového projektu. Viz Začínáme [s vlastním hlasem](./how-to-custom-voice.md).

Aplikace teď bude ve vybraném hlasu reagovat místo na výchozí hlas.
