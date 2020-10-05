---
title: Připojte funkci jazyka Java k Azure Storage
description: Naučte se připojit funkci Java aktivovanou protokolem HTTP, která se Azure Storage pomocí výstupní vazby úložiště fronty.
author: KarlErickson
ms.custom: devx-track-java
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: 9f512e3bbf7947361fa9890e9514693610c9f99d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87321951"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Připojte funkci jazyka Java k Azure Storage

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

V tomto článku se dozvíte, jak integrovat funkci, kterou jste vytvořili v [předchozím článku rychlý Start](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java&tabs=bash,browser) s frontou Azure Storage. Výstupní vazba, kterou přidáte do této funkce, zapisuje data z požadavku HTTP do zprávy ve frontě.

Většina vazeb vyžaduje uložený připojovací řetězec, který funkce používá pro přístup k vázané službě. Aby bylo toto připojení snazší, použijte účet úložiště, který jste vytvořili v aplikaci Function App. Připojení k tomuto účtu je již Uloženo v nastavení aplikace s názvem `AzureWebJobsStorage` .  

## <a name="prerequisites"></a>Předpoklady

Než začnete s tímto článkem, proveďte kroky v [části 1 rychlého startu Java](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java&tabs=bash,browser).

## <a name="download-the-function-app-settings"></a>Stažení nastavení Function App

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Povolit sady rozšíření

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Nyní můžete přidat výstupní vazbu úložiště do projektu.

## <a name="add-an-output-binding"></a>Přidání výstupní vazby

[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Přidání kódu, který používá výstupní vazbu

[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]

Nyní jste připraveni vyzkoušet novou výstupní vazbu místně.

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Stejně jako dřív použijte následující příkaz pro sestavení projektu a místní spuštění Functions Runtime:

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
> Vzhledem k tomu, že jste povolili sady rozšíření v host.jsna, bylo [rozšíření pro vytváření úložiště](functions-bindings-storage-blob.md#add-to-your-functions-app) během spouštění staženo a nainstalováno společně s dalšími rozšířeními vazby společnosti Microsoft.

Stejně jako dřív aktivujte funkci z příkazového řádku pomocí funkce kudrlinkou v novém okně terminálu:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Tentokrát výstupní vazba také vytvoří ve svém účtu úložiště frontu s názvem `outqueue` a přidá zprávu se stejným řetězcem.

Dále pomocí Azure CLI zobrazíte novou frontu a ověříte, že se přidala zpráva. Frontu můžete také zobrazit pomocí [Průzkumník služby Microsoft Azure Storage][Azure Storage Explorer] nebo v [Azure Portal](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Znovu nasadit projekt 

Pokud chcete aktualizovat publikovanou aplikaci, spusťte následující příkaz znovu:  

# <a name="maven"></a>[Maven](#tab/maven)  
```bash
mvn azure-functions:deploy
```
# <a name="gradle"></a>[Gradle](#tab/gradle)  
```bash
gradle azureFunctionsDeploy
```
---

Znovu můžete použít oblé k otestování nasazené funkce. Stejně jako dřív předejte hodnotu `AzureFunctions` v těle požadavku post na adresu URL, jako v tomto příkladu:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Opětovným [zkontrolováním zprávy fronty úložiště](#query-the-storage-queue) můžete ověřit, zda výstupní vazba vygeneruje novou zprávu ve frontě, podle očekávání.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Další kroky

Aktualizovali jste funkci aktivovanou protokolem HTTP, která zapisuje data do fronty úložiště. Další informace o vývoji Azure Functions pomocí jazyka Java najdete v tématu [Příručka pro vývojáře v Azure Functions Java](functions-reference-java.md) a [Azure Functions triggery a vazby](functions-triggers-bindings.md). Příklady kompletních projektů funkcí v jazyce Java naleznete v tématu [ukázky funkcí jazyka Java](/samples/browse/?products=azure-functions&languages=Java). 

Dále byste měli povolit Application Insights monitorování aplikace Function App:

> [!div class="nextstepaction"]
> [Povolení integrace Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/
