---
title: Chybové kódy rozhraní REST API – trezor klíčů Azure
description: Tyto kódy chyb může být vrácena operace na webové služby Azure Key Vault.
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: 01fb5393217834bc0196da25c4a56314ca7eae2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294540"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Chybové kódy rozhraní API úložiště klíčů Azure
 
Následující kódy chyb může být vrácena operace na webové služby Azure Key Vault.
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401: Neověřený požadavek

401 znamená, že požadavek je neověřený pro trezor klíčů. 

Požadavek je ověřen, pokud:

- Trezor klíčů zná identitu volajícího; A
- Volající se může pokusit o přístup k prostředkům trezoru klíčů. 

Existuje několik různých důvodů, proč žádost může vrátit 401.

### <a name="no-authentication-token-attached-to-the-request"></a>K požadavku není připojen žádný ověřovací token. 

Zde je příklad put požadavku, nastavení hodnoty tajného klíče:

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

Hlavička "Autorizace" je přístupový token, který je vyžadován při každém volání trezoru klíčů pro operace datové roviny. Pokud záhlaví chybí, musí být odpověď 401.

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>Token postrádá správný prostředek s ním spojené. 

Při vyžádání přístupového tokenu z koncového bodu Azure OAUTH je povinný parametr s názvem "prostředek". Hodnota je důležitá pro zprostředkovatele tokenu, protože obory token pro jeho zamýšlené použití. Prostředek pro **všechny** tokeny pro přístup k trezoru klíčů je *https:\//vault.keyvault.net* (bez koncové lomítko).

### <a name="the-token-is-expired"></a>Platnost tokenu vypršela.

Tokeny jsou zakódovány base64 a hodnoty mohou být [http://jwt.calebb.net](http://jwt.calebb.net)dekódovány na webových stránkách, jako je například . Zde je výše uvedený token dekódován:

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

Můžeme vidět mnoho důležitých částí v tomto tokenu:

- aud (publikum): Zdroj tokenu. Všimněte si, že se jedná <https://vault.azure.net>o . Tento token nebude fungovat pro žádný prostředek, který explicitně neodpovídá této hodnotě, jako je například graf.
- iat (vydáno na): Počet značek od začátku epochy, kdy byl token vydán.
- nbf (ne dříve): Počet značek od začátku epochy, když se tento token stane platným.
- exp (expirace): Počet značek od začátku epochy, když vyprší platnost tohoto tokenu.
- appid (ID aplikace): IDENTIFIKÁTOR GUID pro ID aplikace, které tuto žádost provádí.
- tid (ID klienta): IDENTIFIKÁTOR GUID pro ID klienta hlavního, který tuto žádost provádí

Je důležité, aby všechny hodnoty správně identifikovány v tokenu, aby požadavek fungoval. Pokud je vše v pořádku, pak požadavek nebude mít za následek 401.

### <a name="troubleshooting-401"></a>Poradce při potížích s 401

401s by měly být zkoumány z bodu generování tokenu, před požadavek do trezoru klíčů. Obecně kód se používá k vyžádání tokenu. Jakmile je token přijat, je předán do požadavku trezoru klíčů. Pokud je kód spuštěn místně, můžete použít Šumař zachytit `https://login.microsoftonline.com`požadavek/odpověď na . Požadavek vypadá takto:

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

Následující uživatelem zadané informace mush být správné:

- ID klienta trezoru klíčů
- Hodnota prostředku nastavená na https%3A%2F%2Fvault.azure.net (kódované adresy URL)
- ID klienta
- Tajný klíč klienta

Ujistěte se, že zbytek požadavku je téměř totožný.

Pokud můžete získat pouze přístupový token odpovědi, můžete dekódovat (jak je uvedeno výše) k zajištění ID klienta, ID klienta (ID aplikace) a prostředek.

## <a name="http-403-insufficient-permissions"></a>HTTP 403: Nedostatečná oprávnění

HTTP 403 znamená, že požadavek byl ověřen (zná žádající identitu), ale identita nemá oprávnění k přístupu k požadovanému prostředku. Existují dvě příčiny:

- Neexistuje žádná zásada přístupu pro identitu.
- Adresa IP žádajícího prostředku není uvedena na seznamu povolených adres v nastavení brány firewall trezoru klíčů.

HTTP 403 často dochází, když aplikace zákazníka nepoužívá ID klienta, který si zákazník myslí, že je. To obvykle znamená, že zásady přístupu není správně nastavena pro skutečné volající identity.

### <a name="troubleshooting-403"></a>Poradce při potížích 403

Nejprve zapněte protokolování. Pokyny k tomu naleznete v tématu [Protokolování trezoru klíčů Azure](key-vault-logging.md).

Po zapnutí protokolování můžete určit, zda je 403 z důvodu zásad přístupu nebo zásad brány firewall.

#### <a name="error-due-to-firewall-policy"></a>Chyba způsobená zásadou brány firewall

"Adresa klienta (00.00.00.00) není autorizována a volající není důvěryhodná služba"

Existuje omezený seznam "Azure Trusted Services". Weby Azure **nejsou** důvěryhodnou službou Azure. Další informace najdete v příspěvku blogu [Key Vault Firewall access by Azure App Services](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services).

Aby fungovala, musíte do trezoru klíčů přidat IP adresu webu Azure.

Pokud z důvodu zásady přístupu: najděte ID objektu pro požadavek a ujistěte se, že ID objektu odpovídá objektu, ke kterému se uživatel pokouší přiřadit zásady přístupu. Tam bude často více objektů v AAD, které mají stejný název, takže výběr správného je velmi důležité. Odstraněním a opětovným přidáním zásad přístupu je možné zjistit, zda existuje více objektů se stejným názvem.

Většina zásad přístupu navíc nevyžaduje použití "Autorizované aplikace", jak je znázorněno na portálu. Autorizovaná aplikace se používají pro scénáře ověřování "jménem", které jsou vzácné. 


## <a name="http-429-too-many-requests"></a>HTTP 429: Příliš mnoho požadavků

Omezení dochází, když počet požadavků překročí uvedené maximum pro časový rámec. Pokud dojde k omezení, bude odpověď trezoru klíčů http 429. Pro typy pořizované žádosti jsou uvedena maxima. Například: vytvoření klíče 2048 bit ového serveru HSM je 5 požadavků za 10 sekund, ale všechny ostatní transakce hsm mají limit 1000 požadavku/ 10 sekund. Proto je důležité pochopit, které typy volání jsou prováděny při určování příčiny omezení.
Obecně platí, že požadavky na trezor klíčů jsou omezeny na 2000 požadavků / 10 sekund. Výjimkou jsou operace s klíčem, jak je popsáno v [limitech služeb trezoru klíčů.](key-vault-service-limits.md)

### <a name="troubleshooting-429"></a>Řešení potíží 429
Omezení se pracuje pomocí těchto technik:

- Snižte počet požadavků na trezor klíčů určením, zda existují vzorky pro požadovaný prostředek, a pokusem o jejich uložení do mezipaměti v volající aplikaci. 

- Dojde-li k omezení trezoru klíčů, přizpůsobte žádající kód tak, aby používal exponenciální backoff pro opakování. Algoritmus je vysvětlen zde: [Jak omezit aplikaci](key-vault-ovw-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

- Pokud počet požadavků nelze snížit ukládánído mezipaměti a timed backoff nefunguje, zvažte rozdělení klíčů do více trezorů klíčů. Limit služby pro jedno předplatné je 5x více než limit trezoru klíčů. Pokud používáte více než 5 trezorů klíčů, je třeba zvážit použití více odběrů. 

Podrobné pokyny, včetně žádosti o zvýšení limitů, najdete zde: [Key Vault omezení pokyny](key-vault-ovw-throttling.md)


