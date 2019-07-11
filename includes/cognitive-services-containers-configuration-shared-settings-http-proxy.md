---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 9911b1c92bdca6c0cdf064ea484cfb603e659467
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712484"
---
Pokud je potřeba nakonfigurovat proxy server HTTP pro provedení odchozích požadavků, použijte tyto dva argumenty:

| Name | Typ dat | Popis |
|--|--|--|
|HTTP_PROXY|řetězec|Tento proxy server používat, například `http://proxy:8888`<br>< adresa url proxy serveru >|
|HTTP_PROXY_CREDS|řetězec|žádné přihlašovací údaje potřebné k ověření vůči proxy serveru, například uživatelské_jméno.|
|`<proxy-user>`|řetězec|Uživatel pro proxy server.|
|`proxy-password`|řetězec|Heslo přidružené k `<proxy-user>` pro proxy server.|
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