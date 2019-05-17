---
title: 'Kurz: Použít vlastní logo detektor k rozpoznání Azure services – Custom Vision'
titlesuffix: Azure Cognitive Services
description: V tomto kurzu přesune se krokování přes ukázkovou aplikaci, která používá Azure Custom Vision jako součást scénáře detekce logo. Dozvíte se, jak je využívat Custom Vision s ostatními součástmi dodávat aplikace začátku do konce.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: pafarley
ms.openlocfilehash: 51b2cd42fabe6406f88388e99459a6f3dd3e69f5
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827657"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>Kurz: Rozpoznat loga služeb Azure v fotoaparátu

V tomto kurzu prozkoumáte ukázkovou aplikaci, která používá Azure Custom Vision jako součást většího scénáře. Zřizování Visual AI aplikace Xamarin.Forms app pro mobilní platformy, analyzuje obrázky fotoaparát služby Azure log a pak nasadí skutečné služby k účtu uživatele Azure. Zde se dozvíte, jak používá vlastní vize ve spolupráci s ostatními součástmi poskytovat užitečné aplikace začátku do konce. Spustit scénář celá aplikaci sami, nebo jednoduše dokončete Custom Vision součást instalace a prozkoumejte, jak se aplikace používá.

V tomto kurzu se dozvíte, jak:

> [!div class="checklist"]
> - Vytvořte vlastní objekt detektor rozpoznat loga služby Azure.
> - Připojte svou aplikaci k Azure počítačové zpracování obrazu a Custom Vision.
> - Vytvoření instančního objektu účtu služby Azure k nasazení služby Azure z aplikace.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete. 

## <a name="prerequisites"></a>Požadavky

- [Visual Studio 2017 nebo novější](https://www.visualstudio.com/downloads/)
- Úlohy Xamarinu pro Visual Studio (viz [instalaci Xamarin](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/windows))
- Se systémem iOS nebo emulátoru Androidu pro Visual Studio
- [Rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) (volitelné)

## <a name="get-the-source-code"></a>Získat zdrojový kód

Pokud chcete pomocí zadané webové aplikace, klonovat nebo stáhnout zdrojový kód aplikace z [Visual zřízení AI](https://github.com/Microsoft/AIVisualProvision) úložišti na Githubu. Otevřít *Source/VisualProvision.sln* souboru v sadě Visual Studio. Později upravíte některé soubory projektu, můžete tuto aplikaci spustit.

## <a name="create-an-object-detector"></a>Vytvoření detektor objektu

Přihlaste se k [webu Custom Vision](https://customvision.ai/) a vytvořte nový projekt. Zadejte projekt detekce objektů a používat Logo domény. To vám umožní službě použít algoritmus optimalizovaná pro zjišťování logo. 

![Okno Nový projekt na webu Custom Vision v prohlížeči Chrome](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>Nahrání a označení obrázků

V dalším kroku učení algoritmu detekce logo nahrávání imagí Azure služba log a označit ručně. Úložiště AIVisualProvision obsahuje sadu trénovacích obrázků, které můžete použít. Na webu, vyberte **přidávat image** tlačítko **Trénovacích obrázků** kartu. Pak přejděte **dokumenty a obrázky/Training_DataSet** složky úložiště. Budete muset ručně označit loga každé Image, proto pokud jenom testujete tento projekt, je vhodné nahrát pouze podmnožinu bitové kopie. Nahrajte aspoň 15 výskyty jednotlivé značky, které chcete použít.

Po odeslání trénovacích obrázků, vyberte první z nich na displeji. Tím se otevře okno značek. Vykreslení polí a přiřazení značek pro obě loga každé Image. 

![Logo označování na webu Custom Vision](media/azure-logo-tutorial/tag-logos.png)

Aplikace je nakonfigurováno pro práci s řetězci konkrétní značku. Definice v zjistíte *Source\VisualProvision\Services\Recognition\RecognitionService.cs* souboru:

[!code-csharp[Tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?range=18-33)]

Po můžete označit image, přejděte vpravo na další značky. Jakmile skončíte, zavřete okno označení.

## <a name="train-the-object-detector"></a>Trénování objekt detectoru

V levém podokně, nastavte **značky** přepnout na **značkami** k zobrazení obrázků. Zvolte zelené tlačítko v horní části stránky pro trénování modelu. To se dozvíte, že algoritmus rozpozná stejné značky v nových imagí. Taky otestujete na některé z vašich existujících imagí k vygenerování skóre přesnost modelu.

![Vize vlastní web na kartě Trénovacích obrázků. Na tomto snímku obrazovky je popsána tlačítko trénování](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>Získat adresu URL predikcí

Po model se trénuje, jste připraveni k jeho integraci do vaší aplikace. Chcete-li to provést, musíte získat adresu URL koncového bodu (adresa váš model, který bude dotaz na aplikace) a předpovědi klíče (k udělení přístupu aplikací k předpovědi požadavky). Na **výkonu** kartu, vyberte **předpovědi URL** tlačítko v horní části stránky.

![Custom Vision webu, zobrazuje okno Prediction API, který zobrazuje adresu URL a klíč rozhraní API](media/azure-logo-tutorial/cusvis-endpoint.png)

Zkopírujte adresu URL souboru obrázku a **předpovědi klíč** hodnota, která má odpovídající pole v *Source\VisualProvision\AppSettings.cs* souboru:

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=22-26)]

## <a name="examine-custom-vision-usage"></a>Zkontrolujte využití Custom Vision

Otevřít *Source/VisualProvision/Services/Recognition/CustomVisionService.cs* souboru a zjistěte, jak aplikace používá vaše Custom Vision key a koncového bodu adresy URL. **PredictImageContentsAsync** – metoda přijímá datový proud bajtů souboru obrázku spolu s zrušením tokenu (pro asynchronní úlohu správy), volá prediktivního rozhraní API Custom Vision a vrátí výsledek do predikce. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?range=12-28)]

Tento výsledek má formu **PredictionResult** instance, které obsahuje seznam **předpovědi** instancí. A **předpovědi** obsahuje zjištěné značku a její ohraničující umístění pole na obrázku.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?range=3-12)]

Další informace o způsobu, jakým aplikace zpracovává tato data, začínat **GetResourcesAsync** metody. Tato metoda je definována v *Source/VisualProvision/Services/Recognition/RecognitionService.cs* souboru.  

## <a name="add-computer-vision"></a>Přidat pro počítačové zpracování obrazu

Custom Vision část tohoto kurzu je dokončena. Pokud chcete aplikaci spustit, musíte integrovat službu pro počítačové zpracování obrazu. Aplikace používá funkce rozpoznávání textu pro počítačové zpracování obrazu k doplnění procesu zjišťování logo. Azure logo můžete rozpoznat podle jejího vzhledu *nebo* podle textu zobrazeny máte telefon po ruce. Na rozdíl od zpracování obrazu vlastní modely pro počítačové zpracování obrazu je které je předem vytrénované k provádění určitých operací imagí nebo videa.

Přihlásit ke službě pro počítačové zpracování obrazu a získejte klíč a koncový bod adresy URL. Nápovědu v tomto kroku naleznete v tématu [získání klíčů předplatného](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe).

![Služba pro počítačové zpracování obrazu na webu Azure Portal, s úvodní nabídky vybrána. Odkaz pro klíče je uvedeno, jako je adresa URL koncového bodu rozhraní API](media/azure-logo-tutorial/comvis-keys.png)

Dále otevřete *Source\VisualProvision\AppSettings.cs* souboru a naplnit `ComputerVisionEndpoint` a `ComputerVisionKey` proměnné s správné hodnoty.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=28-32)]

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Aplikace vyžaduje účet instančního objektu služby Azure k nasazení služeb do vašeho předplatného Azure. Instanční objekt služby umožňuje delegovat konkrétní oprávnění k aplikaci pomocí řízení přístupu na základě rolí. Další informace najdete v tématu [instanční objekty průvodce](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals).

Je můžete vytvořit instanční objekt pomocí Azure Cloud Shell nebo Azure CLI, jak je znázorněno zde. Pokud chcete začít, přihlaste se a vyberte předplatné, které chcete použít.

```console
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

Pak vytvořte instančního objektu služby. (Tento proces může chvíli trvat.)

```console
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

Po úspěšném dokončení byste měli vidět následující výstup ve formátu JSON, včetně potřebné přihlašovací údaje.

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```

Poznamenejte si `clientId` a `tenantId` hodnoty. Přidat do příslušných polí v *Source\VisualProvision\AppSettings.cs* souboru.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=8-16)]

## <a name="run-the-app"></a>Spuštění aplikace

V tomto okamžiku jste dali přístup k aplikaci pro:

- trénovaného modelu Custom Vision
- službu pro počítačové zpracování obrazu
- instanční objekt

Postupujte podle těchto kroků a spusťte aplikaci:

1. V Průzkumníku řešení Visual Studio, vyberte buď **VisualProvision.Android** projektu nebo **VisualProvision.iOS** projektu. Zvolte odpovídající emulátoru nebo připojeného mobilní zařízení z rozevírací nabídky na hlavním panelu nástrojů. Spusťte aplikaci.

    > [!NOTE]
    > Budete potřebovat ke spuštění emulátoru iOS zařízení s MacOS.

1. Na první obrazovce zadejte ID klienta instančního objektu služby, ID tenanta a heslo. Vyberte **přihlášení** tlačítko.

    > [!NOTE]
    > V některých emulátory **přihlášení** tlačítko nemusí být aktivovaná v tomto kroku. Pokud k tomu dojde, zastavte aplikaci, otevřete *Source/VisualProvision/Pages/LoginPage.xaml* souboru, vyhledejte `Button` prvku označeného **TLAČÍTKA pro přihlášení**, odstraňte následující řádek a spusťte aplikaci znovu.
    >  ```xaml
    >  IsEnabled="{Binding IsValid}"
    >  ```
    
    ![Na obrazovce aplikace zobrazí pole pro pověření instančního objektu](media/azure-logo-tutorial/app-credentials.png)

1. Na další obrazovce vyberte své předplatné Azure z rozevírací nabídky. (Tato nabídka by měl obsahovat všechna předplatná, ke kterým má hlavní službě přístup.) Vyberte **pokračovat** tlačítko. V tomto okamžiku může aplikace výzva k udělení přístupu k fotoaparátu zařízení a fotografie úložiště. Udělte oprávnění k přístupu.

    ![Na obrazovce aplikace zobrazuje pole rozevíracího seznamu pro předplatné Azure cílového](media/azure-logo-tutorial/app-az-subscription.png)


1. Aktivuje se používání fotoaparátu v zařízení. Pořiďte fotku jedné služby Azure log, které jste školení. Okno nasazení by měl vyzvat k výběru oblast a skupinu prostředků pro nové služby (jak byste udělali, pokud byly jejich nasazení na webu Azure Portal). 

    ![Obrazovky fotoaparátu smartphone, zaměřuje na dvě výřezy papíru Azure log](media/azure-logo-tutorial/app-camera-capture.png)

    ![Obrazovce aplikace zobrazí pole pro nasazení oblast a skupinu prostředků](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste postupovali podle všech kroků v tomto scénáři a používá aplikace k nasazení služby Azure do vašeho účtu, přejděte [webu Azure portal](https://ms.portal.azure.com/). Zrušte, služby, které nechcete použít.

Pokud budete chtít vytvořit vlastní projekt zjišťování objektu s Custom Vision, můžete chtít odstranit projekt detekce logo, které jste vytvořili v tomto kurzu. Bezplatná zkušební verze pro vlastní zpracování obrazu umožňuje pouze dva projekty. Odstranění projektu detekce logo, na [webu Custom Vision](https://customvision.ai), otevřete **projekty** a potom vyberte ikonu koše v rámci **tento nový projekt**.

## <a name="next-steps"></a>Další postup

V tomto kurzu se nastavit a prozkoumat plně funkční aplikaci Xamarin.Forms, používající Custom Vision service k detekci loga v mobilních snímků. Dále se naučíte osvědčené postupy pro vytváření modelu zpracování obrazu vlastní tak, aby při vytváření vlastní aplikace pro, můžete si je efektivní a přesné.

> [!div class="nextstepaction"]
> [K vylepšení klasifikátoru](getting-started-improving-your-classifier.md)
