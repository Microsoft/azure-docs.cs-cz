---
title: Použití jazyků Java a Maven/Gradle k publikování funkce do Azure
description: Vytvoření a publikování funkce aktivované protokolem HTTP do Azure pomocí jazyků Java a Maven nebo Gradle.
author: KarlErickson
ms.author: karler
ms.topic: quickstart
ms.date: 08/10/2018
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: ad3b38a12020c56c31e03879b3fbcb9a8dda25f1
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136863"
---
# <a name="quickstart-use-java-and-mavengradle-to-create-and-publish-a-function-to-azure"></a>Rychlý Start: použití jazyků Java a Maven/Gradle k vytvoření a publikování funkce v Azure

V tomto článku se dozvíte, jak sestavit a publikovat funkci Java pro Azure Functions pomocí nástroje příkazového řádku Maven/Gradle. Jakmile budete hotovi, váš kód funkce se spustí v Azure v [plánu hostování bez serveru](functions-scale.md#consumption-plan) a spustí se požadavkem http.

<!--
> [!NOTE] 
> You can also create a Kotlin-based Azure Functions project by using the azure-functions-kotlin-archetype instead. Visit the [GitHub repository](https://github.com/microsoft/azure-maven-archetypes/tree/develop/azure-functions-kotlin-archetype) for more information.
-->

## <a name="prerequisites"></a>Požadavky

K vývoji funkcí pomocí Javy musíte mít nainstalovaný následující software:

- [Java Developer Kit](https://aka.ms/azure-jdks) verze 8
- [Azure CLI]
- [Azure Functions Core Tools](./functions-run-local.md#v2) verze 2.6.666 nebo vyšší
::: zone pivot="java-build-tools-maven" 
- [Apache Maven](https://maven.apache.org) verze 3.0 nebo novější
::: zone-end

::: zone pivot="java-build-tools-gradle"  
- [Gradle](https://gradle.org/)verze 4,10 a vyšší
::: zone-end 

Budete také potřebovat aktivní předplatné Azure. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


> [!IMPORTANT]
> Pro dokončení tohoto rychlého startu musí být proměnná prostředí JAVA_HOME nastavená na umístění instalace sady JDK.

## <a name="prepare-a-functions-project"></a>Příprava projektu Functions

::: zone pivot="java-build-tools-maven" 
Spuštěním následujícího příkazu v prázdné složce vygenerujte projekt Functions z [archetypu Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype 
```

> [!NOTE]
> Pokud používáte PowerShell, nezapomeňte přidat "" kolem parametrů.

> [!NOTE]
> Pokud máte problémy se spuštěním příkazu, podívejte se na to, co se používá `maven-archetype-plugin` verze. Vzhledem k tomu, že příkaz spouštíte v prázdném adresáři bez `.pom` souboru, může se pokusit použít modul plug-in starší verze z `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin`, pokud jste Maven upgradovali ze starší verze. Pokud ano, zkuste odstranit adresář `maven-archetype-plugin` a znovu spustit příkaz.

Maven vás vyzve k zadání hodnot potřebných k dokončení generování projektu při nasazení. Po zobrazení výzvy zadejte následující hodnoty:

| Hodnota | Popis |
| ----- | ----------- |
| **groupId** | Hodnota, která jednoznačně identifikuje váš projekt napříč všemi projekty, podle [pravidel pro pojmenovávání balíčků](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) pro Java. Příklady v tomto rychlém startu používají `com.fabrikam.functions`. |
| **artifactId** | Hodnota, která představuje název jar bez čísla verze. Příklady v tomto rychlém startu používají `fabrikam-functions`. |
| **znění** | Vyberte výchozí hodnotu `1.0-SNAPSHOT`. |
| **balíček** | Hodnota, která je balíčkem Java pro vygenerovaný kód funkce. Použijte výchozí hodnotu. Příklady v tomto rychlém startu používají `com.fabrikam.functions`. |
| **appName** | Globálně jedinečný název, který identifikuje novou aplikaci Function App v Azure. Použijte výchozí, což je _artifactId_ připojené k náhodnému číslu. Poznamenejte si tuto hodnotu, budete ji potřebovat později. |
| **appRegion** | Vyberte [oblast](https://azure.microsoft.com/regions/) ve své blízkosti nebo v blízkosti jiných služeb, které vaše funkce využívají. Výchozí formát je `westus`. Spuštěním tohoto příkazu rozhraní příkazového [Azure CLI] zobrazíte seznam všech oblastí:<br/>`az account list-locations --query '[].{Name:name}' -o tsv` |
| **resourceGroup** | Název nové [skupiny prostředků](../azure-resource-manager/management/overview.md) , ve které se má aplikace Function App vytvořit Použijte `myResourceGroup`, které jsou používány příklady v tomto rychlém startu. Skupina prostředků musí být pro vaše předplatné Azure jedinečná.|

Zadejte `Y` nebo stiskněte klávesu ENTER a potvrďte ji.

Maven vytvoří soubory projektu v nové složce s názvem _artifactId_, který v tomto příkladu je `fabrikam-functions`. Spuštěním následujícího příkazu změňte adresář na složku vytvořeného projektu.
```bash
cd fabrikam-function
```

::: zone-end 
::: zone pivot="java-build-tools-gradle"
Pomocí následujícího příkazu naklonujte vzorový projekt:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Otevřete `build.gradle` a změňte `appName` v následující části na jedinečný název, aby nedocházelo ke konfliktu názvů domén při nasazování do Azure. 

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

Otevřete nový soubor Function. Java z cesty *Src/Main/Java* v textovém editoru a zkontrolujte generovaný kód. Tento kód je funkce [aktivovaná protokolem HTTP](functions-bindings-http-webhook.md) , která vypisuje tělo žádosti. 

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Spuštěním následujícího příkazu Sestavte a spusťte projekt funkce:

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

Při místním spuštění projektu se zobrazí výstup podobný následujícímu: Azure Functions Core Tools:

```output
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
```

Aktivujte funkci z příkazového řádku pomocí funkce kudrlinkou v novém okně terminálu:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

```output
Hello AzureFunctions!
```
[Klíč funkce](functions-bindings-http-webhook-trigger.md#authorization-keys) není vyžadován při místním spuštění. Pomocí klávesové zkratky `Ctrl+C` v terminálu zastavte kód aplikace.

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=local-run)

## <a name="deploy-the-function-to-azure"></a>Nasazení funkce do Azure

Aplikace Function App a související prostředky se vytvoří v Azure při prvním nasazení aplikace Function App. Než budete moct nasadit, přihlaste se k předplatnému Azure pomocí příkazu [AZ Login](/cli/azure/authenticate-azure-cli) Azure CLI. 

```azurecli
az login
```

> [!TIP]
> Pokud má váš účet přístup k několika předplatným, použijte příkaz [AZ Account set](/cli/azure/account#az-account-set) k nastavení výchozího předplatného pro tuto relaci. 

K nasazení projektu do nové aplikace Function App použijte následující příkaz. 


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

+ Skupina prostředků. Pojmenovaná se _zdrojem_ prostředků, kterou jste zadali.
+ Účet úložiště. Požadováno funkcemi. Název se vygeneruje náhodně na základě požadavků na název účtu úložiště.
+ Plán služby App Service. Hostování bez serveru pro vaši aplikaci Function App v zadaném _appRegion_. Název se vygeneruje náhodně.
+ Aplikace Function App Function App je jednotka pro nasazení a spouštění pro vaše funkce. Název je vaše _AppName_a je připojen s náhodně generovaným číslem. 

Nasazení také zabalí soubory projektu a nasadí je do nové aplikace Function App pomocí [nasazení zip](functions-deployment-technologies.md#zip-deploy)s povoleným režimem spuštění z balíčku.

Po dokončení nasazení se zobrazí adresa URL, kterou můžete použít pro přístup k koncovým bodům aplikace Function App. Protože Trigger HTTP, který jsme publikovali, používá `authLevel = AuthorizationLevel.FUNCTION`, je potřeba získat klíč funkce, který bude volat koncový bod funkce přes protokol HTTP. Nejjednodušší způsob, jak získat klíč funkce, je z [Azure Portal].

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>Získat adresu URL triggeru HTTP

<!--- We can updates this to remove portal dependency after the Maven archetype returns the full URLs with keys on publish (https://github.com/microsoft/azure-maven-plugins/issues/571). -->

Můžete získat adresu URL potřebnou k aktivaci vaší funkce s klíčem funkce z Azure Portal. 

1. Přejděte na [Azure Portal], přihlaste se **, do horní** části stránky zadejte _AppName_ vaší aplikace Function App a stiskněte klávesu ENTER.
 
1. V aplikaci Function App rozbalte **funkce (jen pro čtení)** , zvolte požadovanou funkci a potom vyberte **</> získat adresu URL funkce** v pravém horním rohu. 

    ![Kopírování adresy URL funkce z webu Azure Portal](./media/functions-create-java-maven/get-function-url-portal.png)

1. Zvolte **výchozí (klíč funkce)** a vyberte **Kopírovat**. 

Pro přístup k funkci teď můžete použít zkopírovanou adresu URL.

## <a name="verify-the-function-in-azure"></a>Ověření funkce v Azure

Pokud chcete ověřit aplikaci Function App běžící v Azure pomocí `cURL`, nahraďte adresu URL z níže uvedené ukázky adresou URL, kterou jste zkopírovali z portálu.

```console
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpExample?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Tím se odešle požadavek POST koncovému bodu funkce s `AzureFunctions` v těle žádosti. Zobrazí se následující odpověď.

```output
Hello AzureFunctions!
```

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>Další kroky

Vytvořili jste projekt funkcí jazyka Java pomocí funkce aktivované protokolem HTTP, spustíte ji na místním počítači a nasadíte ji do Azure. Nyní svou funkci rozšíříte do...

> [!div class="nextstepaction"]
> [Přidání výstupní vazby fronty Azure Storage](functions-add-output-binding-storage-queue-java.md)


[Azure CLI]: /cli/azure
[Azure Portal]: https://portal.azure.com
