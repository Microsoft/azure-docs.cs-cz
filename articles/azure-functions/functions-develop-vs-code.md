---
title: Vývoj Azure Functions pomocí Visual Studio Code
description: Naučte se vyvíjet a testovat Azure Functions pomocí rozšíření Azure Functions pro Visual Studio Code.
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 54bbc46c703646f4680f6dc22d5c4b6781614ae7
ms.sourcegitcommit: 541e6139c535d38b9b4d4c5e3bfa7eef02446fdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2020
ms.locfileid: "75667545"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Vývoj Azure Functions pomocí Visual Studio Code

[Rozšíření Azure Functions pro Visual Studio Code] umožňuje místní vývoj funkcí a jejich nasazování do Azure. Pokud je to vaše první prostředí s Azure Functions, můžete získat další informace v [úvodu k Azure Functions](functions-overview.md).

Rozšíření Azure Functions poskytuje tyto výhody:

* Upravovat, sestavovat a spouštět funkce na místním počítači pro vývoj.
* Publikujte projekt Azure Functions přímo do Azure.
* Využijte výhod Visual Studio Code k psaní vašich funkcí v různých jazycích.

Rozšíření lze použít s následujícími jazyky, které jsou podporovány modulem runtime Azure Functions od verze 2. x:

* [C#zkompilovaná](functions-dotnet-class-library.md)
* <sup>*skriptu</sup> [ C# ](functions-reference-csharp.md)
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup> Vyžaduje, abyste [nastavili C# skript jako výchozí jazyk projektu](#c-script-projects).

V tomto článku jsou příklady aktuálně k dispozici pouze pro JavaScript (Node. js) C# a funkce knihovny tříd.  

Tento článek poskytuje podrobné informace o tom, jak používat rozšíření Azure Functions k vývoji funkcí a jejich publikování v Azure. Před čtením tohoto článku byste měli [vytvořit svoji první funkci pomocí Visual Studio Code](functions-create-first-function-vs-code.md).

> [!IMPORTANT]
> Nekombinujte vývoj místních vývojových a portálů pro jednu aplikaci Function App. Při publikování z místního projektu do aplikace Function App proces nasazení přepíše všechny funkce, které jste vytvořili na portálu.

## <a name="prerequisites"></a>Požadavky

Než nainstalujete a spustíte rozšíření [Azure Functions rozšíření][rozšíření azure functions pro visual studio code], musíte splnit tyto požadavky:

* [Visual Studio Code](https://code.visualstudio.com/) nainstalovat na jednu z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Aktivní předplatné Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Pokud [publikujete pomocí Visual Studio Code](#publish-to-azure), v předplatném se vytvoří další prostředky, které potřebujete, třeba účet služby Azure Storage.

> [!IMPORTANT]
> Můžete vyvíjet funkce místně a publikovat je v Azure, aniž byste je museli spouštět a spouštět místně. Chcete-li spustit funkce místně, je nutné splnit některé další požadavky, včetně automatického stažení Azure Functions Core Tools. Další informace najdete v tématu [Další požadavky pro místní spuštění projektu](#additional-requirements-for-running-a-project-locally).

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Vytvoření projektu Azure Functions

Rozšíření Functions umožňuje vytvořit projekt Function App spolu s první funkcí. Následující kroky ukazují, jak vytvořit funkci aktivovanou protokolem HTTP v novém projektu Functions. [Aktivační procedura http](functions-bindings-http-webhook.md) je nejjednodušší šablona triggeru funkce.

1. V **Azure: funkce**vyberte ikonu **vytvořit funkci** :

    ![Vytvoření funkce](./media/functions-develop-vs-code/create-function.png)

1. Vyberte složku pro projekt Function App a pak **Vyberte jazyk pro projekt funkce**.

1. Vyberte šablonu funkce **triggeru protokolu HTTP** , nebo můžete vybrat **Přeskočit nyní** , chcete-li vytvořit projekt bez funkce. [Funkci můžete do projektu kdykoli přidat](#add-a-function-to-your-project) později.

    ![Volba šablony triggeru HTTP](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Jako název funkce zadejte **HTTPTrigger** a vyberte Enter a pak vyberte Authorization – **funkce** . Tato úroveň autorizace vyžaduje zadání [klíče funkce](functions-bindings-http-webhook.md#authorization-keys) při volání koncového bodu funkce.

    ![Vybrat autorizaci funkce](./media/functions-develop-vs-code/create-function-auth.png)

    Funkce se vytvoří ve zvoleném jazyce a v šabloně funkce aktivované protokolem HTTP.

    ![Šablona funkce aktivovaná protokolem HTTP v Visual Studio Code](./media/functions-develop-vs-code/new-function-full.png)

Šablona projektu vytvoří projekt ve zvoleném jazyce a nainstaluje požadované závislosti. Pro libovolný jazyk má nový projekt tyto soubory:

* **Host. JSON**: umožňuje konfigurovat hostitele funkcí. Tato nastavení platí v případě, že používáte funkce místně a když je spouštíte v Azure. Další informace naleznete v tématu [reference Host. JSON](functions-host-json.md).

* **Local. Settings. JSON**: uchovává nastavení, která se používají, když pracujete s místními funkcemi. Tato nastavení se používají jenom v případě, že používáte funkce místně. Další informace najdete v tématu [místní nastavení souboru](#local-settings-file).

    >[!IMPORTANT]
    >Vzhledem k tomu, že soubor Local. Settings. JSON může obsahovat tajné kódy, je nutné jej vyloučit ze správy zdrojového kódu projektu.

V tomto okamžiku můžete přidat vstupní a výstupní vazby do funkce [úpravou souboru Function. JSON](#add-a-function-to-your-project) nebo [přidáním parametru do funkce knihovny C# tříd](#add-a-function-to-your-project).

[Do projektu můžete také přidat novou funkci](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Instalace rozšíření vazby

S výjimkou triggerů HTTP a Timer jsou vazby implementovány v balíčcích rozšíření. Je nutné nainstalovat balíčky rozšíření pro aktivační události a vazby, které je potřebují. Proces pro instalaci rozšíření vazby závisí na jazyku vašeho projektu.

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

Spuštěním příkazu [dotnet Add Package](/dotnet/core/tools/dotnet-add-package) v okně terminálu nainstalujete balíčky rozšíření, které v projektu potřebujete. Následující příkaz nainstaluje rozšíření Azure Storage, které implementuje vazby pro úložiště objektů blob, front a tabulek.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>Přidání funkce do projektu

Novou funkci můžete přidat do existujícího projektu pomocí jedné z předdefinovaných šablon triggeru Functions. Chcete-li přidat novou aktivační proceduru funkce, vyberte F1 pro otevření palety příkazů a pak vyhledejte a spusťte příkaz **Azure Functions: Create Function**. Podle zobrazených výzev vyberte typ triggeru a definujte požadované atributy triggeru. Pokud aktivační událost vyžaduje přístupový klíč nebo připojovací řetězec pro připojení ke službě, připravte ji ještě před vytvořením triggeru funkce.

Výsledky této akce závisí na jazyku vašeho projektu:

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

Do projektu C# se přidá nový soubor knihovny tříd (. cs).

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

V projektu se vytvoří nová složka. Složka obsahuje nový soubor Functions. JSON a nový soubor kódu JavaScriptu.

---

## <a name="add-input-and-output-bindings"></a>Přidat vstupní a výstupní vazby

Funkci můžete rozšířit přidáním vstupních a výstupních vazeb. Proces přidávání vazeb závisí na jazyku vašeho projektu. Další informace o vazbách naleznete v tématu [Azure Functions triggery a koncepty vazeb](functions-triggers-bindings.md).

Následující příklady se připojují k frontě úložiště s názvem `outqueue`, kde je připojovací řetězec pro účet úložiště nastavený v nastavení aplikace `MyStorageConnection` v souboru Local. Settings. JSON.

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

Aktualizujte metodu funkce a přidejte následující parametr do definice metody `Run`:

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

Tento kód vyžaduje, abyste přidali následující příkaz `using`:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

Parametr `msg` je `ICollector<T>` typ, který představuje kolekci zpráv zapsaných do výstupní vazby po dokončení funkce. Do kolekce přidáte jednu nebo více zpráv. Tyto zprávy se po dokončení funkce odesílají do fronty.

Další informace najdete v dokumentaci [výstupní vazby úložiště ve frontě](functions-bindings-storage-queue.md#output---c-example) .

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

Visual Studio Code umožňuje přidat do souboru Function. JSON vazby pomocí praktické sady výzev. Vazbu vytvoříte tak, že kliknete pravým tlačítkem (CTRL + kliknete na macOS **) do složky** funkce a vyberete **Přidat vazbu**:

![Přidání vazby k existující funkci JavaScriptu ](media/functions-develop-vs-code/function-add-binding.png)

V následujícím příkladu se zobrazí výzva k definování nové výstupní vazby úložiště:

| Výzva | Hodnota | Popis |
| -------- | ----- | ----------- |
| **Vybrat směr vazby** | `out` | Vazba je výstupní vazba. |
| **Výběr vazby se směrováním** | `Azure Queue Storage` | Vazba je vazba fronty Azure Storage. |
| **Název, který slouží k identifikaci této vazby v kódu** | `msg` | Název, který identifikuje parametr vazby, na který se odkazuje v kódu. |
| **Fronta, do které bude odeslána zpráva** | `outqueue` | Název fronty, do které vazba zapisuje. Pokud pole *Queue* neexistuje, vytvoří ho při prvním použití. |
| **Vyberte nastavení z Local. Settings. JSON.** | `MyStorageConnection` | Název nastavení aplikace, které obsahuje připojovací řetězec pro účet úložiště. Nastavení `AzureWebJobsStorage` obsahuje připojovací řetězec pro účet úložiště, který jste vytvořili pomocí aplikace Function App. |

V tomto příkladu je do pole `bindings` v souboru Function. JSON přidána následující vazba:

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

Můžete také přidat stejnou definici vazby přímo do funkce Function. JSON.

Ve vašem kódu funkce je `msg` k vazbě z `context`, jako v tomto příkladu:

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

Další informace najdete v referenčních odkazech na [výstupní vazbu úložiště fronty](functions-bindings-storage-queue.md#output---javascript-example) .

---

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="publish-to-azure"></a>Publikování aplikací do Azure

Visual Studio Code umožňuje publikovat projekt funkcí přímo do Azure. Během postupu vytvoříte aplikaci funkcí a související prostředky ve vašem předplatném Azure. Aplikace funkcí poskytuje kontext spuštění pro vaše funkce. Projekt se zabalí a nasadí do nové aplikace funkcí ve vašem předplatném Azure.

Když publikujete z Visual Studio Code do nové aplikace Function App v Azure, budete mít k dispozici jak rychlou aplikaci funkcí vytvořit cestu, tak i pokročilou cestu. 

Při publikování z Visual Studio Code můžete využít technologii [nasazení zip](functions-deployment-technologies.md#zip-deploy) . 

### <a name="quick-function-app-create"></a>Rychlé vytvoření aplikace Function App

Když zvolíte **+ vytvořit novou aplikaci Function App v Azure**, rozšíření automaticky vygeneruje hodnoty prostředků Azure, které potřebuje vaše aplikace Function App. Tyto hodnoty jsou založené na zvoleném názvu aplikace Function App. Příklad použití výchozích hodnot pro publikování projektu do nové aplikace Function App v Azure najdete v [článku rychlý Start pro Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure).

Pokud chcete pro vytvořené prostředky zadat explicitní názvy, musíte zvolit cestu pro rozšířené vytvoření.

### <a name="enable-publishing-with-advanced-create-options"></a>Publikování projektu do nové aplikace Function App v Azure s využitím pokročilých možností

Následující kroky publikují projekt na novou aplikaci funkcí vytvořenou s možnostmi Upřesnit vytváření:

1. V oblasti **Azure: Functions** vyberte ikonu **nasadit do Function App** .

    ![Nastavení aplikace funkcí](./media/functions-develop-vs-code/function-app-publish-project.png)

1. Pokud nejste přihlášení, budete vyzváni k **přihlášení k Azure**. Můžete si také **vytvořit bezplatný účet Azure**. Po přihlášení z prohlížeče se vraťte na Visual Studio Code.

1. Pokud máte více předplatných, **Vyberte předplatné** pro aplikaci Function App a potom vyberte **+ vytvořit novou Function App v Azure... _Upřesnit_** . Tato _Pokročilá_ možnost nabízí větší kontrolu nad prostředky, které vytvoříte v Azure. 

1. Podle pokynů zadejte tyto informace:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Výběr aplikace Function App v Azure | Vytvoření nových Function App v Azure | Do dalšího řádku zadejte globálně jedinečný název, který identifikuje vaši novou aplikaci Function App, a pak vyberte Enter. Platné znaky pro název aplikace funkcí jsou `a-z`, `0-9` a `-`. |
    | Vybrat operační systém | Windows | Aplikace Function App běží ve Windows. |
    | Vybrat plán hostování | Plán Consumption | Používá se [hostování plánu spotřeby](functions-scale.md#consumption-plan) bez serveru. |
    | Vyberte modul runtime pro novou aplikaci. | Jazyk projektu | Modul runtime musí odpovídat projektu, který publikujete. |
    | Vyberte skupinu prostředků pro nové prostředky. | Vytvořit novou skupinu prostředků | Do dalšího řádku zadejte název skupiny prostředků, například `myResourceGroup`, a pak vyberte Enter. Můžete také vybrat existující skupinu prostředků. |
    | Vyberte účet úložiště. | Vytvoření nového účtu úložiště | Do dalšího řádku zadejte globálně jedinečný název nového účtu úložiště používaného aplikací Function App a pak vyberte Enter. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Můžete také vybrat existující účet. |
    | Vyberte umístění pro nové prostředky. | . | Vyberte umístění v [oblasti](https://azure.microsoft.com/regions/) poblíž nebo v blízkosti jiných služeb, ke kterým mají přístup vaše funkce. |

    Po vytvoření aplikace Function App se zobrazí oznámení a použije se balíček pro nasazení. Pokud chcete zobrazit výsledky vytvoření a nasazení, včetně prostředků Azure, které jste vytvořili, vyberte **Zobrazit výstup** v tomto oznámení.

## <a name="republish-project-files"></a>Znovu publikovat soubory projektu

Při nastavování [průběžného nasazování](functions-continuous-deployment.md)se vaše aplikace Function App v Azure aktualizuje při každé aktualizaci zdrojových souborů v připojeném zdrojovém umístění. Doporučujeme průběžné nasazování, ale aktualizace souborů projektu můžete také znovu publikovat z Visual Studio Code.

> [!IMPORTANT]
> Publikování do existující aplikace funkcí přepíše obsah této aplikace v Azure.

1. V Visual Studio Code vyberte F1 a otevřete tak paletu příkazů. V paletě příkazů vyhledejte a vyberte **Azure Functions: nasadit do aplikace Function App**.

1. Pokud nejste přihlášení, budete vyzváni k **přihlášení k Azure**. Po přihlášení z prohlížeče se vraťte na Visual Studio Code. Pokud máte více předplatných, **Vyberte předplatné** , které obsahuje vaši aplikaci Function App.

1. Vyberte svou stávající aplikaci Function App v Azure. Když se zobrazí upozornění na přepsání všech souborů v aplikaci Function App, vyberte **nasadit** a potvrďte upozornění a pokračujte.

Projekt se znovu sestaví, znovu zabalí a nahraje do Azure. Existující projekt je nahrazen novým balíčkem a aplikace Function App se restartuje.

## <a name="get-the-url-of-the-deployed-function"></a>Získat adresu URL nasazené funkce

Pro volání funkce aktivované protokolem HTTP potřebujete při nasazení do aplikace Function App adresu URL funkce. Tato adresa URL zahrnuje všechny požadované [funkční klávesy](functions-bindings-http-webhook.md#authorization-keys). K získání těchto adres URL pro nasazené funkce můžete použít rozšíření.

1. Výběrem klávesy F1 otevřete paletu příkazů a potom vyhledejte a spusťte příkaz **Azure Functions: Kopírovat adresu URL funkce**.

1. Podle pokynů vyberte aplikaci Function App v Azure a pak konkrétní Trigger HTTP, který chcete vyvolat.

Adresa URL funkce se zkopíruje do schránky spolu s případnými povinnými klíči předanými parametrem `code` dotazu. Pomocí nástroje HTTP odešlete žádosti POST nebo prohlížeč pro požadavky GET na vzdálenou funkci.  

## <a name="run-functions-locally"></a>Místní spuštění funkcí

Rozšíření Azure Functions umožňuje spustit projekt funkcí na místním vývojovém počítači. Místní modul runtime je stejný modul runtime, který hostuje vaši aplikaci Function App v Azure. Místní nastavení jsou načítána ze [souboru Local. Settings. JSON](#local-settings-file).

### <a name="additional-requirements-for-running-a-project-locally"></a>Další požadavky na místní spuštění projektu

Chcete-li spustit projekt Functions v místním prostředí, je nutné splnit tyto další požadavky:

* Nainstalujte verzi 2. x nebo novější z [Azure Functions Core Tools](functions-run-local.md#v2). Balíček Core Tools se stáhne a nainstaluje automaticky při spuštění projektu místně. Základní nástroje zahrnují celý modul runtime Azure Functions, takže stažení a instalace můžou nějakou dobu trvat.

* Nainstalujte konkrétní požadavky pro vámi zvolený jazyk:

    | Jazyk | Požadavek |
    | -------- | --------- |
    | **C#** | [C#klapk](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)<br/>[Nástroje .NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Ladicí program pro rozšíření Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3 nebo novější](https://maven.apache.org/) |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Rozšíření Pythonu](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>Doporučuje se [Python 3.6.8](https://www.python.org/downloads/)|

    <sup>*</sup> Aktivní LTS a verze LTS údržby (Doporučené 8.11.1 a 10.14.1).

### <a name="configure-the-project-to-run-locally"></a>Nakonfigurujte projekt tak, aby běžel místně.

Modul runtime Functions používá účet Azure Storage interně pro všechny typy triggerů kromě HTTP a webhooků. Proto je potřeba nastavit klíč **Values. AzureWebJobsStorage** na platný připojovací řetězec účtu Azure Storage.

Tato část používá [rozšíření Azure Storage pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) se [Průzkumník služby Azure Storage](https://storageexplorer.com/) pro připojení k a načtení připojovacího řetězce úložiště.

Nastavení připojovacího řetězce účtu úložiště:

1. V aplikaci Visual Studio otevřete **Průzkumníka cloudu**, rozbalte položku **účet úložiště** > **účtu úložiště**a pak vyberte **vlastnosti** a zkopírujte hodnotu **primárního připojovacího řetězce** .

2. V projektu otevřete soubor Local. Settings. JSON a nastavte hodnotu klíče **AzureWebJobsStorage** na připojovací řetězec, který jste zkopírovali.

3. Opakujte předchozí krok a přidejte jedinečné klíče do pole **hodnoty** pro všechna ostatní připojení požadovaná funkcemi.

Další informace najdete v tématu [místní nastavení souboru](#local-settings-file).

### <a name="debugging-functions-locally"></a>Místní ladění funkcí  

Chcete-li ladit své funkce, vyberte F5. Pokud jste ještě nestáhli [základní nástroje][Azure Functions Core Tools], budete vyzváni k tomu. Když jsou nainstalované a běžící základní nástroje, zobrazí se výstup v terminálu. To je totéž jako spuštění příkazu `func host start` Core Tools z terminálu, ale s dalšími úlohami sestavení a připojeným ladicím programem.  

Když je projekt spuštěný, můžete aktivovat své funkce stejně, jako byste při nasazení projektu do Azure. Když je projekt spuštěn v režimu ladění, zarážky jsou dosaženy v Visual Studio Code, podle očekávání.

Adresa URL požadavku pro aktivační události HTTP se zobrazí ve výstupu v terminálu. Funkční klávesy pro aktivační události HTTP se nepoužívají, pokud je projekt spuštěn místně. Další informace najdete v tématu [strategie pro testování kódu v Azure Functions](functions-test-a-function.md).  

Další informace najdete v tématu věnovaném [práci s Azure Functions Core Tools][Azure Functions Core Tools].

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Ve výchozím nastavení se tato nastavení nemigrují automaticky, když je projekt publikován do Azure. Po dokončení publikování máte možnost publikovat nastavení z Local. Settings. JSON do aplikace Function App v Azure. Další informace najdete v tématu [publikování nastavení aplikace](#publish-application-settings).

Hodnoty v **connectionStrings** se nikdy nepublikují.

Hodnoty nastavení aplikace Functions lze v kódu přečíst také jako proměnné prostředí. Další informace najdete v oddílech s proměnnými prostředí těchto referenčních článků specifických pro konkrétní jazyk:

* [C#předkompilované](functions-dotnet-class-library.md#environment-variables)
* [C# skript (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Nastavení aplikace v Azure

Nastavení v souboru Local. Settings. JSON v projektu by mělo být stejné jako nastavení aplikace v aplikaci Function App v Azure. Všechna nastavení, která přidáte do Local. Settings. JSON, se musí taky přidat do aplikace Function App v Azure. Tato nastavení nejsou nahrána automaticky při publikování projektu. Podobně se všechna nastavení, která vytvoříte ve vaší aplikaci Function App na [portálu](functions-how-to-use-azure-function-app-settings.md#settings) , musí stáhnout do vašeho místního projektu.

### <a name="publish-application-settings"></a>Publikování nastavení aplikace

Nejjednodušší způsob, jak publikovat požadovaná nastavení do aplikace Function App v Azure, je použít odkaz **nastavení nahrávání** , který se zobrazí po publikování projektu:

![Nahrát nastavení aplikace](./media/functions-develop-vs-code/upload-app-settings.png)

Nastavení můžete také publikovat pomocí příkazu **Azure Functions: nahrání místního nastavení** v paletě příkazů. Do nastavení aplikace v Azure můžete přidat jednotlivá nastavení pomocí příkazu **Azure Functions: přidat nové nastavení** .

> [!TIP]
> Nezapomeňte uložit soubor Local. Settings. JSON předtím, než ho publikujete.

Pokud je místní soubor zašifrovaný, dešifruje, publikuje a znovu zašifruje. Pokud v obou umístěních existují nastavení, která mají konfliktní hodnoty, zobrazí se výzva, abyste si zvolili, jak pokračovat.

Seznamte se s existujícími nastaveními aplikací v oblasti **Azure: Functions** rozbalením předplatného, aplikace Function App a **nastavení aplikace**.

![Zobrazit nastavení aplikace Function App v Visual Studio Code](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Stažení nastavení z Azure

Pokud jste v Azure vytvořili nastavení aplikace, můžete je stáhnout do souboru Local. Settings. JSON pomocí příkazu **Azure Functions: Stáhnout Vzdálená nastavení** .

Pokud je místní soubor zašifrovaný, je stejně jako u nahrávání šifrovaný, aktualizovaný a šifrovaný znovu. Pokud v obou umístěních existují nastavení, která mají konfliktní hodnoty, zobrazí se výzva, abyste si zvolili, jak pokračovat.

## <a name="monitoring-functions"></a>Funkce monitorování

Když [spouštíte funkce místně](#run-functions-locally), data protokolu se streamují do konzoly terminálu. Data protokolu můžete získat také v případě, že projekt Functions běží v aplikaci Function App v Azure. Můžete se buď připojit k protokolům streamování v Azure, abyste viděli data protokolu téměř v reálném čase, nebo můžete povolit Application Insights pro úplnější porozumění tomu, jak se aplikace Function app chová.

### <a name="streaming-logs"></a>Protokoly streamování

Při vývoji aplikace je často užitečné zobrazit informace o protokolování téměř v reálném čase. Můžete zobrazit datový proud souborů protokolu generovaných vašimi funkcemi. Tento výstup je příkladem protokolů streamování pro požadavek na funkci spuštěnou protokolem HTTP:

![Výstup protokolů streamování pro Trigger HTTP](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

Další informace najdete v tématu [streamování protokolů](functions-monitoring.md#streaming-logs).

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Protokoly streamování podporují jenom jednu instanci hostitele Functions. Pokud je vaše funkce škálovaná na více instancí, data z jiných instancí se v datovém proudu protokolu nezobrazují. [Live Metrics Stream](../azure-monitor/app/live-stream.md) v Application Insights podporuje více instancí. I když téměř v reálném čase je analýza streamování založená na [ukázkových datech](functions-monitoring.md#configure-sampling).

### <a name="application-insights"></a>Application Insights

Doporučujeme vám monitorovat provádění vašich funkcí integrací aplikace Function App s Application Insights. Při vytváření aplikace Function App v Azure Portal k této integraci dojde ve výchozím nastavení. Při vytváření aplikace Function App během publikování sady Visual Studio je nutné integrovat Application Insights sami.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Další informace najdete v tématu [monitorování Azure Functions](functions-monitoring.md).

## <a name="c-script-projects"></a>Projekty skriptu v jazyce C\#

Ve výchozím nastavení jsou C# všechny projekty vytvořeny jako [ C# kompilované projekty knihovny tříd](functions-dotnet-class-library.md). Pokud raději chcete pracovat s C# projekty skriptu, musíte jako výchozí jazyk v C# nastavení rozšíření Azure Functions vybrat skript:

1. Vyberte možnost **soubor** > **Předvolby** > **Nastavení**.

1.  > **Azure Functions**přejít na **nastavení uživatele** > **rozšíření** .

1. V Azure Functions vyberte **skript C #** **: jazyk projektu**.

Po dokončení tohoto postupu budou volání do základních základních nástrojů zahrnovat možnost `--csx`, která generuje a publikuje soubory projektu C# skriptu (. csx). Pokud máte zadaný výchozí jazyk, všechny projekty, které vytvoříte jako výchozí, C# skripty projektů. Nejste vyzváni k výběru jazyka projektu, pokud je nastavena výchozí hodnota. Chcete-li vytvořit projekty v jiných jazycích, je nutné toto nastavení změnit, nebo jej odebrat ze souboru User settings. JSON. Po odebrání tohoto nastavení budete znovu vyzváni k výběru jazyka při vytváření projektu.

## <a name="command-palette-reference"></a>Reference k paletě příkazů

Rozšíření Azure Functions poskytuje užitečné grafické rozhraní v oblasti pro interakci s aplikacemi Function App v Azure. Stejné funkce jsou také k dispozici jako příkazy v paletě příkazů (F1). K dispozici jsou tyto příkazy Azure Functions:

|Azure Functions – příkaz  | Popis  |
|---------|---------|
|**Přidat nová nastavení**  |  Vytvoří nové nastavení aplikace v Azure. Další informace najdete v tématu [publikování nastavení aplikace](#publish-application-settings). Může být také nutné [Stáhnout toto nastavení do místního nastavení](#download-settings-from-azure). |
| **Konfigurovat zdroj nasazení** | Připojí aplikaci funkcí v Azure k místnímu úložišti Git. Další informace najdete v tématu [průběžné nasazování pro Azure Functions](functions-continuous-deployment.md). |
| **Připojit k úložišti GitHub** | Připojí aplikaci funkcí k úložišti GitHub. |
| **Kopírovat adresu URL funkce** | Získá vzdálenou adresu URL funkce aktivované protokolem HTTP, která běží v Azure. Další informace najdete v tématu [získání adresy URL nasazené funkce](#get-the-url-of-the-deployed-function). |
| **Vytvoření aplikace Function App v Azure** | Vytvoří ve svém předplatném Azure novou aplikaci Function App. Další informace najdete v části o tom, jak [publikovat do nové aplikace Function App v Azure](#publish-to-azure).        |
| **Nastavení dešifrování** | Dešifruje [místní nastavení](#local-settings-file) , která byla zašifrovaná pomocí **Azure Functions: nastavení šifrování**.  |
| **Odstranit Function App** | Odebere aplikaci funkcí z vašeho předplatného v Azure. Pokud v plánu App Service neexistují žádné jiné aplikace, máte možnost je také odstranit. Další prostředky, jako jsou účty úložiště a skupiny prostředků, se neodstraňují. Pokud chcete odebrat všechny prostředky, měli byste místo toho [Odstranit skupinu prostředků](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). Váš místní projekt to neovlivní. |
|**Odstranit funkci**  | Odebere existující funkci z aplikace Function App v Azure. Vzhledem k tomu, že odstranění nemá vliv na váš místní projekt, zvažte místo toho odebrání funkce místně a opětovné [publikování projektu](#republish-project-files). |
| **Odstranit proxy** | Odebere Azure Functions proxy z aplikace Function App v Azure. Další informace o proxy serverech najdete v tématu věnovaném [práci s proxy služby Azure Functions](functions-proxies.md). |
| **Odstranit nastavení** | Odstraní nastavení aplikace Function App v Azure. Toto odstranění nemá vliv na nastavení v souboru Local. Settings. JSON. |
| **Odpojit od úložiště**  | Odebere připojení [průběžného nasazování](functions-continuous-deployment.md) mezi aplikací funkcí v Azure a úložištěm správy zdrojového kódu. |
| **Stáhnout Vzdálená nastavení** | Stáhne nastavení z vybrané aplikace Function App do Azure do souboru Local. Settings. JSON. Pokud je místní soubor zašifrovaný, dešifruje se, aktualizuje a zašifruje znovu. Pokud v obou umístěních existují nastavení, která mají konfliktní hodnoty, zobrazí se výzva, abyste si zvolili, jak pokračovat. Nezapomeňte před spuštěním tohoto příkazu Uložit změny v souboru Local. Settings. JSON. |
| **Upravit nastavení** | Změní hodnotu existujícího nastavení aplikace Function App v Azure. Tento příkaz nemá vliv na nastavení v souboru Local. Settings. JSON.  |
| **Nastavení šifrování** | Šifruje jednotlivé položky v poli `Values` v [místním nastavení](#local-settings-file). V tomto souboru je `IsEncrypted` také nastaveno na `true`, což určuje, že místní modul runtime bude před použitím dešifrovat nastavení. Zašifrujte místní nastavení, abyste snížili riziko nevracení cenných informací. V Azure jsou nastavení aplikace vždycky uložená zašifrovaná. |
| **Spustit funkci hned** | Ručně spustí funkci aktivovanou [časovačem](functions-bindings-timer.md) v Azure. Tento příkaz se používá pro testování. Další informace o aktivaci funkcí nevyužívajících protokol HTTP v Azure najdete v tématu [Ruční spuštění funkce neaktivované protokolem HTTP](functions-manually-run-non-http.md). |
| **Inicializovat projekt pro použití s VS Code** | Přidá požadované soubory projektu Visual Studio Code do existujícího projektu Functions. Tento příkaz použijte pro práci s projektem, který jste vytvořili pomocí základních nástrojů. |
| **Nainstalovat nebo aktualizovat Azure Functions Core Tools** | Nainstaluje nebo aktualizuje [Azure Functions Core Tools], která se používá ke spouštění funkcí místně. |
| **Znovu nasadit**  | Umožňuje znovu nasadit soubory projektu z připojeného úložiště Git do konkrétního nasazení v Azure. Chcete-li znovu publikovat místní aktualizace z Visual Studio Code, [projekt znovu publikujte](#republish-project-files). |
| **Přejmenovat nastavení** | Změní název klíče stávajícího nastavení aplikace Function App v Azure. Tento příkaz nemá vliv na nastavení v souboru Local. Settings. JSON. Po přejmenování nastavení v Azure byste [tyto změny měli stáhnout do místního projektu](#download-settings-from-azure). |
| **Restartovat** | Restartuje aplikaci Function App v Azure. Nasazení aktualizací také restartuje funkci aplikace Function App. |
| **Nastavení AzureWebJobsStorage**| Nastaví hodnotu nastavení aplikace `AzureWebJobsStorage`. Toto nastavení vyžaduje Azure Functions. Nastavuje se při vytváření aplikace funkcí v Azure. |
| **Start** | Spustí zastavenou aplikaci Function App v Azure. |
| **Spustit streamování protokolů** | Spustí protokoly streamování aplikace Function App v Azure. Při řešení potíží se vzdáleným zpracováním v Azure použijte protokoly streamování, pokud potřebujete zobrazit informace o protokolování téměř v reálném čase. Další informace najdete v tématu [streamování protokolů](#streaming-logs). |
| **Stop** | Zastaví aplikaci Function App, která běží v Azure. |
| **Zastavit streamování protokolů** | Zastaví protokoly streamování aplikace Function App v Azure. |
| **Přepnout jako nastavení slotu** | Pokud je tato možnost povolená, zajistí, že nastavení aplikace pro danou slot nasazení přetrvává. |
| **Odinstalace Azure Functions Core Tools** | Odebere Azure Functions Core Tools, který je vyžadován rozšířením. |
| **Odeslat místní nastavení** | Nahraje nastavení ze souboru Local. Settings. JSON do vybrané aplikace Function App v Azure. Pokud je místní soubor zašifrovaný, dešifruje se, nahraje a zašifruje znovu. Pokud v obou umístěních existují nastavení, která mají konfliktní hodnoty, zobrazí se výzva, abyste si zvolili, jak pokračovat. Nezapomeňte před spuštěním tohoto příkazu Uložit změny v souboru Local. Settings. JSON. |
| **Zobrazit potvrzení na GitHubu** | Pokud je vaše aplikace Function App připojena k úložišti, zobrazí se nejnovější potvrzení v konkrétním nasazení. |
| **Zobrazit protokoly nasazení** | Zobrazuje protokoly pro konkrétní nasazení aplikace Function App v Azure. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure Functions Core Tools najdete v tématu věnovaném [práci s Azure Functions Core Tools](functions-run-local.md).

Další informace o vývoji funkcí jako knihoven tříd .NET naleznete v tématu [Azure Functions C# reference pro vývojáře](functions-dotnet-class-library.md). Tento článek také obsahuje odkazy na příklady použití atributů k deklaraci různých typů vazeb, které jsou podporovány nástrojem Azure Functions.

[Rozšíření Azure Functions pro Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md
