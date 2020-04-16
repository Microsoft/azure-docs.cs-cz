---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: a41c9dc888e85baf021712e3302da7ae6106db47
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421781"
---
Chcete-li dokončit rychlý start rozpoznávání záměru, budete muset vytvořit účet LUIS a projekt pomocí portálu náhledu LUIS. Tento rychlý start vyžaduje pouze předplatné LUIS. Předplatné služby Řeči *není* vyžadováno.

První věc, kterou budete muset udělat, je vytvořit účet LUIS a aplikaci pomocí portálu náhledu LUIS. Aplikace LUIS, kterou vytvoříte, bude používat předem připravenou doménu pro domácí automatizaci, která poskytuje záměry, entity a příklady projevy. Po dokončení budete mít koncový bod LUIS spuštěný v cloudu, který můžete volat pomocí sady Speech SDK. 

Podle těchto pokynů vytvořte aplikaci LUIS:

* <a href="https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">Úvodní příručka: Vytvoření předem sestavené aplikace domény<span class="docon docon-navigate-external x-hidden-focus"></span></a>

Až budete hotovi, budete potřebovat čtyři věci:

* Re-publikovat s **řeč priming** přepínal na
* Primární **klíč** LUIS
* Vaše **umístění služby** LUIS
* **ID aplikace** LUIS

Zde najdete tyto informace na [portálu preview LUIS](https://preview.luis.ai/):

1. Na portálu náhledu LUIS vyberte aplikaci a pak vyberte tlačítko **Publikovat.**

2. Pokud `en-US` používáte možnost **Naplnění řeči,** **vyberte** produkční pozici. **On** Pak vyberte tlačítko **Publikovat.**

    > [!IMPORTANT]
    > **Příprava řeči** je vysoce doporučena, protože zlepší přesnost rozpoznávání řeči.

    > [!div class="mx-imgBorder"]
    > ![Publikování služby LUIS do koncového bodu](../../../media/luis/publish-app-popup.png)

3. Na portálu náhledu LUIS vyberte **Spravovat**a pak vyberte **Prostředky Azure**. Na této stránce najdete klíč a umístění služby LUIS (někdy označované jako _oblast)._

   > [!div class="mx-imgBorder"]
   > ![Klíč a umístění služby LUIS](../../../media/luis/luis-key-region.png)

4. Až budete mít klíč a polohu, budete potřebovat ID aplikace. Vyberte **Nastavení aplikace** – ID aplikace je k dispozici na této stránce.

   > [!div class="mx-imgBorder"]
   > ![ID aplikace LUIS](../../../media/luis/luis-app-id.png)
