---
title: Připojení funkcí Azure k úložišti Azure pomocí kódu Visual Studia
description: Zjistěte, jak připojit funkce Azure k frontě úložiště Azure přidáním výstupní vazby do projektu visual studio code.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: c32f98fc1b3de98592f8e7ceb43c17aa8a9049f7
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673455"
---
# <a name="connect-azure-functions-to-azure-storage-using-visual-studio-code"></a>Připojení funkcí Azure k úložišti Azure pomocí kódu Visual Studia

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Tento článek ukazuje, jak pomocí kódu Visual Studia připojit funkci, kterou jste vytvořili v [předchozím článku rychlého startu,](functions-create-first-function-vs-code.md) k Úložišti Azure. Výstupní vazba, kterou přidáte do této funkce, zapisuje data z požadavku HTTP do zprávy ve frontě úložiště fronty Azure. 

Většina vazeb vyžaduje uložený připojovací řetězec, který funkce používá pro přístup k vázané službě. Chcete-li to usnadnit, použijte účet úložiště, který jste vytvořili pomocí aplikace funkce. Připojení k tomuto účtu je již `AzureWebJobsStorage`uloženo v nastavení aplikace s názvem .  

## <a name="configure-your-local-environment"></a>Konfigurace místního prostředí

Před zahájením tohoto článku musíte splňovat následující požadavky:

* Nainstalujte [rozšíření úložiště Azure pro kód Visual Studia](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).

* Nainstalujte [Průzkumníka úložišť Azure](https://storageexplorer.com/). Průzkumník úložiště je nástroj, který použijete ke kontrole zpráv fronty generovaných výstupní vazbou. Průzkumník úložiště je podporovaný v operačních systémech macOS, Windows a Linux.

::: zone pivot="programming-language-csharp"
* Nainstalujte [nástroje rozhraní .NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x).
::: zone-end

* Proveďte kroky v [části 1 rychlého startu kódu sady Visual Studio](functions-create-first-function-vs-code.md). 

Tento článek předpokládá, že jste již přihlášeni k předplatnému Azure z visual studio kódu. Můžete se přihlásit `Azure: Sign In` spuštěním z palety příkazů. 

## <a name="download-the-function-app-settings"></a>Stažení nastavení aplikace pro funkce

V [předchozím článku rychlého startu](functions-create-first-function-vs-code.md)jste v Azure vytvořili aplikaci funkcí spolu s požadovaným účtem úložiště. Připojovací řetězec pro tento účet se bezpečně uchovává v nastavení aplikace v Azure. V tomto článku zapisujete zprávy do fronty úložiště ve stejném účtu. Chcete-li se připojit k účtu úložiště při místním spuštění funkce, je nutné stáhnout nastavení aplikace do souboru local.settings.json. 

1. Stisknutím klávesy F1 otevřete paletu příkazů a `Azure Functions: Download Remote Settings....`vyhledejte a spusťte příkaz . 

1. Zvolte aplikaci funkcí, kterou jste vytvořili v předchozím článku. Chcete-li přepsat stávající místní nastavení, vyberte možnost Ano pro **všechny.** 

    > [!IMPORTANT]  
    > Vzhledem k tomu, že obsahuje tajné klíče, soubor local.settings.json nikdy publikuje a je vyloučen ze správy zdrojového kódu.

1. Zkopírujte `AzureWebJobsStorage`hodnotu , která je klíčem pro hodnotu připojovacího řetězce účtu úložiště. Toto připojení slouží k ověření, že výstupní vazba funguje podle očekávání.

## <a name="register-binding-extensions"></a>Registrace rozšíření vazeb

Vzhledem k tomu, že používáte výstupové vazby úložiště fronty, musíte mít před spuštěním projektu nainstalováno rozšíření vazeb úložiště. 

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-java"

Projekt byl nakonfigurován tak, aby používal [balíčky rozšíření](functions-bindings-register.md#extension-bundles), které automaticky nainstalují předdefinovanou sadu balíčků rozšíření. 

Rozšíření svazky je povolena v souboru host.json v kořenovém adresáři projektu, který vypadá takto:

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

::: zone pivot="programming-language-csharp"

S výjimkou aktivačních událostí protokolu HTTP a časovače jsou vazby implementovány jako balíčky rozšíření. Spusťte následující příkaz [dotnet add package](/dotnet/core/tools/dotnet-add-package) v okně Terminálu a přidejte balíček rozšíření úložiště do projektu.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

::: zone-end

Nyní můžete přidat vazby výstupu úložiště do projektu.

## <a name="add-an-output-binding"></a>Přidání výstupní vazby

Ve funkcích vyžaduje každý `direction`typ `type`vazby `name` a , a jedinečný, který má být definován v souboru function.json. Způsob definování těchto atributů závisí na jazyku aplikace funkce.

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

Po definování vazby můžete použít `name` vazbu pro přístup k ní jako atribut v podpisu funkce. Pomocí výstupní vazby není potřeba použít kód Sady Azure Storage SDK pro ověřování, získávání odkazů na frontu nebo zápis dat. Funkce runtime a fronty výstupní vazba provést tyto úkoly za vás.

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

[!INCLUDE [functions-add-output-binding-java-test](../../includes/functions-add-output-binding-java-test.md)]

::: zone-end  

<!--- Local testing section --->

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Nová fronta s názvem **outqueue** je vytvořena ve vašem účtu úložiště při funkci runtime funkce při prvním použití výstupní vazby. Pomocí Průzkumníka úložiště ověříte, že fronta byla vytvořena spolu s novou zprávou.

::: zone pivot="programming-language-java"  

[!INCLUDE [functions-add-output-binding-java-test](../../includes/functions-add-output-binding-java-test.md)]

::: zone-end

### <a name="connect-storage-explorer-to-your-account"></a>Propojení Průzkumníka služby Storage s vaším účtem

Tuto část přeskočte, pokud jste už nainstalovali Azure Storage Explorer a připojili ho ke svému účtu Azure.

1. Spusťte nástroj [Průzkumník advité úložiště], vyberte ikonu připojení vlevo a vyberte **Přidat účet**.

    ![Přidání účtu Azure do Průzkumníka úložiště Microsoft Azure](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. V dialogovém okně **Připojit** zvolte **Přidat účet Azure**, zvolte prostředí **Azure**a vyberte **Přihlásit se...**. 

    ![Přihlášení k účtu Azure](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Po úspěšném přihlášení ke svému účtu se zobrazí všechna předplatná Azure přidružená k vašemu účtu.

### <a name="examine-the-output-queue"></a>Prozkoumání výstupní fronty

1. V kódu Visual Studia otevřete stisknutím klávesy F1 paletu příkazů, vyhledejte a spusťte příkaz `Azure Storage: Open in Storage Explorer` a zvolte název účtu úložiště. Váš účet úložiště se otevře v Průzkumníku úložiště Azure.  

1. Rozbalte uzel **Fronty** a potom vyberte frontu s názvem **outqueue**. 

   Tato fronta obsahuje zprávu, kterou vytvořila výstupní vazba fronty při spuštění funkce aktivované protokolem HTTP. Pokud jste tuto funkci volali s výchozí hodnotou `name` (*Azure*), zpráva fronty je *Name passed to the function: Azure*.

    ![Zpráva fronty zobrazená v Průzkumníku úložiště Azure](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Spusťte funkci znovu, odešlete další požadavek a ve frontě se zobrazí nová zpráva.  

Teď je čas znovu publikovat aktualizovanou aplikaci funkcí do Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>Znovu nasadit a ověřit aktualizovanou aplikaci

1. V kódu sady Visual Studio otevřete paletu příkazů stisknutím klávesy F1. V paletě příkazů vyhledejte `Azure Functions: Deploy to function app...`a vyberte .

1. Zvolte aplikaci funkcí, kterou jste vytvořili v prvním článku. Vzhledem k tomu, že projekt znovu nasazujete do stejné aplikace, vyberte **nasazení,** chcete-li zavřít upozornění na přepsání souborů.

1. Po dokončení nasazení můžete znovu použít cURL nebo prohlížeč k testování znovu nasazené funkce. Stejně jako dříve přidejte řetězec `&name=<yourname>` dotazu k adrese URL, jako v následujícím příkladu:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. Znovu [zobrazit zprávu ve frontě úložiště](#examine-the-output-queue) k ověření, že výstupní vazba znovu generuje novou zprávu ve frontě.

## <a name="clean-up-resources"></a>Vyčištění prostředků

*Prostředky* v Azure odkazují na aplikace funkcí, funkce, účty úložiště atd. Jsou seskupené do *skupin prostředků*. Odstraněním skupiny odstraníte všechno, co v této skupině je.

Vytvořili jste prostředky k dokončení těchto rychlých startů. Tyto prostředky se vám mohou fakturovat, a to v závislosti na [stavu účtu](https://azure.microsoft.com/account/) a [cenách služeb](https://azure.microsoft.com/pricing/). Pokud prostředky už nepotřebujete, můžete k jejich odstranění použít tento postup:

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Další kroky

Aktualizovali jste funkci aktivovanou protokolem HTTP tak, aby zapisovali data do fronty úložiště. Teď se můžete dozvědět více o vývoji funkcí pomocí kódu Sady Visual Studio:

+ [Vývoj funkcí Azure pomocí kódu Visual Studia](functions-develop-vs-code.md)
::: zone pivot="programming-language-csharp"  
+ [Příklady úplných projektů funkce v c#](/samples/browse/?products=azure-functions&languages=csharp).
+ [Odkaz na vývojáře Azure Functions C#](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Příklady kompletních projektů funkcí v JavaScriptu](/samples/browse/?products=azure-functions&languages=javascript).
+ [Průvodce vývojářem JavaScriptu azure funkce](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Příklady úplných projektů funkce v typescriptu](/samples/browse/?products=azure-functions&languages=typescript).
+ [Průvodce vývojářem Azure Functions TypeScript](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Příklady kompletních projektů funkcí v Pythonu](/samples/browse/?products=azure-functions&languages=python).
+ [Průvodce vývojářem Azure Functions Pythonu](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Příklady kompletních projektů funkcí v prostředí PowerShell](/samples/browse/?products=azure-functions&languages=azurepowershell).
+ [Průvodce vývojářem prostředí Azure Functions PowerShell](functions-reference-powershell.md) 
::: zone-end
+ [Azure Functions aktivační události a vazby](functions-triggers-bindings.md).
+ [Stránka s cenami funkcí](https://azure.microsoft.com/pricing/details/functions/)
+ [Odhad nákladů plánu spotřeby](functions-consumption-costs.md) článek
