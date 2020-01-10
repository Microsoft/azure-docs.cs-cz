---
title: Vytvoření Azure Functions na platformě Linux s použitím vlastní image
description: Naučte se vytvářet funkce služby Azure Functions běžící na vlastní imagi Linuxu.
ms.date: 09/27/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: e70edac09c8b2d61c148c9ba0fd04ec231e9a965
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769315"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>Vytvoření funkce na platformě Linux pomocí vlastní image

Služba Azure Functions umožňuje hostovat funkce v Linuxu ve vašem vlastním kontejneru. Můžete také [hostovat ve výchozím kontejneru služby Azure App Service](functions-create-first-azure-function-azure-cli-linux.md). Tato funkce vyžaduje [modul runtime Functions 2. x](functions-versions.md).

V tomto kurzu se dozvíte, jak do Azure nasadit funkce jako vlastní image Dockeru. Tento model je užitečný v případě, že potřebujete přizpůsobit integrovanou image kontejneru. Když vaše funkce vyžadují určitou jazykovou verzi nebo konkrétní závislost nebo konfiguraci, kterou vestavěná image neposkytuje, můžete chtít použít vlastní image. Podporované základní image pro Azure Functions najdete v [úložišti Azure Functions Base images](https://hub.docker.com/_/microsoft-azure-functions-base). 

V tomto kurzu se dozvíte, jak pomocí Azure Functions Core Tools vytvořit funkci ve vlastní imagi Linuxu. Tuto image publikujete do aplikace funkcí v Azure, která se vytvořila pomocí Azure CLI. Později aktualizujete funkci pro připojení k úložišti Azure Queue.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit aplikaci funkcí a soubor Dockerfile pomocí Core Tools
> * Sestavit vlastní image pomocí Dockeru
> * Publikovat vlastní image do registru kontejneru
> * Vytvořit účet služby Azure Storage
> * Vytvořte plán hostování Premium.
> * Nasadit aplikaci Function App z Docker Hubu
> * Přidat do aplikace Function App nastavení aplikace
> * Povolte průběžné nasazování.
> * Povolte připojení SSH ke kontejneru.
> * Přidejte výstupní vazbu úložiště fronty. 

Následující kroky se podporují na počítačích se systémem Mac, Windows a Linux. 

## <a name="prerequisites"></a>Požadavky

Před spuštěním této ukázky musíte mít následující:

* Nainstalujte [Azure Core Tools verze 2.x](functions-run-local.md#v2).

* Nainstalujte [rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli). Tento článek vyžaduje použití Azure CLI verze 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`.  
Můžete také použít [Azure Cloud Shell](https://shell.azure.com/bash).

* Aktivní předplatné Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="create-the-local-project"></a>Vytvořit místní projekt

Spuštěním následujícího příkazu z příkazového řádku vytvoříte projekt aplikace funkcí ve složce `MyFunctionProj` aktuálního místního adresáře. V případě projektu v Pythonu [musíte běžet ve virtuálním prostředí](functions-create-first-function-python.md#create-and-activate-a-virtual-environment).

```bash
func init MyFunctionProj --docker
```

Pokud zahrnete možnost `--docker`, pro projekt se vygeneruje soubor Dockerfile. Tento soubor slouží k vytvoření vlastního kontejneru,ve kterém se projekt spustí. Použitá základní image závisí na zvoleném jazyce modulu runtime pracovního procesu.  

Po zobrazení výzvy zvolte modul runtime pracovního procesu z následujících jazyků:

* `dotnet`: vytvoří projekt knihovny tříd .NET Core (. csproj).
* `node`: vytvoří projekt jazyka JavaScript.
* `python`: vytvoří projekt Pythonu.  

Pomocí následujícího příkazu přejděte do složky nového projektu `MyFunctionProj`.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-from-the-docker-file"></a>Sestavení ze souboru Docker

Prohlédněte si soubor _Dockerfile_ v kořenové složce projektu. Tento soubor popisuje prostředí potřebné pro spuštění aplikace Function App v Linuxu. Následující příklad souboru Dockerfile vytvoří kontejner, ve kterém se spouští aplikace funkcí v modulu runtime pracovního procesu JavaScriptu (Node.js): 

```Dockerfile
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> Úplný seznam podporovaných základních imagí pro Azure Functions najdete na [stránce Azure Functions Base image](https://hub.docker.com/_/microsoft-azure-functions-base).

### <a name="run-the-build-command"></a>Spuštění příkazu `build`

V kořenové složce spusťte příkaz [docker build](https://docs.docker.com/engine/reference/commandline/build/) a zadejte název `mydockerimage` a značku `v1.0.0`. Položku `<docker-id>` nahraďte ID vašeho účtu Docker Hubu. Tento příkaz sestaví image Dockeru pro kontejner.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Po dokončení příkazu můžete nový kontejner spustit místně.

### <a name="run-the-image-locally"></a>Spustit bitovou kopii místně
Ověřte si funkčnost sestavené image tak, že image Dockeru spustíte v místním kontejneru. Zadejte příkaz [docker run](https://docs.docker.com/engine/reference/commandline/run/) a předejte mu název a značku image. Nezapomeňte zadat porty pomocí argumentu `-p`.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Když už v místním kontejneru Dockeru běží vlastní image, ověřte správnou funkčnost aplikace funkcí a kontejneru tak, že přejdete na adresu <http://localhost:8080>.

![Spusťte aplikaci Function App lokálně.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

> [!NOTE]
> V tomto okamžiku se při pokusu o volání konkrétní funkce HTTP zobrazí chybová odpověď HTTP 401. Je to proto, že vaše funkce běží v místním kontejneru jako v Azure, což znamená, že se vyžaduje klíč funkce. Vzhledem k tomu, že kontejner ještě nebyl publikován do aplikace Function App, není k dispozici žádný funkční klíč. Později uvidíte, že pokud k publikování kontejneru použijete základní nástroje, zobrazí se vám funkční klíče. Chcete-li otestovat funkci spuštěnou v místním kontejneru, můžete změnit [autorizační klíč](functions-bindings-http-webhook.md#authorization-keys) na `anonymous`. 

Po ověření aplikace Function App v kontejneru můžete aplikaci zastavit. Teď můžete vlastní image odeslat do svého účtu Docker Hubu.

## <a name="push-to-docker-hub"></a>Vložit do Docker Hub

Registr je aplikace, která je hostitelem imagí a poskytuje image a služby kontejneru. Pokud chcete svou image sdílet, musíte ji odeslat do registru. Docker Hub je registr pro image Dockeru, který umožňuje hostovat vlastní veřejná nebo privátní úložiště.

Než budete moct odeslat image, musíte se přihlásit k Docker Hubu pomocí příkazu [docker login](https://docs.docker.com/engine/reference/commandline/login/). Položku `<docker-id>` nahraďte názvem svého účtu a po zobrazení výzvy zadejte do konzoly své heslo. Další možnosti hesla do Docker Hubu najdete v [dokumentaci k příkazu docker login](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id>
```

Zpráva o úspěšném přihlášení potvrzuje, že jste přihlášeni. Po přihlášení odešlete image do Docker Hubu pomocí příkazu [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Po úspěšném vložení můžete image použít jako zdroj nasazení nové aplikace Function App v Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-premium-plan"></a>Vytvořit plán Premium

Hostování Linux pro kontejnery vlastních funkcí se podporuje na [vyhrazených plánech (App Service)](functions-scale.md#app-service-plan) a [plánech Premium](functions-premium-plan.md#features). V tomto kurzu se používá plán Premium, který se může podle potřeby škálovat. Další informace o hostování najdete v [porovnání plánů hostování služby Azure Functions](functions-scale.md).

Následující příklad vytvoří plán Premium s názvem `myPremiumPlan` v cenové úrovni **elastické Premium 1** (`--sku EP1`) v oblasti Západní USA (`-location WestUS`) a v kontejneru Linux (`--is-linux`).

```azurecli-interactive
az functionapp plan create --resource-group myResourceGroup --name myPremiumPlan \
--location WestUS --number-of-workers 1 --sku EP1 --is-linux
```

## <a name="create-an-app-from-the-image"></a>Vytvoření aplikace z Image

Aplikace Function App spravuje spouštění vašich funkcí v plánu hostování. Aplikaci Function App vytvoříte z image z Docker Hubu pomocí příkazu [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

V následujícím příkazu nahraďte zástupný symbol `<app_name>` jedinečným názvem vaší aplikace funkcí a `<storage_name>` názvem účtu úložiště. Jako výchozí doména DNS pro příslušnou aplikaci Function App se použije `<app_name>`, a proto musí být název mezi všemi aplikacemi v Azure jedinečný. Stejně jako předtím má váš účet Dockeru název `<docker-id>`.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myPremiumPlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

Parametr _deployment-container-image-name_ určuje image hostovanou v Docker Hubu, která se má použít k vytvoření aplikace Function App. Pomocí příkazu [AZ functionapp config Container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) zobrazíte informace o imagi používané k nasazení. Pomocí příkazu [AZ functionapp config Container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) nasaďte z jiné image.

## <a name="configure-the-function-app"></a>Konfigurace aplikace Function App

Funkce potřebuje k připojení k výchozímu účtu úložiště připojovací řetězec. Když publikujete vlastní image na účet privátního kontejneru, měli byste tato nastavení aplikace nastavit jako proměnné prostředí v souboru Dockerfile pomocí [instrukcí ENV](https://docs.docker.com/engine/reference/builder/#env)nebo podobného.

V tomto případě je `<storage_name>` název účtu úložiště, který jste vytvořili. Připojovací řetězec zobrazíte pomocí příkazu [az storage account show-connection-string](/cli/azure/storage/account). Pomocí příkazu [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) přidejte tato nastavení aplikace do aplikace Function App.

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_name> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings AzureWebJobsStorage=$storageConnectionString
```

> [!NOTE]
> Pokud je váš kontejner privátní, museli byste nastavit i následující nastavení aplikace.  
> - DOCKER_REGISTRY_SERVER_USERNAME  
> - DOCKER_REGISTRY_SERVER_PASSWORD  
>
> Aby se tyto hodnoty vybraly, budete muset aplikaci Function App zastavit a pak ji spustit.

## <a name="verify-your-functions"></a>Ověření funkcí

<!-- we should replace this with a CLI or API-based approach, when we get something better than REST -->

Vytvořená funkce spuštěná protokolem HTTP vyžaduje při volání koncového bodu [klíč funkce](functions-bindings-http-webhook.md#authorization-keys) . V tuto chvíli nejjednodušší způsob, jak získat adresu URL vaší funkce, včetně klíče, pochází z [Azure Portal]. 

> [!TIP]
> Klíče funkcí můžete získat také pomocí [rozhraní API pro správu klíčů](https://github.com/Azure/azure-functions-host/wiki/Key-management-API), které vyžaduje, abyste získali [nosný token pro ověřování](/cli/azure/account#az-account-get-access-token).

Vyhledejte novou aplikaci Function App v [Azure Portal] tak, že do pole **Hledat** v horní části stránky zadáte název aplikace functions a vyberete prostředek **App Service** .

Vyberte funkci **MyHttpTrigger** , vyberte **</> získat adresu URL funkce** > **výchozí (klíč funkce)**  > **copy**.

![Kopírování adresy URL funkce z webu Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)

V této adrese URL je klíčovou funkcí parametrem `code` dotazu. 

> [!NOTE]  
> Vzhledem k tomu, že je vaše aplikace Function App nasazena jako kontejner, nemůžete na portálu provádět změny kódu funkce. Místo toho je nutné projekt aktualizovat v místním kontejneru a znovu ho publikovat do Azure.

Vložte adresu URL funkce do panelu Adresa vašeho prohlížeče. Na konec této adresy URL připojte hodnotu řetězce dotazu `&name=<yourname>` a stisknutím klávesy `Enter` na klávesnici požadavek proveďte. V prohlížeči by se měla zobrazit odpověď, kterou funkce vrátila.

Následující příklad ukazuje odpověď v prohlížeči:

![Odezva funkce v prohlížeči.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

Adresa URL požadavku obsahuje klíč, který je ve výchozím nastavení nezbytný pro přístup k funkci přes protokol HTTP. 

## <a name="enable-continuous-deployment"></a>Povolit průběžné nasazování

Jednou z výhod používání kontejnerů je podpora průběžného nasazování. Funkce vám umožní automaticky nasadit aktualizace, když se kontejner aktualizuje v registru. Pomocí příkazu [AZ functionapp Deployment Container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) povolte průběžné nasazování.

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

Tento příkaz vrátí adresu URL Webhooku nasazení po povolení průběžného nasazování. K vrácení této adresy URL můžete použít také příkaz [AZ functionapp Deployment Container show-CD-URL](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) . 

Zkopírujte adresu URL nasazení a vyhledejte úložiště Dockerhubu, klikněte na kartu **Webhooky** , zadejte **název** Webhooku Webhooku, vložte adresu URL do pole **Webhook URL**a pak zvolte symbol plus ( **+** ).

![Přidání Webhooku do úložiště Dockerhubu](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

Při nastavení Webhooku všechny aktualizace propojené image v Dockerhubu mají za následek stažení a instalaci nejnovější image do aplikace Function App.

## <a name="enable-ssh-connections"></a>Povolit připojení SSH

SSH umožňuje zabezpečenou komunikaci mezi kontejnerem a klientem. Když je povolený SSH, můžete se k kontejneru připojit pomocí nástrojů App Service Advanced Tools (Kudu). Aby se usnadnilo připojení ke kontejneru pomocí SSH, poskytují funkce základní image, která má již povolený SSH. 

### <a name="change-the-base-image"></a>Změna základní image

V souboru Dockerfile přidejte řetězec `-appservice` k základní imagi ve vaší `FROM` instrukci, která pro JavaScriptový projekt vypadá podobně jako následující.

```docker
FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
```

Rozdíly ve dvou základních bitových kopiích umožňují připojení SSH ke kontejneru. Tyto rozdíly jsou popsány v [tomto kurzu App Services](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections).

### <a name="rebuild-and-redeploy-the-image"></a>Opětovné sestavení a opětovné nasazení image

V kořenové složce spusťte znovu příkaz [Docker Build](https://docs.docker.com/engine/reference/commandline/build/) , stejně jako předtím nahraďte `<docker-id>` číslem účtu Docker Hub. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Nahrajte aktualizovanou bitovou kopii zpátky do Docker Hub.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Aktualizovaný obrázek se znovu nasadí do vaší aplikace Function App.

### <a name="connect-to-your-container-in-azure"></a>Připojení ke kontejneru v Azure

V prohlížeči přejděte na následující rozšířené nástroje (Kudu) `scm.` koncový bod pro kontejner Function App, nahraďte `<app_name>` názvem vaší aplikace Function App.

```
https://<app_name>.scm.azurewebsites.net/
```

Přihlaste se ke svému účtu Azure a pak vyberte kartu **SSH** a vytvořte připojení SSH k vašemu kontejneru.

Po navázání spojení spusťte příkaz `top` pro zobrazení aktuálně spuštěných procesů. 

![Horní příkaz pro Linux běžící v relaci SSH.](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-queue-storage"></a>Zapsat do fronty úložiště

Funkce vám umožní připojit služby Azure a další prostředky k funkcím bez nutnosti psát vlastní kód pro integraci. Tyto *vazby*, které představují vstupní i výstupní, jsou deklarovány v rámci definice funkce. Data z vazeb jsou k dispozici funkci jako parametry. *Trigger* je speciální typ vstupní vazby. I když má funkce pouze jednu Trigger, může mít více vstupních a výstupních vazeb. Další informace najdete v tématu [Azure Functions triggery a koncepty vazeb](functions-triggers-bindings.md).

V této části se dozvíte, jak integrovat funkci do fronty Azure Storage. Výstupní vazba, kterou přidáte do této funkce, zapisuje data z požadavku HTTP do zprávy ve frontě.

### <a name="download-the-function-app-settings"></a>Stažení nastavení Function App

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

### <a name="enable-extension-bundles"></a>Povolit sady rozšíření

Vzhledem k tomu, že používáte výstupní vazbu úložiště front, musíte mít nainstalované rozšíření úložiště vazeb před spuštěním projektu. 


# <a name="javascript--pythontabnodejspython"></a>[JavaScript/Python](#tab/nodejs+python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

S výjimkou aktivačních událostí protokolu HTTP a časovače jsou vazby implementovány jako balíčky rozšíření. Spusťte následující příkaz [dotnet přidat balíček](/dotnet/core/tools/dotnet-add-package) v okně terminálu a přidejte do projektu balíček rozšíření úložiště.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

> [!TIP]
> Při použití sady Visual Studio můžete k přidání tohoto balíčku použít také správce balíčků NuGet.

---

Nyní můžete přidat výstupní vazbu úložiště do projektu.

### <a name="add-an-output-binding"></a>Přidání výstupní vazby

V rámci funkcí vyžaduje každý typ vazby `direction`, `type`a jedinečné `name`, které mají být definovány v souboru Function. JSON. Způsob, jakým definujete tyto atributy, závisí na jazyku aplikace Function App.

# <a name="javascript--pythontabnodejspython"></a>[JavaScript/Python](#tab/nodejs+python)

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

---

### <a name="add-code-that-uses-the-output-binding"></a>Přidání kódu, který používá výstupní vazbu

Po definování vazby můžete použít `name` vazby k přístupu jako atributu v signatuře funkce. Pomocí výstupní vazby nemusíte pro ověřování používat kód Azure Storage SDK, získat odkaz na frontu nebo zapisovat data. Úlohy za běhu functions a Queue výstupní vazby jsou za vás.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

---

### <a name="update-the-hosted-container"></a>Aktualizace hostovaného kontejneru

V kořenové složce spusťte znovu příkaz [Docker Build](https://docs.docker.com/engine/reference/commandline/build/) a tentokrát aktualizujte verzi ve značce na `v1.0.2`. Stejně jako dřív nahraďte `<docker-id>` číslem účtu Docker Hub. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.2
```

Nahrajte aktualizovaný obrázek zpátky do úložiště.

```bash
docker push <docker-id>/mydockerimage:v1.0.2
```

### <a name="verify-the-updates-in-azure"></a>Ověření aktualizací v Azure

Chcete-li aktivovat funkci, použijte stejnou adresu URL jako předtím v prohlížeči. Měla by se zobrazit stejná odpověď. Tentokrát ale řetězec, který předáte jako parametr `name`, se zapíše do fronty úložiště `outqueue`.

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Další kroky

Teď, když jste úspěšně nasadili vlastní kontejner do aplikace Function App v Azure, si můžete přečíst další informace o následujících tématech:

+ [Funkce monitorování](functions-monitoring.md)
+ [Možnosti škálování a hostování](functions-scale.md)
+ [Hostování bez serveru založené na Kubernetes](functions-kubernetes-keda.md)

[Azure Portal]: https://portal.azure.com
