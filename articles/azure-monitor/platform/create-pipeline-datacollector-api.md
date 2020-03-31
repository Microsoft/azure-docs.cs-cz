---
title: Vytvoření datového kanálu pomocí rozhraní API pro kolekcí dat
description: Rozhraní API pro shromažďování dat HTTP monitoru Azure můžete použít k přidání dat POST JSON do pracovního prostoru Analýzy protokolů z libovolného klienta, který může volat rozhraní REST API. Tento článek popisuje, jak automaticky nahrávat data uložená v souborech.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/09/2018
ms.openlocfilehash: 96c64f6a0167b678f14bf0199069ecd6b4c8d57a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055115"
---
# <a name="create-a-data-pipeline-with-the-data-collector-api"></a>Vytvoření datového kanálu pomocí rozhraní API pro kolekcí dat

Rozhraní [AZURE Monitor Data Collector API](data-collector-api.md) umožňuje importovat všechna vlastní data protokolu do pracovního prostoru Analýzy protokolů v Azure Monitoru. Jediným požadavkem je, že data jsou formátována json a rozdělena do segmentů 30 MB nebo méně. Jedná se o zcela flexibilní mechanismus, který lze připojit mnoha způsoby: od dat odesílaných přímo z vaší aplikace až po jednorázové nahrávání adhoc. Tento článek bude nastínit některé výchozí body pro běžný scénář: potřeba nahrávat data uložená v souborech v pravidelných, automatizovaných bázi. I když zde prezentovaný kanál nebude nejvýkonnější nebo jinak optimalizovaný, má sloužit jako výchozí bod pro vlastní výstavbu výrobního kanálu.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="example-problem"></a>Příklad problému
Pro zbytek tohoto článku budeme zkoumat data zobrazení stránky v Application Insights. V našem hypotetickém scénáři chceme korelovat geografické informace shromážděné ve výchozím nastavení sadou Application Insights SDK s vlastními daty obsahujícími populaci každé země nebo oblasti na světě s cílem určit, kde bychom měli utrácet nejvíce marketingových dolarů. 

K tomuto účelu používáme veřejný zdroj údajů, jako jsou [světové populační vyhlídky OSN.](https://esa.un.org/unpd/wpp/) Data budou mít následující jednoduché schéma:

![Příklad jednoduchého schématu](./media/create-pipeline-datacollector-api/example-simple-schema-01.png)

V našem příkladu předpokládáme, že jakmile bude k dispozici, nahrajeme nový soubor s údaji za poslední rok.

## <a name="general-design"></a>Obecný design
Používáme klasickou logiku typu ETL k návrhu našeho potrubí. Architektura bude vypadat takto:

![Architektura kanálu shromažďování dat](./media/create-pipeline-datacollector-api/data-pipeline-dataflow-architecture.png)

Tento článek se nebude zabývat tím, jak vytvářet data nebo [je nahrát do účtu azure blob storage](../../storage/blobs/storage-upload-process-images.md). Spíše jsme vyzvednout tok, jakmile je nový soubor nahraný do objektu blob. Odtud:

1. Proces zjistí, že byla odeslána nová data.  Náš příklad používá [aplikaci Azure Logic App](../../logic-apps/logic-apps-overview.md), která má k dispozici aktivační událost pro detekci nových dat odesílaných do objektu blob.

2. Procesor přečte tato nová data a převede je na JSON, formát vyžadovaný Azure Monitor V tomto příkladu používáme [funkci Azure](../../azure-functions/functions-overview.md) jako lehký, nákladově efektivní způsob provádění našeho kódu zpracování. Funkce je odstartována stejnou aplikací logiky, kterou jsme použili k detekci nových dat.

3. Nakonec, jakmile je k dispozici objekt JSON, je odeslán a Azure Monitor. Stejná aplikace logiky odesílá data do Azure Monitoru pomocí integrované aktivity shromažďování dat analýzy protokolů.

Zatímco podrobné nastavení úložiště objektů blob, aplikace logiky nebo funkce Azure není popsané v tomto článku, podrobné pokyny jsou k dispozici na stránkách konkrétních produktů.

Chcete-li sledovat tento kanál, používáme Application Insights ke sledování [podrobností o](../../azure-functions/functions-monitoring.md)funkcích Azure zde a Azure Monitor ke sledování podrobností o naší [aplikaci logiky zde](../../logic-apps/logic-apps-monitor-your-logic-apps-oms.md). 

## <a name="setting-up-the-pipeline"></a>Nastavení potrubí
Chcete-li nastavit kanál, nejprve se ujistěte, že máte kontejner objektů blob vytvořené a nakonfigurované. Podobně se ujistěte, že je vytvořen pracovní prostor Analýzy protokolů, do kterého chcete odesílat data.

## <a name="ingesting-json-data"></a>Ingestování dat JSON
Ingestování dat JSON je triviální s Logic Apps a protože žádná transformace nemusí proběhne, můžeme zapouzdřit celý kanál v jedné aplikaci logiky. Jakmile kontejner objektů blob a pracovní prostor Log Analytics byly nakonfigurovány, vytvořte novou aplikaci logiky a nakonfigurujte ji takto:

![Příklad pracovního postupu aplikací logiky](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-01.png)

Uložte aplikaci logiky a pokračujte v testování.

## <a name="ingesting-xml-csv-or-other-formats-of-data"></a>Ingestování XML, CSV nebo jiných formátů dat
Logic Apps dnes nemá vestavěné funkce pro snadnou transformaci XML, CSV nebo jiných typů do formátu JSON. Proto musíme použít jiný způsob, jak dokončit tuto transformaci. Pro tento článek používáme výpočetní funkce Azure Functions bez serveru jako velmi lehký a nákladově přívětivý způsob, jak toho dosáhnout. 

V tomto příkladu analyzujeme soubor CSV, ale jakýkoli jiný typ souboru může být podobně zpracován. Jednoduše upravte část funkce Azure, která bude odrážet správnou logiku pro konkrétní datový typ.

1.  Vytvořte novou funkci Azure pomocí funkce runtime v1 a na základě spotřeby po zobrazení výzvy.  Vyberte šablonu **aktivační události HTTP** zaměřenou na C# jako výchozí bod, který konfiguruje vaše vazby podle toho, co požadujeme. 
2.  Na kartě **Zobrazit soubory** v pravém podokně vytvořte nový soubor s názvem **project.json** a vložte následující kód z balíčků NuGet, které používáme:

    ![Projekt příkladu funkcí Azure](./media/create-pipeline-datacollector-api/functions-example-project-01.png)
    
    ``` JSON
    {
      "frameworks": {
        "net46":{
          "dependencies": {
            "CsvHelper": "7.1.1",
            "Newtonsoft.Json": "11.0.2"
          }  
        }  
       }  
     }  
    ```

3. Přepněte na **soubor run.csx** z pravého podokna a nahraďte výchozí kód následujícím. 

    >[!NOTE]
    >Pro váš projekt je třeba nahradit model záznamu (třída "PopulationRecord") vlastním schématem dat.
    >

    ```   
    using System.Net;
    using Newtonsoft.Json;
    using CsvHelper;
    
    class PopulationRecord
    {
        public String Location { get; set; }
        public int Time { get; set; }
        public long Population { get; set; }
    }

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        string filePath = await req.Content.ReadAsStringAsync(); //get the CSV URI being passed from Logic App
        string response = "";

        //get a stream from blob
        WebClient wc = new WebClient();
        Stream s = wc.OpenRead(filePath);         

        //read the stream
        using (var sr = new StreamReader(s))
        {
            var csvReader = new CsvReader(sr);
    
            var records = csvReader.GetRecords<PopulationRecord>(); //deserialize the CSV stream as an IEnumerable
    
            response = JsonConvert.SerializeObject(records); //serialize the IEnumerable back into JSON
        }    

        return response == null
            ? req.CreateResponse(HttpStatusCode.BadRequest, "There was an issue getting data")
            : req.CreateResponse(HttpStatusCode.OK, response);
     }  
    ```

4. Uložte svou funkci.
5. Otestujte funkci a ujistěte se, že kód funguje správně. Přepněte na kartu **Test** v pravém podokně a nakonfigurujte test následujícím způsobem. Umístěte odkaz na objekt blob s ukázkovými daty do textového pole **Vyžitý základní** text. Po klepnutí na tlačítko **Spustit**byste měli vidět výstup JSON v poli **Výstup:**

    ![Testovací kód funkčních aplikací](./media/create-pipeline-datacollector-api/functions-test-01.png)

Nyní se musíme vrátit a upravit aplikaci logiky, kterou jsme začali vytvářet dříve, aby zahrnovala data požitá a převedená do formátu JSON.  Pomocí Návrháře zobrazení nakonfigurujte následujícím způsobem a pak uložte aplikaci logiky:

![Kompletní příklad pracovního postupu Logic Apps](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-02.png)

## <a name="testing-the-pipeline"></a>Testování potrubí
Teď můžete nahrát nový soubor do objektu blob nakonfigurované dříve a nechat ho sledovat pomocí aplikace logiky. Brzy byste měli vidět novou instanci aplikace logiky kick off, volat na funkci Azure a pak úspěšně odeslat data do Azure Monitoru. 

>[!NOTE]
>Může trvat až 30 minut, než se data zobrazí v Azure Monitoru při prvním odeslání nového datového typu.


## <a name="correlating-with-other-data-in-log-analytics-and-application-insights"></a>Korelace s dalšími daty v Log Analytics a Application Insights
Chcete-li dokončit náš cíl korelace dat zobrazení stránky Přehledy aplikací s daty populace, která jsme pořídili z našeho vlastního zdroje dat, spusťte následující dotaz z okna Application Insights Analytics nebo z pracovního prostoru Log Analytics:

``` KQL
app("fabrikamprod").pageViews
| summarize numUsers = count() by client_CountryOrRegion
| join kind=leftouter (
   workspace("customdatademo").Population_CL
) on $left.client_CountryOrRegion == $right.Location_s
| project client_CountryOrRegion, numUsers, Population_d
```

Výstup by měl zobrazit dva zdroje dat nyní připojen.  

![Koreláce disjoined data v příkladu výsledku hledání](./media/create-pipeline-datacollector-api/correlating-disjoined-data-example-01.png)

## <a name="suggested-improvements-for-a-production-pipeline"></a>Navrhovaná vylepšení pro výrobní kanál
Tento článek představil funkční prototyp, logiku, za kterou lze použít na skutečné řešení kvality výroby. Pro takové řešení kvality výroby se doporučují následující vylepšení:

* Přidejte zpracování chyb a logiku opakování ve vaší aplikaci logiky a funkci.
* Přidejte logiku, která zajistí, že není překročen limit volání rozhraní API 30MB/jeden protokol analytics ingestion. V případě potřeby rozdělte data do menších segmentů.
* Nastavte zásady čištění úložiště objektů blob. Po úspěšném odeslání do pracovního prostoru Log Analytics, pokud nechcete zachovat nezpracovaná data k dispozici pro účely archivace, není důvod pokračovat v ukládání. 
* Ověřte, zda je monitorování povoleno v celém kanálu, přidání trasovacích bodů a výstrah podle potřeby.
* Využijte řízení zdrojového kódu ke správě kódu pro vaši funkci a aplikaci logiky.
* Ujistěte se, že je dodržena správná zásada správy změn, takže pokud se změní schéma, funkce a logic Apps jsou odpovídajícím způsobem upraveny.
* Pokud nahráváte více různých datových typů, oddělte je do jednotlivých složek v kontejneru objektů blob a vytvořte logiku pro fanaci logiky na základě datového typu. 


## <a name="next-steps"></a>Další kroky
Další informace o [rozhraní API pro shromažďování dat](data-collector-api.md) pro zápis dat do pracovního prostoru Log Analytics z libovolného klienta rozhraní REST API.
