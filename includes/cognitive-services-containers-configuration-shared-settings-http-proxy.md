---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: fe1b4699a300831294c26b103d322fb83ad87d3b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116691"
---
Pokud je potřeba nakonfigurovat proxy server HTTP pro provedení odchozích požadavků, použijte tyto dva argumenty:

| Název | Typ dat | Popis |
|--|--|--|
|HTTP_PROXY|string|Tento proxy server používat, například `http://proxy:8888`<br>< adresa url proxy serveru >|
|HTTP_PROXY_CREDS|string|žádné přihlašovací údaje potřebné k ověření vůči proxy serveru, například uživatelské_jméno.|
|`<proxy-user>`|string|Uživatel pro proxy server.|
|`proxy-password`|string|Heslo přidružené k `<proxy-user>` pro proxy server.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```