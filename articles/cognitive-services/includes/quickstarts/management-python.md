---
title: 'Rychlý Start: Klientská knihovna pro správu Azure pro Python'
description: V tomto rychlém startu Začínáme s klientskou knihovnou pro správu Azure pro Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: fb908cdcf3e235654effc043de29e599a48179d4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879749"
---
[Referenční dokumentace](/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices)  |  [Balíček (PyPi)](https://pypi.org/project/azure-mgmt-cognitiveservices/)  |  [Ukázky](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices/tests)

## <a name="python-prerequisites"></a>Požadavky Pythonu

* Platné předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/).
* [Python 3.x](https://www.python.org/)

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Vytvořte novou aplikaci v Pythonu v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE) a přejděte do projektu v okně konzoly.

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Klientskou knihovnu můžete nainstalovat pomocí nástroje:

```console
pip install azure-mgmt-cognitiveservices
```

Pokud používáte integrované vývojové prostředí (IDE) sady Visual Studio, je knihovna klienta k dispozici jako balíček NuGet ke stažení.

### <a name="import-libraries"></a>Import knihoven

Otevřete skript Pythonu a importujte následující knihovny.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Ověření klienta

Do kořenového adresáře skriptu přidejte následující pole a vyplňte jejich hodnoty pomocí instančního objektu, který jste vytvořili, a informací o účtu Azure.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_constants)]

Pak přidejte následující kód pro vytvoření objektu **CognitiveServicesManagementClient** . Tento objekt je potřebný pro všechny vaše operace správy Azure.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_auth)]

## <a name="create-a-cognitive-services-resource-python"></a>Vytvoření prostředku Cognitive Services (Python)

Pokud chcete vytvořit nový prostředek Cognitive Services a přihlásit se k jeho odběru, použijte funkci **Create** . Tato funkce přidá nový fakturovatelný prostředek do skupiny prostředků, kterou předáte. Když vytváříte nový prostředek, budete muset znát "druh" služby, kterou chcete použít, spolu s její cenovou úrovní (nebo SKU) a umístěním Azure. Následující funkce přebírá všechny tyto argumenty a vytvoří prostředek.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Výběr služby a cenové úrovně

Při vytváření nového prostředku budete muset znát "druh" služby, kterou chcete použít, spolu s [cenovou úrovní](https://azure.microsoft.com/pricing/details/cognitive-services/) (nebo SKU), kterou chcete. Tuto a další informace použijete jako parametry při vytváření prostředku. Následující funkce Vypíše dostupné typy "druhy", které jsou k dispozici.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Zobrazení prostředků

Pokud chcete zobrazit všechny prostředky v rámci vašeho účtu Azure (napříč všemi skupinami prostředků), použijte tuto funkci:

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list)]

## <a name="delete-a-resource"></a>Odstranění prostředku

Následující funkce odstraní zadaný prostředek z dané skupiny prostředků.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_delete)]

## <a name="call-management-functions"></a>Funkce správy volání

Přidejte následující kód do dolní části skriptu pro volání výše uvedených funkcí. Tento kód Vypíše dostupné prostředky a vytvoří vzorový prostředek, zobrazí seznam vlastněných prostředků a pak odstraní ukázkový prostředek.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_calls)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci z příkazového řádku pomocí `python` příkazu.

```console
python <your-script-name>.py
```

## <a name="see-also"></a>Viz také

* Informace o tom, jak bezpečně pracovat s Cognitive Services, najdete v tématu **[ověření požadavků do Azure Cognitive Services](../../authentication.md)** .
* V tématu **[co je Azure Cognitive Services?](../../what-are-cognitive-services.md)** získáte seznam různých kategorií v rámci Cognitive Services.
* Seznam přirozených jazyků, které Cognitive Services podporuje, najdete v článku **[Podpora přirozeného jazyka](../../language-support.md)** .
* Pokud chcete pochopit, jak používat Cognitive Services on-Prem, přečtěte si téma **[použití Cognitive Services jako kontejnerů](../../cognitive-services-container-support.md)** .
* Odhad nákladů na používání Cognitive Services najdete v tématu **[plánování a Správa nákladů na Cognitive Services](../../plan-manage-costs.md)** .
* Další podrobnosti o sadě Management SDK najdete v **[referenční dokumentaci k sadě Azure Management SDK](/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices)** .
