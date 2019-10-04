---
title: Přidání vazby fronty Azure Storage k funkci Pythonu
description: Naučte se, jak do funkce Pythonu Přidat výstupní vazbu Azure Storage fronty pomocí základních nástrojů Azure CLI a Functions.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 92ee9b0a8a0906bca31d7dcb1730c3464d0d6cbc
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839183"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Přidání vazby fronty Azure Storage k funkci Pythonu

Azure Functions umožňuje připojit služby Azure a další prostředky k funkcím bez nutnosti psát vlastní kód pro integraci. Tyto *vazby*, které představují vstupní i výstupní, jsou deklarovány v rámci definice funkce. Data z vazeb jsou k dispozici funkci jako parametry. *Trigger* je speciální typ vstupní vazby. I když má funkce pouze jednu Trigger, může mít více vstupních a výstupních vazeb. Další informace najdete v tématu [Azure Functions triggery a koncepty vazeb](functions-triggers-bindings.md).

V tomto článku se dozvíte, jak integrovat funkci, kterou jste vytvořili v [předchozím článku rychlý Start](functions-create-first-function-python.md) s frontou Azure Storage. Výstupní vazba, kterou přidáte do této funkce, zapisuje data z požadavku HTTP do zprávy ve frontě.

Většina vazeb vyžaduje uložený připojovací řetězec, který funkce používá pro přístup k vázané službě. Aby bylo toto připojení snazší, použijte účet úložiště, který jste vytvořili v aplikaci Function App. Připojení k tomuto účtu je již Uloženo v nastavení aplikace s názvem `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto článkem, proveďte kroky v [části 1 rychlého startu v Pythonu](functions-create-first-function-python.md).

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="download-the-function-app-settings"></a>Stažení nastavení Function App

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Povolit sady rozšíření

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Nyní můžete přidat výstupní vazbu úložiště do projektu.

## <a name="add-an-output-binding"></a>Přidání výstupní vazby

V případě funkcí vyžaduje každý typ vazby `direction`, `type` a jedinečné `name`, které budou definovány v souboru Function. JSON. Způsob, jakým definujete tyto atributy, závisí na jazyku aplikace Function App.

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Přidat kód, který používá výstupní vazbu

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

Když použijete výstupní vazbu, nemusíte používat Azure Storage kód SDK pro ověřování, získání odkazu na frontu nebo zápis dat. Úlohy za běhu functions a Queue výstupní vazby jsou za vás.

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Stejně jako dřív pomocí následujícího příkazu spusťte modul runtime Functions místně:

```bash
func host start
```

> [!NOTE]  
> Vzhledem k tomu, že v předchozím rychlém startu jste povolili sady rozšíření v Host. JSON, bylo [rozšíření pro vytváření úložiště](functions-bindings-storage-blob.md#packages---functions-2x) během spouštění staženo a nainstalováno společně s dalšími rozšířeními vazby společnosti Microsoft.

Zkopírujte adresu URL funkce `HttpTrigger` z výstupu za běhu a vložte ji do adresního řádku prohlížeče. Připojí řetězec dotazu `?name=<yourname>` k této adrese URL a spustí požadavek. V prohlížeči by se měla zobrazit stejná odpověď jako v předchozím článku.

Tentokrát výstupní vazba také vytvoří ve svém účtu úložiště frontu s názvem `outqueue` a přidá zprávu se stejným řetězcem.

Dále pomocí Azure CLI zobrazíte novou frontu a ověříte, že se přidala zpráva. Frontu můžete také zobrazit pomocí [Průzkumník služby Microsoft Azure Storage][Azure Storage Explorer] nebo v [Azure Portal](https://portal.azure.com).

### <a name="set-the-storage-account-connection"></a>Nastavení připojení účtu úložiště

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

### <a name="query-the-storage-queue"></a>Dotazování fronty úložiště

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

Teď je čas na opětovné publikování aktualizované aplikace Function App do Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

Znovu můžete k otestování nasazené funkce použít kudrlinkou nebo prohlížeč. Stejně jako dřív připojíte řetězec dotazu `&name=<yourname>` k adrese URL, jako v tomto příkladu:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

Můžete [zkontrolovat zprávu fronty úložiště](#query-the-storage-queue) a ověřit, zda výstupní vazba znovu generuje novou zprávu ve frontě.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Další kroky

Aktualizovali jste funkci aktivovanou protokolem HTTP, která zapisuje data do fronty úložiště. Další informace o vývoji Azure Functions pomocí Pythonu najdete v [příručce pro vývojáře Azure Functions Pythonu](functions-reference-python.md) a v [Azure Functions triggerech a vazbách](functions-triggers-bindings.md). Příklady kompletních projektů funkcí v Pythonu najdete v tématu [ukázky funkcí Pythonu](/samples/browse/?products=azure-functions&languages=python). 

Dále byste měli povolit Application Insights monitorování aplikace Function App:

> [!div class="nextstepaction"]
> [Povolit integraci Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/