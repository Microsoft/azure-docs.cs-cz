---
title: Použití rozhraní API kolekce dat k vytvoření datového kanálu
description: Rozhraní API kolekce dat Azure Monitor HTTP můžete použít k přidání dat JSON do pracovního prostoru Log Analytics z libovolného klienta, který může volat REST API. Tento článek popisuje, jak v automatizovaných způsobech nahrávat data uložená v souborech.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/09/2018
ms.openlocfilehash: ab2e9c691f17b8f0891ecbc82ff42cd3529a1328
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031187"
---
# <a name="create-a-data-pipeline-with-the-data-collector-api"></a>Vytvoření datového kanálu pomocí rozhraní API kolekce dat

[Rozhraní Azure monitor API kolekce dat](data-collector-api.md) umožňuje importovat jakákoli vlastní data protokolu do pracovního prostoru Log Analytics v Azure monitor. Jedinými požadavky je, že data jsou ve formátu JSON a rozdělená na 30 MB nebo méně segmentů. Toto je zcela flexibilní mechanismus, který se dá zapojit do mnoha způsobů: od dat odesílaných přímo z vaší aplikace až po jednosměrná odeslání ad hoc. V tomto článku se dozvíte několik výchozích bodů pro běžný scénář: nutnost nahrávat data uložená v souborech pravidelně a automaticky. I když zde uvedený kanál nebude nejúčinnější ani neoptimalizovaný, je určený k tomu, aby sloužil jako výchozí bod k vytvoření vlastního produkčního kanálu.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="example-problem"></a>Příklad problému
Ve zbývající části tohoto článku prohledáme data zobrazení stránky v Application Insights. V našem hypotetickém scénáři chceme, aby se v sadě Application Insights SDK pro vlastní data, která obsahují populace všech zemí nebo oblastí na světě, shromáždily geografické údaje, které jsou ve výchozím nastavení shromažďovány, s cílem určit, kde bychom měli platit nejvíc marketingu. 

Pro tento účel používáme veřejný zdroj dat, jako je například [nadějný web](https://esa.un.org/unpd/wpp/) . Data budou mít následující jednoduché schéma:

![Příklad jednoduchého schématu](./media/create-pipeline-datacollector-api/example-simple-schema-01.png)

V našem příkladu předpokládáme, že budeme nahrávat nový soubor s daty z posledního roku, jakmile bude k dispozici.

## <a name="general-design"></a>Obecný návrh
Pro návrh našeho kanálu používáme klasickou logiku typu ETL. Architektura bude vypadat následovně:

![Architektura kanálu shromažďování dat](./media/create-pipeline-datacollector-api/data-pipeline-dataflow-architecture.png)

Tento článek nepopisuje, jak vytvářet data nebo [nahrávat je do účtu Azure Blob Storage](../../storage/blobs/storage-upload-process-images.md). Místo toho se postup vybere až do okamžiku, kdy se do objektu BLOB nahraje nový soubor. Z tohoto místa:

1. Proces zjistí, že se nahrála nová data.  V našem příkladu se používá [Aplikace logiky Azure](../../logic-apps/logic-apps-overview.md), která má k dispozici aktivační událost pro detekci nových dat odesílaných do objektu BLOB.

2. Procesor přečte Tato nová data a převede je na JSON, formát vyžadovaný Azure Monitor v tomto příkladu používáme [Azure Function](../../azure-functions/functions-overview.md) jako odlehčený a cenově efektivní způsob, jakým se spouští náš kód pro zpracování. Funkce je spuštěna stejnou aplikací logiky, kterou jsme použili k detekci nových dat.

3. Nakonec, jakmile je objekt JSON k dispozici, je odeslán do Azure Monitor. Stejná aplikace logiky odesílá data Azure Monitor pomocí předdefinované aktivity kolekce dat Log Analytics.

I když není v tomto článku popsané nastavení úložiště objektů blob, aplikace logiky nebo funkce Azure, jsou podrobné pokyny k dispozici na stránkách konkrétní produkty.

K monitorování tohoto kanálu používáme Application Insights k monitorování [detailů](../../azure-functions/functions-monitoring.md)Azure Functions tady a Azure monitor k monitorování našich podrobností aplikace logiky [tady](../../logic-apps/monitor-logic-apps-log-analytics.md). 

## <a name="setting-up-the-pipeline"></a>Nastavení kanálu
Abyste mohli kanál nastavit, nejdřív se ujistěte, že máte vytvořený a nakonfigurovaný kontejner objektů BLOB. Podobně se ujistěte, že je vytvořen Log Analytics pracovní prostor, do kterého chcete odeslat data.

## <a name="ingesting-json-data"></a>Ingestování dat JSON
Ingestování dat JSON je triviální pomocí Logic Apps a vzhledem k tomu, že není potřeba provádět žádnou transformaci, můžeme celý kanál uzavřou v jediné aplikaci logiky. Po nakonfigurování kontejneru objektů BLOB i Log Analytics pracovního prostoru vytvořte novou aplikaci logiky a nakonfigurujte ji takto:

![Příklad pracovního postupu Logic Apps](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-01.png)

Uložte aplikaci logiky a pokračujte v testování.

## <a name="ingesting-xml-csv-or-other-formats-of-data"></a>Ingestování souborů XML, CSV nebo jiných formátů dat
Logic Apps dnes nemá integrované možnosti, jak snadno transformovat XML, CSV nebo jiné typy do formátu JSON. Proto je pro dokončení této transformace nutné použít jiný způsob. Pro účely tohoto článku používáme výpočetní funkce bez serveru Azure Functions jako velmi jednoduchý a finančně přívětivý způsob. 

V tomto příkladu analyzujeme soubor CSV, ale jakýkoli jiný typ souboru se může zpracovat podobně. Jednoduše upravte část deserializace funkce Azure Functions tak, aby odrážela správnou logiku konkrétního datového typu.

1.  Po zobrazení výzvy vytvořte novou funkci Azure pomocí modulu runtime funkce V1 a na základě spotřeby.  Vyberte šablonu **triggeru http** , která cílí na C#, jako výchozí bod, který konfiguruje vaše vazby podle potřeby. 
2.  Na kartě **Zobrazit soubory** v pravém podokně vytvořte nový soubor s názvem **project.jsv** a vložte následující kód z balíčků NuGet, které používáme:

    ![Azure Functions ukázkový projekt](./media/create-pipeline-datacollector-api/functions-example-project-01.png)
    
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

3. V pravém podokně přepněte na **Run. csx** a nahraďte výchozí kód následujícím kódem. 

    >[!NOTE]
    >Pro váš projekt je nutné nahradit model záznamu (třídu "PopulationRecord") vlastními schématy dat.
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

4. Uložte funkci.
5. Otestujte funkci, abyste se ujistili, že kód funguje správně. V pravém podokně přepněte na kartu **test** a nakonfigurujte test následujícím způsobem. Umístěte odkaz na objekt BLOB s ukázkovými daty do pole **text požadavku** . Po kliknutí na tlačítko **Spustit** by se v poli **výstup** měl zobrazit výstup JSON:

    ![Testovací kód aplikací Function App](./media/create-pipeline-datacollector-api/functions-test-01.png)

Nyní potřebujeme přejít zpátky a upravit aplikaci logiky, kterou jsme začali sestavovat, aby zahrnovala data ingestovaná a převedená na formát JSON.  Pomocí návrháře zobrazení nakonfigurujte následující postup a pak uložte aplikaci logiky:

![Příklad dokončeného pracovního postupu Logic Apps](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-02.png)

## <a name="testing-the-pipeline"></a>Testování kanálu
Nyní můžete nahrát nový soubor do dříve nakonfigurovaného objektu BLOB a nechat ho monitorovat aplikace logiky. Brzy by se měla zobrazit nová instance aplikace logiky, zavolat do funkce Azure a pak úspěšně odeslat data do Azure Monitor. 

>[!NOTE]
>Může trvat až 30 minut, než se data objeví v Azure Monitor při prvním odeslání nového datového typu.


## <a name="correlating-with-other-data-in-log-analytics-and-application-insights"></a>Korelace s jinými daty v Log Analytics a Application Insights
Pokud chcete dokončit náš cíl korelace Application Insightsch zobrazení stránky s daty populace, kterou jsme z našeho vlastního zdroje dat ingestoval, spusťte následující dotaz z okna analýzy Application Insights nebo z pracovního prostoru Log Analytics:

``` KQL
app("fabrikamprod").pageViews
| summarize numUsers = count() by client_CountryOrRegion
| join kind=leftouter (
   workspace("customdatademo").Population_CL
) on $left.client_CountryOrRegion == $right.Location_s
| project client_CountryOrRegion, numUsers, Population_d
```

Výstup by měl zobrazovat dva zdroje dat, které jsou nyní připojeny.  

![Příklad korelace nepřipojených dat ve výsledku hledání](./media/create-pipeline-datacollector-api/correlating-disjoined-data-example-01.png)

## <a name="suggested-improvements-for-a-production-pipeline"></a>Navrhovaná vylepšení pro produkční kanál
V tomto článku je uveden pracovní prototyp, což je logika, kterou je možné použít k skutečnému řešení produkční kvality. Pro řešení produkční kvality doporučujeme následující vylepšení:

* Přidejte zpracování chyb a logiku opakování do své aplikace logiky a funkce.
* Přidejte logiku, aby se zajistilo, že limit volání rozhraní API 30MB/Single Log Analytics nepřekračuje. V případě potřeby rozdělení dat na menší segmenty.
* Nastavte na úložišti objektů BLOB čisticí zásadu. Po úspěšném odeslání do pracovního prostoru Log Analytics, pokud nechcete, aby nezpracovaná data zůstala k dispozici pro účely archivace, neexistuje důvod, abyste je ukládali. 
* Ověřte, zda je monitorování povoleno v celém kanálu, podle potřeby přidejte body trasování a výstrahy.
* Využijte správu zdrojového kódu ke správě kódu pro vaši funkci a aplikaci logiky.
* Zajistěte, aby následovala správná zásada správy změn. to znamená, že pokud se schéma změní, funkce a Logic Apps se odpovídajícím způsobem upraví.
* Pokud nahráváte více různých typů dat, oddělit je do jednotlivých složek v rámci kontejneru objektů BLOB a vytvořte logiku pro ventilátor na základě datového typu. 


## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o  [rozhraní API kolekce dat](data-collector-api.md) pro zápis dat do Log Analytics pracovního prostoru z libovolného klienta REST API.
