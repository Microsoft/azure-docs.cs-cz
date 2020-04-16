---
title: Rychlý start Vytvoření třídění – vlastní služba Vision Service
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
ms.openlocfilehash: 56bdaa324420bf274e7cda8ac1c6506e4bc9ad21
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404060"
---
# <a name="quickstart-how-to-build-a-classifier-with-custom-vision"></a>Úvodní příručka: Jak vytvořit klasifikátor s vlastní vize

V tomto rychlém startu se dozvíte, jak vytvořit třídění prostřednictvím webu Custom Vision. Po vytvoření modelu třídění, můžete použít vlastní vize služby pro klasifikaci bitových obrázků.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

- Sada obrázků, s nimiž se trénování třídění. Tipy pro výběr obrázků najdete níže.

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Vytvoření prostředků vlastní vize na webu Azure Portal

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Vytvoření nového projektu

Ve webovém prohlížeči přejděte na [webovou stránku Vlastní vize](https://customvision.ai) a __vyberte Přihlásit se__. Přihlaste se pomocí stejného účtu, který jste použili k přihlášení na portál Azure.

![Obrázek přihlašovací stránky](./media/browser-home.png)


1. Chcete-li vytvořit první projekt, vyberte **nový projekt**. Zobrazí se dialogové okno **Vytvořit nový projekt.**

    ![Dialogové okno nový projekt obsahuje pole pro název, popis a domény.](./media/getting-started-build-a-classifier/new-project.png)

1. Zadejte název a popis projektu. Pak vyberte skupinu prostředků. Pokud je váš účet pro přihlášení přidružený k účtu Azure, rozbalovací seznam Skupiny prostředků zobrazí všechny vaše skupiny prostředků Azure, které obsahují prostředek služby Custom Vision Service. 

   > [!NOTE]
   > Pokud není k dispozici žádná skupina prostředků, potvrďte, že jste se přihlásili [k customvision.ai](https://customvision.ai) se stejným účtem, jako jste použili k přihlášení k [portálu Azure](https://portal.azure.com/). Potvrďte také, že jste vybrali stejný "adresář" na portálu vlastní vize jako adresář na portálu Azure, kde jsou umístěny vaše vlastní prostředky Vision. Na obou webech můžete vybrat svůj adresář z nabídky rozevíracího účtu v pravém horním rohu obrazovky. 

1. V části __Typy projektů__vyberte možnost __Klasifikace__ . Potom v části __Typy klasifikace__zvolte **buď Vícelabel** nebo **Vícetřídy**, v závislosti na případu použití. Klasifikace vícepopisek aplikuje libovolný počet značek na obrázek (nula nebo více), zatímco klasifikace více tříd seřadí obrázky do jednotlivých kategorií (každý obrázek, který odešlete, bude seřazen do nejpravděpodobnější značky). Pokud budete chtít, budete moci později změnit typ klasifikace.

1. Dále vyberte jednu z dostupných domén. Každá doména optimalizuje třídění pro určité typy bitových kopií, jak je popsáno v následující tabulce. Budete moci změnit doménu později, pokud si budete přát.

    |Domain (Doména)|Účel|
    |---|---|
    |__Obecné__| Optimalizováno pro širokou škálu úloh klasifikace obrázků. Pokud žádná z ostatních domén není vhodná nebo si nejste jisti, kterou doménu zvolit, vyberte obecnou doménu. |
    |__Potravin__|Optimalizováno pro fotografie jídel, jak byste je viděli v menu restaurace. Chcete-li klasifikovat fotografie jednotlivých druhů ovoce nebo zeleniny, použijte doménu Potraviny.|
    |__Památek__|Optimalizováno pro rozpoznatelné orientační body, přírodní i umělé. Tato doména funguje nejlépe, když je orientační bod jasně viditelný na fotografii. Tato doména funguje i v případě, že orientační bod je mírně blokován lidmi před ním.|
    |__Maloobchod__|Optimalizováno pro obrázky, které se nacházejí v nákupním katalogu nebo na webu nákupu. Pokud chcete vysoce přesné třídění mezi šaty, kalhoty a košile, použijte tuto doménu.|
    |__Kompaktní domény__| Optimalizováno pro omezení klasifikace v reálném čase na mobilních zařízeních. Modely generované kompaktními doménami lze exportovat pro místní spuštění.|

1. Nakonec vyberte __vytvořit projekt__.

## <a name="choose-training-images"></a>Výběr tréninkových obrázků

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Nahrávání a označování obrázků

V této části nahrajete a ručně označíte obrázky, které vám pomohou trénovat třídění. 

1. Chcete-li přidat obrázky, klepněte na tlačítko __Přidat obrázky__ a vyberte __procházet místní soubory__. Výběrem __možnosti Otevřít__ se přesuňte na tagování. Výběr značek se použije na celou skupinu obrázků, které jste vybrali k nahrání, takže je snazší nahrát obrázky v samostatných skupinách podle požadovaných značek. Značky pro jednotlivé obrázky můžete také změnit po jejich nahrání.

    ![Ovládací prvek Přidat obrázky se zobrazí v levém horním rohu a jako tlačítko v dolní části středu.](./media/getting-started-build-a-classifier/add-images01.png)


1. Chcete-li vytvořit značku, zadejte text do pole __Moje značky__ a stiskněte Enter. Pokud značka již existuje, zobrazí se v rozevírací nabídce. V projektu s více popisky můžete k obrázkům přidat více než jednu značku, ale v projektu s více třídami můžete přidat pouze jednu. Chcete-li dokončit nahrávání obrázků, použijte tlačítko __Nahrát soubory [číslo].__ 

    ![Obrázek stránky značky a nahrávání](./media/getting-started-build-a-classifier/add-images03.png)

1. Po nahrání obrázků vyberte __Hotovo.__

    ![Indikátor průběhu zobrazuje všechny dokončené úkoly.](./media/getting-started-build-a-classifier/add-images04.png)

Chcete-li nahrát další sadu obrázků, vraťte se do horní části této části a opakujte kroky.

## <a name="train-the-classifier"></a>Trénování klasifikátoru

Chcete-li klasifikátor trénovat, vyberte tlačítko **Vlak.** Třídění používá všechny aktuální obrázky k vytvoření modelu, který identifikuje vizuální vlastnosti každé značky.

![Tlačítko vlak v pravém horním rohu panelu nástrojů záhlaví webové stránky](./media/getting-started-build-a-classifier/train01.png)

Tréninkový proces by měl trvat jen několik minut. Během této doby se na kartě **Výkon** zobrazí informace o procesu školení.

![Okno prohlížeče s trénovacím dialogem v hlavní části](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>Vyhodnocení třídění

Po dokončení školení se výkon modelu odhadne a zobrazí. Služba Custom Vision Service používá obrázky, které jste odeslali k trénování, k výpočtu přesnosti a odvolání pomocí procesu nazývaného [křížové ověření k-fold](https://en.wikipedia.org/wiki/Cross-validation_(statistics)). Přesnost a zpození jsou dvě různá měření účinnosti klasifikátoru:

- **Přesnost** označuje zlomek identifikovaných klasifikací, které byly správné. Pokud by například model identifikoval 100 obrázků jako psy a 99 z nich byli ve skutečnosti psi, pak by přesnost byla 99%.
- **Odvolání** označuje zlomek skutečné klasifikace, které byly správně identifikovány. Například, pokud tam bylo skutečně 100 obrázků jablek, a model identifikovány 80 jako jablka, odvolání by bylo 80%.

![Výsledky školení ukazují celkovou přesnost a odvolání a přesnost a odvolání pro každou značku v klasifikátoru.](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>Prahová hodnota pravděpodobnosti

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Správa iterací školení

Pokaždé, když trénujete klasifikátor, vytvoříte novou _iteraci_ s vlastními aktualizovanými metrikami výkonu. Všechny iterace můžete zobrazit v levém podokně karty **Výkon.** Najdete zde také tlačítko **Odstranit,** které můžete použít k odstranění iterace, pokud je zastaralá. Když odstraníte iteraci, odstraníte všechny obrazy, které jsou s ní jednoznačně přidruženy.

Viz [Použití modelu s rozhraním API předpovědi,](./use-prediction-api.md) kde se dozvíte, jak programově přistupovat k trénovaným modelům.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit a trénovat model klasifikace obrázků pomocí webu Custom Vision. Dále získáte další informace o iterativním procesu zlepšování modelu.

> [!div class="nextstepaction"]
> [Testování a přetrénování modelu](test-your-model.md)

