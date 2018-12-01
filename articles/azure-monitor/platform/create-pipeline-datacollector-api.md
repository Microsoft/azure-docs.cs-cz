---
title: Vytvoření datového kanálu pomocí rozhraní API Azure Log Analytics Data kolekce | Dokumentace Microsoftu
description: V Log Analytics HTTP rozhraní API kolekce dat slouží k přidání dat POST JSON do úložiště Log Analytics z libovolného klienta, která může volat rozhraní REST API. Tento článek popisuje, jak nahrát data uložená v souborech automatizovaným způsobem.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/09/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 453101b1f5368dba9aa9dbfc89f5b893d42a0542
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52684590"
---
# <a name="create-a-data-pipeline-with-the-data-collector-api"></a>Vytvoření datového kanálu pomocí rozhraní API kolekce dat

[Rozhraní API kolekce dat Log Analytics](../../log-analytics/log-analytics-data-collector-api.md) umožňuje importovat všechny vlastní data do Log Analytics. Jediným požadavkem je, že data ve formátu JSON a rozdělení do 30 MB nebo méně segmenty. Toto je zcela flexibilní mechanismus, který může být připojeno do mnoha způsoby: z dat odesílaných přímo z vaší aplikace, odešle do jednorázové ad hoc. V tomto článku se popisují některé počáteční body pro běžné scénáře: potřeba nahrát data uložená v souborech na základě pravidelné a automatizované. Během kanálu uvedené tady nesmí být většina výkonné nebo jinak k optimalizaci, je určené k sloužit jako výchozí bod na vytváření procesních toků pro produkční vlastní.

## <a name="example-problem"></a>Příklad problému
Pro zbývající část tohoto článku prozkoumáme data o zobrazeních stránek ve službě Application Insights. V tomto scénáři hypotetické chceme korelovat zeměpisné údaje shromážděné ve výchozím nastavení Application Insights SDK na vlastní data obsahují počet obyvatel každé zemi na světě, s cílem identifikovat, kde jsme by měl být útraty na maximum marketingové dolarech. 

Použijeme veřejný zdroj dat, jako [zrušení World naplnění potenciální zákazníky](https://esa.un.org/unpd/wpp/) pro tento účel. Data budou mít tyto jednoduché schéma:

![Příklad jednoduché schéma](./media/create-pipeline-datacollector-api/example-simple-schema-01.png)

V našem příkladu předpokládáme, že jsme nahraje nový soubor s daty nejzazší rok, jakmile bude k dispozici.

## <a name="general-design"></a>Obecné návrhu
Používáme classic ETL typ logiky navrhnout našeho kanálu. Architektura bude vypadat takto:

![Architektura kanálu kolekce dat](./media/create-pipeline-datacollector-api/data-pipeline-dataflow-architecture.png)

Tento článek se nevztahuje na tom, jak vytvořit data nebo [nahrajte ho do účtu služby Azure Blob Storage](../../storage/blobs/storage-upload-process-images.md). Místo toho vybíráme tok poté, co je do objektu blob nahraje nový soubor. Z tohoto:

1. Proces zjistí, že nová data se odeslal.  Naše Ukázka používá [aplikace logiky Azure](../../logic-apps/logic-apps-overview.md), který má k dispozici triggeru pro rozpoznávání nových dat odesílaných do objektu blob.

2. Procesor těmito novými daty čte a převede do formátu JSON, formát vyžadované Log Analytics.  V tomto příkladu vytvoříme s využitím [funkce Azure Functions](../../azure-functions/functions-overview.md) jako jednoduché, nákladově efektivní způsob, jak provádění kódu pro zpracování. Funkce je vydáno ve stejné aplikaci logiky, kterou jste použili ke zjištění a nová data.

3. A konečně Jakmile je objekt JSON je k dispozici, zasláním ke službě Log Analytics. Aplikace logiky odešle data do Log Analytics pomocí integrovaného v aktivitě kolekce dat Log Analytics.

Zatímco podrobné nastavení úložiště objektů blob, funkce Azure nebo aplikace logiky není popsané v tomto článku, podrobné pokyny jsou k dispozici na stránkách konkrétní produkty.

Sledovat tento kanál, použijeme k monitorování naší funkce Azure Application Insights [podrobnosti tady](../../azure-functions/functions-monitoring.md)a Log Analytics k monitorování naší aplikace logiky [podrobnosti tady](../../logic-apps/logic-apps-monitor-your-logic-apps-oms.md). 

## <a name="setting-up-the-pipeline"></a>Nastavení kanálu
Nastavení kanálu, nejprve zkontrolujte, zda že máte vytvořený a nakonfigurovaný kontejneru objektů blob. Podobně Ujistěte se, že se vytvoří pracovní prostor Log Analytics, ve kterém chcete odesílat data do.

## <a name="ingesting-json-data"></a>Příjem dat JSON
Příjem dat JSON je jednoduché s Logic Apps a protože žádná transformace musí proběhnout, jsme encase celý kanál v jedné aplikaci logiky. Po dokončení konfigurace kontejneru objektů blob a pracovní prostor Log Analytics vytvořit novou aplikaci logiky a nakonfigurujte následujícím způsobem:

![Příklad pracovního postupu aplikace logiky](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-01.png)

Uložení aplikace logiky a pokračovat a otestovat ho.

## <a name="ingesting-xml-csv-or-other-formats-of-data"></a>Ingestovat XML, CSV nebo jiných formátů data
Logic Apps ještě dnes nemá vestavěné možnosti snadno transformovat XML, CSV nebo jiných typů do formátu JSON. Proto musíme použít jiný způsob k dokončení této transformace. Pro účely tohoto článku použijeme výpočetní prostředí funkce Azure Functions jako způsob velmi jednoduchý a podporou náklady tohoto postupu. 

V tomto příkladu se nám analyzovat soubor CSV, ale můžete podobně zpracovat jakýkoli typ souboru. Při deserializaci část funkce Azure Functions tak, aby odrážely správnou logiku pro určitý datový typ. stačí upravte.

1.  Vytvořit novou funkci Azure, pomocí funkce modulu runtime v1 a založenou na skutečné spotřebě při zobrazení výzvy.  Vyberte **triggeru HTTP** šablonu zacílenou na C# jako výchozí bod, který konfiguruje vazby, můžeme vyžadovat. 
2.  Z **zobrazit soubory** kartu v pravém podokně, vytvořte nový soubor s názvem **project.json** a vložte následující kód z balíčků NuGet, které používáme:

    ![Příklad projektu Azure Functions](./media/create-pipeline-datacollector-api/functions-example-project-01.png)
    
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

3. Přepnout na **run.csx** v pravém podokně a nahraďte kód následujícím kódem. 

    >[!NOTE]
    >Pro váš projekt budete muset nahraďte vlastní schéma dat záznamů modelu (třída "PopulationRecord").
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

4. Uložte vaši funkci.
5. Test funkce, ujistěte se, že kód funguje správně. Přepněte **testování** kartu v pravém podokně, konfigurace testu následujícím způsobem. Umístit odkaz na objekt blob se ukázkových dat. **text žádosti** textového pole. Po kliknutí na tlačítko **spustit**, JSON by se měla zobrazit výstup v **výstup** pole:

    ![Aplikace Function App testování kódu](./media/create-pipeline-datacollector-api/functions-test-01.png)

Nyní potřebujeme vrátíte k úpravě aplikace logiky, můžeme začít vytvářet dříve zahrnout data ingestují a převést do formátu JSON.  Pomocí návrháře zobrazení, nakonfigurujte následujícím způsobem a pak uložte aplikaci logiky:

![Kompletní příklad pracovního postupu Logic Apps](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-02.png)

## <a name="testing-the-pipeline"></a>Testování kanálu
Teď můžete nahrát nový soubor do objektu blob nakonfigurovali v předchozích krocích a jeho monitoruje vaše aplikace logiky. By měla brzy, najdete v článku novou instanci aplikace logiky odstartoval, vyžadují pro vaši funkci Azure functions a úspěšně odeslat data do Log Analytics. 

>[!NOTE]
>Může trvat až 30 minut, než se zobrazí v Log Analytics první, když odesíláte nový datový typ data.


## <a name="correlating-with-other-data-in-log-analytics-and-application-insights"></a>Korelace s jinými daty v Log Analytics a Application Insights
K dokončení cíl korelaci dat zobrazení stránky služby Application Insights s daty naplnění, které jsme ingestována z naší vlastní zdroj dat, spusťte následující dotaz z Application Insights Analytics období nebo pracovního prostoru Log Analytics:

``` KQL
app("fabrikamprod").pageViews
| summarize numUsers = count() by client_CountryOrRegion
| join kind=leftouter (
   workspace("customdatademo").Population_CL
) on $left.client_CountryOrRegion == $right.Location_s
| project client_CountryOrRegion, numUsers, Population_d
```

Výstup by měl zobrazit dva datové zdroje teď připojené.  

![Korelace narušenému důvěrnému data v příkladu výsledek vyhledávání](./media/create-pipeline-datacollector-api/correlating-disjoined-data-example-01.png)

## <a name="suggested-improvements-for-a-production-pipeline"></a>Navržených vylepšení pro výrobu kanálu
Tento článek zobrazí prototyp pracovní nedají použít logice true řešení produkční kvality. Pro toto vysoce kvalitní řešení se doporučuje následující vylepšení:

* Přidání zpracování chyb a logika opakování v aplikaci logiky a funkce.
* Přidejte logiku a ujistěte se, že není překročen limit volání rozhraní API služby Log Analytics Ingestování 30MB/jednou. V případě potřeby rozdělte data do menších částí.
* Nastavení zásady čištění v úložišti objektů blob. Jakmile se úspěšně odesílají do Log Analytics, pokud byste chtěli zachovat nezpracovaná data, která je k dispozici pro účely archivace, neexistuje žádný důvod k dalšímu ukládání ho. 
* Ověření monitorování je povoleno napříč úplný kanál přidání body sledování a výstrahy podle potřeby.
* Využijte správy zdrojového kódu ke správě kódu pro funkce a aplikace logiky.
* Ujistěte se, že je a potom zásadu správy správné změn, tak, že pokud se změní schéma, funkce a Logic Apps jsou upraveny.
* Pokud nahráváte více různých datových typů, oddělit do jednotlivých složek v kontejneru objektů blob a vytvořit logiku pro ventilátor logiky si podle datového typu. 


## <a name="next-steps"></a>Další postup
Další informace o [rozhraní API kolekce dat](../../log-analytics/log-analytics-data-collector-api.md) zapsat data do Log Analytics z jakéhokoli klienta REST API.
