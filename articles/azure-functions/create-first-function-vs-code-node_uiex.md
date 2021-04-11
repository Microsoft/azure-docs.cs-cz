---
title: Vytvoření funkce JavaScriptu pomocí Visual Studio Code-Azure Functions
description: Naučte se vytvořit funkci JavaScriptu a pak publikovat místní Node.js projekt k hostování bez serveru v Azure Functions pomocí rozšíření Azure Functions v Visual Studio Code.
ms.topic: quickstart
ms.date: 11/03/2020
ms.custom:
- devx-track-js
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3cf47d04da51db898e667ef8b31d42d79c9f354e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748379"
---
# <a name="quickstart-create-a-javascript-function-in-azure-using-visual-studio-code"></a>Rychlý Start: vytvoření funkce JavaScriptu v Azure pomocí Visual Studio Code

> [!div class="op_single_selector" title1="Vyberte jazyk funkce: "]
> - [JavaScript](create-first-function-vs-code-node.md)
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [Python](create-first-function-vs-code-python.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Jiné (Go/Rust)](create-first-function-vs-code-other.md)

Pomocí Visual Studio Code můžete vytvořit funkci JavaScriptu, která reaguje na požadavky HTTP. Otestujte kód místně a pak ho nasaďte do prostředí s Azure Functions bez serveru.

Po dokončení tohoto rychlého startu dojde k malým nákladům na několik centů nebo méně. <abbr title="Účet Azure je globální jedinečná entita, která vám umožní přístup ke službám Azure a vašim předplatným Azure.">Účet Azure</abbr>.

## <a name="1-prepare-your-environment"></a>1. Příprava prostředí

Než začnete, ujistěte se, že máte zavedené následující požadavky:

+ Účet Azure s <abbr title="Předplatné Azure je logický kontejner, který se používá ke zřizování prostředků v Azure. Obsahuje podrobnosti o všech vašich prostředcích, jako jsou virtuální počítače, databáze a podobně.">aktivní předplatné</abbr>. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Node.js 10.14.1 +](https://nodejs.org/)

+ [Visual Studio Code](https://code.visualstudio.com/)

+ [Azure Functions rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pro Visual Studio Code

+ [Základní nástroje Azure Functions](functions-run-local.md?tabs=linux%2Ccsharp%2Cbash#install-the-azure-functions-core-tools)

<hr/>
<br/>

## <a name="2-create-your-local-functions-project"></a>2. <a name="create-an-azure-functions-project"></a> vytvořte projekt místních funkcí.

1. Vyberte ikonu Azure v <abbr title="">Řádek aktivity</abbr>a potom v oblasti **Azure: Functions** vyberte ikonu **vytvořit nový projekt...** .

    ![Volba možnosti Vytvořit nový projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. **Zvolte umístění adresáře** pro váš pracovní prostor projektu a pak zvolte **Vybrat**. 

1. Podle pokynů na obrazovce zadejte tyto informace:

    + **Vyberte jazyk pro váš projekt funkce:** Zvolte `JavaScript`.

    + **Vyberte šablonu pro první funkci projektu:** Zvolte `HTTP trigger`.

    + **Zadejte název funkce:** Zadejte příkaz `HttpExample`.

    + **Úroveň autorizace**: vyberte `Anonymous` , který umožňuje komukoli zavolat koncový bod funkce.

    + **Vyberte způsob, jakým chcete projekt otevírat:** Zvolte `Add to workspace`.




<br/>
<details>
<summary><strong>Nemůžete vytvořit projekt funkce?</strong></summary>

Nejběžnější problémy, které je potřeba vyřešit při vytváření projektu místní funkce, jsou:
* Nemáte nainstalovanou příponu Azure Functions. 
</details>

<hr/>
<br/>

## <a name="3-run-the-function-locally"></a>3. Spusťte funkci místně.


1. Stisknutím klávesy <kbd>F5</kbd> spusťte projekt Function App. 

1. V **terminálu** se podívejte na koncový bod adresy URL vaší funkce spuštěné místně.

    ![Místní funkce VS Code výstupem](../../includes/media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Zkopírujte následující adresu URL a vložte ji do webového prohlížeče a potom stiskněte klávesu ENTER.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Zobrazit vrácenou odpověď


    ![Browser – ukázkový výstup pro localhost](./media/create-first-function-vs-code-other/functions-test-local-browser.png)

1. Zobrazit informace na panelu **terminálu** o žádosti.

    ![Spuštění hostitele úkolu – výstup VS Code terminálu](../../includes/media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Stisknutím <kbd>kombinace kláves CTRL + C</kbd> zastavte základní nástroje a odpojte ladicí program.

<br/>
<details>
<summary><strong>Funkci nejde spustit lokálně?</strong></summary>

Nejběžnější problémy, které je potřeba vyřešit při spuštění projektu místní funkce, jsou:
* Nemáte nainstalované základní nástroje. 
*  Pokud máte potíže při spuštění ve Windows, ujistěte se, že výchozí prostředí terminálu pro Visual Studio Code není nastavené na bash WSL. 
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

    + **Zadejte globálně jedinečný název aplikace Function App**: v cestě URL zadejte název, který je v rámci Azure jedinečný. Název, který zadáte, je ověřený k zajištění globální jedinečnosti.

    + **Vyberte modul runtime**: zvolte verzi Node.js, na které jste pracovali místně. `node --version`K zkontrolování vaší verze můžete použít příkaz.

    + **Vyberte umístění pro nové prostředky**: pro lepší výkon zvolte [oblast](https://azure.microsoft.com/regions/) poblíž. 

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
1. Na bočním panelu **Azure: Functions** rozbalte novou aplikaci Function App. 
1. Rozbalte **funkce** a pak klikněte pravým tlačítkem na **HttpExample** a pak zvolte **Spustit funkci hned...**.

    ![Zkopírujte adresu URL funkce pro nový Trigger HTTP.](../../includes/media/functions-vs-code-run-remote/execute-function-now.png)

1. **Stisknutím klávesy ENTER** odešlete výchozí zprávu požadavku vaší funkci. 

1. Oznámení je vyvoláno v Visual Studio Code při dokončení spuštění funkce.

<br/>
<details>
<summary><strong>Nepovedlo se spustit cloudovou aplikaci funkcí?</strong></summary>

* Zapomněli jste přidat dotaz QueryString na konec adresy URL?

</details>

<hr/>
<br/>

## <a name="7-clean-up-resources"></a>7. vyčištění prostředků

Odstraňte aplikaci funkcí a její prostředky, abyste se vyhnuli dalším nákladům.

1. V Visual Studio Code na řádku aktivity vyberte ikonu Azure a pak vyberte oblast funkce na bočním panelu. 
1. Vyberte aplikaci Function App, klikněte na ni pravým tlačítkem a vyberte **Odstranit aplikaci Function App...**.

<hr/>
<br/>

## <a name="next-steps"></a>Další kroky

Rozbalí funkci přidáním <abbr title="Vazba na funkci je způsob deklarativního připojení jiného prostředku k funkci.">výstupní vazba</abbr>. Tato vazba zapíše řetězec z požadavku HTTP do zprávy ve frontě Azure Queue Storage. 

> [!div class="nextstepaction"]
> [Připojení k frontě Azure Storage](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-javascript)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
