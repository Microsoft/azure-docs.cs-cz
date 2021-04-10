---
title: 'Rychlý Start: Klientská knihovna pro rozpoznávání optického znaku pro Node.js'
description: Začínáme s klientskou knihovnou pro rozpoznávání optických znaků pro Node.js s tímto rychlým startem
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: cb4679152740b73d6bb9cf7288fcaa811b6d6141
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073543"
---
<a name="HOLTop"></a>

Pomocí klientské knihovny pro rozpoznávání optických znaků si můžete přečíst vytištěné a ručně psaný text pomocí rozhraní API pro čtení.

[Referenční dokumentace](/javascript/api/@azure/cognitiveservices-computervision/)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision)  |  [Balíček (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision)  |  [Ukázky](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Aktuální verze [Node.js](https://nodejs.org/)
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" vytvořte prostředek počítačové zpracování obrazu vytvoření prostředku "  target="_blank"> Počítačové zpracování obrazu </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k Počítačové zpracování obrazu službě budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj.

```console
mkdir myapp && cd myapp
```

Spuštěním `npm init` příkazu vytvořte aplikaci uzlu se `package.json` souborem.

```console
npm init
```

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Nainstalujte `ms-rest-azure` balíček a `@azure/cognitiveservices-computervision` npm:

```console
npm install @azure/cognitiveservices-computervision
```

Nainstalujte také modul Async:

```console
npm install async
```

Soubor vaší aplikace `package.json` bude aktualizován pomocí závislostí.

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js), který obsahuje příklady kódu v tomto rychlém startu.

Vytvořte nový soubor *index.js* a otevřete ho v textovém editoru.

### <a name="find-the-subscription-key-and-endpoint"></a>Najít klíč předplatného a koncový bod

Přejděte na Azure Portal. Pokud se prostředek Počítačové zpracování obrazu, který jste vytvořili v části **předpoklady** , se úspěšně nasadil, klikněte v části **Další kroky** na tlačítko **Přejít k prostředku** . Klíč předplatného a koncový bod můžete najít na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**. 

Vytvořte proměnné pro klíč předplatného Počítačové zpracování obrazu a koncový bod. Vložte klíč předplatného a koncový bod do následujícího kódu, kde je uvedený. Váš koncový bod Počítačové zpracování obrazu má formulář `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> Nezapomeňte odebrat klíč předplatného z kódu, až budete hotovi, a nikdy ho zveřejnit. V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Například [Trezor klíčů Azure](../../../../key-vault/general/overview.md).

> [!div class="nextstepaction"]
> [Jsem u klienta nastavil](?success=set-up-client#object-model) [problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=set-up-client)

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé z hlavních funkcí sady OCR Node.js SDK.

|Název|Description|
|---|---|
| [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) | Tato třída je potřebná pro všechny funkce Počítačové zpracování obrazu. Vytvoříte jeho instanci s informacemi o předplatném a použijete ho k provádění většiny operací s imagí.|

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny OCR pro Node.js:

* [Ověření klienta](#authenticate-the-client)
* [Číst vytištěné a ručně psaný text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Ověření klienta


Vytvořte instanci klienta s vaším koncovým bodem a klíčem. Vytvořte objekt [ApiKeyCredentials](/python/api/msrest/msrest.authentication.apikeycredentials) s klíčem a koncovým bodem a použijte ho k vytvoření objektu [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

Pak definujte funkci `computerVision` a deklarujte asynchronní řadu s primární funkcí a funkcí zpětného volání. Do primární funkce přidáte svůj kód pro rychlý Start a zavoláte `computerVision` se do dolní části skriptu. Zbytek kódu v tomto rychlém startu přechází dovnitř `computerVision` funkce.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

> [!div class="nextstepaction"]
> Jsem ověřil (a) jsem [, že klient](?success=authenticate-client#read-printed-and-handwritten-text) [narazil na problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=authenticate-client)



## <a name="read-printed-and-handwritten-text"></a>Číst vytištěné a ručně psaný text

Služba OCR může extrahovat viditelný text v obrázku a převést jej na datový proud. Tato ukázka používá operace čtení.

### <a name="set-up-test-images"></a>Nastavení testovacích imagí

Uložte odkaz na adresu URL obrázků, ze kterých chcete extrahovat text.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

> [!NOTE]
> Můžete si také přečíst text z místní image. Podívejte se na metody [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) , jako je například **readInStream**. Nebo si přečtěte ukázkový kód na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) , kde najdete scénáře týkající se místních imagí.

### <a name="call-the-read-api"></a>Volání rozhraní API pro čtení

Definujte následující pole ve funkci, aby se načetly hodnoty stavu volání metody Read.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_statuses)]

Přidejte následující kód, který volá `readTextFromURL` funkci pro dané obrázky.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

Definujte `readTextFromURL` funkci. Volá metodu **Read** objektu klienta, který vrací ID operace a spustí asynchronní proces pro čtení obsahu obrázku. Pak pomocí ID operace zkontroluje stav operace, dokud nebudou vráceny výsledky. Vrátí extrahované výsledky.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

Pak definujte pomocnou funkci `printRecText` , která vytiskne výsledky operací čtení do konzoly.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

> [!div class="nextstepaction"]
> [Načetl jsem text](?success=read-printed-handwritten-text#run-the-application) , [který jsem narazil na problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí `node` příkazu v souboru rychlého startu.

```console
node index.js
```

> [!div class="nextstepaction"]
> [Spustil (a) jsem aplikaci,](?success=run-the-application#clean-up-resources) na kterou [mám problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=run-the-application)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Vyčistil](?success=clean-up-resources#next-steps) jsem problémy, které [jsem narazil na problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=clean-up-resources)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak nainstalovat knihovnu klienta pro rozpoznávání OCR a použít rozhraní API pro čtení. V dalším kroku se dozvíte víc o funkcích rozhraní API pro čtení.

> [!div class="nextstepaction"]
>[Volání rozhraní API pro čtení](../../Vision-API-How-to-Topics/call-read-api.md)

* [Přehled rozpoznávání OCR](../../overview-ocr.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js).
