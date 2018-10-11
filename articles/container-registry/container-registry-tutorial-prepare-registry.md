---
title: Kurz služby Azure Container Registry – Příprava geograficky replikovaného registru kontejnerů Azure
description: Vytvořte registr kontejnerů Azure, nakonfigurujte geografickou replikaci, připravte image Dockeru a nasaďte ji do registru. První část třídílné série.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: tutorial
ms.date: 04/30/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 9d1371dc02f37889b15b3448d82c62d76472f847
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854299"
---
# <a name="tutorial-prepare-a-geo-replicated-azure-container-registry"></a>Kurz: Příprava geograficky replikovaného registru kontejnerů Azure

Registr kontejnerů Azure je privátní registr Dockeru nasazený v Azure, který můžete uchovávat v síti blízké vašim nasazením. V této sérií tří kurzů zjistíte, jak s využitím geografické replikace nasadit webovou aplikaci ASP.NET Core spuštěnou v kontejneru Linuxu do dvou instancí služby [Web App for Containers](../app-service/containers/index.yml). Uvidíte, jak Azure automaticky nasadí image do obou instancí webové aplikace z nejbližšího geograficky replikovaného úložiště.

V tomto kurzu, který je první částí třídílné série, se naučíte:

> [!div class="checklist"]
> * Vytvoření geograficky replikovaného registru kontejnerů Azure
> * Klonování zdrojového kódu aplikace z GitHubu
> * Sestavení image kontejneru Dockeru ze zdroje aplikace
> * Nasdílení image kontejneru do registru

V dalších kurzech nasadíte kontejner ze svého privátního registru do webové aplikace spuštěné ve dvou oblastech Azure. Pak aktualizujete kód aplikace a pomocí jediného příkazu `docker push` pro váš registr aktualizujete obě instance webové aplikace.

## <a name="before-you-begin"></a>Než začnete

Tento kurz vyžaduje místní instalaci Azure CLI (verze 2.0.31 nebo novější). Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

Měli byste znát klíčové koncepty Dockeru, jako jsou kontejnery, image kontejnerů a základní příkazy Docker CLI. Základní informace o kontejnerech najdete v článku [Get started with Docker]( https://docs.docker.com/get-started/) (Začínáme s Dockerem).

K dokončení tohoto kurzu potřebujete místní instalaci Dockeru. Docker poskytuje pokyny k instalaci pro systémy [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) a [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell neobsahuje součásti Dockeru nutné pro dokončení všech kroků v tomto kurzu. Proto doporučujeme místní instalaci Azure CLI a vývojového prostředí pro Docker.

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

Přihlaste se k webu [Azure Portal](http://portal.azure.com).

Vyberte **Vytvořit prostředek** > **Kontejnery** > **Azure Container Registry**.

![Vytvoření registru kontejnerů na webu Azure Portal][tut-portal-01]

Nakonfiguruje nový registr pomocí následujících nastavení:

* **Název registru:** Vytvořte název registru, který je globálně jedinečný v rámci Azure a obsahuje 5 až 50 alfanumerických znaků.
* **Skupina prostředků:** **Vytvořit novou** > `myResourceGroup`
* **Umístění:** `West US`
* **Uživatel s rolí správce:** `Enable` (vyžadováno službou Web App for Containers ke stahování imagí)
* **Skladová položka:** `Premium` (vyžadováno pro geografickou replikaci)

Vyberte **Vytvořit** a nasaďte instanci služby ACR.

![Vytvoření registru kontejnerů na webu Azure Portal][tut-portal-02]

V celé zbývající části tohoto kurzu používáme `<acrName>` jako zástupný symbol pro **Název registru** kontejnerů, který jste zvolili.

> [!TIP]
> Vzhledem k tomu, že registry kontejnerů Azure jsou obvykle dlouhodobé prostředky, které se používají na více hostitelích kontejnerů, doporučujeme vytvořit registr ve vlastní skupině prostředků. Když budete konfigurovat geograficky replikované registry a webhooky, umístí se tyto další prostředky do stejné skupiny prostředků.
>

## <a name="configure-geo-replication"></a>Konfigurace geografické replikace

Když teď máte registr úrovně Premium, můžete nakonfigurovat geografickou replikaci. Vaše webová aplikace, kterou v dalším kurzu nakonfigurujete pro spouštění ve dvou oblastech, si pak bude moci stáhnout image kontejnerů z nejbližšího registru.

Na webu Azure Portal přejděte do svého nového registru kontejnerů a v části **SLUŽBY** vyberte **Replikace**.

![Replikace v uživatelském rozhraní registru kontejnerů na webu Azure Portal][tut-portal-03]

Zobrazí se mapa se zelenými šestiúhelníky, které představují oblasti Azure dostupné pro geografickou replikaci:

 ![Mapa oblastí na webu Azure Portal][tut-map-01]

Replikujte svůj registr do oblasti USA – východ tak, že vyberete příslušný zelený šestiúhelník a pak v části **Vytvořit replikaci** vyberete **Vytvořit**:

 ![Vytvoření replikace v uživatelském rozhraní na webu Azure Portal][tut-portal-04]

Po dokončení replikace se na portálu u obou oblastí zobrazí stav *Připraveno*. Pomocí tlačítka **Aktualizovat** aktualizujte stav replikace. Vytvoření a synchronizace replik může trvat několik minut.

![Stav replikace v uživatelském rozhraní na webu Azure Portal][tut-portal-05]

## <a name="container-registry-login"></a>Přihlášení k registru kontejneru

Když teď máte nakonfigurovanou geografickou replikaci, sestavte image kontejneru a nasdílejte ji do svého registru. Před nahráním imagí do instance služby ACR se k ní musíte přihlásit.

Pomocí příkazu [az acr login](https://docs.microsoft.com/cli/azure/acr#az-acr-login) se ověřte a uložte do mezipaměti přihlašovací údaje pro váš registr. `<acrName>` nahraďte názvem registru, který jste vytvořili dříve.

```azurecli
az acr login --name <acrName>
```

Příkaz po dokončení vrátí zprávu `Login Succeeded` (Přihlášení bylo úspěšné).

## <a name="get-application-code"></a>Získání kódu aplikace

Ukázka v tomto kurzu zahrnuje malou webovou aplikaci vytvořenou v [ASP.NET Core][aspnet-core]. Aplikace slouží jako stránka HTML zobrazující oblast, ze které služba Azure Container Registry nasadila image.

![Ukázková aplikace zobrazená v prohlížeči][tut-app-01]

Pomocí Gitu stáhněte ukázku do místního adresáře a pomocí příkazu `cd` do tohoto adresáře přejděte:

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

Pokud nemáte nainstalovaný `git`, můžete si [stáhnout archiv ZIP][acr-helloworld-zip] přímo z GitHubu.

## <a name="update-dockerfile"></a>Aktualizace souboru Dockerfile

Soubor Dockerfile, který je součástí ukázky, ukazuje postup sestavení kontejneru. Spustí se z oficiální image [aspnetcore][dockerhub-aspnetcore], zkopíruje soubory aplikace do kontejneru, nainstaluje závislosti, zkompiluje výstup pomocí oficiální image [aspnetcore-build][dockerhub-aspnetcore-build] a nakonec sestaví optimalizovanou image aspnetcore.

Soubor [Dockerfile][dockerfile] se v naklonovaném zdroji nachází v umístění `./AcrHelloworld/Dockerfile`.

```dockerfile
FROM microsoft/aspnetcore:2.0 AS base
# Update <acrName> with the name of your registry
# Example: uniqueregistryname.azurecr.io
ENV DOCKER_REGISTRY <acrName>.azurecr.io
WORKDIR /app
EXPOSE 80

FROM microsoft/aspnetcore-build:2.0 AS build
WORKDIR /src
COPY *.sln ./
COPY AcrHelloworld/AcrHelloworld.csproj AcrHelloworld/
RUN dotnet restore
COPY . .
WORKDIR /src/AcrHelloworld
RUN dotnet build -c Release -o /app

FROM build AS publish
RUN dotnet publish -c Release -o /app

FROM base AS production
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "AcrHelloworld.dll"]
```

Aplikace v imagi *acr-helloworld* se pokouší určit oblast, ze které se nasadil její kontejner, dotazováním serveru DNS na informace o přihlašovacím serveru registru. Plně kvalifikovaný název domény přihlašovacího serveru vašeho registru musíte zadat do proměnné prostředí `DOCKER_REGISTRY` v souboru Dockerfile.

Nejprve získejte přihlašovací server registru pomocí příkazu `az acr show`. Nahraďte `<acrName>` názvem registru, který jste vytvořili v předchozích krocích.

```azurecli
az acr show --name <acrName> --query "{acrLoginServer:loginServer}" --output table
```

Výstup:

```bash
AcrLoginServer
-----------------------------
uniqueregistryname.azurecr.io
```

Pak aktualizujte řádek `ENV DOCKER_REGISTRY` s použitím plně kvalifikovaného názvu domény přihlašovacího serveru vašeho registru. Tento příklad odráží název registru, *uniqueregistryname*:

```dockerfile
ENV DOCKER_REGISTRY uniqueregistryname.azurecr.io
```

## <a name="build-container-image"></a>Sestavení image kontejneru

Když jste aktualizovali v souboru Dockerfile plně kvalifikovaný název domény přihlašovacího serveru vašeho registru, můžete teď pomocí příkazu `docker build` vytvořit image kontejneru. Spuštěním následujícího příkazu, ve kterém opět nahradíte `<acrName>` názvem vašeho registru, sestavte image a označte ji adresou URL vašeho privátního registru:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

Během sestavování image Dockeru se zobrazí několik řádků výstupu (tady je zkrácený):

```bash
Sending build context to Docker daemon  523.8kB
Step 1/18 : FROM microsoft/aspnetcore:2.0 AS base
2.0: Pulling from microsoft/aspnetcore
3e17c6eae66c: Pulling fs layer

[...]

Step 18/18 : ENTRYPOINT dotnet AcrHelloworld.dll
 ---> Running in 6906d98c47a1
 ---> c9ca1763cfb1
Removing intermediate container 6906d98c47a1
Successfully built c9ca1763cfb1
Successfully tagged uniqueregistryname.azurecr.io/acr-helloworld:v1
```

Pomocí `docker images` zobrazíte sestavené a označené image:

```console
$ docker images
REPOSITORY                                      TAG    IMAGE ID        CREATED               SIZE
uniqueregistryname.azurecr.io/acr-helloworld    v1     01ac48d5c8cf    About a minute ago    284MB
[...]
```

## <a name="push-image-to-azure-container-registry"></a>Nahrání image do služby Azure Container Registry

Potom pomocí příkazu `docker push` nasdílejte image *acr-helloworld* do svého registru. Nahraďte `<acrName>` názvem vašeho registru.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Vzhledem k tomu, že jste pro registr nakonfigurovali geografickou replikaci, vaše image se pomocí tohoto jediného příkazu `docker push` automaticky replikuje do oblasti *USA – západ* i *USA – východ*.

```console
$ docker push uniqueregistryname.azurecr.io/acr-helloworld:v1
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
cd54739c444b: Pushed
d6803756744a: Pushed
b7b1f3a15779: Pushed
a89567dff12d: Pushed
59c7b561ff56: Pushed
9a2f9413d9e4: Pushed
a75caa09eb1f: Pushed
v1: digest: sha256:0799014f91384bda5b87591170b1242bcd719f07a03d1f9a1ddbae72b3543970 size: 1792
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili privátní a geograficky replikovaný registr kontejnerů, sestavili jste image kontejneru a pak jste image nasdíleli do svého registru.

Přejděte k dalšímu kurzu, kde nasadíte kontejner do více instancí služby Web App for Containers s využitím geografické replikace k místní obsluze imagí.

> [!div class="nextstepaction"]
> [Nasazení webové aplikace ze služby Azure Container Registry](container-registry-tutorial-deploy-app.md)

<!-- IMAGES -->
[tut-portal-01]: ./media/container-registry-tutorial-prepare-registry/tut-portal-01.png
[tut-portal-02]: ./media/container-registry-tutorial-prepare-registry/tut-portal-02.png
[tut-portal-03]: ./media/container-registry-tutorial-prepare-registry/tut-portal-03.png
[tut-portal-04]: ./media/container-registry-tutorial-prepare-registry/tut-portal-04.png
[tut-portal-05]: ./media/container-registry-tutorial-prepare-registry/tut-portal-05.png
[tut-app-01]: ./media/container-registry-tutorial-prepare-registry/tut-app-01.png
[tut-map-01]: ./media/container-registry-tutorial-prepare-registry/tut-map-01.png

<!-- LINKS - External -->
[acr-helloworld-zip]: https://github.com/Azure-Samples/acr-helloworld/archive/master.zip
[aspnet-core]: http://dot.net
[dockerhub-aspnetcore]: https://hub.docker.com/r/microsoft/aspnetcore/
[dockerhub-aspnetcore-build]: https://store.docker.com/community/images/microsoft/aspnetcore-build
[dockerfile]: https://github.com/Azure-Samples/acr-helloworld/blob/master/AcrHelloworld/Dockerfile