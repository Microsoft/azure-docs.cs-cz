---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2019
ms.openlocfilehash: 493810e85ef535005e75f4ac37fcb1920e5d8549
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56741262"
---
Pokud je potřeba nakonfigurovat proxy server HTTP pro provedení odchozích požadavků, použijte tyto dva argumenty:

| Název | Typ dat | Popis |
|--|--|--|
|HTTP_PROXY|řetězec|Tento proxy server používat, například http://proxy:8888|
|HTTP_PROXY_CREDS|řetězec|žádné přihlašovací údaje potřebné k ověření vůči proxy serveru, například uživatelské_jméno.|

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=http://190.169.1.6:3128 \
HTTP_PROXY_CREDS=jerry:123456 \
Logging:Disk:LogLevel=Debug Logging:Disk:Format=json
```