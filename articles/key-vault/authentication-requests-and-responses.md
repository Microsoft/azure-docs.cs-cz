---
title: Ověřování, požadavky a odpovědi
description: Ověření do služby AD pro používání Key Vault
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 4c321939-8a5b-42ca-83c4-2f5f647ca13e
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: 94080fb124478a4b8e196e341c335ca32321ecdf
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012069"
---
# <a name="authentication-requests-and-responses"></a>Ověřování, požadavky a odpovědi

Azure Key Vault podporuje formátu JSON požadavky a odpovědi. Požadavky na Azure Key Vault jsou směrované platná adresa URL trezoru klíčů Azure pomocí že HTTPS s některé parametry adresy URL a JSON kódování těla požadavku a odpovědi.

Toto téma obsahuje podrobnosti pro službu Azure Key Vault. Obecné informace o použití rozhraní Azure REST, včetně ověřování/autorizace služby a jak získat přístupový token, najdete v části [referenční dokumentace rozhraní API REST Azure](https://docs.microsoft.com/rest/api/).

## <a name="request-url"></a>Adresa URL požadavku  
 Operace správy klíčů používají HTTP DELETE, GET, opravy, PUT a HTTP POST a kryptografické operace u existující klíče objektů pomocí HTTP POST. Klienti, kteří nemohou podporovat konkrétní příkazy HTTP může také použít HTTP POST pomocí hlavičku požadavku HTTP X-zadat příkaz určený; požadavky, které standardně nevyžadují text by měla obsahovat prázdným textem zprávy. při použití HTTP POST, například při použití POST místo odstranění.  

 Pro práci s objekty v Azure Key Vault, jsou následující adresy URL příklad:  

-   Chcete-li vytvořit klíč s názvem TESTKEY Key Vault používá – `PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

-   Chcete-li IMPORTOVAT klíč s názvem IMPORTEDKEY do Key Vault použití- `POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

-   Chcete-li získat tajný klíč názvem MYSECRET Key Vault používá – `GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

-   Pro přihlášení ověřování algoritmem digest pomocí klíče názvem TESTKEY Key Vault používá – `POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

 Autorita pro žádost o Key Vault je vždy následujícím způsobem,  `https://{keyvault-name}.vault.azure.net/`  

 Klíče byly vždy uloženy v cestě /keys, tajné klíče byly vždy uloženy v cestě /secrets.  

## <a name="api-version"></a>Verze rozhraní API  
 Službě Azure Key Vault podporuje verze protokolu zajistit kompatibilitu s klienty nižší úrovně, i když ne všechny funkce bude k dispozici pro tyto klienty. Klienti musí používat `api-version` parametr řetězce zadat verzi protokolu, které podporují jak není žádné výchozí dotazu.  

 Verze protokolu Azure Key Vault, postupujte podle schématu pomocí {rrrr} číslování datum. {MM}. Formát {DD}.  

## <a name="request-body"></a>Text žádosti  
 Podle specifikace protokolu HTTP GET operations není musí obsahovat text žádosti a operace POST a PUT musí obsahovat text žádosti. Text v operacemi odstranění je volitelné protokolu HTTP.  

 Pokud není uvedeno jinak, v popisu operaci, typu obsahu text požadavku musí být application/json a musí obsahovat serializovaných vyhovující objekt JSON pro typ obsahu.  

 Pokud není uvedeno jinak, v popisu operace, Hlavička Accept žádost musí obsahovat typ média application/json.  

## <a name="response-body"></a>Text odpovědi  
 Pokud není uvedeno jinak, v popisu operaci, typu obsahu text odpovědi úspěšných i neúspěšných operací bude application/json a obsahuje podrobné informace o chybě.  

## <a name="using-http-post"></a>Pomocí HTTP POST  
 Někteří klienti nemusí být možné používat určité příkazy HTTP, jako je například opravy nebo odstranit. Azure Key Vault podporuje HTTP POST jako alternativu pro tyto klienty za předpokladu, že klient také zahrnuje "X-HTTP-METHOD" hlavičky k operaci konkrétní původní HTTP. Podpora pro metodu POST protokolu HTTP je uvedeno pro každou z rozhraní API definované v tomto dokumentu.  

## <a name="error-responses"></a>Chybové odpovědi  
 Zpracování chyb použije stavové kódy HTTP. Typické výsledky jsou:  

-   2xx – Úspěch: používá pro běžné operace. Text odpovědi bude obsahovat očekávaný výsledek  

-   3xx – Přesměrování: 304 "nedojde ke změně" mohou být vráceny ke splnění podmíněného GET. Ostatní kódy 3xx lze v budoucnu k zachycení změn DNS a cestu.  

-   4xx – chyba klienta: používá se pro chybných požadavků, chybějící klíče, chyby syntaxe, neplatný parametry, chybám při ověřování, atd. Text odpovědi bude obsahovat podrobné informace o chybě vysvětlení.  

-   5xx – Chyba serveru: používá pro vnitřní chyby serveru. Text odpovědi bude obsahovat informace o souhrnnou chybě.  

 Systém je navržen pro práci za proxy nebo brány firewall. Klient proto může zobrazit další kódy chyb.  

 Azure Key Vault také vrací informace o chybě v textu odpovědi, když dojde k potížím. Text odpovědi je formátu JSON, která má tvar:  

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
 Všechny požadavky na Azure Key Vault musí být ověřen. Azure Key Vault podporuje Azure Active Directory přístupové tokeny, které lze získat pomocí OAuth2 [[RFC6749](http://tools.ietf.org/html/rfc6749)]. 
 
 Další informace o registraci vaší aplikace a ověřování používat Azure Key Vault najdete v tématu [zaregistrovat klientské aplikace s Azure AD](https://docs.microsoft.com/rest/api/index#register-your-client-application-with-azure-ad).
 
 Přístupové tokeny, musí se poslat do služby pomocí protokolu HTTP autorizační hlavičky:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 Pokud není zadaný token přístupu, nebo pokud token nebyla přijata službou, chybu HTTP 401 bude vrácen do klienta a bude obsahovat záhlaví WWW-Authenticate, například:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 Parametry v hlavička WWW-Authenticate jsou:  

-   autorizace: adresa služby ověřování OAuth2, který se dá použít k získání tokenu přístupu pro daný požadavek.  

-   prostředek: název prostředku pro použití v požadavek ověřování.  

## <a name="see-also"></a>Viz také  
 [Informace o klíčích, tajných kódech a certifikátech](about-keys-secrets-and-certificates.md)
