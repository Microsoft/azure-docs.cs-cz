---
title: 'Úvodní příručka: Klientská knihovna počítačového vidění pro Python'
description: Začněte s klientskou knihovnou Počítačové vidění pro Python s tímto rychlým startem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 5f552dfc320459d23e538e65e5f34c085f5b5ad9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80136004"
---
<a name="HOLTop"></a>

[Referenční dokumentace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision) | [Ukázky](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0) [zdrojového kódu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-computervision) | [knihovny (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-computervision/) | 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Nastavení

### <a name="create-a-computer-vision-azure-resource"></a>Vytvoření prostředku Azure pro počítačové zpracování obrazu

Azure Cognitive Services jsou reprezentované prostředky Azure, které si předplatíte. Vytvořte prostředek pro počítačové zpracování pomocí [portálu Azure nebo](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na místním počítači. Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services/#decision) platný po dobu sedmi dnů zdarma. Po registraci bude dostupná na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Zobrazení prostředků na [webu Azure Portal](https://portal.azure.com/)

Po získání klíče z zkušebního předplatného nebo prostředku [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT`adresu URL klíče a koncového bodu s názvem a , resp.
 
### <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Vytvořte například&mdash;nový skript Pythonu*quickstart-file.py*. Pak jej otevřete v upřednostňovaném editoru nebo rozhraní IDE a importujte následující knihovny.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports)]

Potom vytvořte proměnné pro koncový bod azure a klíč vašeho prostředku.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_vars)]

> [!NOTE]
> Pokud jste vytvořili proměnnou prostředí po spuštění aplikace, budete muset zavřít a znovu otevřít editor, IDE nebo prostředí, které ji spustilo, abyste měli přístup k proměnné.

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Klientskou knihovnu můžete nainstalovat pomocí:

```console
pip install --upgrade azure-cognitiveservices-vision-computervision
```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce sady Computer Vision Python SDK.

|Name (Název)|Popis|
|---|---|
|[ComputerVisionClientOperationsMixin](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin?view=azure-python)| Tato třída přímo zpracovává všechny operace obrazu, jako je například analýza obrazu, detekce textu a generování miniatur.|
| [Klient ComputerVision](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) | Tato třída je potřebná pro všechny funkce počítačového vidění. Můžete vytvořit konkretizovat s informacemi o předplatném a použít k vytvoření instance jiných tříd. Implementuje **ComputerVisionClientOperationsMixin**.|
|[VisualFeatureTypes](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python)| Tento výčet definuje různé typy analýzy obrázků, které lze provést ve standardní operaci Analyzovat. V závislosti na vašich potřebách zadáte sadu hodnot **VisualFeatureTypes.** |

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující úkoly s klientskou knihovnou Počítačové vidění pro Python:

* [Ověření klienta](#authenticate-the-client)
* [Analýza obrázku](#analyze-an-image)
* [Čtení tištěného a ručně psaného textu](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE]
> Tento rychlý start předpokládá, že jste [vytvořili proměnnou](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) `COMPUTER_VISION_SUBSCRIPTION_KEY`prostředí pro klíč počítačového vidění s názvem .

Vytvořte instanci klienta pomocí koncového bodu a klíče. Vytvořte objekt [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) s klíčem a použijte jej s koncovým bodem k vytvoření objektu [ComputerVisionClient.](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

## <a name="analyze-an-image"></a>Analýza obrázku

Uložte odkaz na adresu URL obrázku, který chcete analyzovat.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_remoteimage)]

### <a name="get-image-description"></a>Získat popis obrázku

Následující kód získá seznam generovaných titulků pro obrázek. Další podrobnosti [najdete v tématu Popis obrázků.](../../concept-describing-images.md)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_describe)]

### <a name="get-image-category"></a>Získat kategorii obrázků

Následující kód získá zjištěnou kategorii obrázku. Další [podrobnosti najdete v tématu Kategorizace obrázků.](../../concept-categorizing-images.md)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_categorize)]

### <a name="get-image-tags"></a>Získat značky obrázků

Následující kód získá sadu zjištěných značek v obraze. Další podrobnosti najdete v [tématu Značky obsahu.](../../concept-tagging-images.md)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_tags)]

### <a name="detect-objects"></a>Detekce objektů

Následující kód detekuje běžné objekty v obraze a vytiskne je do konzoly. Další [podrobnosti najdete v tématu Detekce objektů.](../../concept-object-detection.md)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_objects)]

### <a name="detect-brands"></a>Detekce značek

Následující kód detekuje podnikové značky a loga v obrázku a vytiskne je do konzoly. Další [podrobnosti najdete v tématu Detekce značek.](../../concept-brand-detection.md)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_brands)]

### <a name="detect-faces"></a>Rozpoznávání tváří

Následující kód vrátí zjištěné plochy v obraze s jejich obdélníkovými souřadnicemi a vybranými atributy plochy. Další [podrobnosti najdete v tématu Detekce obličejů.](../../concept-detecting-faces.md)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detekujte obsah pro dospělé, pikantní nebo krvavý

Následující kód vytiskne zjištěnou přítomnost obsahu pouze pro dospělé v obrázku. Další podrobnosti najdete [v tématu Dospělý, pikantní, krvavý obsah.](../../concept-detecting-adult-content.md)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Získat barevné schéma obrázku

Následující kód vytiskne zjištěné barevné atributy v obraze, například dominantní barvy a barvy zvýraznění. Další podrobnosti najdete [v části Barevná schémata.](../../concept-detecting-color-schemes.md)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Získání obsahu specifického pro doménu

Počítačové vidění lze použít specializovaný model k další analýze obrázků. Další podrobnosti najdete [v tématu Obsah specifický pro doménu.](../../concept-detecting-domain-content.md) 

Následující kód analyzuje data o zjištěných celebrit v obraze.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_celebs)]

Následující kód analyzuje data o zjištěných orientačních bodů v obraze.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Získání typu obrázku

Následující kód vytiskne informace&mdash;o typu obrázku, ať už se jedná o kresbu klipartem nebo čarou.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Čtení tištěného a ručně psaného textu

Počítačové vidění může číst viditelný text v obraze a převést jej na datový proud znaků. Uděláš to ve dvou částech.

### <a name="call-the-read-api"></a>Volání rozhraní READ API

Nejprve použijte následující kód k volání **metody batch_read_file** pro daný obrázek. To vrátí ID operace a spustí asynchronní proces číst obsah obrazu.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_call)]

### <a name="get-read-results"></a>Získat výsledky čtení

Dále získejte ID operace vrácené z **batch_read_file** volání a použijte jej k dotazování služby na výsledky operace. Následující kód kontroluje operaci v intervalech jedné sekundy, dokud nejsou vráceny výsledky. Potom vytiskne extrahovaná textová data do konzoly.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_response)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí příkazu `python` v souboru quickstart.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků také odstraníte všechny další prostředky, které jsou s ní spojené.

* [Portál](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)


## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili používat knihovnu počítačového vidění pro Python k úkolům základů. Dále prozkoumejte referenční dokumentaci, abyste se dozvěděli více o knihovně.


> [!div class="nextstepaction"]
>[Reference api pro počítačové zpracování obrazu (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)

* [Co je počítačové zpracování obrazu?](../../Home.md)
* Zdrojový kód pro tuto ukázku lze nalézt na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py).
