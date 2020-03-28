---
title: Vytváření funkcí Azure na Linuxu pomocí vlastní image
description: Naučte se vytvářet funkce služby Azure Functions běžící na vlastní imagi Linuxu.
ms.date: 01/15/2020
ms.topic: tutorial
ms.custom: mvc
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 8c074c677c645dd03e3cf5288d82aa3e65720e8b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239627"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Vytvoření funkce v Linuxu pomocí vlastního kontejneru

V tomto kurzu vytvoříte a nasadíte kód Pythonu do Azure Functions jako vlastní kontejner Dockeru pomocí základní image Linuxu. Vlastní bitovou kopii se obvykle používá v případě, že funkce vyžadují určitou jazykovou verzi nebo mají určitou závislost nebo konfiguraci, která není poskytována vestavěnou bitovou kopií.

Můžete také použít výchozí kontejner služby Azure App Service, jak je popsáno v [na vytvořit první funkci hostované na Linuxu](functions-create-first-azure-function-azure-cli-linux.md). Podporované základní image pro funkce Azure se nacházejí v [repo základní image Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte aplikaci funkcí a Dockerfile pomocí nástrojů Azure Functions Core Tools.
> * Sestavit vlastní image pomocí Dockeru
> * Publikovat vlastní image do registru kontejneru
> * Vytvoření podpůrných prostředků v Azure pro aplikaci funkcí
> * Nasadit aplikaci Function App z Docker Hubu
> * Přidat do aplikace Function App nastavení aplikace
> * Povolte průběžné nasazení.
> * Povolte připojení SSH ke kontejneru.
> * Přidejte výstupní vazbu úložiště fronty. 

Tento návod můžete sledovat na libovolném počítači se systémem Windows, Mac OS nebo Linux. Dokončení kurzu bude mít za následek náklady na několik amerických dolarů ve vašem účtu Azure.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Základní nástroje Azure functions](./functions-run-local.md#v2) verze 2.7.1846 nebo novější
- Azure [CLI](/cli/azure/install-azure-cli) verze 2.0.77 nebo novější
- [Za běhu Azure Functions 2.x](functions-versions.md)
- Následující součásti běhu jazyka:
    ::: zone pivot="programming-language-csharp"
    - [.NET Core 2.2.x nebo novější](https://dotnet.microsoft.com/download)
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    - [Node.js](https://nodejs.org/en/download/)
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    - [PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-7)
    ::: zone-end
    ::: zone pivot="programming-language-python"
    - [Python 3.6 - 64 bit](https://www.python.org/downloads/release/python-3610/) nebo [Python 3.7 - 64 bit](https://www.python.org/downloads/release/python-376/)
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    - [Node.js](https://nodejs.org/en/download/)
    - [TypeScript](http://www.typescriptlang.org/#download-links)
    ::: zone-end
- [Docker](https://docs.docker.com/install/)
- [ID dockeru](https://hub.docker.com/signup)

### <a name="prerequisite-check"></a>Kontrola předpokladů

1. V terminálu nebo příkazovém okně spusťte `func --version` a zkontrolujte, zda jsou základní nástroje Azure Functions core tools verze 2.7.1846 nebo novější.
1. Spuštěním `az --version` zkontrolujte, zda je verze příkazového příkazového příkazu Azure 2.0.76 nebo novější.
1. Spusťte `az login` přihlášení k Azure a ověřte aktivní předplatné.
1. Spusťte `docker login` přihlášení do Dockeru. Tento příkaz se nezdaří, pokud Docker není spuštěn, v takovém případě spustit docker a opakujte příkaz.

## <a name="create-and-test-the-local-functions-project"></a>Vytvoření a testování projektu místních funkcí

1. V terminálu nebo příkazovém řádku vytvořte složku pro tento kurz na příslušném místě a přejděte do této složky.

1. Postupujte podle pokynů na [vytvoření a aktivaci virtuálního prostředí](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#create-venv) k vytvoření virtuálního prostředí pro použití v tomto kurzu.

1. Spusťte následující příkaz pro zvolený jazyk a vytvořte `LocalFunctionsProject`projekt aplikace funkcí ve složce s názvem . Možnost `--docker` generuje `Dockerfile` pro projekt, který definuje vhodný vlastní kontejner pro použití s Funkcemi Azure a vybraného běhu.

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
    
1. Přidejte funkci do projektu pomocí následujícího `--name` příkazu, kde argument je `--template` jedinečný název funkce a argument určuje aktivační událost funkce. `func new`Vytvořte podsložku odpovídající názvu funkce, která obsahuje soubor kódu odpovídající zvolenému jazyku projektu a konfigurační soubor s názvem *function.json*.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

1. Chcete-li otestovat funkci místně, spusťte místního hostitele runtime Azure Functions ve složce *LocalFunctionsProject:*
   
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

1. Jakmile se `HttpExample` koncový bod zobrazí ve výstupu, přejděte na `http://localhost:7071/api/HttpExample?name=Functions`. Prohlížeč by měl zobrazit zprávu jako "Hello, Functions" (mírně se liší v závislosti na zvoleném programovacím jazyce).

1. K zastavení hostitele použijte **kombinaci kláves**-**C.**

## <a name="build-the-container-image-and-test-locally"></a>Sestavení image kontejneru a testování místně

1. (Nepovinné) Zkontrolujte *Dockerfile" ve složce *LocalFunctionsProj.* Dockerfile popisuje požadované prostředí pro spuštění aplikace funkce na Linuxu: 

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
    > Úplný seznam podporovaných základních bitových kopií pro funkce Azure najdete na [stránce základní bitové kopie Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base).
    
1. Ve složce *LocalFunctionsProject* spusťte příkaz [sestavení dockeru](https://docs.docker.com/engine/reference/commandline/build/) a zadejte název `azurefunctionsimage`a značku . `v1.0.0` Položku `<docker_id>` nahraďte ID vašeho účtu Docker Hubu. Tento příkaz sestaví image Dockeru pro kontejner.

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
    Po dokončení příkazu můžete spustit nový kontejner místně.
    
1. Chcete-li otestovat sestavení, spusťte bitovou kopii v `<docker_id>` místním kontejneru pomocí příkazu `-p 8080:80` [docker run,](https://docs.docker.com/engine/reference/commandline/run/) znovu nahraďte ID Dockeru a přidejte argument portů:

    ```
    docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Jakmile je obrázek spuštěn v místním kontejneru, otevřete prohlížeč , který `http://localhost:8080`by měl zobrazit zástupný obrázek zobrazený níže. Bitová kopie se zobrazí v tomto okamžiku, protože vaše funkce běží v místním kontejneru, stejně jako v Azure, což `"authLevel": "function"` znamená, že je chráněn přístupový klíč, jak je definováno v *function.json* s vlastností. Kontejner ještě nebyl publikován do aplikace funkce v Azure, takže klíč ještě není k dispozici. Pokud chcete testovat místně, zastavit docker, změnit `"authLevel": "anonymous"`vlastnost autorizace na , znovu sestavit image a restartovat docker. Poté `"authLevel": "function"` resetujte v *souboru function.json*. Další informace naleznete v tématu [autorizační klíče](functions-bindings-http-webhook-trigger.md#authorization-keys).

    ![Zástupný obrázek označující, že kontejner běží místně](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

1. Po ověření aplikace funkce v kontejneru zastavte docker pomocí **klávesCtrl**+**C**.

## <a name="push-the-image-to-docker-hub"></a>Posunutí image do Centra Dockeru

Docker Hub je registr kontejnerů, který hostuje bitové kopie a poskytuje služby image a kontejner. Chcete-li sdílet bitovou kopii, která zahrnuje nasazení do Azure, musíte ji přesunout do registru.

1. Pokud jste se ještě nepřihlásili do Dockeru, uvažte to pomocí příkazu [přihlášení dockeru](https://docs.docker.com/engine/reference/commandline/login/) a nahraďte je `<docker_id>` ID Dockeru. Tento příkaz zobrazí výzvu k zadání uživatelského jména a hesla. Zpráva "Přihlášení proběhlo úspěšně" potvrzuje, že jste přihlášeni.

    ```
    docker login
    ```
    
1. Po přihlášení převeletimage do Docker Hubu pomocí příkazu push `<docker_id>` [dockeru,](https://docs.docker.com/engine/reference/commandline/push/) který znovu nahradí vaše ID Dockeru.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. V závislosti na rychlosti sítě může první stisknutí obrázku trvat několik minut (posun následných změn je mnohem rychlejší). Zatímco čekáte, můžete přejít k další části a vytvořit prostředky Azure v jiném terminálu.

## <a name="create-supporting-azure-resources-for-your-function"></a>Vytvoření podpůrných prostředků Azure pro vaši funkci

Chcete-li nasadit kód funkce do Azure, musíte vytvořit tři prostředky:

- Skupina prostředků, která je logickým kontejnerem pro související prostředky.
- Účet Azure Storage, který udržuje stav a další informace o vašich projektech.
- Aplikace Azure funkce, která poskytuje prostředí pro provádění kódu funkce. Aplikace funkcí se mapuje na projekt místních funkcí a umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazení a sdílení prostředků.

K vytvoření těchto položek se používají příkazy Příkazy příkazového příkazu Azure. Každý příkaz poskytuje výstup JSON po dokončení.

1. Přihlaste se k Azure pomocí příkazu [az login:](/cli/azure/reference-index#az-login)

    ```azurecli
    az login
    ```
    
1. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Následující příklad vytvoří skupinu `AzureFunctionsContainers-rg` prostředků `westeurope` s názvem v oblasti. (Skupinu prostředků a prostředky obvykle vytváříte v oblasti ve `az account list-locations` vašem okolí pomocí dostupné oblasti z příkazu.)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Linux a Windows nelze hostovat ve stejné skupině prostředků. Pokud máte existující skupinu `AzureFunctionsContainers-rg` prostředků pojmenovanou pomocí aplikace pro funkce Windows nebo webové aplikace, musíte použít jinou skupinu prostředků.
    
1. Vytvořte účet úložiště pro obecné účely ve skupině prostředků a oblasti pomocí příkazu [vytvořit účet úložiště az.](/cli/azure/storage/account#az-storage-account-create) V následujícím příkladu `<storage_name>` nahraďte globálně jedinečný název, který je vám vhodný. Názvy musí obsahovat pouze tři až 24 písmen a malá písmena. `Standard_LRS`určuje typický účet pro obecné účely.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    Účet úložiště vznikne pouze několik USD centů pro tento kurz.
    
1. Pomocí příkazu vytvořte plán Premium `myPremiumPlan` pro funkce Azure pojmenovaný`--sku EP1`v cenové úrovni Elastic`-location westeurope` **Premium 1** ( ), v oblasti Západní`--is-linux`Evropa ( , nebo použijte vhodnou oblast ve vašem okolí) a v kontejneru Linuxu ( ).

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    Linux hosting pro vlastní funkce kontejnery jsou podporovány na [vyhrazené (App Service) plány](functions-scale.md#app-service-plan) a [Premium plány](functions-premium-plan.md#features). Zde používáme plán Premium, který lze podle potřeby škálovat. Další informace o hostování najdete v [porovnání plánů hostování služby Azure Functions](functions-scale.md). Chcete-li vypočítat náklady, přečtěte si [stránku S cenami funkce](https://azure.microsoft.com/pricing/details/functions/).

    Příkaz také zřídí přidruženou instanci Azure Application Insights ve stejné skupině prostředků, pomocí které můžete sledovat aplikaci funkcí a zobrazit protokoly. Další informace naleznete v [tématu Sledování funkcí Azure](functions-monitoring.md). Instance nevznikne žádné náklady, dokud ji neaktivujete.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Vytvoření a konfigurace aplikace funkcí v Azure pomocí bitové kopie

Aplikace funkcí v Azure spravuje provádění vašich funkcí ve vašem hostingovém plánu. V této části použijete prostředky Azure z předchozí části k vytvoření aplikace funkcí z image v Docker Hubu a nakonfigurujete ji pomocí připojovacího řetězce k Azure Storage.

1. Vytvořte aplikaci Funkce pomocí příkazu [az functionapp create.](/cli/azure/functionapp#az-functionapp-create) V následujícím příkladu `<storage_name>` nahraďte názvem, který jste použili v předchozí části pro účet úložiště. Také `<app_name>` nahraďte globálně jedinečný název, `<docker_id>` který je vám vhodný, a vaše ID Dockeru.

    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
    Parametr *název deployment-container-image* určuje bitovou kopii, která se má použít pro aplikaci funkce. Pomocí příkazu [az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) můžete zobrazit informace o bitové kopii použité pro nasazení. Můžete také použít [az functionapp config kontejner nastavit](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) příkaz k nasazení z jiné bitové kopie.

1. Načtěte připojovací řetězec pro účet úložiště, který jste vytvořili, pomocí příkazu `storageConnectionString` [az storage account show-connection-string](/cli/azure/storage/account) a přiřaďte jej proměnné prostředí :

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. Přidejte toto nastavení do aplikace funkce pomocí příkazu [az functionapp config appsettings set.](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) V následujícím příkazu `<app_name>` nahraďte názvem aplikace funkce `<connection_string>` a nahraďte připojovacím řetězcem z předchozího kroku (dlouhý kódovaný řetězec začínající řetězcem "DefaultEndpointProtocol="):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

1. Funkce nyní můžete použít tento připojovací řetězec pro přístup k účtu úložiště.

> [!TIP]
> V bash, můžete použít proměnnou prostředí zachytit připojovací řetězec namísto použití schránky. Nejprve pomocí následujícího příkazu vytvořte proměnnou s připojovacím řetězcem:
> 
> ```bash
> storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
> ```
> 
> Potom naleznete proměnnou v druhém příkazu:
> 
> ```azurecli
> az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
> ```

> [!NOTE]    
> Pokud publikujete vlastní bitovou kopii do účtu soukromého kontejneru, měli byste místo toho použít proměnné prostředí v souboru Dockerfile pro připojovací řetězec. Další informace naleznete v [pokynech env](https://docs.docker.com/engine/reference/builder/#env). Měli byste také nastavit `DOCKER_REGISTRY_SERVER_USERNAME` `DOCKER_REGISTRY_SERVER_PASSWORD`proměnné a . Chcete-li použít hodnoty, potom je nutné znovu sestavit bitovou kopii, push image do registru a potom restartovat aplikaci funkce v Azure.

## <a name="verify-your-functions-on-azure"></a>Ověření funkcí v Azure

S image nasazené do aplikace funkce v Azure, teď můžete vyvolat funkci prostřednictvím požadavků HTTP. Vzhledem k tomu, že definice `"authLevel": "function"` *function.json* obsahuje vlastnost , musíte nejprve získat přístupový klíč (také nazývaný "funkční klíč") a zahrnout jej jako parametr URL do všech požadavků na koncový bod.

1. Načíst adresu URL funkce pomocí přístupového (funkčního) klíče pomocí portálu `az rest` Azure nebo pomocí příkazu Azure CLI.)

    # <a name="portal"></a>[Portál](#tab/portal)

    1. Přihlaste se k portálu Azure portal a vyhledejte aplikaci funkcí zadáním názvu aplikace funkce do **vyhledávacího** pole v horní části stránky. Ve výsledcích vyberte prostředek **služby App Service.**

    1. Na levém navigačním panelu vyberte v části **Funkce (jen pro čtení)** název funkce.

    1. V panelu podrobnosti vyberte **adresu URL funkce</> Získat**:
    
        ![Příkaz Získat adresu URL funkce na webu Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)   

    1. Ve vyskakovacím okno vyberte **výchozí (funkční klávesa)** a potom **zkopírujte**. Klíčem je řetězec následujících `?code=`znaků .

        ![Kopírování adresy URL funkce z webu Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key-popup.png)   

    > [!NOTE]  
    > Vzhledem k tomu, že vaše aplikace funkce se nasadí jako kontejner, nemůžete provádět změny kódu funkce na portálu. Místo toho je nutné aktualizovat projekt v místní bitové kopii, znovu přesunout bitovou kopii do registru a znovu nasadit do Azure. Průběžné nasazení můžete nastavit v pozdější části.
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

    1. Vytvořte řetězec ADRESY URL v `<subscription_id>`následujícím `<resource_group>`formátu, který nahradí , a `<app_name>` s ID předplatného Azure, skupiny prostředků vaší aplikace funkce a název aplikace funkce, v uvedeném pořadí:

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        Adresa URL může například vypadat jako následující adresa:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > Pro větší pohodlí můžete místo toho přiřadit adresu URL `az rest` proměnné prostředí a použít ji v příkazu.
    
    1. Spusťte `az rest` následující příkaz (k dispozici ve verzi Azure CLI verze `<uri>` 2.0.77 a novější), který nahradí řetězec URI z posledního kroku, včetně uvozovek:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. Výstuppříkazu je funkční klávesa. Adresa URL plné `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>`funkce je `<app_name>` `<function_name>`pak `<key>` , nahrazení , a vaše konkrétní hodnoty.
    
        > [!NOTE]
        > Klíč načtený zde je *hostitelský* klíč, který funguje pro všechny funkce v aplikaci funkce; metoda zobrazená pro portál načte klíč pouze pro jednu funkci.

    ---

1. Vložte adresu URL funkce do adresního `&name=Azure` řádku prohlížeče a přidejte parametr na konec této adresy URL. Text jako "Hello Azure" by se měl zobrazit v prohlížeči.

    ![Odezva funkce v prohlížeči.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Chcete-li otestovat autorizaci, odeberte parametr code= z adresy URL a ověřte, že z funkce nedostanete žádnou odpověď.


## <a name="enable-continuous-deployment-to-azure"></a>Povolení průběžného nasazení do Azure

Funkce Azure můžete povolit automatické aktualizace nasazení bitové kopie při každé aktualizaci bitové kopie v registru.

1. Povolte průběžné nasazení pomocí [příkazu konfigurace kontejneru nasazení az functionapp,](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) který nahradí `<app_name>` název aplikace funkce:

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    Tento příkaz umožňuje průběžné nasazení a vrátí adresu URL webhooku nasazení. (Tuto adresu URL můžete načíst kdykoli později pomocí příkazu [az functionapp deployment container show-cd-url.)](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url)

1. Zkopírujte adresu URL webhooku nasazení do schránky.

1. Otevřete [Centrum Dockeru](https://hub.docker.com/), přihlaste se a na panelu na vyvolených vyberte **Úložiště.** Vyhledejte a vyberte obrázek, vyberte kartu **Webhooks,** zadejte **název Webhooku**, vložte adresu URL do **adresy URL webhooku**a pak vyberte **Vytvořit**:

    ![Přidání webového háčku do úložiště DockerHubu](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Se sadou webhooku Azure Functions znovu nasadí vaši image při každé aktualizaci v Docker Hubu.

## <a name="enable-ssh-connections"></a>Povolení připojení SSH

SSH umožňuje zabezpečenou komunikaci mezi kontejnerem a klientem. S povolenou funkcí SSH se můžete připojit ke svému kontejneru pomocí pokročilých nástrojů služby App Service (Kudu). Chcete-li usnadnit připojení ke kontejneru pomocí SSH, Funkce Azure poskytuje základní image, která má SSH již povolena. Stačí upravit soubor Dockerfile a potom znovu sestavit a znovu nasadit bitovou kopii. Poté se můžete připojit ke kontejneru prostřednictvím rozšířeného nástroje (Kudu)

1. Ve vašem Dockerfile, připojit `-appservice` řetězec k základní `FROM` image v instrukci:

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

    Rozdíly mezi základní image jsou popsány v [aplikační služby – vlastní docker image kurzu](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections).

1. Znovu sestavte `docker build` obraz pomocí příkazu znovu a nahraďte `<docker_id>` se svým ID Dockeru:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Posuňte aktualizovanou bitovou kopii do centra Docker Hub, což by mělo trvat podstatně méně času než první nabízená zpráva, že je potřeba nahrát pouze aktualizované segmenty bitové kopie.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Funkce Azure automaticky znovu nasadí image do aplikace funkce; proces probíhá za méně než minutu.

1. V prohlížeči `https://<app_name>.scm.azurewebsites.net/`otevřete `<app_name>` a nahraďte jedinečným jménem. Tato adresa URL je koncový bod Rozšířené nástroje (Kudu) pro kontejner aplikace funkce.

1. Přihlaste se ke svému účtu Azure a pak vyberte **SSH** pro navázání připojení s kontejnerem. Připojení může trvat několik okamžiků, pokud Azure je stále v procesu aktualizace image kontejneru.

1. Po navázání připojení s kontejnerem `top` spusťte příkaz a zobrazte aktuálně spuštěné procesy. 

    ![Linux top příkaz běží v relaci SSH](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>Zápis do fronty Azure Storage

Funkce Azure vám umožní připojit vaše funkce k dalším službám Azure a prostředkům, které musí psát vlastní integrační kód. Tyto *vazby*, které představují vstup i výstup, jsou deklarovány v rámci definice funkce. Data z vazeb jsou k dispozici funkci jako parametry. *Aktivační událost* je zvláštní typ vstupní vazby. Přestože funkce má pouze jednu aktivační událost, může mít více vstupních a výstupních vazeb. Další informace najdete v [tématu Azure Functions aktivační události a vazby koncepty](functions-triggers-bindings.md).

V této části se zobrazí, jak integrovat funkci s frontou azure storage. Výstupní vazba, kterou přidáte do této funkce, zapisuje data z požadavku HTTP do zprávy ve frontě.

## <a name="retrieve-the-azure-storage-connection-string"></a>Načtení připojovacího řetězce úložiště Azure

Dříve jste vytvořili účet Úložiště Azure pro použití aplikací funkce. Připojovací řetězec pro tento účet se bezpečně uchovává v nastavení aplikace v Azure. Stažením nastavení do souboru *local.settings.json* můžete toto připojení použít do fronty úložiště ve stejném účtu při místním spuštění funkce. 

1. V kořenovém adresáři projektu spusťte `<app_name>` následující příkaz, který nahradí název aplikace funkce z předchozího rychlého startu. Tento příkaz přepíše všechny existující hodnoty v souboru.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Otevřete *soubor local.settings.json* `AzureWebJobsStorage`a vyhledejte hodnotu s názvem , což je připojovací řetězec účtu úložiště. Název `AzureWebJobsStorage` a připojovací řetězec v jiných částech tohoto článku.

> [!IMPORTANT]
> Vzhledem k tomu, *že local.settings.json* obsahuje tajné klíče stažené z Azure, vždy vyloučit tento soubor ze správy zdrojového kódu. Soubor *.gitignore* vytvořený s projektem místních funkcí ve výchozím nastavení vyloučí soubor.

### <a name="add-an-output-binding-to-functionjson"></a>Přidání výstupní vazby do souboru function.json

V Azure Functions každý typ `direction`vazby `type`vyžaduje `name` , a jedinečný, které mají být definovány v souboru *function.json.* Vaše *function.json* již obsahuje vstupní vazbu pro typ "httpTrigger" a výstupní vazbu pro odpověď HTTP. Chcete-li přidat vazbu do fronty úložiště, upravte soubor následujícím způsobem, který přidá výstupní vazbu pro typ "queue", kde se fronta zobrazí v kódu jako vstupní argument s názvem `msg`. Vazba fronty také vyžaduje název fronty, který `outqueue`má být v tomto případě používán , a `AzureWebJobStorage`název nastavení, které obsahuje připojovací řetězec, v tomto případě .

::: zone pivot="programming-language-csharp"

V projektu knihovny tříd jazyka C# jsou vazby definovány jako atributy vazby na metodě funkce. Soubor *function.json* je pak automaticky generován na základě těchto atributů.

1. Pro vazbu fronty spusťte následující příkaz [dotnet add package](/dotnet/core/tools/dotnet-add-package) a přidejte balíček rozšíření úložiště do projektu.

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
    ```

1. Otevřete *soubor HttpTrigger.cs* a `using` přidejte následující příkaz:

    ```cs
    using Microsoft.Azure.WebJobs.Extensions.Storage;
    ```
    
1. Do definice `Run` metody přidejte následující parametr:
    
    ```csharp
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
    ```
    
    Definice `Run` metody by nyní měla odpovídat následujícímu kódu:
    
    ```csharp
    [FunctionName("HttpTrigger")]
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
        [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
    ```

Parametr `msg` je `ICollector<T>` typ, který představuje kolekci zpráv, které jsou zapsány do výstupní vazby po dokončení funkce. V tomto případě je výstupem `outqueue`fronta úložiště s názvem . Připojovací řetězec pro účet `StorageAccountAttribute`úložiště je nastaven . Tento atribut označuje nastavení, které obsahuje připojovací řetězec účtu úložiště a lze jej použít na úrovni třídy, metody nebo parametru. V takovém případě můžete vynechat, `StorageAccountAttribute` protože už používáte výchozí účet úložiště.

::: zone-end

::: zone pivot="programming-language-javascript"

Aktualizujte *soubor function.json* tak, aby odpovídal následujícímu, přidáním vazby fronty za vazbu HTTP:

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

Aktualizujte *soubor function.json* tak, aby odpovídal následujícímu, přidáním vazby fronty za vazbu HTTP:

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

Aktualizujte *soubor function.json* tak, aby odpovídal následujícímu, přidáním vazby fronty za vazbu HTTP:

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

Aktualizujte *soubor function.json* tak, aby odpovídal následujícímu, přidáním vazby fronty za vazbu HTTP:

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

## <a name="add-code-to-use-the-output-binding"></a>Přidání kódu pro použití výstupní vazby

Po definování vazby se název vazby v `msg`tomto případě zobrazí v kódu funkce `context` jako argument (nebo v objektu v jazyce JavaScript a TypeScript). Tuto proměnnou pak můžete použít k zápisu zpráv do fronty. Je třeba napsat libovolný kód pro ověřování, získání odkazu na frontu nebo zápis dat. Všechny tyto úlohy integrace jsou pohodlně zpracovány v Azure Functions runtime a fronty výstupní vazby.

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

### <a name="update-the-image-in-the-registry"></a>Aktualizace bitové kopie v registru

1. V kořenové složce spusťte `docker build` znovu a tentokrát `v1.0.1`aktualizujte verzi značky na . Stejně jako `<docker_id>` dříve nahraďte ID účtu Centra Docker:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1
    ```
    
1. Zatlačte aktualizovaný obrázek zpět `docker push`do úložiště pomocí :

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Vzhledem k tomu, že jste nakonfigurovali průběžné doručování, aktualizace bitové kopie v registru znovu automaticky aktualizuje vaši aplikaci funkce v Azure.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Zobrazení zprávy ve frontě Azure Storage

V prohlížeči použijte stejnou adresu URL jako dříve k vyvolání funkce. Prohlížeč by měl zobrazit stejnou odpověď jako dříve, protože jste tuto část kódu funkce neupravili. Přidaný kód však napsal zprávu `name` pomocí parametru URL do fronty `outqueue` úložiště.

Frontu můžete zobrazit na [webu Azure Portal](../storage/queues/storage-quickstart-queues-portal.md) nebo v [Průzkumníkovi úložiště Microsoft Azure](https://storageexplorer.com/). Frontu můžete také zobrazit v příkazovém příkazu k dispozici pro Azure, jak je popsáno v následujících krocích:

1. Otevřete soubor *local.setting.json* projektu funkce a zkopírujte hodnotu připojovacího řetězce. V terminálu nebo příkazovém okně spusťte `AZURE_STORAGE_CONNECTION_STRING`následující příkaz a vytvořte proměnnou prostředí s názvem a místo ní vložte konkrétní připojovací řetězec `<connection_string>`. (Tato proměnná prostředí znamená, že není nutné zadávat připojovací řetězec ke každému následnému příkazu pomocí argumentu.) `--connection-string`

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. (Nepovinné) Pomocí [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) příkazu můžete zobrazit fronty úložiště ve vašem účtu. Výstup z tohoto příkazu by `outqueue`měl obsahovat frontu s názvem , která byla vytvořena, když funkce napsala svou první zprávu do této fronty.
    
    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    ---

1. Pomocí [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) příkazu můžete zobrazit zprávy v této frontě, což by mělo být první jméno, které jste použili při testování funkce dříve. Příkaz načte první zprávu ve frontě v [kódování base64](functions-bindings-storage-queue-trigger.md#encoding), takže je nutné také dekódovat zprávu, která má být zobrazována jako text.

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    Protože potřebujete odkazovat na kolekci zpráv a dekódovat z base64, spusťte PowerShell a použijte příkaz PowerShell.

    ---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v práci s Azure Function pomocí prostředků, které jste vytvořili v tomto kurzu, můžete ponechat všechny tyto prostředky na místě. Protože jste vytvořili prémiový plán pro funkce Azure, bude vám účtovánjeden nebo dva USD za den v průběžných nákladech.

Chcete-li se vyhnout `AzureFunctionsContainer-rg` průběžných nákladů, odstraňte skupinu prostředků a vyčistěte všechny prostředky v této skupině: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Další kroky

+ [Monitorovací funkce](functions-monitoring.md)
+ [Možnosti škálování a hostování](functions-scale.md)
+ [Hosting bez serveru založený na Kubernetes](functions-kubernetes-keda.md)
