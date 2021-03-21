---
title: Vytvoření prostředku Cognitive Services pro analýzu textu
titleSuffix: Azure Cognitive Services
description: Naučte se vytvořit prostředek Cognitive Services Analýza textu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/09/2021
ms.author: aahi
ms.openlocfilehash: 5b6479d48a51ba962f2f6bfba16dac3b0886a9ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750508"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Vytvoření prostředku Cognitive Services pro analýzu textu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Vyberte **Vytvořit prostředek** a pak přejděte na **AI a strojové učení** > **Analýza textu**.
   Případně můžete přejít na [vytvořit analýza textu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Nastavte všechny požadované hodnoty:

    |Nastavení|Hodnota|
    |--|--|
    |Název|Zadejte název (2–64 znaků).|
    |Předplatné|Vyberte příslušné předplatné.|
    |Umístění|Vyberte umístění ve svém okolí.|
    |Cenová úroveň| Zadejte **S**, což je standardní cenová úroveň.|
    |Skupina prostředků|Vyberte dostupnou skupinu prostředků.|

1. Vyberte **Vytvořit** a počkejte na vytvoření prostředku. Prohlížeč se automaticky přesměruje na nově vytvořenou stránku prostředku.
1. Shromáždění nakonfigurovaného `endpoint` a klíč rozhraní API:

    |Karta prostředek na portálu|Nastavení|Hodnota|
    |--|--|--|
    |**Přehled**|Koncový bod|Zkopírujte koncový bod. Vypadá to jako `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0` .|
    |**Klíče**|Klíč rozhraní API|Zkopírujte jeden ze dvou klíčů. Jedná se o alfanumerický řetězec s 32 znaky bez mezer nebo pomlček: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
