---
title: Import dat pro analýzy ve službě Azure Application Insights | Dokumentace Microsoftu
description: Importovat statická data spojovat s telemetrie aplikace nebo importovat samostatné datového proudu provádět dotazy pomocí Analytics.
services: application-insights
keywords: Otevřít schéma, import dat
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/14/2018
ms.author: mbullwin
ms.openlocfilehash: 7160232cf511226b26c15e6476ff75fcdf5ad33e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52866596"
---
# <a name="import-data-into-analytics"></a>Import dat do analýzy

Importovat žádná tabulková data do [Analytics](app-insights-analytics.md), buď ji s připojí [Application Insights](app-insights-overview.md) telemetrie z vaší aplikace, nebo tak, že je možné analyzovat jako samostatné datový proud. Analýza je vhodné řešení pro analýzu proudů časovým razítkem velkého objemu telemetrických dat výkonný dotazovací jazyk.
Data můžete importovat do analýz pomocí vlastní schéma. Není třeba použít standardní schémata Application Insights jako požadavek nebo trasování.

JSON nebo zobrazení zdroje dat (oddělovač hodnot oddělených čárkami - čárka, středník nebo kartu) můžete importovat soubory.

> [!IMPORTANT]
> Tento článek byl **zastaralé**. Doporučeným způsobem, jak dostat data do Log Analytics je prostřednictvím [rozhraní API kolekce dat Log Analytics.](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api)

Existují tři situacích užitečné import Analytics:

* **Připojte se k díky telemetrii aplikací.** Například může importovat tabulku, která mapuje adresy URL z webu titulů lépe čitelný. V Analytics můžete vytvořit sestavu grafu řídicího panelu, který zobrazuje deset nejoblíbenějších stránek na vašem webu. Nyní ji můžete zobrazit titulů místo adresy URL.
* **Korelace telemetrie vaší aplikace** s dalšími zdroji, jako jsou síťové přenosy dat serveru nebo CDN soubory protokolu.
* **Platí pro samostatné datový proud Analytics.** Application Insights Analytics je výkonný nástroj, který pracuje s zhuštěný, časovým razítkem datové proudy - mnohem lepší než SQL v mnoha případech. Pokud máte takový stream z nějakého jiného zdroje, můžete analyzovat pomocí Analytics.

Odesílání dat ke zdroji dat je snadné. 

1. (Jednou) Definujte schéma vašich dat v datovém zdroji.
2. (Pravidelně) Nahrání dat do služby Azure storage a volat rozhraní REST API upozornit, že nová data čeká na příjem. Během několika minut data jsou k dispozici pro dotaz v Analytics.

Frekvence odesílání je definována je a jak rychle přejete si vaše data k dispozici pro dotazy. To je mnohem efektivnější nahrát data do větších bloků dat, ale ne větší než 1GB.

> [!NOTE]
> *Máte spoustu zdroje dat k analýze?* [*Zvažte použití* logstash *k odeslání dat do Application Insights.*](https://github.com/Microsoft/logstash-output-application-insights)
> 

## <a name="before-you-start"></a>Než začnete

Budete potřebovat:

1. Prostředek Application Insights v Microsoft Azure.

 * Pokud chcete analyzovat data odděleně od jiných telemetrických dat, [vytvořit nový prostředek Application Insights](app-insights-create-new-resource.md).
 * Pokud jste připojení nebo porovnání dat s využitím telemetrie z aplikace, která je již nastavena pomocí Application Insights, můžete použít na prostředek pro tuto aplikaci.
 * Přispěvatel nebo vlastník přístup do tohoto prostředku.
 
2. Úložiště Azure. Odeslání do úložiště Azure a Analytics získá data z něj. 

 * Doporučujeme že vytvořit účet vyhrazeného úložiště pro objektů BLOB. Pokud objektů BLOB jsou sdíleny s jinými procesy, trvá delší dobu našich procesů ke čtení objektů BLOB.


## <a name="define-your-schema"></a>Definování schématu

Předtím, než importujete data, je nutné definovat *zdroj dat,* která určuje schéma dat.
Můžete mít až 50 datových zdrojů v jediném prostředku Application Insights

1. Spuštění Průvodce zdrojem dat. Použijte tlačítko "Přidat nový zdroj dat". Můžete také – klikněte na tlačítko nastavení v pravém horním rohu a vyberte "Zdroje dat" v rozevírací nabídce.

    ![Přidat nový zdroj dat](./media/app-insights-analytics-import/add-new-data-source.png)

    Zadejte název pro nový zdroj dat.

2. Definování formátu souborů, které odešlete.

    Můžete definovat ručně ve formátu, nebo odeslání ukázkového souboru.

    Pokud jsou data ve formátu CSV, první řádek vzorek může být záhlaví sloupců. Můžete změnit názvy polí v dalším kroku.

    Ukázka by měl obsahovat alespoň 10 řádků nebo záznamů dat s.

    Názvy sloupců nebo pole by měly mít alfanumerické názvy (bez mezer nebo interpunkční znaménka).

    ![Odeslání ukázkového souboru](./media/app-insights-analytics-import/sample-data-file.png)


3. Zkontrolujte schéma, které má obor průvodce. Pokud ji odvodit typy z ukázky, můžete potřebovat upravit odvozené typy sloupců.

    ![Zkontrolujte odvozené schématu](./media/app-insights-analytics-import/data-source-review-schema.png)

 * (Volitelné.) Nahrajte definici schématu. Podívejte se níže uvedeným formátem.

 * Vyberte časové razítko. Všechna data v Analytics musí mít pole časového razítka. Musí mít typ `datetime`, ale nemusí to být s názvem "časové razítko". Pokud data obsahují sloupec obsahující datum a čas ve formátu ISO, tuto možnost zvolte, jako sloupec časového razítka. Jinak klikněte na tlačítko "jako data dostali" a proces importu přidá pole časového razítka.

5. Vytvořte zdroj dat.

### <a name="schema-definition-file-format"></a>Formát souboru definice schématu

Místo pro úpravy schématu v uživatelském rozhraní, můžete načíst definici schématu ze souboru. Formát definice schématu je následujícím způsobem: 

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
 
Každý sloupec je identifikován umístění a název typu.

* Umístění – pro formátování souboru s oddělovači je pozice ze namapovanou hodnotu. Pro formát JSON je jpath z namapované klíč.
* Název – název zobrazený ve sloupci.
* Typ – datový typ sloupce.

> [!NOTE]
> V případě se používá ukázková data a jsou odděleny formát souboru, definici schématu musí namapovat všechny sloupce a přidávat nové sloupce na konci.
> 
> JSON umožňuje částečné mapování dat, proto nemusí být mapování každý klíč, který se nachází v ukázkových dat definici schématu s formátem JSON. Můžete také namapovat sloupce, které nejsou součástí ukázková data. 

## <a name="import-data"></a>Import dat

K importu dat, nahrajte ho do úložiště Azure, vytvořit přístupový klíč pro ni a pak proveďte volání rozhraní REST API.

![Přidat nový zdroj dat](./media/app-insights-analytics-import/analytics-upload-process.png)

Můžete provádět následující proces ručně nebo nastavit automatizovaného systému provádět v pravidelných intervalech. Budete muset postupovat podle těchto kroků pro každý blok dat, které chcete importovat.

1. Nahrání dat do [úložiště objektů blob v Azure](../storage/blobs/storage-quickstart-blobs-dotnet.md). 

 * Objekty BLOB může být libovolné velikosti až 1GB nekomprimované. Velké objekty BLOB ze stovek MB jsou ideální z hlediska výkonu.
 * Můžete je komprimovat pomocí Gzip ke zlepšení doba ukládání a latencí pro data, která mají být k dispozici pro dotaz. Použití `.gz` příponu názvu souboru.
 * Je nejvhodnější použít samostatný účet úložiště pro tento účel, aby se zabránilo volání z různých služeb zpomalení výkonu.
 * Při odesílání dat v vysokou frekvencí každých několik sekund, se doporučuje použít více než jeden účet úložiště z důvodů výkonu.

 
2. [Vytvoření klíče sdíleného přístupového podpisu pro objekt blob](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md). Klíč musí mít období vypršení platnosti o jeden den a poskytují přístup pro čtení.
3. Volání REST oznámit Application Insights, která čeká na data.

 * Koncový bod: `https://dc.services.visualstudio.com/v2/track`
 * Metoda HTTP: příspěvek
 * Datová část:

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

* `Blob URI with Shared Access Key`: Zobrazí se to v postupu pro vytvoření klíče. Je specifický pro objekt blob.
* `Schema ID`: ID schéma vygenerovaný pro definované schéma. Data v tento objekt blob by měl odpovídat schématu.
* `DateTime`: Doba, jakou se odešle požadavek, UTC. Můžeme přijmout tyto formáty: ISO8601 (například "2016-01-01 13:45:01"); Rfc822 ("středa, Dec 16 14 14:57:01 + 0000"); RFC850 ("středa, 14. prosince 16 14:57:00 UTC"); RFC1123 ("středa, 14. prosince 2016 14:57:00 + 0000").
* `Instrumentation key` váš prostředek služby Application Insights.

Data jsou k dispozici v Analytics za pár minut.

## <a name="error-responses"></a>Chybové odpovědi

* **400 o neplatném požadavku**: Určuje, že datová část požadavku je neplatný. Kontrola:
 * Správné Instrumentační klíč.
 * Hodnota doby platnosti. Měla by být čas ve standardu UTC, nyní.
 * JSON události odpovídá schématu.
* **403 Zakázáno**: jste odeslali objekt blob není přístupný. Ujistěte se, že sdílený přístupový klíč je platný a že nevypršela platnost.
* **404 Nenalezeno**:
 * Objekt blob neexistuje.
 * ID zdroje je nesprávné.

Podrobnější informace jsou k dispozici v chybové zprávě odpovědi.


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

### <a name="ingest-data"></a>Ingestace dat

Pomocí tohoto kódu pro každý objekt blob. 

```csharp
   AnalyticsDataSourceClient client = new AnalyticsDataSourceClient(); 

   var ingestionRequest = new AnalyticsDataSourceIngestionRequest("iKey", "sourceId", "blobUrlWithSas"); 

   bool success = await client.RequestBlobIngestion(ingestionRequest);
```

## <a name="next-steps"></a>Další postup

* [Prohlídka dotazovací jazyk Log Analytics](../azure-monitor/log-query/get-started-portal.md)
* Pokud používáte Logstash, použijte [modul plug-in pro Logstash pro odesílání dat do Application Insights](https://github.com/Microsoft/logstash-output-application-insights)
