---
title: Rychlý Start – vytvoření objektu detektoru – Custom Vision Service
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak pomocí Custom Vision webu vytvořit model objektu pro rozpoznávání.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 09/15/2020
ms.author: pafarley
ms.openlocfilehash: 234c80e84e11579a66737c3e5efedfda34182ac5
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2020
ms.locfileid: "90602859"
---
# <a name="quickstart-how-to-build-an-object-detector-with-custom-vision"></a>Rychlý Start: vytvoření objektu detektoru pomocí Custom Vision

V tomto rychlém startu se dozvíte, jak vytvořit objektový detektor prostřednictvím webu Custom Vision. Po vytvoření modelu můžete testovat s využitím nových imagí a následně je integrovat do vlastního softwaru pro rozpoznávání imagí.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Požadavky

- Sada imagí, se kterými se má model detektoru naučit. Můžete použít sadu [ukázkových imagí](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision/images) na GitHubu. Případně můžete pomocí níže uvedených tipů zvolit vlastní image.

## <a name="create-custom-vision-resources"></a>Vytvoření prostředků Custom Vision

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Vytvoření nového projektu

Ve webovém prohlížeči přejděte na [webovou stránku Custom Vision](https://customvision.ai) a vyberte __Přihlásit__se. Přihlaste se pomocí stejného účtu, který jste použili k přihlášení do Azure Portal.

![Obrázek přihlašovací stránky](./media/browser-home.png)


1. Chcete-li vytvořit svůj první projekt, vyberte možnost **Nový projekt**. Zobrazí se dialogové okno **vytvořit nový projekt** .

    ![Dialogové okno Nový projekt obsahuje pole pro název, popis a domény.](./media/get-started-build-detector/new-project.png)

1. Zadejte název a popis projektu. Pak vyberte skupinu prostředků. Pokud je přihlášený účet přidružený k účtu Azure, zobrazí se v rozevíracím seznamu Skupina prostředků všechny skupiny prostředků Azure, které zahrnují prostředek Custom Vision Service. 

   > [!NOTE]
   > Pokud není k dispozici žádná skupina prostředků, potvrďte prosím, že jste se k [customvision.AI](https://customvision.ai) přihlásili pomocí stejného účtu, jako jste použili k přihlášení do [Azure Portal](https://portal.azure.com/). Ověřte také, že jste na webu Custom Vision vybrali stejný adresář jako adresář v Azure Portal, kde se nacházejí Custom Vision prostředky. V obou lokalitách můžete adresář vybrat v nabídce účtu rozevírací nabídky v pravém horním rohu obrazovky. 

1. V části __typy projektů__vyberte možnost __detekce objektu__ .

1. V dalším kroku vyberte jednu z dostupných domén. Každá doména optimalizuje detektor pro konkrétní typy imagí, jak je popsáno v následující tabulce. V případě potřeby budete moci doménu později změnit.

    |Doména|Účel|
    |---|---|
    |__Obecné__| Optimalizováno pro širokou škálu úloh detekce objektů. Pokud žádná z ostatních domén není vhodná nebo si nejste jisti, kterou doménu si zvolíte, vyberte obecnou doménu. |
    |__Logo__|Optimalizováno pro hledání loga značky v obrázcích.|
    |__Produkty na police__|Optimalizováno pro zjišťování a klasifikaci produktů v police.|
    |__Kompaktní domény__| Optimalizováno pro omezení detekce objektů v reálném čase na mobilních zařízeních. Modely generované pomocí kompaktních domén lze exportovat pro místní spuštění.|

1. Nakonec vyberte __vytvořit projekt__.

## <a name="choose-training-images"></a>Zvolit školicí obrázky

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Nahrávání a označování obrázků

V této části nahrajete a ručně označíte obrázky, které vám pomůžou naučit detektor. 

1. Pokud chcete přidat obrázky, klikněte na tlačítko __Přidat image__ a pak vyberte __Procházet místní soubory__. Vyberte __otevřít__ a nahrajte obrázky.

    ![Ovládací prvek přidat obrázky se zobrazí v levém horním rohu a jako tlačítko ve spodní části středu.](./media/get-started-build-detector/add-images.png)

1. Nahrané obrázky se zobrazí v části bez **značky** v uživatelském rozhraní. Dalším krokem je ruční označení objektů, které má detektor zjistit, aby se rozpoznal. Kliknutím na první obrázek otevřete dialogové okno tagování. 

    ![Nahrané obrázky v oddílu bez příznaku](./media/get-started-build-detector/images-untagged.png)

1. Klikněte na obdélník kolem objektu v imagi a přetáhněte ho. Pak zadejte nový název značky s **+** tlačítkem nebo vyberte existující značku z rozevíracího seznamu. Je velmi důležité označit všechny instance objektů, které chcete detekovat, protože detektor používá oblast bez příznaku na pozadí jako negativní příklad při výuce. Po dokončení označování značek klikněte na šipku vpravo a uložte značky a přejděte k dalšímu obrázku.

    ![Označení objektu obdélníkovým výběrem](./media/get-started-build-detector/image-tagging.png)

Pokud chcete nahrát další sadu imagí, vraťte se na začátek této části a opakujte postup.

## <a name="train-the-detector"></a>Naučit detektor

Chcete-li naučit model detektoru, vyberte tlačítko **výuka** . Detektor používá všechny aktuální image a jejich značky k vytvoření modelu, který identifikuje každý tagovaný objekt.

![Tlačítko vlaku v pravém horním rohu panelu nástrojů záhlaví webové stránky](./media/getting-started-build-a-classifier/train01.png)

Proces školení by měl trvat jen několik minut. Během této doby se na kartě **výkon** zobrazí informace o procesu školení.

![Okno prohlížeče s výukovým dialogem v hlavní části](./media/get-started-build-detector/training.png)

## <a name="evaluate-the-detector"></a>Vyhodnocení detektoru

Po dokončení školení se vypočítává a zobrazuje výkon modelu. Služba Custom Vision používá obrázky, které jste odeslali pro školení, a vypočítá přesnost, odvolání a průměrnou přesnost. Přesnost a odvolání jsou dvě odlišná měření účinnosti detektoru:

- **Přesnost** označuje zlomek identifikovaných klasifikací, které byly správné. Například pokud model identifikoval 100 obrázků jako psi a 99 z nich byly ve skutečnosti psi, pak přesnost by byla 99%.
- **Odvolání** indikuje zlomek skutečných klasifikací, které byly správně identifikovány. Pokud by například existovaly skutečné 100 bitové kopie jablek a model identifikoval 80 jako jablk, odvolání by bylo 80%.

![Ve výsledcích školení se zobrazuje celková přesnost a odvolání a průměrná hodnota přesnosti.](./media/get-started-build-detector/trained-performance.png)

### <a name="probability-threshold"></a>Prahová hodnota pravděpodobnosti

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Spravovat iterace cvičení

Při každém výukovém detektoru vytvoříte novou _iteraci_ s vlastní aktualizovanou metrikou výkonu. Všechny své iterace můžete zobrazit v levém podokně karty **výkon** . V levém podokně se zobrazí také tlačítko **Odstranit** , které můžete použít k odstranění iterace, pokud je zastaralá. Odstraněním iterace odstraníte všechny bitové kopie, které s ní jsou jednoznačně přidružené.

Informace o tom, jak programově přistupovat k vašim vyškolených modelům, najdete v tématu [použití modelu s prediktivním rozhraním API](./use-prediction-api.md) .

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit a naučit model objektového detektoru pomocí Custom Vision webu. Dále Získejte další informace o iterativním procesu zlepšení modelu.

> [!div class="nextstepaction"]
> [Testování a přetrénování modelu](test-your-model.md)

* [Co je Custom Vision?](./overview.md)
