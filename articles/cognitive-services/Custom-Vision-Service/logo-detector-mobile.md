---
title: 'Kurz: Použít vlastní logo detektor k rozpoznání Azure services – Custom Vision'
titlesuffix: Azure Cognitive Services
description: V tomto kurzu přesune se krokování přes ukázkovou aplikaci, která používá Azure Custom Vision jako součást scénáře detekce logo. Dozvíte se, jak je využívat Custom Vision s ostatními součástmi dodávat aplikace začátku do konce.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pafarley
ms.openlocfilehash: 520e1981544f6ad0a6f1412a47dc8ea50fdd53a5
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55773134"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>Kurz: Rozpoznat loga služeb Azure v fotoaparátu

V tomto kurzu přesune se krokování přes ukázkovou aplikaci, která používá Azure Custom Vision jako součást většího scénáře. Zřizování Visual AI aplikace Xamarin.Forms app pro mobilní platformy, analyzuje obrázky fotoaparát služby Azure log a pak nasadí skutečné služby k účtu uživatele Azure. Zde se dozvíte, jak používá vlastní vize ve spolupráci s ostatními součástmi poskytovat užitečné aplikace začátku do konce. Možná budete chtít spustit celou aplikaci sami, nebo můžete jednoduše dokončete Custom Vision součást instalace a prozkoumejte, jak aplikace využívá ho.

V tomto kurzu se dozvíte, jak:

> [!div class="checklist"]
> - Vytvořit vlastní objekt detektor rozpoznat loga služby Azure
> - Připojte svou aplikaci k Azure počítačové zpracování obrazu a Custom Vision
> - Vytvoření instančního objektu účtu služby Azure k nasazení služby Azure z aplikace

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete. 

## <a name="prerequisites"></a>Požadavky

- [Visual Studio 2017](https://www.visualstudio.com/downloads/)
- Úlohy Xamarinu pro Visual Studio (viz [instalaci Xamarin](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/windows))
- Se systémem iOS nebo emulátoru Androidu pro Visual Studio
- [Rozhraní příkazového řádku Azure (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) (volitelné)

## <a name="get-the-source-code"></a>Získat zdrojový kód

Pokud chcete pomocí zadané webové aplikace, klonovat nebo stáhnout zdrojový kód aplikace z [Visual zřízení AI](https://github.com/Microsoft/AIVisualProvision) úložišti na Githubu. Otevřít *Source/VisualProvision.sln* souboru v sadě Visual Studio. Později bude proveďte potřebné úpravy na některé soubory projektu, aby bylo možné aplikaci spustit.

## <a name="create-an-object-detector"></a>Vytvoření detektor objektu

Přihlaste se k [webu Custom Vision](https://customvision.ai/) a vytvořte nový projekt. Zadejte projekt detekce objektů a používat Logo domény. To vám umožní službě a použití algoritmu optimalizovaná pro zjišťování logo. 

![okno dialogové okno nového projektu na webu Custom Vision v prohlížeči Chrome](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>Nahrání a označení obrázků

V dalším kroku je potřeba učení algoritmu detekce logo nahrávání imagí Azure služba log a označit ručně. Úložiště AIVisualProvision obsahuje sadu trénovacích obrázků, které můžete použít. Na webu, vyberte **přidávat image** tlačítko **Trénovacích obrázků** kartu a potom přejděte na **dokumenty a obrázky/Training_DataSet** složce úložiště. Budete muset ručně označit loga v každé bitové kopie, takže pokud jenom testujete tento projekt, můžete chtít odeslat pouze podmnožinu bitové kopie. Minimálně musíte nahrát 15 výskyty jednotlivé značky, které chcete použít.

Po nahrání trénovacích obrázků, vyberte první z nich na displeji. Tím se otevře okno značek. Vykreslení polí a přiřazení značek pro obě loga každé Image. 

![Obrázek loga díky značkám se použijí na webu Custom Vision](media/azure-logo-tutorial/tag-logos.png)

Aplikace je nakonfigurováno pro práci s konkrétní značkou řetězce; Viz definice v *Source\VisualProvision\Services\Recognition\RecognitionService.cs* souboru:

[!code-csharp[tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?range=18-33)]

Když jste označili image, přejděte vpravo na další značky. Až budete hotovi, ukončete mimo okno označení.

## <a name="train-the-object-detector"></a>Trénování objekt detectoru

V levém podokně, nastavte **značky** přepnout na **značkami**, a měli byste vidět všechny Image. Klikněte na zelené tlačítko v horní části stránky pro trénování modelu. To se dozvíte algoritmus rozpozná stejné značky v nových imagí. Taky otestujete na některé z vašich existujících imagí k vygenerování skóre přesnost modelu.

![pro zpracování obrazu vlastní web, na kartě Trénovacích obrázků. uvedené tlačítko trénování](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>Získat adresu URL predikcí

Jakmile váš model se trénuje, jste připraveni k jeho integraci do vaší aplikace. Chcete-li to provést, musíte získat adresu URL koncového bodu (adresa váš model, který bude dotaz na aplikace) a předpovědi klíče (k udělení přístupu aplikací k předpovědi požadavky). V **výkonu** klikněte na tlačítko **předpovědi URL** tlačítko v horní části stránky.

![na webu Custom Vision Prediction API obrazovku, zobrazuje adresu URL adresy a klíč rozhraní api](media/azure-logo-tutorial/cusvis-endpoint.png)

Zkopírujte adresu URL souboru obrázku a `Prediction-key` hodnota, která má odpovídající pole v *Source\VisualProvision\AppSettings.cs* souboru:

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=22-26)]

## <a name="examine-custom-vision-usage"></a>Zkontrolujte využití Custom Vision

Otevřít *Source/VisualProvision/Services/Recognition/CustomVisionService.cs* souboru a zjistěte, jak vaše Custom Vision key a koncového bodu adresy URL se používají v aplikaci. **PredictImageContentsAsync** – metoda přijímá datový proud bajtů souboru obrázku spolu s zrušením tokenu (pro asynchronní úlohu správy), volá prediktivního rozhraní API Custom Vision a vrátí výsledek do predikce. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?range=12-28)]

Tento výsledek má formu **PredictionResult** instance, které obsahuje seznam **předpovědi** instancí. A **předpovědi** obsahuje zjištěné značku a její ohraničující umístění pole na obrázku.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?range=3-12)]

Pokud chcete získat další informace o způsobu, jakým aplikace zpracovává tato data, začínají **GetResourcesAsync** metoda, definována v *Source/VisualProvision/Services/Recognition/RecognitionService.cs* souboru. 

## <a name="add-computer-vision"></a>Přidat pro počítačové zpracování obrazu

Custom Vision část tohoto kurzu je dokončeno, ale pokud chcete aplikaci spustit, je potřeba integrovat službu pro počítačové zpracování obrazu. Aplikace používá funkce rozpoznávání textu počítačové zpracování obrazu k doplnění procesu zjišťování logo. Azure logo můžete rozpoznat podle jejího vzhledu _nebo_ podle textu zobrazeny máte telefon po ruce. Na rozdíl od zpracování obrazu vlastní modely pro počítačové zpracování obrazu předem vyškolený k provádění určitých operací imagí nebo videa.

Stačí se přihlásit k odběru službě pro počítačové zpracování obrazu a získejte klíč a koncový bod adresy URL. Zobrazit [získání klíčů předplatného](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe) Pokud potřebujete pomoc s tímto krokem.

![službu pro počítačové zpracování obrazu na webu Azure Portal, s úvodní nabídky vybrána. odkaz pro klíče je zvýrazněn, jak je adresa URL koncového bodu rozhraní API](media/azure-logo-tutorial/comvis-keys.png)

Pak otevřete *Source\VisualProvision\AppSettings.cs* souboru a naplnit `ComputerVisionEndpoint` a `ComputerVisionKey` proměnné s správné hodnoty.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=28-32)]


## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Aplikace vyžaduje účet instančního objektu služby Azure k nasazení služeb do vašeho předplatného Azure. Instanční objekt služby umožňuje delegovat konkrétní oprávnění k aplikaci pomocí řízení přístupu na základě rolí. Najdete v článku [instanční objekty průvodce](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals) Pokud chcete získat další informace.

Můžete vytvořit instanční objekt služby Azure Cloud Shell nebo Azure CLI (následujícím způsobem). Nejprve Přihlaste se a vyberte předplatné, které chcete použít.

```console
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

Vytvořte instanční (Všimněte si, že může trvat nějaký čas na dokončení).

```console
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

Po úspěšném dokončení měli byste vidět následující výstup ve formátu JSON obsahující potřebné přihlašovací údaje.

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```
Poznamenejte si `clientId`, a `tenantId` hodnoty. Přidat do příslušných polí v *Source\VisualProvision\AppSettings.cs* souboru.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=8-16)]

## <a name="run-the-app"></a>Spuštění aplikace

V tomto okamžiku jste dali přístup k aplikaci pro:
* trénovaného modelu Custom Vision
* službu pro počítačové zpracování obrazu
* instanční objekt 

Postupujte podle těchto kroků a spusťte aplikaci:

1. V Průzkumníku řešení Visual Studio vyberte projekt VisualProvision.Android nebo VisualProvision.iOS a z rozevírací nabídky na hlavním panelu nástrojů zvolte odpovídající emulátoru nebo připojeného mobilních zařízení (Všimněte si, že je třeba zařízení s MacOS ke spuštění emulátor iOS). Spusťte aplikaci.

1. V první obrazovka, která načte zadejte ID klienta instančního objektu služby, ID tenanta a heslo. Klikněte na tlačítko **přihlášení** tlačítko.

    > [!NOTE]
    > V některých emulátory **přihlášení** nemusí aktivovat tlačítko v tomto kroku. Pokud k tomu dojde, zastavte aplikaci, otevřete _Source/VisualProvision/Pages/LoginPage.xaml_ souboru, vyhledejte `Button` element označeny TLAČÍTKA pro přihlášení a odebrání řádku:
      ```xaml
      IsEnabled="{Binding IsValid}"
      ```
    
    > Spusťte aplikaci znovu.

    ![na obrazovce aplikace s poli pro pověření instančního objektu](media/azure-logo-tutorial/app-credentials.png)

1. Na další obrazovce vyberte své předplatné Azure z rozevírací nabídky (to by měl obsahovat všechna předplatná, ke kterým má instančního objektu služby přístup). Klikněte na tlačítko **pokračovat** tlačítko.

    ![na obrazovce aplikace s pole rozevíracího seznamu pro předplatné Azure cílového](media/azure-logo-tutorial/app-az-subscription.png)

    V tomto okamžiku může aplikaci výzvu k udělení přístupu k zařízením úložiště kamery a fotka. Přijměte tato oprávnění.

1. V dalším kroku se budou aktivovat používání fotoaparátu v zařízení. Pořiďte fotku jedné služby Azure log, kterou jste školení. Dialogové okno nasazení by měl vyzvat k výběru oblast a skupinu prostředků pro nové služby (jak byste udělali, pokud byly jejich nasazení na webu Azure Portal). 

    ![na obrazovce chytrém telefonu fotoaparátu dvě výřezy papíru Azure log v zobrazení](media/azure-logo-tutorial/app-camera-capture.png)

    ![obrazovce aplikace s poli pro zadání skupiny nasazení oblasti a prostředků](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Pokud jste postupovali podle všech kroků v tomto scénáři a používá aplikace k nasazení služby Azure do vašeho účtu, ujistěte se, přejděte [webu Azure portal](https://ms.portal.azure.com/) při dokončení a zrušit jej nepřejete použít služby.

Také pokud budete chtít vytvořit vlastní objekt detekce projekt s Custom Vision v budoucnu, můžete chtít odstranit projekt detekce logo, které jste vytvořili v tomto kurzu. Bezplatná zkušební verze pro vlastní zpracování obrazu umožňuje dva projekty. Na [webu Custom Vision](https://customvision.ai), přejděte na **projekty** a vyberte Koše můžete v rámci **tento nový projekt**.

## <a name="next-steps"></a>Další postup

V tomto kurzu se nastavit a prozkoumat plně funkční aplikaci Xamarin.Forms, která využívá službu Custom Vision zjišťovat loga v mobilních snímků. Dále se naučíte osvědčené postupy pro vytváření modelu zpracování obrazu vlastní tak, aby při vytváření vlastní aplikace pro můžete si je efektivní a přesné.

> [!div class="nextstepaction"]
> [K vylepšení klasifikátoru](getting-started-improving-your-classifier.md)