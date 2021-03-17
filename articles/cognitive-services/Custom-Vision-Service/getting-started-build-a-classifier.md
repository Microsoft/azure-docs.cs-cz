---
title: 'Rychlý Start: vytvoření klasifikátoru pomocí Custom Vision Web'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak používat Custom Vision web k vytváření, školení a testování modelu klasifikace imagí.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 01/29/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: rozpoznávání obrázků, aplikace pro rozpoznávání obrázků, vlastní vize
ms.openlocfilehash: d25b226f5dfff34aa572b40d1e4de142cf8e0b7b
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221240"
---
# <a name="quickstart-build-a-classifier-with-the-custom-vision-website"></a>Rychlý Start: vytvoření klasifikátoru pomocí Custom Vision Web

V tomto rychlém startu se dozvíte, jak pomocí Custom Vision webu vytvořit model klasifikace imagí. Jakmile model sestavíte, můžete ho otestovat pomocí nových imagí a následně ho integrovat do vlastní aplikace pro rozpoznávání imagí.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Požadavky

- Sada imagí, se kterými chcete své třídění proškolit. Tipy k výběru imagí najdete níže.

## <a name="create-custom-vision-resources"></a>Vytvoření prostředků Custom Vision

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Vytvoření nového projektu

Ve webovém prohlížeči přejděte na [webovou stránku Custom Vision](https://customvision.ai) a vyberte __Přihlásit__ se. Přihlaste se pomocí stejného účtu, který jste použili k přihlášení do Azure Portal.

![Obrázek přihlašovací stránky](./media/browser-home.png)


1. Chcete-li vytvořit svůj první projekt, vyberte možnost **Nový projekt**. Zobrazí se dialogové okno **vytvořit nový projekt** .

    ![Dialogové okno Nový projekt obsahuje pole pro název, popis a domény.](./media/getting-started-build-a-classifier/new-project.png)

1. Zadejte název a popis projektu. Pak vyberte skupinu prostředků. Pokud je přihlášený účet přidružený k účtu Azure, zobrazí se v rozevíracím seznamu Skupina prostředků všechny skupiny prostředků Azure, které zahrnují prostředek Custom Vision Service. 

   > [!NOTE]
   > Pokud není k dispozici žádná skupina prostředků, potvrďte prosím, že jste se k [customvision.AI](https://customvision.ai) přihlásili pomocí stejného účtu, jako jste použili k přihlášení do [Azure Portal](https://portal.azure.com/). Ověřte také, že jste na webu Custom Vision vybrali stejný adresář jako adresář v Azure Portal, kde se nacházejí Custom Vision prostředky. V obou lokalitách můžete adresář vybrat v nabídce účtu rozevírací nabídky v pravém horním rohu obrazovky. 

1. V části __typy projektů__ vyberte __klasifikace__ . Pak v části __typy klasifikací__ zvolte v závislosti na použitém případu použití buď více **štítků** , nebo více **tříd**. Klasifikace s více štítky aplikuje libovolný počet značek na obrázek (nula nebo více), zatímco klasifikace s více třídami řadí obrázky do jednoduchých kategorií (každý odeslaný obrázek bude seřazený do nejpravděpodobnější značky). V případě, že chcete, budete moci typ klasifikace později změnit.

1. V dalším kroku vyberte jednu z dostupných domén. Každá doména optimalizuje klasifikátor pro konkrétní typy imagí, jak je popsáno v následující tabulce. V případě potřeby budete moci doménu později změnit.

    |Doména|Účel|
    |---|---|
    |__Obecné__| Optimalizováno pro širokou škálu úloh klasifikace imagí. Pokud žádná z ostatních domén není vhodná nebo si nejste jisti, jakou doménu chcete vybrat, vyberte obecnou doménu. |
    |__Simulant__|Optimalizováno pro fotografie misek, jak byste je viděli v nabídce restaurace. Pokud chcete klasifikovat fotografie jednotlivých druhů ovoce a zeleniny, použijte doménu jídla.|
    |__Orientační body tváře__|Optimalizováno pro rozpoznatelný orientačních bodů, jak přirozené, tak umělé. Tato doména funguje nejlépe, když je ve fotografii jasně viditelný bod. Tato doména funguje i v případě, že je bod lehce překážkou pro lidi před ním.|
    |__Maloobchod__|Optimalizováno pro obrázky, které se nacházejí v nákupním katalogu nebo na nákupním webu. Pokud požadujete vysokou přesnost klasifikace mezi dresses, Pants a košile, použijte tuto doménu.|
    |__Kompaktní domény__| Optimalizováno pro omezení klasifikace v reálném čase na mobilních zařízeních. Modely generované pomocí kompaktních domén lze exportovat pro místní spuštění.|

1. Nakonec vyberte __vytvořit projekt__.

## <a name="choose-training-images"></a>Zvolit školicí obrázky

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Nahrávání a označování obrázků

V této části nahrajete a ručně označíte obrázky, které vám pomůžou rozučit třídění. 

1. Pokud chcete přidat obrázky, vyberte __Přidat image__ a pak vyberte __Procházet místní soubory__. Vyberte __otevřít__ a přejděte k označení. Výběr značek bude použit pro celou skupinu imagí, které jste vybrali k nahrání, takže je snazší nahrávat obrázky do samostatných skupin podle jejich použitých značek. Můžete také změnit značky pro jednotlivé obrázky po jejich nahrání.

    ![Ovládací prvek přidat obrázky se zobrazí v levém horním rohu a jako tlačítko ve spodní části středu.](./media/getting-started-build-a-classifier/add-images01.png)


1. Chcete-li vytvořit značku, zadejte text do pole __Moje značky__ a stiskněte klávesu ENTER. Pokud značka již existuje, zobrazí se v rozevírací nabídce. V projektu s více štítky můžete do imagí přidat více než jednu značku, ale v projektu s více třídami můžete přidat pouze jeden. K dokončení nahrávání imagí použijte tlačítko __nahrát [číslo] soubory__ . 

    ![Obrázek stránky značek a nahrání](./media/getting-started-build-a-classifier/add-images03.png)

1. Po nahrání imagí vyberte __Hotovo__ .

    ![Indikátor průběhu zobrazuje všechny dokončené úkoly.](./media/getting-started-build-a-classifier/add-images04.png)

Pokud chcete nahrát další sadu imagí, vraťte se na začátek této části a opakujte postup.

## <a name="train-the-classifier"></a>Trénování klasifikátoru

Chcete-li naučit třídění, vyberte tlačítko **výuka** . Klasifikátor používá všechny aktuální image k vytvoření modelu, který identifikuje vizuální vlastnosti jednotlivých značek.

![Tlačítko vlaku v pravém horním rohu panelu nástrojů záhlaví webové stránky](./media/getting-started-build-a-classifier/train01.png)

Proces školení by měl trvat jen několik minut. Během této doby se na kartě **výkon** zobrazí informace o procesu školení.

![Okno prohlížeče s výukovým dialogem v hlavní části](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>Vyhodnotit klasifikátor

Po dokončení školení bude výkon modelu odhadnut a zobrazen. Custom Vision Service používá bitové kopie, které jste odeslali pro školení, aby bylo možné vypočítat přesnost a odvolání pomocí procesu nazývaného [křížové ověření k skládání](https://en.wikipedia.org/wiki/Cross-validation_(statistics)). Přesnost a odvolání jsou dvě odlišná měření efektivity klasifikátoru:

- **Přesnost** označuje zlomek identifikovaných klasifikací, které byly správné. Například pokud model identifikoval 100 obrázků jako psi a 99 z nich byly ve skutečnosti psi, pak přesnost by byla 99%.
- **Odvolání** indikuje zlomek skutečných klasifikací, které byly správně identifikovány. Pokud by například existovaly skutečné 100 bitové kopie jablek a model identifikoval 80 jako jablk, odvolání by bylo 80%.

![Ve výsledcích školení se zobrazuje celková přesnost a odvolání a přesnost a odvolání jednotlivých značek v třídění.](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>Probability threshold

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Spravovat iterace cvičení

Pokaždé, když roznaučíte klasifikátor, vytvoříte novou _iteraci_ s vlastní aktualizovanou metrikou výkonu. Všechny své iterace můžete zobrazit v levém podokně karty **výkon** . Také najdete tlačítko **Odstranit** , které můžete použít k odstranění iterace, pokud je zastaralá. Odstraněním iterace odstraníte všechny bitové kopie, které s ní jsou jednoznačně přidružené.

Informace o tom, jak programově přistupovat k vašim vyškolených modelům, najdete v tématu [použití modelu s prediktivním rozhraním API](./use-prediction-api.md) .

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit a naučit model klasifikace obrázků pomocí Custom Vision webu. Dále Získejte další informace o iterativním procesu zlepšení modelu.

> [!div class="nextstepaction"]
> [Testování a přetrénování modelu](test-your-model.md)

* [Co je Custom Vision?](./overview.md)