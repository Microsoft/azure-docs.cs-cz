---
title: Vytvoření funkce Java pomocí Visual Studio Code-Azure Functions
description: Naučte se vytvořit funkci Java a pak publikovat místní projekt na hostování bez serveru v Azure Functions pomocí rozšíření Azure Functions v Visual Studio Code.
ms.topic: quickstart
ms.date: 11/03/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 6c84bf4ebc73857fa0280ffbcbf46b68c2da630f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031714"
---
# <a name="quickstart-create-a-java-function-in-azure-using-visual-studio-code"></a>Rychlý Start: vytvoření funkce Java v Azure pomocí Visual Studio Code

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

Pomocí Visual Studio Code vytvořte funkci jazyka Java, která reaguje na požadavky HTTP. Otestujte kód místně a pak ho nasaďte do prostředí s Azure Functions bez serveru.

Po dokončení tohoto rychlého startu dojde k malým nákladům na několik centů nebo méně. <abbr title="Profil, který uchovává fakturační informace pro využití Azure">Účet Azure</abbr>.

Pokud Visual Studio Code není preferovaným vývojářským nástrojem, podívejte se na naše podobné kurzy pro vývojáře v jazyce Java, které používají [Maven](create-first-function-cli-java.md), [GRADLE](./functions-create-first-java-gradle.md) a [IntelliJ nápad](/azure/developer/java/toolkit-for-intellij/quickstart-functions).

## <a name="1-prepare-your-environment"></a>1. Příprava prostředí

Než začnete, ujistěte se, že máte zavedené následující požadavky:

+ Účet Azure s aktivním <abbr title="Základní organizační struktura, ve které spravujete prostředky v Azure, obvykle přidružené k jednotlivcům nebo oddělením v rámci organizace.">předplatné</abbr>. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Sada Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), verze 8 nebo 11.

+ [Apache Maven](https://maven.apache.org)verze 3,0 nebo vyšší.

+ [Visual Studio Code](https://code.visualstudio.com/) na jedné z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms)

+ [Balíček rozšíření Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)  

+ [Rozšíření Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pro Visual Studio Code

<br/>
<hr/>

## <a name="2-create-your-local-functions-project"></a>2. <a name="create-an-azure-functions-project"></a> vytvořte projekt místních funkcí.

1. Zvolte ikonu Azure na **řádku aktivity** a potom v oblasti **Azure: funkce** vyberte ikonu **vytvořit nový projekt...** .

    ![Volba možnosti Vytvořit nový projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. **Zvolte umístění adresáře** pro váš pracovní prostor projektu a pak zvolte **Vybrat**.

1. Podle pokynů na obrazovce zadejte tyto informace:

    + **Vyberte jazyk pro váš projekt funkce:** Zvolte `Java`.

    + **Vyberte verzi Java**: zvolte `Java 8` nebo `Java 11` , verze jazyka Java, na které budou vaše funkce běžet v Azure. Vyberte verzi jazyka Java, kterou jste ověřili místně.

    + **Zadejte ID skupiny**: vyberte `com.function` .

    + **Zadejte ID artefaktu**: vyberte `myFunction` .

    + **Zadejte verzi**: vyberte `1.0-SNAPSHOT` .

    + **Zadejte název balíčku**: vyberte `com.function` .

    + **Zadejte název aplikace**: vyberte `myFunction-12345` .

    + **Úroveň autorizace**: vyberte `Anonymous` , který umožňuje komukoli zavolat koncový bod funkce.

    + **Vyberte způsob, jakým chcete projekt otevírat:** Zvolte `Add to workspace`.

<br/>

<details>
<summary><strong>Nemůžete vytvořit projekt funkce?</strong></summary>

Nejběžnější problémy, které je potřeba vyřešit při vytváření projektu místní funkce, jsou:
* Nemáte nainstalovanou příponu Azure Functions. 
</details>

<br/>
<hr/>

## <a name="3-run-the-function-locally"></a>3. Spusťte funkci místně.

1. Stisknutím klávesy <kbd>F5</kbd> spusťte projekt Function App.

1. V **terminálu** se podívejte na koncový bod adresy URL vaší funkce spuštěné místně.

    ![Místní funkce VS Code výstupem](media/functions-create-first-function-vs-code/functions-vscode-f5.png)

1. Se základními nástroji, které běží, přejdete do oblasti **Azure: Functions** . V části **funkce** rozbalte **místní**  >  **funkce** projektu. Klikněte pravým tlačítkem myši (Windows) nebo <kbd>stiskněte klávesu CTRL</kbd> (MacOS) `HttpExample` a vyberte možnost **Spustit funkci nyní...**.

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Spustit funkci hned z Visual Studio Code":::

1. V části **Zadejte text žádosti** se zobrazí hodnota tělo zprávy žádosti `{ "name": "Azure" }` . Stisknutím klávesy <kbd>ENTER</kbd> odešlete tuto zprávu požadavku vaší funkci.  

1. Když se funkce spustí místně a vrátí odpověď, vygeneruje se oznámení v Visual Studio Code. Informace o spuštění funkce se zobrazí na panelu **terminálu** .

1. Stisknutím <kbd>kombinace kláves CTRL + C</kbd> zastavte základní nástroje a odpojte ladicí program.

<br/>

<details>
<summary><strong>Funkci nejde spustit lokálně?</strong></summary>

Nejběžnější problémy, které je potřeba vyřešit při spuštění projektu místní funkce, jsou:
* Nemáte nainstalované základní nástroje. 
*  Pokud máte potíže při spuštění ve Windows, ujistěte se, že výchozí prostředí terminálu pro Visual Studio Code není nastavené na bash WSL. 
</details>

<br/>
<hr/>

## <a name="4-sign-in-to-azure"></a>4. Přihlaste se k Azure.

Pokud chcete aplikaci publikovat, přihlaste se k Azure. Pokud jste už přihlášení, přečtěte si další část.

1. Zvolte ikonu Azure na řádku aktivity a potom v oblasti **Azure: funkce** zvolte **Přihlásit se k Azure...**.

    ![Přihlaste se k Azure v rámci VS Code](../../includes/media/functions-sign-in-vs-code/functions-sign-into-azure.png)

1. Po zobrazení výzvy v prohlížeči **Vyberte svůj účet Azure** a **přihlaste** se pomocí svých přihlašovacích údajů k účtu Azure.

1. Po úspěšném přihlášení zavřete nové okno prohlížeče a vraťte se zpět na Visual Studio Code.

<br/>
<hr/>

## <a name="5-publish-the-project-to-azure"></a>5. publikujte projekt do Azure.

Vaše první nasazení kódu zahrnuje vytvoření prostředku funkce ve vašem předplatném Azure.

1. Zvolte ikonu Azure na řádku aktivity a potom v oblasti **Azure: funkce** zvolte tlačítko **nasadit do aplikace Function App...** .

    ![Publikování projektu do Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Podle pokynů na obrazovce zadejte tyto informace:

    + **Vyberte složku**: Zvolte složku, která obsahuje vaši aplikaci Function App. 

    + **Vyberte předplatné**: zvolte předplatné, které chcete použít. Toto se nezobrazí, pokud máte jenom jedno předplatné.

    + **Výběr aplikace funkcí v Azure:** Zvolte `Create new Function App`.

    + **Zadejte globálně jedinečný název aplikace Function App**: v cestě URL zadejte název, který je v rámci Azure jedinečný. Název, který zadáte, je ověřený k zajištění globální jedinečnosti.

    - **Vyberte umístění pro nové prostředky**: pro lepší výkon zvolte [oblast](https://azure.microsoft.com/regions/) poblíž.

1. Po vytvoření aplikace funkcí a použití balíčku nasazení se zobrazí oznámení. Pokud chcete zobrazit výsledky vytvoření a nasazení, vyberte **Zobrazit výstup** .

    ![Vytvořit kompletní oznámení](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

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

<br/>
<hr/>

## <a name="6-run-the-function-in-azure"></a>6. Spusťte funkci v Azure

1. Zpátky v oblasti **Azure: Functions (funkce** ) na bočním panelu rozbalte své předplatné, novou aplikaci Function App a **funkce**. Klikněte pravým tlačítkem myši (Windows) nebo <kbd>stiskněte klávesu CTRL</kbd> (MacOS) `HttpExample` a vyberte možnost **Spustit funkci nyní...**.

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Spustit nyní funkci v Azure z Visual Studio Code":::

1. V části **Zadejte text žádosti** se zobrazí hodnota tělo zprávy žádosti `{ "name": "Azure" }` . Stisknutím klávesy ENTER odešlete tuto zprávu požadavku vaší funkci.  

1. Když se funkce spustí v Azure a vrátí odpověď, vygeneruje se oznámení v Visual Studio Code.

<br/>
<hr/>

## <a name="7-clean-up-resources"></a>7. vyčištění prostředků

Pokud neplánujete pokračovat k [dalšímu kroku](#next-steps), odstraňte aplikaci Function App a její prostředky, abyste se vyhnuli dalším nákladům.

1. V Visual Studio Code na řádku aktivity vyberte ikonu Azure a pak vyberte oblast funkce na bočním panelu.
1. Vyberte aplikaci Function App, klikněte na ni pravým tlačítkem a vyberte **Odstranit aplikaci Function App...**.

<br/>
<hr/>

## <a name="next-steps"></a>Další kroky

Rozbalí funkci přidáním <abbr title="V Azure Storage znamená přidružení funkce k frontě úložiště, aby mohla vytvářet zprávy ve frontě.">výstupní vazba</abbr>. Tato vazba zapíše řetězec z požadavku HTTP do zprávy ve frontě Azure Queue Storage.

> [!div class="nextstepaction"]
> [Připojení k frontě Azure Storage](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-java)
