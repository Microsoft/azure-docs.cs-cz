---
title: Získat předplatné klíče pro službu vlastní řeči v Azure | Microsoft Docs
description: Další informace o získání předplatného klíče pro volání ke službě vlastní řeči v kognitivní služby.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: fcef86a19a77581ff82b64173e2ac68b26ae708a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342455"
---
# <a name="obtain-subscription-keys"></a>Získání klíčů předplatného
Abyste mohli začít používat službu Azure vlastní rozpoznávání řeči, musíte nejprve propojení účtu uživatele k předplatnému Azure. Odběry bezplatnými a placenými úrovněmi jsou k dispozici. Informace o vrstvy, najdete v článku [stránce s cenami](https://www.microsoft.com/cognitive-services/en-us/pricing).

## <a name="get-a-subscription-key"></a>Získat klíč předplatného
1. Klíč předplatného můžete získat z portálu Azure v jednom ze dvou způsobů:

    * Přejděte na [portál Azure](https://ms.portal.azure.com), a přidání nového rozhraní API kognitivní služby vyhledáním _kognitivní služby_ a potom vyberete **kognitivní rozhraní API služby**.

      ![Kognitivní služby vyhledávání](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription.png)

    * Nebo přejít přímo na [kognitivní rozhraní API služby](https://ms.portal.azure.com/#create/Microsoft.CognitiveServices).

        ![Rozhraní API služeb Cognitive Services](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription2.png)

    
2. Vyplňte následující povinná pole:

      a. **Název účtu**. Použijte název, který vám vyhovuje. Pamatovat si tento název tak, aby vaše předplatné kognitivní služby můžete najít v seznamu prostředků.

      b. **Předplatné**. Vyberte jeden z vašich předplatných Azure.

      c. **Typ rozhraní API**. Vyberte **služby vlastní řeči (Preview)**.

      d. **Umístění**. Je momentálně **západní USA**.

      e. **Cenová úroveň**. Vyberte vrstvu, který vám vyhovuje. **F0** je úroveň free. Kvóty, které jsou povoleny jsou vysvětlené na [stránce s cenami](https://www.microsoft.com/cognitive-services/en-us/pricing).

      ![Kognitivní vytváření účtu služby](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-blade.png)

3. V seznamu prostředků byste měli najít buď zobrazení na řídicím panelu nebo služby s názvem zadaného účtu. Pokud ji vyberete, zobrazí se Přehled služby. V seznamu na levé straně v části **Správa prostředků**, vyberte **klíče**. Kopírování **klíč 1**.

      Tento klíč předplatného je nutný v dalších krocích.

      ![KLÍČ 1](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys2.png)

      > [!NOTE]
      > Nekopírujte **ID předplatného** na stránce Přehled. Je nutné klíč předplatného v dalším kroku.
      >

      ![ID předplatného – přehled](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys.png)

4. Zadejte svůj klíč předplatného, na pásu karet v pravém horním rohu vyberte uživatelský účet. V rozevírací nabídce vyberte **odběry**.

      ![Položka nabídky odběry](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-selection.png)

    Zobrazí se tabulku odběrů, což je prázdný, při prvním otevření.

    ![Odběry tabulky](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-list.png)

5. Vyberte **přidat nový**. Zadejte název pro předplatné a klíč předplatného. Může být buď **klíč 1** (primární klíč) nebo **klíč 2** (sekundární klíč) ze svého předplatného.

      ![Název klíče předplatného](../../../media/cognitive-services/custom-speech-service/custom-speech-enter-subsciption.png)

Odesílat data, trénují podle nich model, nebo provést nasazení, musíte k propojení vaší služby řeči vlastní aktivity k předplatnému Azure. Může být úroveň free nebo vrstvě placené předplatné. Další informace najdete na [stránce s cenami](https://www.microsoft.com/cognitive-services/en-us/pricing).

## <a name="get-a-subscription-id"></a>Získání ID předplatného
Chcete-li získat ID předplatného, přejděte na portál Azure. Vyhledejte *kognitivní služby* a *vlastní řeči služby*a postupujte podle pokynů.

> [!NOTE]
> Později v tomto procesu je požadován klíč předplatného.
>

## <a name="next-steps"></a>Další postup
* Začít vytvářet vaše [vlastní akustickými modelu](cognitive-services-custom-speech-create-acoustic-model.md).
* Začít vytvářet vaše [vlastní jazyk modelu](cognitive-services-custom-speech-create-language-model.md).
