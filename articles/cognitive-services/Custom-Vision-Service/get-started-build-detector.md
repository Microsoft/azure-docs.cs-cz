---
title: Rychlý start Vytvoření detektoru objektů – služba Custom Vision Service
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak pomocí webu Custom Vision vytvořit model klasifikace obrázků.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: ba121400368f37c4a562a9c34e209c59d15b173c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404117"
---
# <a name="quickstart-how-to-build-an-object-detector-with-custom-vision"></a>Úvodní příručka: Jak vytvořit detektor objektů s vlastní vizí

V tomto rychlém startu se dozvíte, jak vytvořit detektor objektů prostřednictvím webu Custom Vision. Po vytvoření modelu detektoru můžete pro detekci objektů použít službu Custom Vision.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

- Sada obrázků, s nimiž se trénování modelu detektoru. Sadu [ukázkových obrazů](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision/images) můžete použít na GitHubu. Nebo si můžete vybrat vlastní obrázky pomocí níže uvedených tipů.

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Vytvoření prostředků vlastní vize na webu Azure Portal

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Vytvoření nového projektu

Ve webovém prohlížeči přejděte na [webovou stránku Vlastní vize](https://customvision.ai) a __vyberte Přihlásit se__. Přihlaste se pomocí stejného účtu, který jste použili k přihlášení na portál Azure.

![Obrázek přihlašovací stránky](./media/browser-home.png)


1. Chcete-li vytvořit první projekt, vyberte **nový projekt**. Zobrazí se dialogové okno **Vytvořit nový projekt.**

    ![Dialogové okno nový projekt obsahuje pole pro název, popis a domény.](./media/get-started-build-detector/new-project.png)

1. Zadejte název a popis projektu. Pak vyberte skupinu prostředků. Pokud je váš účet pro přihlášení přidružený k účtu Azure, rozbalovací seznam Skupiny prostředků zobrazí všechny vaše skupiny prostředků Azure, které obsahují prostředek služby Custom Vision Service. 

   > [!NOTE]
   > Pokud není k dispozici žádná skupina prostředků, potvrďte, že jste se přihlásili [k customvision.ai](https://customvision.ai) se stejným účtem, jako jste použili k přihlášení k [portálu Azure](https://portal.azure.com/). Potvrďte také, že jste vybrali stejný "adresář" na portálu vlastní vize jako adresář na portálu Azure, kde jsou umístěny vaše vlastní prostředky Vision. Na obou webech můžete vybrat svůj adresář z nabídky rozevíracího účtu v pravém horním rohu obrazovky. 

1. V části __Typy projektů__vyberte __možnost Detekce objektů__ .

1. Dále vyberte jednu z dostupných domén. Každá doména optimalizuje detektor pro určité typy obrázků, jak je popsáno v následující tabulce. Budete moci změnit doménu později, pokud si budete přát.

    |Domain (Doména)|Účel|
    |---|---|
    |__Obecné__| Optimalizováno pro širokou škálu úloh detekce objektů. Pokud žádná z ostatních domén není vhodná nebo si nejste jisti, kterou doménu zvolit, vyberte obecnou doménu. |
    |__Logo__|Optimalizováno pro vyhledávání log značky v obrázcích.|
    |__Kompaktní domény__| Optimalizováno pro omezení detekce objektů v reálném čase na mobilních zařízeních. Modely generované kompaktními doménami lze exportovat pro místní spuštění.|

1. Nakonec vyberte __vytvořit projekt__.

## <a name="choose-training-images"></a>Výběr tréninkových obrázků

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Nahrávání a označování obrázků

V této části budete nahrávat a ručně tag obrázky pomoci trénovat detektor. 

1. Chcete-li přidat obrázky, klepněte na tlačítko __Přidat obrázky__ a vyberte __procházet místní soubory__. Chcete-li obrázky nahrát, vyberte __otevřít.__

    ![Ovládací prvek Přidat obrázky se zobrazí v levém horním rohu a jako tlačítko v dolní části středu.](./media/get-started-build-detector/add-images.png)

1. Nahrané obrázky se zobrazí v části **Neoznačené označení** v uznaném uživateli. Dalším krokem je ruční označení objektů, které se má detektor naučit rozpoznávat. Kliknutím na první obrázek otevřete dialogové okno tagování. 

    ![Nahrané obrázky v sekci Netagované](./media/get-started-build-detector/images-untagged.png)

1. Klepněte a přetáhněte obdélník kolem objektu v obraze. Potom zadejte nový název **+** značky s tlačítkem nebo vyberte existující značku z rozevíracího seznamu. Je velmi důležité označit každou instanci objektu(ů), který chcete zjistit, protože detektor používá netagovované pozadí jako negativní příklad v tréninku. Po dokončení tagování klikněte na šipku vpravo, abyste si značky uložili a přešli k dalšímu obrázku.

    ![Označení objektu obdélníkovým výběrem](./media/get-started-build-detector/image-tagging.png)

Chcete-li nahrát další sadu obrázků, vraťte se do horní části této části a opakujte kroky.

## <a name="train-the-detector"></a>Vyškolit detektor

Chcete-li trénovat model detektoru, vyberte tlačítko **Vlak.** Detektor používá všechny aktuální obrázky a jejich značky k vytvoření modelu, který identifikuje každý tagovaný objekt.

![Tlačítko vlak v pravém horním rohu panelu nástrojů záhlaví webové stránky](./media/getting-started-build-a-classifier/train01.png)

Tréninkový proces by měl trvat jen několik minut. Během této doby se na kartě **Výkon** zobrazí informace o procesu školení.

![Okno prohlížeče s trénovacím dialogem v hlavní části](./media/get-started-build-detector/training.png)

## <a name="evaluate-the-detector"></a>Vyhodnoťte detektor

Po dokončení školení se vypočítá a zobrazí výkon modelu. Služba Vlastní vize používá obrázky, které jste odeslali k školení, k výpočtu přesnosti, odvolání a průměrné přesnosti. Přesnost a zpození jsou dvě různá měření účinnosti detektoru:

- **Přesnost** označuje zlomek identifikovaných klasifikací, které byly správné. Pokud by například model identifikoval 100 obrázků jako psy a 99 z nich byli ve skutečnosti psi, pak by přesnost byla 99%.
- **Odvolání** označuje zlomek skutečné klasifikace, které byly správně identifikovány. Například, pokud tam bylo skutečně 100 obrázků jablek, a model identifikovány 80 jako jablka, odvolání by bylo 80%.

![Výsledky školení ukazují celkovou přesnost a odvolání a průměrnou přesnost.](./media/get-started-build-detector/trained-performance.png)

### <a name="probability-threshold"></a>Prahová hodnota pravděpodobnosti

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Správa iterací školení

Pokaždé, když trénujete detektor, vytvoříte novou _iteraci_ s vlastními aktualizovanými metrikami výkonu. Všechny iterace můžete zobrazit v levém podokně karty **Výkon.** V levém podokně najdete také tlačítko **Odstranit,** které můžete použít k odstranění iterace, pokud je zastaralá. Když odstraníte iteraci, odstraníte všechny obrazy, které jsou s ní jednoznačně přidruženy.

Viz [Použití modelu s rozhraním API předpovědi,](./use-prediction-api.md) kde se dozvíte, jak programově přistupovat k trénovaným modelům.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili vytvářet a trénovat model detektoru objektů pomocí webu Custom Vision. Dále získáte další informace o iterativním procesu zlepšování modelu.

> [!div class="nextstepaction"]
> [Testování a přetrénování modelu](test-your-model.md)

