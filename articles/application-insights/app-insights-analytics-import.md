---
title: Import dat k analýze ve službě Azure Application Insights | Microsoft Docs
description: Importovat statických dat pro připojení s telemetrií aplikace, nebo importovat samostatné datový proud do dotazu s Analytics.
services: application-insights
keywords: Otevřete schéma, import dat
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 10/04/2017
ms.author: mbullwin
ms.openlocfilehash: 688d620e19a8a6f536d134d9c4d7c837ec06bbdc
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293617"
---
# <a name="import-data-into-analytics"></a>Importovat data do Analytics

Importovat žádná tabulková data do [Analytics](app-insights-analytics.md), buď propojit je s [Application Insights](app-insights-overview.md) telemetrie z vaší aplikace, nebo tak, aby je bylo možné analyzovat jako samostatné datový proud. Analytics je účinný dotazovací jazyk, který je vhodné řešení pro analýzu proudů označen časovým razítkem vysoký počet telemetrie.

Data můžete importovat do analýzy pomocí vlastního schématu. Nemá používat standardní schémata Application Insights, jako je například požadavek nebo trasování.

Můžete importovat JSON nebo DSV (oddělovač oddělovači - čárku, středník nebo kartě) soubory.

Existují tři situacích výhodné import do Analytics:

* **Připojte se telemetrie aplikace.** Mohli byste například importovat tabulku, která mapuje adresy URL vašeho webu na titulů srozumitelnější. V analýzy můžete vytvořit sestavu grafu řídicího panelu, která obsahuje deset nejoblíbenější stránky ve vašem webu. Nyní ji můžete zobrazují názvy stránky místo adresy URL.
* **Korelovat telemetrii aplikace** s jinými zdroji například síťových přenosů dat serveru nebo CDN soubory protokolu.
* **Analýza platí pro samostatné datového proudu.** Application Insights Analytics je výkonný nástroj, který pracuje s zhuštěné, označen časovým razítkem datové proudy - lépe než SQL v mnoha případech. Pokud máte datový proud z z jiného zdroje, můžete ho s Analytics analyzovat.

Odesílání dat do zdroje dat je snadné. 

1. (Jednou) Definování schématu dat v datovém zdroji.
2. (Pravidelně) Nahrání dat do úložiště Azure a volání rozhraní REST API nám, které nová data se čeká na přijímání oznámení. Během několika minut je k dispozici pro dotaz v Analytics data.

Frekvence odesílání je definována vy a jak rychlý chcete, aby vaše data k dispozici pro dotazy. Je efektivnější odesílat data v větší bloky dat, ale není větší než 1GB.

> [!NOTE]
> *Máte velký počet zdrojů dat k analýze?* [*Zvažte použití* logstash *pro odeslání dat do služby Application Insights.*](https://github.com/Microsoft/logstash-output-application-insights)
> 

## <a name="before-you-start"></a>Než začnete

Budete potřebovat:

1. Prostředek Application Insights v Microsoft Azure.

 * Pokud chcete analyzovat data odděleně od jiných telemetrie [vytvořte nový prostředek Application Insights](app-insights-create-new-resource.md).
 * Pokud chcete propojení nebo porovnávání svá data pomocí telemetrie z aplikace, která je již nastavena pomocí služby Application Insights, můžete použít na prostředek pro tuto aplikaci.
 * Přispěvatel nebo vlastníka přístup k prostředku.
 
2. Úložiště Azure. Nahrát do úložiště Azure a analýzy získá vaše data z ní. 

 * Doporučujeme že vytvořit účet vyhrazeného úložiště pro objektů BLOB. Pokud objektů BLOB jsou sdíleny s jinými procesy, trvá déle pro naše procesy ke čtení objektů BLOB.


## <a name="define-your-schema"></a>Definování schématu

Před importem dat, je nutné definovat *zdroje dat,* který určuje schéma vaše data.
Může mít až 50 zdroje dat v jediném prostředku Application Insights

1. Spuštění Průvodce zdrojem dat. Použijte tlačítko "Přidat nový zdroj dat". Případně – klikněte na tlačítko nastavení v pravém horním rohu a v rozevírací nabídce vyberte "Zdroje dat".

    ![Přidat nový zdroj dat](./media/app-insights-analytics-import/add-new-data-source.png)

    Zadejte název nového zdroje dat.

2. Definování formátu souborů, které odešlete.

    Můžete definovat formát ručně, nebo odeslání ukázkového souboru.

    Pokud jsou data ve formátu CSV, může být první řádek v ukázkovém záhlaví sloupců. Můžete změnit názvy polí v dalším kroku.

    Ukázka by měla obsahovat alespoň 10 řádků nebo záznamů dat s.

    Sloupec nebo pole názvy by měly mít alfanumerické názvy (bez mezer a interpunkce).

    ![Odeslání ukázkového souboru](./media/app-insights-analytics-import/sample-data-file.png)


3. Zkontrolujte schéma, které má potom průvodce. Pokud ho odvodit typů z ukázku, můžete upravit odvozené typy sloupců.

    ![Zkontrolujte odvozené schématu](./media/app-insights-analytics-import/data-source-review-schema.png)

 * (Volitelné.) Nahrajte definici schématu. Viz níže uvedeným formátem.

 * Vyberte časové razítko. Všechna data v Analytics musí mít pole časového razítka. Musí mít typ `datetime`, ale nemá s názvem 'časové razítko'. Pokud data obsahují sloupec obsahující datum a čas ve formátu ISO, zvolte to jako sloupec časového razítka. Jinak, vyberte "jako data doručen" a proces importu přidá pole časového razítka.

5. Vytvoření zdroje dat.

### <a name="schema-definition-file-format"></a>Formát souboru definice schématu

Neupravujte schématu v uživatelském rozhraní, můžete načíst definici schématu ze souboru. Formát definice schématu je následující: 

Formát s oddělovačem 
```
[ 
    {"location": "0", "name": "RequestName", "type": "string"}, 
    {"location": "1", "name": "timestamp", "type": "datetime"}, 
    {"location": "2", "name": "IPAddress", "type": "string"} 
] 
```

Formát JSON 
```
[ 
    {"location": "$.name", "name": "name", "type": "string"}, 
    {"location": "$.alias", "name": "alias", "type": "string"}, 
    {"location": "$.room", "name": "room", "type": "long"} 
]
```
 
Každý sloupec je určený podle umístění, název a typ. 

* Umístění – souboru s oddělovači naformátujte ho je umístění namapované hodnoty. Pro formát JSON je jpath namapované klíče.
* Název – zobrazovaný název sloupce.
* Typ – datový typ sloupce.
 
V případě, že byl použit ukázková data a jsou odděleny formát souboru, musí definice schématu mapovat všechny sloupce a přidejte nové sloupce na konci. 

JSON umožňuje částečné mapování dat, proto definice schématu formátu JSON nemá pro každý klíč, který se nachází v ukázková data mapování. Také ho můžete namapovat sloupce, které nejsou součástí ukázková data. 

## <a name="import-data"></a>Import dat

K importu dat, nahrajte ho do úložiště Azure, vytvořte přístupový klíč pro tuto a pak proveďte volání rozhraní REST API.

![Přidat nový zdroj dat](./media/app-insights-analytics-import/analytics-upload-process.png)

Můžete provést následující proces ručně, nebo nastavit automatizovaný systém to udělat v pravidelných intervalech. Musíte provést následující kroky pro každý blok dat, které chcete importovat.

1. Nahrání dat do [úložiště objektů blob Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md). 

 * Objekty BLOB může být libovolné velikosti až 1GB nekomprimovaným. Velké objekty BLOB stovek MB jsou ideální z hlediska výkonu.
 * Můžete je komprimovat s Gzip ke zlepšení doba nahrávání a latence pro data, která mají být k dispozici pro dotaz. Použití `.gz` příponu názvu souboru.
 * Je nejvhodnější použít účet samostatného úložiště pro tento účel, aby se zabránilo volání z jiné služby zpomalení výkonu.
 * Při odesílání dat v vysoká frekvence, každých několik sekund, se doporučuje použít více než jeden účet úložiště, z důvodů výkonu.

 
2. [Vytvoření klíče sdíleného přístupového podpisu pro tento objekt blob](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md). Klíč by měl mít jeden den po dobu vypršení platnosti a poskytovat přístup pro čtení.
3. Volání REST oznámit Application Insights, čekající data.

 * Koncový bod: `https://dc.services.visualstudio.com/v2/track`
 * Metoda HTTP: POST
 * Datové části:

```JSON

    {
       "data": {
            "baseType":"OpenSchemaData",
            "baseData":{
               "ver":"2",
               "blobSasUri":"<Blob URI with Shared Access Key>",
               "sourceName":"<Schema ID>",
               "sourceVersion":"1.0"
             }
       },
       "ver":1,
       "name":"Microsoft.ApplicationInsights.OpenSchema",
       "time":"<DateTime>",
       "iKey":"<instrumentation key>"
    }
```

Zástupné symboly jsou:

* `Blob URI with Shared Access Key`: Zobrazí to v postupu pro vytváření klíčů. Je specifická pro objekt blob.
* `Schema ID`: ID schématu vygenerované definované schéma. Data v tohoto objektu blob musí být v souladu schématu.
* `DateTime`: Čas, kdy je odeslána žádost, UTC. Můžeme přijmout tyto formáty: ISO8601 (jako je "2016-01-01 13:45:01"); Rfc822 ("ST, 14 16 DEC – 14:57:01 + 0000"); RFC850 ("středa, 14. prosince 16 UTC 14:57:00"); RFC1123 ("st 14 Dec 2016 14:57:00 + 0000").
* `Instrumentation key` z prostředku Application Insights.

Data jsou k dispozici v Analytics po několika minutách.

## <a name="error-responses"></a>Chybové odpovědi

* **požadavek je 400 nesprávný**: označuje, že datová část požadavku je neplatné. Kontrola:
 * Klíč instrumentace správné.
 * Hodnota doby platnosti. Mělo by být čas nyní ve standardu UTC.
 * JSON události odpovídá schématu.
* **403 Zakázáno**: Objekt blob, které jste odeslali není dostupný. Ujistěte se, že je sdílený přístupový klíč platný a zda nevypršela platnost.
* **404 nebyl nalezen**:
 * Objekt blob neexistuje.
 * ID zdroje je nesprávné.

Podrobnější informace najdete v chybové zprávě odpovědi.


## <a name="sample-code"></a>Ukázka kódu

Tento kód používá [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/9.0.1) balíček NuGet.

### <a name="classes"></a>Třídy

```csharp
namespace IngestionClient 
{ 
    using System; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceIngestionRequest 
    { 
        #region Members 
        private const string BaseDataRequiredVersion = "2"; 
        private const string RequestName = "Microsoft.ApplicationInsights.OpenSchema"; 
        #endregion Members 

        public AnalyticsDataSourceIngestionRequest(string ikey, string schemaId, string blobSasUri, int version = 1) 
        { 
            Ver = version; 
            IKey = ikey; 
            Data = new Data 
            { 
                BaseData = new BaseData 
                { 
                    Ver = BaseDataRequiredVersion, 
                    BlobSasUri = blobSasUri, 
                    SourceName = schemaId, 
                    SourceVersion = version.ToString() 
                } 
            }; 
        } 


        [JsonProperty("data")] 
        public Data Data { get; set; } 

        [JsonProperty("ver")] 
        public int Ver { get; set; } 

        [JsonProperty("name")] 
        public string Name { get { return RequestName; } } 

        [JsonProperty("time")] 
        public DateTime Time { get { return DateTime.UtcNow; } } 

        [JsonProperty("iKey")] 
        public string IKey { get; set; } 
    } 

    #region Internal Classes 

    public class Data 
    { 
        private const string DataBaseType = "OpenSchemaData"; 

        [JsonProperty("baseType")] 
        public string BaseType 
        { 
            get { return DataBaseType; } 
        } 

        [JsonProperty("baseData")] 
        public BaseData BaseData { get; set; } 
    } 


    public class BaseData 
    { 
        [JsonProperty("ver")] 
        public string Ver { get; set; } 

        [JsonProperty("blobSasUri")] 
        public string BlobSasUri { get; set; } 

        [JsonProperty("sourceName")] 
        public string SourceName { get; set; } 

        [JsonProperty("sourceVersion")] 
        public string SourceVersion { get; set; } 
    } 

    #endregion Internal Classes 
} 


namespace IngestionClient 
{ 
    using System; 
    using System.IO; 
    using System.Net; 
    using System.Text; 
    using System.Threading.Tasks; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceClient 
    { 
        #region Members 
        private readonly Uri endpoint = new Uri("https://dc.services.visualstudio.com/v2/track"); 
        private const string RequestContentType = "application/json; charset=UTF-8"; 
        private const string RequestAccess = "application/json"; 
        #endregion Members 

        #region Public 

        public async Task<bool> RequestBlobIngestion(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            HttpWebRequest request = WebRequest.CreateHttp(endpoint); 
            request.Method = WebRequestMethods.Http.Post; 
            request.ContentType = RequestContentType; 
            request.Accept = RequestAccess; 

            string notificationJson = Serialize(ingestionRequest); 
            byte[] notificationBytes = GetContentBytes(notificationJson); 
            request.ContentLength = notificationBytes.Length; 

            Stream requestStream = request.GetRequestStream(); 
            requestStream.Write(notificationBytes, 0, notificationBytes.Length); 
            requestStream.Close(); 

            try 
            { 
                using (var response = (HttpWebResponse)await request.GetResponseAsync())
                {
                    return response.StatusCode == HttpStatusCode.OK;
                }
            } 
            catch (WebException e) 
            { 
                HttpWebResponse httpResponse = e.Response as HttpWebResponse; 
                if (httpResponse != null) 
                { 
                    Console.WriteLine( 
                        "Ingestion request failed with status code: {0}. Error: {1}", 
                        httpResponse.StatusCode, 
                        httpResponse.StatusDescription); 
                    return false; 
                }
                throw; 
            } 
        } 
        #endregion Public 

        #region Private 
        private byte[] GetContentBytes(string content) 
        { 
            return Encoding.UTF8.GetBytes(content);
        } 


        private string Serialize(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            return JsonConvert.SerializeObject(ingestionRequest); 
        } 
        #endregion Private 
    } 
} 
```

### <a name="ingest-data"></a>Příjem dat

Pomocí tohoto kódu pro každý objekt blob. 

```csharp
   AnalyticsDataSourceClient client = new AnalyticsDataSourceClient(); 

   var ingestionRequest = new AnalyticsDataSourceIngestionRequest("iKey", "sourceId", "blobUrlWithSas"); 

   bool success = await client.RequestBlobIngestion(ingestionRequest);
```

## <a name="next-steps"></a>Další postup

* [Prohlídka dotazovací jazyk analýzy protokolů](app-insights-analytics-tour.md)
* Pokud používáte Logstash, použijte [Logstash modulu plug-in pro odesílání dat do služby Application Insights](https://github.com/Microsoft/logstash-output-application-insights)
