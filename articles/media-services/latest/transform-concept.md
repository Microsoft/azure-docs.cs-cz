---
title: Transformuje a úlohy ve službě Azure Media Services | Microsoft Docs
description: Při použití Media Services, budete muset vytvořit transformace pro popis pravidla nebo specifikace pro zpracování videa. Tento článek nabízí přehled transformace je a způsobu jeho použití.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: b755e0573098d3dbed1bea18a40af634be609f76
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="transforms-and-jobs"></a>Úlohy a transformace

## <a name="overview"></a>Přehled 

Nejnovější verzi Azure Media Services REST API (v3) zavádí nový prostředek šablonované pracovního postupu pro kódování nebo analýza videa, názvem **transformace**. **Transformuje** můžete použít ke konfiguraci běžné úlohy kódování nebo analying videa. Každý **transformace** popisuje jednoduché pracovního postupu úloh pro zpracování video nebo zvuk soubory. 

**Transformace** objekt je recepturách a **úlohy** je skutečný požadavek na službu Azure Media Services použít, **transformace** pro daný vstupní video nebo zvuk obsah. **Úloha** určuje informace, jako je umístění vstupního videa a umístění pro výstup. Můžete zadat umístění videa pomocí: adresy URL http (s), SAS adresy URL nebo cestu k souborům místně nebo v Azure Blob storage. Můžete mít až 100 transformací ve vašem účtu Azure Media Services a odesílání úloh v rámci těchto transformací. Potom k odběru události, jako jsou například změny stavu úlohy, používání oznámení, které přímo integrovat oznámení systému Azure událostí mřížky. 

Vzhledem k tomu, že toto rozhraní API vycházejí pomocí Správce prostředků Azure, můžete vytvořit a nasadit transformací ve vašem účtu Media Services šablony Resource Manageru. Řízení přístupu na základě role můžete také nastavit na úrovni prostředků v tomto rozhraní API umožňuje zablokovat přístup ke konkrétním prostředkům jako transformace.

## <a name="transform-definition"></a>Transformace definice

V následující tabulce se zobrazují vlastnosti transformace a dává jejich definice.

|Název|Typ|Popis|
|---|---|---|
|ID|řetězec|ID plně kvalifikovaný prostředků pro prostředek.|
|jméno|řetězec|Název prostředku.|
|Properties.Created |řetězec|UTC datum a čas, kdy transformovat byl vytvořen, v ' rrrr-MM-ddTHH: formát.|
|Properties.Description |řetězec|Volitelné podrobný popis pro transformaci.|
|properties.lastModified |řetězec|UTC datum a čas, kdy transformovat poslední aktualizace, v ' rrrr-MM-ddTHH ' formátu.|
|Properties.Outputs |[TransformOutput]|Pole jeden nebo více TransformOutputs, které by měl generovat pro transformaci.|
|type|řetězec|Typ prostředku.|

Úplné definici naleznete v tématu [transformuje](https://docs.microsoft.com/rest/api/media/transforms).

## <a name="job-definition"></a>Definice úlohy

V následující tabulce se zobrazují vlastnosti úlohy a dává jejich definice.

|Název|Typ|Popis|
|---|---|---|
|ID|řetězec|ID plně kvalifikovaný prostředků pro prostředek.|
|jméno|řetězec|Název prostředku.|
|Properties.Created |řetězec|UTC datum a čas, kdy transformovat byl vytvořen, v ' rrrr-MM-ddTHH: formát.|
|Properties.Description |řetězec|Volitelný podrobný popis úlohy.|
|properties.lastModified |řetězec|UTC datum a čas, kdy transformovat poslední aktualizace, v ' rrrr-MM-ddTHH ' formátu.|
|Properties.Outputs |[JobOutput]: [JobOutputAsset] |Výstupy úlohy.|
|Properties.priority |Priorita |Priorita, se kterým má být zpracován úlohy. Úlohy s vyšší prioritou se zpracovávají před úlohami s nižší prioritou. Pokud není nastavena, výchozí hodnota je normální.
|Properties.state |JobState |Aktuální stav úlohy.
|type|řetězec|Typ prostředku.|

Úplné definici naleznete v tématu [úlohy](https://docs.microsoft.com/rest/api/media/jobs).

## <a name="typical-workflow-and-example"></a>Obvyklý pracovní postup a příklad

1. Vytvoření transformace 
2. Odesílání úloh v rámci této transformace 
3. Seznam transformací 
4. Transformace, odstraňte, pokud nemáte v plánu používat tento "recept" v budoucnu. 

Předpokládejme, že jste chtěli extrahovat první snímek všech videa jako miniaturu – postup, kterým by se: 

1. Definovat pravidla pro zpracování – třeba, použijte první snímek videa jako miniaturu 
2. Pro každý video, které máte jako vstup pro službu, pak by zjistit službu: 
    1. Kde najít video, a 
    2. Kde má být zapsán výstup – například na miniaturu 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Video soubory datového proudu](stream-files-dotnet-quickstart.md)
