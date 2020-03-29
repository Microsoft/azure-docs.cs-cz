---
title: Ověřování, požadavky a odpovědi
description: Ověření ve službě AD za použití trezoru klíčů
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 8cb68b1a5363889e6ea7c49cf0cdda6ed5f0e027
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294562"
---
# <a name="authentication-requests-and-responses"></a>Ověřování, požadavky a odpovědi

Azure Key Vault podporuje požadavky a odpovědi ve formátu JSON. Požadavky na Azure Key Vault jsou směrovány na platnou adresu URL azure key vault pomocí protokolu HTTPS s některými parametry adresy URL a json kódované požadavky a těla odpovědí.

Toto téma popisuje specifika služby Azure Key Vault. Obecné informace o používání rozhraní Azure REST, včetně ověřování/autorizace a jak získat přístupový token, najdete v [tématu Azure REST API Reference](https://docs.microsoft.com/rest/api/azure).

## <a name="request-url"></a>Adresa URL požadavku  
 Operace správy klíčů používají operace HTTP DELETE, GET, PATCH, PUT a HTTP POST a kryptografické operace proti existujícím klíčovým objektům používají http post. Klienti, kteří nemohou podporovat konkrétní slovesa HTTP, mohou také použít protokol HTTP POST pomocí hlavičky X-HTTP-REQUEST k určení zamýšleného slovesa. požadavky, které obvykle nevyžadují tělo by měl obsahovat prázdné tělo při použití HTTP POST, například při použití POST místo DELETE.  

 Chcete-li pracovat s objekty v trezoru klíčů Azure, jsou k tomu ukázkové adresy URL:  

- Chcete-li vytvořit klíč s názvem TESTKEY v trezoru klíčů použití -`PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

- Import klíče importovaného klíče do trezoru klíčů`POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

- Chcete-li získat tajný klíč s názvem MYSECRET v trezoru klíčů použití -`GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

- Chcete-li podepsat digest pomocí klíče s názvem TESTKEY v trezoru klíčů použití -`POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

  Oprávnění k žádosti do trezoru klíčů je vždy následující,`https://{keyvault-name}.vault.azure.net/`  

  Klíče jsou vždy uloženy pod /keys cestu, tajné klíče jsou vždy uloženy pod /secrets cestu.  

## <a name="api-version"></a>Verze rozhraní API  
 Služba Azure Key Vault service podporuje správu verzí protokolů, aby byla zajištěna kompatibilita s klienty nižší úrovně, i když ne všechny funkce budou k dispozici těmto klientům. Klienti musí `api-version` použít parametr řetězce dotazu k určení verze protokolu, který podporují, protože neexistuje žádné výchozí nastavení.  

 Verze protokolu Azure Key Vault postupujte podle schématu číslování dat pomocí {YYYY}. {MM}. {DD} formátu.  

## <a name="request-body"></a>Text žádosti  
 Podle specifikace HTTP get operace nesmí mít tělo požadavku a POST a PUT operace musí mít tělo požadavku. Tělo v operacích DELETE je volitelné v protokolu HTTP.  

 Pokud není v popisu operace uvedeno jinak, musí být typ obsahu těla požadavku application/json a musí obsahovat serializovaný objekt JSON, který odpovídá typu obsahu.  

 Pokud není v popisu operace uvedeno jinak, musí hlavička Přijmout požadavek obsahovat typ média aplikace/json.  

## <a name="response-body"></a>Tělo odpovědi  
 Pokud není v popisu operace uvedeno jinak, bude typem obsahu těla odpovědi úspěšných i neúspěšných operací aplikace/json a obsahuje podrobné informace o chybě.  

## <a name="using-http-post"></a>Použití funkce HTTP POST  
 Někteří klienti nemusí být schopni používat určitá slovesa HTTP, například PATCH nebo DELETE. Azure Key Vault podporuje HTTP POST jako alternativu pro tyto klienty za předpokladu, že klient také obsahuje hlavičku "X-HTTP-METHOD" pro konkrétní původní sloveso HTTP. Podpora pro HTTP POST je třeba poznamenat, pro každý z rozhraní API definované v tomto dokumentu.  

## <a name="error-responses"></a>Chybové odpovědi  
 Při zpracování chyb budou používat stavové kódy HTTP. Typickými výsledky jsou:  

- 2xx – Úspěch: Používá se pro běžný provoz. Tělo odpovědi bude obsahovat očekávaný výsledek  

- 3xx – Přesměrování: 304 "Nezměněno" může být vrácena ke splnění podmíněné GET. Další kódy 3xx mohou být v budoucnu použity k označení změn DNS a cesty.  

- 4xx - Chyba klienta: Používá se pro chybné požadavky, chybějící klíče, syntaktické chyby, neplatné parametry, chyby ověřování atd. Tělo odpovědi bude obsahovat podrobné vysvětlení chyby.  

- 5xx – Chyba serveru: Používá se pro interní chyby serveru. Tělo odpovědi bude obsahovat souhrnné informace o chybě.  

  Systém je navržen tak, aby fungoval za proxy nebo firewallem. Klient proto může obdržet další kódy chyb.  

  Azure Key Vault také vrátí informace o chybě v těle odpovědi, když dojde k problému. Tělo odezvy je formátováno jsonem a má podobu:  

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

## <a name="authentication"></a>Ověřování  
 Všechny požadavky na Azure Key Vault musí být ověřeny. Azure Key Vault podporuje tokeny přístupu služby Azure Active Directory, které lze získat pomocí OAuth2 [[RFC6749](https://tools.ietf.org/html/rfc6749)]. 
 
 Další informace o registraci aplikace a ověřování používat Azure Key Vault, najdete v [tématu Registrace klientské aplikace s Azure AD](https://docs.microsoft.com/rest/api/azure/index#register-your-client-application-with-azure-ad).
 
 Přístupové tokeny musí být odeslány službě pomocí hlavičky autorizace protokolu HTTP:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 Pokud není zadán přístupový token nebo pokud token není službou přijat, bude klientovi vrácena chyba HTTP 401, která bude obsahovat například hlavičku WWW-Authenticate:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 Parametry v hlavičce WWW-Authenticate jsou:  

-   Autorizace: Adresa autorizační služby OAuth2, která může být použita k získání přístupového tokenu pro požadavek.  

-   Zdroj: Název prostředku (`https://vault.azure.net`) pro použití v žádosti o autorizaci.  

## <a name="see-also"></a>Viz také  
 [Informace o klíčích, tajných kódech a certifikátech](about-keys-secrets-and-certificates.md)
