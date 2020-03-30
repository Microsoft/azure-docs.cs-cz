---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/02/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6fc1f4152b2e16e1597c018e5af6e0245b075c3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255831"
---
## <a name="robots933456-in-logs"></a>robots933456 v protokolech

V protokolech kontejneru se může zobrazit následující zpráva:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Tuto zprávu klidně ignorujte. `/robots933456.txt`je fiktivní cesta URL, kterou služba App Service používá ke kontrole, zda je kontejner schopen obsluhovat požadavky. Odpověď 404 jednoduše označuje, že cesta neexistuje, ale umožňuje službě App Service vědět, že kontejner je v pořádku a připraven reagovat na požadavky.

