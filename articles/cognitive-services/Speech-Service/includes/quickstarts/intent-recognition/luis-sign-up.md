---
title: 'Rychlý Start: rozpoznávání řeči, záměrů a entit, Python-Speech Service'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/27/2019
ms.author: erhopf
ms.openlocfilehash: aee5d6e1f6ed0519df7d1059b39f215d9f0d9091
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660470"
---
K dokončení rychlého startu pro rozpoznávání záměrů budete muset vytvořit účet LUIS a projekt pomocí portálu LUIS Preview. Tento rychlý Start vyžaduje jenom předplatné LUIS. Předplatné služby Speech není vyžadováno.

První věc, kterou je potřeba udělat, je vytvořit účet LUIS a aplikaci pomocí portálu LUIS Preview. Aplikace LUIS, kterou vytvoříte, bude používat předem vytvořenou doménu pro automatizaci domů, která poskytuje záměry, entity a příklady projevy. Až budete hotovi, budete mít v cloudu spuštěný koncový bod LUIS, který můžete volat pomocí sady Speech SDK. 

Pokud chcete vytvořit aplikaci LUIS, postupujte podle těchto pokynů: 

* <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">Rychlý Start: sestavení předem sestavené doménové aplikace</a>

Až budete hotovi, budete potřebovat tři věci: 

* Váš LUIS klíč
* Vaše LUIS oblast
* ID aplikace LUIS

Tyto informace můžete najít na [portálu Luis Preview](https://preview.luis.ai/):

1. Na portálu LUIS Preview vyberte **Spravovat**a pak vyberte **prostředky Azure**. Na této stránce najdete LUIS klíč a umístění (někdy označované jako _oblast_).  

   > [!div class="mx-imgBorder"]
   > ![klíč a umístění LUIS](../../../media/luis/luis-key-region.png)

2. Až budete mít klíč a polohu, budete potřebovat ID aplikace. Vybrat **nastavení aplikace** – na této stránce je k dispozici vaše ID aplikace.

   > [!div class="mx-imgBorder"]
   > ID aplikace ![LUIS](../../../media/luis/luis-app-id.png)