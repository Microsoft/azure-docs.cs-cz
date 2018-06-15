---
title: Migrace z Azure videa Indexer API v1 na v2 | Microsoft Docs
description: Toto téma vysvětluje, jak migrovat z rozhraní API služby Azure Video Indexer v1 v2.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 05/13/2018
ms.author: juliako
ms.openlocfilehash: 00f5a0d7c9a37c24866cd5e1259800d5e431ccc3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35346023"
---
# <a name="migrate-from-the-video-indexer-api-v1-to-v2"></a>Migrace z Video Indexer API v1 na v2

> [!Note]
> Rozhraní API V1 videu Indexer jsou už zastaralé a bude odebrána na 1. srpna 2018. Měli byste začít pomocí rozhraní API v2 Video Indexer, aby se zabránilo přerušení.
>
> Pro vývoj pomocí rozhraní API v2 Video Indexer, pokynů naleznete [zde](https://api-portal.videoindexer.ai/). 

Tento článek popisuje změny, které byly zavedeny v v2.  

## <a name="api-changes"></a>Rozhraní API změny

### <a name="authorization-and-operations"></a>Operace a autorizace

Ve verzi v2 Video Indexer změnit model ověřování a autorizace rozhraní API. Existují dvě sady rozhraní API: 

* Autorizace 
* Operace

**Autorizace** rozhraní API slouží k získání přístupových tokenů pro volání **Operations** rozhraní API. **Operations** rozhraní API obsahuje všechna Video Indexer rozhraní API, jako je například nahrávání video, Statistika Get a další operace.

Jednou budete [přihlášení k odběru](video-indexer-get-started.md) k **autorizace** rozhraní API, bude možné získat přístupové tokeny předáním svůj klíč předplatného (stejně jako jste to udělali v v1.)

## <a name="getting-and-using-the-access-token-for-operations-apis"></a>Získávání a použití tokenu přístupu pro operace rozhraní API

Při volání metody **Operations** už nebude používat rozhraní API, klíč předplatného. Místo toho předá přístupové tokeny, získat **autorizace** rozhraní API. 

Každý požadavek by měl mít platný token, odpovídající úroveň přístupu tohoto rozhraní API jsou volání. Operace u uživatelů, jako je například získávání vaše účty, například vyžadovat token přístupu uživatele. Operace v účtu úrovně, jako je například seznam všech videa, vyžadují účet přístupový token. Operace na videa, jako je například nové indexování video, vyžadují účet přístupový token nebo video přístupový token.

Chcete-li zájmu usnadnění můžete použít **autorizace** rozhraní API > **GetAccounts** se získat token nejprve vaše účty bez získání uživatele. Můžete také požádat získat účty s platné tokeny, umožňuje přejděte další volání s cílem získat token účtu.

Další informace o různých přístupových tokenů, najdete v části [rozhraní API pomocí Azure Video Indexer](video-indexer-use-apis.md).

### <a name="locations"></a>Umístění

Každé volání rozhraní API by měla obsahovat umístění účtu Video Indexer. Volání rozhraní API bez umístění nebo s nesprávnou umístění se nezdaří.

Použít hodnoty popsané v následující tabulce. **Param hodnota** při předáte hodnotu používá rozhraní API.

|**Název**|**Hodnota parametru**|**Popis**|
|---|---|---|
|Zkušební verze|Zkušební verze|Použít pro zkušebními účty. Například: https://api.videoindexer.ai/trial/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|
|Západní USA|westus2|Používá se pro oblast Azure západní USA 2.  Například: https://api.videoindexer.ai/westus2/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|
|Severní Evropa |northeurope|Používá se pro oblast Severní Evropa Azure. Například https://api.videoindexer.ai/northeurope/Accounts/{accountId}/Videos/{videoId}/Index?language=English. |
|Východní Asie|eastasia|Používá se pro oblast Azure východní Asie. Například https://api.videoindexer.ai/eastasia/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|

### <a name="data-model"></a>Datový Model

Video Indexer má nyní zjednodušené datového modelu k poskytování mnohem lepší přehled. Další informace o výstup vytvořený pomocí rozhraní API v2 najdete v tématu [vyhledejte ve výstupu videa Indexer vytvořeného rozhraním v2 API](video-indexer-output-json-v2.md).

### <a name="swagger"></a>Swagger

Definice rozhraní API Indexer Video byly příslušným způsobem aktualizuje a jsou k dispozici ke stažení prostřednictvím [rozhraní API portálu](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token).


### <a name="v1-vs-v2-examples"></a>V1 vs příklady V2

Nová rozhraní API V2 zahrnují 3 hlavní parametry:

1. [Umístění] – jak bylo popsáno výše. Buď zkušební verze, westus2, northeurope nebo eastasia.
2. [YOUR_ACCOUNT_ID] - A Guid id vašeho účtu. Načíst při získávání všechny účty (popsaný níže).
3. [YOUR_VIDEO_ID] - id videa (například "d4fa369abc"). Vrátí při nahrávání video nebo při hledání videa.

#### <a name="uploading-a-video-in-v1"></a>Odesílání video v V1:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns?name=some_name&description=some_description&privacy=private&videoUrl=http://URL_TO_YOUR_VIDEO
```

#### <a name="uploading-a-video-in-v2"></a>Odesílání video v V2:

1. Získání tokenu přístupu pro odeslání žádosti:

  Buď můžete získat všechny účty a přístupových tokenů:

    ```
    https://api.videoindexer.ai/auth/[LOCATION]/Accounts?generateAccessTokens=true&allowEdit=true
    ```

  Nebo získání tokenu přístupu konkrétním účet:
  
  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/AccessToken?allowEdit=true
  ```
2. Odešlete video:

  ```
  POST https://api.videoindexer.ai/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos?name=MySample&description=MySampleDescription&videoUrl=[URL_ENCODED_VIDEO_URL]&accessToken=eyJ0eXAiOiJ...
  ```

#### <a name="getting-insights-in-v1"></a>Analyzovat v V1:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]
```
  
#### <a name="getting-insights-in-v2"></a>Analyzovat v V2:

1. Použijte účet přístupového tokenu nebo získat token video úroveň přístupu:

  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/AccessToken
  ```
  
2. Získejte přehledy:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

#### <a name="getting-video-processing-state-in-v1"></a>Načítání stavu zpracování videa v V1:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]/State
```
  
#### <a name="getting-video-processing-state-in-v2"></a>Načítání stavu zpracování videa v V2:

V rozhraní API v2 se vrátí stav zpracování v rámci rozhraní API získat Index Video.

1. Použijte účet přístupového tokenu nebo získat token video úroveň přístupu:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```
  
2. Získejte přehledy:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

## <a name="next-steps"></a>Další postup

[Použití Azure videa Indexer rozhraní API](video-indexer-use-apis.md)

