---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 84cd8ed79281b005407b5a857398b5669635c072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68320499"
---
Pokud potřebujete nakonfigurovat proxy http pro vytváření odchozích požadavků, použijte tyto dva argumenty:

| Name (Název) | Datový typ | Popis |
|--|--|--|
|HTTP_PROXY|řetězec|Proxy použít, například,`http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|řetězec|Všechna pověření potřebná k ověření proti proxy serveru, například uživatelské jméno:heslo.|
|`<proxy-user>`|řetězec|Uživatel proxy serveru.|
|`<proxy-password>`|řetězec|Heslo přidružené `<proxy-user>` k serveru proxy.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
