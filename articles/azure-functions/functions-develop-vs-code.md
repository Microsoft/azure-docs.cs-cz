---
title: Vývoj Azure Functions pomocí nástroje Visual Studio Code | Dokumentace Microsoftu
description: Zjistěte, jak vyvíjet a testovat funkce Azure s využitím Azure Functions rozšíření pro Visual Studio Code.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: glenga
ms.openlocfilehash: 17550e148ea61eb69a20fc6a3215dfb63b65f18e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452693"
---
# <a name="develop-azure-functions-using-visual-studio-code"></a>Vývoj Azure Functions pomocí nástroje Visual Studio Code

[Rozšíření Azure Functions pro Visual Studio Code] umožňuje místní vývoj a nasazení služby Azure functions. Pokud toto prostředí je první s využitím Azure Functions, další informace najdete v [Úvod do služby Azure Functions](functions-overview.md).

Rozšíření Azure Functions poskytuje následující výhody: 

* Upravit, vytvářet a spouštět službu functions na místním počítači pro vývoj. 
* Projekt Azure Functions publikujte přímo do Azure. 
* Zápis funkce přitom všechny výhody Visual Studio Code má v různých jazycích. 

Rozšíření lze použít s následujícími jazyky podporované modulem runtime verze 2.x Azure Functions: 

* [C#kompilaci](functions-dotnet-class-library.md) 
* [Skript jazyka C#](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>Je nutné, kterou jste [nastavit C# skript jako výchozí jazyk projektu](#c-script-projects).

V tomto článku najdete příklady jsou aktuálně dostupné pouze pro jazyk JavaScript (Node.js) a C# třídy funkce knihovny.  

Tento článek obsahuje podrobnosti o tom, jak pomocí rozšíření Azure Functions můžete vyvíjet funkce a publikovat je do Azure. Předtím, než se pustíte do čtení tohoto článku, měli byste [vytvoření první funkce pomocí nástroje Visual Studio Code](functions-create-first-function-vs-code.md).

> [!IMPORTANT]
> Nekombinujte místní vývoj pomocí portálu ve stejné aplikaci function app. Při publikování z místní projekt aplikace function app, procesu nasazení přepíše všechny funkce, které jste vytvořili na portálu.

## <a name="prerequisites"></a>Požadavky

Před instalací a spustit [rozšíření Azure Functions][rozšíření azure functions pro visual studio code], musí splňovat následující požadavky:

* [Visual Studio Code](https://code.visualstudio.com/) nainstalována na jednom z [podporované platformy](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Aktivní předplatné Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Další materiály, které potřebujete, jako je například účet služby Azure Storage jsou vytvořeny ve vašem předplatném při vám [publikovat pomocí Visual Studio Code](#publish-to-azure).

> [!IMPORTANT]
> Můžete vyvíjet funkce místně a publikovat do Azure bez nutnosti spuštění a spouštíte je místně. Existují další požadavky na spuštění vašich funkční místně, včetně automatické stahování nástrojů Azure Functions Core. Další informace najdete v tématu [další požadavky na místní spuštění](#additional-requirements-to-run-locally). 

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Vytvoření projektu Azure Functions

Funkce rozšíření umožňuje vytvářet projekt aplikace funkcí, spolu s první funkce. Následující kroky ukazují, jak v nového projektu functions vytvořit funkci aktivovanou protokolem HTTP. [HTTP trigger](functions-bindings-http-webhook.md) je nejjednodušší aktivační událost šablony funkce k předvedení.

1. Z **Azure: Funkce**, zvolte ikonu Create Function.

    ![Vytvoření funkce](./media/functions-develop-vs-code/create-function.png)

1. Vyberte složku pro váš projekt aplikace funkcí a potom **vyberte jazyk pro projekt funkcí**. 

1. Vyberte **triggeru HTTP** šablony funkce, nebo se můžete rozhodnout k **zatím přeskočit** vytvoření projektu bez funkce. Vždy můžete [přidání funkce do vašeho projektu](#add-a-function-to-your-project) později. 

    ![Volba šablony triggeru HTTP](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Typ `HTTPTrigger` pro název funkce a potom stiskněte klávesu Enter, pak vyberte **funkce** autorizace. Tato úroveň autorizace vyžaduje, abychom vám poskytli [klíč funkce](functions-bindings-http-webhook.md#authorization-keys) při volání koncového bodu funkce.

    ![Vybrat ověřování – funkce](./media/functions-develop-vs-code/create-function-auth.png)

    Funkce ve vybraném jazyce se vytvoří pomocí šablony funkce aktivované protokolem HTTP.

    ![Šablona funkce aktivované protokolem HTTP ve Visual Studio Code](./media/functions-develop-vs-code/new-function-full.png)

Šablona projektu vytvoří projekt ve zvoleném jazyce, nainstaluje požadované závislosti. Nový projekt pro libovolný jazyk, má následující soubory:

* **host.json**: Umožňuje konfigurovat funkce hostitele. Tato nastavení platí i při spuštění místně i v Azure. Další informace najdete v tématu [referenční materiály k host.json](functions-host-json.md).

* **local.settings.json**: Udržuje nastavení používaná při místním spuštění funkce. Tato nastavení se používají pouze při místním spuštění. Další informace najdete v tématu [souboru místní nastavení](#local-settings-file).

    >[!IMPORTANT]
    >Protože souboru local.settings.json může obsahovat tajné kódy, musíte ho vyloučit ze správy zdrojových kódů pro váš projekt.

V tomto okamžiku můžete přidat vstupní a výstupní vazby funkce podle [upravovat soubor function.json](#javascript-2), nebo [přidání parametru k C# třídy funkce knihovny](#c-class-library-2).

Můžete také [přidat nové funkce do vašeho projektu](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Instalace rozšíření vazby

S výjimkou aktivační události HTTP a časovač vazby jsou implementovány v balíčky rozšíření. Je nutné nainstalovat balíčky rozšíření pro triggery a vazby, které je vyžadují. Způsob, jak nainstalovat rozšíření vazby závisí na vaší jazyk projektu.

### <a name="javascript"></a>JavaScript

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

### <a name="c-class-library"></a>C\# knihovny tříd

Spustit [se příkaz dotnet add package](/dotnet/core/tools/dotnet-add-package) příkazu v okně terminálu nainstalovat balíčky rozšíření, které potřebujete ve vašem projektu. Následující příklad nainstaluje rozšíření Azure Storage, který implementuje vazby pro objekt Blob, Queue a Table storage.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

## <a name="add-a-function-to-your-project"></a>Přidání funkce do vašeho projektu

Můžete přidat nové funkce do existujícího projektu pomocí jedné z předdefinovaných šablon funkce triggeru. Pokud chcete přidat nové funkce triggeru, stisknutím klávesy F1 otevřete paletu příkazů, vyhledejte a spusťte příkaz **Azure Functions: Vytvořte funkci...** . Postupujte podle pokynů vyberte typ své aktivační události a definovat povinné atributy se aktivační událost. Pokud trigger vyžaduje přístupový klíč nebo připojovací řetězec pro připojení ke službě, vše Připravíme před vytvořením funkce triggeru. 

Výsledky této operace závisí na jazyku projektu:

### <a name="javascript"></a>JavaScript

V projektu, který obsahuje nový soubor function.json a nový soubor kódu jazyka JavaScript je vytvořena nová složka.

### <a name="c-class-library"></a>C\# knihovny tříd

Nový C# třídy soubor knihovny (.cs) se přidá do vašeho projektu.

## <a name="add-input-and-output-bindings"></a>Přidat vstupní a výstupní vazby

Vaše funkce můžete rozšířit tak, že přidáte vstupní a výstupní vazby. Způsob, jak to provést závisí na jazyku projektu. Další informace o vazbách, v tématu [aktivace Azure Functions a vazby koncepty](functions-triggers-bindings.md). 

Následující příklady připojit do fronty úložiště s názvem `outqueue`, je-li připojovací řetězec pro účet úložiště je nastavena v `MyStorageConnection` nastavení local.settings.json aplikace. 

### <a name="javascript"></a>JavaScript

Visual Studio Code umožňuje přidat vazby do souboru function.json podle pohodlný sadu pokynů. Chcete-li vytvořit vazbu, klikněte pravým tlačítkem na (Ctrl + kliknutí v systému macOS) `function.json` souboru ve složce funkce a zvolte **přidat vazbu...** . 

![Přidat vazbu k existující funkci jazyka JavaScript ](media/functions-develop-vs-code/function-add-binding.png)

Následuje příklad výzvy k definování nové výstupní vazby úložiště:

| Výzva | Hodnota | Popis |
| -------- | ----- | ----------- |
| **Vyberte vazba – směrování** | `out` | Vazba je výstupní vazbu. |
| **Vyberte vazbu se směrem...** | `Azure Queue Storage` | Vazba je vazbu fronty Azure Storage. |
| **Název používaný k identifikaci této vazby v kódu** | `msg` | Název identifikující vazby parametru odkazovaného v kódu. |
| **Fronty, na který se pošle zpráva** | `outqueue` | Název, který vazba zapisuje do fronty. Když *queueName* neexistuje, vazba se vytvoří při prvním použití. |
| **Vyberte nastavení z "local.setting.json"** | `MyStorageConnection` | Název nastavení aplikace, které obsahuje připojovací řetězec pro účet úložiště. `AzureWebJobsStorage` Nastavení obsahuje připojovací řetězec pro účet úložiště, který jste vytvořili pomocí aplikace function app. |

V tomto příkladu se přidá následující vazby na `bindings` pole v souboru function.json:

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

Můžete také přidat stejnou definici pojmů vazbu přímo do vaší function.json.

V kódu funkce `msg` vazby přistupuje z `context`, jako v následujícím příkladu:

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

Další informace najdete v tématu [výstupní vazbu Queue storage](functions-bindings-storage-queue.md#output---javascript-example) odkaz.

### <a name="c-class-library"></a>C\# knihovny tříd

Funkce metody přidejte následující parametr pro aktualizaci `Run` definici metody:

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

Tento kód je nutné přidat následující `using` – příkaz:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

`msg` Parametr je `ICollector<T>` typ, který představuje kolekci zpráv, které jsou zapsány do výstupní vazby, když funkce skončí. Přidáte jednu nebo více zpráv do kolekce, které se odesílají do fronty po dokončení funkce.

Další informace najdete v tématu [výstupní vazbu Queue storage](functions-bindings-storage-queue.md#output---c-example) odkaz.

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="publish-to-azure"></a>Publikování do Azure

Visual Studio Code umožňuje publikovat projekt funkcí přímo do Azure. Během postupu vytvoříte aplikaci funkcí a související prostředky ve vašem předplatném Azure. Aplikace funkcí poskytuje kontext spuštění pro vaše funkce. Projekt se zabalí a nasadí do nové aplikace funkcí ve vašem předplatném Azure.

Při publikování z aplikace Visual Studio Code, se používají jedním ze dvou způsobů nasazení:

* [Zazipovat Deploy with Run-z-Package povolené](functions-deployment-technologies.md#zip-deploy): používá se pro většinu nasazení Azure Functions.
* [Adresy URL externího balíčku](functions-deployment-technologies.md#external-package-url): používá pro nasazení Linuxové aplikace na [plánu Consumption](functions-scale.md#consumption-plan).

### <a name="quick-function-app-creation"></a>Vytvoření aplikace rychlý – funkce

Ve výchozím nastavení Visual Studio Code automaticky vygeneruje hodnoty pro prostředky Azure potřebné aplikace function App. Tyto hodnoty jsou založeny na název aplikace funkcí, které zvolíte. Příklad použití výchozích hodnot pro publikování projektu do nové aplikace function app v Azure, najdete v článku [článek Rychlý start pro Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure).

Pokud chcete zadat explicitní názvy vytvořené prostředky, je nutné povolit publikování pomocí rozšířené možnosti.

### <a name="enabled-publishing-with-advanced-create-options"></a>Vytvořit povoleno publikování pomocí pokročilé možnosti

Umožňují řídit nastavení přidružené k vytváření Azure Functions aplikace, aktualizujte rozšíření Azure Functions pro umožnění upřesňující nastavení.

1. Klikněte na tlačítko **soubor > Předvolby > Nastavení**

1. Procházet **uživatelská nastavení > Rozšíření > Služba Azure Functions**

1. Zaškrtněte políčko pro **funkce Azure Functions: Pokročilé vytváření**

### <a name="publish-to-a-new-function-app-in-azure-with-advanced-creation"></a>Publikovat do nové aplikace funkcí v Azure pomocí pokročilé vytváření

Následující kroky projekt publikovat na novou aplikaci function app vytvořena pomocí rozšířené možnosti vytvoření.

1. V **Azure: Funkce** oblasti, vyberte nasazení na ikonu aplikace Function App.

    ![Nastavení aplikace – funkce](./media/functions-develop-vs-code/function-app-publish-project.png)

1. Pokud nejsou přihlášeni, zobrazí se výzva k **přihlášení k Azure**. Můžete také **vytvořit si bezplatný účet Azure**. Po úspěšném přihlášení z prohlížeče přejděte zpět do Visual Studio Code.

1. Pokud máte více předplatných, **vybrat odběr, který** pro aplikaci function app, klikněte na tlačítko **+ vytvořit novou aplikaci Function App v Azure**.

1. Po vyzvání zadejte následující informace:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Vyberte aplikaci function app v Azure | + Vytvořit novou aplikaci Function App v Azure | V dalším řádku zadejte globálně jedinečný název identifikující novou aplikaci function app a stiskněte klávesu Enter. Platné znaky pro název aplikace funkcí jsou `a-z`, `0-9` a `-`. |
    | Vyberte operační systém | Windows | Aplikace Function app běží na Windows |
    | Vyberte plán hostování | Plán Consumption | Bez serveru [spotřeby plánu hostování](functions-scale.md#consumption-plan) se používá. |
    | Vyberte modul runtime pro nové aplikace | Jazyk projektu | Modul runtime musí odpovídat projekt, který publikujete. |
    | Vyberte skupinu prostředků pro nové prostředky | Vytvořit novou skupinu prostředků | V dalším řádku, zadejte název skupiny prostředků, jako je třeba `myResourceGroup`, a stiskněte klávesu enter. Můžete také zvolit existující skupinu prostředků. |
    | Vyberte účet úložiště. | Vytvoření nového účtu úložiště | V dalším řádku, zadejte globálně jedinečný název nového účtu úložiště používá vaše aplikace function app a potom stiskněte klávesu Enter. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Můžete také zvolit existující účet. |
    | Vyberte umístění pro nové prostředky | oblast | Zvolte umístění v [oblasti](https://azure.microsoft.com/regions/) ve své blízkosti nebo v blízkosti jiných služeb, které vaše funkce využívají. |

    Po vytvoření aplikace funkcí a použití balíčku nasazení se zobrazí oznámení. Vyberte **zobrazení výstupu** toto oznámení zobrazíte vytváření a výsledky nasazení, včetně Azure prostředky, které jste vytvořili.

## <a name="republish-project-files"></a>Znovu publikovat soubory projektu

Když nastavíte [průběžné nasazování](functions-continuous-deployment.md), vaše aplikace function app v Azure se aktualizuje pokaždé, když jsou aktualizovány zdrojové soubory v umístění připojené zdroje. Přestože doporučujeme, abyste tento postup vývoje, můžete také znovu publikovat aktualizace souboru projektu z Visual Studio Code. 

> [!IMPORTANT]
> Publikování do existující aplikace funkcí přepíše obsah této aplikace v Azure.

1. Ve Visual Studio Code stisknutím klávesy F1 otevřete paletu příkazů. V paletu příkazů, vyhledejte a vyberte `Azure Functions: Deploy to function app...`.

1. Pokud nejsou přihlášeni, zobrazí se výzva k **přihlášení k Azure**. Po úspěšném přihlášení z prohlížeče přejděte zpět do Visual Studio Code. Pokud máte více předplatných, **vybrat odběr, který** , která obsahuje vaši aplikaci function app.

1. Zvolte existující aplikaci function app v Azure. Když varuje, že přepíšete všechny soubory do aplikace function App, vyberte **nasadit** potvrdit upozornění a pokračujte. 

Projekt je znovu sestavit, znovu zabalené, sadu a nahráli do Azure. Nahrazuje existující projekt nový balíček a restartuje aplikaci function app.

## <a name="get-deployed-function-url"></a>Získat adresu URL nasazené funkce

Aby bylo možné volat funkci aktivovanou protokolem HTTP, budete potřebovat adresu URL funkce při nasazení do aplikace function app. Tato adresa URL obsahuje všechny požadované [funkční klávesy](functions-bindings-http-webhook.md#authorization-keys). Rozšíření můžete použít k získání těchto adres URL pro vaše nasazené funkce.

1. klíč stisknutím klávesy F1 otevřete paletu příkazů, vyhledejte a spusťte příkaz **Azure Functions: Zkopírujte adresu URL funkce**.

1. Postupujte podle pokynů vyberte vaši aplikaci function app v Azure a pak konkrétní triggeru protokolu HTTP, které chcete vyvolat. 

Funkce, adresa URL je zkopírován do schránky, spolu s předaným pomocí kláves `code` parametr dotazu. Pomocí nástroje protokolu HTTP k odeslání požadavků POST nebo prohlížeč pro požadavky GET odeslané na vzdálenou funkci.  

## <a name="run-functions-locally"></a>Místní spuštění funkce

Rozšíření Azure Functions vám umožní spustit projekt functions na místním počítači pro vývoj. Místní modul runtime je stejného modulu runtime, který je hostitelem vaší aplikace funkcí v Azure. Místní nastavení jsou přečtena z [souboru local.settings.json](#local-settings-file).

### <a name="additional-requirements-to-run-locally"></a>Další požadavky na místní spuštění

Pokud chcete mohli spustit projekt Functions místně, musíte také splnit tyto požadavky:

* Nainstalovat verzi 2.x [nástrojů Azure Functions Core](functions-run-local.md#v2). Balíček nástroje Core je stáhnout a nainstalovat pro vaše automaticky při spuštění projektu místně. Základní nástroje zahrnují celý modul runtime Azure Functions, tak ke stažení a instalace může nějakou dobu trvat.

* Nainstalujte konkrétní požadavky pro vámi zvolený jazyk:

    | Jazyk | Požadavek |
    | -------- | --------- |
    | **C#** | [Rozšíření jazyka C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)<br/>[Nástroje rozhraní příkazového řádku .NET core](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Ladicí program pro rozšíření Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3+](https://maven.apache.org/) |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Rozšíření Pythonu](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[Python 3.6 +](https://www.python.org/downloads/)|

    <sup>*</sup>Aktivní LTS a údržby LTS verze (8.11.1 a 10.14.1 doporučeno).

### <a name="configure-the-project-to-run-locally"></a>Konfigurace projektu pro místní spuštění

Modul runtime služby Functions interně používá účet Azure Storage pro všechny typy triggerů než HTTP a webhooky. To znamená, že je nutné nastavit **Values.AzureWebJobsStorage** klíč platný připojovací řetězec účtu úložiště Azure.

Tato část používá [služby Azure Storage rozšíření pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) s [Microsoft Azure Storage Explorer](https://storageexplorer.com/) k připojení a načtení připojovacího řetězce úložiště.   

Chcete-li nastavit připojovací řetězec účtu úložiště:

1. V sadě Visual Studio, otevřete **Průzkumníka cloudu**, rozbalte **účtu úložiště** > **svůj účet úložiště**a pak vyberte **vlastnosti**a zkopírujte **primární připojovací řetězec** hodnotu.

2. Ve vašem projektu otevřete soubor local.settings.json a nastavte hodnotu **AzureWebJobsStorage** zkopírujete připojovací řetězec klíče.

3. Opakujte předchozí krok a přidejte jedinečné klíče **hodnoty** pole pro všechna připojení, které vyžadují vaše funkce.

Další informace najdete v tématu [souboru místní nastavení](#local-settings-file).

### <a name="debugging-functions-locally"></a>Ladění funkcí místně  

Chcete-li ladit vaše funkce, stiskněte klávesu F5. Pokud jste ještě nestáhli [Core Tools][nástroje azure functions core], budete k tomu vyzváni. Když Core Tools je nainstalovaná a spuštěná, v terminálu se zobrazí výstup. To je stejný jako spuštění `func host start` Core Tools příkazu z terminálu, ale s požadovanými dodatečnými vytvářet úkoly a připojený ladicí program.  

Spuštění k projektu můžete aktivovat funkce stejně jako při nasazení do Azure. Při spuštění v režimu ladění, zarážky ve Visual Studio Code, podle očekávání.

Adresa URL požadavku pro aktivační události HTTP se zobrazí ve výstupu, v terminálu. Funkční klávesy pro aktivační události HTTP nejsou používány při místním spuštění. Další informace najdete v tématu [strategie pro testování kódu ve službě Azure Functions](functions-test-a-function.md).  

Další informace najdete v tématu [pracovat s Azure Functions Core Tools][nástroje azure functions core].

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Ve výchozím nastavení se nemigrují automaticky při publikování projektu do Azure. Po dokončení publikování budete mít možnost publikování nastavení z local.settings.json aplikace funkcí v Azure. Další informace najdete v tématu [aplikace nastavení publikování](#publish-application-settings).

Hodnoty v **ConnectionStrings** se nikdy publikováno.

Hodnoty nastavení aplikace funkcí můžete číst také ve vašem kódu jako proměnné prostředí. Další informace najdete v sekci proměnných prostředí z těchto článků reference specifická pro jazyk:

* [Předkompilované C#](functions-dotnet-class-library.md#environment-variables)
* [C# skript (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Nastavení aplikace v Azure

Nastavení v souboru local.settings.json ve vašem projektu by měla být stejná jako nastavení aplikace do aplikace function App v Azure. Všechna nastavení, které přidáte do souboru local.settings.json musí být rovněž přidán do aplikace function app v Azure. Tato nastavení nejsou automaticky nahraje při publikování tohoto projektu. Podobně platí, všechna nastavení, které vytvoříte ve své aplikaci function app [na portálu](functions-how-to-use-azure-function-app-settings.md#settings) musí stáhnout do místní projektu.

### <a name="publish-application-settings"></a>Aplikace nastavení publikování

Nejjednodušší způsob, jak publikovat požadovaná nastavení do vaší aplikace funkcí v Azure je použít **nastavení odesílání** odkaz, který se zobrazí po úspěšném publikování projektu.

![Nasazení dokončeno nahrání aplikace nastavení](./media/functions-develop-vs-code/upload-app-settings.png)

Nastavení můžete také publikovat pomocí `Azure Functions: Upload Local Setting` v paletu příkazů. Jednotlivá nastavení jsou přidány do nastavení aplikace v Azure pomocí `Azure Functions: Add New Setting...` příkazu. 

> [!TIP]
> Nezapomeňte si uložit soubor local.settings.json před publikováním.

Pokud místní soubor je zašifrovaný, je dešifrovat, publikování a znovu zašifrována. Pokud nastavení existuje s různými hodnotami v obou umístěních, zobrazí se výzva rozhodnout se, jak pokračovat dál.

Zobrazit existující nastavení aplikace v **Azure: Funkce** oblasti tak, že rozbalíte vašeho předplatného, vaše aplikace function app a **nastavení aplikace**.

![Zobrazení nastavení aplikace funkcí ve Visual Studio Code](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Nastavení stahování z Azure

Když vytvoříte nastavení aplikace v Azure si můžete stáhnout do souboru local.settings.json. s použitím `Azure Functions: Download Remote Settings...` příkazu. 

Stejně jako u nahrát, pokud místní soubor je zašifrovaný, je dešifrovat, aktualizovat a znovu zašifrována. Pokud nastavení existuje s různými hodnotami v obou umístěních, zobrazí se výzva rozhodnout se, jak pokračovat dál.

## <a name="monitoring-functions"></a>Funkce monitorování

Když jste [místní spuštění](#run-functions-locally), data protokolu se streamují do terminálu konzoly. Můžete také získat data protokolu, když váš projekt funkce běží v aplikaci function app v Azure. Můžete buď připojit ke streamování protokolů v Azure najdete v téměř reálném čase data protokolu nebo Application Insights můžete povolit pro více porozumět jak vaši aplikaci function app nepracuje.

### <a name="streaming-logs"></a>Protokoly streamování

Při vývoji aplikace, je často užitečné prohlédnout si informace o protokolování v téměř reálném čase. Můžete zobrazit datový proud protokolů generovaných vašich funkcí. Následující výstup je příkladem streamování protokolů pro požadavek HTTP aktivuje funkci:

![Streamování protokolů, výstupu triggeru HTTP](media/functions-develop-vs-code/streaming-logs-vscode-console.png) 

Další informace najdete v tématu [datový proud protokolů](functions-monitoring.md#streaming-logs). 

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Protokoly streamování podporují pouze jedna instance funkce hostitele. Když vaše funkce je škálování na více instancí, data z jiných instancí se nezobrazují v datovém proudu protokolu. [Live Metrics Stream](../azure-monitor/app/live-stream.md) ve službě Application Insights nepodporuje více instancí. Zatímco také v téměř reálném čase, streamovou analýzu jsou také založeny na [vzorků dat](functions-monitoring.md#configure-sampling).

### <a name="application-insights"></a>Application Insights

Díky integraci vaší aplikace function app pomocí Azure Application Insights je doporučeným způsobem, jak provádění funkcí monitorování. Když vytvoříte aplikaci function app na webu Azure Portal, je tato integrační ve výchozím nastavení provede za vás. Když vytvoříte aplikaci function app během publikování sady Visual Studio, není dokončení integrace ve vaší aplikaci function app v Azure.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Další informace najdete v tématu [monitorování Azure Functions](functions-monitoring.md).

## <a name="c-script-projects"></a>C\# skriptu projekty

Ve výchozím nastavení všechny C# projektů je vytvořena jako [ C# zkompilován projekty knihovny tříd](functions-dotnet-class-library.md). Pokud chcete raději pracovat C# skript projektů, je nutné vybrat C# skript jako výchozí jazyk v nastavení rozšíření Azure Functions.

1. Klikněte na tlačítko **soubor > Předvolby > Nastavení**.

1. Procházet **uživatelská nastavení > Rozšíření > Služba Azure Functions**.

1. Zvolte **C #Script** z **funkce Azure Functions: Projekt jazyka**.

V tomto okamžiku volání provedli základní základní nástroje zahrnují `--csx` možnost, která vygeneruje a publikuje C# skriptu soubory projektu (.csx). Výchozí jazyk zadán, všechny vytvořen výchozí projekty C# skriptu projekty. Zobrazí se výzva k zvolte jazyk projektu, pokud je nastavena výchozí. Chcete-li vytvořit ostatní projekty jazyka, musí toto nastavení změnit nebo odebrat ze souboru settings.json uživatele. Jakmile odstraníte toto nastavení, budete vyzváni znovu zvolte si jazyk při vytváření projektu.

## <a name="command-palette-reference"></a>Informace o příkazech palety

Rozšíření Azure Functions poskytuje užitečné grafické rozhraní v oblasti Azure pro interakci s vaší aplikace function App v Azure. Stejné funkce jsou také dostupné jako příkazy v paletu příkazů (F1). Následující příkazy Azure specifické funkce jsou k dispozici:

|Příkaz Azure Functions  | Popis  |
|---------|---------|
|**Přidáte nové nastavení...**  |  Vytvoří nové nastavení aplikace v Azure. Další informace najdete v tématu [aplikace nastavení publikování](#publish-application-settings). Budete také muset [stáhnout svá místní nastavení, toto nastavení](#download-settings-from-azure). |
| **Nastavit zdroj nasazení...** | Připojení vaší aplikace funkcí v Azure do místního úložiště Git. Další informace najdete v tématu [průběžné nasazování pro službu Azure Functions](functions-continuous-deployment.md). |
| **Připojte k úložišti Githubu...** | Připojte svou aplikaci funkce úložiště GitHub. |
| **Zkopírujte adresu URL funkce** | Získá vzdálené adresu URL protokolu HTTP aktivované funkce spuštěné v Azure. Další informace najdete v tématu Jak [získat adresu URL nasazené funkce](#get-deployed-function-url). |
| **Vytvoření aplikace function app v Azure...** | Vytvoří novou aplikaci function app v rámci vašeho předplatného v Azure. Další informace najdete v tématu Jak [publikovat do nové aplikace function app v Azure](#publish-to-azure).        |
| **Dešifrování nastavení** | Použít k dešifrování [místní nastavení](#local-settings-file) , která byla zašifrována pomocí `Azure Functions: Encrypt Settings`.  |
| **Odstraňuje se aplikace Function App...** | Existující aplikaci function app se odebere z předplatného v Azure. Pokud nejsou žádné jiné aplikace v plánu služby App Service, budete mít možnost odstranit tento příliš. Další prostředky, jako jsou účty úložiště a skupiny prostředků, nebudou odstraněny. Pokud chcete odebrat všechny prostředky, měli byste místo toho [Odstraňte skupinu prostředků](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). Místní projekt nemá vliv. |
|**Odstraňte funkci...**  | Odebere existující funkci z aplikace function app v Azure. Protože toto odstranění nemá vliv na vaše místní projekt, místo toho zvažte odebrání funkce místně a pak [opětovné publikování projektu](#republish-project-files). |
| **Odstraňte proxy server...** | Proxy služby Azure Functions se odebere z vaší aplikace funkcí v Azure. Další informace o proxy serverů najdete v tématu [pracovat s proxy služby Azure Functions](functions-proxies.md). |
| **Odstraňte nastavení...** | Odstraní nastavení aplikace funkcí v Azure. Nastavení v souboru local.settings.json nemá vliv. |
| **Odpojte od úložiště...**  | Odeberte [průběžné nasazování](functions-continuous-deployment.md) připojení mezi aplikace function app v Azure a úložiště správy zdrojového kódu. |
| **Stáhněte vzdálené nastavení...** | Stáhne nastavení z vybrané funkce aplikace v Azure do souboru local.settings.json. Pokud místní soubor je zašifrovaný, je dešifrovat, aktualizovat a znovu zašifrována. Pokud nastavení existuje s různými hodnotami v obou umístěních, zobrazí se výzva rozhodnout se, jak pokračovat dál. Ujistěte se, že jste uložili změny do souboru local.settings.json před spuštěním tohoto příkazu. |
| **Upravte nastavení...** | Změní hodnotu existujícímu nastavení aplikace funkcí v Azure. Nastavení v souboru local.settings.json nemá vliv.  |
| **Nastavení šifrování** | Šifruje jednotlivé položky `Values` obsahuje pole [místní nastavení](#local-settings-file). V tomto souboru `IsEncrypted` je také nastavena na `true`, který říká místní modul runtime k dešifrování nastavení před jejich použitím. Šifrování místní nastavení, abyste snížili riziko úniku cenné informace. V Azure aplikace, které nastavení se ukládají vždy šifrují. |
| **Nyní spusťte – funkce** | Spustí [funkce aktivované časovačem](functions-bindings-timer.md) v Azure pro účely testování ručně. Další informace o Aktivace jiným protokolem než HTTP funkcí v Azure najdete v tématu [ručně spustit jiných funkci aktivovanou protokolem HTTP](functions-manually-run-non-http.md). |
| **Inicializace projektu pro použití s VS Code...** | Přidá do existujícího projektu funkce požadované soubory projektu Visual Studio Code. Tímto příkazem pro práci s projektem, který jste vytvořili pomocí jádra nástroje. |
| **Instalace nástrojů Azure Functions Core aktualizace** | Nainstaluje nebo aktualizuje [Nástroje Azure Functions Core] , které umožňují spouštět místně. |
| **Opětovné nasazení**  | Umožňuje znovu nasadit soubory projektu z připojené úložiště Git pro konkrétní nasazení v Azure. Chcete-li znovu publikovat místní aktuality z Visual Studio Code, [znovu publikujte projekt](#republish-project-files). |
| **Přejmenujte nastavení...** | Změní název klíče existujícímu nastavení aplikace funkcí v Azure. Nastavení v souboru local.settings.json nemá vliv. Po přejmenování nastavení v Azure, měli byste [stáhnout tyto změny do místní projekt](#download-settings-from-azure). |
| **Restartovat** | Restartování aplikace function app v Azure. Nasazení aktualizací se také restartuje, aplikace function app. |
| **Nastavit AzureWebJobStorage...**| Nastaví hodnotu vlastnosti `AzureWebJobStorage` nastavení aplikace. Toto nastavení se vyžaduje služba Azure functions a je nastavena, když se aplikace function app v Azure. |
| **Start** | Spustí zastavené function app v Azure. | 
| **Spuštění streamování protokolů** | Spuštění streamování protokolů pro aplikaci function app v Azure. Datový proud protokolů použijte při vzdáleném řešení potíží v Azure potřebujete zobrazit tyto informace v téměř reálném čase. Další informace najdete v tématu [datový proud protokolů](#streaming-logs). |
| **Stop** | Dojde k vypnutí dolů aplikaci funkcí spuštěnou v Azure. |
| **Zastavení streamování protokolů** | Zastavení streamování protokolů pro aplikaci function app v Azure. |
| **Přepnout jako nastavení slotu** | Pokud povolená, zajišťuje, že ukládá nastavení aplikace pro zadaný slot pro nasazení. |
| **Odinstalace nástrojů Azure Functions Core** | Odebere nástrojů Azure Functions Core, který se vyžaduje rozšíření. |
| **Místní nastavení odesílání...** | Ukládání nastavení ze souboru local.settings.json aplikace zvolené function App v Azure. Pokud místní soubor je zašifrovaný, je dešifrovat, nahraje a znovu zašifrována. Pokud nastavení existuje s různými hodnotami v obou umístěních, zobrazí se výzva rozhodnout se, jak pokračovat dál. Ujistěte se, že jste uložili změny do souboru local.settings.json před spuštěním tohoto příkazu. |
| **Zobrazení potvrzení v Githubu** | Ukazuje poslední potvrzení změn v určitém nasazení při aplikaci function app je připojený k úložišti. |
| **Zobrazit protokoly nasazení** | Zobrazí protokoly pro konkrétní nasazení pro aplikaci function app v Azure. |

## <a name="next-steps"></a>Další postup

Další informace o Azure Functions Core Tools, naleznete v tématu [kódu a testování Azure functions místně](functions-run-local.md).

Další informace o vývoji funkcí jako knihoven tříd .NET najdete v tématu [referenční informace pro vývojáře Azure Functions C#](functions-dotnet-class-library.md). Tento článek taky obsahuje odkazy na příklady toho, jak použít atributy k deklarování různé typy vazeb Azure Functions podporuje.    

[Rozšíření Azure Functions pro Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Nástroje Azure Functions Core]: functions-run-local.md