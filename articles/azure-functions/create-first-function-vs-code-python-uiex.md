---
title: Vytvoření funkce Pythonu pomocí Visual Studio Code-Azure Functions
description: Naučte se vytvořit funkci Pythonu a potom publikovat místní projekt na hostování bez serveru v Azure Functions pomocí rozšíření Azure Functions v Visual Studio Code.
ms.topic: quickstart
ms.date: 11/04/2020
ms.custom: devx-track-python
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 19a8a73428bef3c29c5bf9e56b45750adf5fb2dd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748481"
---
# <a name="quickstart-create-a-function-in-azure-with-python-using-visual-studio-code"></a>Rychlý Start: vytvoření funkce v Azure pomocí Pythonu pomocí Visual Studio Code

> [!div class="op_single_selector" title1="Vyberte jazyk funkce: "]
> - [Python](create-first-function-vs-code-python.md)
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Jiné (Go/Rust)](create-first-function-vs-code-other.md)

V tomto článku použijete Visual Studio Code k vytvoření funkce jazyka Python, která reaguje na požadavky HTTP. Po místním testování kódu ho nasadíte do <abbr title="Běhové výpočetní prostředí, ve kterém jsou všechny podrobnosti serveru transparentní pro vývojáře aplikací, což zjednodušuje proces nasazení a správy kódu.">Prostředí bez serveru</abbr> prostředí pro <abbr title="Služba Azure, která pro aplikace poskytuje výpočetní prostředí bez serveru s nízkými náklady.">Azure Functions</abbr>.

Po dokončení tohoto rychlého startu dojde v účtu Azure k malým nákladům na několik centů nebo méně.

K dispozici je také verze tohoto článku [na bázi CLI](create-first-function-cli-python.md) .

## <a name="1-prepare-your-environment"></a>1. Příprava prostředí

Než začnete, ujistěte se, že máte zavedené následující požadavky:

+ Azure <abbr title="Profil, který uchovává fakturační informace pro využití Azure">account</abbr> s aktivní <abbr title="Základní organizační struktura, ve které spravujete prostředky v Azure, obvykle přidružené k jednotlivcům nebo oddělením v rámci organizace.">předplatné</abbr>. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) verze 3. x.

+ [Python 3,8](https://www.python.org/downloads/release/python-381/), [Python 3,7](https://www.python.org/downloads/release/python-375/), [Python 3,6](https://www.python.org/downloads/release/python-368/) jsou podporovány Azure Functions (x64).

+ [Visual Studio Code](https://code.visualstudio.com/) na jedné z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms)

+ [Přípona Pythonu](https://marketplace.visualstudio.com/items?itemName=ms-python.python) pro Visual Studio Code.  

+ [Rozšíření Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pro Visual Studio Code

<hr/>
<br/>

## <a name="2-create-your-local-project"></a>2. <a name="create-an-azure-functions-project"></a> vytvořte svůj místní projekt

1. Vyberte ikonu Azure v <abbr title="Svislá skupina ikon na levé straně okna Visual Studio Code.">Řádek aktivity</abbr>a potom v oblasti **Azure: Functions** vyberte ikonu **vytvořit nový projekt...** .

    ![Volba možnosti Vytvořit nový projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Zvolte umístění adresáře pro váš pracovní prostor projektu a zvolte **Vybrat**.

    > [!NOTE]
    > Tyto kroky jsou navržené tak, aby se dokončily mimo pracovní prostor. V tomto případě nevybírejte složku projektu, která je součástí pracovního prostoru.

1. Podle pokynů na obrazovce zadejte tyto informace:

    + **Vyberte jazyk pro váš projekt funkce:** Zvolte `Python`.

    + **Vyberte alias Pythonu pro vytvoření virtuálního prostředí**: Zvolte umístění překladače Pythonu. Pokud se umístění nezobrazuje, zadejte úplnou cestu k binárnímu souboru Pythonu.  

    + **Vyberte šablonu pro první funkci projektu:** Zvolte `HTTP trigger`.

    + **Zadejte název funkce:** Zadejte příkaz `HttpExample`.

    + **Úroveň autorizace**: vyberte `Anonymous` , který umožňuje komukoli zavolat koncový bod funkce. Další informace o úrovních autorizace najdete v tématu [autorizační klíče](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Vyberte způsob, jakým chcete projekt otevírat:** Zvolte `Add to workspace`.

<br/>
<details>
<summary><strong>Nemůžete vytvořit projekt funkce?</strong></summary>

Nejběžnější problémy, které je potřeba vyřešit při vytváření projektu místní funkce, jsou:
* Nemáte nainstalovanou příponu Azure Functions. 
</details>

<hr/>
<br/>

## <a name="run-the-function-locally"></a>Místní spuštění funkce

1. Stisknutím klávesy <kbd>F5</kbd> spusťte projekt Function App.

1. Na panelu **terminálu** se podívejte na koncový bod adresy URL vaší funkce spuštěné místně.

    ![Místní funkce VS Code výstupem](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)


1. Se základními nástroji, které běží, přejdete do oblasti **Azure: Functions** . V části **funkce** rozbalte **místní**  >  **funkce** projektu. Klikněte pravým tlačítkem myši (Windows) nebo <kbd>stiskněte klávesu CTRL</kbd> (MacOS) `HttpExample` a vyberte možnost **Spustit funkci nyní...**.

    :::image type="content" source="media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Spustit funkci hned z Visual Studio Code":::

1. V části **Zadejte text žádosti** se zobrazí hodnota tělo zprávy žádosti `{ "name": "Azure" }` . Stisknutím klávesy ENTER odešlete tuto zprávu požadavku vaší funkci.  

1. Když se funkce spustí místně a vrátí odpověď, vygeneruje se oznámení v Visual Studio Code. Informace o spuštění funkce se zobrazí na panelu **terminálu** .

1. Stisknutím <kbd>kombinace kláves CTRL + C</kbd> zastavte základní nástroje a odpojte ladicí program.

<br/>
<details>
<summary><strong>Funkci nejde spustit lokálně?</strong></summary>

Nejběžnější problémy, které je potřeba vyřešit při spuštění projektu místní funkce, jsou:
* Nemáte nainstalované základní nástroje. 
*  Pokud máte potíže při spuštění ve Windows, ujistěte se, že výchozí prostředí terminálu pro Visual Studio Code není nastavené na **bash WSL**. 
</details>

<hr/>
<br/>

## <a name="4-sign-in-to-azure"></a>4. Přihlaste se k Azure.

Pokud chcete aplikaci publikovat, přihlaste se k Azure. Pokud jste už přihlášení, přečtěte si další část.

1. Zvolte ikonu Azure na řádku aktivity a potom v oblasti **Azure: funkce** zvolte **Přihlásit se k Azure...**.

    ![Přihlaste se k Azure v rámci VS Code](../../includes/media/functions-sign-in-vs-code/functions-sign-into-azure.png)

1. Po zobrazení výzvy v prohlížeči **Vyberte svůj účet Azure** a **přihlaste** se pomocí svých přihlašovacích údajů k účtu Azure.

1. Po úspěšném přihlášení zavřete nové okno prohlížeče a vraťte se zpět na Visual Studio Code. 

<hr/>
<br/>

## <a name="5-publish-the-project-to-azure"></a>5. publikujte projekt do Azure.

Vaše první nasazení kódu zahrnuje vytvoření prostředku funkce ve vašem předplatném Azure.

1. Zvolte ikonu Azure na řádku aktivity a potom v oblasti **Azure: funkce** zvolte tlačítko **nasadit do aplikace Function App...** .

    ![Publikování projektu do Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Podle pokynů na obrazovce zadejte tyto informace:

    + **Vyberte složku**: Zvolte složku, která obsahuje vaši aplikaci Function App.

    + **Vyberte předplatné**: zvolte předplatné, které chcete použít. Toto se nezobrazí, pokud máte jenom jedno předplatné.

    + **Výběr aplikace funkcí v Azure:** Zvolte `+ Create new Function App`.

    + **Zadejte globálně jedinečný název aplikace Function App**: zadejte název, který je platný v cestě URL. Název, který zadáte, bude ověřený, abyste se ujistili, že je <abbr title="Název musí být jedinečný ve všech zákaznících Azure globálně. Například můžete použít kombinaci názvu osobního nebo organizace, názvu aplikace a číselného identifikátoru, jako je například contoso-bizapp-Func-20.">jedinečné v rámci Azure</abbr>. 

    + **Vyberte modul runtime**: Zvolte verzi Pythonu, kterou jste spustili místně. `python --version`K zkontrolování vaší verze můžete použít příkaz.

    + **Vyberte umístění pro nové prostředky**: pro lepší výkon zvolte [oblast](https://azure.microsoft.com/regions/) poblíž.

    Rozšíření zobrazuje stav jednotlivých prostředků, které se vytváří v Azure v oznamovací oblasti.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Oznámení o vytváření prostředků Azure":::

1. Po vytvoření aplikace funkcí a použití balíčku nasazení se zobrazí oznámení. Pokud chcete zobrazit výsledky vytvoření a nasazení, vyberte **Zobrazit výstup** . 

    ![Vytvořit kompletní oznámení](./media/functions-create-first-function-vs-code/function-create-notifications.png)

<br/>
<details>
<summary><strong>Funkci nelze publikovat?</strong></summary>

Tato část vytvořila prostředky Azure a nasadila svůj místní kód do aplikace Function App. Pokud to nebylo úspěšné:

* Přečtěte si výstup informací o chybě. Ikona zvonku v pravém dolním rohu je dalším způsobem, jak zobrazit výstup. 
* Publikovali jste do existující aplikace Function App? Tato akce přepíše obsah této aplikace v Azure.
</details>


<br/>
<details>
<summary><strong>Jaké prostředky byly vytvořeny?</strong></summary>

Po dokončení se ve vašem předplatném vytvoří následující prostředky Azure s použitím názvů na základě názvu vaší aplikace Function App: 
* **Skupina prostředků**: Skupina prostředků je logický kontejner pro související prostředky ve stejné oblasti.
* **Účet Azure Storage**: prostředek úložiště uchovává stav a další informace o projektu.
* **Plán spotřeby**: plán spotřeby definuje základního hostitele pro aplikaci s funkcí bez serveru.
* **Aplikace Function App**: aplikace Function App poskytuje prostředí pro provádění kódu funkce a skupin funkcí jako logické jednotky.
* **Application Insights**: Application Insights sleduje využití funkce bez serveru.

</details>

<hr/>
<br/>

## <a name="6-run-the-function-in-azure"></a>6. Spusťte funkci v Azure

1. Zpátky na bočním panelu **Azure: Functions (funkce** ) rozbalte novou aplikaci Function App.
1. Rozbalte **funkce**, potom klikněte pravým tlačítkem myši (Windows) nebo <kbd>CTRL-</kbd> Click (MacOS) `HttpExample` a zvolte **Spustit funkci nyní..**..

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Spustit nyní funkci v Azure z Visual Studio Code":::

1. V části **Zadejte text žádosti** se zobrazí hodnota tělo zprávy žádosti `{ "name": "Azure" }` .

    Stisknutím klávesy ENTER odešlete tuto zprávu požadavku vaší funkci.  

1. Když se funkce spustí v Azure a vrátí odpověď, vygeneruje se oznámení v Visual Studio Code.

## <a name="7-clean-up-resources"></a>7. vyčištění prostředků

Když přejdete k [dalšímu kroku](#next-steps) a přidáte <abbr title="Způsob, jak přidružit funkci k frontě úložiště, aby mohla vytvářet zprávy ve frontě.">Výstupní vazba fronty Azure Storage</abbr> do své funkce budete muset zachovat všechny vaše prostředky na místě, abyste mohli vytvářet informace o tom, co jste už provedli.

V opačném případě můžete pomocí následujících kroků odstranit aplikaci Function App a její související prostředky, abyste se vyhnuli dalším nákladům.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

Další informace o nákladech na funkce najdete v tématu [odhad nákladů na plán spotřeby](functions-consumption-costs.md).

## <a name="next-steps"></a>Další kroky

Rozbalí tuto funkci přidáním výstupu. <abbr title="Deklarativní připojení mezi funkcí a dalšími prostředky. Vstupní vazba poskytuje data funkci. výstupní vazba poskytuje data z funkce do jiných prostředků.">vazba</abbr>. Tato vazba zapíše řetězec z požadavku HTTP do zprávy ve frontě Azure Queue Storage. 

> [!div class="nextstepaction"]
> [Připojení k frontě Azure Storage](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-python)

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/python-functions-qs-survey)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
