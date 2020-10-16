---
title: Publikování funkce do Azure pomocí Java a Gradle
description: Vytvoření a publikování funkce aktivované protokolem HTTP do Azure pomocí jazyků Java a Gradle
author: KarlErickson
ms.custom: devx-track-java
ms.author: karler
ms.topic: how-to
ms.date: 04/08/2020
ms.openlocfilehash: a9592d848398c71bc573c073f0b712898f666640
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104865"
---
# <a name="use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>Vytvoření a publikování funkce v Azure pomocí Java a Gradle

V tomto článku se dozvíte, jak sestavit a publikovat projekt funkce jazyka Java pro Azure Functions pomocí nástroje příkazového řádku Gradle. Jakmile budete hotovi, váš kód funkce se spustí v Azure v [plánu hostování bez serveru](functions-scale.md#consumption-plan) a spustí se požadavkem http. 

> [!NOTE]
> Pokud Gradle není preferovaným vývojářským nástrojem, Projděte si podobné kurzy pro vývojáře v jazyce Java, které využívají [Maven](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java), [IntelliJ](/azure/developer/java/toolkit-for-intellij/quickstart-functions) a [vs Code](./functions-create-first-function-vs-code.md?pivots=programming-language-java).

## <a name="prerequisites"></a>Předpoklady

K vývoji funkcí pomocí Javy musíte mít nainstalovaný následující software:

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support) verze 8
- [Azure CLI]
- [Azure Functions Core Tools](./functions-run-local.md#v2) verze 2.6.666 nebo vyšší
- [Gradle](https://gradle.org/)verze 4,10 a vyšší

Budete také potřebovat aktivní předplatné Azure. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Pro dokončení tohoto rychlého startu musí být proměnná prostředí JAVA_HOME nastavená na umístění instalace sady JDK.

## <a name="prepare-a-functions-project"></a>Příprava projektu Functions

Pomocí následujícího příkazu naklonujte vzorový projekt:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Otevřete `build.gradle` a změňte `appName` v následující části jedinečný název, aby nedocházelo ke konfliktu názvů domén při nasazování do Azure. 

```gradle
azurefunctions {
    resourceGroup = 'java-functions-group'
    appName = 'azure-functions-sample-demo'
    pricingTier = 'Consumption'
    region = 'westus'
    runtime {
      os = 'windows'
    }
    localDebug = "transport=dt_socket,server=y,suspend=n,address=5005"
}
```

Otevřete nový soubor Function. Java z cesty *Src/Main/Java* v textovém editoru a zkontrolujte generovaný kód. Tento kód je funkce [aktivovaná protokolem HTTP](functions-bindings-http-webhook.md) , která vypisuje tělo žádosti. 

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=generate-project)

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Spuštěním následujícího příkazu Sestavte a spusťte projekt funkce:

```bash
gradle jar --info
gradle azureFunctionsRun
```
Výstup se zobrazí jako následující z Azure Functions Core Tools při místním spuštění projektu:

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
</pre>

Aktivujte funkci z příkazového řádku pomocí následujícího příkazu složeného v novém okně terminálu:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

Očekávaný výstup je následující:

<pre>
Hello, AzureFunctions
</pre>

> [!NOTE]
> Pokud nastavíte authLevel na `FUNCTION` nebo `ADMIN` , [klíč funkce](functions-bindings-http-webhook-trigger.md#authorization-keys) se při místním spuštění nepožaduje.  

Pomocí klávesové zkratky `Ctrl+C` v terminálu zastavte kód aplikace.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=local-run)

## <a name="deploy-the-function-to-azure"></a>Nasazení funkce do Azure

Aplikace Function App a související prostředky se vytvoří v Azure při prvním nasazení aplikace Function App. Než budete moct nasadit, přihlaste se k předplatnému Azure pomocí příkazu [AZ Login](/cli/azure/authenticate-azure-cli) Azure CLI. 

```azurecli
az login
```

> [!TIP]
> Pokud má váš účet přístup k několika předplatným, použijte příkaz [AZ Account set](/cli/azure/account#az-account-set) k nastavení výchozího předplatného pro tuto relaci. 

K nasazení projektu do nové aplikace Function App použijte následující příkaz. 

```bash
gradle azureFunctionsDeploy
```

Tím se v Azure vytvoří následující prostředky na základě hodnot v souboru Build. Gradle:

+ Skupina prostředků. Pojmenovaná se _zdrojem_ prostředků, kterou jste zadali.
+ Účet úložiště. Požadováno funkcemi. Název se vygeneruje náhodně na základě požadavků na název účtu úložiště.
+ App Service plán. Plán spotřeby bez serveru hostuje pro vaši aplikaci Function App v zadaném _appRegion_. Název se vygeneruje náhodně.
+ Aplikace Function App Function App je jednotka pro nasazení a spouštění pro vaše funkce. Název je vaše _AppName_a je připojen s náhodně generovaným číslem. 

Nasazení také zabalí soubory projektu a nasadí je do nové aplikace Function App pomocí [nasazení zip](functions-deployment-technologies.md#zip-deploy)s povoleným režimem spuštění z balíčku.

AuthLevel pro Trigger HTTP v ukázkovém projektu je `ANONYMOUS` , který přeskočí ověřování. Pokud však použijete jiné authLevel jako `FUNCTION` nebo `ADMIN` , musíte získat klíč funkce pro volání koncového bodu funkce přes protokol HTTP. Nejjednodušší způsob, jak získat klíč funkce, je z [Azure Portal].

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=deploy)

## <a name="get-the-http-trigger-url"></a>Získat adresu URL triggeru HTTP

Můžete získat adresu URL potřebnou k aktivaci vaší funkce s klíčem funkce z Azure Portal. 

1. Přejděte na [Azure Portal], přihlaste se **, do horní** části stránky zadejte _AppName_ vaší aplikace Function App a stiskněte klávesu ENTER.
 
1. Ve vaší aplikaci Function App vyberte **funkce**, zvolte funkci a pak klikněte na **</> získat adresu URL funkce** v pravém horním rohu. 

    :::image type="content" source="./media/functions-create-first-java-gradle/get-function-url-portal.png" alt-text="Kopírování adresy URL funkce z webu Azure Portal":::

1. Zvolte **výchozí (klíč funkce)** a vyberte **Kopírovat**. 

Pro přístup k funkci teď můžete použít zkopírovanou adresu URL.

## <a name="verify-the-function-in-azure"></a>Ověření funkce v Azure

Pokud chcete ověřit aplikaci Function App běžící v Azure pomocí `cURL` , nahraďte adresu URL z níže uvedené ukázky adresou URL, kterou jste zkopírovali z portálu.

```console
curl -w "\n" http://azure-functions-sample-demo.azurewebsites.net/api/HttpExample --data AzureFunctions
```

Tím se odešle požadavek POST koncovému bodu funkce `AzureFunctions` v těle žádosti. Zobrazí se následující odpověď.

<pre>
Hello, AzureFunctions
</pre>

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=verify-deployment)

## <a name="next-steps"></a>Další kroky

Vytvořili jste projekt funkcí jazyka Java pomocí funkce aktivované protokolem HTTP, spustíte ji na místním počítači a nasadíte ji do Azure. Nyní svou funkci rozšíříte do...

> [!div class="nextstepaction"]
> [Přidání výstupní vazby fronty Azure Storage](functions-add-output-binding-storage-queue-java.md)


[Azure CLI]: /cli/azure
[Azure Portal]: https://portal.azure.com