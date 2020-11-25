---
title: zahrnout soubor
description: zahrnout soubor
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 08/04/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6a16106495e584c9acbc02d380242df665f35ce5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019992"
---
## <a name="push-image-to-registry"></a>Odeslání image do registru

Pokud chcete nasdílet image do služby Azure Container Registry, musíte nejprve mít nějakou image. Pokud ještě nemáte žádné místní image kontejneru, spusťte následující příkaz [Docker Pull][docker-pull] , který vyžádá existující image z Docker Hub. V tomto příkladu si přetáhnete `hello-world` obrázek.

```
docker pull hello-world
```

Před nahráním image do registru je nutné ji označit pomocí plně kvalifikovaného názvu přihlašovacího serveru registru. Název přihlašovacího serveru je ve formátu *\<registry-name\> . azurecr.IO* (musí být malými písmeny), například *mycontainerregistry.azurecr.IO*.

Označte image pomocí příkazu [docker tag][docker-tag]. Nahraďte `<login-server>` názvem přihlašovacího serveru vaší instance ACR.

```
docker tag hello-world <login-server>/hello-world:v1
```

Příklad:

```
docker tag hello-world mycontainerregistry.azurecr.io/hello-world:v1
```


Nakonec pomocí příkazu [Docker push][docker-push] nahrajte image do instance registru. Nahraďte `<login-server>` názvem přihlašovacího serveru vaší instance registru. Tento příklad vytvoří úložiště **Hello-World** obsahující `hello-world:v1` image.

```
docker push <login-server>/hello-world:v1
```

Po nahrání image do registru kontejneru odeberte `hello-world:v1` image z místního prostředí Docker. (Všimněte si, že tento příkaz [Docker RMI][docker-rmi] neodebere image z úložiště **Hello-World** ve vašem registru kontejneru Azure.)

```
docker rmi <login-server>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

