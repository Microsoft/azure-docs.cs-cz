---
title: 'Úvodní příručka: Vytvoření klíče služby LUIS'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci LUIS a získat klíč.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: trbye
ms.openlocfilehash: df12b51020083489d431d0ebcd7eb506ef97caa2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400852"
---
# <a name="quickstart-getting-a-luis-endpoint-key"></a>Úvodní příručka: Získání klíče koncového bodu LUIS

## <a name="prerequisites"></a>Požadavky

Než začnete tento kurz, ujistěte se, že máte následující položky:

* Účet služby LUIS. Můžete ho získat zdarma prostřednictvím [portálu LUIS](https://www.luis.ai/home).

## <a name="luis-and-speech"></a>Služba LUIS a řeč

Služba LUIS se integruje se službou Speech za účelem rozpoznávání záměrů z řeči. Nepotřebujete předplatné služby Speech, stačí vám služba LUIS.

Služba LUIS používá tři druhy klíčů:

|Typ klíče|Účel|
|--------|-------|
|Vytváření obsahu|Umožňuje vytvářet a upravovat aplikace LUIS programově.|
|Starter|Umožňuje otestovat aplikaci LUIS pouze pomocí textu.|
|Koncový bod |Autorizuje přístup k určité aplikaci LUIS.|

Pro účely tohoto kurzu potřebujete typ klíče koncového bodu. Kurz používá příklad domácí automatizace LUIS aplikace, kterou můžete vytvořit podle [použití předem domácí automatizace aplikace](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) rychlý start. Pokud jste vytvořili vlastní aplikaci LUIS, můžete ji místo toho použít.

Když vytvoříte aplikaci LUIS, LUIS automaticky vygeneruje počáteční klíč, takže můžete aplikaci otestovat pomocí textových dotazů. Tento klíč neumožňuje integraci služby Rozpoznávání řeči a nebude fungovat s tímto kurzem. Vytvořte prostředek LUIS na řídicím panelu Azure a přiřaďte ho k aplikaci LUIS. Pro účely tohoto kurzu můžete použít na bezplatnou úroveň předplatného.

Po vytvoření prostředku LUIS na řídicím panelu Azure se přihlaste k [portálu LUIS](https://www.luis.ai/home), zvolte aplikaci na stránce **Moje aplikace a** přepněte na stránku **Správa** aplikace. Nakonec vyberte **klíče a koncové body** v postranním panelu.

![Nastavení klíčů a koncových bodů portálu LUIS](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-endpoints-page.png)

Na stránce **Nastavení kláves a koncových bodů:**

1. Posuňte se dolů do části **Zdroje a klíče a** vyberte Přiřadit **prostředek**.
1. V **dialogovém okně Přiřadit klíč k aplikaci proveďte** následující změny:

   * V části **Tenant**zvolte **Microsoft**.
   * V **části Název předplatného**zvolte předplatné Azure, které obsahuje prostředek LUIS, který chcete použít.
   * V části **Klíč**vyberte prostředek LUIS, který chcete s aplikací použít.

   Nové předplatné se za chvíli zobrazí v tabulce v dolní části stránky.

1. Vyberte ikonu vedle klávesy, chcete-li ji zkopírovat do schránky. (Můžete použít kterýkoli z klíčů.)

![Klíče předplatného aplikace LUIS](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-assigned.png)


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rozpoznat záměry](~/articles/cognitive-services/Speech-Service/quickstarts/intent-recognition.md)
