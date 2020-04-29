---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 84cd8ed79281b005407b5a857398b5669635c072
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "68320499"
---
Pokud potřebujete nakonfigurovat proxy server HTTP pro vytváření odchozích požadavků, použijte tyto dva argumenty:

| Název | Datový typ | Popis |
|--|--|--|
|HTTP_PROXY|řetězec|Proxy, který se má použít, například`http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|řetězec|Jakékoli přihlašovací údaje potřebné k ověření vůči proxy serveru, například uživatelské jméno: heslo.|
|`<proxy-user>`|řetězec|Uživatel pro proxy|
|`<proxy-password>`|řetězec|Heslo spojené s `<proxy-user>` pro proxy server.|
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
