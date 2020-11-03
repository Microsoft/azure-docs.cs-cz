---
title: Ověřování, požadavky a odpovědi
description: Přečtěte si, jak Azure Key Vault používá žádosti a odpovědi ve formátu JSON a o požadovaném ověřování pro použití trezoru klíčů.
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 48f223a55e4a1e4db4ac7057065d67ae64fa0f2c
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288474"
---
# <a name="authentication-requests-and-responses"></a>Ověřování, požadavky a odpovědi

Azure Key Vault poskytuje dva typy kontejnerů pro ukládání a správu tajných kódů pro cloudové aplikace:

|Typ kontejneru|Podporované typy objektů|Koncový bod roviny dat|
|--|--|--|
| **Trezory**|<ul><li>Klíče chráněné softwarem</li><li>Klíče chráněné HSM (s SKU úrovně Premium)</li><li>Certifikáty</li><li>Klíče účtu úložiště</li></ul> | https://{trezor-Name}. trezor. Azure. NET
|**Managed HSM** |<ul><li>Klíče chráněné pomocí HSM</li></ul> | https://{HSM-Name}. managedhsm. Azure. NET

Tady jsou přípony adres URL používané pro přístup k jednotlivým typům objektů.

|typ objektu|Přípona adresy URL|
|--|--|
|Klíče chráněné softwarem| /keys |
|Klíče chráněné pomocí HSM| /keys |
|Tajné kódy|/secrets|
|Certifikáty| /certificates|
|Klíče účtu úložiště|/storageaccounts
||

Azure Key Vault podporuje žádosti a odpovědi ve formátu JSON. Požadavky na Azure Key Vault jsou směrovány na platnou adresu URL Azure Key Vault pomocí protokolu HTTPS s některými parametry adresy URL a texty požadavků a odpovědí kódovaných v kódování JSON.

Toto téma popisuje konkrétní služby Azure Key Vault. Obecné informace o používání rozhraní REST Azure, včetně ověřování/autorizace a získání přístupového tokenu, najdete v tématu [Azure REST API Reference](/rest/api/azure).

## <a name="request-url"></a>Adresa URL požadavku  
 Operace správy klíčů používají operace HTTP DELETE, GET, PATCH, PUT a HTTP POST a kryptografických operací pro existující klíčové objekty, které používají HTTP POST. Klienti, kteří nemůžou podporovat konkrétní příkazy HTTP, můžou k určení zamýšleného příkazu použít taky POST protokolu HTTP pomocí záhlaví X-HTTP-REQUEST. požadavky, které obvykle nevyžadují tělo, by měly při použití HTTP POST zahrnovat prázdné tělo, například při použití POST namísto DELETE.  

 Chcete-li pracovat s objekty v Azure Key Vault, jsou zde uvedené příklady adres URL:  

- Postup vytvoření klíče s názvem TESTKEY v Key Vault použít- `PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

- IMPORT klíče s názvem IMPORTEDKEY do Key Vault use- `POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

- ZÍSKÁNÍ tajného kódu s názvem MYSECRET v Key Vault použití- `GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

- PODEPSÁNí algoritmu Digest pomocí klíče s názvem TESTKEY v Key Vault use- `POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

- Autorita pro požadavek na Key Vault je vždy následující:
  - Pro trezory: `https://{keyvault-name}.vault.azure.net/`
  - Pro spravované HSM: `https://{HSM-name}.managedhsm.azure.net/`

  Klíče se vždycky ukládají do cesty/Keys, tajné klíče se vždycky ukládají do cesty/Secrets.  

## <a name="api-version"></a>Verze rozhraní API  
 Služba Azure Key Vault podporuje správu verzí protokolu, aby poskytovala kompatibilitu s klienty nižší úrovně, i když pro tyto klienty nebudou k dispozici všechny možnosti. Klienti musí použít `api-version` parametr řetězce dotazu k určení verze protokolu, který podporují, protože není k dispozici výchozí hodnota.  

 Verze Azure Key Vaultho protokolu následují po schématu číslování dat pomocí {rrrr}. {MM}. Formát {DD}.  

## <a name="request-body"></a>Text požadavku  
 Podle specifikace HTTP nesmí operace GET obsahovat text žádosti a operace POST a PUT musí obsahovat text žádosti. Tělo v rámci operací odstranění je v HTTP volitelné.  

 Pokud není uvedeno jinak v popisu operace, musí být typ obsahu textu požadavku Application/JSON a musí obsahovat serializovaný objekt JSON, který odpovídá typu obsahu.  

 Pokud není uvedeno jinak v popisu operace, musí hlavička žádosti o přijetí obsahovat typ média Application/JSON.  

## <a name="response-body"></a>Tělo odpovědi  
 Pokud není uvedeno jinak v popisu operace, bude typ obsahu odpovědi úspěšné i nezdařené operace aplikace/JSON a obsahuje podrobné informace o chybě.  

## <a name="using-http-post"></a>Použití HTTP POST  
 Někteří klienti nemusí být schopni použít určité příkazy HTTP, například PATCH nebo DELETE. Azure Key Vault podporuje HTTP POST jako alternativu pro tyto klienty, pokud klient nabízí také hlavičku "X-HTTP-METHOD" pro konkrétní původní příkaz HTTP. Pro každé rozhraní API definované v tomto dokumentu se poznamená podpora pro HTTP POST.  

## <a name="error-responses"></a>Chybové odpovědi  
 Zpracování chyb bude používat stavové kódy HTTP. Typické výsledky jsou:  

- 2xx – úspěch: použito pro normální operaci. Tělo odpovědi bude obsahovat očekávaný výsledek.  

- 3xx – přesměrování: pro splnění podmíněného načtení může být vráceno 304 "nezměněno". V budoucnu se můžou použít jiné kódy 3xx k označení změn DNS a cest.  

- 4xx – Chyba klienta: používá se pro chybné požadavky, chybějící klíče, chyby syntaxe, neplatné parametry, chyby ověřování atd. Tělo odpovědi bude obsahovat podrobné vysvětlení chyby.  

- 5xx – chyba serveru: používá se pro interní chyby serveru. Tělo odpovědi bude obsahovat souhrnné informace o chybě.  

  Systém je navržený tak, aby fungoval za proxy serverem nebo bránou firewall. Proto může klient získat další chybové kódy.  

  Azure Key Vault také vrátí informace o chybě v těle odpovědi, pokud dojde k problému. Tělo odpovědi je ve formátu JSON a má podobu:  

```  

{  
  "error":  
  {  
    "code": "BadArgument",  
    "message":  

      "’Foo’ is not a valid argument for ‘type’."  
    }  
  }  
}  

```  

## <a name="authentication"></a>Authentication  
 Všechny požadavky na Azure Key Vault musí být ověřeny. Azure Key Vault podporuje přístupové tokeny Azure Active Directory, které se dají získat pomocí OAuth2 [[RFC6749](https://tools.ietf.org/html/rfc6749)]. 
 
 Další informace o registraci aplikace a ověřování pro použití Azure Key Vault najdete v tématu [registrace klientské aplikace pomocí Azure AD](/rest/api/azure/index#register-your-client-application-with-azure-ad).
 
 Přístupové tokeny musí být odesílány službě pomocí hlavičky autorizace protokolu HTTP:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 Pokud není k dispozici přístupový token nebo když služba token nepřijme, vrátí se klientovi Chyba HTTP 401 a bude obsahovat hlavičku WWW-Authenticate, například:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 Parametry v hlavičce WWW-Authenticate jsou:  

-   autorizace: adresa autorizační služby OAuth2, která se dá použít k získání přístupového tokenu pro požadavek.  

-   prostředek: název prostředku ( `https://vault.azure.net` ), který se má použít v žádosti o autorizaci.