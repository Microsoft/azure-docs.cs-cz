---
title: Vývoj Azure Functions pomocí Visual Studio Code
description: Naučte se vyvíjet a testovat Azure Functions pomocí rozšíření Azure Functions pro Visual Studio Code.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/21/2019
ms.openlocfilehash: d4353e6be313d61716933879efa930e22472781b
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493938"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Vývoj Azure Functions pomocí Visual Studio Code

[Azure Functions rozšíření pro Visual Studio Code] umožňuje místní vývoj funkcí a jejich nasazování do Azure. Pokud je to vaše první prostředí s Azure Functions, můžete získat další informace v [úvodu k Azure Functions](functions-overview.md).

Rozšíření Azure Functions poskytuje tyto výhody:

* Upravovat, sestavovat a spouštět funkce na místním počítači pro vývoj.
* Publikujte projekt Azure Functions přímo do Azure.
* Využijte výhod Visual Studio Code k psaní vašich funkcí v různých jazycích.

Rozšíření lze použít s následujícími jazyky, které jsou podporovány modulem runtime Azure Functions od verze 2. x:

* [C# kompilováno](functions-dotnet-class-library.md)
* [Skript jazyka C#](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>Vyžaduje, abyste [nastavili skript C# jako výchozí jazyk projektu](#c-script-projects).

V tomto článku jsou příklady aktuálně k dispozici pouze pro funkce knihovny JavaScript (Node.js) a C#.  

Tento článek poskytuje podrobné informace o tom, jak používat rozšíření Azure Functions k vývoji funkcí a jejich publikování v Azure. Před čtením tohoto článku byste měli [vytvořit svoji první funkci pomocí Visual Studio Code](./create-first-function-vs-code-csharp.md).

> [!IMPORTANT]
> Nekombinujte vývoj místních vývojových a portálů pro jednu aplikaci Function App. Při publikování z místního projektu do aplikace Function App proces nasazení přepíše všechny funkce, které jste vytvořili na portálu.

## <a name="prerequisites"></a>Požadavky

Než nainstalujete a spustíte rozšíření [Azure Functions rozšíření][Azure Functions pro Visual Studio Code], musíte splnit tyto požadavky:

* [Visual Studio Code](https://code.visualstudio.com/) nainstalovat na jednu z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Musíte mít aktivní předplatné Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Pokud [publikujete pomocí Visual Studio Code](#publish-to-azure), v předplatném se vytvoří další prostředky, které potřebujete, třeba účet služby Azure Storage. 

### <a name="run-local-requirements"></a>Spustit místní požadavky

Tyto požadavky jsou nutné pouze ke [spuštění a ladění funkcí v místním prostředí](#run-functions-locally). Nejsou nutné k vytváření nebo publikování projektů pro Azure Functions.

# <a name="c"></a>[R\#](#tab/csharp)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) verze 2. x nebo novější. Balíček Core Tools se stáhne a nainstaluje automaticky při spuštění projektu místně. Základní nástroje zahrnují celý modul runtime Azure Functions, takže stažení a instalace můžou nějakou dobu trvat.

+ [Rozšíření pro jazyk C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) pro Visual Studio Code 

+ [.NET Core CLI nástroje](/dotnet/core/tools/?tabs=netcore2x).  

# <a name="java"></a>[Java](#tab/java)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) verze 2. x nebo novější. Balíček Core Tools se stáhne a nainstaluje automaticky při spuštění projektu místně. Základní nástroje zahrnují celý modul runtime Azure Functions, takže stažení a instalace můžou nějakou dobu trvat.

+ [Ladicí program pro rozšíření Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug).

+ Doporučuje se [Java 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) . Další podporované verze najdete v tématu [verze Java](functions-reference-java.md#java-versions).

+ [Maven 3 nebo novější](https://maven.apache.org/)

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) verze 2. x nebo novější. Balíček Core Tools se stáhne a nainstaluje automaticky při spuštění projektu místně. Základní nástroje zahrnují celý modul runtime Azure Functions, takže stažení a instalace můžou nějakou dobu trvat.

+ [Node.js](https://nodejs.org/), LTS a verze LTS údržby (doporučeno 10.14.1). Pomocí `node --version` příkazu ověřte svou verzi. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) verze 2. x nebo novější. Balíček Core Tools se stáhne a nainstaluje automaticky při spuštění projektu místně. Základní nástroje zahrnují celý modul runtime Azure Functions, takže stažení a instalace můžou nějakou dobu trvat.

+ Doporučuje se [PowerShell 7](/powershell/scripting/install/installing-powershell-core-on-windows) . Informace o verzi najdete v tématu [verze PowerShellu](functions-reference-powershell.md#powershell-versions).

+ Runtime [.NET core 3,1](https://www.microsoft.com/net/download) a [.NET Core 2,1 runtime](https://dotnet.microsoft.com/download/dotnet-core/2.1)  

+ [Rozšíření PowerShellu pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).  

# <a name="python"></a>[Python](#tab/python)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) verze 2. x nebo novější. Balíček Core Tools se stáhne a nainstaluje automaticky při spuštění projektu místně. Základní nástroje zahrnují celý modul runtime Azure Functions, takže stažení a instalace můžou nějakou dobu trvat.

+ [Python 3. x](https://www.python.org/downloads/). Informace o verzi naleznete v tématu [verze pythonu](functions-reference-python.md#python-version) Azure Functions runtime.

+ [Rozšíření Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) pro Visual Studio Code.

---

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Vytvoření projektu služby Azure Functions

Rozšíření Functions umožňuje vytvořit projekt Function App spolu s první funkcí. Následující kroky ukazují, jak vytvořit funkci aktivovanou protokolem HTTP v novém projektu Functions. [Aktivační procedura http](functions-bindings-http-webhook.md) je nejjednodušší šablona triggeru funkce.

1. V **Azure: funkce** vyberte ikonu **vytvořit funkci** :

    ![Vytvoření funkce](./media/functions-develop-vs-code/create-function.png)

1. Vyberte složku pro projekt Function App a pak **Vyberte jazyk pro projekt funkce**.

1. Vyberte šablonu funkce **triggeru protokolu HTTP** , nebo můžete vybrat **Přeskočit nyní** , chcete-li vytvořit projekt bez funkce. [Funkci můžete do projektu kdykoli přidat](#add-a-function-to-your-project) později.

    ![Volba šablony triggeru HTTP](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Jako název funkce zadejte **HttpExample** a vyberte Enter a pak vyberte Authorization – **funkce** . Tato úroveň autorizace vyžaduje zadání [klíče funkce](functions-bindings-http-webhook-trigger.md#authorization-keys) při volání koncového bodu funkce.

    ![Vybrat autorizaci funkce](./media/functions-develop-vs-code/create-function-auth.png)

    Funkce se vytvoří ve zvoleném jazyce a v šabloně funkce aktivované protokolem HTTP.

    ![Šablona funkce aktivovaná protokolem HTTP v Visual Studio Code](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>Generované soubory projektu

Šablona projektu vytvoří projekt ve zvoleném jazyce a nainstaluje požadované závislosti. Pro libovolný jazyk má nový projekt tyto soubory:

* **host.js**: umožňuje konfigurovat hostitele funkcí. Tato nastavení platí v případě, že používáte funkce místně a když je spouštíte v Azure. Další informace najdete v tématu [host.jsv referenci](functions-host-json.md).

* **local.settings.json**: udržuje nastavení používaná při místním spouštění funkcí. Tato nastavení se používají jenom v případě, že používáte funkce místně. Další informace najdete v tématu [místní nastavení souboru](#local-settings-file).

    >[!IMPORTANT]
    >Vzhledem k tomu, že local.settings.jsv souboru může obsahovat tajné kódy, je nutné ji vyloučit ze správy zdrojového kódu projektu.

V závislosti na jazyku jsou tyto další soubory vytvořeny:

# <a name="c"></a>[R\#](#tab/csharp)

* [HttpExample.cs soubor knihovny tříd](functions-dotnet-class-library.md#functions-class-library-project) , který implementuje funkci.

# <a name="java"></a>[Java](#tab/java)

+ Soubor pom.xml v kořenové složce definující parametry projektu a nasazení, včetně závislostí projektu a [verze Java](functions-reference-java.md#java-versions). pom.xml taky obsahuje informace o prostředcích Azure, které se vytvoří během nasazení.   

+ [Soubor Functions. Java](functions-reference-java.md#triggers-and-annotations) v cestě src, který implementuje funkci.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

* package.jsv souboru v kořenové složce.

* Složka HttpExample, která obsahuje [function.jssouboru definice](functions-reference-node.md#folder-structure) a [index.js souboru](functions-reference-node.md#exporting-a-function), Node.js soubor, který obsahuje kód funkce.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

* Složka HttpExample obsahující [function.jsv definičním souboru](functions-reference-powershell.md#folder-structure) a run.ps1 soubor, který obsahuje kód funkce.
 
# <a name="python"></a>[Python](#tab/python)
    
* Soubor requirements.txt na úrovni projektu, ve kterém jsou uvedeny balíčky požadované funkcemi.
    
* Složka HttpExample obsahující [function.jsv souboru definice](functions-reference-python.md#folder-structure) a v \_ \_ \_ \_ souboru init. py, který obsahuje kód funkce.

---

V tomto okamžiku můžete do funkce [přidat vstupní a výstupní vazby](#add-input-and-output-bindings) . [Do projektu můžete také přidat novou funkci](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Instalace rozšíření vazby

S výjimkou triggerů HTTP a Timer jsou vazby implementovány v balíčcích rozšíření. Je nutné nainstalovat balíčky rozšíření pro aktivační události a vazby, které je potřebují. Proces pro instalaci rozšíření vazby závisí na jazyku vašeho projektu.

# <a name="c"></a>[R\#](#tab/csharp)

Spuštěním příkazu [dotnet Add Package](/dotnet/core/tools/dotnet-add-package) v okně terminálu nainstalujete balíčky rozšíření, které v projektu potřebujete. Následující příkaz nainstaluje rozšíření Azure Storage, které implementuje vazby pro úložiště objektů blob, front a tabulek.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="java"></a>[Java](#tab/java)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="powershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="python"></a>[Python](#tab/python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>Přidání funkce do projektu

Novou funkci můžete přidat do existujícího projektu pomocí jedné z předdefinovaných šablon triggeru Functions. Chcete-li přidat novou aktivační proceduru funkce, vyberte F1 pro otevření palety příkazů a pak vyhledejte a spusťte příkaz **Azure Functions: Create Function**. Podle zobrazených výzev vyberte typ triggeru a definujte požadované atributy triggeru. Pokud aktivační událost vyžaduje přístupový klíč nebo připojovací řetězec pro připojení ke službě, připravte ji ještě před vytvořením triggeru funkce.

Výsledky této akce závisí na jazyku vašeho projektu:

# <a name="c"></a>[R\#](#tab/csharp)

Do projektu se přidá nový soubor knihovny tříd C# (. cs).

# <a name="java"></a>[Java](#tab/java)

Do projektu se přidá nový soubor Java (. Java).

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

V projektu se vytvoří nová složka. Složka obsahuje novou function.jspro soubor a nový soubor kódu JavaScriptu.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

V projektu se vytvoří nová složka. Složka obsahuje novou function.jspro soubor a nový soubor kódu prostředí PowerShell.

# <a name="python"></a>[Python](#tab/python)

V projektu se vytvoří nová složka. Složka obsahuje novou function.jspro soubor a nový soubor kódu Pythonu.

---

## <a name="connect-to-services"></a><a name="add-input-and-output-bindings"></a>Připojení ke službám

Svou funkci můžete připojit k ostatním službám Azure přidáním vstupních a výstupních vazeb. Vazby spojují vaši funkci s dalšími službami, aniž byste museli psát kód připojení. Proces přidávání vazeb závisí na jazyku vašeho projektu. Další informace o vazbách naleznete v tématu [Azure Functions triggery a koncepty vazeb](functions-triggers-bindings.md).

Následující příklady se připojují k frontě úložiště s názvem `outqueue` , kde je připojovací řetězec pro účet úložiště nastavený v `MyStorageConnection` nastavení aplikace v local.settings.jszapnuto.

# <a name="c"></a>[R\#](#tab/csharp)

Aktualizujte metodu funkce tak, aby do definice metody přidal následující parametr `Run` :

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

`msg`Parametr je `ICollector<T>` typ, který představuje kolekci zpráv zapsaných do výstupní vazby po dokončení funkce. Následující kód přidá do kolekce zprávu:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="30-31":::

 Zprávy se odesílají do fronty po dokončení funkce.

Další informace najdete v dokumentaci k [referenčnímu článku o vazbě výstupu služby Queue Storage](functions-bindings-storage-queue-output.md?tabs=csharp) . Další informace o tom, které vazby je možné přidat do funkce, najdete v tématu [Přidání vazeb do existující funkce v Azure Functions](add-bindings-existing-function.md?tabs=csharp). 

# <a name="java"></a>[Java](#tab/java)

Aktualizujte metodu funkce tak, aby do definice metody přidal následující parametr `Run` :

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

`msg`Parametr je `OutputBinding<T>` typ, kde je `T` řetězec, který je zapsán do výstupní vazby po dokončení funkce. Následující kód nastaví zprávu ve výstupní vazbě:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="33-34":::

Tato zpráva se odešle do fronty po dokončení funkce.

Další informace najdete v dokumentaci k [referenčnímu článku o vazbě výstupu služby Queue Storage](functions-bindings-storage-queue-output.md?tabs=java) . Další informace o tom, které vazby je možné přidat do funkce, najdete v tématu [Přidání vazeb do existující funkce v Azure Functions](add-bindings-existing-function.md?tabs=java). 

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

V kódu funkce `msg` je vazba k dispozici z rozhraní `context` , jako v tomto příkladu:

:::code language="javascript" range="5-7" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/index.js":::

Tato zpráva se odešle do fronty po dokončení funkce.

Další informace najdete v dokumentaci k [referenčnímu článku o vazbě výstupu služby Queue Storage](functions-bindings-storage-queue-output.md?tabs=javascript) . Další informace o tom, které vazby je možné přidat do funkce, najdete v tématu [Přidání vazeb do existující funkce v Azure Functions](add-bindings-existing-function.md?tabs=javascript). 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

:::code language="powershell" range="18-19" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/run.ps1":::

Tato zpráva se odešle do fronty po dokončení funkce.

Další informace najdete v dokumentaci k [referenčnímu článku o vazbě výstupu služby Queue Storage](functions-bindings-storage-queue-output.md?tabs=powershell) . Další informace o tom, které vazby je možné přidat do funkce, najdete v tématu [Přidání vazeb do existující funkce v Azure Functions](add-bindings-existing-function.md?tabs=powershell). 

# <a name="python"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

Aktualizujte `Main` definici a přidejte výstupní parametr `msg: func.Out[func.QueueMessage]` tak, aby definice vypadala jako v následujícím příkladu:

:::code language="python" range="6" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Následující kód přidá řetězcová data z požadavku do výstupní fronty:

:::code language="python" range="18" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Tato zpráva se odešle do fronty po dokončení funkce.

Další informace najdete v dokumentaci k [referenčnímu článku o vazbě výstupu služby Queue Storage](functions-bindings-storage-queue-output.md?tabs=python) . Další informace o tom, které vazby je možné přidat do funkce, najdete v tématu [Přidání vazeb do existující funkce v Azure Functions](add-bindings-existing-function.md?tabs=python). 

---

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Publikování do Azure

Visual Studio Code umožňuje publikovat projekt funkcí přímo do Azure. Během postupu vytvoříte aplikaci funkcí a související prostředky ve vašem předplatném Azure. Aplikace funkcí poskytuje kontext spuštění pro vaše funkce. Projekt se zabalí a nasadí do nové aplikace funkcí ve vašem předplatném Azure.

Když publikujete z Visual Studio Code do nové aplikace Function App v Azure, můžete zvolit rychlou aplikaci funkcí vytvořit cestu pomocí výchozích hodnot nebo pokročilou cestu, kde máte větší kontrolu nad vytvořenými vzdálenými prostředky. 

Při publikování z Visual Studio Code můžete využít technologii [nasazení zip](functions-deployment-technologies.md#zip-deploy) . 

### <a name="quick-function-app-create"></a>Rychlé vytvoření aplikace Function App

Když zvolíte **+ vytvořit novou aplikaci Function App v Azure**, rozšíření automaticky vygeneruje hodnoty prostředků Azure, které potřebuje vaše aplikace Function App. Tyto hodnoty jsou založené na zvoleném názvu aplikace Function App. Příklad použití výchozích hodnot pro publikování projektu do nové aplikace Function App v Azure najdete v [článku rychlý Start pro Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure).

Pokud chcete pro vytvořené prostředky zadat explicitní názvy, musíte zvolit cestu pro rozšířené vytvoření.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>Publikování projektu do nové aplikace Function App v Azure s využitím pokročilých možností

Následující kroky publikují projekt na novou aplikaci funkcí vytvořenou s možnostmi Upřesnit vytváření:

1. Na paletě příkazu zadejte **Azure Functions: Deploy do aplikace Function App**.

1. Pokud nejste přihlášení, budete vyzváni k **přihlášení k Azure**. Můžete si také **vytvořit bezplatný účet Azure**. Po přihlášení z prohlížeče se vraťte na Visual Studio Code.

1. Pokud máte více předplatných, **Vyberte předplatné** pro aplikaci Function App a potom vyberte **+ vytvořit novou Function App v Azure... _Upřesnit_**. Tato _Pokročilá_ možnost nabízí větší kontrolu nad prostředky, které vytvoříte v Azure. 

1. Podle pokynů zadejte tyto informace:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Výběr aplikace Function App v Azure | Vytvoření nových Function App v Azure | Do dalšího řádku zadejte globálně jedinečný název, který identifikuje vaši novou aplikaci Function App, a pak vyberte Enter. Platné znaky pro název aplikace funkcí jsou `a-z`, `0-9` a `-`. |
    | Vybrat operační systém | Windows | Aplikace Function App běží ve Windows. |
    | Vybrat plán hostování | Plán Consumption | Používá se [hostování plánu spotřeby](consumption-plan.md) bez serveru. |
    | Vyberte modul runtime pro novou aplikaci. | Jazyk projektu | Modul runtime musí odpovídat projektu, který publikujete. |
    | Vyberte skupinu prostředků pro nové prostředky. | Vytvořit novou skupinu prostředků | Do dalšího řádku zadejte název skupiny prostředků, jako `myResourceGroup` je, a pak vyberte zadat. Můžete také vybrat existující skupinu prostředků. |
    | Vyberte účet úložiště. | Vytvoření nového účtu úložiště | Do dalšího řádku zadejte globálně jedinečný název nového účtu úložiště používaného aplikací Function App a pak vyberte Enter. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Můžete také vybrat existující účet. |
    | Vyberte umístění pro nové prostředky. | oblast | Vyberte umístění v [oblasti](https://azure.microsoft.com/regions/) poblíž nebo v blízkosti jiných služeb, ke kterým mají přístup vaše funkce. |

    Po vytvoření aplikace Function App se zobrazí oznámení a použije se balíček pro nasazení. Pokud chcete zobrazit výsledky vytvoření a nasazení, včetně prostředků Azure, které jste vytvořili, vyberte **Zobrazit výstup** v tomto oznámení.

### <a name="get-the-url-of-an-http-triggered-function-in-azure"></a><a name="get-the-url-of-the-deployed-function"></a>Získat adresu URL funkce aktivované protokolem HTTP v Azure

Pro volání funkce aktivované protokolem HTTP z klienta budete potřebovat adresu URL funkce při jejím nasazení do aplikace Function App. Tato adresa URL zahrnuje všechny požadované funkční klávesy. K získání těchto adres URL pro nasazené funkce můžete použít rozšíření. Pokud chcete jenom spustit vzdálenou funkci v Azure, [použijte funkci spustit nyní](#run-functions-in-azure) v rozšíření.

1. Výběrem klávesy F1 otevřete paletu příkazů a potom vyhledejte a spusťte příkaz **Azure Functions: Kopírovat adresu URL funkce**.

1. Podle pokynů vyberte aplikaci Function App v Azure a pak konkrétní Trigger HTTP, který chcete vyvolat.

Adresa URL funkce se zkopíruje do schránky spolu s případnými povinnými klíči předanými `code` parametrem dotazu. Pomocí nástroje HTTP odešlete žádosti POST nebo prohlížeč pro požadavky GET na vzdálenou funkci.  

Při získávání adresy URL funkcí v Azure používá rozšíření účet Azure k automatickému načtení klíčů potřebných ke spuštění funkce. [Přečtěte si další informace o přístupových klíčích funkcí](security-concepts.md#function-access-keys). Spouštění funkcí aktivovaných jiným systémem než HTTP vyžaduje použití klíče správce.

## <a name="republish-project-files"></a>Znovu publikovat soubory projektu

Při nastavování [průběžného nasazování](functions-continuous-deployment.md)se vaše aplikace Function App v Azure aktualizuje při aktualizaci zdrojových souborů v připojeném zdrojovém umístění. Doporučujeme průběžné nasazování, ale aktualizace souborů projektu můžete také znovu publikovat z Visual Studio Code.

> [!IMPORTANT]
> Publikování do existující aplikace funkcí přepíše obsah této aplikace v Azure.

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

## <a name="run-functions"></a>Spustit funkce

Rozšíření Azure Functions umožňuje spustit jednotlivé funkce, a to buď v projektu na místním vývojovém počítači, nebo v předplatném Azure. 

V případě funkcí triggeru HTTP volá rozšíření koncový bod HTTP. Pro jiné druhy triggerů volá rozhraní API Správce, aby se spustila funkce. Tělo zprávy žádosti odeslané funkci závisí na typu triggeru. Pokud Trigger vyžaduje testovací data, budete vyzváni k zadání dat v konkrétním formátu JSON.

### <a name="run-functions-in-azure"></a>Spouštění funkcí v Azure

Spuštění funkce v Azure z Visual Studio Code. 

1. Na paletě příkazu zadejte **Azure Functions: Execute Function Now** a vyberte své předplatné Azure. 

1. Ze seznamu vyberte aplikaci Function App v Azure. Pokud nevidíte aplikaci Function App, ujistěte se, že jste přihlášeni ke správnému předplatnému. 

1. V seznamu vyberte funkci, kterou chcete spustit, a v části **Zadejte text žádosti** zadejte text zprávy žádosti. Stisknutím klávesy ENTER odešlete tuto zprávu požadavku vaší funkci. Výchozí text v textu **žádosti o zadání požadavku** by měl označovat formát těla. Pokud vaše aplikace Function App neobsahuje žádné funkce, zobrazí se chyba oznámení s touto chybou. 

1. Když se funkce spustí v Azure a vrátí odpověď, vygeneruje se oznámení v Visual Studio Code.
 
Tuto funkci můžete také spustit z oblasti **Azure: Functions** kliknutím pravým tlačítkem (stisknutím klávesy CTRL a kliknutím na Mac) funkce, kterou chcete spustit z aplikace Function App ve vašem předplatném Azure, a volbou **Spustit funkci nyní..**.

Při spouštění funkcí v Azure používá rozšíření účet Azure k automatickému načtení klíčů potřebných ke spuštění funkce. [Přečtěte si další informace o přístupových klíčích funkcí](security-concepts.md#function-access-keys). Spouštění funkcí aktivovaných jiným systémem než HTTP vyžaduje použití klíče správce.

### <a name="run-functions-locally"></a>Místní spuštění funkcí

Místní modul runtime je stejný modul runtime, který hostuje vaši aplikaci Function App v Azure. Místní nastavení jsou načítána z [local.settings.jsv souboru](#local-settings-file). Chcete-li spustit projekt Functions v místním prostředí, je nutné splnit [Další požadavky](#run-local-requirements).

#### <a name="configure-the-project-to-run-locally"></a>Nakonfigurujte projekt tak, aby běžel místně.

Modul runtime Functions používá účet Azure Storage interně pro všechny typy triggerů kromě HTTP a webhooků. Proto je potřeba nastavit klíč **Values. AzureWebJobsStorage** na platný připojovací řetězec účtu Azure Storage.

Tato část používá [rozšíření Azure Storage pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) se [Průzkumník služby Azure Storage](https://storageexplorer.com/) pro připojení k a načtení připojovacího řetězce úložiště.

Nastavení připojovacího řetězce účtu úložiště:

1. V aplikaci Visual Studio otevřete **Průzkumník cloudu**, rozbalte **účet úložiště**  >  a pak vyberte **vlastnosti** a zkopírujte hodnotu **primárního připojovacího řetězce** .

2. V projektu otevřete local.settings.jsv souboru a nastavte hodnotu klíče **AzureWebJobsStorage** na připojovací řetězec, který jste zkopírovali.

3. Opakujte předchozí krok a přidejte jedinečné klíče do pole **hodnoty** pro všechna ostatní připojení požadovaná funkcemi.

Další informace najdete v tématu [místní nastavení souboru](#local-settings-file).

#### <a name="debug-functions-locally"></a><a name="debugging-functions-locally"></a>Místní ladění funkcí  

Chcete-li ladit své funkce, vyberte F5. Pokud jste ještě nestáhli [základní nástroje][Azure Functions Core Tools], budete vyzváni k tomu. Když jsou nainstalované a běžící základní nástroje, zobrazí se výstup v terminálu. To je stejné jako spuštění `func host start` příkazu Core Tools z terminálu, ale s dalšími úlohami sestavení a připojeným ladicím programem.  

Když je projekt spuštěný, můžete použít funkci **Spustit funkci nyní...** a aktivovat vaše funkce, jako byste to chtěli při nasazení projektu do Azure. V případě projektu spuštěného v režimu ladění se zarážky narazí v Visual Studio Code, jak byste očekávali. 

1. Na paletě příkazu zadejte **Azure Functions: Execute Function Now** a klikněte na **místní projekt**. 

1. Vyberte funkci, kterou chcete spustit v projektu, a do pole **Zadejte text žádosti** zadejte text zprávy žádosti. Stisknutím klávesy ENTER odešlete tuto zprávu požadavku vaší funkci. Výchozí text v textu **žádosti o zadání požadavku** by měl označovat formát těla. Pokud vaše aplikace Function App neobsahuje žádné funkce, zobrazí se chyba oznámení s touto chybou. 

1. Když je funkce spuštěna místně a po přijetí odpovědi, je v Visual Studio Code vyvolána oznámení. Informace o spuštění funkce se zobrazí na panelu **terminálu** .

Spouštění funkcí místně nevyžaduje použití klíčů. 

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Ve výchozím nastavení se tato nastavení nemigrují automaticky, když je projekt publikován do Azure. Po dokončení publikování máte možnost publikovat nastavení z local.settings.jsdo aplikace Function App v Azure. Další informace najdete v tématu  [publikování nastavení aplikace](#publish-application-settings).

Hodnoty v **connectionStrings** se nikdy nepublikují.

Hodnoty nastavení aplikace Functions lze v kódu přečíst také jako proměnné prostředí. Další informace najdete v oddílech s proměnnými prostředí těchto referenčních článků specifických pro konkrétní jazyk:

* [Předkompilovaná C#](functions-dotnet-class-library.md#environment-variables)
* [Skript jazyka C# (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

## <a name="application-settings-in-azure"></a>Nastavení aplikace v Azure

Nastavení v local.settings.jssouboru v projektu by mělo být stejné jako nastavení aplikace v aplikaci Function App v Azure. Všechna nastavení, která přidáte do local.settings.js, musíte také přidat do aplikace Function App v Azure. Tato nastavení nejsou nahrána automaticky při publikování projektu. Podobně se všechna nastavení, která vytvoříte ve vaší aplikaci Function App na [portálu](functions-how-to-use-azure-function-app-settings.md#settings) , musí stáhnout do vašeho místního projektu.

### <a name="publish-application-settings"></a>Publikování nastavení aplikace

Nejjednodušší způsob, jak publikovat požadovaná nastavení do aplikace Function App v Azure, je použít odkaz **nastavení nahrávání** , který se zobrazí po publikování projektu:

![Nahrát nastavení aplikace](./media/functions-develop-vs-code/upload-app-settings.png)

Nastavení můžete také publikovat pomocí příkazu **Azure Functions: nahrání místního nastavení** v paletě příkazů. Do nastavení aplikace v Azure můžete přidat jednotlivá nastavení pomocí příkazu **Azure Functions: přidat nové nastavení** .

> [!TIP]
> Nezapomeňte uložit local.settings.jsdo souboru předtím, než ho publikujete.

Pokud je místní soubor zašifrovaný, dešifruje, publikuje a znovu zašifruje. Pokud v obou umístěních existují nastavení, která mají konfliktní hodnoty, zobrazí se výzva, abyste si zvolili, jak pokračovat.

Seznamte se s existujícími nastaveními aplikací v oblasti **Azure: Functions** rozbalením předplatného, aplikace Function App a **nastavení aplikace**.

![Zobrazit nastavení aplikace Function App v Visual Studio Code](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Stažení nastavení z Azure

Pokud jste v Azure vytvořili nastavení aplikace, můžete je stáhnout do local.settings.jsdo souboru pomocí příkazu **Azure Functions: Stáhnout Vzdálená nastavení** .

Pokud je místní soubor zašifrovaný, je stejně jako u nahrávání šifrovaný, aktualizovaný a šifrovaný znovu. Pokud v obou umístěních existují nastavení, která mají konfliktní hodnoty, zobrazí se výzva, abyste si zvolili, jak pokračovat.

## <a name="monitoring-functions"></a>Funkce monitorování

Když [spouštíte funkce místně](#run-functions-locally), data protokolu se streamují do konzoly terminálu. Data protokolu můžete získat také v případě, že projekt Functions běží v aplikaci Function App v Azure. Můžete se buď připojit k protokolům streamování v Azure, abyste viděli data protokolu téměř v reálném čase, nebo můžete povolit Application Insights pro úplnější porozumění tomu, jak se aplikace Function app chová.

### <a name="streaming-logs"></a>Protokoly streamování

Při vývoji aplikace je často užitečné zobrazit informace o protokolování téměř v reálném čase. Můžete zobrazit datový proud souborů protokolu generovaných vašimi funkcemi. Tento výstup je příkladem protokolů streamování pro požadavek na funkci spuštěnou protokolem HTTP:

![Výstup protokolů streamování pro Trigger HTTP](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

Další informace najdete v tématu [streamování protokolů](functions-monitoring.md#streaming-logs).

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Protokoly streamování podporují jenom jednu instanci hostitele Functions. Pokud je vaše funkce škálovaná na více instancí, data z jiných instancí se v datovém proudu protokolu nezobrazují. [Live Metrics Stream](../azure-monitor/app/live-stream.md) v Application Insights podporuje více instancí. I když téměř v reálném čase je analýza streamování založená na [ukázkových datech](configure-monitoring.md#configure-sampling).

### <a name="application-insights"></a>Application Insights

Doporučujeme vám monitorovat provádění vašich funkcí integrací aplikace Function App s Application Insights. Při vytváření aplikace Function App v Azure Portal k této integraci dojde ve výchozím nastavení. Při vytváření aplikace Function App během publikování sady Visual Studio je nutné integrovat Application Insights sami. Další informace najdete v tématu [Povolení integrace Application Insights](configure-monitoring.md#enable-application-insights-integration).

Další informace o monitorování pomocí Application Insights najdete v tématu [monitorování Azure Functions](functions-monitoring.md).

## <a name="c-script-projects"></a>\#Projekty skriptu jazyka C

Ve výchozím nastavení jsou všechny projekty C# vytvořeny jako [projekty knihovny kompilovaných tříd jazyka c#](functions-dotnet-class-library.md). Pokud raději chcete pracovat s projekty skriptů v jazyce C#, je nutné vybrat skript jazyka C# jako výchozí jazyk v nastavení rozšíření Azure Functions:

1. Vyberte   >    >  **Nastavení** předvoleb souboru.

1. Přejít na **rozšíření uživatelských nastavení**  >    >  **Azure Functions**.

1. V Azure Functions vyberte **skript C #** **: jazyk projektu**.

Po dokončení tohoto postupu budou volání do základních základních nástrojů zahrnovat `--csx` možnost, která generuje a publikuje soubory projektu skriptu C# (. csx). Pokud máte zadaný výchozí jazyk, všechny projekty, které vytvoříte ve výchozím nastavení pro projekty skriptu C#. Nejste vyzváni k výběru jazyka projektu, pokud je nastavena výchozí hodnota. Chcete-li vytvořit projekty v jiných jazycích, musíte toto nastavení změnit nebo ho odebrat z settings.jsuživatele v souboru. Po odebrání tohoto nastavení budete znovu vyzváni k výběru jazyka při vytváření projektu.

## <a name="command-palette-reference"></a>Reference k paletě příkazů

Rozšíření Azure Functions poskytuje užitečné grafické rozhraní v oblasti pro interakci s aplikacemi Function App v Azure. Stejné funkce jsou také k dispozici jako příkazy v paletě příkazů (F1). K dispozici jsou tyto příkazy Azure Functions:

|Azure Functions – příkaz  | Description  |
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
| **Odstranit nastavení** | Odstraní nastavení aplikace Function App v Azure. Toto odstranění nemá vliv na nastavení v local.settings.jssouboru. |
| **Odpojit od úložiště**  | Odebere připojení [průběžného nasazování](functions-continuous-deployment.md) mezi aplikací funkcí v Azure a úložištěm správy zdrojového kódu. |
| **Stáhnout Vzdálená nastavení** | Stáhne nastavení z vybrané aplikace Function App v Azure do vaší local.settings.jsv souboru. Pokud je místní soubor zašifrovaný, dešifruje se, aktualizuje a zašifruje znovu. Pokud v obou umístěních existují nastavení, která mají konfliktní hodnoty, zobrazí se výzva, abyste si zvolili, jak pokračovat. Před spuštěním tohoto příkazu nezapomeňte uložit změny local.settings.jsv souboru. |
| **Upravit nastavení** | Změní hodnotu existujícího nastavení aplikace Function App v Azure. Tento příkaz nemá vliv na nastavení v local.settings.jssouboru.  |
| **Nastavení šifrování** | Šifruje jednotlivé položky v poli `Values` v [místním nastavení](#local-settings-file). V tomto souboru `IsEncrypted` je také nastaveno na `true` , který určuje, že místní modul runtime bude před použitím dešifrovat nastavení. Zašifrujte místní nastavení, abyste snížili riziko nevracení cenných informací. V Azure jsou nastavení aplikace vždycky uložená zašifrovaná. |
| **Spustit funkci hned** | Ručně spustí funkci pomocí rozhraní API pro správu. Tento příkaz se používá pro testování v místním prostředí během ladění a proti funkcím spuštěným v Azure. Když se aktivuje funkce v Azure, rozšíření nejprve automaticky získá klíč správce, který používá k volání rozhraní API vzdáleného správce, která spouštějí funkce v Azure. Tělo zprávy odeslané do rozhraní API závisí na typu triggeru. Aktivační události časovače nevyžadují předání jakýchkoli dat. |
| **Inicializovat projekt pro použití s VS Code** | Přidá požadované soubory projektu Visual Studio Code do existujícího projektu Functions. Tento příkaz použijte pro práci s projektem, který jste vytvořili pomocí základních nástrojů. |
| **Nainstalovat nebo aktualizovat Azure Functions Core Tools** | Nainstaluje nebo aktualizuje [Azure Functions Core Tools], která se používá ke spouštění funkcí místně. |
| **Opětovné nasazení**  | Umožňuje znovu nasadit soubory projektu z připojeného úložiště Git do konkrétního nasazení v Azure. Chcete-li znovu publikovat místní aktualizace z Visual Studio Code, [projekt znovu publikujte](#republish-project-files). |
| **Přejmenovat nastavení** | Změní název klíče stávajícího nastavení aplikace Function App v Azure. Tento příkaz nemá vliv na nastavení v local.settings.jssouboru. Po přejmenování nastavení v Azure byste [tyto změny měli stáhnout do místního projektu](#download-settings-from-azure). |
| **Restartovat** | Restartuje aplikaci Function App v Azure. Nasazení aktualizací také restartuje funkci aplikace Function App. |
| **Nastavení AzureWebJobsStorage**| Nastaví hodnotu `AzureWebJobsStorage` nastavení aplikace. Toto nastavení vyžaduje Azure Functions. Nastavuje se při vytváření aplikace funkcí v Azure. |
| **Zahájení** | Spustí zastavenou aplikaci Function App v Azure. |
| **Spustit streamování protokolů** | Spustí protokoly streamování aplikace Function App v Azure. Při řešení potíží se vzdáleným zpracováním v Azure použijte protokoly streamování, pokud potřebujete zobrazit informace o protokolování téměř v reálném čase. Další informace najdete v tématu [streamování protokolů](#streaming-logs). |
| **Zastavit** | Zastaví aplikaci Function App, která běží v Azure. |
| **Zastavit streamování protokolů** | Zastaví protokoly streamování aplikace Function App v Azure. |
| **Přepnout jako nastavení slotu** | Pokud je tato možnost povolená, zajistí, že nastavení aplikace pro danou slot nasazení přetrvává. |
| **Odinstalace Azure Functions Core Tools** | Odebere Azure Functions Core Tools, který je vyžadován rozšířením. |
| **Odeslat místní nastavení** | Nahraje nastavení z vaší local.settings.jsdo souboru do vybrané aplikace Function App v Azure. Pokud je místní soubor zašifrovaný, dešifruje se, nahraje a zašifruje znovu. Pokud v obou umístěních existují nastavení, která mají konfliktní hodnoty, zobrazí se výzva, abyste si zvolili, jak pokračovat. Před spuštěním tohoto příkazu nezapomeňte uložit změny local.settings.jsv souboru. |
| **Zobrazit potvrzení na GitHubu** | Pokud je vaše aplikace Function App připojena k úložišti, zobrazí se nejnovější potvrzení v konkrétním nasazení. |
| **Zobrazit protokoly nasazení** | Zobrazuje protokoly pro konkrétní nasazení aplikace Function App v Azure. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure Functions Core Tools najdete v tématu věnovaném [práci s Azure Functions Core Tools](functions-run-local.md).

Další informace o vývoji funkcí jako knihoven tříd .NET naleznete v tématu [Azure Functions C# Reference pro vývojáře](functions-dotnet-class-library.md). Tento článek také obsahuje odkazy na příklady použití atributů k deklaraci různých typů vazeb, které jsou podporovány nástrojem Azure Functions.

[Rozšíření Azure Functions pro Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md