---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.date: 12/29/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: aahi
ms.reviewer: roy-har
ms.openlocfilehash: 7aa2fba6ef551a745ccaf5b00f36021b9d8680ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "97820668"
---
1. Vyberte možnost [pizza-app-for-luis-v6.jszapnuto](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-app-for-luis-v6.json) a zobrazte stránku GitHub pro daný `pizza-app-for-luis.json` soubor.
1. Klikněte pravým tlačítkem nebo dlouho klepněte na tlačítko **nezpracované** a vyberte **Uložit odkaz jako** a uložte `pizza-app-for-luis.json` ho do svého počítače.
1. Přihlaste se k [portálu Luis](https://www.luis.ai).
1. Vyberte [Moje aplikace](https://www.luis.ai/applications).
1. Na stránce **Moje aplikace** vyberte **+ Nová aplikace pro konverzaci**.
1. Vyberte **importovat jako JSON**.
1. V dialogovém okně **importovat novou aplikaci** vyberte tlačítko **Vybrat soubor** .
1. Vyberte `pizza-app-for-luis.json` soubor, který jste stáhli, a pak vyberte **otevřít**.
1. V poli **název** dialogového okna **importovat novou aplikaci** zadejte název vaší aplikace Pizza a pak vyberte tlačítko **Hotovo** .

Aplikace se naimportuje.

Pokud se zobrazí dialogové okno, **jak vytvořit efektivní aplikaci Luis**, zavřete dialogové okno.

## <a name="train-and-publish-the-pizza-app"></a>Výuka a publikování aplikace Pizza

Měla by se zobrazit stránka **záměry** se seznamem záměrů v aplikaci Pizza.

[!INCLUDE [How to train](howto-train.md)]

[!INCLUDE [How to publish](howto-publish.md)]

Vaše aplikace Pizza je teď připravená k použití.

## <a name="record-the-app-id-prediction-key-and-prediction-endpoint-of-your-pizza-app"></a>Zaznamenání ID aplikace, klíč předpovědi a koncového bodu předpovědi vaší aplikace Pizza

Pro použití vaší nové aplikace Pizza budete potřebovat ID aplikace, klíč předpovědi a koncový bod předpovědi vaší aplikace Pizza.

Vyhledání těchto hodnot:

1. Na stránce **záměry** vyberte **Spravovat**.
1. Na stránce **nastavení aplikace** si poznamenejte **ID aplikace**.
1. Vyberte **Azure Resources** (Prostředky Azure).
1. Na stránce **prostředky Azure** zaznamenejte **primární klíč**. Tato hodnota je váš klíč předpovědi.
1. Zaznamenejte **adresu URL koncového bodu**. Tato hodnota je vaším koncovým bodem předpovědi.
