---
title: 'Rychlý Start: vytvoření klíče LUIS'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci LUIS a získat klíč.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: wolfma
ms.openlocfilehash: 23f0b459b67088518375fbb4fd0b106da3aaf57c
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815838"
---
# <a name="quickstart-getting-a-luis-endpoint-key"></a>Rychlý Start: získání klíče koncového bodu LUIS

## <a name="prerequisites"></a>Předpoklady

Než začnete s tímto kurzem, ujistěte se, že máte následující položky:

* Účet služby LUIS. Můžete ho získat zdarma prostřednictvím [portálu LUIS](https://www.luis.ai/home).

## <a name="luis-and-speech"></a>Služba LUIS a řeč

Služba LUIS se integruje se službou Speech za účelem rozpoznávání záměrů z řeči. Nepotřebujete předplatné služby Speech, stačí vám služba LUIS.

LUIS používá tři typy klíčů:

|Typ klíče|Účel|
|--------|-------|
|Vytváření obsahu|Umožňuje vytvářet a upravovat aplikace LUIS programově.|
|Starter|Umožňuje testovat aplikace LUIS jenom pomocí textu.|
|Koncový bod |Autorizuje přístup ke konkrétní aplikaci LUIS.|

Pro tento kurz potřebujete typ klíče koncového bodu. V tomto kurzu se používá ukázková aplikace LUIS pro automatizaci, kterou můžete vytvořit pomocí programu pro rychlý Start pro [domovskou automatizaci pomocí předdefinovaných aplikací](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) . Pokud jste vytvořili vlastní aplikaci LUIS, můžete ji místo toho použít.

Když vytvoříte aplikaci LUIS, LUIS automaticky vygeneruje počáteční klíč, abyste mohli otestovat aplikaci pomocí textových dotazů. Tento klíč nepovoluje integraci služby Speech Service a nebude v tomto kurzu fungovat. Vytvořte prostředek LUIS na řídicím panelu Azure a přiřaďte ho do aplikace LUIS. Pro účely tohoto kurzu můžete použít na bezplatnou úroveň předplatného.

Po vytvoření prostředku LUIS na řídicím panelu Azure se přihlaste k [portálu Luis](https://www.luis.ai/home), vyberte svou aplikaci na stránce **Moje aplikace** a pak přepněte na stránku **správy** aplikace. Nakonec na bočním panelu vyberte **klíče a koncové body** .

![Nastavení klíčů a koncových bodů portálu LUIS](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-endpoints-page.png)

Na stránce **klíče a nastavení koncového bodu** :

1. Přejděte dolů k části **prostředky a klíče** a vyberte **přiřadit prostředek**.
1. V dialogovém okně **přiřadit klíč k aplikaci** proveďte následující změny:

   * V části **tenant**klikněte na **Microsoft**.
   * V části **název předplatného**vyberte předplatné Azure, které obsahuje prostředek Luis, který chcete použít.
   * V části **klíč**vyberte prostředek Luis, který chcete s aplikací používat.

   Nové předplatné se za chvíli zobrazí v tabulce v dolní části stránky.

1. Vyberte ikonu vedle klíče a zkopírujte ji do schránky. (Můžete použít kterýkoli z klíčů.)

![Klíče předplatného aplikace LUIS](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-assigned.png)


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rozpoznávat záměry](~/articles/cognitive-services/Speech-Service/quickstarts/intent-recognition.md)
