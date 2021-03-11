---
title: Vývoj a publikování funkcí rozhraní .NET 5 pomocí Azure Functions
description: Naučte se vytvářet a ladit funkce C# pomocí .NET 5,0 a pak nasadit místní projekt na hostování bez serveru v Azure Functions.
ms.date: 03/03/2021
ms.topic: how-to
zone_pivot_groups: development-environment-functions
ms.openlocfilehash: 70eacc5ec7f6adb65ba6e01c55acc6c6e3075ca9
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583894"
---
# <a name="develop-and-publish-net-5-function-using-azure-functions"></a>Vývoj a publikování funkce .NET 5 pomocí Azure Functions 

V tomto článku se dozvíte, jak pracovat s funkcemi jazyka C# pomocí rozhraní .NET 5,0, které se spouští mimo proces z modulu runtime Azure Functions. Naučíte se, jak vytvářet, ladit místně a publikovat tyto funkce izolovaného procesu .NET do Azure. V Azure se tyto funkce spouštějí v izolovaném procesu, který podporuje .NET 5,0. Další informace najdete v tématu [Průvodce pro spouštění funkcí v .net 5,0 v Azure](dotnet-isolated-process-guide.md).

Pokud nepotřebujete podporovat .NET 5,0 nebo spouštět funkce mimo proces, můžete místo toho [vytvořit funkci knihovny tříd jazyka C#](functions-create-your-first-function-visual-studio.md).

>[!NOTE]
>Vývoj funkcí izolovaného procesu .NET v Azure Portal není aktuálně podporován. K vytvoření aplikace Function App v Azure, která podporuje spouštění aplikací .NET 5,0, je nutné použít buď Azure CLI, nebo publikování Visual Studio Code.   

## <a name="prerequisites"></a>Požadavky

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [.NET SDK 5.0](https://www.microsoft.com/net/download)

+ [Azure Functions Core Tools](functions-run-local.md#v2) verze 3.0.3381 nebo novější verze.

+ [Azure CLI](/cli/azure/install-azure-cli) verze 2,20 nebo novější verze.  
::: zone pivot="development-environment-vscode"
+ [Visual Studio Code](https://code.visualstudio.com/) na jedné z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms)  

+ [Rozšíření pro jazyk C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) pro Visual Studio Code  

+ [Rozšíření Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pro Visual Studio Code, verze 1.3.0 nebo novější.
::: zone-end
::: zone pivot="development-environment-vs"
+ [Visual Studio 2019](https://azure.microsoft.com/downloads/), včetně úlohy **vývoje Azure** .  
Šablony projektů izolované funkce .NET a publikování nejsou aktuálně k dispozici v aplikaci Visual Studio.
::: zone-end

## <a name="create-a-local-function-project"></a>Vytvoření projektu místní funkce

V Azure Functions je projekt funkce kontejnerem pro jednu nebo více jednotlivých funkcí, které každý reaguje na konkrétní Trigger. Všechny funkce v projektu sdílejí stejné místní konfigurace a konfigurace hostování. V této části vytvoříte projekt funkce, který obsahuje jedinou funkci.

::: zone pivot="development-environment-vs"

>[!NOTE]  
> V tuto chvíli nejsou k dispozici žádné šablony projektů sady Visual Studio, které podporují vytváření projektů izolované funkce .NET. V tomto článku se dozvíte, jak pomocí základních nástrojů vytvořit projekt C#, který můžete spustit místně a ladit v aplikaci Visual Studio.  

::: zone-end

::: zone pivot="development-environment-vscode"  
1. Zvolte ikonu Azure na panelu Aktivita a pak v oblasti **Azure: Funkce** vyberte ikonu **Vytvořit nový projekt**.

    ![Volba možnosti Vytvořit nový projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Zvolte umístění adresáře pro váš pracovní prostor projektu a zvolte **Vybrat**.

    > [!NOTE]
    > Tyto kroky jsou navržené tak, aby se dokončily mimo pracovní prostor. V tomto případě nevybírejte složku projektu, která je součástí pracovního prostoru.

1. Podle pokynů na obrazovce zadejte tyto informace:

    + **Vyberte jazyk pro váš projekt funkce:** Zvolte `C#`.

    + **Vyberte modul runtime .NET**: zvolte `.NET 5 isolated` .

    + **Vyberte šablonu pro první funkci projektu:** Zvolte `HTTP trigger`.

    + **Zadejte název funkce:** Zadejte příkaz `HttpExample`.

    + **Zadejte obor názvů:** Zadejte příkaz `My.Functions`.

    + **Úroveň autorizace**: vyberte `Anonymous` , který umožňuje komukoli zavolat koncový bod funkce. Další informace o úrovni autorizace najdete v tématu [autorizační klíče](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Vyberte způsob, jakým chcete projekt otevírat:** Zvolte `Add to workspace`.

1. Pomocí těchto informací Visual Studio Code generuje Azure Functions projekt pomocí triggeru protokolu HTTP. Můžete zobrazit soubory místních projektů v Průzkumníkovi. Další informace o souborech, které jsou vytvořeny, najdete v tématu [vygenerované soubory projektu](functions-develop-vs-code.md#generated-project-files).
::: zone-end  
::: zone pivot="development-environment-cli,development-environment-vs"  

1. Spusťte `func init` příkaz následujícím způsobem a vytvořte projekt Functions ve složce s názvem *LocalFunctionProj*:  

    ```console
    func init LocalFunctionProj --worker-runtime dotnetisolated
    ```
    
    Určení `dotnetisolated` vytvoří projekt, který běží v .net 5,0.


1. Přejděte do složky projektu:

    ```console
    cd LocalFunctionProj
    ```

    Tato složka obsahuje různé soubory pro projekt, včetně [local.settings.js](functions-run-local.md#local-settings-file) a [host.jsna](functions-host-json.md) konfiguracích souborů. Vzhledem k tomu, že *local.settings.json* může obsahovat tajné kódy stažené z Azure, soubor je ve výchozím nastavení vyloučený ze správy zdrojového kódu v souboru *. gitignore* .

1. Do projektu přidejte funkci pomocí následujícího příkazu, kde `--name` argument je jedinečný název vaší funkce (HttpExample) a `--template` argument určuje aktivační událost funkce (http).

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 

    `func new` Vytvoří soubor kódu HttpExample.cs.
::: zone-end  

::: zone pivot="development-environment-vscode"  

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end  
::: zone pivot="development-environment-cli" 

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone-end

::: zone pivot="development-environment-vs"

## <a name="run-the-function-locally"></a>Místní spuštění funkce

V tomto okamžiku můžete spustit `func start` příkaz z kořenové složky projektu pro zkompilování a spuštění projektu izolovaných funkcí C#. V současné době, pokud chcete ladit kód mimoprocesové funkce v aplikaci Visual Studio, je nutné ručně připojit ladicí program k běžícímu procesu runtime Functions pomocí následujících kroků:  

1. Otevřete soubor projektu (. csproj) v aplikaci Visual Studio. Můžete zkontrolovat a upravit kód projektu a nastavit všechny požadované body přerušení v kódu. 

1. Z kořenové složky projektu použijte následující příkaz z terminálu nebo příkazového řádku pro spuštění hostitele modulu runtime:

    ```console
    func start --dotnet-isolated-debug
    ```

    `--dotnet-isolated-debug`Možnost instruuje proces, aby čekal na připojení ladicího programu, než bude pokračovat. Ke konci výstupu byste měli vidět něco podobného jako následující řádky: 
    
    <pre>
    ...
    
    Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample

    For detailed output, run func with --verbose flag.
    [2021-03-09T08:41:41.904Z] Azure Functions .NET Worker (PID: 81720) initialized in debug mode. Waiting for debugger to attach...
    ...
    
    </pre> 

    `PID: XXXXXX`Určuje ID procesu (PID) procesu dotnet.exe, který je hostitelem spuštěných funkcí.
 
1. Ve výstupu Azure Functions modulu runtime si poznamenejte ID procesu hostitelského procesu, ke kterému budete připojovat ladicí program. Také si poznamenejte adresu URL místní funkce.

1. V nabídce **ladění** v aplikaci Visual Studio vyberte možnost **připojit k procesu...**, vyhledejte proces dotnet.exe, který odpovídá ID procesu, a vyberte **připojit**. 
    
    :::image type="content" source="media/dotnet-isolated-process-developer-howtos/attach-to-process.png" alt-text="Připojit ladicí program k procesu hostitele Functions":::    

    Pomocí připojeného ladicího programu můžete kód funkce ladit jako normální.

1. Do adresního řádku prohlížeče zadejte adresu URL místní funkce, která vypadá následovně, a spusťte požadavek. 

    <http://localhost:7071/api/HttpExample>

    Měl by se zobrazit výstup trasování z požadavku zapsaného do běžícího terminálu. Spuštění kódu se zastaví na všech bodech přerušení, které jste nastavili v kódu funkce.

1. Až budete hotovi, přejděte do terminálu a stisknutím kombinace kláves CTRL + C Zastavte proces hostitele.
 
Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.

> [!NOTE]  
> Publikování sady Visual Studio není aktuálně k dispozici pro aplikace izolovaného procesu .NET. Po dokončení vývoje projektu v aplikaci Visual Studio je nutné pomocí rozhraní příkazového řádku Azure vytvořit vzdálené prostředky Azure. Potom můžete znovu použít Azure Functions Core Tools z příkazového řádku a publikovat tak projekt do Azure.
::: zone-end

::: zone pivot="development-environment-cli,development-environment-vs" 
## <a name="create-supporting-azure-resources-for-your-function"></a>Vytvoření podpory prostředků Azure pro vaši funkci

Než budete moct nasadit kód funkce do Azure, musíte vytvořit tři prostředky:

- [Skupina prostředků](../azure-resource-manager/management/overview.md), což je logický kontejner pro související prostředky.
- [Účet úložiště](../storage/common/storage-account-create.md), který se používá k údržbě stavu a dalších informací o vašich funkcích.
- Aplikace Function App, která poskytuje prostředí pro spuštění kódu vaší funkce. Aplikace Function App se mapuje na váš místní projekt funkce a umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků.

Tyto položky vytvoříte pomocí následujících příkazů.

1. Pokud jste to ještě neudělali, přihlaste se k Azure:

    ```azurecli
    az login
    ```

    Příkaz [AZ Login](/cli/azure/reference-index#az_login) vás přihlásí k účtu Azure.

1. Vytvořte skupinu prostředků s názvem `AzureFunctionsQuickstart-rg` v `westeurope` oblasti:

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Pomocí příkazu [AZ Group Create](/cli/azure/group#az_group_create) se vytvoří skupina prostředků. Obecně vytvoříte skupinu prostředků a prostředky v oblasti blízko vás pomocí dostupné oblasti vrácené z `az account list-locations` příkazu.

1. Vytvořte účet úložiště pro obecné účely ve vaší skupině prostředků a oblasti:

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Příkaz [AZ Storage Account Create](/cli/azure/storage/account#az_storage_account_create) vytvoří účet úložiště. 

    V předchozím příkladu nahraďte `<STORAGE_NAME>` názvem, který je vhodný pro vás a jedinečný v Azure Storage. Názvy musí obsahovat tři až 24 znaků a jenom malá písmena. `Standard_LRS` Určuje účet pro obecné účely, který je [podporován funkcemi](storage-considerations.md#storage-account-requirements).

1. Vytvoření aplikace Function App v Azure:
   
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet-isolated --runtime-version 5.0 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```

    Příkazem [AZ functionapp Create](/cli/azure/functionapp#az_functionapp_create) se vytvoří aplikace Function App v Azure. 
    
    V předchozím příkladu nahraďte `<STORAGE_NAME>` názvem účtu, který jste použili v předchozím kroku, a nahraďte `<APP_NAME>` globálně jedinečným názvem vhodným pro vás. `<APP_NAME>` je také výchozí doména DNS pro aplikaci funkcí. 
    
    Tento příkaz vytvoří aplikaci funkcí, na které běží .NET 5,0 v [plánu Azure Functions spotřeby](consumption-plan.md). Tento plán by měl být bezplatný pro množství využití, které jste v tomto článku zaznamenali. Příkaz rovněž zřídí přidruženou instanci služby Azure Application Insights ve stejné skupině prostředků. Pomocí této instance můžete monitorovat aplikaci Function App a zobrazovat protokoly. Další informace najdete v tématu [monitorování Azure Functions](functions-monitoring.md). Instance nenese žádné náklady, dokud ji neaktivujete.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

::: zone-end

::: zone pivot="development-environment-vscode"

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>Publikování projektu do Azure

V této části vytvoříte aplikaci funkcí a související prostředky v předplatném Azure a pak svůj kód nasadíte.

> [!IMPORTANT]
> Publikování do existující aplikace funkcí přepíše obsah této aplikace v Azure.


1. Zvolte ikonu Azure na řádku aktivity a potom v oblasti **Azure: funkce** zvolte tlačítko **nasadit do aplikace Function App...** .

    ![Publikování projektu do Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Podle pokynů na obrazovce zadejte tyto informace:

    - **Vyberte složku**: Zvolte složku z pracovního prostoru nebo přejděte k nějakému, který obsahuje vaši aplikaci Function App. Tato výzva se nezobrazí, když už máte spuštěnou platnou aplikaci Function App.

    - **Vyberte předplatné**: zvolte předplatné, které chcete použít. Tato výzva se nezobrazí, když máte jenom jedno předplatné.

    - **Výběr aplikace funkcí v Azure:** Zvolte `- Create new Function App`. (Nevybírejte `Advanced` možnost, která není pokrytá v tomto článku.)
      
    - **Zadejte globálně jedinečný název aplikace Function App**: zadejte název, který je platný v cestě URL. Název, který zadáte, bude ověřený, abyste se ujistili, že je v Azure Functions jedinečný.
    
    - **Vyberte zásobník modulu runtime**: zvolte `.NET 5 (non-LTS)` . 
    
    - **Vyberte umístění pro nové prostředky**: pro lepší výkon zvolte [oblast](https://azure.microsoft.com/regions/) poblíž. 
    
    V oznamovací oblasti uvidíte stav jednotlivých prostředků, které se vytváří v Azure.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Oznámení o vytváření prostředků Azure":::
    
1.  Po dokončení se ve vašem předplatném vytvoří následující prostředky Azure s použitím názvů na základě názvu vaší aplikace Function App:
    
    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    Po vytvoření aplikace funkcí a použití balíčku nasazení se zobrazí oznámení. 

    [!INCLUDE [functions-vs-code-create-tip](../../includes/functions-vs-code-create-tip.md)]

4. Pokud chcete zobrazit výsledky vytvoření a nasazení, včetně prostředků Azure, které jste vytvořili, vyberte **Zobrazit výstup** v tomto oznámení. Pokud jste oznámení nezobrazili, vyberte ikonu zvonku v pravém dolním rohu, abyste ji viděli znovu.

    ![Vytvořit kompletní oznámení](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

::: zone-end

::: zone pivot="development-environment-cli"  
[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]  
::: zone-end  

::: zone pivot="development-environment-vscode"  
[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]  
::: zone-end  

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vytvořili jste prostředky pro dokončení tohoto článku. Tyto prostředky se vám mohou fakturovat, a to v závislosti na [stavu účtu](https://azure.microsoft.com/account/) a [cenách služeb](https://azure.microsoft.com/pricing/). 

::: zone pivot="development-environment-cli"  
Pomocí následujícího příkazu odstraňte skupinu prostředků a všechny její obsažené prostředky, abyste se vyhnuli vzniku dalších nákladů.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```
::: zone-end  

::: zone pivot="development-environment-vscode"  
Pomocí následujících kroků odstraňte aplikaci Function App a její související prostředky, abyste se vyhnuli dalším nákladům.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]  
::: zone-end  
::: zone pivot="development-environment-vs"   
Pomocí následujících kroků odstraňte aplikaci Function App a její související prostředky, abyste se vyhnuli dalším nákladům.

1. V Průzkumníku cloudu rozbalte své předplatné > **App Services**, klikněte pravým tlačítkem myši na svou aplikaci Function App a vyberte **otevřít na portálu**. 

1. Na stránce Function App vyberte kartu **Přehled** a potom vyberte odkaz v části **Skupina prostředků**.

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="Vyberte skupinu prostředků, kterou chcete odstranit ze stránky Function App.":::

2. Na stránce **Skupina prostředků** zkontrolujte seznam zahrnutých prostředků a ověřte, že se jedná o ty, které chcete odstranit.
 
3. Vyberte **Odstranit skupinu prostředků** a postupujte podle pokynů.

   Odstranění může trvat několik minut. Po jeho dokončení se na několik sekund zobrazí oznámení. K zobrazení tohoto oznámení můžete také vybrat ikonu zvonku v horní části stránky.
::: zone-end

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o izolovaných funkcích .NET](dotnet-isolated-process-guide.md)

