---
title: Vývoj funkcí Azure pomocí kódu Visual Studia
description: Zjistěte, jak vyvíjet a testovat funkce Azure pomocí rozšíření Azure Functions pro visual studio kód.
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 2d33b7dddf29d37d174bdb7734e9048bc1658840
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277164"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Vývoj funkcí Azure pomocí kódu Visual Studia

[Rozšíření Azure Functions pro kód Visual Studia] umožňuje místně vyvíjet funkce a nasazovat je do Azure. Pokud toto prostředí je vaše první s Funkcemi Azure, můžete se [dozvědět](functions-overview.md)více na úvod do Funkce Azure .

Rozšíření Azure Functions poskytuje tyto výhody:

* Upravujte, vytvářejte a spouštějte funkce v místním vývojovém počítači.
* Publikujte svůj projekt Azure Functions přímo do Azure.
* Napište své funkce v různých jazycích a využijte výhod kódu sady Visual Studio.

Rozšíření lze použít s následujícími jazyky, které jsou podporovány modulem runtime Azure Functions začínajícím verzí 2.x:

* [C# zkompilován](functions-dotnet-class-library.md)
* [Skript Jazyka C#](functions-reference-csharp.md)<sup>*</sup>
* [Javascript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>Vyžaduje, abyste [nastavili skript Jazyka C# jako výchozí jazyk projektu](#c-script-projects).

V tomto článku jsou příklady aktuálně k dispozici pouze pro funkce knihovny tříd JavaScript (Node.js) a C#.  

Tento článek obsahuje podrobnosti o tom, jak používat rozšíření Azure Functions k vývoji funkcí a jejich publikování do Azure. Než si přečtete tento článek, měli byste [vytvořit první funkci pomocí kódu sady Visual Studio](functions-create-first-function-vs-code.md).

> [!IMPORTANT]
> Nekombinujte místní vývoj a vývoj portálu pro aplikaci s jednou funkcí. Při publikování z místního projektu do aplikace funkce proces nasazení přepíše všechny funkce, které jste vyvinuli na portálu.

## <a name="prerequisites"></a>Požadavky

Před instalací a spuštěním rozšíření [Azure Functions Azure][Functions pro kód Visual Studio]musíte splňovat tyto požadavky:

* [Visual Studio Code](https://code.visualstudio.com/) nainstalován na jedné z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Aktivní předplatné Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Další prostředky, které potřebujete, jako je účet úložiště Azure, se vytvoří ve vašem předplatném při [publikování pomocí kódu Visual Studia](#publish-to-azure).

> [!IMPORTANT]
> Funkce můžete vyvíjet místně a publikovat je do Azure, aniž byste je museli spouštět a spouštět místně. Chcete-li spouštět funkce místně, budete muset splnit některé další požadavky, včetně automatického stažení nástrojů Azure Functions Core Tools. Další informace naleznete v tématu [Další požadavky pro místní spuštění projektu](#additional-requirements-for-running-a-project-locally).

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Vytvoření projektu Azure Functions

Rozšíření Funkce umožňuje vytvořit projekt aplikace funkce spolu s vaší první funkcí. Následující kroky ukazují, jak vytvořit funkci aktivovanou protokolem HTTP v novém projektu Functions. [Aktivační událost PROTOKOLU HTTP](functions-bindings-http-webhook.md) je nejjednodušší šablona aktivační události, kterou je třeba demonstrovat.

1. Z **Azure: Funkce**vyberte ikonu **Vytvořit funkci:**

    ![Vytvoření funkce](./media/functions-develop-vs-code/create-function.png)

1. Vyberte složku projektu aplikace funkce a **vyberte jazyk pro projekt funkce**.

1. Pokud jste základní nástroje ještě nenainstalovali, budete vyzváni k **výběru verze** základních nástrojů k instalaci. Zvolte verzi 2.x nebo novější verzi. 

1. Vyberte šablonu **aktivační funkce protokolu HTTP,** nebo můžete vybrat **Přeskočit pro tuto chvíli** a vytvořit projekt bez funkce. Funkci můžete [do projektu přidat](#add-a-function-to-your-project) vždy později.

    ![Volba šablony triggeru HTTP](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Zadejte **HttpExample** pro název funkce a vyberte Enter a pak vyberte **autorizace funkce.** Tato úroveň autorizace vyžaduje, abyste při volání koncového bodu funkce poskytli [funkční klávesu.](functions-bindings-http-webhook-trigger.md#authorization-keys)

    ![Vybrat autorizaci funkce](./media/functions-develop-vs-code/create-function-auth.png)

    Funkce je vytvořena ve zvoleném jazyce a v šabloně pro funkci spouštěnou http.

    ![Šablona funkce spouštěná http v kódu Sady Visual Studio](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>Generované soubory projektu

Šablona projektu vytvoří projekt ve zvoleném jazyce a nainstaluje požadované závislosti. Pro libovolný jazyk má nový projekt tyto soubory:

* **host.json**: Umožňuje konfigurovat hostitele Functions. Tato nastavení platí, když spouštěte funkce místně a když je spouštěte v Azure. Další informace naleznete v [tématu host.json reference](functions-host-json.md).

* **local.settings.json**: Udržuje nastavení používaná při místním spuštění funkcí. Tato nastavení se používají pouze v případě, že používáte funkce místně. Další informace naleznete v [tématu Local settings file](#local-settings-file).

    >[!IMPORTANT]
    >Vzhledem k tomu, že soubor local.settings.json může obsahovat tajné klíče, je třeba jej vyloučit ze správy zdrojového kódu projektu.

V závislosti na vašem jazyce jsou vytvořeny tyto další soubory:

# <a name="c"></a>[C\#](#tab/csharp)

* [HttpExample.cs soubor knihovny tříd,](functions-dotnet-class-library.md#functions-class-library-project) který implementuje funkci.

# <a name="javascript"></a>[Javascript](#tab/nodejs)

* Soubor package.json v kořenové složce.

* Složka HttpExample, která obsahuje [definiční soubor function.json](functions-reference-node.md#folder-structure) a [soubor index.js](functions-reference-node.md#exporting-a-function), soubor Node.js obsahující kód funkce.

<!-- # [PowerShell](#tab/powershell)

* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the run.ps1 file, which contains the function code.
 
# [Python](#tab/python)
    
* A project-level requirements.txt file that lists packages required by Functions.
    
* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the \_\_init\_\_.py file, which contains the function code.
     -->
---

V tomto okamžiku můžete přidat vstupní a výstupní vazby do funkce [úpravou souboru function.json](#add-a-function-to-your-project) nebo [přidáním parametru do funkce knihovny tříd jazyka C#](#add-a-function-to-your-project).

Do projektu můžete také [přidat novou funkci](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Instalace rozšíření vazby

S výjimkou aktivačních událostí protokolu HTTP a časovače jsou vazby implementovány v rozšiřujících balíčcích. Je nutné nainstalovat balíčky rozšíření pro aktivační události a vazby, které je potřebují. Proces instalace rozšíření vazby závisí na jazyku projektu.

# <a name="c"></a>[C\#](#tab/csharp)

Spusťte příkaz [dotnet add package](/dotnet/core/tools/dotnet-add-package) v okně Terminálu a nainstalujte balíčky rozšíření, které potřebujete v projektu. Následující příkaz nainstaluje rozšíření Azure Storage, které implementuje vazby pro úložiště objektů Blob, Fronta a tabulka.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="javascript"></a>[Javascript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>Přidání funkce do projektu

Novou funkci můžete přidat do existujícího projektu pomocí jedné z předdefinovaných šablon aktivačních událostí. Chcete-li přidat novou aktivační událost funkce, vyberte Klávesu F1, chcete-li otevřít paletu příkazů, a pak vyhledejte a spusťte příkaz **Funkce Azure: Vytvořit funkci**. Podle pokynů zvolte typ aktivační události a definujte požadované atributy aktivační události. Pokud aktivační událost vyžaduje přístupový klíč nebo připojovací řetězec pro připojení ke službě, připravte si ji před vytvořením aktivační události funkce.

Výsledky této akce závisí na jazyku projektu:

# <a name="c"></a>[C\#](#tab/csharp)

Do projektu je přidán nový soubor knihovny tříd jazyka C#.

# <a name="javascript"></a>[Javascript](#tab/nodejs)

V projektu je vytvořena nová složka. Složka obsahuje nový soubor function.json a nový soubor kódu JavaScript.

---

## <a name="add-input-and-output-bindings"></a>Přidání vstupních a výstupních vazeb

Funkci můžete rozšířit přidáním vstupních a výstupních vazeb. Proces přidávání vazeb závisí na jazyku projektu. Další informace o vazbách najdete v [tématu Azure Functions triggers and bindings concepts](functions-triggers-bindings.md).

Následující příklady se připojují k frontě úložiště s názvem `outqueue` `MyStorageConnection` , kde je připojovací řetězec pro účet úložiště nastaven v nastavení aplikace v local.settings.json.

# <a name="c"></a>[C\#](#tab/csharp)

Aktualizujte metodu funkce a přidejte do definice `Run` metody následující parametr:

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

Tento kód vyžaduje, abyste `using` přidali následující příkaz:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

Parametr `msg` je `ICollector<T>` typ, který představuje kolekci zpráv, které jsou zapsány do výstupní vazby po dokončení funkce. Přidáte jednu nebo více zpráv do kolekce. Tyto zprávy jsou odesílány do fronty po dokončení funkce.

Další informace naleznete v dokumentaci [k vazbě výstupu úložiště fronty.](functions-bindings-storage-queue-output.md)

# <a name="javascript"></a>[Javascript](#tab/nodejs)

Visual Studio Code umožňuje přidat vazby do souboru function.json pomocí pohodlné sady výzev. Chcete-li vytvořit vazbu, klepněte pravým tlačítkem myši (Ctrl+klikněte na macOS) na soubor **function.json** ve složce funkce a vyberte **Přidat vazbu**:

![Přidání vazby k existující funkci JavaScriptu ](media/functions-develop-vs-code/function-add-binding.png)

Následují příklady výzev k definování nové vazby výstupu úložiště:

| Výzva | Hodnota | Popis |
| -------- | ----- | ----------- |
| **Vybrat směr vazby** | `out` | Vazba je výstupní vazba. |
| **Výběr vazby se směrem** | `Azure Queue Storage` | Vazba je vazby fronty Azure Storage. |
| **Název použitý k identifikaci této vazby v kódu** | `msg` | Název, který identifikuje parametr vazby odkazovaný ve vašem kódu. |
| **Fronta, do které bude zpráva odeslána** | `outqueue` | Název fronty, do které vazba zapisuje. Pokud *queueName* neexistuje, vazba vytvoří při prvním použití. |
| **Vybrat nastavení z "local.settings.json"** | `MyStorageConnection` | Název nastavení aplikace, která obsahuje připojovací řetězec pro účet úložiště. Toto `AzureWebJobsStorage` nastavení obsahuje připojovací řetězec pro účet úložiště, který jste vytvořili pomocí aplikace funkce. |

V tomto příkladu je do `bindings` pole v souboru function.json přidána následující vazba:

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

Můžete také přidat stejnou definici vazby přímo do souboru function.json.

V kódu funkce `msg` je vazba přístupná z `context`, jako v tomto příkladu:

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

Další informace naleznete v odkazu [na vazbu výstupu úložiště fronty.](functions-bindings-storage-queue-output.md)

---

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Publikování aplikací do Azure

Visual Studio Code umožňuje publikovat projekt functions přímo do Azure. Během postupu vytvoříte aplikaci funkcí a související prostředky ve vašem předplatném Azure. Aplikace funkcí poskytuje kontext spuštění pro vaše funkce. Projekt se zabalí a nasadí do nové aplikace funkcí ve vašem předplatném Azure.

Když publikujete z Visual Studio Code do nové aplikace funkcí v Azure, je vám nabídnuta cesta k vytvoření aplikace s rychlou funkcí a pokročilá cesta. 

Při publikování z Visual Studio Code, můžete využít technologie [nasazení ZIP.](functions-deployment-technologies.md#zip-deploy) 

### <a name="quick-function-app-create"></a>Rychlé vytvoření aplikace pro rychlé funkce

Když zvolíte **+ Vytvořit novou aplikaci funkcí v Azure...**, rozšíření automaticky generuje hodnoty pro prostředky Azure, které vaše aplikace funkce potřebuje. Tyto hodnoty jsou založeny na názvu aplikace funkce, kterou zvolíte. Příklad použití výchozích hodnot k publikování projektu do nové aplikace funkcí v Azure najdete v [článku rychlého startu kódu visual studia](functions-create-first-function-vs-code.md#publish-the-project-to-azure).

Pokud chcete zadat explicitní názvy vytvořených prostředků, musíte zvolit cestu rozšířeného vytvoření.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>Publikování projektu do nové aplikace funkcí v Azure pomocí pokročilých možností

Následující kroky publikují projekt do nové aplikace pro funkce vytvořené pomocí rozšířených možností vytváření:

1. V oblasti **Azure: Funkce** vyberte ikonu **Nasadit do funkce aplikace.**

    ![Nastavení aplikace funkcí](./media/functions-develop-vs-code/function-app-publish-project.png)

1. Pokud nejste přihlášení, budete vyzváni k **přihlášení do Azure**. Můžete si taky **vytvořit bezplatný účet Azure**. Po přihlášení z prohlížeče se vraťte ke kódu sady Visual Studio.

1. Pokud máte více předplatných, **vyberte předplatné** pro aplikaci funkce a pak vyberte **+ Vytvořit novou aplikaci funkce v Azure... _Pokročilé_**. Tato _možnost Upřesnit_ poskytuje větší kontrolu nad prostředky, které vytvoříte v Azure. 

1. V následujících výzvách zadejte tyto informace:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Výběr aplikace funkcí v Azure | Vytvoření aplikace nové funkce v Azure | Na další výzvu zadejte globálně jedinečný název, který identifikuje vaši novou aplikaci funkcí, a pak vyberte Enter. Platné znaky pro název aplikace funkcí jsou `a-z`, `0-9` a `-`. |
    | Výběr operačního příkazu | Windows | Aplikace funkce běží ve Windows. |
    | Vyberte hostingový plán | Plán Consumption | Používá se [hosting plánu spotřeby](functions-scale.md#consumption-plan) bez serveru. |
    | Výběr runtime pro novou aplikaci | Váš jazyk projektu | Za běhu musí odpovídat projektu, který publikujete. |
    | Výběr skupiny prostředků pro nové zdroje | Vytvořit novou skupinu prostředků | Na další výzvu zadejte název `myResourceGroup`skupiny prostředků, třeba , a pak vyberte enter. Můžete také vybrat existující skupinu prostředků. |
    | Výběr účtu úložiště | Vytvoření nového účtu úložiště | Na další výzvu zadejte globálně jedinečný název nového účtu úložiště, který používá vaše aplikace pro funkce, a pak vyberte Enter. Názvy účtů úložiště musí mít dlouhou 3 až 24 znaků a mohou obsahovat pouze čísla a malá písmena. Můžete také vybrat existující účet. |
    | Výběr umístění pro nové zdroje | oblast | Vyberte umístění v [oblasti](https://azure.microsoft.com/regions/) ve vašem okolí nebo v blízkosti jiných služeb, ke kterým vaše funkce přistupují. |

    Po vytvoření aplikace funkce a použití balíčku nasazení se zobrazí oznámení. Vyberte **Zobrazit výstup** v tomto oznámení zobrazíte výsledky vytváření a nasazení, včetně prostředků Azure, které jste vytvořili.

## <a name="republish-project-files"></a>Opětovné publikování souborů projektu

Když nastavíte [průběžné nasazování](functions-continuous-deployment.md), vaše aplikace funkcí v Azure se aktualizuje při každé aktualizaci zdrojových souborů v připojeném zdrojovém umístění. Doporučujeme průběžné nasazení, ale můžete také znovu publikovat aktualizace souboru projektu z Visual Studio Code.

> [!IMPORTANT]
> Publikování do existující aplikace funkcí přepíše obsah této aplikace v Azure.

1. V kódu visual ateliéru otevřete paletu příkazů vyberte F1. V paletě příkazů vyhledejte a vyberte **Azure Functions: Deploy to function app**.

1. Pokud nejste přihlášení, budete vyzváni k **přihlášení do Azure**. Po přihlášení z prohlížeče se vraťte na Visual Studio Code. Pokud máte více předplatných, **vyberte předplatné,** které obsahuje vaši aplikaci funkce.

1. VYberte stávající aplikaci funkcí v Azure. Když budete upozorněni na přepsání všech souborů v aplikaci funkce, vyberte **Nasadit,** abyste potvrdili upozornění a pokračovali.

Projekt se znovu sestaví, přebalí a nahraje do Azure. Stávající projekt je nahrazen nový balíček a aplikace funkce restartuje.

## <a name="get-the-url-of-the-deployed-function"></a>Získání adresy URL nasazené funkce

Chcete-li volat funkci aktivovanou protokolem HTTP, potřebujete adresu URL funkce, když se nasadí do aplikace funkce. Tato adresa URL obsahuje všechny požadované [funkční klávesy](functions-bindings-http-webhook-trigger.md#authorization-keys). Pomocí rozšíření můžete získat tyto adresy URL pro nasazené funkce.

1. Výběrem klávesy F1 otevřete paletu příkazů a pak vyhledejte a spusťte příkaz **Azure Functions: Copy Function URL**.

1. Podle pokynů vyberte aplikaci funkce v Azure a potom konkrétní aktivační událost HTTP, kterou chcete vyvolat.

Adresa URL funkce je zkopírována do schránky spolu `code` se všemi požadovanými klíči předanými parametrem dotazu. Pomocí nástroje HTTP odesílejte požadavky POST nebo prohlížeč pro požadavky GET do vzdálené funkce.  

## <a name="run-functions-locally"></a>Spouštět funkce místně

Rozšíření Azure Functions umožňuje spustit projekt Functions v místním vývojovém počítači. Místní runtime je stejný runtime, který hostuje vaši funkční aplikaci v Azure. Místní nastavení jsou čtena ze [souboru local.settings.json](#local-settings-file).

### <a name="additional-requirements-for-running-a-project-locally"></a>Další požadavky na místní spuštění projektu

Chcete-li spustit projekt Functions místně, musíte splnit tyto další požadavky:

* Nainstalujte verzi 2.x nebo novější z [nástrojů Azure Functions Core Tools](functions-run-local.md#v2). Balíček Nástroje jádra se stáhne a nainstaluje automaticky při spuštění projektu místně. Základní nástroje zahrnují celou dobu runtime Funkce Azure, takže stahování a instalace může nějakou dobu trvat.

* Nainstalujte konkrétní požadavky pro vámi zvolený jazyk:

    | Jazyk | Požadavek |
    | -------- | --------- |
    | **C #** | [Rozšíření Jazyka C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)<br/>[Nástroje .NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Ladicí program pro rozšíření Javy](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3 nebo novější](https://maven.apache.org/) |
    | **Javascript** | [Node.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Rozšíření Pythonu](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[Python 3.6.8](https://www.python.org/downloads/) doporučeno|

    <sup>*</sup>Aktivní LTS a údržba LTS (8.11.1 a 10.14.1 doporučeno).

### <a name="configure-the-project-to-run-locally"></a>Konfigurace projektu pro místní spuštění

Za běhu Functions používá účet Úložiště Azure interně pro všechny typy aktivačních událostí než HTTP a webhooky. Takže je potřeba nastavit klíč **Values.AzureWebJobsStorage** na platný připojovací řetězec účtu Azure Storage.

Tato část používá [rozšíření úložiště Azure pro kód Visual Studia](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) s [Průzkumníkem úložiště Azure](https://storageexplorer.com/) pro připojení a načtení připojovacího řetězce úložiště.

Nastavení připojovacího řetězce účtu úložiště:

1. V Sadě Visual Studio **otevřete Průzkumníka Cloud**, > **rozbalte účet úložiště, váš účet úložiště**, a pak vyberte **Vlastnosti** a zkopírujte hodnotu **Primární připojovací řetězec.** **Storage Account**

2. V projektu otevřete soubor local.settings.json a nastavte hodnotu klíče **AzureWebJobsStorage** na zkopírovaný připojovací řetězec.

3. Opakováním předchozího kroku přidejte do pole **Hodnoty** jedinečné klíče pro všechna ostatní připojení vyžadovaná vašimi funkcemi.

Další informace naleznete v [tématu Local settings file](#local-settings-file).

### <a name="debugging-functions-locally"></a>Ladění funkcí místně  

Chcete-li ladit funkce, vyberte možnost F5. Pokud jste ještě nestáhli [základní nástroje][nástroje Azure Functions Core Tools ,]budete vyzváni k tomu. Když je nainstalována a spuštěna základní nástroje, výstup se zobrazí v terminálu. To je stejné jako `func host start` spuštění příkazu Základní nástroje z terminálu, ale s dalšími úlohami sestavení a připojeným ladicím programem.  

Když je projekt spuštěn, můžete aktivovat vaše funkce, jako při nasazení projektu do Azure. Při spuštění projektu v režimu ladění, zarážky jsou přístupů v kódu sady Visual Studio, podle očekávání.

Adresa URL požadavku na aktivační události HTTP se zobrazí ve výstupu v terminálu. Funkční klávesy pro aktivační události HTTP se nepoužívají, pokud je projekt spuštěn místně. Další informace najdete [v tématu Strategies for testing your code in Azure Functions](functions-test-a-function.md).  

Další informace najdete v [tématu Práce s nástroji Azure Functions]Core Nástroje Azure Základní[nástroje].

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Ve výchozím nastavení se tato nastavení při publikování projektu do Azure nemigrují automaticky. Po dokončení publikování máte možnost publikovat nastavení z local.settings.json do aplikace funkce v Azure. Další informace najdete v [tématu Publikování nastavení aplikace](#publish-application-settings).

Hodnoty v **připojovacích řetězcích** nejsou nikdy publikovány.

Hodnoty nastavení aplikace funkce lze také číst v kódu jako proměnné prostředí. Další informace naleznete v části Proměnné prostředí v těchto referenčních článcích specifických pro daný jazyk:

* [Předkompilováno.](functions-dotnet-class-library.md#environment-variables)
* [Skript jazyka C# (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [Javascript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Nastavení aplikací v Azure

Nastavení v souboru local.settings.json v projektu by mělo být stejné jako nastavení aplikace v aplikaci funkce v Azure. Všechna nastavení, která přidáte do local.settings.json, musí být také přidána do aplikace funkce v Azure. Tato nastavení se při publikování projektu nenahrají automaticky. Stejně tak všechna nastavení, která vytvoříte v aplikaci funkce [na portálu,](functions-how-to-use-azure-function-app-settings.md#settings) musí být stažena do místního projektu.

### <a name="publish-application-settings"></a>Publikovat nastavení aplikace

Nejjednodušší způsob, jak publikovat požadovaná nastavení do aplikace funkce v Azure, je použít odkaz **Nastavení nahrávání,** který se zobrazí po publikování projektu:

![Nahrát nastavení aplikace](./media/functions-develop-vs-code/upload-app-settings.png)

Nastavení můžete také publikovat pomocí příkazu **Azure Functions: Upload Local Setting** v paletě příkazů. Jednotlivá nastavení nastavení aplikací v Azure můžete přidat pomocí příkazu **Azure Functions: Add New Setting** .

> [!TIP]
> Před publikováním soubor local.settings.json nezapomeňte uložit.

Pokud je místní soubor zašifrován, dešifrován, publikován a znovu zašifrován. Pokud existují nastavení, která mají konfliktní hodnoty ve dvou umístěních, budete vyzváni k výběru, jak postupovat.

Zobrazení existujících nastavení aplikace v oblasti **Azure: Funkce** rozšířením předplatného, aplikace funkcí a **nastavení aplikace**.

![Zobrazení nastavení aplikace funkcí v kódu Visual Studia](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Nastavení stahování z Azure

Pokud jste v Azure vytvořili nastavení aplikací, můžete si je stáhnout do souboru local.settings.json pomocí příkazu **Azure Functions: Download Remote Settings** .

Stejně jako při nahrávání, pokud je místní soubor zašifrován, dešifrován, aktualizován a znovu zašifrován. Pokud existují nastavení, která mají konfliktní hodnoty ve dvou umístěních, budete vyzváni k výběru, jak postupovat.

## <a name="monitoring-functions"></a>Monitorovací funkce

Při [místním spuštění funkcí](#run-functions-locally)jsou data protokolu přenášena do terminálové konzole. Můžete také získat data protokolu, když váš projekt functions běží v aplikaci funkce v Azure. Můžete se buď připojit k protokolům streamování v Azure, abyste viděli data protokolu téměř v reálném čase, nebo můžete povolit Application Insights pro úplnější pochopení toho, jak se vaše aplikace funkce chová.

### <a name="streaming-logs"></a>Streamovací protokoly

Při vývoji aplikace je často užitečné zobrazit informace o protokolování v téměř reálném čase. Můžete zobrazit datový proud souborů protokolu generovaných funkcemi. Tento výstup je příkladem datových proudů protokolů pro požadavek na funkci spouštěnou protokolem HTTP:

![Výstup datových protokolů pro aktivační událost PROTOKOLU HTTP](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

Další informace naleznete v [tématu Streamování protokolů](functions-monitoring.md#streaming-logs).

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Protokoly streamování podporují pouze jednu instanci hostitele Functions. Při škálování funkce na více instancí se v datovém proudu protokolu nezobrazí data z jiných instancí. [Živé metriky Stream](../azure-monitor/app/live-stream.md) v Application Insights podporuje více instancí. Zatímco také v téměř reálném čase, streamování analýzy je založena na [vzorkovaných dat](functions-monitoring.md#configure-sampling).

### <a name="application-insights"></a>Application Insights

Doporučujeme sledovat provádění funkcí integrací aplikace funkcí s Application Insights. Když na webu Azure Portal vytvoříte aplikaci funkcí, k této integraci ve výchozím nastavení dojde. Když vytvoříte aplikaci funkcí během publikování v Sadě Visual Studio, musíte sami integrovat Application Insights.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Další informace najdete [v tématu Monitorování funkcí Azure](functions-monitoring.md).

## <a name="c-script-projects"></a>C\# skript projekty

Ve výchozím nastavení jsou všechny projekty jazyka C# vytvořeny jako [projekty zkompilované knihovny tříd jazyka C#](functions-dotnet-class-library.md). Pokud místo toho dáváte přednost práci s projekty skriptů jazyka C#, musíte v nastavení rozšíření Azure Functions vybrat skript C#jako výchozí jazyk:

1. Vyberte**Nastavení****předvoleb** >  **souborů** > .

1. Přejděte na**Rozšíření uživatelských** >  **nastavení** > **Azure Functions**.

1. Vyberte **C#Script** z **Funkce Azure: Jazyk projektu**.

Po dokončení těchto kroků volání základní nástroje zahrnují `--csx` možnost, která generuje a publikuje c# skript (.csx) soubory projektu. Pokud máte tento výchozí jazyk zadán, všechny projekty, které vytvoříte výchozí c# skript projekty. Při nastavení výchozího nastavení nebudete vyzváni k výběru jazyka projektu. Chcete-li vytvořit projekty v jiných jazycích, je nutné toto nastavení změnit nebo odebrat ze souboru user settings.json. Po odebrání tohoto nastavení budete při vytváření projektu znovu vyzváni k výběru jazyka.

## <a name="command-palette-reference"></a>Odkaz na paletu příkazů

Rozšíření Funkce Azure poskytuje užitečné grafické rozhraní v oblasti pro interakci s aplikacemi funkce v Azure. Stejné funkce jsou k dispozici také jako příkazy v paletě příkazů (F1). Tyto příkazy Azure Functions jsou k dispozici:

|Azure Functions, příkaz  | Popis  |
|---------|---------|
|**Přidat nové nastavení**  |  Vytvoří nové nastavení aplikace v Azure. Další informace najdete v [tématu Publikování nastavení aplikace](#publish-application-settings). Toto nastavení může být nutné [stáhnout také do místního nastavení](#download-settings-from-azure). |
| **Konfigurace zdroje nasazení** | Připojí vaši funkční aplikaci v Azure k místnímu úložišti Git. Další informace najdete [v tématu Průběžné nasazení pro funkce Azure](functions-continuous-deployment.md). |
| **Připojení k úložišti GitHub** | Připojí vaši funkční aplikaci k úložišti GitHub. |
| **Kopírovat adresu URL funkce** | Získá vzdálenou adresu URL funkce aktivované HTTP, která běží v Azure. Další informace najdete [v tématu Získání adresy URL nasazené funkce](#get-the-url-of-the-deployed-function). |
| **Vytvoření aplikace funkcí v Azure** | Vytvoří novou aplikaci funkcí ve vašem předplatném v Azure. Další informace najdete v části o [tom,](#publish-to-azure)jak publikovat do nové aplikace funkcí v Azure .        |
| **Dešifrování nastavení** | Dešifruje [místní nastavení,](#local-settings-file) která byla zašifrována pomocí **Azure Functions: Encrypt Settings**.  |
| **Odstranit aplikaci funkce** | Odebere aplikaci funkce z předplatného v Azure. Pokud v plánu služby App Service nejsou žádné jiné aplikace, máte možnost ji také odstranit. Ostatní prostředky, jako jsou účty úložiště a skupiny prostředků, se neodstraní. Chcete-li odebrat všechny prostředky, měli byste místo toho [odstranit skupinu prostředků](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). Váš místní projekt není ovlivněn. |
|**Funkce Odstranit**  | Odebere existující funkci z aplikace funkce v Azure. Vzhledem k tomu, že toto odstranění nemá vliv na místní projekt, zvažte místo toho odebrání funkce místně a [opětovné publikování projektu](#republish-project-files). |
| **Odstranit proxy server** | Odebere proxy server Azure Functions z vaší aplikace funkce v Azure. Další informace o proxy serverech najdete v [tématu Práce s azure functions proxy servery](functions-proxies.md). |
| **Nastavení odstranit** | Odstraní nastavení aplikace funkce v Azure. Toto odstranění nemá vliv na nastavení v souboru local.settings.json. |
| **Odpojit od repo**  | Odebere [nepřetržité připojení k nasazení](functions-continuous-deployment.md) mezi aplikací funkce v Azure a úložištěm správy zdrojového kódu. |
| **Stáhnout vzdálená nastavení** | Stáhne nastavení z vybrané aplikace funkce v Azure do souboru local.settings.json. Pokud je místní soubor zašifrován, dešifrován, aktualizován a znovu zašifrován. Pokud existují nastavení, která mají konfliktní hodnoty ve dvou umístěních, budete vyzváni k výběru, jak postupovat. Před spuštěním tohoto příkazu nezapomeňte uložit změny do souboru local.settings.json. |
| **Upravit nastavení** | Změní hodnotu existujícího nastavení aplikace funkce v Azure. Tento příkaz nemá vliv na nastavení v souboru local.settings.json.  |
| **Šifrovat nastavení** | Šifruje jednotlivé `Values` položky v poli v [místním nastavení](#local-settings-file). V tomto `IsEncrypted` souboru je `true`také nastavena na , která určuje, že místní runtime bude dešifrovat nastavení před jejich použitím. Šifrujte místní nastavení, abyste snížili riziko úniku cenných informací. V Azure se nastavení aplikací vždy ukládá zašifrovaná. |
| **Spustit funkci nyní** | Ručně spustí [funkci aktivovanou časovačem](functions-bindings-timer.md) v Azure. Tento příkaz se používá pro testování. Další informace o spouštění funkcí bez protokolu HTTP v Azure najdete v [tématu Ruční spuštění funkce aktivované protokolem HTTP](functions-manually-run-non-http.md). |
| **Inicializovat projekt pro použití s kódem VS** | Přidá požadované soubory projektu Visual Studio Code do existujícího projektu Functions. Tento příkaz slouží k práci s projektem, který jste vytvořili pomocí základních nástrojů. |
| **Instalace nebo aktualizace základních nástrojů funkcí Azure** | Nainstaluje nebo aktualizuje [nástroje Azure Functions Core Tools], které se používají ke spuštění funkcí místně. |
| **Opětovné nasazení**  | Umožňuje znovu nasadit soubory projektu z připojeného úložiště Git do konkrétního nasazení v Azure. Chcete-li znovu publikovat místní aktualizace z kódu sady Visual Studio, [znovu publikujte projekt](#republish-project-files). |
| **Přejmenovat nastavení** | Změní název klíče existující funkce nastavení aplikace v Azure. Tento příkaz nemá vliv na nastavení v souboru local.settings.json. Po přejmenování nastavení v Azure, měli byste [stáhnout tyto změny do místního projektu](#download-settings-from-azure). |
| **Restartování** | Restartuje aplikaci funkce v Azure. Nasazení aktualizací také restartuje aplikaci funkce. |
| **Nastavení azurewebjobs**| Nastaví hodnotu `AzureWebJobsStorage` nastavení aplikace. Toto nastavení je vyžadováno funkcemi Azure. Nastaví se, když se v Azure vytvoří aplikace pro funkce. |
| **Zahájení** | Spustí aplikaci zastavené funkce v Azure. |
| **Spustit protokoly datových proudů** | Spustí protokoly streamování pro aplikaci funkce v Azure. Protokoly datových proudů používejte během vzdáleného řešení potíží v Azure, pokud potřebujete zobrazit informace o protokolování v téměř reálném čase. Další informace naleznete v [tématu Streamování protokolů](#streaming-logs). |
| **Zastavit** | Zastaví aplikaci funkcí, která běží v Azure. |
| **Zastavit protokoly datových proudů** | Zastaví protokoly streamování pro aplikaci funkce v Azure. |
| **Přepnout jako nastavení slotu** | Pokud je povoleno, zajišťuje, že nastavení aplikace přetrvává pro daný slot nasazení. |
| **Odinstalace základních nástrojů Azure Functions** | Odebere základní nástroje Azure Functions, které je vyžadováno rozšířením. |
| **Nahrát místní nastavení** | Nahraje nastavení ze souboru local.settings.json do vybrané aplikace funkce v Azure. Pokud je místní soubor zašifrován, dešifrován, nahrán a znovu zašifrován. Pokud existují nastavení, která mají konfliktní hodnoty ve dvou umístěních, budete vyzváni k výběru, jak postupovat. Před spuštěním tohoto příkazu nezapomeňte uložit změny do souboru local.settings.json. |
| **Zobrazit potvrzení na GitHubu** | Zobrazuje nejnovější potvrzení v konkrétním nasazení, když je vaše aplikace funkcí připojena k úložišti. |
| **Zobrazit protokoly nasazení** | Zobrazuje protokoly pro konkrétní nasazení do aplikace funkce v Azure. |

## <a name="next-steps"></a>Další kroky

Další informace o základních nástrojích Azure Functions najdete v [tématu Práce s nástroji Core functions Azure](functions-run-local.md).

Další informace o vývoji funkcí jako knihoven tříd .NET najdete v [tématu Azure Functions C# odkaz na vývojáře](functions-dotnet-class-library.md). Tento článek také obsahuje odkazy na příklady, jak používat atributy deklarovat různé typy vazeb podporovaných funkcemi Azure.

[Rozšíření Azure Functions pro Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Základní nástroje Azure Functions]: functions-run-local.md
