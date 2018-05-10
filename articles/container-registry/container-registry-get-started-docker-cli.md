---
title: Obrázek Docker nabízené privátní Azure registru
description: Nahrání a stažení imagí Dockeru do soukromého registru kontejnerů v Azure pomocí rozhraní příkazového řádku Dockeru
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 11/29/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d729a45b28ad02a652c265974d46fe1aaf752198
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Nahrání první image do soukromého registru kontejnerů Dockeru pomocí rozhraní příkazového řádku Dockeru

Registr kontejnerů Azure uchovává a spravuje privátní image kontejnerů [Dockeru](http://hub.docker.com) podobným způsobem, jakým [Docker Hub](https://hub.docker.com/) uchovává veřejné image Dockeru. Můžete použít [rozhraní příkazového řádku Dockeru](https://docs.docker.com/engine/reference/commandline/cli/) (příkazového řádku Dockeru) pro [přihlášení](https://docs.docker.com/engine/reference/commandline/login/), [nabízené](https://docs.docker.com/engine/reference/commandline/push/), [vyžádání](https://docs.docker.com/engine/reference/commandline/pull/)a další operace na vaše kontejneru registru.

V následujících krocích stáhnout oficiální [Nginx image](https://store.docker.com/images/nginx) z veřejného úložiště Docker Hub registru, značka pro vaši privátní kontejner Azure registru, poslat ho přímo v registru a pak ho načítat z registru.

## <a name="prerequisites"></a>Požadavky

* **Registr kontejnerů Azure** – Vytvořte registr kontejnerů ve svém předplatném Azure. Použijte k tomu například [Azure Portal](container-registry-get-started-portal.md) nebo [Azure CLI 2.0 ](container-registry-get-started-azure-cli.md).
* **Rozhraní příkazového řádku dockeru** – Pokud chcete nastavit místní počítač jako hostitele Docker a přístup k příkazům příkazového řádku Dockeru, nainstalujte [Docker](https://docs.docker.com/engine/installation/).

## <a name="log-in-to-a-registry"></a>Přihlášení k registru

Existují [několik způsobů, jak ověřit](container-registry-authentication.md) vaší privátní kontejneru registru. Doporučujeme při práci na příkazovém řádku je pomocí příkazu příkazového řádku Azure CLI [az acr přihlášení](/cli/azure/acr?view=azure-cli-latest#az_acr_login). Například k přihlášení do registru s názvem *myregistry*:

```azurecli
az acr login --name myregistry
```

Můžete také protokolu s [docker přihlášení](https://docs.docker.com/engine/reference/commandline/login/). Následující příklad předá ID a heslo [instančního objektu](../active-directory/active-directory-application-objects.md) Azure Active Directory. Například můžete mít [přiřazen objekt služby](container-registry-authentication.md#service-principal) do registru scénář automatizace.

```Bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Vrátí oba příkazy `Login Succeeded` po dokončení. Pokud používáte `docker login`, můžete si také prohlédnout upozornění zabezpečení doporučujeme použít `--password-stdin` parametr. I když je jeho použití nad rámec tohoto článku, doporučujeme řídit se osvědčeným postupem. Další informace najdete v tématu [docker přihlášení](https://docs.docker.com/engine/reference/commandline/login/) příkaz odkaz.

> [!TIP]
> Vždy zadejte název plně kvalifikovaný registru (všechny malá písmena) při použití `docker login` a když můžete označit bitových kopií pro vkládání do registru. V příkladech v tomto článku je plně kvalifikovaný název *myregistry.azurecr.io*.

## <a name="pull-the-official-nginx-image"></a>Bitovou kopii oficiální Nginx pro vyžádání obsahu

Nejprve stáhněte veřejné Nginx bitovou kopii do místního počítače.

```Bash
docker pull nginx
```

## <a name="run-the-container-locally"></a>Místní spuštění kontejneru

Provést následující [docker spustit](https://docs.docker.com/engine/reference/run/) příkaz ke spuštění místní instanci kontejner nginx a sváže interaktivně (`-it`) na portu 8080. `--rm` Argument určuje, že má být odebrána kontejneru při jeho zastavení.

```Bash
docker run -it --rm -p 8080:80 nginx
```

Přejděte do [ http://localhost:8080 ](http://localhost:8080) zobrazíte obsloužených Nginx v kontejneru spuštěné výchozí webové stránky. Měli byste vidět stránka podobná následující:

![Server Nginx na místním počítači](./media/container-registry-get-started-docker-cli/nginx.png)

Protože jste spustili kontejneru interaktivně s `-it`, najdete v části Nginx server výstupu na příkazovém řádku po přechodu na ni v prohlížeči.

Chcete-li zastavit a odstranit kontejner, stiskněte `Control` + `C`.

## <a name="create-an-alias-of-the-image"></a>Vytvoření aliasu bitové kopie

Použití [docker značky](https://docs.docker.com/engine/reference/commandline/tag/) k vytvoření zástupce bitové kopie s plně kvalifikovaná cesta k registru. V tomto příkladu se určí obor názvů `samples`, aby se zabránilo nepořádku v kořenovém adresáři registru.

```Bash
docker tag nginx myregistry.azurecr.io/samples/nginx
```

Další informace o označování s obory názvů najdete v tématu [obory názvů úložiště](container-registry-best-practices.md#repository-namespaces) části [osvědčené postupy pro Azure kontejneru registru](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>Push bitovou kopii do registru

Teď, když jste označili bitové kopie s plně kvalifikovanou cestu k vaší privátní registru, můžete ho push registru s [docker nabízené](https://docs.docker.com/engine/reference/commandline/push/):

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Bitovou kopii pro vyžádání obsahu z registru

Použití [docker vyžádání](https://docs.docker.com/engine/reference/commandline/pull/) příkaz, který bitovou kopii pro vyžádání obsahu z registru:

```Bash
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Spusťte kontejner nginx a sváže

Použití [docker spustit](https://docs.docker.com/engine/reference/run/) příkaz ke spuštění bitovou kopii si vyžádat z registru:

```Bash
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Přejděte do [ http://localhost:8080 ](http://localhost:8080) zobrazíte kontejneru spuštěné.

Chcete-li zastavit a odstranit kontejner, stiskněte `Control` + `C`.

## <a name="remove-the-image-optional"></a>Odeberte bitovou kopii (volitelné)

Pokud již nepotřebujete bitovou kopii Nginx, chcete-li odstranit místně s [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) příkaz.

```Bash
docker rmi myregistry.azurecr.io/samples/nginx
```

Odebrat bitové kopie z registru kontejner Azure, můžete použít příkaz příkazového řádku Azure CLI [az acr úložiště odstranit](/cli/azure/acr/repository#az_acr_repository_delete). Například následující příkaz odstraní manifest odkazuje značku, všechna data přidružená vrstvy a všechny ostatní značky odkazující na manifest.

```azurecli
az acr repository delete --name myregistry --repository samples/nginx --tag latest --manifest
```

## <a name="next-steps"></a>Další postup

Nyní když znáte základy, jste připraveni začít používat registr! Nasazení bitových kopií kontejneru z registru na:

* [Služba Azure Kubernetes (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Kontejner Azure instancí](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)
