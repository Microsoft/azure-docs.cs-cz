---
title: Nahrání image Dockeru do privátní registr kontejnerů Azure
description: Nahrání a stažení imagí Dockeru do soukromého registru kontejnerů v Azure pomocí rozhraní příkazového řádku Dockeru
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 11/29/2017
ms.author: stevelas
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 3cbc9f30f180913fefa79f24612e50db75f5c9cd
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53260578"
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Nahrání první image do soukromého registru kontejnerů Dockeru pomocí rozhraní příkazového řádku Dockeru

Registr kontejnerů Azure uchovává a spravuje privátní image kontejnerů [Dockeru](http://hub.docker.com) podobným způsobem, jakým [Docker Hub](https://hub.docker.com/) uchovává veřejné image Dockeru. Můžete použít [rozhraní příkazového řádku Dockeru](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) pro [přihlášení](https://docs.docker.com/engine/reference/commandline/login/), [nabízených](https://docs.docker.com/engine/reference/commandline/push/), [o přijetí změn](https://docs.docker.com/engine/reference/commandline/pull/)a další operace na kontejnerech registru.

V následujících krocích stažení oficiální [image serveru Nginx](https://store.docker.com/images/nginx) z veřejného registru Docker Hub, označte ji pro váš privátní registr kontejnerů Azure, nahrajte ho do vašeho registru a pak přetahování z registru.

## <a name="prerequisites"></a>Požadavky

* **Registr kontejnerů Azure** – Vytvořte registr kontejnerů ve svém předplatném Azure. Například použít [webu Azure portal](container-registry-get-started-portal.md) nebo [rozhraní příkazového řádku Azure](container-registry-get-started-azure-cli.md).
* **Rozhraní příkazového řádku dockeru** – Chcete-li nastavit místní počítač jako hostitele Docker a přístup k příkazům rozhraní příkazového řádku Dockeru, nainstalujte [Docker](https://docs.docker.com/engine/installation/).

## <a name="log-in-to-a-registry"></a>Přihlášení k registru

Existují [několik způsobů, jak ověřit](container-registry-authentication.md) pro váš privátní registr kontejnerů. Je doporučeným způsobem při práci v příkazovém řádku pomocí příkazu Azure CLI [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login). Například pro přihlášení k registru s názvem *myregistry*:

```azurecli
az acr login --name myregistry
```

Také se můžete přihlásit pomocí [docker login](https://docs.docker.com/engine/reference/commandline/login/). Následující příklad předá ID a heslo [instančního objektu](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory. Například můžete mít [přiřadit instanční objekt služby](container-registry-authentication.md#service-principal) do vašeho registru pro účely scénáře automatizace.

```Bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Vrátí oba příkazy `Login Succeeded` po dokončení. Pokud používáte `docker login`, můžete si také prohlédnout upozornění zabezpečení doporučující použití `--password-stdin` parametru. I když je jeho použití nad rámec tohoto článku, doporučujeme řídit se osvědčeným postupem. Další informace najdete v tématu [docker login](https://docs.docker.com/engine/reference/commandline/login/) referenčních příkazu.

> [!TIP]
> Vždy plně kvalifikovaný název registru (malými písmeny) zadejte při použití `docker login` a kdy označit Image pro doručením (push) do svého registru. V příkladech v tomto článku, je plně kvalifikovaný název *myregistry.azurecr.io*.

## <a name="pull-the-official-nginx-image"></a>Stáhněte si oficiální image serveru Nginx

Nejprve si stáhněte veřejnou image serveru Nginx do místního počítače.

```Bash
docker pull nginx
```

## <a name="run-the-container-locally"></a>Místní spuštění kontejneru

Spusťte následující [dockeru spustit](https://docs.docker.com/engine/reference/run/) příkaz spustit místní instanci kontejneru Nginx interaktivně (`-it`) na portu 8080. `--rm` Argument určuje, že má být odebrána kontejneru při jeho zastavení.

```Bash
docker run -it --rm -p 8080:80 nginx
```

Přejděte do [ http://localhost:8080 ](http://localhost:8080) Chcete-li zobrazit výchozí webová stránka obsluhuje Nginx v spuštěný kontejner. Zobrazí se stránka podobná následujícímu:

![Server Nginx na místním počítači](./media/container-registry-get-started-docker-cli/nginx.png)

Protože jste spustili kontejner interaktivně s `-it`, můžete zobrazit výstup serveru Nginx na příkazovém řádku po přechodu na něj v prohlížeči.

Chcete-li zastavit a odebrat kontejneru, stiskněte `Control` + `C`.

## <a name="create-an-alias-of-the-image"></a>Vytvoření aliasu Image

Použití [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) vytvoření aliasu Image s plně kvalifikovanou cestou k vašemu registru. V tomto příkladu se určí obor názvů `samples`, aby se zabránilo nepořádku v kořenovém adresáři registru.

```Bash
docker tag nginx myregistry.azurecr.io/samples/nginx
```

Další informace o značkách s obory názvů najdete v tématu [obory názvů úložiště](container-registry-best-practices.md#repository-namespaces) část [osvědčené postupy pro službu Azure Container Registry](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>Nahrání image do registru

Teď, když jste označili image s plně kvalifikovanou cestu do privátního registru, můžete ho odeslat do registru příkazem [docker push](https://docs.docker.com/engine/reference/commandline/push/):

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Stáhněte si image z registru

Použití [operace docker pull](https://docs.docker.com/engine/reference/commandline/pull/) příkaz a vyžádejte si image z registru:

```Bash
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Spuštění kontejneru Nginx

Použití [dockeru spustit](https://docs.docker.com/engine/reference/run/) příkaz ke spuštění image jste stáhli ze svého registru:

```Bash
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Přejděte do [ http://localhost:8080 ](http://localhost:8080) Chcete-li zobrazit spuštěný kontejner.

Chcete-li zastavit a odebrat kontejneru, stiskněte `Control` + `C`.

## <a name="remove-the-image-optional"></a>Odebrat obrázek (nepovinné)

Pokud image serveru Nginx už nepotřebujete, můžete ho místně pomocí odstranit [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) příkazu.

```Bash
docker rmi myregistry.azurecr.io/samples/nginx
```

Odebrání Image ze služby Azure container registry, můžete použít příkaz rozhraní příkazového řádku Azure [az acr úložiště odstranit](/cli/azure/acr/repository#az-acr-repository-delete). Například následující příkaz odstraní manifest odkazuje značku, všechna data přidružená vrstvy a všechny ostatní značky odkazující na manifest.

```azurecli
az acr repository delete --name myregistry --repository samples/nginx --tag latest --manifest
```

## <a name="next-steps"></a>Další postup

Teď, když znáte základy, jste připraveni začít používat svůj registr! Nasazení Image kontejneru z registru pro:

* [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)
