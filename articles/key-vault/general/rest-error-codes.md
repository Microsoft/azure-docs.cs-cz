---
title: Kódy chyb rozhraní REST API –⁠ Azure Key Vault
description: Tyto kódy chyb by mohly být vráceny operací na Azure Key Vault webové služby.
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: 30b7e34f2a791cfd8dec1a6d8e81d706fa07939f
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631218"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Kódy chyb Azure Key Vault REST API
 
Následující chybové kódy by mohly být vráceny operací na Azure Key Vault webové služby.
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401: neověřená žádost

401 znamená, že žádost není ověřená pro Key Vault. 

Žádost je ověřena v těchto případech:

- Trezor klíčů zná identitu volajícího; ani
- Volající se může pokusit o přístup k prostředkům Key Vault. 

Existuje několik různých důvodů, proč může požadavek vracet 401.

### <a name="no-authentication-token-attached-to-the-request"></a>K požadavku není připojen žádný ověřovací token. 

Tady je příklad požadavku PUT a nastavení hodnoty tajného klíče:

``` 
PUT https://putreqexample.vault.azure.net//secrets/DatabaseRotatingPassword?api-version=7.0 HTTP/1.1
x-ms-client-request-id: 03d275a2-52a4-4bed-82c8-6fe15165affb
accept-language: en-US
Authorization: Bearer     eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9.eyJhdWQiOiJodHRwczovL3ZhdWx0LmF6dXJlLm5ldCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpYXQiOjE1NDg2OTc1MTMsIm5iZiI6MTU0ODY5NzUxMywiZXhwIjoxNTQ4NzAxNDEzLCJhaW8iOiI0MkpnWUhoODVqaVBnZHF5ZlRGZE5TdHY3bGUvQkFBPSIsImFwcGlkIjoiZmFkN2Q1YjMtNjlkNi00YjQ4LTkyNTktOGQxMjEyNGUxY2YxIiwiYXBwaWRhY3IiOiIxIiwiaWRwIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsIm9pZCI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInN1YiI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjItZ3JoUmtlSWs2QmVZLUxuNDJtQUEiLCJ2ZXIiOiIxLjAifQ.fgubiz1MKqTJTXI8dHIV7t9Fle6FdHrkaGYKcBeVRX1WtLVuk1QVxzIFDlZKLXJ7QPNs0KWpeiWQI9IWIRK-8wO38yCqKTfDlfHOiNWGOpkKddlG729KFqakVf2w0GPyGPFCONRDAR5wjQarN9Bt8I8YbHwZQz_M1hztlnv-Lmsk1jBmech9ujD9-lTMBmSfFFbHcqquev119V7sneI-zxBZLf8C0pIDkaXf1t8y6Xr8CUJDMdlWLslCf3pBCNIOy65_TyGvy4Z4AJryTPBarNBPwOkNAtjCfZ4BDc2KqUZM5QN_VK4foP64sVzUL6mSr0Gh7lQJIL5b1qIpJxjxyQ
User-Agent: FxVersion/4.7.3324.0 OSName/Windows OSVersion/6.2.9200.0 Microsoft.Azure.KeyVault.KeyVaultClient/3.0.3.0
Content-Type: application/json; charset=utf-8
Host: putreqexample.vault.azure.net
Content-Length: 31

{
   "value": "m*gBJ7$Zuoz)"
}
```

Hlavička "Authorization" je přístupový token, který je vyžadován při každém volání Key Vault pro operace roviny dat. Pokud hlavička chybí, musí být odpověď 401.

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>Token nemá přidružený správný prostředek. 

Při vyžádání přístupového tokenu z koncového bodu Azure OAUTH je parametr s názvem "prostředek" povinný. Hodnota je důležitá pro poskytovatele tokenů, protože je v oboru pro zamýšlené použití. Prostředek **pro přístup** k Key Vault je *https: \/ /Vault.keyvault.NET* (bez koncového lomítka).

### <a name="the-token-is-expired"></a>Platnost tokenu vypršela.

Tokeny mají kódování Base64 a hodnoty lze dekódovat na webech, jako je například [http://jwt.calebb.net](http://jwt.calebb.net) . Toto je dekódování výše uvedeného tokenu:

```
    {
 typ: "JWT",
 alg: "RS256",
 x5t: "nbCwW11w3XkB-xUaXwKRSLjMHGQ",
 kid: "nbCwW11w3XkB-xUaXwKRSLjMHGQ"
}.
{
 aud: "https://vault.azure.net",
 iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 iat: 1548697513,
 nbf: 1548697513,
 exp: 1548701413,
 aio: "42JgYHh85jiPgdqyfTFdNStv7le/BAA=",
 appid: "fad7d5b3-69d6-4b48-9259-8d12124e1cf1",
 appidacr: "1",
 idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 oid: "3975aeed-7d08-453b-b6f4-445f32698091",
 sub: "3975aeed-7d08-453b-b6f4-445f32698091",
 tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",
 uti: "2-grhRkeIk6BeY-Ln42mAA",
 ver: "1.0"
}.
[signature]
```

V tomto tokenu jsme viděli spoustu důležitých částí:

- AUD (cílová skupina): prostředek tokenu. Všimněte si, že je to `https://vault.azure.net` . Tento token nebude fungovat pro všechny prostředky, které explicitně neodpovídají této hodnotě, jako je například Graph.
- IAT (vydáno v): počet taktů od začátku epocha při vydání tokenu.
- NBF (ne dříve): počet taktů od začátku epocha, pokud je tento token platný.
- EXP (vypršení platnosti): počet taktů od začátku epocha po vypršení platnosti tokenu.
- AppID (ID aplikace): identifikátor GUID pro ID aplikace, který tuto žádost odeslal.
- TID (ID tenanta): identifikátor GUID ID tenanta, který vytváří tuto žádost.

Je důležité, aby všechny hodnoty správně identifikovaly v tokenu, aby mohla žádost fungovat. Pokud je vše správné, požadavek nebude mít 401.

### <a name="troubleshooting-401"></a>Řešení potíží 401

401s by se mělo prozkoumat z bodu generování tokenu, než se do trezoru klíčů dovede požadavek. K vyžádání tokenu se používá všeobecně používaný kód. Po přijetí tokenu je předán do žádosti Key Vault. Pokud kód běží lokálně, můžete použít Fiddler k zachycení žádosti nebo odpovědi na `https://login.microsoftonline.com` . Požadavek vypadá takto:

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

Následující informace, které jsou zadány uživatelem, musí být správné:

- ID tenanta trezoru klíčů
- Hodnota prostředku nastavená na https %3 A %2 F %2 F trezor. Azure. NET (s kódováním URL)
- ID klienta
- Tajný klíč klienta

Zajistěte, aby zbytek žádosti byl skoro identický.

Pokud máte přístup jenom k tokenu odpovědi, můžete ho dekódovat (jak vidíte výše), abyste zajistili ID tenanta, ID klienta (ID aplikace) a prostředek.

## <a name="http-403-insufficient-permissions"></a>HTTP 403: nedostatečná oprávnění

HTTP 403 znamená, že žádost byla ověřena (zná požadavek identity), ale identita nemá oprávnění pro přístup k požadovanému prostředku. Existují dva příčiny:

- Pro tuto identitu nejsou k dispozici žádné zásady přístupu.
- IP adresa žádajícího prostředku není schválená v nastavení brány firewall trezoru klíčů.

K protokolu HTTP 403 dochází často tehdy, když aplikace zákazníka nepoužívá ID klienta, které mu zákazník považuje. To obvykle znamená, že zásady přístupu nejsou správně nastaveny pro skutečnou volající identitu.

### <a name="troubleshooting-403"></a>Řešení potíží 403

Nejdříve zapněte protokolování. Pokyny k tomu, jak to udělat, najdete v tématu [protokolování Azure Key Vault](logging.md).

Jakmile je protokolování zapnuté, můžete zjistit, jestli je 403 v důsledku zásad přístupu nebo zásad brány firewall.

#### <a name="error-due-to-firewall-policy"></a>Chyba kvůli zásadám brány firewall

"Adresa klienta (00.00.00.00) není autorizována a volající není důvěryhodná služba"

Existuje omezený seznam "důvěryhodných služeb Azure". Weby **Azure nejsou důvěryhodnou** službou Azure. Další informace najdete v blogovém příspěvku [Key Vault přístupu k bráně firewall pomocí Azure App Services](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services).

Aby fungovala, je nutné přidat IP adresu webu Azure do Key Vault.

Pokud se jedná o zásady přístupu: Najděte ID objektu pro požadavek a ujistěte se, že ID objektu odpovídá objektu, ke kterému se uživatel pokouší přiřadit zásady přístupu. V AAD bude často více objektů, které mají stejný název, takže výběr správné je velmi důležitý. Odstraněním a novým přidáním zásad přístupu je možné zjistit, zda existuje více objektů se stejným názvem.

Kromě toho většina zásad přístupu nevyžaduje použití autorizované aplikace, jak je znázorněno na portálu. Ověřená aplikace se používá pro scénáře ověřování, které jsou zřídka. 


## <a name="http-429-too-many-requests"></a>HTTP 429: příliš mnoho požadavků

K omezování dochází, když počet požadavků překročí stanovený maximální časový rámec. Pokud dojde k omezení, bude odpověď Key Vault HTTP 429. Pro typy požadavků byly zadány maximální hodnoty. Například: vytvoření 2048-bitového klíče modulu HSM je 5 požadavků za 10 sekund, ale všechny ostatní transakce HSM mají omezení 1000 požadavků/10 sekund. Proto je důležité pochopit, jaké typy volání jsou vytvářeny při určování příčiny omezení.
Obecně platí, že požadavky na Key Vault jsou omezeny na 2000 požadavků za 10 sekund. Výjimky jsou klíčové operace, jak je popsáno v [Key Vault omezení služby](service-limits.md)

### <a name="troubleshooting-429"></a>Řešení potíží 429
Omezování se řeší pomocí těchto technik:

- Snižte počet požadavků provedených v Key Vault tím, že určíte, zda existují vzory pro požadovaný prostředek, a pokusíte se je Uložit do mezipaměti v volající aplikaci. 

- Když dojde k omezení Key Vault, přizpůsobte si požadavek na použití exponenciálního omezení rychlostiu pro opakování. Tento algoritmus je vysvětlený tady: [jak omezit vaši aplikaci](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits) .

- Pokud se počet požadavků nedá snížit ukládáním do mezipaměti a časovým limitem omezení rychlosti nefunguje, zvažte rozdělení klíčů do několika trezorů klíčů. Limit služby pro jedno předplatné je pětinásobné omezením jednotlivých Key Vault. Pokud používáte více než 5 trezorů klíčů, měli byste zvážit použití více předplatných. 

Podrobné pokyny, včetně požadavků na zvýšení limitů, najdete tady: [Key Vault pokyny k omezování](overview-throttling.md) .
