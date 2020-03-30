---
title: Rozhraní HTTP Data Collector API pro monitorování Azure | Dokumenty společnosti Microsoft
description: Rozhraní API pro shromažďování dat HTTP monitoru Azure můžete použít k přidání dat POST JSON do pracovního prostoru Analýzy protokolů z libovolného klienta, který může volat rozhraní REST API. Tento článek popisuje použití rozhraní API a obsahuje příklady publikování dat pomocí různých programovacích jazyků.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/01/2019
ms.openlocfilehash: f12e9e90b99a055945c34398ff5351334c344253
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666748"
---
# <a name="send-log-data-to-azure-monitor-with-the-http-data-collector-api-public-preview"></a>Odeslání dat protokolu do azure monitoru pomocí rozhraní API pro shromažďování dat HTTP (public preview)
Tento článek ukazuje, jak pomocí rozhraní API shromažďování dat PROTOKOLU HTTP odesílat data protokolu do Azure Monitor z klienta rozhraní REST API.  Popisuje, jak formátovat data shromážděná skriptem nebo aplikací, zahrnout je do požadavku a mít tento požadavek autorizovaný službou Azure Monitor.  Příklady jsou k dispozici pro PowerShell, C# a Python.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> Rozhraní API shromažďování dat http monitoru Azure je ve verzi Public Preview.

## <a name="concepts"></a>Koncepty
Rozhraní API shromažďování dat PROTOKOLU HTTP můžete použít k odeslání dat protokolu do pracovního prostoru Analýzy protokolů v Azure Monitoru z libovolného klienta, který může volat rozhraní REST API.  Může to být runbook v Azure Automation, který shromažďuje data správy z Azure nebo jiného cloudu, nebo to může být alternativní systém správy, který používá Azure Monitor ke konsolidaci a analýze dat protokolu.

Všechna data v pracovním prostoru Log Analytics jsou uložena jako záznam s určitým typem záznamu.  Formátdat pro odeslání do rozhraní API pro shromažďování dat HTTP jako více záznamů v json.  Při odeslání dat je v úložišti vytvořen jednotlivý záznam pro každý záznam v datové části požadavku.


![Přehled kolekcí dat HTTP](media/data-collector-api/overview.png)



## <a name="create-a-request"></a>Vytvoření požadavku
Chcete-li použít rozhraní API pro sběr dat HTTP, vytvořte požadavek POST, který obsahuje data pro odeslání v zápisu objektu JavaScript (JSON).  V následujících třech tabulkách jsou uvedeny atributy, které jsou požadovány pro každý požadavek. Každý atribut podrobněji popisujeme dále v článku.

### <a name="request-uri"></a>Identifikátor URI žádosti
| Atribut | Vlastnost |
|:--- |:--- |
| Metoda |POST |
| Identifikátor URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Typ obsahu |application/json |

### <a name="request-uri-parameters"></a>Parametry identifikátoru URI požadavku
| Parametr | Popis |
|:--- |:--- |
| CustomerID |Jedinečný identifikátor pracovního prostoru Analýzy protokolů. |
| Prostředek |Název prostředku rozhraní API: /api/logs. |
| Verze rozhraní API |Verze rozhraní API pro použití s tímto požadavkem. V současné době je to 2016-04-01. |

### <a name="request-headers"></a>Hlavičky požadavku
| Hlavička | Popis |
|:--- |:--- |
| Autorizace |Autorizační podpis. Dále v článku si můžete přečíst o tom, jak vytvořit hlavičku HMAC-SHA256. |
| Typ protokolu |Zadejte typ záznamu dat, která jsou předkládána. Může obsahovat pouze písmena, číslice a podtržítko (_) a nesmí přesáhnout 100 znaků. |
| x-ms-datum |Datum zpracování požadavku ve formátu RFC 1123. |
| x-ms-AzureResourceId | ID prostředku prostředku Azure, ke kterým by měla být data přidružena. Tím se naplní [vlastnost _ResourceId](log-standard-properties.md#_resourceid) a data mají být zahrnuta do dotazů [kontextu prostředků.](design-logs-deployment.md#access-mode) Pokud toto pole není zadáno, data nebudou zahrnuta do dotazů kontextu prostředků. |
| časově generované pole | Název pole v datech, která obsahují časové razítko datové položky. Pokud zadáte pole, pak jeho obsah se použije pro **TimeGenerated**. Pokud toto pole není zadán, výchozí pro **TimeGenerated** je čas, který je pozven zprávu. Obsah pole zprávy by měl následovat formát ISO 8601 YYYY-MM-DDThh:mm:ssZ. |

## <a name="authorization"></a>Autorizace
Jakýkoli požadavek na rozhraní API shromažďování dat HTTP monitoru Azure monitoru musí obsahovat hlavičku autorizace. Chcete-li ověřit požadavek, musíte podepsat požadavek pomocí primárního nebo sekundárního klíče pro pracovní prostor, který žádost provádí. Potom předaj tento podpis jako součást požadavku.   

Zde je formát pro autorizaci záhlaví:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* je jedinečný identifikátor pracovního prostoru Log Analytics. *Podpis* je [kód hmac (Hash-based Message Authentication Code),](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) který je vytvořen z požadavku a poté vypočítán pomocí [algoritmu SHA256](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx). Potom jej zakódovat pomocí kódování Base64.

Tento formát slouží ke kódování podpisového řetězce **SharedKey:**

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

Tady je příklad řetězce podpisu:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Pokud máte řetězec podpisu, kódovat pomocí algoritmu HMAC-SHA256 na UTF-8 kódovaný řetězec a pak kódovat výsledek jako Base64. Použijte tento formát:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Ukázky v následujících částech mají ukázkový kód, který vám pomůže vytvořit hlavičku autorizace.

## <a name="request-body"></a>Text požadavku
Text zprávy musí být v JSON. Musí obsahovat jeden nebo více záznamů s názvem vlastnosti a dvojicemi hodnot v následujícím formátu. Název vlastnosti může obsahovat pouze písmena, čísla a podtržítko (_).

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

Pomocí následujícího formátu můžete dávkovat více záznamů dohromady v jednom požadavku. Všechny záznamy musí být stejného typu záznamu.

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    },
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

## <a name="record-type-and-properties"></a>Typ a vlastnosti záznamu
Vlastní typ záznamu definujete při odesílání dat prostřednictvím rozhraní API shromažďování dat HTTP monitoru Azure Monitor. V současné době nelze zapisovat data do existujících typů záznamů, které byly vytvořeny jinými datovými typy a řešeními. Azure Monitor přečte příchozí data a potom vytvoří vlastnosti, které odpovídají datovým typům zadávaných hodnot.

Každý požadavek na rozhraní API pro sběr dat musí obsahovat hlavičku **typu protokolu** s názvem pro typ záznamu. Přípona **_CL** je automaticky připojena k názvu, který zadáte, aby se odlišila od ostatních typů protokolů jako vlastní protokol. Pokud například zadáte název **MyNewRecordType**, Azure Monitor vytvoří záznam s typem **MyNewRecordType_CL**. To pomáhá zajistit, že neexistují žádné konflikty mezi uživateli vytvořené názvy typů a názvy dodávaných v současných nebo budoucích řešeních společnosti Microsoft.

K identifikaci datového typu vlastnosti Azure Monitor přidá příponu k názvu vlastnosti. Pokud vlastnost obsahuje hodnotu null, vlastnost není zahrnuta v tomto záznamu. V této tabulce je uveden datový typ vlastnosti a odpovídající přípona:

| Datový typ vlastnosti | Přípona |
|:--- |:--- |
| Řetězec |_s |
| Logická hodnota |_b |
| Double |_d |
| Datum/čas |_t |
| GUID (uloženo jako řetězec) |_g |

Datový typ, který Azure Monitor používá pro každou vlastnost, závisí na tom, jestli typ záznamu pro nový záznam už existuje.

* Pokud typ záznamu neexistuje, Azure Monitor vytvoří nový pomocí odvození typu JSON k určení datového typu pro každou vlastnost pro nový záznam.
* Pokud typ záznamu existuje, Azure Monitor se pokusí vytvořit nový záznam na základě existujících vlastností. Pokud datový typ vlastnosti v novém záznamu neodpovídá a nelze jej převést na existující typ nebo pokud záznam obsahuje vlastnost, která neexistuje, Azure Monitor vytvoří novou vlastnost, která má příslušnou příponu.

Tato položka odeslání by například vytvořila záznam se třemi vlastnostmi, **number_d**, **boolean_b**a **string_s**:

![Záznam vzorku 1](media/data-collector-api/record-01.png)

Pokud jste pak odeslali tuto další položku se všemi hodnotami formátované jako řetězce, vlastnosti by se nezměnily. Tyto hodnoty lze převést na existující datové typy:

![Záznam vzorku 2](media/data-collector-api/record-02.png)

Ale pokud jste pak provedli toto další odeslání, Azure Monitor by vytvořil nové vlastnosti **boolean_d** a **string_d**. Tyto hodnoty nelze převést:

![Záznam vzorku 3](media/data-collector-api/record-03.png)

Pokud jste pak odeslali následující položku, před vytvořením typu záznamu azure monitor by vytvořit záznam se třemi vlastnostmi, **number_s**, **boolean_s**a **string_s**. V této položce je každá počáteční hodnota formátována jako řetězec:

![Záznam vzorku 4](media/data-collector-api/record-04.png)

## <a name="reserved-properties"></a>Rezervované vlastnosti
Následující vlastnosti jsou vyhrazeny a neměly by být použity ve vlastním typu záznamu. Pokud datová část obsahuje některý z těchto názvů vlastností, zobrazí se chyba.

- Nájemce

## <a name="data-limits"></a>Omezení dat
Existují určitá omezení kolem dat zaúčtovaných do rozhraní API shromažďování dat monitorování Azure.

* Maximálně 30 MB na příspěvek do rozhraní API pro shromažďování dat monitoru Azure. Toto je limit velikosti pro jeden příspěvek. Pokud data z jednoho příspěvku, který přesahuje 30 MB, měli byste rozdělit data na menší velikosti bloků a odeslat je současně.
* Maximální limit 32 KB pro hodnoty polí. Pokud je hodnota pole větší než 32 kB, budou data zkrácena.
* Doporučený maximální počet polí pro daný typ je 50. Jedná se o praktický limit z hlediska použitelnosti a vyhledávání zkušeností.  
* Tabulka v pracovním prostoru Analýzy protokolů podporuje pouze až 500 sloupců (v tomto článku označované jako pole). 
* Maximální počet znaků pro název sloupce je 500.

## <a name="return-codes"></a>Návratové kódy
Stavový kód HTTP 200 znamená, že požadavek byl přijat ke zpracování. To znamená, že operace byla úspěšně dokončena.

V této tabulce je uvedena úplná sada stavových kódů, které může služba vrátit:

| kód | Status | Kód chyby | Popis |
|:--- |:--- |:--- |:--- |
| 200 |OK | |Požadavek byl úspěšně přijat. |
| 400 |Chybný požadavek |Neaktivnízákazník |Pracovní prostor byl uzavřen. |
| 400 |Chybný požadavek |Verze InvalidApiVersion |Zadaná verze rozhraní API nebyla službou rozpoznána. |
| 400 |Chybný požadavek |Neplatný zákaznický id |Zadané ID pracovního prostoru je neplatné. |
| 400 |Chybný požadavek |Neplatný datový formát |Byl odeslán neplatný odkaz JSON. Tělo odpovědi může obsahovat další informace o tom, jak chybu vyřešit. |
| 400 |Chybný požadavek |Neplatný typ log |Zadaný typ protokolu obsahoval speciální znaky nebo číselné znaky. |
| 400 |Chybný požadavek |Chybějícíverze Api |Nebyla zadána verze rozhraní API. |
| 400 |Chybný požadavek |Chybějící typ obsahu |Typ obsahu nebyl zadán. |
| 400 |Chybný požadavek |Chybějící typ log |Nebyl zadán požadovaný typ protokolu hodnot. |
| 400 |Chybný požadavek |UnsupportedContentType |Typ obsahu nebyl nastaven na **aplikaci/json**. |
| 403 |Forbidden |Neplatná autorizace |Službě se nepodařilo ověřit požadavek. Ověřte, zda je ID pracovního prostoru a klíč připojení platné. |
| 404 |Nebyl nalezen. | | Zadaná adresa URL je nesprávná nebo je požadavek příliš velký. |
| 429 |Příliš mnoho požadavků | | Služba zažívá velký objem dat z vašeho účtu. Opakujte požadavek později. |
| 500 |Vnitřní chyba serveru |Nespecifikovaná chyba |Služba zjistila vnitřní chybu. Opakujte požadavek. |
| 503 |Služba není k dispozici. |Služba není k dispozici. |Služba není aktuálně k dispozici pro příjem požadavků. Opakujte žádost zopakujte. |

## <a name="query-data"></a>Dotazování dat
Chcete-li dotazovat data odeslaná rozhraním API pro shromažďování dat HTTP monitoru Azure Monitor, vyhledejte záznamy s **typem,** který se rovná zadané hodnotě **LogType** připojené s **_CL**. Pokud jste například použili **mycustomlog**, vrátíte `MyCustomLog_CL`všechny záznamy s .

## <a name="sample-requests"></a>Ukázkové požadavky
V dalších částech najdete ukázky, jak odeslat data do rozhraní API shromažďování dat AZURE Monitor HTTP pomocí různých programovacích jazyků.

Pro každou ukázku postupujte takto, chcete-li nastavit proměnné pro hlavičku autorizace:

1. Na webu Azure Portal vyhledejte pracovní prostor Analýzy protokolů.
2. Vyberte **Upřesnit nastavení** a potom **připojené zdroje**.
2. Napravo od **ID pracovního prostoru**vyberte ikonu kopírování a vložte ID jako hodnotu proměnné **ID zákazníka.**
3. Napravo od **primárního klíče**vyberte ikonu kopírování a vložte ID jako hodnotu proměnné **Sdílený klíč.**

Alternativně můžete změnit proměnné pro typ protokolu a JSON data.

### <a name="powershell-sample"></a>Ukázka PowerShellu
```powershell
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
$TimeStampField = "DateValue"


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-LogAnalyticsData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-LogAnalyticsData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>Ukázka v jazyce C#
```csharp
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace OIAPIExample
{
    class ApiExample
    {
        // An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField3"":""DemoValue3"",""DemoField4"":""DemoValue4""}]";

        // Update customerId to your Log Analytics workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Azure Monitor
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            var jsonBytes = Encoding.UTF8.GetBytes(json);
            string stringToHash = "POST\n" + jsonBytes.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

        // Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

        // Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            try
            {
                string url = "https://" + customerId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";

                System.Net.Http.HttpClient client = new System.Net.Http.HttpClient();
                client.DefaultRequestHeaders.Add("Accept", "application/json");
                client.DefaultRequestHeaders.Add("Log-Type", LogName);
                client.DefaultRequestHeaders.Add("Authorization", signature);
                client.DefaultRequestHeaders.Add("x-ms-date", date);
                client.DefaultRequestHeaders.Add("time-generated-field", TimeStampField);

                System.Net.Http.HttpContent httpContent = new StringContent(json, Encoding.UTF8);
                httpContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                Task<System.Net.Http.HttpResponseMessage> response = client.PostAsync(new Uri(url), httpContent);

                System.Net.Http.HttpContent responseContent = response.Result.Content;
                string result = responseContent.ReadAsStringAsync().Result;
                Console.WriteLine("Return Result: " + result);
            }
            catch (Exception excep)
            {
                Console.WriteLine("API Post Exception: " + excep.Message);
            }
        }
    }
}

```

### <a name="python-2-sample"></a>Ukázka pythonu 2
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```
## <a name="alternatives-and-considerations"></a>Alternativy a úvahy
Zatímco rozhraní API kolekcí dat by měla zahrnovat většinu vašich potřeb ke shromažďování volných dat do protokolů Azure, existují případy, kdy může být vyžadována alternativa k překonání některých omezení rozhraní API. Všechny vaše možnosti jsou následující, hlavní aspekty zahrnuty:

| Alternativní | Popis | Nejvhodnější pro |
|---|---|---|
| [Vlastní události:](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#properties)Nativní ingestování na základě sady SDK v application insights | Application Insights, obvykle instrumentované prostřednictvím sady SDK v rámci vaší aplikace, nabízí možnost odesílat vlastní data prostřednictvím vlastních událostí. | <ul><li> Data, která jsou generována v rámci vaší aplikace, ale nejsou vyzvednuta sadou SDK prostřednictvím jednoho z výchozích datových typů (požadavky, závislosti, výjimky a tak dále).</li><li> Data, která se nejčastěji korelují s jinými daty aplikací v Application Insights </li></ul> |
| Rozhraní API kolektoru dat v protokolech monitorování Azure | Rozhraní API kolekcí dat v protokolech monitorování Azure je zcela otevřený způsob ingestování dat. Zde lze odeslat všechna data formátovaná v objektu JSON. Po odeslání bude zpracována a k dispozici v protokolech, které mají být korelovány s jinými daty v protokolech nebo proti jiným datům Application Insights. <br/><br/> Je poměrně snadné nahrát data jako soubory do objektu blob Azure, odkud budou tyto soubory zpracovány a nahrány do Log Analytics. Naleznete [v tomto](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) článku ukázkové implementace takového kanálu. | <ul><li> Data, která není nutně generována v rámci aplikace instrumentované v rámci Application Insights.</li><li> Mezi příklady patří vyhledávací tabulky a tabulky faktů, referenční data, předem agregované statistiky a tak dále. </li><li> Určeno pro data, která budou křížově odkazována na jiná data Azure Monitor (Application Insights, jiné datové typy protokolů, Centrum zabezpečení, Azure Monitor pro kontejnery/virtuální počítače a tak dále). </li></ul> |
| [Průzkumník dat Azure](https://docs.microsoft.com/azure/data-explorer/ingest-data-overview) | Azure Data Explorer (ADX) je datová platforma, která pohání Application Insights Analytics a Azure Monitor Protokoly. Nyní obecně dostupné ("GA"), pomocí datové platformy v jeho nezpracované podobě poskytuje úplnou flexibilitu (ale vyžaduje režii správy) přes clusteru (RBAC, míra uchování, schéma a tak dále). ADX poskytuje mnoho [možností ingestování,](https://docs.microsoft.com/azure/data-explorer/ingest-data-overview#ingestion-methods) včetně [souborů CSV, TSV a JSON.](https://docs.microsoft.com/azure/kusto/management/mappings?branch=master) | <ul><li> Data, která nebudou korelovat s jinými daty v rámci Application Insights nebo Protokoly. </li><li> Data vyžadující pokročilé možnosti ingestování nebo zpracování, která dnes nejsou v protokolech monitorování Azure dostupná. </li></ul> |


## <a name="next-steps"></a>Další kroky
- Pomocí [rozhraní API pro vyhledávání protokolů](../log-query/log-query-overview.md) načtěte data z pracovního prostoru Analýzy protokolů.

- Přečtěte si další informace o [tom,](create-pipeline-datacollector-api.md) jak vytvořit datový kanál pomocí rozhraní API pro shromažďování dat pomocí pracovního postupu Logic Apps na Azure Monitor.
