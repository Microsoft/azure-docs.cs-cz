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
ms.openlocfilehash: 7ed8b96a528d56b28262936c4b200762b3e93b8e
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302239"
---
## <a name="push-image-to-registry"></a>Odeslání image do registru

Pokud chcete nasdílet image do služby Azure Container Registry, musíte nejprve mít nějakou image. Pokud ještě nemáte žádné Image místního kontejneru, spusťte následující příkaz [operace docker pull] [ docker-pull] příkaz a vyžádejte si existující image z Docker Hubu. V tomto příkladu o přijetí změn `hello-world` bitové kopie.

```Docker
docker pull hello-world
```

Předtím než můžete nasdílet image do registru, musíte ji označit plně kvalifikovaným názvem přihlašovacího serveru ACR. Název přihlašovacího serveru je ve formátu  *\<název registru\>. azurecr.io* (malými písmeny), například *mycontainerregistry007.azurecr.io*.

Označte image pomocí příkazu [docker tag][docker-tag]. Nahraďte `<acrLoginServer>` názvem přihlašovacího serveru vaší instance ACR.

```Docker
docker tag hello-world <acrLoginServer>/hello-world:v1
```

Nakonec pomocí příkazu [docker push][docker-push] nasdílejte image do instance služby ACR. Nahraďte `<acrLoginServer>` názvem přihlašovacího serveru vaší instance ACR. Tento příklad vytvoří **hello-world** úložiště, který obsahuje `hello-world:v1` image.

```Docker
docker push <acrLoginServer>/hello-world:v1
```

Po nasdílení změn image do vašeho registru kontejneru, odeberte `hello-world:v1` obrázek z vašeho místního prostředí Docker. (Všimněte si, že tento [docker rmi] [ docker-rmi] příkazem neodebere image z **hello-world** úložiště v Azure container registry.)

```Docker
docker rmi <acrLoginServer>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

