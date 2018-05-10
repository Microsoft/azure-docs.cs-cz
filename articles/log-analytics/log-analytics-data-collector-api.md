---
title: Protokolu kolekcí dat protokolu HTTP Analytics rozhraní API | Microsoft Docs
description: Log Analytics HTTP dat kolekce API můžete přidat POST JSON data do úložiště analýzy protokolů z libovolného klienta, který můžete volat rozhraní REST API. Tento článek popisuje, jak použít rozhraní API a obsahuje příklady, jak publikovat data pomocí různých programovacích jazyků.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: ''
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: bwren
ms.openlocfilehash: d42069e8ed72a834973b56df55488955d62e71f2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="send-data-to-log-analytics-with-the-http-data-collector-api-public-preview"></a>Odesílání dat k analýze protokolů s rozhraním API týkající se kolekce dat protokolu HTTP (verze public preview)
Tento článek ukazuje, jak používat rozhraní API sady kolekcí dat protokolu HTTP k odesílání dat k analýze protokolů z klienta pro REST API.  Popisuje, jak formátu data shromažďovaná společností skriptu nebo aplikaci, její zahrnutí do žádost a mít této žádosti autorizovat analýzy protokolů.  Příklady jsou uvedené pro prostředí PowerShell, C# a Python.

> [!NOTE]
> Log Analytics HTTP dat kolekce API je ve verzi public preview.

## <a name="concepts"></a>Koncepty
Rozhraní API sady kolekcí dat protokolu HTTP můžete použít k odesílání dat k analýze protokolů z libovolného klienta, který můžete volat rozhraní REST API.  To může být sady runbook ve službě Azure Automation, který shromažďuje správy dat z Azure nebo ve jiný cloud nebo může být alternativní správy systému, který používá analýzy protokolů konsolidovat a analyzovat data.

Všechna data v úložišti analýzy protokolů uloženo jako záznam s konkrétní typ záznamu.  Formátování dat odesílat na rozhraní API sady kolekcí dat protokolu HTTP jako více záznamů ve formátu JSON.  Při odesílání dat jednotlivých záznamů je vytvořen v úložišti pro každý záznam v datová část požadavku.


![Přehled kolekcí dat protokolu HTTP](media/log-analytics-data-collector-api/overview.png)



## <a name="create-a-request"></a>Vytvořit žádost o
Chcete-li použít rozhraní API sady kolekcí dat protokolu HTTP, vytvořte požadavek POST, která obsahuje data k odeslání v JavaScript Object Notation (JSON).  Následující tři tabulky obsahují atributy, které jsou požadovány pro každý požadavek. Jsme popisují každý atribut podrobněji později v článku.

### <a name="request-uri"></a>Identifikátor URI žádosti
| Atribut | Vlastnost |
|:--- |:--- |
| Metoda |POST |
| URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Typ obsahu |application/json |

### <a name="request-uri-parameters"></a>Parametry identifikátoru URI požadavku
| Parametr | Popis |
|:--- |:--- |
| CustomerID |Jedinečný identifikátor pro pracovní prostor analýzy protokolů. |
| Prostředek |Název prostředku rozhraní API: / api/protokoly. |
| Verze rozhraní API |Verze rozhraní API používat s touto žádostí. V současné době je 2016-04-01. |

### <a name="request-headers"></a>Hlavičky požadavku
| Záhlaví | Popis |
|:--- |:--- |
| Autorizace |Podpis autorizace. Dále v tomto článku si můžete přečíst o tom, jak vytvořit hlavičku HMAC SHA256. |
| Typ protokolu |Zadejte typ záznamu dat, která je odesílána. Typ protokolu v současné době podporuje pouze alfanumerické znaky. Nepodporuje se číslice nebo speciální znaky. Omezení velikosti pro tento parametr je 100 znaků. |
| x-ms-date |Datum, kdy byl požadavek zpracovat, v dokumentu RFC 1123 formátu. |
| čas generované pole |Název pole v datech, která obsahuje časové razítko datová položka. Pokud určíte pole a její obsah se používají pro **TimeGenerated**. Pokud toto pole není určena, výchozí hodnota pro **TimeGenerated** je čas, který je konzumována zprávy. Obsah zprávy pole by mělo vyhovovat formátu ISO 8601 rrrr-MM-ddTHH. |

## <a name="authorization"></a>Autorizace
Každá žádost o Log Analytics HTTP dat kolekce API musí obsahovat hlavičku autorizace. K ověření požadavku, musíte se odhlásit požadavek s primární nebo sekundární klíč pro pracovní prostor, který je vytvoření požadavku. Pak předejte tento podpis jako součást požadavku.   

Tady je formát pro hlavičku autorizace:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* je jedinečný identifikátor pro pracovní prostor analýzy protokolů. *Podpis* je [Hash-based ověřování kódu metoda HMAC (Message)](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) , se vytvářejí na základě požadavku a potom vypočítaného pomocí [algoritmus SHA256](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx). Potom můžete zakódovat je pomocí kódování Base64.

Použijte tento formát ke kódování **SharedKey** podpis řetězec:

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

Tady je příklad řetězce podpis:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Máte-li řetězec podpis, zakódovat je pomocí algoritmus HMAC s klíčem SHA256 na řetězec kódování UTF-8 a pak kódování výsledek jako Base64. Použijte tento formát:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Ukázky v dalších částech mít ukázkový kód vám pomůže vytvořit autorizační hlavičky.

## <a name="request-body"></a>Tělo požadavku
Tělo zprávy musí být ve formátu JSON. Musí obsahovat jeden nebo více záznamů s dvojice názvů a hodnot vlastností v tomto formátu:

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

Pomocí následujícího formátu můžete dávky více záznamů společně v jedné žádosti. Všechny záznamy musí být stejného typu záznamu.

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
},
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

## <a name="record-type-and-properties"></a>Typ záznamu a vlastnosti
Při odesílání dat prostřednictvím Log Analytics HTTP dat kolekce API definujete vlastní typ záznamu. V současné době nelze zapisovat data do existující typy záznamů, které byly vytvořeny tak, že jiné datové typy a řešení. Analýzy protokolů přečte příchozích dat a poté vytvoří vlastnosti, které odpovídají datové typy hodnot, které zadáte.

Každý požadavek pro rozhraní API Log Analytics musí obsahovat **typ protokolu** záhlaví s názvem pro typ záznamu. Přípona **_CL** se automaticky připojí k názvu zadáte jako vlastní protokol ho odlišuje od ostatních typů protokolu. Pokud zadáte název například **MyNewRecordType**, analýzy protokolů vytvoří záznam s typem **MyNewRecordType_CL**. To pomáhá zajistit, že neexistují žádné konflikty mezi názvy typů vytvořené uživatelem a ty poskytuje současný nebo budoucí řešení společnosti Microsoft.

Pokud chcete identifikovat datový typ vlastnost, analýzy protokolů přidá příponu název vlastnosti. Pokud vlastnost obsahuje hodnotu null, vlastnost není součástí záznamů. Tato tabulka uvádí datový typ vlastnosti a odpovídající příponu:

| Datový typ vlastnosti | Přípona |
|:--- |:--- |
| Řetězec |_s |
| Logická hodnota |_b |
| Dvojitý |_d |
| Datum a čas |_t |
| IDENTIFIKÁTOR GUID |_g |

Datový typ, který používá analýzy protokolů pro každou vlastnost závisí na tom, jestli typ záznamu pro nový záznam již existuje.

* Pokud typ záznamu neexistuje, analýzy protokolů vytvoří novou. Analýzy protokolů používá k určení datového typu pro každou vlastnost pro nový záznam odvození typu JSON.
* Pokud typ záznamu neexistuje, analýzy protokolů se pokusí vytvořit nový záznam na základě existující vlastností. Pokud datový typ pro vlastnost v novém záznamu se neshoduje se a nelze převést na typ existující, nebo pokud záznam obsahuje vlastnosti, která neexistuje, analýzy protokolů vytvoří novou vlastnost s příponou relevantní.

Například by tato položka odeslání vytvořit záznam s třemi vlastnostmi **number_d**, **boolean_b**, a **string_s**:

![Ukázka záznamu 1](media/log-analytics-data-collector-api/record-01.png)

Pokud pak odeslání této další položky, hodnoty všech formátu řetězce, nebude změnit vlastnosti. Tyto hodnoty lze převést na existující typy dat:

![Ukázka záznamu 2](media/log-analytics-data-collector-api/record-02.png)

Ale pokud jste provedli poté tento další odeslání, analýzy protokolů by vytvořit nové vlastnosti **boolean_d** a **string_d**. Nelze převést tyto hodnoty:

![Ukázka záznamu 3](media/log-analytics-data-collector-api/record-03.png)

Pokud tuto položku, se potom odeslán, před vytvořením typ záznamu, analýzy protokolů by vytvořit záznam s třemi vlastnostmi **úspěch**, **boolean_s**, a **string_s**. V této položce všechny počáteční hodnoty, je naformátovaná jako řetězec:

![Ukázka záznamu 4](media/log-analytics-data-collector-api/record-04.png)

## <a name="data-limits"></a>Omezení dat
Existují některá omezení kolem data odeslány do kolekce Log Analytics Data rozhraní API.

* Maximálně 30 MB za post protokolu analýzy dat kolekce API. Toto je omezení velikosti pro jednu metodu post. Pokud data z jedné odeslání, který překračuje 30 MB, měli rozdělení dat až bloky s menší velikostí a odešlete je současně.
* Maximální limit 32 KB pro pole hodnot. Pokud hodnota pole je větší než 32 KB, bude zkrácen data.
* Doporučený maximální počet polí pro daný typ je 50. Toto je praktické omezení z perspektivy prostředí vyhledávání a použitelnost.  

## <a name="return-codes"></a>Návratové kódy
Stavový kód HTTP 200 znamená, že žádost byla přijata pro zpracování. To znamená, že operace úspěšně dokončena.

Tato tabulka uvádí kompletní sadu stavové kódy, které může vrátit službu:

| Kód | Status | Kód chyby | Popis |
|:--- |:--- |:--- |:--- |
| 200 |OK | |Žádost byla přijata úspěšně. |
| 400 |Nesprávná žádost |InactiveCustomer |Pracovní prostor byl uzavřen. |
| 400 |Nesprávná žádost |InvalidApiVersion |Služba nebyla rozpoznána verze rozhraní API, který jste zadali. |
| 400 |Nesprávná žádost |InvalidCustomerId |Zadané ID pracovního prostoru je neplatný. |
| 400 |Nesprávná žádost |InvalidDataFormat |Byla odeslána neplatná JSON. Text odpovědi může obsahovat další informace o tom, jak vyřešit chyby. |
| 400 |Nesprávná žádost |InvalidLogType |Typ protokolu zadat obsahují zvláštní znaky nebo číslice. |
| 400 |Nesprávná žádost |MissingApiVersion |Verze rozhraní API není zadaný. |
| 400 |Nesprávná žádost |MissingContentType |Typ obsahu, který nebyl zadán. |
| 400 |Nesprávná žádost |MissingLogType |Typ protokolu požadovaná hodnota nebyl zadán. |
| 400 |Nesprávná žádost |UnsupportedContentType |Typ obsahu, který nebyl nastaven na **application/json**. |
| 403 |Zakázáno |InvalidAuthorization |Službě se nepodařilo ověřit žádost. Ověření platnosti připojení ID a klíč pracovního prostoru. |
| 404 |Nenalezené | | Buď je zadaná adresa URL nesprávná nebo požadavku je příliš velký. |
| 429 |Příliš mnoho požadavků | | Služba dochází k velkému počtu data z účtu. Opakujte požadavek později. |
| 500 |Vnitřní chyba serveru |UnspecifiedError |Služba zjistila vnitřní chybu. Opakujte žádost. |
| 503 |Služba není dostupná |ServiceUnavailable |Služba je momentálně nedostupný a nepřijímá požadavky. Opakujte žádost. |

## <a name="query-data"></a>Dotazování dat
K dotazování na data odeslaná vyhledejte záznamy s aktualizace Log Analytics HTTP dat kolekce API **typ** který se rovná **LogType** hodnotu, která jste zadali, spolu s **_CL**. Pokud jste použili například **MyCustomLog**, pak by vrátit všechny záznamy s **typ = MyCustomLog_CL**.

>[!NOTE]
> Pokud pracovní prostor byl upgradován na verzi [nové analýzy protokolů dotazu jazyka](log-analytics-log-search-upgrade.md), pak výše uvedeném dotazu by změnit na následující.

> `MyCustomLog_CL`

## <a name="sample-requests"></a>Ukázka požadavků
V následujících částech najdete ukázky postup odesílání dat do kolekce API protokolu analýzy HTTP Data pomocí různých programovacích jazyků.

Pro každý vzorek proveďte tyto kroky nastavit proměnné pro hlavičku autorizace:

1. Na portálu Azure vyhledejte pracovní prostor analýzy protokolů.
2. Vyberte **upřesňující nastavení** a potom **připojené zdroje**.
2. Napravo od **ID pracovního prostoru**, vyberte ikonu kopírování a vložte ID jako hodnotu **ID zákazníka** proměnné.
3. Napravo od **primární klíč**, vyberte ikonu kopírování a vložte ID jako hodnotu **sdílený klíč** proměnné.

Alternativně můžete změnit proměnné pro typ protokolu a JSON data.

### <a name="powershell-sample"></a>Ukázkové prostředí PowerShell
```
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# Specify a field with the created time for the records
$TimeStampField = "DateValue"


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2016-05-12T20:00:00.625Z",
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
        -fileName $fileName `
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
```
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

        // LogName is name of the event type that is being submitted to Log Analytics
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Log Analytics assumes the time is the message ingestion time
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

### <a name="python-sample"></a>Ukázka Pythonu
```
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

## <a name="next-steps"></a>Další postup
- Použití [rozhraní API pro vyhledávání protokolu](log-analytics-log-search-api.md) k načtení dat z úložiště analýzy protokolů.
