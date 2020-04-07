---
title: Připojení funkce Java k Azure Storage
description: Zjistěte, jak připojit funkci Java spouštěnou http k Úložišti Azure pomocí vazby výstupu úložiště fronty.
author: KarlErickson
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: d9815fd27a57acc8b418962e610d2ae1c106edde
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673309"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Připojení funkce Java k Azure Storage

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Tento článek ukazuje, jak integrovat funkci, kterou jste vytvořili v [předchozím článku rychlého startu](functions-create-first-java-maven.md) s frontou služby Azure Storage. Výstupní vazba, kterou přidáte do této funkce, zapisuje data z požadavku HTTP do zprávy ve frontě.

Většina vazeb vyžaduje uložený připojovací řetězec, který funkce používá pro přístup k vázané službě. Chcete-li toto připojení usnadnit, použijte účet úložiště, který jste vytvořili pomocí aplikace funkce. Připojení k tomuto účtu je již `AzureWebJobsStorage`uloženo v nastavení aplikace s názvem .  

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto článku proveďte kroky v [části 1 rychlého startu jazyka Java](functions-create-first-java-maven.md).

## <a name="download-the-function-app-settings"></a>Stažení nastavení aplikace pro funkce

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Povolení rozšiřujících balíčků

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Nyní můžete přidat úložiště výstupní vazby do projektu.

## <a name="add-an-output-binding"></a>Přidání výstupní vazby

[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Přidání kódu, který používá výstupní vazbu

[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]

Nyní jste připraveni vyzkoušet novou vazbu výstupu místně.

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Stejně jako dříve použijte následující příkaz k sestavení projektu a spuštění prostředí Functions místně:

# <a name="maven"></a>[Maven](#tab/maven)
```bash
mvn clean package 
mvn azure-functions:run
```
# <a name="gradle"></a>[Gradle](#tab/gradle) 
```bash
gradle jar --info
gradle azureFunctionsRun
```
---

> [!NOTE]  
> Vzhledem k tomu, že jste povolili balíčky rozšíření v host.json, [rozšíření vazby úložiště](functions-bindings-storage-blob.md#add-to-your-functions-app) byla stažena a nainstalována pro vás při spuštění, spolu s dalšími rozšířeními vazby Společnosti Microsoft.

Stejně jako dříve aktivujete funkci z příkazového řádku pomocí cURL v novém okně terminálu:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Tentokrát výstupní vazba také vytvoří `outqueue` frontu pojmenovanou ve vašem účtu úložiště a přidá zprávu se stejným řetězcem.

Dále použijete rozhraní příkazového příkazu K zobrazení nové fronty a ověříte, že byla přidána zpráva. Frontu můžete zobrazit taky pomocí [Průzkumníka úložiště Microsoft Azure][Azure Storage Explorer] nebo na [webu Azure Portal](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Přesadit projekt 

Chcete-li publikovanou aplikaci aktualizovat, spusťte znovu následující příkaz:  

# <a name="maven"></a>[Maven](#tab/maven)  
```bash
mvn azure-functions:deploy
```
# <a name="gradle"></a>[Gradle](#tab/gradle)  
```bash
gradle azureFunctionsDeploy
```
---

Znovu můžete použít cURL k testování nasazené funkce. Stejně jako dříve `AzureFunctions` předavěte hodnotu v textu požadavku POST na adresu URL, jako v tomto příkladu:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Můžete [znovu zkontrolovat zprávu fronty úložiště](#query-the-storage-queue) a ověřit, zda výstupní vazba generuje novou zprávu ve frontě, podle očekávání.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Další kroky

Aktualizovali jste funkci aktivovanou protokolem HTTP tak, aby zapisovali data do fronty úložiště. Další informace o vývoji funkcí Azure pomocí Jazyka Java najdete v [průvodci vývojářem Azure Functions Java](functions-reference-java.md) a [v Azure Functions triggery a vazby](functions-triggers-bindings.md). Příklady kompletních projektů funkcí v javě naleznete v [ukázkách funkcí jazyka Java](/samples/browse/?products=azure-functions&languages=Java). 

Dále byste měli povolit monitorování Application Insights pro vaši aplikaci funkcí:

> [!div class="nextstepaction"]
> [Povolení integrace Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/
