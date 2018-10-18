---
title: Transformace a úlohy ve službě Azure Media Services | Dokumentace Microsoftu
description: Při použití služby Media Services, je potřeba vytvořit transformace pro popis pravidla nebo specifikace pro zpracování vašeho videa. Tento článek obsahuje přehled transformace je a jak ji používat.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 214d4d3d11255e417f3df1e5f6e648b2a30225ea
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377304"
---
# <a name="transforms-and-jobs"></a>Transformace a úlohy

## <a name="overview"></a>Přehled 

Nejnovější verzi Azure Media Services REST API (v3) představuje nového prostředku šablony pracovního postupu pro kódování a/nebo analyzovat videa, volá se **transformace**. **Transformuje** slouží ke konfiguraci běžné úlohy kódování nebo analying videa. Každý **transformace** popisuje jednoduchý pracovní postup úloh zpracování videa nebo zvukových souborů. 

**Transformace** objekt představuje recept a **úlohy** je skutečnou žádost do služby Azure Media Services použít **transformace** do daného vstupního videa nebo zvukový obsah. **Úloha** určuje informace, jako je umístění vstupního videa a umístění pro výstup. Můžete zadat umístění videa pomocí: adresy URL http (s), adresy URL SAS nebo cesty k souborům umístěným lokálně nebo do úložiště objektů Blob v Azure. Můžete mít až 100 transformace ve vašem účtu Azure Media Services a odesílání úloh v rámci těchto transformací. Potom k odběru události, například změny stavu úlohy pomocí oznámení, které integrovat Azure Event Grid oznámení systému. 

Protože toto rozhraní API je řízené pomocí Azure Resource Manageru, můžete použít šablony Resource Manageru k vytvoření a nasazení transformace ve vašem účtu Media Services. Řízení přístupu na základě rolí lze také nastavit na úrovni prostředků v tomto rozhraní API, díky tomu umožňuje uzamknout přístup ke konkrétním prostředkům, jako je transformace.

## <a name="transform-definition"></a>Transformace definice

Následující tabulka uvádí vlastnosti transformace a umožňuje jejich definice.

|Název|Typ|Popis|
|---|---|---|
|ID|řetězec|Plně kvalifikované ID prostředku pro prostředek.|
|jméno|řetězec|Název prostředku.|
|Properties.Created |řetězec|Čas UTC datum a čas, kdy transformací, která byla vytvořena, v ' rrrr-MM-: ssZ "formátu.|
|Properties.Description |řetězec|Podrobný popis transformace.|
|properties.lastModified |řetězec|Datum a čas UTC při transformaci poslední aktualizace, data v ' rrrr-MM-: ssZ "formátu.|
|Properties.Outputs |[] TransformOutput|Pole jednoho nebo více TransformOutputs, které transformací, která se má generovat.|
|type|řetězec|Typ prostředku.|

Kompletní definici, naleznete v tématu [transformuje](https://docs.microsoft.com/rest/api/media/transforms).

## <a name="job-definition"></a>Definice úlohy

Následující tabulka uvádí vlastnosti úlohy a umožňuje jejich definice.

|Název|Typ|Popis|
|---|---|---|
|ID|řetězec|Plně kvalifikované ID prostředku pro prostředek.|
|jméno|řetězec|Název prostředku.|
|Properties.Created |řetězec|Čas UTC datum a čas, kdy transformací, která byla vytvořena, v ' rrrr-MM-: ssZ "formátu.|
|Properties.Description |řetězec|Podrobný popis úlohy.|
|properties.lastModified |řetězec|Datum a čas UTC při transformaci poslední aktualizace, data v ' rrrr-MM-: ssZ "formátu.|
|Properties.Outputs |[] JobOutput: JobOutputAsset] |Výstupy úlohy.|
|Properties.priority |Priorita |Priorita, pomocí kterého by se měly zpracovat úlohy. Přenosy s vyšší prioritou se zpracovávají před úlohami s nižší prioritou. Pokud není nastavena, výchozí hodnota je normální.
|Properties.state |JobState |Aktuální stav úlohy.
|type|řetězec|Typ prostředku.|

Kompletní definici, naleznete v tématu [úlohy](https://docs.microsoft.com/rest/api/media/jobs).

## <a name="typical-workflow-and-example"></a>Typický pracovní postup a příklad

1. Vytvoření transformace 
2. Odesílání úloh v rámci této transformace 
3. Seznam transformací 
4. Transformace, odstraňte, pokud nemáte v úmyslu použít tento "předpisu" v budoucnu. 

Předpokládejme, že chcete extrahovat první snímek všech videí jako obrázek miniatury – kroky, které byste prováděli jsou: 

1. Definovat pravidla pro zpracování – třeba, použijte první snímek video jako miniatury 
2. Pro každé video, které máte jako vstup do služby, pak budou předávat služby: 
    1. Kde se mají hledat videa, a 
    2. Kde má být zapsán výstup – například miniatury 

## <a name="next-steps"></a>Další postup

[Stream video soubory](stream-files-dotnet-quickstart.md)
