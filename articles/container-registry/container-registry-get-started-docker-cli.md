---
title: Obrázek Dockeru pro tlačení &
description: Nahrání a stažení imagí Dockeru do soukromého registru kontejnerů v Azure pomocí rozhraní příkazového řádku Dockeru
ms.topic: article
ms.date: 01/23/2019
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 6751a04c3c1bfe826334161704c20c1ba2e5a6d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456354"
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Nahrání první image do soukromého registru kontejnerů Dockeru pomocí rozhraní příkazového řádku Dockeru

Registr kontejnerů Azure uchovává a spravuje privátní image kontejnerů [Dockeru](https://hub.docker.com) podobným způsobem, jakým [Docker Hub](https://hub.docker.com/) uchovává veřejné image Dockeru. Rozhraní [příkazového řádku Dockeru](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) můžete použít pro [přihlášení](https://docs.docker.com/engine/reference/commandline/login/), [push](https://docs.docker.com/engine/reference/commandline/push/), [pull](https://docs.docker.com/engine/reference/commandline/pull/)a další operace v registru kontejneru.

V následujících krocích stáhnete oficiální [bitovou kopii Nginx](https://store.docker.com/images/nginx) z veřejného registru Docker Hub, označíte ji pro svůj soukromý registr kontejnerů Azure, předáte ji do registru a pak ji vytáhnete z registru.

## <a name="prerequisites"></a>Požadavky

* **Registr kontejnerů Azure** – Vytvořte registr kontejnerů ve svém předplatném Azure. Například použijte [portál Azure](container-registry-get-started-portal.md) nebo [Azure CLI](container-registry-get-started-azure-cli.md).
* **Docker CLI** - Musíte mít také Docker nainstalován místně. Docker nabízí balíčky pro snadnou konfiguraci Dockeru v libovolném systému [macOS][docker-mac], [Windows][docker-windows] nebo [Linux][docker-linux].

## <a name="log-in-to-a-registry"></a>Přihlášení k registru

Existuje [několik způsobů, jak ověřit](container-registry-authentication.md) do registru soukromého kontejneru. Doporučená metoda při práci v příkazovém řádku je s příkazem Azure CLI [az acr přihlášení](/cli/azure/acr?view=azure-cli-latest#az-acr-login). Chcete-li se například přihlásit do registru s názvem *myregistry*:

```azurecli
az acr login --name myregistry
```

Můžete se také přihlásit pomocí [přihlášení dockeru](https://docs.docker.com/engine/reference/commandline/login/). Například jste mohli [přiřadit instanční objekt](container-registry-authentication.md#service-principal) do registru pro scénář automatizace. Při spuštění následujícího příkazu, interaktivně zadejte id instančního objektu (uživatelské jméno) a heslo po zobrazení výzvy. Doporučené postupy pro správu přihlašovacích údajů najdete v tématu odkaz na příkaz [pro přihlášení do dockeru:](https://docs.docker.com/engine/reference/commandline/login/)

```
docker login myregistry.azurecr.io
```

Oba příkazy `Login Succeeded` se vrátí po dokončení.

> [!TIP]
> Při použití `docker login` a při označování obrázků pro odesílání do registru vždy zadejte úplný název registru (všechna malá písmena). V příkladech v tomto článku je plně kvalifikovaný název *myregistry.azurecr.io*.

## <a name="pull-the-official-nginx-image"></a>Vytáhněte oficiální obrázek Nginx

Nejprve přetáhněte veřejný obrázek Nginx do místního počítače.

```
docker pull nginx
```

## <a name="run-the-container-locally"></a>Místní spuštění kontejneru

Spusťte následující příkaz [spuštění dockeru](https://docs.docker.com/engine/reference/run/) a spusťte`-it`místní instanci kontejneru Nginx interaktivně ( ) na portu 8080. Argument `--rm` určuje, že kontejner by měl být odebrán, když jej zastavíte.

```
docker run -it --rm -p 8080:80 nginx
```

Přejděte `http://localhost:8080` na výchozí webovou stránku obsluhovanou nginxem v běžícím kontejneru. Měli byste vidět stránku podobnou následující:

![Server Nginx na místním počítači](./media/container-registry-get-started-docker-cli/nginx.png)

Vzhledem k tomu, `-it`že jste kontejner spustili interaktivně pomocí aplikace , můžete po přechodu na něj v prohlížeči zobrazit výstup serveru Nginx na příkazovém řádku.

Chcete-li nádobu zastavit `Control` + `C`a vyjmout, stiskněte klávesu .

## <a name="create-an-alias-of-the-image"></a>Vytvoření aliasu obrázku

Pomocí [značky docker](https://docs.docker.com/engine/reference/commandline/tag/) vytvořte alias bitové kopie s plně kvalifikovanou cestou k registru. V tomto příkladu se určí obor názvů `samples`, aby se zabránilo nepořádku v kořenovém adresáři registru.

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```

Další informace o označování pomocí oborů názvů najdete v části [Obory názvů úložiště](container-registry-best-practices.md#repository-namespaces) v části [Doporučené postupy pro Azure Container Registry](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>Nahrání image do vlastního registru

Nyní, když jste označili bitovou kopii plně kvalifikovanou cestou do soukromého registru, můžete ji zatlačit do registru pomocí [služby docker push](https://docs.docker.com/engine/reference/commandline/push/):

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Stažení image z vlastního registru

Pomocí příkazu [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) můžete obrázek z registru vytáhnout:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Spuštění kontejneru Nginx

Pomocí příkazu [docker run](https://docs.docker.com/engine/reference/run/) spusťte bitovou kopii, kterou jste vytáhli z registru:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Přejděte `http://localhost:8080` na zobrazení spuštěné kontejneru.

Chcete-li nádobu zastavit `Control` + `C`a vyjmout, stiskněte klávesu .

## <a name="remove-the-image-optional"></a>Odebrání obrázku (volitelné)

Pokud již nepotřebujete bitovou kopii Nginx, můžete ji odstranit místně pomocí příkazu [docker rmi.](https://docs.docker.com/engine/reference/commandline/rmi/)

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Chcete-li odebrat image z registru kontejnerů Azure, můžete použít příkaz Azure CLI [az acr repozitář odstranit](/cli/azure/acr/repository#az-acr-repository-delete). Například následující příkaz odstraní manifest odkazovaný `samples/nginx:latest` značkou, všechna jedinečná data vrstvy a všechny ostatní značky odkazující na manifest.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>Další kroky

Nyní, když znáte základy, jste připraveni začít používat registr! Například nasazení iobrazek kontejnerů z registru do:

* [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

Volitelně nainstalujte [rozšíření Docker u Visual Studia code](https://code.visualstudio.com/docs/azure/docker) a rozšíření účtu [Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) pro práci s registry kontejnerů Azure. Vyžádat a push image do registru kontejneru Azure nebo spustit úlohy ACR, to vše v rámci kódu Visual Studio.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
