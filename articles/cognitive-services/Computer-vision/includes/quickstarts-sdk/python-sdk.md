---
title: 'Rychlý Start: Klientská knihovna pro rozpoznávání optického znaku pro Python'
description: Začněte s tímto rychlým startem pomocí klientské knihovny pro rozpoznávání optického znaku pro Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 230934ea3d4a8a970ca1fca46160b96e3f2234e8
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073412"
---
<a name="HOLTop"></a>

Pomocí klientské knihovny pro rozpoznávání optických znaků si můžete přečíst vytištěné a ručně psaný text pomocí rozhraní API pro čtení.

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

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py), který obsahuje příklady kódu v tomto rychlém startu.

Vytvořte nový soubor Python &mdash; *Quickstart-File.py*, například. Pak ho otevřete v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE).

### <a name="find-the-subscription-key-and-endpoint"></a>Najít klíč předplatného a koncový bod

Přejděte na Azure Portal. Pokud se prostředek Počítačové zpracování obrazu, který jste vytvořili v části **předpoklady** , se úspěšně nasadil, klikněte v části **Další kroky** na tlačítko **Přejít k prostředku** . Klíč předplatného a koncový bod můžete najít na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**. 

Vytvořte proměnné pro klíč předplatného Počítačové zpracování obrazu a koncový bod. Vložte klíč předplatného a koncový bod do následujícího kódu, kde je uvedený. Váš koncový bod Počítačové zpracování obrazu má formulář `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> Nezapomeňte odebrat klíč předplatného z kódu, až budete hotovi, a nikdy ho zveřejnit. V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Například [Trezor klíčů Azure](../../../../key-vault/general/overview.md).

> [!div class="nextstepaction"]
> [Jsem u klienta nastavil](?success=set-up-client#object-model) [problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=set-up-client)

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé z hlavních funkcí sady OCR Python SDK.

|Název|Description|
|---|---|
|[ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin)| Tato třída přímo zpracovává všechny operace obrázků, jako je analýza obrázků, detekce textu a generování miniatur.|
| [ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient) | Tato třída je potřebná pro všechny funkce Počítačové zpracování obrazu. Vytvoří se jeho instance s informacemi o předplatném a Vy ho použijete k vytvoření instancí jiných tříd. Implementuje **ComputerVisionClientOperationsMixin**.|
|[VisualFeatureTypes](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| Tento výčet definuje různé typy analýz obrázků, které lze provést v rámci standardní operace analýzy. V závislosti na vašich potřebách můžete zadat sadu hodnot **VisualFeatureTypes** . |

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny OCR pro Python:

* [Ověření klienta](#authenticate-the-client)
* [Číst vytištěné a ručně psaný text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte instanci klienta s vaším koncovým bodem a klíčem. Vytvořte objekt [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) s klíčem a použijte ho u svého koncového bodu k vytvoření objektu [ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

> [!div class="nextstepaction"]
> Jsem ověřil (a) jsem [, že klient](?success=authenticate-client#read-printed-and-handwritten-text) [narazil na problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=authenticate-client)

## <a name="read-printed-and-handwritten-text"></a>Číst vytištěné a ručně psaný text

Služba OCR může číst viditelný text v obrázku a převést jej na datový proud znaků. Provedete to ve dvou částech.

### <a name="call-the-read-api"></a>Volání rozhraní API pro čtení

Nejprve použijte následující kód pro volání metody **Read** pro daný obrázek. Tato operace vrátí ID operace a spustí asynchronní proces pro čtení obsahu obrázku.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_call)]

> [!TIP]
> Můžete si také přečíst text z místní image. Podívejte se na metody [ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin) , jako je například **read_in_stream**. Nebo si přečtěte ukázkový kód na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py) , kde najdete scénáře týkající se místních imagí.

### <a name="get-read-results"></a>Získat výsledky čtení

Potom Získejte ID operace vrácené voláním metody **Read** a použijte ji k dotazování služby na výsledky operace. Následující kód kontroluje operaci v intervalu 1 sekund, dokud nebudou vráceny výsledky. Pak zobrazí extrahovaná textová data do konzoly.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_response)]

> [!div class="nextstepaction"]
> [Načetl jsem text](?success=read-printed-handwritten-text#run-the-application) , [který jsem narazil na problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=read-printed-handwritten-text)

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

V tomto rychlém startu jste zjistili, jak nainstalovat knihovnu klienta pro rozpoznávání OCR a použít rozhraní API pro čtení. V dalším kroku se dozvíte víc o funkcích rozhraní API pro čtení.

> [!div class="nextstepaction"]
>[Volání rozhraní API pro čtení](../../Vision-API-How-to-Topics/call-read-api.md)

* [Přehled rozpoznávání OCR](../../overview-ocr.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py).
