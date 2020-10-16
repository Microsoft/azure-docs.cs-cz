---
title: Azure Monitor rozhraní API kolekce dat HTTP | Microsoft Docs
description: Pomocí rozhraní API kolekce dat služby Azure Monitor HTTP můžete přidat data JSON do pracovního prostoru Log Analytics z libovolného klienta, který může volat REST API. Tento článek popisuje, jak používat rozhraní API a obsahuje příklady, jak publikovat data pomocí různých programovacích jazyků.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/14/2020
ms.openlocfilehash: 530aa17a165092fc9219629180c81014039c3dac
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132682"
---
# <a name="send-log-data-to-azure-monitor-with-the-http-data-collector-api-public-preview"></a>Odeslání dat protokolu do Azure Monitor pomocí rozhraní API kolekce dat HTTP (Public Preview)
V tomto článku se dozvíte, jak pomocí rozhraní API kolekce dat HTTP odesílat data protokolu Azure Monitor z klienta REST API.  Popisuje, jak formátovat data shromážděná vaším skriptem nebo aplikací, jak je zahrnout do žádosti a které vyžadují autorizaci Azure Monitor.  Příklady jsou k dispozici pro PowerShell, C# a Python.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> Rozhraní API kolekce dat Azure Monitor HTTP je ve verzi Public Preview.

## <a name="concepts"></a>Koncepty
Rozhraní API kolekce dat HTTP můžete použít k odeslání dat protokolu do pracovního prostoru Log Analytics v Azure Monitor z libovolného klienta, který může volat REST API.  Může to být sada Runbook v Azure Automation, která shromažďuje data správy z Azure nebo jiného cloudu, nebo může být alternativním systémem pro správu, který používá Azure Monitor k konsolidaci a analýze dat protokolu.

Všechna data v pracovním prostoru Log Analytics se ukládají jako záznam s konkrétním typem záznamu.  Data naformátujete tak, aby se odesílala do rozhraní API kolekce dat HTTP jako několik záznamů ve formátu JSON.  Při odeslání dat se v úložišti vytvoří jednotlivý záznam pro každý záznam v datové části požadavku.


![Přehled kolekce dat HTTP](media/data-collector-api/overview.png)



## <a name="create-a-request"></a>Vytvoření žádosti
Pokud chcete použít rozhraní API kolekce dat HTTP, vytvoříte požadavek POST, který obsahuje data, která se mají poslat v JavaScript Object Notation (JSON).  V následujících třech tabulkách jsou uvedeny atributy, které jsou požadovány pro jednotlivé požadavky. Každý atribut podrobněji popisujeme dále v článku.

### <a name="request-uri"></a>Identifikátor URI žádosti
| Atribut | Vlastnost |
|:--- |:--- |
| Metoda |POST |
| Identifikátor URI |https:// \<CustomerId\> . ODS.opinsights.Azure.com/API/logs?API-Version=2016-04-01 |
| Typ obsahu |application/json |

### <a name="request-uri-parameters"></a>Parametry identifikátoru URI žádosti
| Parametr | Popis |
|:--- |:--- |
| CustomerID |Jedinečný identifikátor pro Log Analytics pracovní prostor |
| Prostředek |Název prostředku rozhraní API:/API/Logs. |
| Verze rozhraní API |Verze rozhraní API, která se má použít s touto žádostí V současné době je to 2016-04-01. |

### <a name="request-headers"></a>Hlavičky požadavku
| Záhlaví | Popis |
|:--- |:--- |
| Autorizace |Podpis autorizace. Později v článku si můžete přečíst o tom, jak vytvořit hlavičku HMAC-SHA256. |
| Log-Type |Zadejte typ záznamu dat, která se odesílají. Může obsahovat pouze písmena, číslice a podtržítka (_) a nesmí překročit 100 znaků. |
| x-MS-Date |Datum zpracování žádosti ve formátu RFC 1123. |
| x-MS-AzureResourceId | ID prostředku prostředku Azure, ke kterému by se měla data přidružit Tím se naplní vlastnost [_ResourceId](./log-standard-columns.md#_resourceid) a povolí zahrnutí dat do dotazů [kontextu prostředků](design-logs-deployment.md#access-mode) . Pokud toto pole není zadáno, data nebudou obsažena v dotazech kontextu prostředků. |
| pole vygenerované časem | Název pole v datech, které obsahuje časové razítko datové položky. Pokud zadáte pole, bude jeho obsah použit pro **TimeGenerated**. Pokud toto pole není zadané, výchozí hodnota pro **TimeGenerated** je čas, kdy se zpráva ingestuje. Obsah pole zpráva by měl odpovídat formátu ISO 8601 RRRR-MM-DDThh: mm: ssZ. |

## <a name="authorization"></a>Autorizace
Všechny požadavky na Azure Monitor rozhraní API kolekce dat HTTP musí zahrnovat autorizační hlavičku. Chcete-li ověřit žádost, je nutné podepsat žádost buď s primárním, nebo sekundárním klíčem pracovního prostoru, který požadavek odeslal. Pak tento podpis předejte jako součást požadavku.   

Tady je formát autorizační hlavičky:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*ID pracovního prostoru* je jedinečný identifikátor pro Log Analytics pracovní prostor. *Signatura* je [ověřovací kód zprávy založený na hodnotě hash (HMAC)](/dotnet/api/system.security.cryptography.hmacsha256?view=netcore-3.1) , který je vytvořen z požadavku a následně vypočítán pomocí [algoritmu SHA256](/dotnet/api/system.security.cryptography.sha256?view=netcore-3.1). Pak ho zakódujete pomocí kódování Base64.

Použijte tento formát ke kódování řetězce podpisu **SharedKey** :

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

Pokud máte řetězec signatury, zakódovat ho pomocí algoritmu HMAC-SHA256 na řetězci kódovaném v kódování UTF-8 a výsledek zakódovat jako base64. Použijte tento formát:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Ukázky v dalších částech obsahují vzorový kód, který vám pomůže vytvořit autorizační hlavičku.

## <a name="request-body"></a>Text požadavku
Tělo zprávy musí být ve formátu JSON. Musí obsahovat jeden nebo více záznamů s páry název vlastnosti a hodnota v následujícím formátu. Název vlastnosti může obsahovat jenom písmena, číslice a podtržítka (_).

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

V jednom požadavku můžete dávkovat více záznamů pomocí následujícího formátu. Všechny záznamy musí být stejného typu záznamu.

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
Vlastní typ záznamu definujete při odesílání dat prostřednictvím rozhraní Azure Monitor API kolekce dat HTTP. V současné době nemůžete zapisovat data do existujících typů záznamů, které byly vytvořeny jinými datovými typy a řešeními. Azure Monitor přečte příchozí data a pak vytvoří vlastnosti, které odpovídají datovým typům hodnot, které zadáte.

Každý požadavek na rozhraní API kolekce dat musí obsahovat hlavičku **typu protokolu** s názvem pro daný typ záznamu. Přípona **_CL** se automaticky připojí k názvu, který zadáte, abyste ho rozlišili od ostatních typů protokolů jako vlastní protokol. Pokud například zadáte název **MyNewRecordType**, Azure monitor vytvoří záznam s typem **MyNewRecordType_CL**. To pomáhá zajistit, že mezi uživatelem vytvořenými názvy typů a s dodanými v aktuálních nebo budoucích řešeních Microsoftu nedochází k žádnému konfliktu.

Chcete-li identifikovat datový typ vlastnosti, Azure Monitor přidá příponu k názvu vlastnosti. Pokud vlastnost obsahuje hodnotu null, vlastnost není součástí daného záznamu. Tato tabulka uvádí datový typ vlastnosti a odpovídající příponu:

| Datový typ vlastnosti | Auditování |
|:--- |:--- |
| Řetězec |_s |
| Logická hodnota |_b |
| dvojité |_d |
| Datum a čas |_t |
| GUID (uloženo jako řetězec) |_g |

> [!NOTE]
> Řetězcové hodnoty, které se jeví jako identifikátory GUID, budou předány _g příponou a formátovány jako identifikátor GUID, a to i v případě, že příchozí hodnota nezahrnuje pomlčky. Například "8145d822-13a7-44ad-859c-36f31a84f6dd" a "8145d82213a744ad859c36f31a84f6dd" budou uloženy jako "8145d822-13a7-44ad-859c-36f31a84f6dd". Jediným rozdílem mezi tímto a jiným řetězcem je _g v názvu a vložení spojovníků, pokud nejsou zadány ve vstupu. 

Datový typ, který Azure Monitor používá pro jednotlivé vlastnosti závisí na tom, zda typ záznamu pro nový záznam již existuje.

* Pokud typ záznamu neexistuje, Azure Monitor vytvoří nový pomocí odvození typu JSON pro určení datového typu pro každou vlastnost nového záznamu.
* Pokud typ záznamu existuje, Azure Monitor se pokusí vytvořit nový záznam na základě existujících vlastností. Pokud datový typ pro vlastnost v novém záznamu neodpovídá a nelze jej převést na existující typ, nebo pokud záznam obsahuje vlastnost, která neexistuje, Azure Monitor vytvoří novou vlastnost, která má příslušnou příponu.

Tato položka odeslání by například vytvořila záznam se třemi vlastnostmi, **number_d**, **boolean_b**a **string_s**:

![Vzorový záznam 1](media/data-collector-api/record-01.png)

Pokud pak tuto další položku odešlete se všemi hodnotami formátovanými jako řetězce, vlastnosti se nezmění. Tyto hodnoty lze převést na stávající datové typy:

![Vzorový záznam 2](media/data-collector-api/record-02.png)

Pokud pak toto další odeslání provedete, Azure Monitor by vytvořilo nové vlastnosti **boolean_d** a **string_d**. Tyto hodnoty nejde převést:

![Vzorový záznam 3](media/data-collector-api/record-03.png)

Pokud jste potom před vytvořením typu záznamu odeslali následující položku, Azure Monitor by vytvořil záznam se třemi vlastnostmi, **number_s**, **boolean_s**a **string_s**. V této položce je každá počáteční hodnota formátována jako řetězec:

![Vzorový záznam 4](media/data-collector-api/record-04.png)

## <a name="reserved-properties"></a>Rezervované vlastnosti
Následující vlastnosti jsou rezervované a neměly by se používat v typu vlastního záznamu. Pokud datová část obsahuje některý z těchto názvů vlastností, zobrazí se chyba.

- tenant

## <a name="data-limits"></a>Omezení dat
Existují určitá omezení pro data odeslaná do Azure Monitor rozhraní API pro shromažďování dat.

* Maximálně 30 MB na post na Azure Monitor rozhraní API kolekce dat. Toto je omezení velikosti pro jeden příspěvek. Pokud data z jednoho příspěvku, který překračuje 30 MB, byste měli rozdělit do bloků dat na menší velikost a současně je odeslat.
* Maximální limit 32 KB pro hodnoty polí Pokud je hodnota pole větší než 32 KB, data se zkrátí.
* Doporučený maximální počet polí pro daný typ je 50. Toto je praktický limit z perspektivy použitelnosti a zkušeností s vyhledáváním.  
* Tabulka v pracovním prostoru Log Analytics podporuje pouze až 500 sloupců (v tomto článku se označují jako pole). 
* Maximální počet znaků pro název sloupce je 500.

## <a name="return-codes"></a>Návratové kódy
Stavový kód HTTP 200 znamená, že žádost byla přijata ke zpracování. To znamená, že operace byla úspěšně dokončena.

Tato tabulka uvádí kompletní sadu stavových kódů, které může služba vracet:

| Kód | Status | Kód chyby | Popis |
|:--- |:--- |:--- |:--- |
| 200 |OK | |Požadavek byl úspěšně přijat. |
| 400 |Chybný požadavek |InactiveCustomer |Pracovní prostor je uzavřený. |
| 400 |Chybný požadavek |InvalidApiVersion |Zadaná verze rozhraní API nebyla službou rozpoznána. |
| 400 |Chybný požadavek |InvalidCustomerId |Zadané ID pracovního prostoru je neplatné. |
| 400 |Chybný požadavek |InvalidDataFormat |Byl odeslán neplatný kód JSON. Tělo odpovědi může obsahovat další informace o tom, jak chybu vyřešit. |
| 400 |Chybný požadavek |InvalidLogType |Zadaný typ protokolu obsahuje speciální znaky nebo číslice. |
| 400 |Chybný požadavek |MissingApiVersion |Verze rozhraní API nebyla určena. |
| 400 |Chybný požadavek |MissingContentType |Typ obsahu se nezadal. |
| 400 |Chybný požadavek |MissingLogType |Nebyl zadán požadovaný typ protokolu hodnot. |
| 400 |Chybný požadavek |UnsupportedContentType |Typ obsahu nebyl nastaven na hodnotu **Application/JSON**. |
| 403 |Forbidden |InvalidAuthorization |Službě se nepovedlo ověřit požadavek. Ověřte, zda je ID pracovního prostoru a klíč připojení platné. |
| 404 |Nenalezeno | | Buď zadaná adresa URL není správná, nebo je požadavek příliš velký. |
| 429 |Příliš mnoho žádostí | | Ve službě dochází k velkému objemu dat z vašeho účtu. Opakujte prosím požadavek později. |
| 500 |Vnitřní chyba serveru |UnspecifiedError |V této službě došlo k vnitřní chybě. Opakujte prosím požadavek. |
| 503 |Služba není k dispozici |ServiceUnavailable |Služba momentálně není k dispozici pro příjem požadavků. Opakujte prosím požadavek. |

## <a name="query-data"></a>Dotazování dat
Chcete-li zadat dotaz na data odeslaná Azure Monitor rozhraní API kolekce dat HTTP, vyhledejte záznamy s **typem** , který se rovná hodnotě **LogType** , kterou jste zadali, připojenou pomocí **_CL**. Pokud jste například použili **MyCustomLog**, pak byste měli vrátit všechny záznamy s `MyCustomLog_CL` .

## <a name="sample-requests"></a>Ukázkové požadavky
V následujících částech najdete ukázky, jak odesílat data do Azure Monitor rozhraní API kolekce dat HTTP pomocí různých programovacích jazyků.

Pro každou ukázku proveďte tyto kroky a nastavte proměnné pro autorizační hlavičku:

1. V Azure Portal Najděte pracovní prostor Log Analytics.
2. Vyberte **Správa agentů**.
2. Napravo od **ID pracovního prostoru**, vyberte ikonu kopírování a vložte ID jako hodnotu proměnné **ID zákazníka** .
3. Napravo od **primárního klíče**vyberte ikonu kopírování a vložte ID jako hodnotu proměnné **sdíleného klíče** .

Alternativně můžete změnit proměnné pro typ protokolu a data JSON.

### <a name="powershell-sample"></a>Ukázka PowerShellu
```powershell
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
$TimeStampField = ""


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

### <a name="python-2-sample"></a>Ukázka Pythonu 2
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

### <a name="python-3-sample"></a>Ukázka Python 3
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
    bytes_to_hash = bytes(string_to_hash, encoding="utf-8")  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest()).decode()
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
        print('Accepted')
    else:
        print("Response code: {}".format(response.status_code))

post_data(customer_id, shared_key, body, log_type)
```


### <a name="java-sample"></a>Ukázka Java

```java

import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.springframework.http.MediaType;

import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.text.SimpleDateFormat;
import java.util.Base64;
import java.util.Calendar;
import java.util.TimeZone;

import static org.springframework.http.HttpHeaders.CONTENT_TYPE;

public class ApiExample {

  private static final String workspaceId = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
  private static final String sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";
  private static final String logName = "DemoExample";
  /*
  You can use an optional field to specify the timestamp from the data. If the time field is not specified,
  Azure Monitor assumes the time is the message ingestion time
   */
  private static final String timestamp = "";
  private static final String json = "{\"name\": \"test\",\n" + "  \"id\": 1\n" + "}";
  private static final String RFC_1123_DATE = "EEE, dd MMM yyyy HH:mm:ss z";

  public static void main(String[] args) throws IOException, NoSuchAlgorithmException, InvalidKeyException {
    String dateString = getServerTime();
    String httpMethod = "POST";
    String contentType = "application/json";
    String xmsDate = "x-ms-date:" + dateString;
    String resource = "/api/logs";
    String stringToHash = String
        .join("\n", httpMethod, String.valueOf(json.getBytes(StandardCharsets.UTF_8).length), contentType,
            xmsDate , resource);
    String hashedString = getHMAC254(stringToHash, sharedKey);
    String signature = "SharedKey " + workspaceId + ":" + hashedString;

    postData(signature, dateString, json);
  }

  private static String getServerTime() {
    Calendar calendar = Calendar.getInstance();
    SimpleDateFormat dateFormat = new SimpleDateFormat(RFC_1123_DATE);
    dateFormat.setTimeZone(TimeZone.getTimeZone("GMT"));
    return dateFormat.format(calendar.getTime());
  }

  private static void postData(String signature, String dateString, String json) throws IOException {
    String url = "https://" + workspaceId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";
    HttpPost httpPost = new HttpPost(url);
    httpPost.setHeader("Authorization", signature);
    httpPost.setHeader(CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE);
    httpPost.setHeader("Log-Type", logName);
    httpPost.setHeader("x-ms-date", dateString);
    httpPost.setHeader("time-generated-field", timestamp);
    httpPost.setEntity(new StringEntity(json));
    try(CloseableHttpClient httpClient = HttpClients.createDefault()){
      HttpResponse response = httpClient.execute(httpPost);
      int statusCode = response.getStatusLine().getStatusCode();
      System.out.println("Status code: " + statusCode);
    }
  }

  private static String getHMAC254(String input, String key) throws InvalidKeyException, NoSuchAlgorithmException {
    String hash;
    Mac sha254HMAC = Mac.getInstance("HmacSHA256");
    Base64.Decoder decoder = Base64.getDecoder();
    SecretKeySpec secretKey = new SecretKeySpec(decoder.decode(key.getBytes(StandardCharsets.UTF_8)), "HmacSHA256");
    sha254HMAC.init(secretKey);
    Base64.Encoder encoder = Base64.getEncoder();
    hash = new String(encoder.encode(sha254HMAC.doFinal(input.getBytes(StandardCharsets.UTF_8))));
    return hash;
  }

}


```


## <a name="alternatives-and-considerations"></a>Alternativy a požadavky
I když by rozhraní API kolekce dat mělo zahrnovat většinu vašich potřeb ke shromažďování dat volných formulářů do protokolů Azure, existují případy, kdy může být k překonání některých omezení v rozhraní API potřeba Alternativně. K dispozici jsou následující možnosti, které obsahují hlavní důležité požadavky:

| Jiné | Popis | Nejlépe vhodné pro |
|---|---|---|
| [Vlastní události](../app/api-custom-events-metrics.md?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#properties): ingestování na základě nativní sady SDK v Application Insights | Application Insights obvykle instrumentované prostřednictvím sady SDK v rámci aplikace, nabízí možnost odesílat vlastní data prostřednictvím vlastních událostí. | <ul><li> Data, která jsou generována v rámci aplikace, ale nejsou vyzvednuta sadou SDK prostřednictvím jednoho z výchozích datových typů (požadavky, závislosti, výjimky atd.).</li><li> Data, která jsou často korelujá s jinými daty aplikace v Application Insights </li></ul> |
| Rozhraní API kolekce dat v protokolu Azure Monitor | Rozhraní API kolekce dat v protokolu Azure Monitor představuje zcela otevřený způsob ingestování dat. Všechna data formátovaná v objektu JSON lze odeslat zde. Po odeslání se zpracuje a v protokolech bude k dispozici, aby se mohla korelovat s ostatními daty v protokolech nebo s jinými Application Insights daty. <br/><br/> Data je poměrně snadné nahrát jako soubory do objektu blob Azure Blob, ze kterého se tyto soubory zpracují a nahrají do Log Analytics. Ukázkovou implementaci takového kanálu najdete v [tomto](./create-pipeline-datacollector-api.md) článku. | <ul><li> Data, která nejsou nutně generovaná v rámci aplikace instrumentované v rámci Application Insights.</li><li> Mezi příklady patří tabulky pro vyhledávání a fakty, referenční data, předem agregované statistiky atd. </li><li> Určeno pro data, která budou odkazována na jiné Azure Monitor data (Application Insights, jiné protokoly, Security Center, Azure Monitor pro kontejnery a virtuální počítače atd.). </li></ul> |
| [Azure Data Explorer](/azure/data-explorer/ingest-data-overview) | Azure Průzkumník dat (ADX) je datová platforma, která je Application Insights analýzou a Azure Monitor protokoly. Nyní všeobecně dostupná ("GA"): pomocí datové platformy v její nezpracované podobě získáte kompletní flexibilitu (ale vyžaduje režii správy) přes cluster (RBAC, rychlost uchování, schéma atd.). ADX poskytuje mnoho [možností přijímání](/azure/data-explorer/ingest-data-overview#ingestion-methods) do příjmu včetně souborů [CSV, TSV a JSON](/azure/kusto/management/mappings?branch=master) . | <ul><li> Data, která se nevztahují na žádná jiná data v Application Insights nebo protokolech. </li><li> Data vyžadující pokročilé funkce příjmu nebo zpracování, které ještě nejsou dostupné v protokolech Azure Monitor. </li></ul> |


## <a name="next-steps"></a>Další kroky
- K načtení dat z pracovního prostoru Log Analytics použijte [rozhraní API pro prohledávání protokolu](../log-query/log-query-overview.md) .

- Přečtěte si další informace o tom, jak [vytvořit datový kanál s rozhraním API kolekce dat](create-pipeline-datacollector-api.md) pomocí Logic Apps pracovní postup k Azure monitor.