---
title: Získání klíčů předplatného pro Custom Speech Service v Azure | Dokumentace Microsoftu
description: Další informace o získání klíčů předplatného pro volání služby Custom Speech Service ve službě Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: 84ef657af2cc3dc4a7168a815b5e51d6f4f33fd7
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49338365"
---
# <a name="obtain-subscription-keys"></a>Získání klíčů předplatného

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Abyste mohli začít používat Azure Custom Speech Service, musíte nejprve propojí váš účet uživatele k předplatnému Azure. Předplatná na bezplatné a placené úrovně jsou k dispozici. Informace o úrovních najdete v tématu [stránce s cenami](https://www.microsoft.com/cognitive-services/en-us/pricing).

## <a name="get-a-subscription-key"></a>Získat klíč předplatného
1. Klíč předplatného můžete získat z webu Azure portal v jednom ze dvou způsobů:

    * Přejděte na [portál Azure](https://ms.portal.azure.com) a přidejte nové rozhraní Cognitive Services API tak, že vyhledáte _Cognitive Services_ a poté vyberete **Cognitive Services APIs**.

      ![Hledání služeb cognitive Services](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription.png)

    * Také přejít přímo na [rozhraní API služeb Cognitive Services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServices).

        ![Rozhraní API služeb Cognitive Services](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription2.png)

    
1. Zadejte následující požadované pole:

      a. **Název účtu**. Použijte název, který vám vyhovuje. Nezapomeňte toto jméno tak, aby vaše předplatné služeb Cognitive Services můžete najít v seznamu prostředků.

      b. **Předplatné**. Vyberte jeden z vašich předplatných Azure.

      c. **Typ rozhraní API**. Vyberte **Custom Speech Service (Preview)**.

      d. **Umístění**. Momentálně **USA – západ**.

      e. **Cenová úroveň**. Vyberte úroveň, která vám vyhovuje. **F0** je úroveň free. Kvóty, které jsou povoleny jsou vysvětlené na [stránce s cenami](https://www.microsoft.com/cognitive-services/en-us/pricing).

      ![Vytvoření účtu cognitive Services](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-blade.png)

1. V seznamu prostředků byste měli najít buď zobrazení na řídicím panelu nebo službu s názvem zadaného účtu. Když ji vyberete, zobrazí se přehled vaší služby. V seznamu na levé straně v části **správy prostředků**vyberte **klíče**. Kopírování **klíč 1**.

      Tento klíč předplatného se vyžaduje v dalších krocích.

      ![KLÍČ 1](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys2.png)

      > [!NOTE]
      > Nekopírovat **ID předplatného** na stránce Přehled. Budete potřebovat klíč předplatného v dalším kroku.
      >

      ![ID předplatného – přehled](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys.png)

1. Zadejte klíč předplatného. na pásu karet v pravém horním rohu vyberte váš uživatelský účet. V rozevírací nabídce vyberte **předplatná**.

      ![Položka nabídky předplatného](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-selection.png)

    Zobrazí se tabulky z předplatných, což je prázdný, při prvním otevření.

    ![Tabulka předplatná](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-list.png)

1. Vyberte **přidat nový**. Zadejte název předplatného a klíč předplatného. Může být buď **klíč 1** (primární klíč) nebo **klíče 2** (sekundární klíč) z vašeho předplatného.

      ![Název klíče předplatného](../../../media/cognitive-services/custom-speech-service/custom-speech-enter-subsciption.png)

Nahrání dat, trénování modelu, nebo provést nasazení, budete potřebovat odkázat vaše aktivity Custom Speech Service k předplatnému Azure. Může být úroveň free nebo placenou úroveň předplatného. Další informace najdete na [stránce s cenami](https://www.microsoft.com/cognitive-services/en-us/pricing).

## <a name="get-a-subscription-id"></a>Získat Identifikátor předplatného.
ID předplatného najdete na webu Azure portal. Vyhledejte *služeb Cognitive Services* a *Custom Speech Service*a postupujte podle pokynů.

> [!NOTE]
> Klíč předplatného je vyžadováno později v tomto procesu.
>

## <a name="next-steps"></a>Další postup
* Začít vytvářet vaše [vlastního akustického modelu](cognitive-services-custom-speech-create-acoustic-model.md).
* Začít vytvářet vaše [vlastního jazykového modelu](cognitive-services-custom-speech-create-language-model.md).
