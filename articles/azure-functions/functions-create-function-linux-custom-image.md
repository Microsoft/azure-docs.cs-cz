---
title: Vytvoření Azure Functions na platformě Linux s použitím vlastní image
description: Naučte se vytvářet funkce služby Azure Functions běžící na vlastní imagi Linuxu.
ms.date: 01/15/2020
ms.topic: tutorial
ms.custom: mvc
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: c7c9e66e302c2c4181e6249555a7eaaafcc0a960
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2020
ms.locfileid: "78272700"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Vytvoření funkce na platformě Linux pomocí vlastního kontejneru

V tomto kurzu vytvoříte a nasadíte kód Pythonu, který se Azure Functions jako vlastní kontejner Docker pomocí základní image Linux. Vlastní image se obvykle používá, když vaše funkce vyžadují specifickou jazykovou verzi nebo mají konkrétní závislost nebo konfiguraci, která není poskytovaná integrovanou imagí.

Můžete také použít výchozí kontejner Azure App Service, jak je popsáno v tématu [Vytvoření první funkce hostované v systému Linux](functions-create-first-azure-function-azure-cli-linux.md). Podporované základní image pro Azure Functions najdete v [úložišti Azure Functions Base images](https://hub.docker.com/_/microsoft-azure-functions-base).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte aplikaci funkcí a souboru Dockerfile pomocí Azure Functions Core Tools.
> * Sestavit vlastní image pomocí Dockeru
> * Publikovat vlastní image do registru kontejneru
> * Vytvoření pomocných prostředků v Azure pro aplikaci Function App
> * Nasadit aplikaci Function App z Docker Hubu
> * Přidat do aplikace Function App nastavení aplikace
> * Povolte průběžné nasazování.
> * Povolte připojení SSH ke kontejneru.
> * Přidejte výstupní vazbu úložiště fronty. 

Můžete postupovat podle tohoto kurzu na jakémkoli počítači se systémem Windows, Mac OS nebo Linux. Dokončení kurzu vám bude účtovat v účtu Azure náklady na několik amerických dolarů.

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Verze [Azure Functions Core Tools](./functions-run-local.md#v2) 2.7.1846 nebo novější
- [Azure CLI](/cli/azure/install-azure-cli) verze 2.0.77 nebo novější
- [Modul runtime Azure Functions 2. x](functions-versions.md)
- Následující součásti jazykového modulu runtime:
    ::: zone pivot="programming-language-csharp"
    - [.NET Core 2.2. x nebo novější](https://dotnet.microsoft.com/download)
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    - [Node.js](https://nodejs.org/en/download/)
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    - [PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-7)
    ::: zone-end
    ::: zone pivot="programming-language-python"
    - [Python 3,6 – 64 bit](https://www.python.org/downloads/release/python-3610/) nebo [python 3,7 – 64 bit](https://www.python.org/downloads/release/python-376/)
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    - [Node.js](https://nodejs.org/en/download/)
    - [Psací](http://www.typescriptlang.org/#download-links)
    ::: zone-end
- [Docker](https://docs.docker.com/install/)
- [ID Docker](https://hub.docker.com/signup)

### <a name="prerequisite-check"></a>Kontrola požadovaných součástí

1. V terminálu nebo příkazovém okně spusťte `func --version` a ověřte, že Azure Functions Core Tools jsou verze 2.7.1846 nebo novější.
1. Spusťte `az --version` a ověřte, že verze Azure CLI je 2.0.76 nebo novější.
1. Spusťte `az login`, abyste se přihlásili do Azure a ověřili aktivní předplatné.
1. Spusťte `docker login` pro přihlášení k Docker. Tento příkaz se nepovede, pokud Docker není spuštěný, v takovém případě spusťte Docker a zkuste příkaz zopakovat.

## <a name="create-and-test-the-local-functions-project"></a>Vytvoření a otestování projektu místní funkce

1. V terminálu nebo příkazovém řádku vytvořte složku pro tento kurz v příslušném umístění a pak přejděte do této složky.

1. Podle pokynů v tématu [Vytvoření a aktivace virtuálního prostředí](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#create-venv) vytvořte virtuální prostředí pro použití v tomto kurzu.

1. Spusťte následující příkaz pro zvolený jazyk pro vytvoření projektu Function App ve složce s názvem `LocalFunctionsProject`. Možnost `--docker` generuje `Dockerfile` pro projekt, který definuje vhodný vlastní kontejner pro použití s Azure Functions a vybraným modulem runtime.

    ::: zone pivot="programming-language-csharp"
    ```
    func init LocalFunctionsProject --worker-runtime dotnet --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```
    func init LocalFunctionsProject --worker-runtime node --language javascript --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```
    func init LocalFunctionsProject --worker-runtime powershell --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```
    func init LocalFunctionsProject --worker-runtime python --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```
    func init LocalFunctionsProject --worker-runtime node --language typescript --docker
    ```
    ::: zone-end
    
1. Přejděte do složky projektu:

    ```
    cd LocalFunctionsProject
    ```
    
1. Do projektu přidejte funkci pomocí následujícího příkazu, kde `--name` argument je jedinečný název vaší funkce a argument `--template` určuje Trigger funkce. `func new` vytvořit podsložku, která odpovídá názvu funkce, který obsahuje soubor kódu, který je vhodný pro zvolený jazyk projektu, a konfiguračního souboru s názvem *Function. JSON*.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

1. Pokud chcete funkci místně otestovat, spusťte místního hostitele modulu runtime Azure Functions ve složce *LocalFunctionsProject* :
   
    ::: zone pivot="programming-language-csharp"
    ```
    func start --build
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```
    func start
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```
    func start
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```
    func start
    ```
    ::: zone-end    

    ::: zone pivot="programming-language-typescript"
    ```
    npm install
    ```

    ```
    npm start
    ```
    ::: zone-end

1. Jakmile se ve výstupu zobrazí koncový bod `HttpExample`, přejděte na `http://localhost:7071/api/HttpExample?name=Functions`. V prohlížeči by se měla zobrazit zpráva, například Hello, Functions (v závislosti na zvoleném programovacím jazyce je mírně proměnlivá).

1. K zastavení hostitele použijte **kombinaci kláves Ctrl**-**C** .

## <a name="build-the-container-image-and-test-locally"></a>Sestavení image kontejneru a místní test

1. Volitelné Prověřte * souboru Dockerfile ve složce *LocalFunctionsProj* . Souboru Dockerfile popisuje požadované prostředí pro spuštění aplikace Function App v systému Linux: 

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM microsoft/dotnet:2.2-sdk AS installer-env

    COPY . /src/dotnet-function-app
    RUN cd /src/dotnet-function-app && \
        mkdir -p /home/site/wwwroot && \
        dotnet publish *.csproj --output /home/site/wwwroot
    
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/dotnet:2.0-appservice 
    FROM mcr.microsoft.com/azure-functions/dotnet:2.0
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY --from=installer-env ["/home/site/wwwroot", "/home/site/wwwroot"]
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/node:2.0
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot
    
    RUN cd /home/site/wwwroot && \
    npm install    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/powershell:2.0
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY requirements.txt /
    RUN pip install -r /requirements.txt
    
    COPY . /home/site/wwwroot    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/node:2.0
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot
    
    RUN cd /home/site/wwwroot && \
    npm install    
    ```
    ::: zone-end

    > [!NOTE]
    > Úplný seznam podporovaných základních imagí pro Azure Functions najdete na [stránce Azure Functions Base image](https://hub.docker.com/_/microsoft-azure-functions-base).
    
1. Ve složce *LocalFunctionsProject* spusťte příkaz [Docker Build](https://docs.docker.com/engine/reference/commandline/build/) a zadejte název, `azurefunctionsimage`a značku `v1.0.0`. Položku `<docker_id>` nahraďte ID vašeho účtu Docker Hubu. Tento příkaz sestaví image Dockeru pro kontejner.

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
    Po dokončení příkazu můžete nový kontejner spustit místně.
    
1. K otestování sestavení spusťte image v místním kontejneru pomocí příkazu [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) , který se znovu nahradí `<docker_id>` s ID Docker a přidáním argumentu portů `-p 8080:80`:

    ```
    docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Po spuštění image v místním kontejneru otevřete v prohlížeči `http://localhost:8080`, ve kterém se má zobrazit zástupný obrázek uvedený níže. V tomto okamžiku se zobrazí obrázek, protože funkce je spuštěná v místním kontejneru, stejně jako v Azure, což znamená, že je chráněná přístupovým klíčem definovaným v *Function. JSON* s vlastností `"authLevel": "function"`. Kontejner ještě není v Azure publikovaný do aplikace Function App, takže tento klíč ještě není dostupný. Pokud chcete místně testovat test, zastavte Docker, změňte vlastnost Authorization na `"authLevel": "anonymous"`, znovu sestavte image a restartujte Docker. Pak resetujte `"authLevel": "function"` v *Function. JSON*. Další informace najdete v tématu [autorizační klíče](functions-bindings-http-webhook-trigger.md#authorization-keys).

    ![Zástupný obrázek označující, že je kontejner spuštěn místně](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

1. Po ověření aplikace Function App v kontejneru zastavte Docker pomocí **Ctrl**+**C**.

## <a name="push-the-image-to-docker-hub"></a>Odeslat image do Docker Hub

Docker Hub je kontejner kontejneru, který je hostitelem imagí a poskytuje image a služby kontejneru. Pokud chcete sdílet image, která zahrnuje nasazení do Azure, je potřeba ji vložit do registru.

1. Pokud jste se ještě přihlásili k Docker, udělejte to pomocí příkazu [Docker Login](https://docs.docker.com/engine/reference/commandline/login/) , který nahradí `<docker_id>` vaším ID Docker. Tento příkaz vás vyzve k zadání uživatelského jména a hesla. Zpráva o úspěšném přihlášení potvrzuje, že jste přihlášení.

    ```
    docker login
    ```
    
1. Až se přihlásíte, nahrajte image do Docker Hub pomocí příkazu [Docker push](https://docs.docker.com/engine/reference/commandline/push/) a znovu nahraďte `<docker_id>` číslem ID Docker.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. V závislosti na rychlosti sítě se může při prvním vložení image trvat několik minut (vkládání dalších změn je mnohem rychlejší). Až budete čekat, můžete přejít k další části a vytvořit prostředky Azure v jiném terminálu.

## <a name="create-supporting-azure-resources-for-your-function"></a>Vytvoření podpory prostředků Azure pro vaši funkci

Pokud chcete kód funkce nasadit do Azure, musíte vytvořit tři prostředky:

- Skupina prostředků, což je logický kontejner pro související prostředky.
- Účet Azure Storage, který uchovává stav a další informace o vašich projektech.
- Aplikace Azure Functions, která poskytuje prostředí pro provádění kódu funkce. Aplikace Function App se mapuje na váš místní projekt funkce a umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků.

K vytvoření těchto položek použijete příkazy rozhraní příkazového řádku Azure. Každý příkaz poskytuje výstup JSON po dokončení.

1. Přihlaste se k Azure pomocí příkazu [AZ Login](/cli/azure/reference-index#az-login) :

    ```azurecli
    az login
    ```
    
1. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Následující příklad vytvoří skupinu prostředků s názvem `AzureFunctionsContainers-rg` v oblasti `westeurope`. (Obecně vytvoříte skupinu prostředků a prostředky v oblasti, která je blízko vás, pomocí dostupné oblasti z příkazu `az account list-locations`.)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Nemůžete hostovat aplikace pro Linux a Windows ve stejné skupině prostředků. Pokud máte existující skupinu prostředků s názvem `AzureFunctionsContainers-rg` s aplikací Function App nebo webovou aplikací Windows, musíte použít jinou skupinu prostředků.
    
1. Pomocí příkazu [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create) vytvořte účet úložiště pro obecné účely ve vaší skupině prostředků a oblasti. V následujícím příkladu nahraďte `<storage_name>` globálně jedinečným názvem vhodným pro vás. Názvy musí obsahovat tři až 24 znaků a jenom malá písmena. `Standard_LRS` určuje typický účet pro obecné účely.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    Účet úložiště pro tento kurz vyhledá jenom několik centů za USD.
    
1. Pomocí příkazu vytvořte plán Premium pro Azure Functions s názvem `myPremiumPlan` v cenové úrovni **elastické Premium 1** (`--sku EP1`), v západní Evropa oblasti (`-location westeurope`nebo použijte vhodnou oblast poblíž) a v kontejneru Linux (`--is-linux`).

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    Hostování Linux pro kontejnery vlastních funkcí se podporuje na [vyhrazených plánech (App Service)](functions-scale.md#app-service-plan) a [plánech Premium](functions-premium-plan.md#features). Plán Premium používáme tady, který se může škálovat podle potřeby. Další informace o hostování najdete v [porovnání plánů hostování služby Azure Functions](functions-scale.md). Pokud chcete vypočítat náklady, přečtěte si [stránku s cenami funkcí](https://azure.microsoft.com/pricing/details/functions/).

    Příkaz taky zřídí přidruženou instanci služby Azure Application Insights ve stejné skupině prostředků, pomocí které můžete monitorovat aplikaci Function App a zobrazovat protokoly. Další informace najdete v tématu [monitorování Azure Functions](functions-monitoring.md). Instance nenese žádné náklady, dokud ji neaktivujete.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Vytvoření a konfigurace aplikace Function App v Azure s imagí

Aplikace Function App v Azure spravuje spouštění vašich funkcí v plánu hostování. V této části použijete prostředky Azure z předchozí části k vytvoření aplikace Function App z image v Docker Hub a nakonfigurujete ji pomocí připojovacího řetězce, který Azure Storage.

1. Pomocí příkazu [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) vytvořte aplikaci Functions. V následujícím příkladu nahraďte `<storage_name>` názvem, který jste použili v předchozí části pro účet úložiště. Také nahraďte `<app_name>` globálně jedinečným názvem, který je pro vás vhodný, a `<docker_id>` s ID Docker.

    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
    Parametr *Deployment-Container-image-Name* určuje obrázek, který má být použit pro aplikaci Function App. K zobrazení informací o imagi používané pro nasazení můžete použít příkaz [AZ functionapp config Container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) . K nasazení z jiné image můžete použít taky příkaz [AZ functionapp config Container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) .

1. Pomocí příkazu [AZ Storage Account show-Connection-String](/cli/azure/storage/account) načtěte připojovací řetězec pro účet úložiště, který jste vytvořili, a přiřaďte ho k proměnné prostředí `storageConnectionString`:

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    
1. Add this setting to the function app by using the [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) command. In the following command, replace `<app_name>` with the name of your function app, and replace `<connection_string>` with the connection string from the previous step (a long encoded string that begins with "DefaultEndpointProtocol="):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

1. Tato funkce teď může k účtu úložiště přistupovat pomocí tohoto připojovacího řetězce.

> [!TIP]
> V bash můžete použít proměnnou prostředí pro zachycení připojovacího řetězce místo použití schránky. Nejprve pomocí následujícího příkazu vytvořte proměnnou s připojovacím řetězcem:
> 
> ```bash
> storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
> ```
> 
> Potom v druhém příkazu použijte proměnnou:
> 
> ```azurecli
> az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
> ```

> [!NOTE]    
> Pokud publikujete vlastní image na účet privátního kontejneru, měli byste místo toho použít proměnné prostředí v souboru Dockerfile pro připojovací řetězec. Další informace najdete v [pokynech ENV](https://docs.docker.com/engine/reference/builder/#env). Měli byste také nastavit proměnné `DOCKER_REGISTRY_SERVER_USERNAME` a `DOCKER_REGISTRY_SERVER_PASSWORD`. Pokud chcete použít hodnoty, musíte znovu sestavit image, nasdílet image do registru a pak aplikaci Function App znovu spustit v Azure.

## <a name="verify-your-functions-on-azure"></a>Ověření funkcí v Azure

S imagí nasazenými do aplikace Function App v Azure teď můžete funkci vyvolat pomocí požadavků HTTP. Vzhledem k tomu, že definice *Function. JSON* zahrnuje `"authLevel": "function"`vlastností, musíte nejdřív získat přístupový klíč (označovaný také jako "klíč funkce") a zahrnout ho jako parametr URL do všech požadavků na koncový bod.

1. Pomocí Azure Portal nebo pomocí Azure CLI pomocí příkazu `az rest` načtěte adresu URL funkce s klíčem Access (Function).)

    # <a name="portal"></a>[Azure Portal](#tab/portal)

    1. Přihlaste se k Azure Portal a pak vyhledejte svou aplikaci Function App zadáním názvu vaší aplikace Functions do **vyhledávacího** pole v horní části stránky. Ve výsledcích vyberte prostředek **App Service** .

    1. V levém navigačním panelu v části **funkce (jen pro čtení)** vyberte název funkce.

    1. Na panelu podrobností vyberte **</> získat adresu URL funkce**:
    
        ![Příkaz Get URL funkce na Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)   

    1. V místní nabídce vyberte **výchozí (klíč funkce)** a potom **zkopírujte**. Klíč je řetězec znaků, který následuje po `?code=`.

        ![Kopírování adresy URL funkce z Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key-popup.png)   

    > [!NOTE]  
    > Vzhledem k tomu, že je vaše aplikace Function App nasazena jako kontejner, nemůžete na portálu provádět změny kódu funkce. Místo toho je nutné projekt aktualizovat v místní imagi, znovu nahrajte image do registru a pak znovu nasadit do Azure. Průběžné nasazování můžete nastavit v pozdější části.
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

    1. Vytvořte řetězec adresy URL v následujícím formátu, nahraďte `<subscription_id>`, `<resource_group>`a `<app_name>` s ID předplatného Azure, skupinou prostředků vaší aplikace Function App a názvem vaší aplikace Function App (v uvedeném pořadí):

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        Adresa URL může například vypadat jako následující adresa:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > Pro pohodlí můžete místo toho přiřadit adresu URL proměnné prostředí a použít ji v příkazu `az rest`.
    
    1. Spusťte následující příkaz `az rest` (k dispozici v Azure CLI verze 2.0.77 a novější) nahraďte `<uri>` řetězcem URI z posledního kroku včetně uvozovek:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. Výstupem příkazu je klíč funkce. Úplná adresa URL funkce se pak `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>`, nahradí `<app_name>`, `<function_name>`a `<key>` s konkrétními hodnotami.
    
        > [!NOTE]
        > Klíč, který se načte tady, je *hostitelský* klíč, který funguje pro všechny funkce aplikace Functions. Metoda zobrazená na portálu načítá klíč pouze pro jednu funkci.

    ---

1. Vložte adresu URL funkce do adresního řádku prohlížeče a přidejte parametr `&name=Azure` na konec této adresy URL. V prohlížeči by se měl objevit text jako Hello Azure.

    ![Odezva funkce v prohlížeči.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Chcete-li otestovat autorizaci, odeberte z adresy URL parametr Code = a ověřte, že funkce nezíská žádnou odpověď.


## <a name="enable-continuous-deployment-to-azure"></a>Povolení průběžného nasazování do Azure

Můžete povolit, aby Azure Functions automaticky aktualizovala nasazení image vždy, když aktualizujete image v registru.

1. Pomocí příkazu [AZ functionapp Deployment Container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) povolte průběžné nasazování a nahraďte `<app_name>` názvem vaší aplikace Function App:

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    Tento příkaz umožňuje průběžné nasazování a vrací adresu URL Webhooku nasazení. (Tuto adresu URL můžete v pozdější době načíst pomocí příkazu [AZ functionapp Deployment Container show-CD-URL](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) .)

1. Zkopírujte adresu URL Webhooku nasazení do schránky.

1. Otevřete okno [Docker Hub](https://hub.docker.com/), přihlaste se a v navigačním panelu vyberte **úložiště** . Najděte a vyberte obrázek, vyberte kartu **Webhooky** , zadejte **název Webhooku**, vložte adresu URL do pole **Webhook URL**a pak vyberte **vytvořit**:

    ![Přidání Webhooku do úložiště Dockerhubu](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Při nastavení Webhooku Azure Functions znovu nasazovat image, kdykoli ji aktualizujete v Docker Hub.

## <a name="enable-ssh-connections"></a>Povolit připojení SSH

SSH umožňuje zabezpečenou komunikaci mezi kontejnerem a klientem. Když je povolený SSH, můžete se k kontejneru připojit pomocí nástrojů App Service Advanced Tools (Kudu). Aby se usnadnilo připojení ke kontejneru pomocí SSH, Azure Functions poskytuje základní image, která má již povolený SSH. Potřebujete jenom upravit souboru Dockerfile, pak image znovu sestavit a znovu nasadit. Pak se můžete připojit k kontejneru pomocí pokročilých nástrojů (Kudu).

1. V souboru Dockerfile přidejte řetězec `-appservice` k základní imagi ve vaší `FROM` instrukci:

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM microsoft/dotnet:2.2-sdk-appservice AS installer-env
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    Rozdíly mezi základními bitovými kopiemi jsou popsány v [kurzu App Services-vlastní image Docker](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections).

1. Znovu sestavte bitovou kopii pomocí příkazu `docker build` a nahraďte `<docker_id>` číslem ID Docker:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Nahrajte aktualizovanou bitovou kopii do dokovacího centra, které by mělo trvat mnohem méně času než první nabízená oznámení. je potřeba nahrát jenom aktualizované segmenty image.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions automaticky znovu nasadí image do vaší aplikace Functions. proces probíhá za méně než minutu.

1. V prohlížeči otevřete `https://<app_name>.scm.azurewebsites.net/`a nahraďte `<app_name>` jedinečným názvem. Tato adresa URL je koncový bod pokročilých nástrojů (Kudu) pro váš kontejner aplikace Function App.

1. Přihlaste se ke svému účtu Azure a pak vyberte **SSH** , aby se navázalo připojení ke kontejneru. V případě, že Azure stále probíhá proces aktualizace image kontejneru, může chvíli trvat připojení.

1. Po navázání připojení s vaším kontejnerem spusťte příkaz `top` pro zobrazení aktuálně spuštěných procesů. 

    ![Horní příkaz pro Linux běžící v relaci SSH](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>Zapsat do fronty Azure Storage

Azure Functions vám umožní připojit vaše funkce k dalším službám a prostředkům Azure, které musí napsat vlastní kód pro integraci. Tyto *vazby*, které představují vstupní i výstupní, jsou deklarovány v rámci definice funkce. Data z vazeb jsou k dispozici funkci jako parametry. *Trigger* je speciální typ vstupní vazby. I když má funkce pouze jednu Trigger, může mít více vstupních a výstupních vazeb. Další informace najdete v tématu [Azure Functions triggery a koncepty vazeb](functions-triggers-bindings.md).

V této části se dozvíte, jak integrovat funkci do fronty Azure Storage. Výstupní vazba, kterou přidáte do této funkce, zapisuje data z požadavku HTTP do zprávy ve frontě.

## <a name="retrieve-the-azure-storage-connection-string"></a>Načtení připojovacího řetězce Azure Storage

Dříve jste vytvořili účet Azure Storage, který bude používat aplikace Function App. Připojovací řetězec pro tento účet je bezpečně uložený v nastavení aplikace v Azure. Stažením nastavení do souboru *Local. Settings. JSON* můžete toto připojení použít pro zápis do fronty úložiště ve stejném účtu, pokud je funkce spuštěná místně. 

1. Z kořenového adresáře projektu spusťte následující příkaz, ve kterém nahradíte `<app_name>` názvem vaší aplikace Function App z předchozího rychlého startu. Tento příkaz přepíše všechny existující hodnoty v souboru.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Otevřete *Local. Settings. JSON* a vyhledejte hodnotu s názvem `AzureWebJobsStorage`, což je připojovací řetězec účtu úložiště. Použijete název `AzureWebJobsStorage` a připojovací řetězec v dalších částech tohoto článku.

> [!IMPORTANT]
> Protože *Local. Settings. JSON* obsahuje tajné kódy stažené z Azure, tento soubor vždycky vylučte ze správy zdrojového kódu. Soubor *. gitignore* vytvořený pomocí projektu místní funkce vyloučí soubor ve výchozím nastavení.

### <a name="add-an-output-binding-to-functionjson"></a>Přidání výstupní vazby do Function. JSON

V Azure Functions každý typ vazby vyžaduje, aby bylo v souboru *Function. JSON* definováno `direction`, `type`a jedinečné `name`. Vaše *funkce. JSON* už obsahuje vstupní vazbu pro typ "httpTrigger" a výstupní vazbu pro odpověď HTTP. Chcete-li přidat vazbu do fronty úložiště, upravte soubor následujícím způsobem, který přidá výstupní vazbu pro typ "fronta", kde se fronta zobrazí v kódu jako vstupní argument s názvem `msg`. Vazba fronty také vyžaduje název fronty, která se má použít, v tomto případě `outqueue`a název nastavení, které obsahuje připojovací řetězec, v tomto případě `AzureWebJobStorage`.

::: zone pivot="programming-language-csharp"

V projektu C# knihovny tříd jsou vazby definovány jako atributy vazby v metodě Function. Soubor *Function. JSON* se pak automaticky generuje na základě těchto atributů.

1. Pro vazbu fronty spusťte následující příkaz [dotnet přidat balíček](/dotnet/core/tools/dotnet-add-package) a přidejte do projektu balíček rozšíření úložiště.

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
    ```

1. Otevřete soubor *HttpTrigger.cs* a přidejte následující příkaz `using`:

    ```cs
    using Microsoft.Azure.WebJobs.Extensions.Storage;
    ```
    
1. Do definice metody `Run` přidejte následující parametr:
    
    ```csharp
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
    ```
    
    Definice metody `Run` by nyní měla odpovídat následujícímu kódu:
    
    ```csharp
    [FunctionName("HttpTrigger")]
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
        [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
    ```

Parametr `msg` je `ICollector<T>` typ, který představuje kolekci zpráv zapsaných do výstupní vazby po dokončení funkce. V tomto případě je výstupem fronta úložiště s názvem `outqueue`. Připojovací řetězec pro účet úložiště je nastaven `StorageAccountAttribute`. Tento atribut označuje nastavení, které obsahuje připojovací řetězec účtu úložiště a dá se použít na úrovni třídy, metody nebo parametru. V takovém případě můžete `StorageAccountAttribute` vynechat, protože už používáte výchozí účet úložiště.

::: zone-end

::: zone pivot="programming-language-javascript"

Aktualizujte *funkci Function. JSON* tak, aby odpovídala následujícímu po přidání vazby fronty za vazby http:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-powershell"

Aktualizujte *funkci Function. JSON* tak, aby odpovídala následujícímu po přidání vazby fronty za vazby http:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-python"

Aktualizujte *funkci Function. JSON* tak, aby odpovídala následujícímu po přidání vazby fronty za vazby http:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-typescript"

Aktualizujte *funkci Function. JSON* tak, aby odpovídala následujícímu po přidání vazby fronty za vazby http:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

## <a name="add-code-to-use-the-output-binding"></a>Přidat kód pro použití výstupní vazby

Po definování vazby se název vazby v tomto případě `msg`zobrazí v kódu funkce jako argument (nebo v objektu `context` v jazyce JavaScript a TypeScript). Tuto proměnnou pak můžete použít k zápisu zpráv do fronty. Musíte napsat jakýkoliv kód pro ověřování, získat odkaz na frontu nebo zapisovat data. Všechny tyto úkoly integrace jsou pohodlně zpracovávány v Azure Functions modul runtime a výstupní vazba fronty.

::: zone pivot="programming-language-csharp"
```csharp
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a message to the output collection.
        msg.Add(string.Format("Name passed to the function: {0}", name));
    }
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```
::: zone-end

::: zone pivot="programming-language-javascript"
```js
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    if (req.query.name || (req.body && req.body.name)) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " +
            (req.query.name || req.body.name);

        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};
```
::: zone-end

::: zone pivot="programming-language-powershell"
```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$name = $Request.Query.Name
if (-not $name) {
    $name = $Request.Body.Name
}

if ($name) {
    $outputMsg = "Name passed to the function: $name"
    Push-OutputBinding -name msg -Value $outputMsg

    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
else {
    $status = [HttpStatusCode]::BadRequest
    $body = "Please pass a name on the query string or in the request body."
}

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```
::: zone-end

::: zone pivot="programming-language-python"
```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```
::: zone-end

::: zone pivot="programming-language-typescript"
```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions"

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    context.log('HTTP trigger function processed a request.');
    const name = (req.query.name || (req.body && req.body.name));

    if (name) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " +
            (req.query.name || req.body.name);
        
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};

export default httpTrigger;
```
::: zone-end

### <a name="update-the-image-in-the-registry"></a>Aktualizace image v registru

1. V kořenové složce znovu spusťte `docker build` a tentokrát aktualizujte verzi ve značce na `v1.0.1`. Stejně jako dřív nahraďte `<docker_id>` číslem účtu Docker Hub:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1
    ```
    
1. Nahrajte aktualizovaný obrázek zpátky do úložiště pomocí `docker push`:

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Vzhledem k tomu, že jste nakonfigurovali průběžné doručování, aktualizace image v registru znovu automaticky aktualizuje aplikaci Function App v Azure.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Zobrazení zprávy ve frontě Azure Storage

V prohlížeči použijte stejnou adresu URL jako před voláním funkce. V prohlížeči by se měla zobrazit stejná odpověď jako předtím, protože jste tuto část kódu funkce nezměnili. Přidaný kód však zapsal zprávu pomocí parametru `name` URL do fronty úložiště `outqueue`.

Tuto frontu můžete zobrazit v [Azure Portal](../storage/queues/storage-quickstart-queues-portal.md) nebo v [Průzkumník služby Microsoft Azure Storage](https://storageexplorer.com/). Tuto frontu můžete také zobrazit v rozhraní příkazového řádku Azure CLI, jak je popsáno v následujících krocích:

1. Otevřete soubor *Local. Setting. JSON* projektu funkce a zkopírujte hodnotu připojovacího řetězce. V terminálu nebo příkazovém okně spusťte následující příkaz, který vytvoří proměnnou prostředí s názvem `AZURE_STORAGE_CONNECTION_STRING`a místo `<connection_string>`bude vkládat konkrétní připojovací řetězec. (Tato proměnná prostředí znamená, že připojovací řetězec nemusíte zadávat do každého následného příkazu pomocí argumentu `--connection-string`.)

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmd"></a>[Přepsat](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. Volitelné Pomocí příkazu [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) můžete zobrazit fronty úložiště ve vašem účtu. Výstup z tohoto příkazu by měl zahrnovat frontu s názvem `outqueue`, která byla vytvořena při zapsání první zprávy do této fronty.
    
    # <a name="bash"></a>[bash](#tab/bash)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="cmd"></a>[Přepsat](#tab/cmd)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    ---

1. Pomocí příkazu [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) můžete zobrazit zprávy v této frontě, které by měly být křestní jméno, které jste použili při předchozím testování funkce. Příkaz načte první zprávu ve frontě v [kódování Base64](functions-bindings-storage-queue-trigger.md#encoding), takže musíte také dekódovat zprávu, aby se zobrazila jako text.

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Přepsat](#tab/cmd)
    
    Vzhledem k tomu, že je potřeba odkázat na shromažďování zpráv a dekódovat z formátu base64, spusťte PowerShell a použijte příkaz prostředí PowerShell.

    ---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v práci s funkcí Azure pomocí prostředků, které jste vytvořili v tomto kurzu, můžete ponechat všechny tyto prostředky na místě. Vzhledem k tomu, že jste pro Azure Functions vytvořili plán Premium, za průběžné náklady se vám bude účtovat jedna nebo dvě USD za den.

Chcete-li se vyhnout průběžným nákladům, odstraňte skupinu prostředků `AzureFunctionsContainer-rg` pro vyčištění všech prostředků v této skupině: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Další kroky

+ [Funkce monitorování](functions-monitoring.md)
+ [Možnosti škálování a hostování](functions-scale.md)
+ [Hostování bez serveru založené na Kubernetes](functions-kubernetes-keda.md)
