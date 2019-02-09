---
title: Nahrání image Dockeru do privátní registr kontejnerů Azure
description: Nahrání a stažení imagí Dockeru do soukromého registru kontejnerů v Azure pomocí rozhraní příkazového řádku Dockeru
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/23/2019
ms.author: danlep
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: c27af57ce4fa80a4ae167ce1e27018d049923a3f
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55982841"
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Nahrání první image do soukromého registru kontejnerů Dockeru pomocí rozhraní příkazového řádku Dockeru

Registr kontejnerů Azure uchovává a spravuje privátní image kontejnerů [Dockeru](http://hub.docker.com) podobným způsobem, jakým [Docker Hub](https://hub.docker.com/) uchovává veřejné image Dockeru. Můžete použít [rozhraní příkazového řádku Dockeru](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) pro [přihlášení](https://docs.docker.com/engine/reference/commandline/login/), [nabízených](https://docs.docker.com/engine/reference/commandline/push/), [o přijetí změn](https://docs.docker.com/engine/reference/commandline/pull/)a další operace na kontejnerech registru.

V následujících krocích stažení oficiální [image serveru Nginx](https://store.docker.com/images/nginx) z veřejného registru Docker Hub, označte ji pro váš privátní registr kontejnerů Azure, nahrajte ho do vašeho registru a pak přetahování z registru.

## <a name="prerequisites"></a>Požadavky

* **Registr kontejnerů Azure** – Vytvořte registr kontejnerů ve svém předplatném Azure. Například použít [webu Azure portal](container-registry-get-started-portal.md) nebo [rozhraní příkazového řádku Azure](container-registry-get-started-azure-cli.md).
* **Rozhraní příkazového řádku dockeru** – je také nutné mít Docker nainstalovaný místně. Docker nabízí balíčky pro snadnou konfiguraci Dockeru na jakékoli [macOS] [docker-mac], [Windows] [docker-windows] nebo [Linux] [docker-linux] systému.

## <a name="log-in-to-a-registry"></a>Přihlášení k registru

Existují [několik způsobů, jak ověřit](container-registry-authentication.md) pro váš privátní registr kontejnerů. Je doporučeným způsobem při práci v příkazovém řádku pomocí příkazu Azure CLI [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login). Například pro přihlášení k registru s názvem *myregistry*:

```azurecli
az acr login --name myregistry
```

Také se můžete přihlásit pomocí [docker login](https://docs.docker.com/engine/reference/commandline/login/). Například můžete mít [přiřadit instanční objekt služby](container-registry-authentication.md#service-principal) do vašeho registru pro účely scénáře automatizace. Když spustíte následující příkaz, interaktivně zadejte ID aplikace instančního objektu služby (uživatelské jméno) a heslo po zobrazení výzvy. Osvědčené postupy pro správu přihlašovací údaje, najdete v článku [docker login](https://docs.docker.com/engine/reference/commandline/login/) referenčních příkazu:

```
docker login myregistry.azurecr.io
```

Vrátí oba příkazy `Login Succeeded` po dokončení.

> [!TIP]
> Vždy plně kvalifikovaný název registru (malými písmeny) zadejte při použití `docker login` a kdy označit Image pro doručením (push) do svého registru. V příkladech v tomto článku, je plně kvalifikovaný název *myregistry.azurecr.io*.

## <a name="pull-the-official-nginx-image"></a>Stáhněte si oficiální image serveru Nginx

Nejprve si stáhněte veřejnou image serveru Nginx do místního počítače.

```
docker pull nginx
```

## <a name="run-the-container-locally"></a>Místní spuštění kontejneru

Spusťte následující [dockeru spustit](https://docs.docker.com/engine/reference/run/) příkaz spustit místní instanci kontejneru Nginx interaktivně (`-it`) na portu 8080. `--rm` Argument určuje, že má být odebrána kontejneru při jeho zastavení.

```
docker run -it --rm -p 8080:80 nginx
```

Přejděte do [ http://localhost:8080 ](http://localhost:8080) Chcete-li zobrazit výchozí webová stránka obsluhuje Nginx v spuštěný kontejner. Zobrazí se stránka podobná následujícímu:

![Server Nginx na místním počítači](./media/container-registry-get-started-docker-cli/nginx.png)

Protože jste spustili kontejner interaktivně s `-it`, můžete zobrazit výstup serveru Nginx na příkazovém řádku po přechodu na něj v prohlížeči.

Chcete-li zastavit a odebrat kontejneru, stiskněte `Control` + `C`.

## <a name="create-an-alias-of-the-image"></a>Vytvoření aliasu Image

Použití [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) vytvoření aliasu Image s plně kvalifikovanou cestou k vašemu registru. V tomto příkladu se určí obor názvů `samples`, aby se zabránilo nepořádku v kořenovém adresáři registru.

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```

Další informace o značkách s obory názvů najdete v tématu [obory názvů úložiště](container-registry-best-practices.md#repository-namespaces) část [osvědčené postupy pro službu Azure Container Registry](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>Nahrání image do registru

Teď, když jste označili image s plně kvalifikovanou cestu do privátního registru, můžete ho odeslat do registru příkazem [docker push](https://docs.docker.com/engine/reference/commandline/push/):

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Stáhněte si image z registru

Použití [operace docker pull](https://docs.docker.com/engine/reference/commandline/pull/) příkaz a vyžádejte si image z registru:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Spuštění kontejneru Nginx

Použití [dockeru spustit](https://docs.docker.com/engine/reference/run/) příkaz ke spuštění image jste stáhli ze svého registru:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Přejděte do [ http://localhost:8080 ](http://localhost:8080) Chcete-li zobrazit spuštěný kontejner.

Chcete-li zastavit a odebrat kontejneru, stiskněte `Control` + `C`.

## <a name="remove-the-image-optional"></a>Odebrat obrázek (nepovinné)

Pokud image serveru Nginx už nepotřebujete, můžete ho místně pomocí odstranit [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) příkazu.

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Odebrání Image ze služby Azure container registry, můžete použít příkaz rozhraní příkazového řádku Azure [az acr úložiště odstranit](/cli/azure/acr/repository#az-acr-repository-delete). Například následující příkaz odstraní manifest odkazuje značku, všechna data přidružená vrstvy a všechny ostatní značky odkazující na manifest.

```azurecli
az acr repository delete --name myregistry --repository samples/nginx --tag latest --manifest
```

## <a name="next-steps"></a>Další postup

Teď, když znáte základy, jste připraveni začít používat svůj registr! Například nasaďte Image kontejneru z registru pro:

* [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)
