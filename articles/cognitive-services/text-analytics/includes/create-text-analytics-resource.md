---
title: Vytvoření prostředku Cognitive Services pro analýzu textu
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak vytvořit prostředek služby Cognitive Services pro analýzu textu.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 7e8b4480911f00afa8524ef4b81d697bb8ee5bcc
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877468"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Vytvoření prostředku Cognitive Services pro analýzu textu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyberte **vytvořit prostředek**a pak přejděte na **AI a strojové učení** > **rozhraní Text Analytics**.
   Nebo, přejděte na [vytvořit pro analýzu textu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Zadejte všechna požadovaná nastavení:

    |Nastavení|Hodnota|
    |--|--|
    |Name|Zadejte název (2-64 znaků)|
    |Subscription|Vyberte odpovídající předplatné|
    |Location|Vyberte nejbližší umístění|
    |Cenová úroveň| Zadejte **S**, cenovou úroveň standard|
    |Resource group|Vyberte skupinu prostředků k dispozici|

1. Vyberte **vytvořit** a počkat na prostředek, který má být vytvořen. Váš prohlížeč se automaticky přesměruje na stránku nově vytvořeného prostředku.
1. Shromažďovat nakonfigurované `endpoint` a klíč rozhraní API:

    |Karta prostředků na portálu|Nastavení|Value|
    |--|--|--|
    |**Přehled**|Koncový bod|Zkopírujte koncový bod. Zobrazí se podobný `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |**klíče**|Klíč rozhraní API|Zkopírujte jeden ze dvou klíčů. Je to alfanumerický řetězec 32 znaků bez mezer nebo pomlčky: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
