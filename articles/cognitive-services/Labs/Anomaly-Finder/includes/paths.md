---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: a806cac410eb57e59dacb42da9be954b2f962956
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343014"
---
<a name="paths"></a>
## <a name="paths"></a>Cesty

<a name="anomalydetection-post"></a>
### <a name="detect-anomaly-points-for-the-time-series-data-points-requested"></a>Zjištění anomálií bodů pro požadovaný čas řadu datových bodů
```
POST /anomalydetection
```


#### <a name="parameters"></a>Parametry

|Typ|Název|Popis|Schéma|
|---|---|---|---|
|**Text**|**text**  <br>*Požadované*|Časové řady datové body a období, v případě potřeby.|[Požadavek](#request)|


#### <a name="responses"></a>Odezvy

|Kód HTTP|Popis|Schéma|
|---|---|---|
|**200**|Úspěšná operace.|< [odpověď](#response) > pole|
|**400**|Nelze analyzovat požadavku JSON.|Žádný obsah|
|**403**|Certifikát, který jste zadali nebyla přijata serverem.|Žádný obsah|
|**405**|Metoda není povolena.|Žádný obsah|
|**500**|Interní chyba serveru|Žádný obsah|


#### <a name="consumes"></a>Využívá

* `application/json`


#### <a name="produces"></a>Vytvoří

* `application/json`


#### <a name="tags"></a>Značky

* anomalydetection



