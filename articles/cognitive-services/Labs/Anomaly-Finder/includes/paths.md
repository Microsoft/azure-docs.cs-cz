---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 80503ad154a9fc4d01614ffd2816f9d5fd497fdb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309483"
---
<a name="paths"></a>
## <a name="paths"></a>Cesty

<a name="anomalydetection-post"></a>
### <a name="detect-anomaly-points-for-the-time-series-data-points-requested"></a>Zjištění anomálií bodů pro požadovaný čas datových bodů řady
```
POST /anomalydetection
```


#### <a name="parameters"></a>Parametry

|Type|Název|Popis|Schéma|
|---|---|---|---|
|**Text**|**Text**  <br>*Vyžaduje*|Časové řady datových bodů a je-li potřeba.|[Požadavek](#request)|


#### <a name="responses"></a>Odpovědi

|Kód HTTP|Popis|Schéma|
|---|---|---|
|**200**|Úspěšná operace.|< [odpověď](#response) > pole|
|**400**|Nejde parsovat JSON žádosti.|Žádný obsah|
|**403**|Certifikát, který jste zadali nebyla přijata serverem.|Žádný obsah|
|**405**|Metoda není povolena.|Žádný obsah|
|**500**|Interní chyba serveru|Žádný obsah|


#### <a name="consumes"></a>Využívá

* `application/json`


#### <a name="produces"></a>Vytvoří

* `application/json`


#### <a name="tags"></a>Značky

* anomalydetection, který



