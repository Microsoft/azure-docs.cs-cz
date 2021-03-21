---
title: Vytvoření funkce jazyka C# pomocí Visual Studio Code-Azure Functions
description: Naučte se, jak vytvořit funkci jazyka C# a pak publikovat místní projekt na hostování bez serveru v Azure Functions pomocí rozšíření Azure Functions v Visual Studio Code.
ms.topic: quickstart
ms.date: 11/03/2020
ms.custom: devx-track-csharp
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2848ce6214d59ba2732dcfc148ccaf9936497f17
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121027"
---
# <a name="quickstart-create-a-c-function-in-azure-using-visual-studio-code"></a>Rychlý Start: vytvoření funkce jazyka C# v Azure pomocí Visual Studio Code

> [!div class="op_single_selector" title1="Vyberte jazyk funkce: "]
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [Python](create-first-function-vs-code-python.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Jiné (Go/Rust)](create-first-function-vs-code-other.md)

V tomto článku použijete Visual Studio Code k vytvoření funkce založené na knihovně jazyka C#, která reaguje na požadavky HTTP. Po místním testování kódu ho nasadíte do <abbr title="Běhové výpočetní prostředí, ve kterém jsou všechny podrobnosti serveru transparentní pro vývojáře aplikací, což zjednodušuje proces nasazení a správy kódu.">Prostředí bez serveru</abbr> prostředí pro <abbr title="Služba Azure, která pro aplikace poskytuje výpočetní prostředí bez serveru s nízkými náklady.">Azure Functions</abbr>.

Po dokončení tohoto rychlého startu dojde v účtu Azure k malým nákladům na několik centů nebo méně.

K dispozici je také verze tohoto článku [na bázi CLI](create-first-function-cli-csharp.md) .
    
## <a name="1-configure-your-environment"></a>1. konfigurace prostředí

Než začnete, ujistěte se, že máte zavedené následující požadavky:

+ Azure <abbr title="Profil, který uchovává fakturační informace pro využití Azure">account</abbr> s aktivní <abbr title="Základní organizační struktura, ve které spravujete prostředky v Azure, obvykle přidružené k jednotlivcům nebo oddělením v rámci organizace.">předplatné</abbr>. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) verze 3. x.

+ [Visual Studio Code](https://code.visualstudio.com/) na jedné z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms)

+ [Rozšíření pro jazyk C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) pro Visual Studio Code  

+ [Rozšíření Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pro Visual Studio Code

## <a name="2-create-your-local-project"></a><a name="create-an-azure-functions-project"></a>2. Vytvořte svůj místní projekt

V této části použijete Visual Studio Code k vytvoření místního <abbr title="Logický kontejner pro jednu nebo více jednotlivých funkcí, které lze nasadit a spravovat společně.">Azure Functions projekt</abbr> v jazyce C#. Později v tomto článku publikujete kód funkce do Azure.

1. Vyberte ikonu Azure v <abbr title="Svislá skupina ikon na levé straně okna Visual Studio Code.">Řádek aktivity</abbr>a potom v oblasti **Azure: Functions** vyberte ikonu **vytvořit nový projekt...** .

    ![Volba možnosti Vytvořit nový projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Zvolte umístění adresáře pro váš pracovní prostor projektu a zvolte **Vybrat**.

    > [!NOTE]
    > Tyto kroky jsou navržené tak, aby se dokončily mimo pracovní prostor. V tomto případě nevybírejte složku projektu, která je součástí pracovního prostoru.

1. Podle pokynů na obrazovce zadejte tyto informace:

    + **Vyberte jazyk pro váš projekt funkce:** Zvolte `C#`.

    + **Vyberte šablonu pro první funkci projektu:** Zvolte `HTTP trigger`.

    + **Zadejte název funkce:** Zadejte příkaz `HttpExample`.

    + **Zadejte obor názvů:** Zadejte příkaz `My.Functions`.

    + **Úroveň autorizace**: vyberte `Anonymous` , který umožňuje komukoli zavolat koncový bod funkce. Další informace o úrovních autorizace najdete v tématu [autorizační klíče](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Vyberte způsob, jakým chcete projekt otevírat:** Zvolte `Add to workspace`.

1. Pomocí těchto informací Visual Studio Code generuje projekt Azure Functions s protokolem HTTP. <abbr title="Typ události, která vyvolá kód funkce, jako je například požadavek HTTP, zpráva fronty nebo určitý čas.">signálu</abbr>. Můžete zobrazit soubory místních projektů v Průzkumníkovi. Další informace o souborech, které jsou vytvořeny, najdete v tématu [vygenerované soubory projektu](functions-develop-vs-code.md#generated-project-files).

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Po ověření, že se funkce na místním počítači spustí správně, je čas použít Visual Studio Code k publikování projektu přímo do Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="5-publish-the-project-to-azure"></a>5. publikujte projekt do Azure.

V této části vytvoříte aplikaci funkcí a související prostředky v předplatném Azure a pak svůj kód nasadíte. 

> [!IMPORTANT]
> Publikování do existující aplikace funkcí přepíše obsah této aplikace v Azure. 

1. Zvolte ikonu Azure na řádku aktivity a potom v oblasti **Azure: funkce** zvolte tlačítko **nasadit do aplikace Function App...** .

    

1. Podle pokynů na obrazovce zadejte tyto informace:

    + **Vyberte složku**: Zvolte složku z pracovního prostoru nebo přejděte k nějakému, který obsahuje vaši aplikaci Function App. Tato funkce se nezobrazuje, pokud už máte spuštěnou platnou aplikaci Function App.

    + **Vyberte předplatné**: zvolte předplatné, které chcete použít. Toto se nezobrazí, pokud máte jenom jedno předplatné.

    + **Výběr aplikace funkcí v Azure:** Zvolte `+ Create new Function App`. (Nevybírejte `Advanced` možnost, která není pokrytá v tomto článku.)

    + **Zadejte globálně jedinečný název aplikace Function App**: zadejte název, který je platný v cestě URL. Název, který zadáte, bude ověřený, abyste se ujistili, že je <abbr title="Název musí být jedinečný ve všech projektech funkcí používaných všemi zákazníky Azure globálně. Obvykle používáte kombinaci svého osobního nebo firemního jména, názvu aplikace a číselného identifikátoru, jako ve společnosti Contoso-bizapp-Func-20">jedinečné v Azure Functions</abbr>. 

    + **Vyberte umístění pro nové prostředky**: pro lepší výkon zvolte [oblast](https://azure.microsoft.com/regions/) poblíž.

    Rozšíření zobrazuje stav jednotlivých prostředků, které se vytváří v Azure v oznamovací oblasti.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Oznámení o vytváření prostředků Azure":::

1. Po dokončení se ve vašem předplatném vytvoří následující prostředky Azure s použitím názvů na základě názvu vaší aplikace Function App:

    + **Skupina prostředků**, což je logický kontejner pro související prostředky.
    + Účet standard **Azure Storage**, který uchovává stav a další informace o vašich projektech.
    + **Plán spotřeby**, který definuje základního hostitele pro aplikaci s funkcí bez serveru. 
    + **Aplikace Function App**, která poskytuje prostředí pro spuštění kódu vaší funkce. Aplikace Function App umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků v rámci stejného plánu hostování.
    + **Instance Application Insights** připojená k aplikaci Function App, která sleduje využití funkce bez serveru.

    Po vytvoření aplikace funkcí a použití balíčku nasazení se zobrazí oznámení. 

    > [!TIP]
    > Ve výchozím nastavení se prostředky Azure požadované vaší aplikací Function App vytvoří na základě názvu aplikace Function App, kterou zadáte. Ve výchozím nastavení se také vytvoří ve stejné nové skupině prostředků s aplikací Function App. Pokud chcete přizpůsobit názvy těchto prostředků nebo znovu použít stávající prostředky, je třeba [projekt publikovat s možností pokročilého vytvoření](functions-develop-vs-code.md#enable-publishing-with-advanced-create-options).


1. Pokud chcete zobrazit výsledky vytvoření a nasazení, včetně prostředků Azure, které jste vytvořili, vyberte **Zobrazit výstup** v tomto oznámení. Pokud jste oznámení nezobrazili, vyberte ikonu zvonku v pravém dolním rohu, abyste ji viděli znovu.

    ![Vytvořit kompletní oznámení](./media/functions-create-first-function-vs-code/function-create-notifications.png)

## <a name="6-run-the-function-in-azure"></a>6. Spusťte funkci v Azure

1. Zpátky v oblasti **Azure: Functions (funkce** ) na bočním panelu rozbalte své předplatné, novou aplikaci Function App a **funkce**. Klikněte pravým tlačítkem myši (Windows) nebo <kbd>stiskněte klávesu CTRL</kbd> (MacOS) `HttpExample` a vyberte možnost **Spustit funkci nyní...**.

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Spustit nyní funkci v Azure z Visual Studio Code":::

1. V části **Zadejte text žádosti** se zobrazí hodnota tělo zprávy žádosti `{ "name": "Azure" }` .

    Stisknutím klávesy ENTER odešlete tuto zprávu požadavku vaší funkci.  

1. Když se funkce spustí v Azure a vrátí odpověď, vygeneruje se oznámení v Visual Studio Code.

## <a name="5-clean-up-resources"></a>5. vyčištění prostředků

Když přejdete k [dalšímu kroku](#next-steps) a přidáte <abbr title="Způsob, jak přidružit funkci k frontě úložiště, aby mohla vytvářet zprávy ve frontě.">Výstupní vazba fronty Azure Storage</abbr> do své funkce budete muset zachovat všechny vaše prostředky na místě, abyste mohli vytvářet informace o tom, co jste už provedli.

V opačném případě můžete pomocí následujících kroků odstranit aplikaci Function App a její související prostředky, abyste se vyhnuli dalším nákladům.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

Další informace o nákladech na funkce najdete v tématu [odhad nákladů na plán spotřeby](functions-consumption-costs.md).

## <a name="next-steps"></a>Další kroky

Pomocí Visual Studio Code jste vytvořili aplikaci funkcí s jednoduchou funkcí aktivovanou protokolem HTTP. V dalším článku rozbalíte tuto funkci přidáním výstupu. <abbr title="Deklarativní připojení mezi funkcí a dalšími prostředky. Vstupní vazba poskytuje data funkci. výstupní vazba poskytuje data z funkce do jiných prostředků.">vazba</abbr>. Tato vazba zapíše řetězec z požadavku HTTP do zprávy ve frontě Azure Queue Storage. 

> [!div class="nextstepaction"]
> [Připojení k frontě Azure Storage](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
