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
ms.openlocfilehash: 743b05b38eddc80ce7462a3439613fc767d91daa
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607779"
---
[Referenční dokumentace](https://docs.microsoft.com/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices?view=azure-python)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices)  |  [Balíček (PyPi)](https://pypi.org/project/azure-mgmt-cognitiveservices/)  |  [Ukázky](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices/tests)

## <a name="prerequisites"></a>Předpoklady

* Platné předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/).
* [Python 3.x](https://www.python.org/)

## <a name="create-an-azure-service-principal"></a>Vytvoření instančního objektu Azure

Pokud chcete, aby vaše aplikace pracovala s vaším účtem Azure, potřebujete instanční objekt Azure pro správu oprávnění. Postupujte podle pokynů v tématu [Vytvoření instančního objektu Azure](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0).

Když vytvoříte instanční objekt, uvidíte, že má tajnou hodnotu, ID a ID aplikace. Uložte ID aplikace a tajný kód do dočasného umístění pro pozdější kroky.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Než vytvoříte prostředek Cognitive Services, musí mít váš účet skupinu prostředků Azure, která má prostředek obsahovat. Pokud ještě nemáte skupinu prostředků, vytvořte ji v [Azure Portal](https://ms.portal.azure.com/).

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

## <a name="create-a-cognitive-services-resource"></a>Vytvoření prostředku služeb Cognitive Services

### <a name="choose-a-service-and-pricing-tier"></a>Výběr služby a cenové úrovně

Při vytváření nového prostředku budete muset znát "druh" služby, kterou chcete použít, spolu s [cenovou úrovní](https://azure.microsoft.com/pricing/details/cognitive-services/) (nebo SKU), kterou chcete. Tuto a další informace použijete jako parametry při vytváření prostředku. Následující funkce Vypíše dostupné typy "druhy", které jsou k dispozici.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

Podívejte se na seznam SKU a informace o cenách níže. 

#### <a name="multi-service"></a>Více služeb

| Služba                    | Druh                      |
|----------------------------|---------------------------|
| Několik služeb. Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/cognitive-services/) .            | `CognitiveServices`     |


#### <a name="vision"></a>Obraz

| Služba                    | Druh                      |
|----------------------------|---------------------------|
| Počítačové zpracování obrazu            | `ComputerVision`          |
| Custom Vision – předpověď | `CustomVision.Prediction` |
| Custom Vision – školení   | `CustomVision.Training`   |
| Tvář                       | `Face`                    |
| Rozpoznávání formulářů            | `FormRecognizer`          |
| Rozpoznávání rukopisu             | `InkRecognizer`           |

#### <a name="search"></a>Hledat

| Služba            | Druh                  |
|--------------------|-----------------------|
| Automatické návrhy Bingu   | `Bing.Autosuggest.v7` |
| Vlastní vyhledávání Bingu | `Bing.CustomSearch`   |
| Vyhledávání entit Bingu | `Bing.EntitySearch`   |
| Vyhledávání Bingu        | `Bing.Search.v7`      |
| Kontrola pravopisu Bingu   | `Bing.SpellCheck.v7`  |

#### <a name="speech"></a>Řeč

| Služba            | Druh                 |
|--------------------|----------------------|
| Hlasové služby    | `SpeechServices`     |
| Rozpoznávání řeči | `SpeakerRecognition` |

#### <a name="language"></a>Jazyk

| Služba            | Druh                |
|--------------------|---------------------|
| Porozumění formuláři | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Analýza textu     | `TextAnalytics`     |
| Překlad textu   | `TextTranslation`   |

#### <a name="decision"></a>Rozhodnutí

| Služba           | Druh               |
|-------------------|--------------------|
| Detektor anomálií  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizace      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>Cenové úrovně a fakturace

Cenové úrovně (a množství, které se vám bude účtovat), vycházejí z počtu transakcí, které odešlete pomocí ověřovacích informací. Každá cenová úroveň určuje:
* maximální počet povolených transakcí za sekundu (TPS).
* funkce služby povolené v rámci cenové úrovně.
* náklady na předdefinovaný počet transakcí. Po překročení tohoto čísla dojde k zadání dalších poplatků uvedených v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) vaší služby.

> [!NOTE]
> Mnohé z Cognitive Services mají bezplatnou úroveň, kterou můžete použít k vyzkoušení služby. Pokud chcete používat úroveň Free, použijte `F0` jako SKU pro váš prostředek.

## <a name="create-a-cognitive-services-resource"></a>Vytvoření prostředku služeb Cognitive Services

Pokud chcete vytvořit nový prostředek Cognitive Services a přihlásit se k jeho odběru, použijte funkci **Create** . Tato funkce přidá nový fakturovatelný prostředek do skupiny prostředků, kterou předáte. Když vytváříte nový prostředek, budete muset znát "druh" služby, kterou chcete použít, spolu s její cenovou úrovní (nebo SKU) a umístěním Azure. Následující funkce přebírá všechny tyto argumenty a vytvoří prostředek.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_create)]

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

## <a name="see-also"></a>Viz také:

* [Referenční dokumentace k sadě Azure Management SDK](https://docs.microsoft.com/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices?view=azure-python)
* [Co je Azure Cognitive Services?](../../Welcome.md)
* [Ověřování požadavků do Azure Cognitive Services](../../authentication.md)
* [Vytvoření nového prostředku pomocí webu Azure Portal](../../cognitive-services-apis-create-account.md)