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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "67175563"
---
## <a name="push-image-to-registry"></a>Odeslání image do registru

Pokud chcete nasdílet image do služby Azure Container Registry, musíte nejprve mít nějakou image. Pokud ještě nemáte žádné image místníkontejner, spusťte následující [příkaz docker pull][docker-pull] pro vytažení existující image z Docker Hub. V tomto příkladu `hello-world` vytáhněte obrázek.

```
docker pull hello-world
```

Předtím než můžete nasdílet image do registru, musíte ji označit plně kvalifikovaným názvem přihlašovacího serveru ACR. Název přihlašovacího serveru je ve formátu * \<název\>registru .azurecr.io* (všechna malá písmena), například *mycontainerregistry007.azurecr.io*.

Označte image pomocí příkazu [docker tag][docker-tag]. Nahraďte `<acrLoginServer>` názvem přihlašovacího serveru vaší instance ACR.

```
docker tag hello-world <acrLoginServer>/hello-world:v1
```

Nakonec pomocí příkazu [docker push][docker-push] nasdílejte image do instance služby ACR. Nahraďte `<acrLoginServer>` názvem přihlašovacího serveru vaší instance ACR. Tento příklad vytvoří úložiště **hello-world,** `hello-world:v1` které obsahuje obrázek.

```
docker push <acrLoginServer>/hello-world:v1
```

Po odeslání bitové kopie do `hello-world:v1` registru kontejnerů odeberte bitovou kopii z místního prostředí Dockeru. (Všimněte si, že tento příkaz [docker rmi][docker-rmi] neodebere bitovou kopii z úložiště **hello-world** v registru kontejnerů Azure.)

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

