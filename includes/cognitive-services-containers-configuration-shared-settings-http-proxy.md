---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 664cea26f910fa5b3354e2879a33de50eb13a7f3
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286318"
---
Pokud potřebujete nakonfigurovat proxy server HTTP pro vytváření odchozích požadavků, použijte tyto dva argumenty:

| Name | Typ dat | Popis |
|--|--|--|
|HTTP_PROXY|řetězec|proxy, který se má použít, například`http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|řetězec|jakékoli přihlašovací údaje potřebné k ověření vůči proxy serveru, například uživatelské jméno: heslo.|
|`<proxy-user>`|řetězec|Uživatel pro proxy|
|`proxy-password`|řetězec|Heslo spojené s `<proxy-user>` pro proxy server.|
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
