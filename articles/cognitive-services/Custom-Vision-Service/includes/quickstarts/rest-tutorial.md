---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 12/09/2020
ms.topic: include
ms.openlocfilehash: 8890a0132e5f510a0af2862c483206fd025a68d8
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2021
ms.locfileid: "98256495"
---
Začněte s REST APIem Custom Vision. Postupujte podle těchto kroků a zavolejte rozhraní API a sestavte model klasifikace imagí. Vytvoříte projekt, přidáte značky, provedete projekt a použijete adresu URL koncového bodu předpovědi projektu pro programové testování. Tento příklad použijte jako šablonu pro vytvoření vlastní aplikace pro rozpoznávání imagí.

> [!NOTE]
> Custom Vision se dá snadno použít prostřednictvím sady Client Library SDK nebo prostřednictvím [pokynů v prohlížeči](../../get-started-build-detector.md).

Pomocí klientské knihovny Custom Vision pro .NET:

* Vytvoření nového projektu Custom Vision
* Přidat do projektu značky
* Nahrávání a označování obrázků
* Výuka projektu
* Publikovat aktuální iteraci
* Testování koncového bodu předpovědi

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title=" vytvořte prostředek Custom Vision vytvoření prostředku "  target="_blank"> Custom Vision <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste vytvořili prostředek pro školení a předpověď a získali své klíče a koncový bod. Počkejte na nasazení a klikněte na tlačítko **Přejít k prostředku** .
    * K připojení aplikace k Custom Vision budete potřebovat klíč a koncový bod z prostředků, které vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.


## <a name="create-a-new-custom-vision-project"></a>Vytvoření nového projektu Custom Vision

Pro vytvoření projektu klasifikace obrázků použijete příkaz podobný následujícímu. Vytvořený projekt se zobrazí na [webu Custom Vision](https://customvision.ai/).


:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createproject":::

Zkopírujte příkaz do textového editoru a proveďte následující změny:

* Nahraďte `{subscription key}` platným klíčem pro odběr obličeje.
* Nahraďte `{endpoint}` koncovým bodem, který odpovídá vašemu klíči předplatného.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
* Nahraďte `{name}` názvem vašeho projektu.
* Volitelně můžete nastavit další parametry adresy URL a nakonfigurovat tak typ modelu, který bude projekt používat. Možnosti najdete v tématu [rozhraní API pro CreatProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae) .

Dostanete odpověď JSON podobnou následující. Uložte `"id"` hodnotu projektu do dočasného umístění.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="add-tags-to-the-project"></a>Přidat do projektu značky

Pomocí následujícího příkazu definujte značky, na kterých budete model zaškolovat.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createtag":::

* Znovu vložte svůj vlastní klíč a adresu URL koncového bodu.
* Nahraďte `{projectId}` svým vlastním ID projektu.
* Nahraďte `{name}` názvem značky, kterou chcete použít.

Tento postup opakujte pro všechny značky, které chcete použít ve vašem projektu. Pokud používáte příklady poskytnutých imagí, přidejte značky `"Hemlock"` a `"Japanese Cherry"` .

Dostanete odpověď JSON jako následující. Uložte `"id"` hodnotu každé značky do dočasného umístění.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "type": "Regular",
  "imageCount": 0
}
```

## <a name="upload-and-tag-images"></a>Nahrávání a označování obrázků

Dále si stáhněte ukázkové image pro tento projekt. Uložte obsah [složky Sample images](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) do místního zařízení.

> [!NOTE]
> Studnicí, projekt garáže společnosti Microsoft, umožňuje shromažďovat a kupovat sady imagí pro účely školení. Po shromáždění imagí si je můžete stáhnout a pak je importovat do projektu Custom Vision obvyklým způsobem. Další informace najdete na [stránce studnicí](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3) .

Pomocí následujícího příkazu nahrajte obrázky a použijte značky. jednou pro image "Hemlock" a samostatně pro image z japonského výběru. Další možnosti najdete v tématu [Vytvoření imagí z](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb5) rozhraní API pro data.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="uploadimages":::

* Znovu vložte svůj vlastní klíč a adresu URL koncového bodu.
* Nahraďte `{projectId}` svým vlastním ID projektu.
* Nahraďte `{tagArray}` ID značky.
* Pak naplňte text požadavku do binárních dat imagí, které chcete označit.

## <a name="train-the-project"></a>Výuka projektu

Tato metoda nahraje model u tagovaných imagí, které jste nahráli, a vrátí ID pro aktuální iteraci projektu.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="trainproject":::

* Znovu vložte svůj vlastní klíč a adresu URL koncového bodu.
* Nahraďte `{projectId}` svým vlastním ID projektu.
* Nahraďte `{tagArray}` ID značky.
* Pak naplňte text požadavku do binárních dat imagí, které chcete označit.
* Volitelně můžete použít jiné parametry adresy URL. Možnosti najdete v tématu věnovaném rozhraní API pro [výukové projekty](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) .

> [!TIP]
> Výuka s vybranými značkami
>
> Volitelně můžete naučit pouze podmnožinu použitých značek. Můžete to chtít udělat, pokud jste ještě nepoužili dostatek určitých značek, ale máte dost dalších. Do těla vaší žádosti přidejte volitelný obsah JSON. Naplňte `"selectedTags"` pole identifikátory značek, které chcete použít.
> ```json
> {
>   "selectedTags": [
>     "00000000-0000-0000-0000-000000000000"
>   ]
> }
> ```

Odpověď JSON obsahuje informace o vámi vyškolený projekt, včetně ID iterace ( `"id"` ). Tuto hodnotu uložte pro další krok.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "status": "string",
  "created": "string",
  "lastModified": "string",
  "trainedAt": "string",
  "projectId": "00000000-0000-0000-0000-000000000000",
  "exportable": true,
  "exportableTo": [
    "CoreML"
  ],
  "domainId": "00000000-0000-0000-0000-000000000000",
  "classificationType": "Multiclass",
  "trainingType": "Regular",
  "reservedBudgetInHours": 0,
  "trainingTimeInMinutes": 0,
  "publishName": "string",
  "originalPublishResourceId": "string"
}
```

## <a name="publish-the-current-iteration"></a>Publikovat aktuální iteraci

Tato metoda zpřístupňuje aktuální iteraci modelu, který je k dispozici pro dotazování. Název vráceného modelu použijete jako referenci k odeslání požadavků předpovědi. 

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* Znovu vložte svůj vlastní klíč a adresu URL koncového bodu.
* Nahraďte `{projectId}` svým vlastním ID projektu.
* Nahraďte `{iterationId}` identifikátorem vráceným v předchozím kroku.
* Nahraďte `{publishedName}` názvem, který chcete přidružit k modelu předpovědi.
* Nahraďte `{predictionId}` vlastním ID prostředku předpovědi. Můžete ji najít na kartě **Přehled** prostředku předpovědi na Azure Portal, která je uvedená jako **ID předplatného**.
* Volitelně můžete použít jiné parametry adresy URL. Viz téma [Publish iterace](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fdded5) API.

## <a name="test-the-prediction-endpoint"></a>Testování koncového bodu předpovědi

Nakonec pomocí tohoto příkazu otestujte vyškolený model tak, že nahrajete nový obrázek pro klasifikaci pomocí značek. Obrázek můžete použít ve složce test ukázkových souborů, které jste stáhli dříve.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* Znovu vložte svůj vlastní klíč a adresu URL koncového bodu.
* Nahraďte `{projectId}` svým vlastním ID projektu.
* Nahraďte `{publishedName}` názvem, který jste použili v předchozím kroku.
* Do textu žádosti přidejte binární data místní image.
* Volitelně můžete použít jiné parametry adresy URL. Podívejte se na téma rozhraní API pro [klasifikaci imagí](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3) .

Vrácená odpověď JSON bude mít aspoň každou značku, kterou model pro vaši image nastavil, spolu s skóre pravděpodobnosti pro každou značku. 

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "project": "00000000-0000-0000-0000-000000000000",
  "iteration": "00000000-0000-0000-0000-000000000000",
  "created": "string",
  "predictions": [
    {
      "probability": 0.0,
      "tagId": "00000000-0000-0000-0000-000000000000",
      "tagName": "string",
      "boundingBox": {
        "left": 0.0,
        "top": 0.0,
        "width": 0.0,
        "height": 0.0
      },
      "tagType": "Regular"
    }
  ]
}
```

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Další kroky

Nyní jste provedli všechny kroky procesu klasifikace obrázků pomocí REST API. Tato ukázka provádí jednu výukovou iteraci, ale často je potřeba, abyste model provedli a otestovali několikrát, aby bylo přesnější.

> [!div class="nextstepaction"]
> [Testování a přetrénování modelu](../../test-your-model.md)

* [Co je Custom Vision?](../../overview.md)
* [Referenční dokumentace rozhraní API (školení)](/dotnet/api/overview/azure/cognitiveservices/client/customvision?view=azure-dotnet)
* [Referenční dokumentace rozhraní API (předpověď)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae)
