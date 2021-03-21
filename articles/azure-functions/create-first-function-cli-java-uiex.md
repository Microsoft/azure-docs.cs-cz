---
title: Vytvoření funkce jazyka Java z příkazového řádku – Azure Functions
description: Naučte se vytvořit funkci Java z příkazového řádku a pak publikovat místní projekt na hostování bez serveru v Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 32d91f01fcebec3e1ae0d19b1bff29a71f41c0d1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102050422"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>Rychlý Start: vytvoření funkce Java v Azure z příkazového řádku

> [!div class="op_single_selector" title1="Vyberte jazyk funkce: "]
> - [Java](create-first-function-cli-java.md)
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

Pomocí nástrojů příkazového řádku můžete vytvořit funkci Java, která reaguje na požadavky HTTP. Otestujte kód místně a pak ho nasaďte do prostředí s Azure Functions bez serveru.

Po dokončení tohoto rychlého startu dojde k malým nákladům na několik centů nebo méně. <abbr title="Profil, který uchovává fakturační informace pro využití Azure">Účet Azure</abbr>.

Pokud Maven není preferovaným vývojářským nástrojem, podívejte se na naše podobné kurzy pro vývojáře v jazyce Java, které využívají [Gradle](./functions-create-first-java-gradle.md), [IntelliJ nápad](/azure/developer/java/toolkit-for-intellij/quickstart-functions) a [Visual Studio Code](create-first-function-vs-code-java.md).

## <a name="1-prepare-your-environment"></a>1. Příprava prostředí

Než začnete, musíte mít následující:

+ Účet Azure s aktivním <abbr title="Základní organizační struktura, ve které spravujete prostředky v Azure, obvykle přidružené k jednotlivcům nebo oddělením v rámci organizace.">předplatné</abbr>. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2) verze 3. x.

+ [Azure CLI](/cli/azure/install-azure-cli) verze 2,4 nebo novější.

+ [Sada Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), verze 8 nebo 11. `JAVA_HOME`Proměnná prostředí musí být nastavená na umístění instalace správné verze JDK.

+ [Apache Maven](https://maven.apache.org)verze 3,0 nebo vyšší.

### <a name="prerequisite-check"></a>Kontrola požadovaných součástí

+ V terminálu nebo příkazovém okně spusťte příkaz `func --version` a ověřte, zda se <abbr title="Sada nástrojů příkazového řádku pro práci s Azure Functions v místním počítači.">Azure Functions Core Tools</abbr> jsou verze 3. x.

+ Spusťte `az --version` a ověřte, jestli je verze Azure CLI 2,4 nebo novější.

+ Spusťte `az login` , abyste se přihlásili do Azure a ověřili aktivní předplatné.

<br>
<hr/>

## <a name="2-create-a-local-function-project"></a>2. vytvoření projektu místní funkce

V Azure Functions je projekt funkce kontejnerem pro jednu nebo více jednotlivých funkcí, které jednotlivé reakce na konkrétní <abbr title="Typ události, která vyvolá kód funkce, jako je například požadavek HTTP, zpráva fronty nebo určitý čas.">signálu</abbr>. Všechny funkce v projektu sdílejí stejné místní konfigurace a konfigurace hostování. V této části vytvoříte projekt funkce, který obsahuje jedinou funkci.

1. Spuštěním následujícího příkazu v prázdné složce vygenerujte projekt Functions z [archetypu Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html). 

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" 
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8"
    ```

    ---

    <br/>
    <details>
    <summary><strong>Spuštění funkcí v jazyce Java 11</strong></summary>

    Pokud chcete funkce spouštět ve verzi Java 11, použijte `-DjavaVersion=11`. Další informace najdete v tématu [verze Java](functions-reference-java.md#java-versions).
    </details>

1. Maven vás vyzve k zadání hodnot potřebných k dokončení generování projektu při nasazení.
    Po zobrazení výzvy zadejte následující hodnoty:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | **groupId** | `com.fabrikam` | Hodnota, která jednoznačně identifikuje váš projekt napříč všemi projekty, podle [pravidel pro pojmenovávání balíčků](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) pro Java. |
    | **artifactId** | `fabrikam-functions` | Hodnota, která představuje název jar bez čísla verze. |
    | **znění** | `1.0-SNAPSHOT` | Vyberte výchozí hodnotu. |
    | **balíček** | `com.fabrikam` | Hodnota, která je balíčkem Java pro vygenerovaný kód funkce. Použijte výchozí hodnotu. |

1. `Y`Potvrďte zadáním nebo stisknutím klávesy ENTER.

    Maven vytvoří soubory projektu v nové složce s názvem _artifactId_, který je v tomto příkladu `fabrikam-functions` . 

1. Přejděte do složky projektu:

    ```console
    cd fabrikam-functions
    ```

<br/>
<details>
<summary><strong>Co se vytvořilo ve složce LocalFunctionProj?</strong></summary>

Tato složka obsahuje různé soubory pro projekt, jako *Function. Java*, *FunctionTest. Java* a *pom.xml*. K dispozici jsou také konfigurační soubory s názvem [local.settings.jsv](functions-run-local.md#local-settings-file) a [host.js](functions-host-json.md). Vzhledem k tomu, že *local.settings.json* může obsahovat tajné kódy stažené z Azure, soubor je ve výchozím nastavení vyloučený ze správy zdrojového kódu v souboru *. gitignore* .
</details>

<br/>
<details>
<summary><strong>Kód pro Functions. Java</strong></summary>

*Function. Java* obsahuje `run` metodu, která přijímá data žádosti v `request` proměnné je [zprávy HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) , který je upravený pomocí anotace [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger) , která definuje chování triggeru. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

Zpráva odpovědi vygeneruje rozhraní API [HttpResponseMessage. Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) .

Archetype také generuje test jednotek pro vaši funkci. Když změníte funkci pro přidání vazeb nebo přidání nových funkcí do projektu, budete také muset upravit testy v souboru *FunctionTest. Java* .
</details>

<br/>
<details>
<summary><strong>Kód pro pom.xml</strong></summary>

Nastavení prostředků Azure vytvořených pro hostování vaší aplikace jsou definovaná v **konfiguračním** elementu modulu plug-in s **identifikátorem ID skupiny** `com.microsoft.azure` ve vygenerovaném souboru *pom.xml* . Například konfigurační prvek níže vydá pokyn nasazení založeného na Maven pro vytvoření aplikace Function App ve `java-functions-group` skupině prostředků v `westus` <abbr title="Zeměpisná reference na konkrétní datacentrum Azure, ve kterém jsou prostředky přiděleny.">oblast</abbr>. Aplikace Function App se sama spustí ve Windows hostovaném v `java-functions-app-service-plan` plánu, což je ve výchozím nastavení plán spotřeby bez serveru.

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

Tato nastavení můžete změnit, abyste mohli řídit, jak se v Azure vytvářejí prostředky, třeba změnou `runtime.os` z `windows` na `linux` před počátečním nasazením. Úplný seznam nastavení podporovaných modulem plug-in Maven najdete v [podrobnostech o konfiguraci](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).
</details>

<br>
<hr/>

## <a name="3-run-the-function-locally"></a>3. Spusťte funkci místně.

1. **Spusťte funkci** spuštěním místního hostitele modulu runtime Azure Functions ze složky *LocalFunctionProj* :

    ```console
    mvn clean package
    mvn azure-functions:run
    ```

    Ke konci výstupu by se měly zobrazit následující řádky:

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    ...

    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.

    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    </pre>

    Pokud se HttpExample nezobrazí, jak je uvedeno výše, pravděpodobně jste spustili hostitele mimo kořenovou složku projektu. V takovém případě pomocí <kbd>kombinace kláves CTRL + C</kbd> zastavte hostitele, přejděte do kořenové složky projektu a spusťte předchozí příkaz znovu.

1. **Zkopírujte adresu URL** vaší `HttpExample` funkce z tohoto výstupu do prohlížeče a přidejte řetězec dotazu `?name=<YOUR_NAME>` , jako je třeba celá adresa URL `http://localhost:7071/api/HttpExample?name=Functions` . V prohlížeči by se měla zobrazit zpráva jako `Hello Functions` :

    ![Výsledek funkce místně spuštěný v prohlížeči](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)

    Terminál, ve kterém jste spustili projekt, také zobrazuje výstup protokolu při provádění požadavků.

1. Po dokončení použijte <kbd>kombinaci kláves CTRL + C</kbd> a kliknutím na <kbd>y</kbd> zastavte hostitele Functions.

<br>
<hr/>

## <a name="4-deploy-the-function-project-to-azure"></a>4. nasazení projektu funkce do Azure

Aplikace Function App a související prostředky se vytvoří v Azure při prvním nasazení projektu Functions. Nastavení prostředků Azure vytvořených pro hostování vaší aplikace jsou definovaná v souboru *pom.xml* . V tomto článku přijměte výchozí hodnoty.

<br/>
<details>
<summary><strong>Vytvoření aplikace Function App běžící na systému Linux</strong></summary>

Chcete-li vytvořit aplikaci Function App běžící na systému Linux namísto systému Windows, změňte `runtime.os` prvek v souboru *pom.xml* z `windows` na `linux` . V [těchto oblastech](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions)se podporuje spuštění Linux v plánu spotřeby. Nemůžete mít aplikace spuštěné v systému Linux a aplikace, které běží ve Windows ve stejné skupině prostředků.
</details>

1. Než budete moct nasadit, přihlaste se k předplatnému Azure pomocí rozhraní příkazového řádku Azure CLI nebo Azure PowerShell. 

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Příkaz [AZ Login](/cli/azure/reference-index#az-login) vás přihlásí k účtu Azure.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    Rutina [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) vás přihlásí k účtu Azure.

    ---

1. K nasazení projektu do nové aplikace Function App použijte následující příkaz.

    ```console
    mvn azure-functions:deploy
    ```

    Nasazení zabalí soubory projektu a nasadí je do nové aplikace Function App pomocí [nasazení zip](functions-deployment-technologies.md#zip-deploy). Kód se spouští z balíčku pro nasazení v Azure.

<br/>
<details>
<summary><strong>Co se vytvořilo v Azure?</strong></summary>

+ Skupina prostředků. Název jako _Java-Functions-Group_.
+ Účet úložiště. Požadováno funkcemi. Název se vygeneruje náhodně na základě požadavků na název účtu úložiště.
+ Plán hostování. Hostování bez serveru pro aplikaci Function App v oblasti _westus_ Název je _Java-Functions-App-Service-Plan_.
+ Aplikace Function App Function App je jednotka pro nasazení a spouštění pro vaše funkce. Název se náhodně generuje na základě vašeho _artifactId_ a připojuje se k náhodně vygenerovanému číslu.
</details>

<br>
<hr/>

## <a name="5-invoke-the-function-on-azure"></a>5. vyvolejte funkci v Azure

Vzhledem k tomu, že funkce používá Trigger HTTP, **vyvoláte ji pomocí požadavku HTTP na jeho adresu URL** v prohlížeči nebo pomocí nástroje jako <abbr title="Nástroj příkazového řádku pro generování požadavků HTTP na adresu URL; si https://curl.se/">curl</abbr>.

# <a name="browser"></a>[Prohlížeč](#tab/browser)

Zkopírujte úplnou **adresu URL pro vyvolání** zobrazenou ve výstupu `publish` příkazu do adresního řádku prohlížeče a přidejte parametr dotazu `&name=Functions` . V prohlížeči by se měl zobrazit podobný výstup jako při spuštění funkce místně.

![Výstup funkce spuštěné v Azure v prohlížeči](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Spusťte [`curl`](https://curl.haxx.se/) s **adresou URL vyvolání** a přidejte parametr `&name=Functions` . Výstupem příkazu by měl být text "Hello".

![Výstup funkce spuštěný v Azure pomocí kudrlinkou](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

<br>
<hr/>

## <a name="6-clean-up-resources"></a>6. vyčištění prostředků

Pokud budete pokračovat k [dalšímu kroku](#next-steps) a přidáte Azure Storage <abbr title="V Azure Storage znamená přidružení funkce k frontě úložiště, aby mohla vytvářet zprávy ve frontě.">výstupní vazba fronty</abbr>, zachovejte všechny vaše prostředky na místě, jak budete vytvářet informace o tom, co jste už provedli.

Jinak pomocí následujícího příkazu odstraňte skupinu prostředků a všechny její obsažené prostředky, abyste se vyhnuli vzniku dalších nákladů.

 # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name java-functions-group
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name java-functions-group
```

---

<br>
<hr/>

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Připojení k frontě Azure Storage](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
