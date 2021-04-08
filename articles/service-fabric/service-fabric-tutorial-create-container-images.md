---
title: Vytváření imagí kontejneru v Service Fabric v Azure
description: V tomto kurzu se naučíte vytvářet image kontejneru pro vícekontejnerovou aplikaci Service Fabric.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 31b5f870465bc1dff9d6ff7827a4efed084bcf62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92739063"
---
# <a name="tutorial-create-container-images-on-a-linux-service-fabric-cluster"></a>Kurz: Vytváření imagí kontejneru v clusteru Service Fabric s Linuxem

Tento kurz je součástí série kurzů, která demonstruje používání kontejnerů v clusteru Service Fabric s Linuxem. V tomto kurzu je pro použití s prostředkem Service Fabric připravena vícekontejnerová aplikace. V následujících kurzech jsou tyto image použity jako součást aplikace Service Fabric. Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Klonovat zdroj aplikace z GitHubu
> * Vytvořit image kontejneru ze zdroje aplikace
> * Nasadit instanci služby Azure Container Registry (ACR)
> * Označit image kontejneru pro službu ACR
> * Odeslat image do služby ACR

V této sérii kurzů se naučíte:

> [!div class="checklist"]
> * Vytvářet image kontejneru pro Service Fabric
> * [Sestavit a spustit aplikaci Service Fabric s kontejnery](service-fabric-tutorial-package-containers.md)
> * [Jak se zpracovává převzetí služeb při selhání a škálování v prostředku Service Fabric](service-fabric-tutorial-containers-failover.md)

## <a name="prerequisites"></a>Požadavky

* Vývojové prostředí Linux nastavené pro Service Fabric. Při nastavování prostředí Linux postupujte podle pokynů [zde](service-fabric-get-started-linux.md).
* Tento kurz vyžaduje použití Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli).
* Navíc se vyžaduje, abyste měli k dispozici předplatné Azure. Další informace o bezplatné zkušební verzi najdete [zde](https://azure.microsoft.com/free/).

## <a name="get-application-code"></a>Získání kódu aplikace

Ukázkovou aplikací používanou v tomto kurzu je hlasovací aplikace. Aplikace se skládá z front-end webové součásti a back-end instance Redis. Součásti jsou sbaleny do imagí kontejneru.

Pomocí gitu si stáhněte kopii aplikace do vývojového prostředí.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/
```

Řešení obsahuje dvě složky a soubor docker-compose.yml. Složka azure-vote obsahuje front-end službu Python spolu se souborem Dockerfile sloužícím k sestavení image. Adresář Voting obsahuje balíček aplikace Service Fabric, který je nasazený do clusteru. Tyto adresáře obsahují prostředky potřebné pro tento kurz.

## <a name="create-container-images"></a>Vytváření imagí kontejneru

V adresáři **azure-vote** vytvořte image pro front-end webovou komponentu spuštěním následujícího příkazu. Tento příkaz používá k sestavení image soubor Dockerfile v tomto adresáři.

```bash
docker build -t azure-vote-front .
```
> [!Note]
> Pokud se zobrazí oznámení o odepření oprávnění, postupujte podle [této](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user) dokumentace, která popisuje práci s Dockerem bez příkazu sudo.

Zpracování tohoto příkazu může chvíli trvat, protože je třeba načíst všechny potřebné závislosti z centra Dockeru. Po dokončení můžete vytvořené image zobrazit pomocí příkazu [docker images](https://docs.docker.com/engine/reference/commandline/images/).

```bash
docker images
```

Všimněte si, že byly staženy nebo vytvořeny dvě image. Image *azure-vote-front* obsahuje aplikaci. Je odvozená z image *python* z centra Dockeru.

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB

```

## <a name="deploy-azure-container-registry"></a>Nasazení služby Azure Container Registry

Nejprve spuštěním příkazu **AZ Login** Přihlaste se ke svému účtu Azure.

```azurecli
az login
```

Pak pomocí příkazu **az account** zvolte předplatné pro vytvoření registru kontejneru Azure. Místo parametru <subscription_id> je třeba zadat ID předplatného Azure.

```azurecli
az account set --subscription <subscription_id>
```

Pokud chcete nasadit službu Azure Container Registry, nejprve potřebujete skupinu prostředků. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí příkazu **az group create**. V tomto příkladu se vytvoří skupina prostředků s názvem *myResourceGroup* v oblasti *westus*.

```azurecli
az group create --name <myResourceGroup> --location westus
```

Pomocí příkazu **AZ ACR Create** vytvořte službu Azure Container Registry. Nahraďte \<acrName> názvem registru kontejneru, který chcete vytvořit v rámci vašeho předplatného. Tento název smí obsahovat jen alfanumerické znaky a musí být jedinečný.

```azurecli
az acr create --resource-group <myResourceGroup> --name <acrName> --sku Basic --admin-enabled true
```

V celé zbývající části tohoto kurzu používáme položku „acrName“ jako zástupný symbol pro název registru kontejneru, který jste zvolili. Poznamenejte si tuto hodnotu.

## <a name="sign-in-to-your-container-registry"></a>Přihlaste se ke svému registru kontejneru.

Před nahráním imagí do instance ACR se přihlaste. Dokončete operaci pomocí příkazu **az acr login**. Uveďte jedinečný název zadaný pro registr kontejneru při jeho vytvoření.

```azurecli
az acr login --name <acrName>
```

Příkaz po dokončení vrátí zprávu Login Succeeded (Přihlášení proběhlo úspěšně).

## <a name="tag-container-images"></a>Označování imagí kontejneru

Každá image kontejneru musí být označena názvem loginServer registru. Tato značka se používá pro směrování při nahrávání imagí kontejneru do registru imagí.

Seznam aktuálních imagí můžete zobrazit pomocí příkazu [docker images](https://docs.docker.com/engine/reference/commandline/images/).

```bash
docker images
```

Výstup:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB
```

Pokud chcete zjistit název loginServer, spusťte následující příkaz:

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Výstupem je tabulka s následujícími výsledky. Výsledek se použije pro označení vaší image **azure-vote-front** před jejím nahráním do registru kontejneru v dalším kroku.

```output
Result
------------------
<acrName>.azurecr.io
```

Nyní, označte image *azure-vote-front* názvem loginServer registru kontejneru. Na konec názvu image také přidejte řetězec `:v1`. Tato značka označuje verzi image.

```bash
docker tag azure-vote-front <acrName>.azurecr.io/azure-vote-front:v1
```

Po označení operaci ověřte spuštěním příkazu docker images.

Výstup:

```output
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                       latest              052c549a75bf        23 minutes ago      708MB
<acrName>.azurecr.io/azure-vote-front   v1                  052c549a75bf       23 minutes ago      708MB
tiangolo/uwsgi-nginx-flask             python3.6           590e17342131        5 days ago          707MB

```

## <a name="push-images-to-registry"></a>Nahrávání imagí do registru

Nahrajte image *azure-voice-front* do registru. 

Podle následujícího příkladu nahraďte název loginServer služby ACR názvem loginServer ze svého prostředí.

```bash
docker push <acrName>.azurecr.io/azure-vote-front:v1
```

Dokončení příkazů docker push trvá několik minut.

## <a name="list-images-in-registry"></a>Výpis imagí v registru

Pokud chcete vrátit seznam imagí, které byly nahrány do vašeho registru kontejneru Azure, použijte příkaz [az acr repository list](/cli/azure/acr/repository). Aktualizujte příkaz s použitím názvu instance služby ACR.

```azurecli
az acr repository list --name <acrName> --output table
```

Výstup:

```output
Result
----------------
azure-vote-front
```

Na konci kurzu byla image kontejneru uložena v privátní instanci služby Azure Container Registry. Tato image bude nasazena ze služby ACR do clusteru Service Fabric v následných kurzech.

## <a name="next-steps"></a>Další kroky

V tomto kurzu byla aplikace získána z GitHubu a image kontejneru se vytvořily a vložily do registru. Dokončili jste následující kroky:

> [!div class="checklist"]
> * Klonovat zdroj aplikace z GitHubu
> * Vytvořit image kontejneru ze zdroje aplikace
> * Nasadit instanci služby Azure Container Registry (ACR)
> * Označit image kontejneru pro službu ACR
> * Odeslat image do služby ACR

Po přechodu k dalšímu kurzu se dozvíte o balení kontejnerů do aplikace Service Fabric pomocí Yeomanu.

> [!div class="nextstepaction"]
> [Zabalení a nasazení kontejnerů jako aplikace Service Fabric](service-fabric-tutorial-package-containers.md)
