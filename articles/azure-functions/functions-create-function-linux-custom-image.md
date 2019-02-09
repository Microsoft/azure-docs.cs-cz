---
title: Vytvoření funkce v Linuxu pomocí vlastní image (verze Preview) | Microsoft Docs
description: Naučte se vytvářet funkce služby Azure Functions běžící na vlastní imagi Linuxu.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 10/19/2018
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: 2c80f988583571f3394a29747a6f452951cea878
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978030"
---
# <a name="create-a-function-on-linux-using-a-custom-image-preview"></a>Vytvoření funkce v Linuxu pomocí vlastní image (verze Preview)

Služba Azure Functions umožňuje hostovat funkce v Linuxu ve vašem vlastním kontejneru. Můžete také [hostovat ve výchozím kontejneru služby Azure App Service](functions-create-first-azure-function-azure-cli-linux.md). Tato funkce je aktuálně ve verzi Preview a vyžaduje [modul runtime Functions 2.0](functions-versions.md).

V tomto kurzu se dozvíte, jak do Azure nasadit funkce jako vlastní image Dockeru. Tento vzor je užitečný v případě, že potřebujete přizpůsobit vestavěnou image kontejneru služby App Service. Když vaše funkce vyžadují určitou jazykovou verzi nebo konkrétní závislost nebo konfiguraci, kterou vestavěná image neposkytuje, můžete chtít použít vlastní image.

V tomto kurzu se dozvíte, jak pomocí Azure Functions Core Tools vytvořit funkci ve vlastní imagi Linuxu. Tuto image publikujete do aplikace funkcí v Azure, která se vytvořila pomocí Azure CLI.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit aplikaci funkcí a soubor Dockerfile pomocí Core Tools
> * Sestavit vlastní image pomocí Dockeru
> * Publikovat vlastní image do registru kontejneru
> * Vytvořit účet služby Azure Storage
> * Vytvořit plán služby App Service pro Linux
> * Nasadit aplikaci Function App z Docker Hubu
> * Přidat do aplikace Function App nastavení aplikace

Následující kroky se podporují na počítačích se systémem Mac, Windows a Linux.  

## <a name="prerequisites"></a>Požadavky

Před spuštěním této ukázky musíte mít následující:

* Nainstalujte [Azure Core Tools verze 2.x](functions-run-local.md#v2).

* Nainstalujte [rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli). Tento článek vyžaduje použití Azure CLI verze 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`.  
Můžete také použít [Azure Cloud Shell](https://shell.azure.com/bash).

* Aktivní předplatné Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>Vytvoření projektu funkce místní aplikace

Spuštěním následujícího příkazu z příkazového řádku vytvoříte projekt aplikace funkcí ve složce `MyFunctionProj` aktuálního místního adresáře.

```bash
func init MyFunctionProj --docker
```

Pokud zahrnete možnost `--docker`, pro projekt se vygeneruje soubor Dockerfile. Tento soubor slouží k vytvoření vlastního kontejneru,ve kterém se projekt spustí. Použitá základní image závisí na zvoleném jazyce modulu runtime pracovního procesu.  

Po zobrazení výzvy zvolte modul runtime pracovního procesu z následujících jazyků:

* `dotnet`: vytvoří projekt knihovny třídy .NET (.csproj).
* `node`: vytvoří projekt jazyka JavaScript.
* `python`: vytvoří projekt Python.

Při spuštění příkazu se zobrazí něco jako následující výstup:

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing Dockerfile
```

Pomocí následujícího příkazu přejděte do složky nového projektu `MyFunctionProj`.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-the-image-from-the-docker-file"></a>Sestavení image ze souboru Dockeru

Prohlédněte si soubor _Dockerfile_ v kořenové složce projektu. Tento soubor popisuje prostředí potřebné pro spuštění aplikace Function App v Linuxu. Následující příklad souboru Dockerfile vytvoří kontejner, ve kterém se spouští aplikace funkcí v modulu runtime pracovního procesu JavaScriptu (Node.js): 

```Dockerfile
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> Při hostování image v privátním registru kontejneru byste měli pomocí proměnných **ENV** v souboru Dockerfile přidat do aplikace Function App nastavení připojení. Jelikož tento kurz nemůže zaručit, že použijete privátní registr, nastavení připojení se v rámci osvědčeného postupu v oblasti zabezpečení [přidávají po nasazení pomocí rozhraní Azure CLI](#configure-the-function-app).

### <a name="run-the-build-command"></a>Spuštění příkazu `build`
V kořenové složce spusťte příkaz [docker build](https://docs.docker.com/engine/reference/commandline/build/) a zadejte název `mydockerimage` a značku `v1.0.0`. Položku `<docker-id>` nahraďte ID vašeho účtu Docker Hubu. Tento příkaz sestaví image Dockeru pro kontejner.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Po spuštění příkazu se zobrazí podobný výstup jako v následujícím příkladu, který ukazuje výstup pro modul runtime pracovního procesu JavaScriptu:

```bash
Sending build context to Docker daemon  17.41kB
Step 1/3 : FROM mcr.microsoft.com/azure-functions/node:2.0
2.0: Pulling from azure-functions/node
802b00ed6f79: Pull complete
44580ea7a636: Pull complete
73eebe8d57f9: Pull complete
3d82a67477c2: Pull complete
8bd51cd50290: Pull complete
7bd755353966: Pull complete
Digest: sha256:480e969821e9befe7c61dda353f63298f2c4b109e13032df5518e92540ea1d08
Status: Downloaded newer image for mcr.microsoft.com/azure-functions/node:2.0
 ---> 7c71671b838f
Step 2/3 : ENV AzureWebJobsScriptRoot=/home/site/wwwroot
 ---> Running in ed1e5809f0b7
Removing intermediate container ed1e5809f0b7
 ---> 39d9c341368a
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5e196215935a
Successfully built 5e196215935a
Successfully tagged <docker-id>/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>Testování image v místním prostředí
Ověřte si funkčnost sestavené image tak, že image Dockeru spustíte v místním kontejneru. Zadejte příkaz [docker run](https://docs.docker.com/engine/reference/commandline/run/) a předejte mu název a značku image. Nezapomeňte zadat port pomocí argumentu `-p`.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Když už v místním kontejneru Dockeru běží vlastní image, ověřte správnou funkčnost aplikace funkcí a kontejneru tak, že přejdete na adresu <http://localhost:8080>.

![Místní testování aplikace Function App](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

Volitelně můžete funkci otestovat znovu, tentokrát v místním kontejneru s použitím následující adresy URL:

`http://localhost:8080/api/myhttptrigger?name=<yourname>`

Po ověření aplikace Function App v kontejneru můžete aplikaci zastavit. Teď můžete vlastní image odeslat do svého účtu Docker Hubu.

## <a name="push-the-custom-image-to-docker-hub"></a>Odeslání vlastní image do Docker Hubu

Registr je aplikace, která hostuje image a poskytuje image služeb a služby kontejneru. Pokud chcete svou image sdílet, musíte ji odeslat do registru. Docker Hub je registr pro image Dockeru, který umožňuje hostovat vlastní veřejná nebo privátní úložiště.

Než budete moct odeslat image, musíte se přihlásit k Docker Hubu pomocí příkazu [docker login](https://docs.docker.com/engine/reference/commandline/login/). Položku `<docker-id>` nahraďte názvem svého účtu a po zobrazení výzvy zadejte do konzoly své heslo. Další možnosti hesla do Docker Hubu najdete v [dokumentaci k příkazu docker login](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id>
```

Zpráva „login succeeded“ (Přihlášení proběhlo úspěšně) potvrzuje, že jste přihlášení. Po přihlášení odešlete image do Docker Hubu pomocí příkazu [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Prozkoumejte výstup příkazu a ověřte si, jestli odeslání proběhlo úspěšně.

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from <docker-id>/mydockerimage
e7796c35add2: Mounted from <docker-id>/mydockerimage
ae9a05b85848: Mounted from <docker-id>/mydockerimage
45c86e20670d: Mounted from <docker-id>/mydockerimage
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 1796
```

Teď můžete image použít jako zdroj nasazení nové aplikace Function App v Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Vytvoření plánu služby App Service pro Linux

Plány Consumption v současné době nepodporují hostování služby Functions v Linuxu. Aplikace typu kontejner pro Linux musíte hostovat v plánu služby App Service pro Linux. Další informace o hostování najdete v [porovnání plánů hostování služby Azure Functions](functions-scale.md).

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-and-deploy-the-custom-image"></a>Vytvoření a nasazení vlastní image

Aplikace Function App hostuje provádění vašich funkcí. Aplikaci Function App vytvoříte z image z Docker Hubu pomocí příkazu [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

V následujícím příkazu nahraďte zástupný symbol `<app_name>` jedinečným názvem vaší aplikace funkcí a `<storage_name>` názvem účtu úložiště. Jako výchozí doména DNS pro příslušnou aplikaci Function App se použije `<app_name>`, a proto musí být název mezi všemi aplikacemi v Azure jedinečný. Stejně jako předtím má váš účet Dockeru název `<docker-id>`.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

Po vytvoření aplikace Function App se v Azure CLI zobrazí podobné informace jako v následujícím příkladu:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

Parametr _deployment-container-image-name_ určuje image hostovanou v Docker Hubu, která se má použít k vytvoření aplikace Function App.

## <a name="configure-the-function-app"></a>Konfigurace aplikace Function App

Funkce potřebuje k připojení k výchozímu účtu úložiště připojovací řetězec. Při publikování vlastní image do účtu privátního kontejneru byste měli místo toho určit tato nastavení aplikace jako proměnné prostředí v souboru Dockerfile pomocí [instrukce ENV](https://docs.docker.com/engine/reference/builder/#env) nebo něčeho podobného.

V tomto případě je `<storage_account>` název účtu úložiště, který jste vytvořili. Připojovací řetězec zobrazíte pomocí příkazu [az storage account show-connection-string](/cli/azure/storage/account). Pomocí příkazu [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) přidejte tato nastavení aplikace do aplikace Function App.

```azurecli-interactive
$storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

> [!NOTE]
> Pokud je váš kontejner privátní, je třeba nastavit také následující nastavení aplikace  
> - DOCKER_REGISTRY_SERVER_USERNAME  
> - DOCKER_REGISTRY_SERVER_PASSWORD  
>
> Budete muset zastavit a pak spusťte aplikaci function app pro tyto hodnoty, aby ji mohla

Teď můžete svoje funkce běžící na Linuxu v Azure otestovat.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit aplikaci funkcí a soubor Dockerfile pomocí Core Tools
> * Sestavit vlastní image pomocí Dockeru
> * Publikovat vlastní image do registru kontejneru
> * Vytvořit účet služby Azure Storage
> * Vytvořit plán služby App Service pro Linux
> * Nasadit aplikaci Function App z Docker Hubu
> * Přidat do aplikace Function App nastavení aplikace

Zjistěte, jak povolit funkci průběžné integrace, která je integrovaná v základní platformě App Service. Aplikaci funkcí můžete nakonfigurovat tak, aby se při aktualizaci image v Centru Dockeru znovu nasadil kontejner.

> [!div class="nextstepaction"] 
> [Průběžné nasazování pomocí služby Web App for Containers](../app-service/containers/app-service-linux-ci-cd.md)
