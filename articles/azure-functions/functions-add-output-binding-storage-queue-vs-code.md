---
title: Připojení Azure Functions k Azure Storage pomocí Visual Studio Code
description: Naučte se připojit Azure Functions ke frontě Azure Storage přidáním výstupní vazby do projektu Visual Studio Code.
ms.date: 02/07/2020
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-js
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: e280fddbe83da2a7ee89185046883f6c2c77167a
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739807"
---
# <a name="connect-azure-functions-to-azure-storage-using-visual-studio-code"></a>Připojení Azure Functions k Azure Storage pomocí Visual Studio Code

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

V tomto článku se dozvíte, jak pomocí Visual Studio Code připojit Azure Storage k funkci, kterou jste vytvořili v předchozím článku rychlý Start. Výstupní vazba, kterou do této funkce přidáte, zapisuje data z požadavku HTTP do zprávy ve frontě úložiště Azure Queue. 

Většina vazeb vyžaduje uložený připojovací řetězec, který funkce používá pro přístup k vázané službě. Pro snazší použití účtu úložiště, který jste vytvořili v aplikaci Function App. Připojení k tomuto účtu je již Uloženo v nastavení aplikace s názvem `AzureWebJobsStorage` .  

## <a name="configure-your-local-environment"></a>Konfigurace místního prostředí

Než začnete tento článek, musíte splnit následující požadavky:

* Nainstalujte [Azure Storage rozšíření pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).

* Nainstalujte [Průzkumník služby Azure Storage](https://storageexplorer.com/). Průzkumník služby Storage je nástroj, který budete používat k prohlédnutí zpráv ve frontě generovaných výstupní vazbou. Průzkumník služby Storage se podporují v operačních systémech macOS, Windows a Linux.

::: zone pivot="programming-language-csharp"
* Instalace [.NET Core CLIch nástrojů](/dotnet/core/tools/?tabs=netcore2x).
::: zone-end

::: zone pivot="programming-language-csharp"  
* Proveďte kroky v [části 1 rychlého startu Visual Studio Code](create-first-function-vs-code-csharp.md). 
::: zone-end  
::: zone pivot="programming-language-javascript"  
* Proveďte kroky v [části 1 rychlého startu Visual Studio Code](create-first-function-vs-code-node.md). 
::: zone-end   
::: zone pivot="programming-language-java"  
* Proveďte kroky v [části 1 rychlého startu Visual Studio Code](create-first-function-vs-code-java.md). 
::: zone-end   
::: zone pivot="programming-language-typescript"  
* Proveďte kroky v [části 1 rychlého startu Visual Studio Code](create-first-function-vs-code-typescript.md). 
::: zone-end   
::: zone pivot="programming-language-python"  
* Proveďte kroky v [části 1 rychlého startu Visual Studio Code](create-first-function-vs-code-python.md). 
::: zone-end   
::: zone pivot="programming-language-powershell"  
* Proveďte kroky v [části 1 rychlého startu Visual Studio Code](create-first-function-vs-code-powershell.md). 
::: zone-end   

V tomto článku se předpokládá, že jste už přihlášení k předplatnému Azure z Visual Studio Code. Přihlášení můžete spustit `Azure: Sign In` z palety příkazů. 

## <a name="download-the-function-app-settings"></a>Stažení nastavení Function App

V [předchozím článku rychlý Start](./create-first-function-vs-code-csharp.md)jste vytvořili aplikaci funkcí v Azure spolu s požadovaným účtem úložiště. Připojovací řetězec pro tento účet je bezpečně uložený v nastavení aplikace v Azure. V tomto článku napíšete zprávy do fronty úložiště ve stejném účtu. Pokud se chcete připojit k účtu úložiště, když se funkce spouští místně, musíte si stáhnout nastavení aplikace do local.settings.jsv souboru. 

1. Stisknutím klávesy F1 otevřete paletu příkazů a pak vyhledejte a spusťte příkaz `Azure Functions: Download Remote Settings....` . 

1. Vyberte aplikaci funkcí, kterou jste vytvořili v předchozím článku. Vyberte možnost **Ano pro vše** , pokud chcete přepsat stávající místní nastavení. 

    > [!IMPORTANT]  
    > Vzhledem k tomu, že obsahuje tajné kódy, local.settings.jsv souboru nikdy nezveřejňují a jsou vyloučeny ze správy zdrojového kódu.

1. Zkopírujte hodnotu `AzureWebJobsStorage` , což je klíč pro hodnotu připojovacího řetězce účtu úložiště. Pomocí tohoto připojení ověříte, zda výstupní vazba funguje podle očekávání.

## <a name="register-binding-extensions"></a>Registrace rozšíření vazeb

Vzhledem k tomu, že používáte výstupní vazbu úložiště front, musíte mít nainstalované rozšíření úložiště vazeb před spuštěním projektu. 

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-java"

Projekt byl nakonfigurován tak, aby používal [sady rozšíření](functions-bindings-register.md#extension-bundles), které automaticky instalují předdefinované sady přípon balíčků. 

Použití sad rozšíření je povoleno v host.jsv souboru v kořenovém adresáři projektu, který se zobrazí takto:

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

::: zone pivot="programming-language-csharp"

S výjimkou aktivačních událostí protokolu HTTP a časovače jsou vazby implementovány jako balíčky rozšíření. Spusťte následující příkaz [dotnet přidat balíček](/dotnet/core/tools/dotnet-add-package) v okně terminálu a přidejte do projektu balíček rozšíření úložiště.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage
```

::: zone-end

Nyní můžete přidat výstupní vazbu úložiště do projektu.

## <a name="add-an-output-binding"></a>Přidání výstupní vazby

V Functions každý typ vazby vyžaduje `direction` , `type` , a jedinečný, `name` který má být definován v function.jsv souboru. Způsob, jakým definujete tyto atributy, závisí na jazyku aplikace Function App.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-java"

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [functions-add-output-binding-java](../../includes/functions-add-output-binding-java.md)]

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Přidání kódu, který používá výstupní vazbu

Po definování vazby můžete použít `name` vazbu na k přístupu jako atributu v signatuře funkce. Pomocí výstupní vazby nemusíte pro ověřování používat kód Azure Storage SDK, získat odkaz na frontu nebo zapisovat data. Úlohy za běhu functions a Queue výstupní vazby jsou za vás.

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

::: zone-end

::: zone pivot="programming-language-csharp"  

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

::: zone-end  

::: zone pivot="programming-language-java"  

[!INCLUDE [functions-add-storage-binding-java-code](../../includes/functions-add-storage-binding-java-code.md)]

## <a name="update-the-test-set"></a>Aktualizace sady testů

[!INCLUDE [functions-add-output-binding-java-test](../../includes/functions-add-output-binding-java-test.md)]

::: zone-end  

<!--- Local testing section --->

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

V účtu úložiště se vytvoří nová fronta s názvem **front** . modul runtime Functions při prvním použití výstupní vazby. Pomocí Průzkumník služby Storage ověříte, že se vytvořila fronta spolu s novou zprávou.

::: zone pivot="programming-language-java"  

## <a name="update-the-tests"></a>Aktualizace testů

[!INCLUDE [functions-add-output-binding-java-test](../../includes/functions-add-output-binding-java-test.md)]

::: zone-end

### <a name="connect-storage-explorer-to-your-account"></a>Propojení Průzkumníka služby Storage s vaším účtem

Pokud jste už Průzkumník služby Azure Storage nainstalovali a připojili ho k účtu Azure, přeskočte tuto část.

1. Spusťte nástroj [Průzkumník služby Azure Storage], vyberte ikonu připojit na levé straně a vyberte **Přidat účet**.

    ![Přidat účet Azure do Průzkumník služby Microsoft Azure Storage](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. V dialogovém okně **připojit** zvolte **Přidat účet Azure**, zvolte **prostředí Azure** a pak vyberte **Přihlásit se...**. 

    ![Přihlášení k účtu Azure](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Po úspěšném přihlášení ke svému účtu uvidíte všechna předplatná Azure přidružená k vašemu účtu.

### <a name="examine-the-output-queue"></a>Prozkoumání výstupní fronty

1. V Visual Studio Code stiskněte klávesu F1 a otevřete paletu příkazů, vyhledejte a spusťte příkaz `Azure Storage: Open in Storage Explorer` a zvolte název účtu úložiště. Váš účet úložiště se otevře v Průzkumník služby Azure Storage.  

1. Rozbalte uzel **Fronty** a potom vyberte frontu s názvem **outqueue**. 

   Tato fronta obsahuje zprávu, kterou vytvořila výstupní vazba fronty při spuštění funkce aktivované protokolem HTTP. Pokud jste tuto funkci volali s výchozí hodnotou `name` (*Azure*), zpráva fronty je *Name passed to the function: Azure*.

    ![Zpráva fronty zobrazená v Průzkumník služby Azure Storage](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Znovu spusťte funkci, odešlete další žádost a ve frontě se zobrazí nová zpráva.  

Teď je čas na opětovné publikování aktualizované aplikace Function App do Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>Opětovné nasazení a ověření aktualizované aplikace

1. V Visual Studio Code stisknutím klávesy F1 otevřete paletu příkazů. V paletě příkazů vyhledejte a vyberte `Azure Functions: Deploy to function app...` .

1. Vyberte aplikaci funkcí, kterou jste vytvořili v prvním článku. Vzhledem k tomu, že projekt znovu nasazujete do stejné aplikace, vyberte **nasadit** a zastavte tak upozornění týkající se přepsání souborů.

1. Po dokončení nasazení můžete znovu použít kudrlinkou nebo prohlížeč k otestování znovu nasazené funkce. Stejně jako dřív přidejte řetězec dotazu `&name=<yourname>` k adrese URL, jako v následujícím příkladu:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. Opětovným [zobrazením zprávy ve frontě úložiště](#examine-the-output-queue) ověřte, zda výstupní vazba znovu generuje novou zprávu ve frontě.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V Azure se *prostředky* odkazují na aplikace Function App, funkce, účty úložiště a tak dále. Jsou seskupené do *skupin prostředků* a odstraněním skupiny můžete všechno odstranit ze skupiny.

Vytvořili jste prostředky k dokončení těchto rychlých startů. Tyto prostředky se vám mohou fakturovat, a to v závislosti na [stavu účtu](https://azure.microsoft.com/account/) a [cenách služeb](https://azure.microsoft.com/pricing/). Pokud prostředky už nepotřebujete, můžete k jejich odstranění použít tento postup:

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

## <a name="next-steps"></a>Další kroky

Aktualizovali jste funkci aktivovanou protokolem HTTP, která zapisuje data do fronty úložiště. Nyní se můžete dozvědět víc o vývoji funkcí pomocí Visual Studio Code:

+ [Vývoj Azure Functions pomocí Visual Studio Code](functions-develop-vs-code.md)

+ [Azure Functions triggery a vazby](functions-triggers-bindings.md).
::: zone pivot="programming-language-csharp"  
+ [Příklady kompletních projektů funkcí v jazyce C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Referenční informace pro vývojáře v jazyce C# Azure Functions](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Příklady kompletních projektů funkcí v JavaScriptu](/samples/browse/?products=azure-functions&languages=javascript).

+ [Azure Functions příručka pro vývojáře JavaScriptu](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-java"  
+ [Příklady kompletních projektů funkcí v jazyce Java](/samples/browse/?products=azure-functions&languages=java).

+ [Azure Functions příručka pro vývojáře Java](functions-reference-java.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Příklady kompletních projektů funkcí v TypeScript](/samples/browse/?products=azure-functions&languages=typescript).

+ [Azure Functions příručka pro vývojáře TypeScript](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Příklady kompletních projektů funkcí v Pythonu](/samples/browse/?products=azure-functions&languages=python)

+ [Příručka pro vývojáře Azure Functions Pythonu](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Příklady kompletních projektů funkcí v prostředí PowerShell](/samples/browse/?products=azure-functions&languages=azurepowershell).

+ [Azure Functions příručka pro vývojáře PowerShellu](functions-reference-powershell.md) 
::: zone-end