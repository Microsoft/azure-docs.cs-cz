---
title: Publikování funkce do Azure pomocí Javy a Maven/Gradle
description: Vytvořte a publikujte funkci spouštěnou http do Azure pomocí Javy a Mavenu nebo Gradle.
author: KarlErickson
ms.author: karler
ms.topic: quickstart
ms.date: 08/10/2018
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: d0f30272a4e605449c946ca402db4f3ba00735bc
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520540"
---
# <a name="quickstart-use-java-and-mavengradle-to-create-and-publish-a-function-to-azure"></a>Úvodní příručka: Vytvoření a publikování funkce do Azure pomocí Javy a Maven/Gradle

Tento článek ukazuje, jak vytvořit a publikovat funkci Java do funkce Azure pomocí nástroje příkazového řádku Maven/Gradle. Až budete hotovi, kód vaší funkce se spustí v Azure v [plánu hostování bez serveru](functions-scale.md#consumption-plan) a aktivuje se požadavek HTTP.

<!--
> [!NOTE] 
> You can also create a Kotlin-based Azure Functions project by using the azure-functions-kotlin-archetype instead. Visit the [GitHub repository](https://github.com/microsoft/azure-maven-archetypes/tree/develop/azure-functions-kotlin-archetype) for more information.
-->

## <a name="prerequisites"></a>Požadavky

K vývoji funkcí pomocí Javy musíte mít nainstalovaný následující software:

- [Java Developer Kit](https://aka.ms/azure-jdks) verze 8
- [Azure CLI]
- [Základní nástroje Azure Functions](./functions-run-local.md#v2) verze 2.6.666 nebo vyšší
::: zone pivot="java-build-tools-maven" 
- [Apache Maven](https://maven.apache.org) verze 3.0 nebo novější
::: zone-end

::: zone pivot="java-build-tools-gradle"  
- [Gradle](https://gradle.org/), verze 4.10 a vyšší
::: zone-end 

Potřebujete také aktivní předplatné Azure. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


> [!IMPORTANT]
> Pro dokončení tohoto rychlého startu musí být proměnná prostředí JAVA_HOME nastavená na umístění instalace sady JDK.

## <a name="prepare-a-functions-project"></a>Příprava projektu Funkce

::: zone pivot="java-build-tools-maven" 
Spuštěním následujícího příkazu v prázdné složce vygenerujte projekt Functions z [archetypu Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype 
```

> [!NOTE]
> Pokud používáte Powershell, nezapomeňte přidat "" kolem parametrů.

> [!NOTE]
> Pokud máte problémy se spuštěním příkazu, podívejte `maven-archetype-plugin` se, jaká verze se používá. Vzhledem k tomu, že příkaz `.pom` používáte v prázdném adresáři bez souboru, `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` může se pokoušet použít plugin starší verze z, pokud jste inovovali maven ze starší verze. Pokud ano, zkuste `maven-archetype-plugin` adresář smažit a příkaz znovu spustit.

Maven vás požádá o hodnoty potřebné k dokončení generování projektu při nasazení. Po zobrazení výzvy uveďte následující hodnoty:

| Hodnota | Popis |
| ----- | ----------- |
| **groupId** | Hodnota, která jednoznačně identifikuje váš projekt ve všech projektech podle [pravidel pro pojmenování balíčků](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) pro Jazyk Java. Příklady v tomto rychlém `com.fabrikam.functions`startu použití . |
| **artefaktId** | Hodnota, která je názvem nádoby bez čísla verze. Příklady v tomto rychlém `fabrikam-functions`startu použití . |
| **Verze** | Zvolte výchozí `1.0-SNAPSHOT`hodnotu aplikace . |
| **Balíček** | Hodnota, která je balíček Java pro kód generované funkce. Použijte výchozí hodnotu. Příklady v tomto rychlém `com.fabrikam.functions`startu použití . |

Potvrďte to toto `Y` příkazem nebo stisknutím klávesy Enter.

Maven vytvoří soubory projektu v nové složce s názvem **artifactId**, což v tomto příkladu je `fabrikam-functions`. Chcete-li změnit adresář na vytvořenou složku projektu, spusťte následující příkaz.
```bash
cd fabrikam-function
```

::: zone-end 
::: zone pivot="java-build-tools-gradle"
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
::: zone-end

Otevřete nový soubor Function.java z cesty *src/main/java* v textovém editoru a zkontrolujte generovaný kód. Tento kód je funkce [spouštěná protokolem HTTP,](functions-bindings-http-webhook.md) která odráží tělo požadavku. 

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Spusťte následující příkaz k sestavení a spusťte projekt funkce:

::: zone pivot="java-build-tools-maven" 
```bash
mvn clean package 
mvn azure-functions:run
```
::: zone-end 

::: zone pivot="java-build-tools-gradle"  
```bash
gradle jar --info
gradle azureFunctionsRun
```
::: zone-end 

Výstup se zobrazí jako následující z nástrojů Azure Functions Core Tools při místním spuštění projektu:

```output
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
```

Spusťte funkci z příkazového řádku pomocí cURL v novém okně terminálu:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

```output
Hello AzureFunctions!
```
[Funkční klávesa](functions-bindings-http-webhook-trigger.md#authorization-keys) není vyžadována při místním spuštění. Pomocí klávesové zkratky `Ctrl+C` v terminálu zastavte kód aplikace.

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


::: zone pivot="java-build-tools-maven" 
```bash
mvn azure-functions:deploy
```
::: zone-end 

::: zone pivot="java-build-tools-gradle"  
```bash
gradle azureFunctionsDeploy
```
::: zone-end

Tím se v Azure vytvoří následující prostředky:

+ Skupina prostředků. Pojmenováno podle _zadané skupiny prostředků._
+ Účet úložiště. Vyžaduje funkce. Název je generován náhodně na základě požadavků na název účtu úložiště.
+ Plán služeb aplikace. Hosting bez serveru pro vaši funkční aplikaci v zadané _appRegion_. Název je generován náhodně.
+ Aplikace funkce. Aplikace funkce je jednotka nasazení a spuštění pro vaše funkce. Název je vaše _appName_, připojené s náhodně generované číslo. 

Nasazení také zabalí soubory projektu a nasadí je do nové aplikace funkce pomocí [nasazení zip](functions-deployment-technologies.md#zip-deploy), s povoleným režimem run-from-package.

Po dokončení nasazení se zobrazí adresa URL, kterou můžete použít pro přístup ke koncovým bodům aplikace funkce. Vzhledem k tomu, `authLevel = AuthorizationLevel.FUNCTION`že aktivační událost HTTP, kterou jsme publikovali, používá , je třeba získat funkční klávesu pro volání koncového bodu funkce přes protokol HTTP. Nejjednodušší způsob, jak získat funkční klávesu je z [portálu Azure].

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>Získání adresy URL aktivační události HTTP

<!--- We can updates this to remove portal dependency after the Maven archetype returns the full URLs with keys on publish (https://github.com/microsoft/azure-maven-plugins/issues/571). -->

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

```output
Hello AzureFunctions!
```

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>Další kroky

Vytvořili jste projekt funkcí jazyka Java s funkcí spouštěnou protokolem HTTP, spusťte ho v místním počítači a nasadíte do Azure. Nyní rozšiřte svou funkci o...

> [!div class="nextstepaction"]
> [Přidání výstupní vazby fronty azure úložiště](functions-add-output-binding-storage-queue-java.md)


[Azure CLI]: /cli/azure
[portál Azure]: https://portal.azure.com
