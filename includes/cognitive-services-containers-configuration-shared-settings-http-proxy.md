---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7f51b7fe95445cbd3a8aff530f89ce55b5abfb1e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629893"
---
Pokud potřebujete nakonfigurovat proxy server HTTP pro vytváření odchozích požadavků, použijte tyto dva argumenty:

| Name | Datový typ | Popis |
|--|--|--|
|HTTP_PROXY|řetězec|Proxy, který se má použít, například `http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|řetězec|Jakékoli přihlašovací údaje potřebné k ověření vůči proxy serveru, například `username:password` . Tato hodnota **musí být malými písmeny**. |
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
