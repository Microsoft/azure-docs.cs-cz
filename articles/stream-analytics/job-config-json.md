---
title: Pole JobConfig.json služby Azure Stream Analytics
description: V tomto článku jsou uvedena podporovaná pole pro soubor JobConfig.json služby Azure Stream Analytics, která slouží k vytváření úloh v kódu sady Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 27ed553035ce9d7abf57ffe93078df9c17b8408c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617954"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Pole JobConfig.json služby Azure Stream Analytics

Následující pole jsou podporována v souboru *JobConfig.json,* který slouží k [vytvoření úlohy Azure Stream Analytics pomocí kódu sady Visual Studio](quick-create-vs-code.md).

```json
{
    "DataLocale": "string",
    "OutputErrorPolicy": "string",
    "EventsLateArrivalMaxDelayInSeconds": "integer",
    "EventsOutOfOrderMaxDelayInSeconds": "integer",
    "EventsOutOfOrderPolicy": "string",
    "StreamingUnits": "integer",
    "CompatibilityLevel": "string",
    "UseSystemAssignedIdentity": "boolean",
    "GlobalStorage": {
        "AccountName": "string",
        "AccountKey": "string",
    },
    "DataSourceCredentialDomain": "string",
    "ScriptType": "string",
    "Tags": {}
}
```

|Name (Název)|Typ|Požaduje se|Hodnota|
|----|----|--------|-----|
|DataLocale|řetězec|Ne|Národní prostředí dat úlohy analýzy datového proudu. Hodnota by měla být název podporované. Výchozí hodnota je "en-US", pokud není zadána.|
|OutputErrorPolicy|řetězec|Ne|Označuje zásadu, která má být aplikována na události, které přicházejí do výstupu a nelze je zapsat do externího úložiště z důvodu jeho nesprávného vytvoření (chybějící hodnoty sloupců, hodnoty sloupců nesprávného typu nebo velikosti). - Stop nebo Drop|
|EventsLateArrivalMaxDelayInSeconds|celé číslo|Ne|Maximální přípustné zpoždění v sekundách, kdy události, které přicházejí pozdě, by mohly být zahrnuty. Podporovaný rozsah je -1 až 1814399 (20.23:59:59 dny) a -1 se používá k určení čekání neomezeně dlouho. Pokud vlastnost chybí, je interpretován mít hodnotu -1.|
|UdálostiOutOfOrderMaxDelayVSeconds|celé číslo|Ne|Maximální přípustné zpoždění v sekundách, kdy mohou být události mimo pořadí upraveny tak, aby byly zpět v pořádku.|
|EventsOutOfOrderPolicy|řetězec|Ne|Označuje zásadu, která má být aplikována na události, které jsou mimo pořadí ve vstupním datovém proudu událostí. - Upravit nebo drop|
|StreamingUnits|celé číslo|Ano|Určuje počet jednotek streamování, které úloha streamování používá.|
|Úroveň kompatibility|řetězec|Ne|Řídí určité chování za běhu úlohy streamování. - Přijatelné hodnoty jsou "1,0", "1,1", "1,2"|
|UseSystemAssignedIdentity|Boolean|Ne|Nastavte hodnotu true, aby tato úloha mohla komunikovat s ostatními službami Azure jako se sebou, pomocí spravované identity Služby Azure Active Directory.|
|GlobalStorage.AccountName|řetězec|Ne|Účet globálního úložiště se používá pro ukládání obsahu souvisejícího s úlohou analýzy datového proudu, jako jsou například snímky referenčních dat SQL.|
|GlobalStorage.AccountKey|řetězec|Ne|Odpovídající klíč pro účet globálního úložiště.|
|Doména pověření zdroje dat|řetězec|Ne|Rezervovaná vlastnost pro místní úložiště pověření.|
|ScriptType|řetězec|Ano|Rezervovaná vlastnost pro zadání typu tohoto zdrojového souboru. Přijatelná hodnota je "JobConfig" pro JobConfig.json.|
|Značky|Dvojice json klíč-hodnota|Ne|Značky jsou dvojice název/hodnota, které umožňují kategorizovat prostředky a zobrazit konsolidovanou fakturaci použitím stejné značky pro více prostředků a skupin prostředků. Názvy značek nerozlišují malá a velká písmena a hodnoty tagů rozlišují malá a velká písmena.|

## <a name="next-steps"></a>Další kroky

* [Vytvoření úlohy Azure Stream Analytics v kódu Visual Studia](quick-create-vs-code.md)
* [Test Stream Analytics dotazy místně s ukázkovými daty pomocí kódu Sady Visual Studio](visual-studio-code-local-run.md)
* [Test Stream Analytics dotazy místně proti vstupu živého datového proudu pomocí kódu](visual-studio-code-local-run-live-input.md)
*Visual Studio[nasazení úlohy Azure Stream Analytics pomocí balíčku CI/CD npm](setup-cicd-vs-code.md)