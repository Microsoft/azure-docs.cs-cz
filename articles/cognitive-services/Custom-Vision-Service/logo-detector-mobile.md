---
title: 'Kurz: Použití vlastního detektoru loga k rozpoznání služeb Azure – Custom Vision'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu budete krokovat ukázkovou aplikaci, která používá vlastní vidění jako součást scénáře detekce loga. Zjistěte, jak se custom vision používá s dalšími součástmi k poskytování komplexní aplikace.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 51fa6d4859eb4b7f059b499ba73d84d9fc65e6f6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "78398980"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>Kurz: Rozpoznání log aslužeb Azure v obrazech fotoaparátu

V tomto kurzu prozkoumáte ukázkovou aplikaci, která používá vlastní vizi jako součást většího scénáře. Aplikace AI Visual Provision, aplikace Xamarin.Forms pro mobilní platformy, analyzuje obrázky z fotoaparátu log a pak nasazuje skutečné služby do účtu Azure uživatele. Zde se dozvíte, jak používá vlastní vizi v koordinaci s ostatními součástmi k poskytování užitečné aplikace od konce. Můžete spustit celý scénář aplikace pro sebe, nebo můžete dokončit pouze vlastní vize část nastavení a prozkoumat, jak ji aplikace používá.

V tomto kurzu se dozvíte, jak:

> [!div class="checklist"]
> - Vytvořte vlastní detektor objektů, který rozpozná loga služby Azure.
> - Připojte svou aplikaci k Azure Computer Vision a Custom Vision.
> - Vytvořte účet hlavního účtu služby Azure pro nasazení služeb Azure z aplikace.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete. 

## <a name="prerequisites"></a>Požadavky

- [Visual Studio 2017 nebo novější](https://www.visualstudio.com/downloads/)
- Úloha Xamarinu pro Visual Studio (viz [Instalace Xamarinu)](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/windows)
- Emulátor iOS nebo Android pro Visual Studio
- [Cli Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) (volitelné)

## <a name="get-the-source-code"></a>Získání zdrojového kódu

Pokud chcete použít zajišťovnou webovou aplikaci, naklonujte nebo stáhněte zdrojový kód aplikace z úložiště [Vizuální zřizování AI](https://github.com/Microsoft/AIVisualProvision) na GitHubu. Otevřete soubor *Source/VisualProvision.sln* v sadě Visual Studio. Později upravíte některé soubory projektu, abyste mohli aplikaci spustit.

## <a name="create-an-object-detector"></a>Vytvoření detektoru objektů

Přihlaste se na [web Custom Vision](https://customvision.ai/) a vytvořte nový projekt. Zadejte projekt detekce objektů a použijte doménu Logo; to umožní službě používat algoritmus optimalizovaný pro detekci loga. 

![Okno nového projektu na webu Custom Vision v prohlížeči Chrome](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>Nahrávání a označování obrázků

Dále trénujte algoritmus detekce loga nahráním bitových kopií log služby Azure a jejich ručním označováním. Úložiště AIVisualProvision obsahuje sadu trénovacích bitových kopií, které můžete použít. Na webu vyberte tlačítko **Přidat obrázky** na kartě **Trénování obrázků.** Potom přejděte do složky **Dokumenty/Obrázky/Training_DataSet** úložiště. Budete muset ručně označit loga v každém obrázku, takže pokud testujete pouze tento projekt, možná budete chtít nahrát pouze podmnožinu obrázků. Nahrajte alespoň 15 instancí každé značky, kterou chcete použít.

Po nahrání trénovacích obrázků vyberte první na displeji. Zobrazí se okno pro označování. Nakreslete pole a přiřaďte značky pro každé logo v každém obrázku. 

![Označování loga na webu Custom Vision](media/azure-logo-tutorial/tag-logos.png)

Aplikace je nakonfigurována pro práci s konkrétními řetězci značek. Definice naleznete v souboru *Zdroj\VisualProvision\Services\Recognition\RecognitionService.cs:*

[!code-csharp[Tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?name=snippet_constants)]

Po označení obrázku přejděte doprava a označte další obrázek. Po dokončení zavřete okno pro označování.

## <a name="train-the-object-detector"></a>Trénování detektoru objektů

V levém podokně nastavte přepínač **Tagy** na **Tagované,** aby se zobrazily vaše obrázky. Pak vyberte zelené tlačítko v horní části stránky trénovat model. Algoritmus bude trénovat rozpoznat stejné značky v nových obrázků. Bude také testovat model na některé z vašich stávajících obrázků generovat přesnost skóre.

![Web Vlastní vize na kartě Tréninkové obrázky. Na tomto snímku obrazovky je načrtnuto tlačítko Vlak](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>Získat predikční adresu URL

Po tréninací modelu jste připraveni ho integrovat do aplikace. Budete muset získat adresu URL koncového bodu (adresu vašeho modelu, který aplikace bude dotaz) a klíč předpověď (udělit aplikaci přístup k požadavkům předpověď). Na kartě **Výkon** vyberte tlačítko **Adresa URL předpovědi** v horní části stránky.

![Web Vlastní vize zobrazující okno rozhraní API předpovědi, které zobrazuje adresu URL a klíč rozhraní API](media/azure-logo-tutorial/cusvis-endpoint.png)

Zkopírujte adresu URL koncového bodu a hodnotu **Predikční klíč** do příslušných polí v souboru *Source\VisualProvision\AppSettings.cs:*

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_cusvis_keys)]

## <a name="examine-custom-vision-usage"></a>Zkontrolujte využití vlastního vidění

Otevřete soubor *Source/VisualProvision/Services/Recognition/CustomVisionService.cs* a zjistěte, jak aplikace používá váš klíč Vlastní vize a adresu URL koncového bodu. **PredictImageContentsAsync** Metoda trvá bajt souboru bitové kopie spolu s token zrušení (pro správu asynchronní úlohy), volá rozhraní API vlastní vize předpověď a vrátí výsledek předpověď. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?name=snippet_prediction)]

Tento výsledek má podobu **PredictionResult** instance, která sama obsahuje seznam **instance Předpověď.** Předpověď **Prediction** obsahuje zjištěnou značku a její umístění ohraničovacího rámečku v obraze.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?name=snippet_prediction_class)]

Další informace o tom, jak aplikace zpracovává tato data, začněte metodou **GetResourcesAsync.** Tato metoda je definována v souboru *Source/VisualProvision/Services/Recognition/RecognitionService.cs.*  

## <a name="add-computer-vision"></a>Přidat počítačové vidění

Vlastní vize část kurzu je dokončena. Pokud chcete aplikaci spustit, budete muset také integrovat službu Počítačové vidění. Aplikace používá funkci rozpoznávání textu počítačového vidění k doplnění procesu detekce loga. Logo Azure lze rozpoznat podle jeho vzhledu *nebo* podle textu vytištěného v jeho blízkosti. Na rozdíl od modelů Custom Vision je počítačové vidění předem trénované k provádění určitých operací s obrázky nebo videi.

Přihlaste se ke službě Počítačové vidění a získejte adresu URL klíče a koncového bodu. Nápovědu k tomuto kroku naleznete v tématu [Jak získat klíče předplatného](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe).

![Služba Počítačové zpracování obrazu na portálu Azure s vybranou nabídkou Rychlý start. Je nastíněn odkaz na klíče, stejně jako adresa URL koncového bodu rozhraní API](media/azure-logo-tutorial/comvis-keys.png)

Dále otevřete soubor *Source\VisualProvision\AppSettings.cs* `ComputerVisionEndpoint` a `ComputerVisionKey` naplňte proměnné a správnými hodnotami.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_comvis_keys)]

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Aplikace vyžaduje účet hlavního účtu služby Azure k nasazení služeb do vašeho předplatného Azure. Instanční objekt umožňuje delegovat konkrétní oprávnění do aplikace pomocí řízení přístupu na základě rolí. Další informace naleznete v [průvodci instančními objekty](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals).

Instancí objektu služby můžete vytvořit pomocí Azure Cloud Shell nebo Azure CLI, jak je znázorněno zde. Začněte, přihlaste se a vyberte předplatné, které chcete použít.

```azurecli
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

Potom vytvořte instanční objekt. (Tento proces může nějakou dobu trvat.)

```azurecli
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

Po úspěšném dokončení byste měli vidět následující výstup JSON, včetně potřebných pověření.

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```

Poznamenejte `clientId` `tenantId` si hodnoty a. Přidejte je do příslušných polí v souboru *Source\VisualProvision\AppSettings.cs.*

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_serviceprincipal)]

## <a name="run-the-app"></a>Spuštění aplikace

V tomto okamžiku jste aplikaci udělili přístup k:

- Vyškolený model Custom Vision
- Služba počítačového vidění
- Účet instančního objektu

Spuštění aplikace spusťte následujícím způsobem:

1. V Průzkumníku řešení Visual Studio vyberte projekt **VisualProvision.Android** nebo projekt **VisualProvision.iOS.** Zvolte odpovídající emulátor nebo připojené mobilní zařízení z rozbalovací nabídky na hlavním panelu nástrojů. Pak spusťte aplikaci.

    > [!NOTE]
    > Ke spuštění emulátoru iOS budete potřebovat zařízení MacOS.

1. Na první obrazovce zadejte ID klienta instančního objektu, ID klienta a heslo. Vyberte tlačítko **Přihlásit** se.

    > [!NOTE]
    > U některých emulátorů nemusí být tlačítko **Přihlášení** v tomto kroku aktivováno. Pokud k tomu dojde, zastavte aplikaci, otevřete soubor *Source/VisualProvision/Pages/LoginPage.xaml,* vyhledejte `Button` prvek s názvem **LOGIN BUTTON**, odeberte následující řádek a pak aplikaci znovu spusťte.
    >  ```xaml
    >  IsEnabled="{Binding IsValid}"
    >  ```
    
    ![Obrazovka aplikace zobrazující pole pro pověření instančního objektu](media/azure-logo-tutorial/app-credentials.png)

1. Na další obrazovce vyberte předplatné Azure z rozbalovací nabídky. (Tato nabídka by měla obsahovat všechna předplatná, ke kterým má váš instanční objekt přístup.) Vyberte tlačítko **Pokračovat.** V tomto okamžiku vás aplikace může vyzvat k udělení přístupu k fotoaparátu a úložišti fotografií zařízení. Udělte přístupová oprávnění.

    ![Obrazovka aplikace s rozevíracím polem pro předplatné Target Azure](media/azure-logo-tutorial/app-az-subscription.png)


1. Fotoaparát v zařízení bude aktivován. Vyfoďte jedno z log služby Azure, které jste trénovali. Okno nasazení by vás mělo vyzvat k výběru oblasti a skupiny prostředků pro nové služby (jako byste to udělali, kdybyste je nasazovali z portálu Azure). 

    ![Obrazovka fotoaparátu smartphonu zaměřená na dva papírové výřezy log Azure](media/azure-logo-tutorial/app-camera-capture.png)

    ![Obrazovka aplikace zobrazující pole pro oblast nasazení a skupinu prostředků](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste postupovali podle všech kroků tohoto scénáře a pomocí aplikace jste ji nasadili ke svým účtům, přejděte na [portál Azure](https://ms.portal.azure.com/). Zde zrušte služby, které nechcete používat.

Pokud plánujete vytvořit vlastní projekt detekce objektů s vlastní vize, můžete odstranit projekt detekce loga, který jste vytvořili v tomto kurzu. Bezplatná zkušební verze pro Custom Vision umožňuje pouze dva projekty. Chcete-li odstranit projekt detekce loga, otevřete na [webu Vlastní vize](https://customvision.ai) **položku Projekty** a vyberte ikonu koše v části **Můj nový projekt**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu nastavíte a prozkoumáte plně vybavenou aplikaci Xamarin.Forms, která používá službu Custom Vision k detekci log v obrazech mobilních fotoaparátů. Dále se seznamte s osvědčenými postupy pro vytváření modelu vlastní vize, abyste ho při vytváření pro vlastní aplikaci mohli udělat výkonnou a přesnou.

> [!div class="nextstepaction"]
> [Jak zlepšit klasifikátor](getting-started-improving-your-classifier.md)
