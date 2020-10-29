---
title: 'Kurz: středně náročný obrázek produktů elektronického obchodování – Content Moderator'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu se dozvíte, jak nastavit aplikaci pro analýzu a klasifikaci imagí produktu pomocí zadaných popisků (použití Azure Počítačové zpracování obrazu a Custom Vision). Označte nežádoucí obrázky, které se budou dále kontrolovat (pomocí Azure Content Moderator).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 10/23/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 6d105528404c99f7273687fcdea6972b4212fcf1
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913683"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Kurz: středně náročné image produktů elektronického obchodování s využitím Azure Content Moderator

V tomto kurzu se naučíte, jak používat Azure Cognitive Services, včetně Content Moderator, ke klasifikaci a mírnému používání imagí produktu pro scénář elektronického obchodování. Pomocí Počítačové zpracování obrazu a Custom Vision použijete značky (štítky) na obrázky a pak vytvoříte tým, který kombinuje Content Moderator technologie založené na strojovém učení s týmy pro personálního přezkoumání, aby poskytovala inteligentní systém moderování.

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Zaregistrujte se Content Moderator a vytvořte si tým pro revize.
> * Vyhledání případného nevhodného obsahu a obsahu pro dospělé pomocí rozhraní API Content Moderatoru pro obrázky
> * Použijte službu Počítačové zpracování obrazu k vyhledávání obsahu celebrit (nebo jiných značek zjistitelných v počítači).
> * Pomocí služby Custom Vision můžete vyhledat přítomnost příznaků, hraček a per (nebo jiných vlastních značek).
> * Prezentovat kombinované výsledky kontroly pro lidskou kontrolu a konečné rozhodování.

Kompletní vzorový kód je k dispozici v [ukázkách úložiště moderování katalogu elektronického obchodování](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) na GitHubu.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/cognitive-services/), ještě než začnete.

## <a name="prerequisites"></a>Předpoklady

- Klíč předplatného Content Moderatoru. Podle pokynů v části [Vytvoření účtu Cognitive Services](../cognitive-services-apis-create-account.md) se přihlaste k odběru služby Content moderator a Získejte svůj klíč.
- Počítačové zpracování obrazu klíč předplatného (stejné pokyny jako výše).
- Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/).
- Sada imagí pro každý popisek, který bude Custom Vision klasifikátoru používat (v tomto případě se jedná o hračky, pera a značky v angličtině).

## <a name="create-a-review-team"></a>Vytvořit tým pro revize

Pokyny, jak se zaregistrovat do [Nástroje pro kontrolu Content moderator](https://contentmoderator.cognitive.microsoft.com/) a vytvořit tým pro kontrolu, najdete v [Content Moderatorm](quick-start.md) rychlém startu na webu. Poznamenejte si hodnotu **ID týmu** na stránce **přihlašovací údaje** .

## <a name="create-custom-moderation-tags"></a>Vytváření vlastních značek moderování

Dále vytvořte vlastní značky v nástroji pro revizi (viz článek [značky](./review-tool-user-guide/configure.md#tags) , pokud potřebujete s tímto procesem pomáhat). V tomto případě přidáme následující značky: **celebrit** , **USA** , **příznak** , **hračky** a **pero** . Ne všechny značky musí být zjistitelné kategorie v Počítačové zpracování obrazu (například **celebrit** ); vlastní značky můžete přidat, pokud Custom Vision třídění, abyste je mohli později zjistit.

![Konfigurace vlastních značek](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Vytvoření projektu v sadě Visual Studio

1. V aplikaci Visual Studio otevřete dialogové okno Nový projekt. Rozbalte položku **nainstalované** a pak **Visual C#** a pak vyberte **Konzolová aplikace (.NET Framework)** .
1. Pojmenujte aplikaci **EcommerceModeration** a pak klikněte na **OK** .
1. Pokud přidáváte tento projekt do existujícího řešení, vyberte tento projekt jako jeden spouštěný projekt.

V tomto kurzu se zvýrazní kód, který je centrální pro projekt, ale nepokrývá každý řádek kódu. Zkopírujte celý obsah _program.cs_ z ukázkového projektu ( [Samples elektronického obchodování pro moderování katalogu](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) do souboru _program.cs_ vašeho nového projektu. Potom si Projděte následující části, kde se dozvíte, jak projekt funguje a jak ho použít sami.

## <a name="define-api-keys-and-endpoints"></a>Definování klíčů a koncových bodů rozhraní API

V tomto kurzu se používá tři služby pro rozpoznávání; proto vyžaduje tři odpovídající klíče a koncové body rozhraní API. Podívejte se na následující pole ve třídě **program** :

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

Budete muset aktualizovat `___Key` pole hodnotami vašich klíčů předplatného a musíte změnit `___Uri` pole na správné adresy URL koncového bodu (později získáte Custom Vision klíč a koncový bod). Tyto hodnoty najdete na kartách **rychlý Start** každého prostředku Azure. Vyplňte `YOURTEAMID` část `ReviewUri` pole ID týmu revize, který jste vytvořili dříve. Poslední část pole vyplníte `CustomVisionUri` později.

[!INCLUDE [subdomains note](../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="primary-method-calls"></a>Volání primárních metod

Podívejte se na následující kód v metodě **Main** , který projde seznamem adres URL obrázků. Analyzuje jednotlivé obrázky se třemi různými službami, zaznamenává použité značky do pole **ReviewTags** a pak vytvoří kontrolu pro lidské moderování odesláním imagí do nástroje pro kontrolu Content moderator. Tyto metody budete zkoumat v následujících částech. Pokud chcete, můžete určit, které obrázky budou odesílány ke kontrole, pomocí pole **ReviewTags** v podmíněném příkazu pro kontrolu, které značky byly použity.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>Metoda EvaluateAdultRacy

Podívejte se na metodu **EvaluateAdultRacy** ve třídě **program** . Tato metoda přebírá adresu URL obrázku a pole párů klíč-hodnota jako parametry. Volá rozhraní API pro Content Moderator Image (pomocí REST) k získání výsledků pro dospělé a Pikantníi obrázku. Pokud je skóre pro jednu z hodnot větší než 0,4 (rozsah je mezi 0 a 1), nastaví odpovídající hodnotu v poli **ReviewTags** na **hodnotu true** .

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecomputervisiontags-method"></a>Metoda EvaluateComputerVisionTags

Další metoda přebírá adresu URL obrázku a informace o Počítačové zpracování obrazu předplatném a analyzuje obrázek pro přítomnost celebrit. Pokud se najde aspoň jeden celebrit, nastaví se odpovídající hodnota v poli **ReviewTags** na **true** .

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>Metoda EvaluateCustomVisionTags

Dále si prohlédněte metodu **EvaluateCustomVisionTags** , která klasifikuje skutečné produkty &mdash; v tomto případě příznaky, hračky a pera. Postupujte podle pokynů v tématu [Postup vytvoření klasifikátoru](../custom-vision-service/getting-started-build-a-classifier.md) pro sestavování vlastního klasifikátoru imagí a rozpoznávání příznaků, hraček a per (nebo bez ohledu na to, co jste zvolili jako vlastní značky) na obrázcích. K rychlému učení některých kategorií v tomto příkladu můžete použít image ve složce **Sample-images** v [úložišti GitHub](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) .

![Custom Vision webové stránky s školicími snímky pro pera, hračky a příznaky](images/tutorial-ecommerce-custom-vision.PNG)

Jakmile provedete klasifikátor, Získejte adresu URL koncového bodu a předpovědi předpovědi (viz [získat adresu URL a klíč předpovědi](../custom-vision-service/use-prediction-api.md#get-the-url-and-prediction-key) , pokud potřebujete pomoc s jejich načtením) a přiřadit tyto hodnoty k `CustomVisionKey` polím a v `CustomVisionUri` uvedeném pořadí. Metoda používá tyto hodnoty k dotazování klasifikátoru. Pokud klasifikátor najde jednu nebo více vlastních značek v obrázku, tato metoda nastaví odpovídající hodnoty v poli **ReviewTags** na **hodnotu true** .

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Vytvořit recenze pro nástroj pro kontrolu

V předchozích částech jste prozkoumali, jak aplikace kontroluje příchozí obrázky pro dospělé a pikantní obsah (Content Moderator), celebrit (Počítačové zpracování obrazu) a různé další objekty (Custom Vision). Dále si přečtěte část metoda **CreateReview** , která nahraje obrázky se všemi použitými značkami (předaných jako _metadata_ ) do nástroje pro kontrolu Content moderator.

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

Obrázky se zobrazí na kartě Revize [Nástroje pro kontrolu Content moderator](https://contentmoderator.cognitive.microsoft.com/).

![Snímek obrazovky nástroje pro revizi Content Moderator s několika obrázky a jejich zvýrazněnými značkami](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Odeslat seznam imagí testu

Jak vidíte v metodě **Main** , tento program hledá adresář "C:test" se souborem _Urls.txt_ , který obsahuje seznam adres URL obrázků. Vytvořte tento soubor a adresář, nebo změňte cestu tak, aby odkazovala na textový soubor. Pak tento soubor naplňte pomocí adres URL imagí, které chcete testovat.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>Spuštění programu

Pokud jste postupovali podle všech výše uvedených kroků, program by měl zpracovat každý obrázek (dotazování na všechny tři služby pro příslušné značky) a potom obrázky s informacemi o značkách nahrát do nástroje pro kontrolu Content Moderator.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nastavili program pro analýzu imagí produktů, označíte je podle typu produktu a umožníte týmu kontroly, aby se o moderování obsahu rozhodli. V dalším kroku se dozvíte více o podrobnostech o moderování imagí.

> [!div class="nextstepaction"]
> [Kontrola moderovaných imagí](./review-tool-user-guide/review-moderated-images.md)