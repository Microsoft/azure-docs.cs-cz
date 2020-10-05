---
title: 'Rychlý Start: Počítačové zpracování obrazu klientskou knihovnu pro Python'
description: Začněte s tímto rychlým startem pomocí klientské knihovny Počítačové zpracování obrazu pro Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 744ef58b54dc3718c4693ce2aad69024e2d6e2fe
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89321834"
---
<a name="HOLTop"></a>

[Referenční dokumentace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-computervision)  |  [Balíček (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-computervision/)  |  [Ukázky](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* [Python 3.x](https://www.python.org/)
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" vytvořte prostředek počítačové zpracování obrazu vytvoření prostředku "  target="_blank"> Počítačové zpracování obrazu <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k Počítačové zpracování obrazu službě budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.
* [Vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro klíč a adresu URL koncového bodu s názvem `COMPUTER_VISION_SUBSCRIPTION_KEY` a v `COMPUTER_VISION_ENDPOINT` uvedeném pořadí.

> [!NOTE]
> Můžete si stáhnout [celý zdrojový kód pro](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py) níže uvedené ukázky a příklady každé funkce dostupné z [ComputerVisionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python).

## <a name="setting-up"></a>Nastavení
 
### <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Vytvořte nový skript Pythonu &mdash; *Quickstart-File.py*, například. Pak ho otevřete v preferovaném editoru nebo integrovaném vývojovém prostředí a importujte následující knihovny.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports)]

Pak vytvořte proměnné pro koncový bod a klíč Azure prostředku.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_vars)]

> [!NOTE]
> Pokud jste po spuštění aplikace vytvořili proměnnou prostředí, budete muset zavřít a znovu otevřít Editor, rozhraní IDE nebo prostředí, na kterém je spuštěný, abyste měli přístup k této proměnné.

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Klientskou knihovnu můžete nainstalovat pomocí nástroje:

```console
pip install --upgrade azure-cognitiveservices-vision-computervision
```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní se řídí některými hlavními funkcemi sady Počítačové zpracování obrazu Python SDK.

|Název|Popis|
|---|---|
|[ComputerVisionClientOperationsMixin](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin?view=azure-python)| Tato třída přímo zpracovává všechny operace obrázků, jako je analýza obrázků, detekce textu a generování miniatur.|
| [ComputerVisionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) | Tato třída je potřebná pro všechny funkce Počítačové zpracování obrazu. Vytvoří se jeho instance s informacemi o předplatném a Vy ho použijete k vytvoření instancí jiných tříd. Implementuje **ComputerVisionClientOperationsMixin**.|
|[VisualFeatureTypes](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python)| Tento výčet definuje různé typy analýz obrázků, které lze provést v rámci standardní operace analýzy. V závislosti na vašich potřebách můžete zadat sadu hodnot **VisualFeatureTypes** . |

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí Počítačové zpracování obrazu klientské knihovny pro Python:

* [Ověření klienta](#authenticate-the-client)
* [Analýza obrázku](#analyze-an-image)
* [Číst vytištěné a ručně psaný text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE]
> V tomto rychlém startu se předpokládá, že jste [vytvořili proměnnou prostředí](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) pro váš počítačové zpracování obrazu klíč s názvem `COMPUTER_VISION_SUBSCRIPTION_KEY` .

Vytvořte instanci klienta s vaším koncovým bodem a klíčem. Vytvořte objekt [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) s klíčem a použijte ho u svého koncového bodu k vytvoření objektu [ComputerVisionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

## <a name="analyze-an-image"></a>Analýza obrázku

Uložte odkaz na adresu URL obrázku, který chcete analyzovat.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_remoteimage)]

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

Počítačové zpracování obrazu může použít specializovaný model k další analýze imagí. Další podrobnosti najdete v tématu [obsah specifický pro doménu](../../concept-detecting-domain-content.md) . 

Následující kód analyzuje data o zjištěných celebrit v imagi.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_celebs)]

Následující kód analyzuje data o zjištěných orientačních seznamech v obrázku.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Získat typ obrázku

Následující kód Vytiskne informace o typu obrázku &mdash; , ať už se jedná o Klipart nebo kreslení čáry.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Číst vytištěné a ručně psaný text

Počítačové zpracování obrazu může číst zobrazený text v obrázku a převést jej na datový proud znaků. Provedete to ve dvou částech.

### <a name="call-the-read-api"></a>Volání rozhraní API pro čtení

Nejprve použijte následující kód pro volání metody **Read** pro daný obrázek. Tato operace vrátí ID operace a spustí asynchronní proces pro čtení obsahu obrázku.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_call)]

### <a name="get-read-results"></a>Získat výsledky čtení

Potom Získejte ID operace vrácené voláním metody **Read** a použijte ji k dotazování služby na výsledky operace. Následující kód kontroluje operaci v intervalu 1 sekund, dokud nebudou vráceny výsledky. Pak zobrazí extrahovaná textová data do konzoly.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_response)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí `python` příkazu v souboru rychlého startu.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)


## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak používat knihovnu Počítačové zpracování obrazu pro Python k provádění základních úloh. Dále si Prozkoumejte referenční dokumentaci, kde najdete další informace o knihovně.


> [!div class="nextstepaction"]
>[Odkaz na rozhraní API pro počítačové zpracování obrazu (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)

* [Co je počítačové zpracování obrazu?](../../overview.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py).
