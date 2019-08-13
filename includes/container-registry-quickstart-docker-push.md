---
title: zahrnout soubor
description: zahrnout soubor
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 01/23/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 09eaf9465ec3912dea6e1f3ee1693f6bfed50abc
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "67175563"
---
## <a name="push-image-to-registry"></a>Odeslání image do registru

Pokud chcete nasdílet image do služby Azure Container Registry, musíte nejprve mít nějakou image. Pokud ještě nemáte žádné místní image kontejneru, spusťte následující příkaz [Docker Pull][docker-pull] , který vyžádá existující image z Docker Hub. V `hello-world` tomto příkladu si přetáhnete obrázek.

```
docker pull hello-world
```

Předtím než můžete nasdílet image do registru, musíte ji označit plně kvalifikovaným názvem přihlašovacího serveru ACR. Název přihlašovacího serveru je ve formátu  *\<Registry-name\>. azurecr.IO* (všechna malá písmena), například *mycontainerregistry007.azurecr.IO*.

Označte image pomocí příkazu [docker tag][docker-tag]. Nahraďte `<acrLoginServer>` názvem přihlašovacího serveru vaší instance ACR.

```
docker tag hello-world <acrLoginServer>/hello-world:v1
```

Nakonec pomocí příkazu [docker push][docker-push] nasdílejte image do instance služby ACR. Nahraďte `<acrLoginServer>` názvem přihlašovacího serveru vaší instance ACR. Tento příklad vytvoří úložiště **Hello-World** obsahující `hello-world:v1` image.

```
docker push <acrLoginServer>/hello-world:v1
```

Po nahrání image do registru kontejneru odeberte `hello-world:v1` image z místního prostředí Docker. (Všimněte si, že tento příkaz [Docker RMI][docker-rmi] neodebere image z úložiště **Hello-World** ve vašem registru kontejneru Azure.)

```
docker rmi <acrLoginServer>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

