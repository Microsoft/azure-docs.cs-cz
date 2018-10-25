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
ms.date: 10/22/2018
ms.author: juliako
ms.openlocfilehash: e13afe26d06f5b5b2dcf7eddf00f9ee481312b2c
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024252"
---
# <a name="transforms-and-jobs"></a>Transformace a úlohy
 
Azure Media Services v3 zavádí nový prostředek bez vizuálního vzhledu pracovního postupu pro nebudou tím správným, který chcete použít pro kódování a/nebo analyzovat vaše videa, volá [transformuje](https://docs.microsoft.com/rest/api/media/transforms). **Transformuje** slouží ke konfiguraci běžné úlohy kódování nebo analyzovat videa. Každý **transformace** popisuje nebudou tím správným nebo pracovního postupu úloh zpracování videa nebo zvukových souborů. 

A **úlohy** je skutečnou žádost do služby Azure Media Services použít **transformace** do daného vstupního videa nebo zvukový obsah. **Úlohy** Určuje informace, jako jsou umístění vstupním videu a umístění pro výstup. Můžete zadat umístění vaše vstupní video použití: adresy URL HTTPs, adresy URL SAS, nebo [Media Services prostředky](https://docs.microsoft.com/rest/api/media/assets).  

## <a name="typical-workflow"></a>Typický pracovní postup

1. Vytvoření transformace 
2. Odesílání úloh v rámci této transformace 
3. Seznam transformací 
4. Transformace, odstraňte, pokud nemáte v úmyslu používat v budoucnosti. 

Předpokládejme, že chcete extrahovat první snímek všech videí jako obrázek miniatury – kroky, které byste prováděli jsou: 

1. Definovat předpisu nebo pravidla pro zpracování videí – "pomocí prvního rámce videa jako miniatury". 
2. Pro každé video budou předávat služby: 
    1. Kde najít tohoto videa  
    2. Kde má být zapsán výstup miniaturu. 

A **transformace** vám pomůže vytvořit jednou předpisu (krok 1) a odeslání úlohy pomocí tohoto předpisu (krok 2).

## <a name="transforms"></a>Transformace

Vytvoření transformací v účtu Media Services pomocí rozhraní API v3 přímo nebo pomocí kteréhokoli z publikované sady SDK. Media Services v3, které vychází rozhraní API pomocí Azure Resource Manageru, šablon Resource Manageru můžete také použít k vytvoření a nasazení se transformuje na svém účtu Media Services. Řízení přístupu na základě role je možné zamezit přístup k transformací.

### <a name="transform-definition"></a>Transformace definice

Následující tabulka uvádí vlastnosti transformace a umožňuje jejich definice.

|Název|Popis|
|---|---|
|ID|Plně kvalifikované ID prostředku pro prostředek.|
|jméno|Název prostředku.|
|Properties.Created |Čas UTC datum a čas, kdy transformací, která byla vytvořena, v ' rrrr-MM-: ssZ "formátu.|
|Properties.Description |Podrobný popis transformace.|
|properties.lastModified |Datum a čas UTC při transformaci poslední aktualizace, data v ' rrrr-MM-: ssZ "formátu.|
|Properties.Outputs |Pole jednoho nebo více TransformOutputs, které transformací, která se má generovat.|
|type|Typ prostředku.|

Kompletní definici, naleznete v tématu [transformuje](https://docs.microsoft.com/rest/api/media/transforms).

Jak jsme vysvětlili výše, transformace vám pomůže vytvořit recepturách a pravidla pro zpracování vašeho videa. Jeden transformace můžete použít více než jedno pravidlo. Transformace může například určit, že každé video zakódovat do souboru MP4 v daném s přenosovou rychlostí a že obrázek miniatury budou generovat z prvního rámce videa. Měli byste přidat jednu položku TransformOutput pro každé pravidlo, které chcete zahrnout do vaší transformace.

> [!NOTE]
> I když definici transformace podporuje operace aktualizace, byste měli věnovat pozornost, abyste měli jistotu, že všechny probíhající úlohy dokončení před provedením změny. Obvykle by aktualizovat existující transformaci, kterou chcete změnit nebo upravit priority základní TransformOutputs. Pokud chcete přepsat receptem, vytvoříte nové transformace.

## <a name="jobs"></a>Úlohy

Po vytvoření transformace, můžete odeslat úlohy pomocí rozhraní API služby Media Services nebo libovolného z publikované sady SDK. Monitorování událostí pomocí služby Event Grid je možné získat průběh a stav úloh. Další informace najdete v tématu [sledovat události pomocí EventGrid](job-state-events-cli-how-to.md ).

### <a name="jobs-definition"></a>Definice úloh

V následující tabulce jsou uvedeny vlastnosti pro úlohy a umožňuje jejich definice.

|Název|Popis|
|---|---|
|id|Plně kvalifikované ID prostředku pro prostředek.|
|jméno   |Název prostředku.|
|properties.correlationData |Zákazník za předpokladu, že data korelace (neměnné), které se vrátí jako součást úlohy a JobOutput stav upozornění na změnu. Další informace najdete v tématu [schémata událostí](media-services-event-schemas.md)<br/><br/>Vlastnost lze použít také pro použití více tenantů v Media Services. ID tenantů můžete umístit v úlohách. |
|Properties.Created |Datum a čas UTC při vytvoření úlohy, v ' rrrr-MM-: ssZ ' formát.|
|Properties.Description |Poskytnuté zákazníkem volitelný popis úlohy.|
|Properties.Input|Vstup pro úlohu.|
|properties.lastModified    |UTC datum a čas, kdy úloha poslední aktualizace, data v ' rrrr-MM-: ssZ ' formát.|
|Properties.Outputs|Výstupy úlohy.|
|Properties.priority    |Priorita, pomocí kterého by se měly zpracovat úlohy. Přenosy s vyšší prioritou se zpracovávají před úlohami s nižší prioritou. Pokud není nastavena, výchozí hodnota je normální.|
|Properties.state   |Aktuální stav úlohy.|
|type   |Typ prostředku.|

Kompletní definici, naleznete v tématu [úlohy](https://docs.microsoft.com/rest/api/media/jobs).

> [!NOTE]
> I když definici úlohy podporuje operace aktualizace, jsou pouze vlastnosti, které můžete upravit po odeslání úlohy popis a prioritu. Dále ke změně priority je platná pouze v případě, že úloha je stále ve stavu zařazení do fronty. Pokud úloha zahájil zpracování nebo dokončení, změna priority nemá žádný vliv.

### <a name="pagination"></a>Stránkování

Úlohy stránkování je podporována v Media Services v3.

> [!TIP]
> Odkaz na další vždy používejte k vytvoření výčtu kolekce a není závislý na konkrétní stránce velikost.

Pokud odpovědi na dotaz obsahuje mnoho položek, tato služba vrátí "\@odata.nextLink" k získání další stránky výsledků. Tímto lze na stránku prostřednictvím úplná sada výsledků. Nelze konfigurovat velikost stránky. 

Pokud úlohy jsou vytvořeny nebo odstranili stránkování prostřednictvím kolekce, změny se projeví v navrácených výsledcích (pokud tyto změny jsou součástí kolekce, která se nestáhla.) 

Následující C# příklad ukazuje, jak zobrazit výčet prostřednictvím úlohy v rámci účtu.

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

ZBÝVAJÍCÍ příklady naleznete v tématu [úloh – seznam](https://docs.microsoft.com/rest/api/media/jobs/list)


## <a name="next-steps"></a>Další postup

[Stream video soubory](stream-files-dotnet-quickstart.md)
