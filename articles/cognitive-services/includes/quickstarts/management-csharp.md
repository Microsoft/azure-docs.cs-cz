---
title: 'Rychlý Start: Klientská knihovna pro správu Azure pro .NET'
description: V tomto rychlém startu Začínáme s klientskou knihovnou pro správu Azure pro .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 93ea0ffc94b78e014b30ab1d45d589eba50fe524
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607776"
---
[Referenční dokumentace](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/management?view=azure-dotnet)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices)  |  [Balíček (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.CognitiveServices/)  |  [Ukázky](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices/tests)

## <a name="prerequisites"></a>Předpoklady

* Platné předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/).
* Aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="create-an-azure-service-principal"></a>Vytvoření instančního objektu Azure

Pokud chcete, aby vaše aplikace pracovala s vaším účtem Azure, potřebujete instanční objekt Azure pro správu oprávnění. Postupujte podle pokynů v tématu [Vytvoření instančního objektu Azure](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0).

Když vytvoříte instanční objekt, uvidíte, že má tajnou hodnotu, ID a ID aplikace. Uložte ID aplikace a tajný kód do dočasného umístění pro pozdější kroky.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Než vytvoříte prostředek Cognitive Services, musí mít váš účet skupinu prostředků Azure, která má prostředek obsahovat. Pokud ještě nemáte skupinu prostředků, vytvořte ji [Azure Portal](https://ms.portal.azure.com/) než budete pokračovat.

## <a name="create-a-new-c-application"></a>Vytvoření nové aplikace v C#

Vytvořte novou aplikaci .NET Core. V okně konzoly (například cmd, PowerShell nebo bash) použijte `dotnet new` příkaz k vytvoření nové aplikace konzoly s názvem `azure-management-quickstart` . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: *program.cs*. 

```console
dotnet new console -n azure-management-quickstart
```

Změňte adresář na nově vytvořenou složku aplikace. Aplikaci můžete vytvořit pomocí:

```console
dotnet build
```

Výstup sestavení by neměl obsahovat žádná upozornění ani chyby. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Instalace klientské knihovny

V adresáři aplikace nainstalujte klientskou knihovnu pro správu Azure pro .NET pomocí následujícího příkazu:

```console
dotnet add package Microsoft.Azure.Management.CognitiveServices
dotnet add package Microsoft.Azure.Management.Fluent
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

Pokud používáte integrované vývojové prostředí (IDE) sady Visual Studio, je knihovna klienta k dispozici jako balíček NuGet ke stažení.

### <a name="import-libraries"></a>Import knihoven

Otevřete *program.cs* a na `using` začátek souboru přidejte následující příkazy:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Ověření klienta

Do kořenového adresáře *program.cs* přidejte následující pole a naplňte jejich hodnoty pomocí instančního objektu, který jste vytvořili, a informací o účtu Azure.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_constants)]

Potom v metodě **Main** pomocí těchto hodnot Sestavte objekt **CognitiveServicesManagementClient** . Tento objekt je potřebný pro všechny vaše operace správy Azure.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_assigns)]

## <a name="call-management-methods"></a>Metody správy volání

Přidejte následující kód do metody **Main** pro vypsání dostupných prostředků, vytvořte ukázkový prostředek, uveďte seznam vlastněných prostředků a pak odstraňte ukázkový prostředek. Tyto metody definujete v dalších krocích.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource"></a>Vytvoření prostředku služeb Cognitive Services

### <a name="choose-a-service-and-pricing-tier"></a>Výběr služby a cenové úrovně

Při vytváření nového prostředku budete muset znát "druh" služby, kterou chcete použít, spolu s [cenovou úrovní](https://azure.microsoft.com/pricing/details/cognitive-services/) (nebo SKU), kterou chcete. Tuto a další informace použijete jako parametry při vytváření prostředku. Seznam dostupného druhu služby pro rozpoznávání získáte tak, že ve svém skriptu zavoláte následující metodu:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list_avail)]

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

Pokud chcete vytvořit nový prostředek Cognitive Services a přihlásit se k jeho odběru, použijte metodu **Create** . Tato metoda přidá nový fakturovatelný prostředek do skupiny prostředků, kterou předáte. Při vytváření nového prostředku budete muset znát "druh" služby, kterou chcete použít, spolu s její cenovou úrovní (nebo SKU) a umístěním Azure. Následující metoda přebírá všechny tyto argumenty jako argumenty a vytvoří prostředek.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_create)]

## <a name="view-your-resources"></a>Zobrazení prostředků

Pokud chcete zobrazit všechny prostředky v rámci vašeho účtu Azure (napříč všemi skupinami prostředků), použijte tuto metodu:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list)]

## <a name="delete-a-resource"></a>Odstranění prostředku

Následující metoda odstraní zadaný prostředek z dané skupiny prostředků.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_delete)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci z adresáře aplikace pomocí `dotnet run` příkazu.

```dotnet
dotnet run
```

## <a name="see-also"></a>Viz také:

* [Referenční dokumentace k sadě Azure Management SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/management?view=azure-dotnet)
* [Co je Azure Cognitive Services?](../../Welcome.md)
* [Ověřování požadavků do Azure Cognitive Services](../../authentication.md)
* [Vytvoření nového prostředku pomocí webu Azure Portal](../../cognitive-services-apis-create-account.md)