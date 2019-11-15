---
title: Přidání vazby fronty Azure Storage k funkci Pythonu
description: Naučte se, jak do funkce Pythonu Přidat výstupní vazbu Azure Storage fronty.
author: ggailey777
ms.author: glenga
ms.date: 10/02/2019
ms.topic: quickstart
ms.service: azure-functions
manager: gwallace
ms.openlocfilehash: da3fb604bfb65f67e50d56a4520620cabc292b93
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082821"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Přidání vazby fronty Azure Storage k funkci Pythonu

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

V tomto článku se dozvíte, jak integrovat funkci, kterou jste vytvořili v [předchozím článku rychlý Start](functions-create-first-function-python.md) s frontou Azure Storage. Výstupní vazba, kterou přidáte do této funkce, zapisuje data z požadavku HTTP do zprávy ve frontě.

Většina vazeb vyžaduje uložený připojovací řetězec, který funkce používá pro přístup k vázané službě. Aby bylo toto připojení snazší, použijte účet úložiště, který jste vytvořili v aplikaci Function App. Připojení k tomuto účtu je již Uloženo v nastavení aplikace s názvem `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto článkem, proveďte kroky v [části 1 rychlého startu v Pythonu](functions-create-first-function-python.md).

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="download-the-function-app-settings"></a>Stažení nastavení Function App

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Povolit sady rozšíření

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Nyní můžete přidat výstupní vazbu úložiště do projektu.

## <a name="add-an-output-binding"></a>Přidání výstupní vazby

V rámci funkcí vyžaduje každý typ vazby `direction`, `type`a jedinečné `name`, které mají být definovány v souboru Function. JSON. Způsob, jakým definujete tyto atributy, závisí na jazyku aplikace Function App.

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Přidání kódu, který používá výstupní vazbu

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

Když použijete výstupní vazbu, nemusíte používat Azure Storage kód SDK pro ověřování, získání odkazu na frontu nebo zápis dat. Úlohy za běhu functions a Queue výstupní vazby jsou za vás.

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Stejně jako dřív pomocí následujícího příkazu spusťte modul runtime Functions místně:

```bash
func host start
```

> [!NOTE]  
> Vzhledem k tomu, že jste povolili sady rozšíření v Host. JSON, [rozšíření pro vytváření vazeb úložiště](functions-bindings-storage-blob.md#packages---functions-2x) se během spouštění stáhlo a nainstalovalo společně s dalšími rozšířeními vazby Microsoftu.

Zkopírujte adresu URL vaší funkce `HttpTrigger` z výstupu modulu runtime a vložte do panelu Adresa vašeho prohlížeče. Připojí řetězec dotazu `?name=<yourname>` k této adrese URL a spustí požadavek. V prohlížeči by se měla zobrazit stejná odpověď jako v předchozím článku.

Tentokrát výstupní vazba také vytvoří ve svém účtu úložiště frontu s názvem `outqueue` a přidá zprávu se stejným řetězcem.

Dále pomocí Azure CLI zobrazíte novou frontu a ověříte, že se přidala zpráva. Frontu můžete také zobrazit pomocí [Průzkumník služby Microsoft Azure Storage][Azure Storage Explorer] nebo v [Azure Portal](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Znovu nasadit projekt 

Pokud chcete aktualizovat publikovanou aplikaci, pomocí příkazu [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) Core Tools nasaďte kód projektu do Azure. V tomto příkladu nahraďte `<APP_NAME>` názvem vaší aplikace.

```command
func azure functionapp publish <APP_NAME> --build remote
```

Znovu můžete k otestování nasazené funkce použít kudrlinkou nebo prohlížeč. Stejně jako dřív připojíte řetězec dotazu `&name=<yourname>` k adrese URL, jako v tomto příkladu:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

Opětovným [zkontrolováním zprávy fronty úložiště](#query-the-storage-queue) můžete ověřit, zda výstupní vazba vygeneruje novou zprávu ve frontě, podle očekávání.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Další kroky

Aktualizovali jste funkci aktivovanou protokolem HTTP, která zapisuje data do fronty úložiště. Další informace o vývoji Azure Functions pomocí Pythonu najdete v [příručce pro vývojáře Azure Functions Pythonu](functions-reference-python.md) a v [Azure Functions triggerech a vazbách](functions-triggers-bindings.md). Příklady kompletních projektů funkcí v Pythonu najdete v tématu [ukázky funkcí Pythonu](/samples/browse/?products=azure-functions&languages=python). Další informace o cenách najdete na stránce s [cenami funkcí](https://azure.microsoft.com/pricing/details/functions/) a v článku [odhad nákladů na plán spotřeby](functions-consumption-costs.md) .

Dále byste měli povolit Application Insights monitorování aplikace Function App:

> [!div class="nextstepaction"]
> [Povolení integrace Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/