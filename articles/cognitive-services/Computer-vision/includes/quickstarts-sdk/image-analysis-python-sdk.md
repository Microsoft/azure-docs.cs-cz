---
title: 'Rychlý Start: Klientská knihovna pro analýzu obrázků pro Python'
description: Začněte s tímto rychlým startem pomocí klientské knihovny pro analýzu imagí pro Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: aae780ad42c39789c0c4448a90a12737074e4176
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073526"
---
<a name="HOLTop"></a>

Pomocí klientské knihovny pro analýzu obrázků můžete analyzovat obrázek pro značky, text popisu, obličeje, obsah pro dospělé a další.

[Referenční dokumentace](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-computervision)  |  [Balíček (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-computervision/)  |  [Ukázky](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* [Python 3.x](https://www.python.org/)
  * Instalace Pythonu by měla zahrnovat [PIP](https://pip.pypa.io/en/stable/). Pomocí příkazu na příkazovém řádku můžete zjistit, jestli máte službu PIP nainstalovanou `pip --version` . Získejte PIP instalací nejnovější verze Pythonu.
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" vytvořte prostředek počítačové zpracování obrazu vytvoření prostředku "  target="_blank"> Počítačové zpracování obrazu </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.

    * K připojení aplikace k Počítačové zpracování obrazu službě budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Klientskou knihovnu můžete nainstalovat pomocí nástroje:

```console
pip install --upgrade azure-cognitiveservices-vision-computervision
```

Nainstalujte také knihovnu Pillow.

```console
pip install pillow
```

### <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Vytvořte nový soubor Python &mdash; *Quickstart-File.py*, například. Pak ho otevřete v preferovaném editoru nebo integrovaném vývojovém prostředí a importujte následující knihovny.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports)]

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py), který obsahuje příklady kódu v tomto rychlém startu.

Pak vytvořte proměnné pro koncový bod a klíč Azure prostředku.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_vars)]

> [!IMPORTANT]
> Přejděte na Azure Portal. Pokud se prostředek Počítačové zpracování obrazu, který jste vytvořili v části **předpoklady** , se úspěšně nasadil, klikněte v části **Další kroky** na tlačítko **Přejít k prostředku** . Klíč a koncový bod můžete najít na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**. 
>
> Nezapomeňte odebrat klíč z kódu, až budete hotovi, a nikdy ho zveřejnit. V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Například [Trezor klíčů Azure](../../../../key-vault/general/overview.md).

> [!div class="nextstepaction"]
> [Jsem u klienta nastavil](?success=set-up-client#object-model) [problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=set-up-client)

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce sady SDK pro sadu imagí pro analýzu obrázků.

|Název|Description|
|---|---|
|[ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin)| Tato třída přímo zpracovává všechny operace obrázků, jako je analýza obrázků, detekce textu a generování miniatur.|
| [ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient) | Tato třída je potřebná pro všechny funkce Počítačové zpracování obrazu. Vytvoří se jeho instance s informacemi o předplatném a Vy ho použijete k vytvoření instancí jiných tříd. Implementuje **ComputerVisionClientOperationsMixin**.|
|[VisualFeatureTypes](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| Tento výčet definuje různé typy analýz obrázků, které lze provést v rámci standardní operace analýzy. V závislosti na vašich potřebách můžete zadat sadu hodnot **VisualFeatureTypes** . |

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny pro analýzu imagí pro Python:

* [Ověření klienta](#authenticate-the-client)
* [Analýza obrázku](#analyze-an-image)

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte instanci klienta s vaším koncovým bodem a klíčem. Vytvořte objekt [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) s klíčem a použijte ho u svého koncového bodu k vytvoření objektu [ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

> [!div class="nextstepaction"]
> Jsem ověřil (a) jsem [, že klient](?success=authenticate-client#analyze-an-image) [narazil na problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=authenticate-client)

## <a name="analyze-an-image"></a>Analýza obrázku

K analýze vizuálních funkcí vzdálené image použijte objekt klienta. Nejdřív uložte odkaz na adresu URL obrázku, který chcete analyzovat.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_remoteimage)]

> [!TIP]
> Můžete také analyzovat místní bitovou kopii. Podívejte se na metody [ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin) , jako je například **analyze_image_in_stream**. Nebo si přečtěte ukázkový kód na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py) , kde najdete scénáře týkající se místních imagí.

### <a name="get-image-description"></a>Získat popis obrázku

Následující kód získá seznam generovaných titulků pro obrázek. Další podrobnosti najdete v tématu [popisujícím obrázky](../../concept-describing-images.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_describe)]

### <a name="get-image-category"></a>Získat kategorii obrázku

Následující kód získá zjištěnou kategorii obrázku. Další podrobnosti najdete v tématu [kategorizace imagí](../../concept-categorizing-images.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_categorize)]

### <a name="get-image-tags"></a>Získat značky obrázku

Následující kód získá sadu zjištěných značek v obrázku. Další podrobnosti najdete v tématu [značky obsahu](../../concept-tagging-images.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_tags)]

### <a name="detect-objects"></a>Detekovat objekty

Následující kód detekuje běžné objekty v imagi a vytiskne je do konzoly. Další podrobnosti najdete v tématu věnovaném [detekci objektů](../../concept-object-detection.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_objects)]        

### <a name="detect-brands"></a>Detekovat značky

Následující kód detekuje firemní značky a loga v imagi a vytiskne je do konzoly. Další podrobnosti najdete v tématu [rozpoznávání značek](../../concept-brand-detection.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_brands)]

### <a name="detect-faces"></a>Rozpoznávání tváří

Následující kód vrátí zjištěné plošky v obrázku s jejich souřadnicemi obdélníku a vyberte možnost atributy obličeje. Další podrobnosti najdete v tématu [rozpoznávání tváře](../../concept-detecting-faces.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Zjištění obsahu pro dospělé, pikantní nebo gorie

Následující kód vytiskne zjištěnou přítomnost obsahu pro dospělé v imagi. Další podrobnosti najdete v článku [obsah pro dospělé, pikantní a gorie](../../concept-detecting-adult-content.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Získat barevné schéma obrázku

Následující kód vytiskne zjištěné atributy barev v obrázku, jako jsou dominantní barvy a Barva zvýraznění. Další podrobnosti najdete v tématu [Barevná schémata](../../concept-detecting-color-schemes.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Získání obsahu specifického pro doménu

Analýza obrázků může pomocí specializovaného modelu provádět další analýzu imagí. Další podrobnosti najdete v tématu [obsah specifický pro doménu](../../concept-detecting-domain-content.md) . 

Následující kód analyzuje data o zjištěných celebrit v imagi.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_celebs)]

Následující kód analyzuje data o zjištěných orientačních seznamech v obrázku.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Získat typ obrázku

Následující kód Vytiskne informace o typu obrázku &mdash; , ať už se jedná o Klipart nebo kreslení čáry.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_type)]

> [!div class="nextstepaction"]
> [Analyzoval jsem obrázek](?success=analyze-image#run-the-application) , který [jsem narazil na problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=analyze-image)



## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí `python` příkazu v souboru rychlého startu.

```console
python quickstart-file.py
```

> [!div class="nextstepaction"]
> [Spustil (a) jsem aplikaci,](?success=run-the-application#clean-up-resources) na kterou [mám problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=run-the-application)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Vyčistil](?success=clean-up-resources#next-steps) jsem problémy, které [jsem narazil na problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=clean-up-resources)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak nainstalovat knihovnu klienta pro analýzu imagí a provést základní volání analýzy obrázků. V dalším kroku se dozvíte víc o funkcích rozhraní API pro analýzu.

> [!div class="nextstepaction"]
>[Volání rozhraní API pro analýzu](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Přehled analýzy obrázků](../../overview-image-analysis.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py).
