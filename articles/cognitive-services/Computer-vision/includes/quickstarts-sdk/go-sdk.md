---
title: 'Rychlý Start: Klientská knihovna pro rozpoznávání optického znaku pro přejít'
titleSuffix: Azure Cognitive Services
description: Začněte s tímto rychlým startem s využitím klientské knihovny pro rozpoznávání optického rozpoznávání znaků.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 0ae059054c68da662e05342525987f6925184906
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073713"
---
<a name="HOLTop"></a>

Pomocí klientské knihovny pro rozpoznávání OCR si můžete přečíst vytištěný a rukou psaný text z obrázků.

[Referenční dokumentace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision)  |  [Balíček](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Nejnovější verze nástroje [Přejít](https://golang.org/dl/)
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" vytvořte prostředek počítačové zpracování obrazu vytvoření prostředku "  target="_blank"> Počítačové zpracování obrazu </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k Počítačové zpracování obrazu službě budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-go-project-directory"></a>Vytvořit adresář projektu přejít

V okně konzoly (cmd, PowerShell, terminál, bash) vytvořte nový pracovní prostor pro projekt na cestách s názvem `my-app` a přejděte na něj.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Váš pracovní prostor bude obsahovat tři složky:

* **Src** – tento adresář bude obsahovat zdrojový kód a balíčky. Všechny balíčky nainstalované pomocí `go get` příkazu se budou nacházet v tomto adresáři.
* **pkg** – tento adresář bude obsahovat kompilované objekty balíčku pro přechod. Všechny tyto soubory mají `.a` rozšíření.
* **bin** – tento adresář bude obsahovat binární spustitelné soubory, které se vytvoří při spuštění `go install` .

> [!TIP]
> Další informace o struktuře pracovního prostoru přejděte v [dokumentaci jazyka přejděte](https://golang.org/doc/code.html#Workspaces). Tato příručka obsahuje informace o nastavení `$GOPATH` a `$GOROOT` .

### <a name="install-the-client-library-for-go"></a>Instalace klientské knihovny pro přejít

Dále nainstalujte klientskou knihovnu pro přejít:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

nebo pokud používáte DEP, v rámci vašeho úložiště úložišť:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Vytvoření aplikace v cestách 

Dále vytvořte soubor v adresáři **Src** s názvem `sample-app.go` :

```bash
cd src
touch sample-app.go
```

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go), který obsahuje příklady kódu v tomto rychlém startu.

Otevřete `sample-app.go` v upřednostňovaném integrovaném vývojovém prostředí (IDE) nebo textovém editoru.

Deklarujete kontext v kořenovém adresáři vašeho skriptu. Tento objekt budete potřebovat ke spuštění většiny Počítačové zpracování obrazu volání funkcí.

### <a name="find-the-subscription-key-and-endpoint"></a>Najít klíč předplatného a koncový bod

Přejděte na Azure Portal. Pokud se prostředek Počítačové zpracování obrazu, který jste vytvořili v části **předpoklady** , se úspěšně nasadil, klikněte v části **Další kroky** na tlačítko **Přejít k prostředku** . Klíč předplatného a koncový bod můžete najít na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**. 

Vytvořte proměnné pro klíč předplatného Počítačové zpracování obrazu a koncový bod. Vložte klíč předplatného a koncový bod do následujícího kódu, kde je uvedený. Váš koncový bod Počítačové zpracování obrazu má formulář `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> Nezapomeňte odebrat klíč předplatného z kódu, až budete hotovi, a nikdy ho zveřejnit. V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Například [Trezor klíčů Azure](../../../../key-vault/general/overview.md).

V dalším kroku začnete přidávat kód pro provádění různých operací OCR.

> [!div class="nextstepaction"]
> [Jsem u klienta nastavil](?success=set-up-client#object-model) [problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=set-up-client)

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé z hlavních funkcí sady SDK pro rozpoznávání OCR.

|Název|Description|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Tato třída je potřebná pro všechny funkce Počítačové zpracování obrazu, jako je například analýza obrázku a čtení textu. Vytvoříte jeho instanci s informacemi o předplatném a použijete ho k provádění většiny operací s imagí.|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Tento typ obsahuje výsledky operace čtení dávky. |

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny OCR pro funkci přejít:

* [Ověření klienta](#authenticate-the-client)
* [Číst vytištěné a ručně psaný text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE]
> Tento krok předpokládá, že jste [vytvořili proměnné prostředí](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) pro svůj počítačové zpracování obrazu klíč a koncový bod s názvem `COMPUTER_VISION_SUBSCRIPTION_KEY` a `COMPUTER_VISION_ENDPOINT` v uvedeném pořadí.

Vytvořte `main` funkci a přidejte do ní následující kód, který vytvoří instanci klienta s vaším koncovým bodem a klíčem.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> Jsem ověřil (a) jsem [, že klient](?success=authenticate-client#read-printed-and-handwritten-text) [narazil na problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=authenticate-client)



## <a name="read-printed-and-handwritten-text"></a>Číst vytištěné a ručně psaný text

Služba OCR může číst viditelný text v obrázku a převést jej na datový proud znaků. Kód v této části definuje funkci, `RecognizeTextReadAPIRemoteImage` která používá objekt klienta k detekci a extrakci vytištěného nebo rukopisného textu v obrázku.

Přidejte odkaz na vzorový obrázek a volání funkce ve `main` funkci.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!TIP]
> Můžete také extrahovat text z místní image. Podívejte se na metody [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) , jako je například **BatchReadFileInStream**. Nebo si přečtěte ukázkový kód na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) , kde najdete scénáře týkající se místních imagí.

### <a name="call-the-read-api"></a>Volání rozhraní API pro čtení

Definujte novou funkci pro čtení textu, `RecognizeTextReadAPIRemoteImage` . Přidejte následující kód, který volá metodu **BatchReadFile** pro daný obrázek. Tato metoda vrátí ID operace a spustí asynchronní proces pro čtení obsahu obrázku.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>Získat výsledky čtení

Potom Získejte ID operace vrácené voláním **BatchReadFile** a použijte ji s metodou **GetReadOperationResult** pro dotazování služby na výsledky operace. Následující kód kontroluje operaci v intervalu 1 sekund, dokud nebudou vráceny výsledky. Pak zobrazí extrahovaná textová data do konzoly.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>Zobrazit výsledky čtení

Přidejte následující kód pro analýzu a zobrazení načtených textových dat a dokončete definici funkce.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

> [!div class="nextstepaction"]
> [Načetl jsem text](?success=read-printed-handwritten-text#run-the-application) , [který jsem narazil na problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci z adresáře aplikace pomocí `go run` příkazu.

```bash
go run sample-app.go
```

> [!div class="nextstepaction"]
> [Spustil (a) jsem aplikaci,](?success=run-the-application#clean-up-resources) na kterou [mám problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=run-the-application)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Vyčistil](?success=clean-up-resources#next-steps) jsem problémy, které [jsem narazil na problém](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=clean-up-resources)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak nainstalovat knihovnu klienta pro rozpoznávání OCR a použít rozhraní API pro čtení. V dalším kroku se dozvíte víc o funkcích rozhraní API pro čtení.

> [!div class="nextstepaction"]
>[Volání rozhraní API pro čtení](../../Vision-API-How-to-Topics/call-read-api.md)

* [Přehled rozpoznávání OCR](../../overview-ocr.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).
