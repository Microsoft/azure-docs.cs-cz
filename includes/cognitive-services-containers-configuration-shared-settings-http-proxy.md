---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2019
ms.openlocfilehash: 2d3d7b37721ca1b19f5d73133352cabdbffe6d68
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "58886838"
---
Pokud je potřeba nakonfigurovat proxy server HTTP pro provedení odchozích požadavků, použijte tyto dva argumenty:

| Název | Typ dat | Popis |
|--|--|--|
|HTTP_PROXY|string|Tento proxy server používat, například `http://proxy:8888`|
|HTTP_PROXY_CREDS|string|žádné přihlašovací údaje potřebné k ověření vůči proxy serveru, například uživatelské_jméno.|

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=http://190.169.1.6:3128 \
HTTP_PROXY_CREDS=jerry:123456 \
```