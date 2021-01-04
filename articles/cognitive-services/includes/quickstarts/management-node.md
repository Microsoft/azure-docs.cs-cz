---
title: 'Rychlý Start: Klientská knihovna pro správu Azure pro Node.js'
description: V tomto rychlém startu Začínáme s klientskou knihovnou pro správu Azure pro Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: eddee6bc0fdcd636b7b9ffb81b121052499251b2
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/23/2020
ms.locfileid: "97745696"
---
[Referenční dokumentace](/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices)  |  [Balíček (npm)](https://www.npmjs.com/package/@azure/arm-cognitiveservices)  |  [Ukázky](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices#sample-code)

## <a name="javascript-prerequisites"></a>JavaScript – požadavky

* Platné předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/).
* Aktuální verze [Node.js](https://nodejs.org/)

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```

Spuštěním `npm init` příkazu vytvořte aplikaci uzlu se `package.json` souborem. 

```console
npm init
```

Než budete pokračovat, vytvořte soubor s názvem _index.js_ .

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Nainstalujte následující balíčky NPM:

```console
npm install @azure/arm-cognitiveservices
npm install @azure/ms-rest-js
npm install @azure/ms-rest-nodeauth
```

Soubor vaší aplikace `package.json` bude aktualizován pomocí závislostí.

### <a name="import-libraries"></a>Import knihoven

Otevřete skript _index.js_ a importujte následující knihovny.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Ověření klienta

Do kořenového adresáře skriptu přidejte následující pole a vyplňte jejich hodnoty pomocí instančního objektu, který jste vytvořili, a informací o účtu Azure.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_constants)]

Dále přidejte následující funkci, `quickstart` která bude zpracovávat hlavní práci vašeho programu. První blok kódu vytvoří objekt **CognitiveServicesManagementClient** pomocí proměnných přihlašovacích údajů, které jste zadali výše. Tento objekt je potřebný pro všechny vaše operace správy Azure.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main_auth)]

## <a name="call-management-functions"></a>Funkce správy volání

Přidejte následující kód na konec `quickstart` funkce k vypsání dostupných prostředků, vytvořte ukázkový prostředek, uveďte seznam vlastněných prostředků a pak odstraňte ukázkový prostředek. Tyto funkce definujete v dalších krocích.

## <a name="create-a-cognitive-services-resource"></a>Vytvoření prostředku služeb Cognitive Services

Pokud chcete vytvořit nový prostředek Cognitive Services a přihlásit se k jeho odběru, použijte funkci **Create** . Tato funkce přidá nový fakturovatelný prostředek do skupiny prostředků, kterou předáte. Když vytváříte nový prostředek, budete muset znát "druh" služby, kterou chcete použít, spolu s její cenovou úrovní (nebo SKU) a umístěním Azure. Následující funkce přebírá všechny tyto argumenty a vytvoří prostředek.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Výběr služby a cenové úrovně

Při vytváření nového prostředku budete muset znát "druh" služby, kterou chcete použít, spolu s [cenovou úrovní](https://azure.microsoft.com/pricing/details/cognitive-services/) (nebo SKU), kterou chcete. Tuto a další informace použijete jako parametry při vytváření prostředku. Následující funkce Vypíše dostupné typy "druhy", které jsou k dispozici.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Zobrazení prostředků

Pokud chcete zobrazit všechny prostředky v rámci vašeho účtu Azure (napříč všemi skupinami prostředků), použijte tuto funkci:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list)]

## <a name="delete-a-resource"></a>Odstranění prostředku

Následující funkce odstraní zadaný prostředek z dané skupiny prostředků.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_delete)]

## <a name="run-the-application"></a>Spuštění aplikace

Přidejte následující kód do dolní části skriptu pro volání hlavní `quickstart` funkce s zpracováním chyb.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main)]

Pak v okně konzoly spusťte aplikaci pomocí `node` příkazu.

```console
node index.js
```

## <a name="see-also"></a>Viz také

* [Referenční dokumentace k sadě Azure Management SDK](/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest)
* [Co je Azure Cognitive Services?](../../what-are-cognitive-services.md)
* [Ověřování požadavků do Azure Cognitive Services](../../authentication.md)
* [Vytvoření nového prostředku pomocí webu Azure Portal](../../cognitive-services-apis-create-account.md)