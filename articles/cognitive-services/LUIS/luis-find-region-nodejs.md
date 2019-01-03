---
title: Koncový bod oblasti a Node.js
titleSuffix: Language Understanding - Azure Cognitive Services
description: S využitím Node.js, publikovat najít oblast s klíče koncového bodu a aplikace ID LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 4d14569219c8db503fc91f52a6867de85373aa05
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53724381"
---
# <a name="programmatically-find-endpoint-region-with-nodejs"></a>Programově najít oblast koncový bod s využitím Node.js
Pokud máte LUIS ID aplikace a služby LUIS ID předplatného, můžete najít jaké oblasti se má použít pro koncový bod dotazy.

> [!NOTE] 
> Kompletní řešení Node.js je k dispozici [ **Azure-Samples** úložiště GitHub](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/find-region/nodejs/).

## <a name="luis-endpoint-query-strategy"></a>Strategie dotazu koncový bod služby LUIS
Každý dotaz LUIS koncového bodu vyžaduje:

* Klíč rozhraní koncového bodu
* ID aplikace
* Oblast

Pokud dotaz koncový bod služby LUIS používá správný koncový bod klíč a app ID ale nesprávné oblasti, je kód odpovědi 401. 401 požadavek se počítá směrem k kvóty předplatného. Tento požadavek proměňte strategie zaměřené na dotazování všech oblastech se najít správný oblast. Jediným požadavkem, který vrátí stavový kód 2xx je správný oblast. 

|Kód odpovědi|Parametry|
|--|--|
|2xx|klíč správný koncový bod<br>ID správné aplikace<br>oblast správného hostitele|
|401|klíč správný koncový bod<br>ID správné aplikace<br>_nesprávný_ oblasti hostitele|

## <a name="nodejs-code-to-find-region"></a>Kód Node.js, který zjistí oblasti
Konzolová aplikace přijímá ID aplikace LUIS a klíče koncového bodu a vrátí všechny oblasti s ním spojená. V současné době se vytvoří klíče rozhraní koncového bodu podle oblastí by měl vrátit tak pouze jedna oblast.

Zahrnout závislosti NPM:

[!code-javascript[Add the dependencies](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=5-6 "Add the dependencies")]

Přidejte konstanty. Nahraďte hodnoty proměnných pro `subscriptionKey` a `appId` vlastními hodnotami.  

[!code-javascript[Add constants](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=8-25 "Add constants")]

Přidat `searchRegions` funkce se najít oblast. Všechny oblasti nesprávné vrátit 401, která je zachycena a ignoruje.

[!code-javascript[Find region](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=27-37 "Find region")]

Volání `searchRegions` funkci a vrátí jedné oblasti:

[!code-javascript[Call the function](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=39-43 "Call the function")]

Při spuštění aplikace terminálu zobrazí v oblasti pro ID aplikace.

![Snímek obrazovky zobrazující oblast LUIS aplikace konzoly](./media/find-region-nodejs/console.png)


## <a name="next-steps"></a>Další postup

Další informace o LUIS [oblastech](luis-reference-regions.md).
