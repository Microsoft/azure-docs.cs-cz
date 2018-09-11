---
title: Ověřování, požadavky a odpovědi
description: Ověřování AD pro používání služby Key Vault
services: key-vault
documentationcenter: ''
author: bryanla
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 4c321939-8a5b-42ca-83c4-2f5f647ca13e
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: bryanla
ms.openlocfilehash: c7cd9dfa019ca0d8560833b10a3e8a1a37a1e1ac
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296546"
---
# <a name="authentication-requests-and-responses"></a>Ověřování, požadavky a odpovědi

Azure Key Vault podporuje ve formátu JSON požadavky a odpovědi. Požadavky pro Azure Key Vault přesměrováni platná adresa URL trezoru klíčů Azure pomocí protokolu HTTPS se některých parametrů adresy URL a JSON zakódovaného těla požadavku a odpovědi.

Toto téma obsahuje podrobné informace o službě Azure Key Vault. Obecné informace o použití rozhraní Azure REST, včetně ověřování/autorizace a tom, jak získat přístupový token, naleznete v tématu [Reference k rozhraní Azure REST API](https://docs.microsoft.com/rest/api/azure).

## <a name="request-url"></a>Adresa URL požadavku  
 Operace správy klíčů pomocí požadavku HTTP DELETE, GET, PATCH, PUT a HTTP POST a kryptografické operace proti existující klíčové objekty pomocí HTTP POST. Klienti, kteří nemůže zajišťovat podporu pro určité příkazy HTTP může také použít k zadání zamýšlené akce; pomocí hlavičku požadavku HTTP X-HTTP POST požadavky, které obvykle nevyžadují text by měl obsahovat prázdným textem zprávy. při použití HTTP POST, například když místo odstranění příspěvku.  

 Pro práci s objekty ve službě Azure Key Vault, tady jsou adresy URL příklad:  

-   Chcete-li vytvořit klíč s názvem TESTKEY služby Key Vault používá – `PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

-   Chcete-li IMPORTOVAT klíč s názvem IMPORTEDKEY na použití služby Key Vault – `POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

-   Chcete-li získat tajný kód volá MYSECRET služby Key Vault používá – `GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

-   K PODEPSÁNÍ přehled pomocí klíče volá TESTKEY v používání služby Key Vault – `POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

 Autorita pro žádost do služby Key Vault je vždy následujícím způsobem,  `https://{keyvault-name}.vault.azure.net/`  

 Na cestě /keys byly vždy uloženy klíče, tajné klíče byly vždy uloženy v rámci cesty /secrets.  

## <a name="api-version"></a>Verze rozhraní API  
 Službu Azure Key Vault podporuje protokol správy verzí pro zajištění kompatibility s klienty nižší úrovně, ale ne všechny funkce bude k dispozici pro klienty. Musí ho klienti používat `api-version` parametr řetězce k určení verze protokolu, které podporují, protože není žádná výchozí hodnota dotazu.  

 Verze protokolu Azure Key Vault, postupujte podle data schématu pomocí {YYYY} číslování. {MM}. Formát {DD}.  

## <a name="request-body"></a>Text žádosti  
 Podle specifikace protokolu HTTP operace GET musí nemá tělo požadavku a operací POST a PUT musí obsahovat text žádosti. Text v operace odstranění je volitelné protokolu HTTP.  

 Pokud není uvedeno jinak v popisu operace, typu obsahu text požadavku musí být application/json a musí obsahovat serializovaná splňující podmínky objekt JSON pro typ obsahu.  

 Pokud není uvedeno jinak v popisu operaci, musí obsahovat hlavičku požadavku Accept typ média application/json.  

## <a name="response-body"></a>Text odpovědi  
 Pokud není uvedeno jinak v popisu operace, typ obsahu těla odpovědi úspěšné a neúspěšné operace budou application/json a obsahuje podrobné informace o chybě.  

## <a name="using-http-post"></a>Pomocí HTTP POST  
 Někteří klienti nebudou moct určité příkazy HTTP, jako je například PATCH nebo DELETE. Azure Key Vault podporuje HTTP POST jako alternativu pro tyto klienty za předpokladu, že klient také obsahuje hlavičku "X-HTTP-METHOD" pro konkrétní původní HTTP příkaz. Podpora pro metodu POST protokolu HTTP je třeba poznamenat, že pro každé rozhraní API definované v tomto dokumentu.  

## <a name="error-responses"></a>Chybové odpovědi  
 Zpracování chyb použije stavové kódy HTTP. Výsledky jsou:  

-   2xx – Úspěch: použít pro běžné operace. Text odpovědi bude obsahovat očekávaný výsledek  

-   3xx – Přesměrování: The 304 "nedojde ke změně" mohou být vráceny ke splnění podmíněné operace GET. Další kódy 3xx lze v budoucnu k označení změny DNS a cestu.  

-   4xx – chyba klienta: používá se pro chybných požadavků, chybí klíče, chyby syntaxe, neplatný parametry, chyby ověřování, atd. Text odpovědi bude obsahovat vysvětlení podrobné informace o chybě.  

-   5xx – Chyba serveru: používá se pro vnitřní chyby serveru. Text odpovědi bude obsahovat informace o souhrnné chybě.  

 Systém je navržen pro práci za proxy nebo brány firewall. Klient proto může zobrazit v dalších chybových kódech.  

 Služba Azure Key Vault také vrátí informace o chybě v těle odpovědi, když dojde k potížím. Text odpovědi je ve formátu JSON a má podobu:  

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
 Všechny požadavky pro Azure Key Vault musí ověřit. Azure Key Vault podporuje Azure Active Directory přístupové tokeny, které lze získat pomocí OAuth2 [[specifikace RFC6749](http://tools.ietf.org/html/rfc6749)]. 
 
 Další informace o registraci vaší aplikace a ověřování pomocí Azure Key Vault najdete v tématu [registrace klientské aplikace v Azure AD](https://docs.microsoft.com/rest/api/azure/index#register-your-client-application-with-azure-ad).
 
 Přístupové tokeny se musí odeslat do služby pomocí protokolu HTTP autorizační hlavičky:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 Pokud není zadaný přístupový token nebo pokud token není změna přijata službou, bude vrácen do klienta chybu HTTP 401 a bude obsahovat hlavičky WWW-Authenticate, například:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 Parametry v hlavičce WWW-Authenticate jsou:  

-   autorizace: adresa služby autorizace OAuth2, která slouží k získání přístupového tokenu pro požadavek.  

-   prostředek: name prostředek má být použit v žádosti o autorizaci.  

## <a name="see-also"></a>Viz také  
 [Informace o klíčích, tajných kódech a certifikátech](about-keys-secrets-and-certificates.md)
