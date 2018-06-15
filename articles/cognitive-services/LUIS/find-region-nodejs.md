---
title: Najít LEOŠ oblasti s Node.js v hranicích znalosti jazyka (LEOŠ) | Microsoft Docs
description: Prostřednictvím kódu programu najít publikování oblasti s klíč předplatného a aplikace ID pro LEOŠ.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/01/2018
ms.author: v-geberr
ms.openlocfilehash: 18ee324c10f074601c0c04573ca1a5266481f21c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/02/2018
ms.locfileid: "35343894"
---
# <a name="region-can-be-determined-from-api-call"></a>Oblasti se dá určit z volání rozhraní API 
Pokud máte LEOŠ ID aplikace a LEOŠ ID předplatného, můžete najít které oblasti používat pro dotazy na koncový bod.

> [!NOTE] 
> Úplné řešení Node.js je k dispozici z [ **LEOŠ-Samples** úložiště Github](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/nodejs/).

## <a name="luis-endpoint-query-strategy"></a>Strategie dotazu LEOŠ koncový bod
Každý dotaz LEOŠ koncového bodu vyžaduje:

* Klíč předplatného
* ID aplikace
* V oblasti

Pokud dotaz koncový bod LEOŠ používá ID předplatného správný klíč a aplikace ale do nesprávné oblasti, je kód odpovědi 401. 401 žádost není počítají směrem k kvóty předplatného. Zapněte tento požadavek do strategie pro dotazování všechny oblasti najít správnou oblast. Jediným požadavkem, který vrátí stavový kód 2xx, je správné oblast. 

|Kód odpovědi|Parametry|
|--|--|
|2xx|klíč správné předplatné<br>Opravte ID aplikace<br>oblast správné hostitele|
|401|klíč správné předplatné<br>Opravte ID aplikace<br>_nesprávný_ oblast hostitele|

## <a name="nodejs-code-to-find-region"></a>Kód Node.js najít oblast
Konzolové aplikace přijímá ID aplikace LEOŠ a klíč předplatného a vrací všechny oblasti s ním spojená. V současné době předplatné klíč byl vytvořený oblast by měla vrátit, pouze jedna oblast.

Zahrnout NPM závislosti:

[!code-javascript[Add the dependencies](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=5-6 "Add the dependencies")]

Přidejte konstanty. Nahraďte hodnoty proměnných pro `subscriptionKey` a `appId` vlastními hodnotami.  

[!code-javascript[Add constants](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=8-25 "Add constants")]

Přidat `searchRegions` funkce Najít oblasti. Všechny nesprávné oblasti vrátí 401, který zachycení a ignorovat.

[!code-javascript[Find region](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=27-37 "Find region")]

Volání `searchRegions` funkce a vrátit jedné oblasti:

[!code-javascript[Call the function](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=39-43 "Call the function")]

Při spuštění aplikace zobrazí terminálu oblasti app ID a předplatné klíče.

![Snímek obrazovky zobrazující LEOŠ oblast konzolové aplikace](./media/find-region-nodejs/console.png)


## <a name="next-steps"></a>Další postup

Další informace o LEOŠ [oblasti](luis-reference-regions.md).