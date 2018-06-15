---
title: Najít LEOŠ oblast pomocí C# v hranicích znalosti jazyka (LEOŠ) | Microsoft Docs
description: Prostřednictvím kódu programu najít publikování oblasti s klíč předplatného a aplikace ID pro LEOŠ.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/31/2018
ms.author: v-geberr
ms.openlocfilehash: c8d2024567255083aec470adfebff0d1706fd472
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/02/2018
ms.locfileid: "35343893"
---
# <a name="region-can-be-determined-from-api-call"></a>Oblasti se dá určit z volání rozhraní API 
Pokud máte LEOŠ ID aplikace a LEOŠ ID předplatného, můžete najít které oblasti používat pro dotazy na koncový bod.

> [!NOTE] 
> Úplné řešení C# je k dispozici [ **LEOŠ-Samples** úložiště Github](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/csharp/).

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

## <a name="c-class-code-to-find-region"></a>Třída kód C# k vyhledání oblasti
Konzolové aplikace přijímá ID aplikace LEOŠ a klíč předplatného a vrací všechny oblasti s ním spojená. V současné době předplatné klíč byl vytvořený oblast by měla vrátit, pouze jedna oblast.

Obsahovat závislosti knihovny .net:

[!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=1-6 "Add the dependencies")]

Přidejte tuto vlastní třídu LEOŠ vytvořená tak, aby najít oblasti. Nahraďte hodnoty proměnných pro `luisAppId` a `luisSubscriptionKey` vlastními hodnotami. Všechny oblasti, které vracejí 401 se zapíšou do konzoly ladění. 

[!code-csharp[Add the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=10-83 "Add the LUIS class")]

Toto je příklad volání vlastní třídu LEOŠ v metodu Main konzolové aplikace:

[!code-csharp[Call the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=85-101 "Call the LUIS class")]

Při spuštění aplikace zobrazí konzole oblasti app ID a předplatné klíče.

![Snímek obrazovky zobrazující LEOŠ oblast konzolové aplikace](./media/find-region-csharp/console.png)

## <a name="next-steps"></a>Další postup

Další informace o LEOŠ [oblasti](luis-reference-regions.md).
