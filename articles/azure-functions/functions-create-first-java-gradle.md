---
title: Publikování funkce do Azure pomocí Javy a Gradle
description: Vytvořte a publikujte funkci spouštěnou http do Azure pomocí Javy a Gradle.
author: KarlErickson
ms.author: karler
ms.topic: how-to
ms.date: 04/08/2020
ms.openlocfilehash: 5e18e035bd237fd489b715986e58d7ede726348d
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886598"
---
# <a name="use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>Vytvoření a publikování funkce do Azure pomocí Javy a Gradle

Tento článek ukazuje, jak vytvořit a publikovat projekt funkce Java do funkce Azure pomocí nástroje příkazového řádku Gradle. Až budete hotovi, kód vaší funkce se spustí v Azure v [plánu hostování bez serveru](functions-scale.md#consumption-plan) a aktivuje se požadavek HTTP. 

> [!NOTE]
> Pokud Gradle není váš preferovaný vývojový nástroj, podívejte se na naše podobné výukové programy pro devlopery Java pomocí [Maven](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java), [IntelliJ IDEA](/azure/java/intellij/azure-toolkit-for-intellij-quickstart-functions) a [VS Code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java).

## <a name="prerequisites"></a>Požadavky

K vývoji funkcí pomocí Javy musíte mít nainstalovaný následující software:

- [Java Developer Kit](https://aka.ms/azure-jdks) verze 8
- [Azure CLI]
- [Základní nástroje Azure Functions](./functions-run-local.md#v2) verze 2.6.666 nebo vyšší
- [Gradle](https://gradle.org/), verze 4.10 a vyšší

Potřebujete také aktivní předplatné Azure. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Pro dokončení tohoto rychlého startu musí být proměnná prostředí JAVA_HOME nastavená na umístění instalace sady JDK.

## <a name="prepare-a-functions-project"></a>Příprava projektu Funkce

Ke klonování ukázkového projektu použijte následující příkaz:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Otevřete `build.gradle` a `appName` změňte v následující části na jedinečný název, abyste se vyhnuli konfliktu názvů domén při nasazování do Azure. 

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

Otevřete nový soubor Function.java z cesty *src/main/java* v textovém editoru a zkontrolujte generovaný kód. Tento kód je funkce [spouštěná protokolem HTTP,](functions-bindings-http-webhook.md) která odráží tělo požadavku. 

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Spusťte následující příkaz k sestavení a spusťte projekt funkce:

```bash
gradle jar --info
gradle azureFunctionsRun
```
Výstup se zobrazí jako následující z nástrojů Azure Functions Core Tools při místním spuštění projektu:

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
</pre>

Aktivují funkci z příkazového řádku pomocí následujícího příkazu cURL v novém okně terminálu:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

Očekávaný výstup je následující:

<pre>
Hello AzureFunctions!
</pre>

[Funkční klávesa](functions-bindings-http-webhook-trigger.md#authorization-keys) není vyžadována při místním spuštění.  
Pomocí klávesové zkratky `Ctrl+C` v terminálu zastavte kód aplikace.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=local-run)

## <a name="deploy-the-function-to-azure"></a>Nasazení funkce do Azure

Aplikace funkcí a související prostředky se vytvoří v Azure při prvním nasazení aplikace funkce. Než budete moci nasadit, použijte příkaz [az přihlášení](/cli/azure/authenticate-azure-cli) Konzi k přihlášení k předplatnému Azure. 

```azurecli
az login
```

> [!TIP]
> Pokud má váš účet přístup k více předplatným, nastavte výchozí předplatné pro tuto relaci pomocí [nastaveného účtu AZ.](/cli/azure/account#az-account-set) 

Pomocí následujícího příkazu nasadit projekt do nové aplikace funkce. 

```bash
gradle azureFunctionsDeploy
```

Tím se v Azure vytvoří následující prostředky založené na hodnotách v souboru build.gradle:

+ Skupina prostředků. Pojmenováno podle _zadané skupiny prostředků._
+ Účet úložiště. Vyžaduje funkce. Název je generován náhodně na základě požadavků na název účtu úložiště.
+ Plán služby App Service. Plán spotřeby bez serveru hosting pro vaši aplikaci funkce v zadané _appRegion_. Název je generován náhodně.
+ Aplikace funkce. Aplikace funkce je jednotka nasazení a spuštění pro vaše funkce. Název je vaše _appName_, připojené s náhodně generované číslo. 

Nasazení také zabalí soubory projektu a nasadí je do nové aplikace funkce pomocí [nasazení zip](functions-deployment-technologies.md#zip-deploy), s povoleným režimem run-from-package.

Vzhledem k tomu, `authLevel = AuthorizationLevel.FUNCTION`že aktivační událost HTTP, kterou jsme publikovali, používá , je třeba získat funkční klávesu pro volání koncového bodu funkce přes protokol HTTP. Nejjednodušší způsob, jak získat funkční klávesu je z [portálu Azure].

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>Získání adresy URL aktivační události HTTP

Adresu URL potřebnou k aktivaci funkce pomocí funkčního klíče můžete získat z portálu Azure. 

1. Přejděte na [portál Azure], přihlaste se, zadejte název _aplikace app_ aplikace do **hledání** v horní části stránky a stiskněte enter.
 
1. V aplikaci funkce rozbalte **funkce (jen pro čtení),** zvolte svou funkci a pak vyberte **</> Získat adresu URL funkce** v pravém horním rohu. 

    ![Kopírování adresy URL funkce z webu Azure Portal](./media/functions-create-java-maven/get-function-url-portal.png)

1. Zvolte **výchozí (funkční klávesa)** a vyberte **Kopírovat**. 

Nyní můžete použít zkopírovanou adresu URL pro přístup k vaší funkci.

## <a name="verify-the-function-in-azure"></a>Ověření funkce v Azure

Chcete-li ověřit funkci aplikace `cURL`spuštěné v Azure pomocí , nahraďte adresu URL z ukázky níže s ADRESOu URL, které jste zkopírovali z portálu.

```console
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpExample?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

To odešle požadavek POST koncovému bodu funkce v `AzureFunctions` těle požadavku. Zobrazí se následující odpověď.

<pre>
Hello AzureFunctions!
</pre>

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>Další kroky

Vytvořili jste projekt funkcí jazyka Java s funkcí spouštěnou protokolem HTTP, spusťte ho v místním počítači a nasadíte do Azure. Nyní rozšiřte svou funkci o...

> [!div class="nextstepaction"]
> [Přidání výstupní vazby fronty azure úložiště](functions-add-output-binding-storage-queue-java.md)


[Azure CLI]: /cli/azure
[Portál Azure]: https://portal.azure.com
