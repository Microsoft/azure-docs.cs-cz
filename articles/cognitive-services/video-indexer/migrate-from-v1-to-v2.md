---
title: Migrace z Azure Video Indexer API v1 na v2
titlesuffix: Azure Cognitive Services
description: Toto téma vysvětluje, jak migrovat z rozhraní Azure Video Indexer API v1 na v2.
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: juliako
ms.openlocfilehash: 3c70bbe11d94e6b03d615b8d1394ccdca6bd3790
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985625"
---
# <a name="migrate-from-the-video-indexer-api-v1-to-v2"></a>Migrace z rozhraní Video Indexer API v1 na v2

> [!Note]
> 1. srpna 2018 se přestala nabízet Video Indexer V1 API. Byste teď měli použít rozhraní Video Indexer API v2. <br/>Vývoj pomocí rozhraní API pro Video Indexer v2, najdete pokyny, nalezeno [tady](https://api-portal.videoindexer.ai/). 

Tento článek popisuje změny, které byly zavedeny v v2.  

## <a name="api-changes"></a>Změny rozhraní API

### <a name="authorization-and-operations"></a>Autorizace a operace

Ve verzi v2 Video Indexer změnit model ověřování a autorizace rozhraní API. Existují dvě sady rozhraní API: 

* Autorizace 
* Operace

**Autorizace** rozhraní API slouží k získání přístupových tokenů pro volání **operace** rozhraní API. **Operace** rozhraní API obsahuje všechna rozhraní Video Indexer API, jako jsou nahrávání videa, získejte přehled a další operace.

Jakmile [odběru](video-indexer-get-started.md) k **autorizace** rozhraní API, bude možné získat přístupové tokeny předáním váš klíč předplatného (stejně jako ve v1.)

## <a name="getting-and-using-the-access-token-for-operations-apis"></a>Získání a pomocí přístupového tokenu pro operace rozhraní API

Při volání **operace** už nebude používat rozhraní API, klíč předplatného. Místo toho předáte přístupové tokeny získat **autorizace** rozhraní API. 

Každý požadavek by měl mít platný token, odpovídající úroveň přístupu jsou volání rozhraní API. Operace pro uživatele, jako je například získávání vaše účty, například vyžadovat přístupový token uživatele. Operace na účtu úrovni, jako je například seznam všech videí, vyžadovat přístupový token účtu. Operace na videa, jako jsou videa reindex vyžadují přístupový token účtu nebo video přístupový token.

Chcete-li usnadnili, můžete použít **autorizace** rozhraní API > **GetAccounts** k získání tokenu nejprve vašich účtů bez získání uživatele. Můžete také požádat účet s platným tokeny, můžete přeskočit další volání pro získání tokenu účtu.

Další informace o různých přístupové tokeny, naleznete v tématu [použití Azure Video Indexer API](video-indexer-use-apis.md).

### <a name="locations"></a>Umístění

Každé volání rozhraní API by měl obsahovat umístění váš účet služby Video Indexer. Volání rozhraní API bez umístění nebo nesprávné umístění se nezdaří.

Použít s hodnotami popsanými v následující tabulce. **Param value** je hodnota, pokud předáte pomocí rozhraní API.

|**Název**|**Hodnota parametru**|**Popis**|
|---|---|---|
|Zkušební verze|Zkušební verze|Používá pro účty. Například: https://api.videoindexer.ai/trial/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|
|USA – západ|westus2|Používá se pro oblasti Azure USA – západ 2.  Například: https://api.videoindexer.ai/westus2/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|
|Severní Evropa |northeurope|Používá se pro oblast Evropa – sever Azure. Například https://api.videoindexer.ai/northeurope/Accounts/{accountId}/Videos/{videoId}/Index?language=English. |
|Východní Asie|eastasia|Používá se pro oblast Azure východní Asie. Například https://api.videoindexer.ai/eastasia/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|

### <a name="data-model"></a>Datový Model

Video Indexer má nyní jednodušší datový model, který poskytují mnohem lepší přehledy. Další informace o výstup vytvořený pomocí rozhraní API v2 najdete v tématu [prozkoumání výstupu funkce Video Indexer vytvořené metodou rozhraní API v2](video-indexer-output-json-v2.md).

### <a name="swagger"></a>Swagger

Video Indexer API definice se příslušným způsobem aktualizuje a jsou k dispozici ke stažení prostřednictvím [portál pro vývojáře Video Indexer](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token).


### <a name="v1-vs-v2-examples"></a>V1 a V2 příklady

Nová rozhraní API V2 zahrnují 3 hlavní parametry:

1. [Umístění] – jak je popsáno výše. Zkušební verze, westus2, northeurope nebo východní Asie.
2. [YOUR_ACCOUNT_ID] - identifikátor Guid A id vašeho účtu. Načíst při získávání všech účtů (popsaných níže).
3. [YOUR_VIDEO_ID] - id videa (například "d4fa369abc"). Vrátí při nahrávání videa nebo při hledání videí.

#### <a name="uploading-a-video-in-v1"></a>Nahrávání videa ve V1:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns?name=some_name&description=some_description&privacy=private&videoUrl=http://URL_TO_YOUR_VIDEO
```

#### <a name="uploading-a-video-in-v2"></a>Nahrávání videa ve verzi V2:

1. Získání přístupového tokenu pro požadavek na odeslání:

  Můžete buď získat všechny účty a jeho přístupových tokenů:

    ```
    https://api.videoindexer.ai/auth/[LOCATION]/Accounts?generateAccessTokens=true&allowEdit=true
    ```

  Nebo získání tokenu přístupu konkrétní účet:
  
  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/AccessToken?allowEdit=true
  ```
2. Nahrání videa:

  ```
  POST https://api.videoindexer.ai/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos?name=MySample&description=MySampleDescription&videoUrl=[URL_ENCODED_VIDEO_URL]&accessToken=eyJ0eXAiOiJ...
  ```

#### <a name="getting-insights-in-v1"></a>Získávají se přehledy ve V1:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]
```
  
#### <a name="getting-insights-in-v2"></a>Získávají se přehledy ve verzi V2:

1. Použijte přístupový token účtu nebo získání tokenu přístupu na úrovni videa:

  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/AccessToken
  ```
  
2. Získejte přehledy:

  ```
  https://api.videoindexer.ai/[LOCATION]/Accounts[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

#### <a name="getting-video-processing-state-in-v1"></a>Načítání stavu zpracování videa v V1:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]/State
```
  
#### <a name="getting-video-processing-state-in-v2"></a>Načítání stavu zpracování videa ve verzi V2:

V rozhraní API v2 je stav zpracování vrácena jako součást rozhraní API získat Index Video.

1. Použijte přístupový token účtu nebo získání tokenu přístupu na úrovni videa:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```
  
2. Získejte přehledy:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

## <a name="next-steps"></a>Další postup

[Použití Azure rozhraní Video Indexer API](video-indexer-use-apis.md)

