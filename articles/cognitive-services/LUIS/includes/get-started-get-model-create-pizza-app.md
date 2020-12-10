---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: roy-har
manager: diberry
ms.service: cognitive-services
ms.date: 06/04/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: roy-har
ms.openlocfilehash: 081b9288d091e26d0e6d30aa849ced18a3c2ffb6
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938456"
---
Vytvořte aplikaci Pizza.

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

## <a name="add-an-authoring-resource-to-the-pizza-app"></a>Přidání prostředku pro vytváření do aplikace Pizza

1. Vyberte **Spravovat**.
1. Vyberte **Azure Resources** (Prostředky Azure).
1. Vyberte **prostředek pro vytváření obsahu**.
1. Vyberte **změnit prostředek pro vytváření obsahu**.

Pokud máte prostředek pro vytváření obsahu, zadejte **název tenanta**, **název předplatného** a **název prostředku Luis** prostředku pro vytváření obsahu.

Pokud nemáte prostředek pro vytváření obsahu:

1. Vyberte **vytvořit nový prostředek**.
1. Zadejte **název tenanta**, **název prostředku**, **název předplatného** a **název skupiny prostředků Azure**.

Vaše aplikace Pizza je teď připravená k použití.

## <a name="record-the-access-values-for-your-pizza-app"></a>Poznamenejte si hodnoty pro přístup k aplikaci Pizza.

Pokud chcete používat novou aplikaci Pizza, budete potřebovat ID aplikace, vytváření klíčů a koncový bod pro vytváření aplikací pro Pizza. K získání předpovědi budete potřebovat samostatný koncový bod předpovědi a klíč předpovědi.

Vyhledání těchto hodnot:

1. Na stránce **záměry** vyberte **Spravovat**.
1. Na stránce **nastavení aplikace** si poznamenejte **ID aplikace**.
1. Vyberte **Azure Resources** (Prostředky Azure).
1. Vyberte **prostředek pro vytváření obsahu**.
1. Na kartách prostředky pro **vytváření prostředků** a **předpovědí prostředků** zaznamenejte **primární klíč**. Tato hodnota je váš kód pro vytváření.
1. Zaznamenejte **adresu URL koncového bodu**. Tato hodnota je vaším koncovým bodem pro vytváření obsahu.
