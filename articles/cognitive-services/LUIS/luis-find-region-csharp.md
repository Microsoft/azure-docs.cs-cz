---
title: Najít oblast LUIS s jazykem C# v hranicích Language Understanding (LUIS) | Dokumentace Microsoftu
description: Prostřednictvím kódu programu publikovat najít oblast s klíče koncového bodu a aplikace ID LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/31/2018
ms.author: diberry
ms.openlocfilehash: 6f2688e492dbba718dc55825de164c189a9b46c9
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225104"
---
# <a name="region-can-be-determined-from-api-call"></a>Můžete určit oblast volání rozhraní API 
Pokud máte LUIS ID aplikace a služby LUIS ID předplatného, můžete najít jaké oblasti se má použít pro koncový bod dotazy.

> [!NOTE] 
> Kompletní řešení C# je k dispozici [ **LUIS-Samples** úložiště Github](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/csharp/).

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

## <a name="c-class-code-to-find-region"></a>Třída kódu jazyka C# se najít oblast
Konzolová aplikace přijímá ID aplikace LUIS a klíče koncového bodu a vrátí všechny oblasti s ním spojená. V současné době se vytvoří klíče rozhraní koncového bodu podle oblastí by měl vrátit tak pouze jedna oblast.

Zahrnout závislosti knihoven .net:

[!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=1-6 "Add the dependencies")]

Přidejte tuto vlastní třídu LUIS navržená tak, aby najít oblast. Nahraďte hodnoty proměnných pro `luisAppId` a `luisSubscriptionKey` vlastními hodnotami. Všechny oblasti, které vracejí 401, se zapíšou do konzolou pro ladění. 

[!code-csharp[Add the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=10-83 "Add the LUIS class")]

Toto je příklad volání vlastní třídu LUIS v metodu Main konzolové aplikace:

[!code-csharp[Call the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=85-101 "Call the LUIS class")]

Při spuštění aplikace konzoly zobrazuje oblast pro ID aplikace.

![Snímek obrazovky zobrazující oblast LUIS aplikace konzoly](./media/find-region-csharp/console.png)

## <a name="next-steps"></a>Další postup

Další informace o LUIS [oblastech](luis-reference-regions.md).
