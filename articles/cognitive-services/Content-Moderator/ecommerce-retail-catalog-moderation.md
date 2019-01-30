---
title: 'Kurz: Moderování obrázků produktů elektronického obchodování – Content Moderator'
titlesuffix: Azure Cognitive Services
description: Nastavit aplikaci pro analýzu a klasifikaci obrázků s zadaný popisky (s použitím Azure pro počítačové zpracování obrazu a Custom Vision) a označení nevhodných obrázků dělit ještě zkontrolovali (pomocí Azure Content Moderator).
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 833aa9caed3d1fd5d39a0c15e9fc03ad32091834
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218899"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Kurz: Obrázky produktů střední e-commerce s Azure Content Moderator

V tomto kurzu se dozvíte, jak efektivně klasifikaci pomocí služby Azure cognitive services, včetně Content Moderator a obrázků produktů střední scénář elektronického obchodování. Budete používat pro počítačové zpracování obrazu a Custom Vision použít různé značky (popisky) na bitové kopie, a pak vytvoříte recenze team, která kombinuje Content Moderator na základě machine learningu technologie s recenze prováděné lidmi týmů k poskytování inteligentní moderování systém.

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Zaregistrujte si Content Moderator a vytvořte tým kontroly.
> * Vyhledání případného nevhodného obsahu a obsahu pro dospělé pomocí rozhraní API Content Moderatoru pro obrázky
> * Pomocí služby pro počítačové zpracování obrazu můžete spustit kontrolu obsahu celebrit (nebo jiné značky pro zpracování obrazu – zjistitelné pro počítače).
> * Použijte službu Custom Vision service kontrolovat přítomnost příznaky, toys a pera (nebo jiných vlastních značek).
> * K dispozici výsledky kombinované kontroly pro recenze prováděné lidmi a finální rozhodování.

Úplný ukázkový kód je k dispozici v [ukázky moderování katalogů elektronického obchodování](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) úložišti na Githubu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

- Klíč předplatného Content Moderatoru. Postupujte podle pokynů v [vytvoření účtu služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) k odběru služby Content Moderator a získejte klíč.
- Klíč předplatného pro počítačové zpracování obrazu (stejné pokyny, jak je uvedeno výše).
- Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/).
- Sadu bitových kopií pro každý popisek, který bude používat třídění vlastní zpracování obrazu (v tomto případu toys, pera a nám příznaky).

## <a name="create-a-review-team"></a>Vytvořit tým kontroly

Odkazovat [Seznamte se s Content Moderatorem](quick-start.md) rychlý start pro pokyny, jak zaregistrovat [Content Moderator Zkontrolujte nástroj](https://contentmoderator.cognitive.microsoft.com/) a vytvořte tým kontroly. Poznamenejte si **ID týmu** hodnoty na **pověření** stránky.

## <a name="create-custom-moderation-tags"></a>Vytvořit vlastní moderování značky

Potom vytvořte vlastní značky v nástroj pro recenze (odkazovat [značky](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) článek, pokud potřebujete pomoc s tímto procesem). V tomto případě přidáme následující značky: **celebrit**, **USA**, **příznak**, **hračka**, a **pera**. Všimněte si, že všechny uvedené značky musí být zjistitelná kategorií v počítačové zpracování obrazu (jako je **celebrit**); vlastní značky můžete přidat tak dlouho, dokud trénování třídění Custom Vision vyhledáním později.

![Konfigurace vlastních značek](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Vytvoření projektu v sadě Visual Studio

1. V sadě Visual Studio otevřete dialogové okno Nový projekt. Rozbalte **nainstalováno**, pak **Visual C#** a pak vyberte **Konzolová aplikace (.NET Framework)**.
1. Pojmenujte aplikaci **EcommerceModeration**, pak klikněte na tlačítko **OK**.
1. Pokud tento projekt přidáváte do existujícího řešení, vyberte tento projekt jako jeden spouštěný projekt.

V tomto kurzu se zaměřuje na kód, který je centrální do projektu, ale nebudeme se zabývat každý jednotlivý řádek kódu, které jsou potřeba. Zkopírujte úplný obsah _Program.cs_ z ukázkového projektu ([ukázky moderování katalogů elektronického obchodování](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) do _Program.cs_ souboru nového projektu. Potom krok v následujících částech Další informace o fungování projekt a jak ji používat sami.

## <a name="define-api-keys-and-endpoints"></a>Definujte klíče rozhraní API a koncových bodů

Jak je uvedeno výše, tento kurz používá tři služby cognitive services; Proto je vyžadováno tři odpovídající klíče a koncových bodů rozhraní API. Zobrazit následující pole v **Program** třídy: 

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

Budete muset aktualizovat `___Key` pole s hodnotami klíče předplatného (zobrazí se `CustomVisionKey` později), a může být nutné změnit `___Uri` pole tak, že obsahují správné oblasti identifikátory. Vyplňte `YOURTEAMID` součástí `ReviewUri` pole ID tým kontroly, který jste vytvořili dříve. Vyplní poslední část `CustomVisionUri` pole později.

## <a name="primary-method-calls"></a>Primární způsob volání

Viz následující kód **hlavní** metodu, která prochází seznam adres URL obrázků. Analyzuje každá image s třemi různými službami, zaznamenává značky použité v **ReviewTags** pole a vytvoří kontrolu pro lidské moderátory (odešle Image do nástroje Content Moderator zkontrolujte). Prozkoumáte tyto metody v následujících částech. Všimněte si, že sem, pokud chcete, můžete řídit imagí, které jsou odesílány ke kontrole, pomocí **ReviewTags** pole v podmíněném příkazu ke kontrole, značky, které byly použity.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>EvaluateAdultRacy – metoda

Najdete v článku **EvaluateAdultRacy** metoda ve **Program** třídy. Tato metoda přebírá adresu URL obrázku a pole párů klíč hodnota jako parametry. Volá rozhraní API Content Moderatoru obrázků (pomocí rozhraní REST) Chcete-li získat skóre Adult a Racy bitové kopie. Pokud skóre pro buď je větší než 0.4 (rozsah je od 0 do 1), nastaví odpovídající hodnotu v **ReviewTags** pole k **True**.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecustomvisiontags-method"></a>EvaluateCustomVisionTags – metoda

Další metoda přebírá adresu URL obrázku a informace o předplatném pro počítačové zpracování obrazu a analyzuje bitovou kopii na přítomnost celebrit. Pokud jeden nebo více celebrit nenajde, nastaví odpovídající hodnotu v **ReviewTags** pole k **True**. 

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>EvaluateCustomVisionTags – metoda

V dalším kroku najdete v článku **EvaluateCustomVisionTags** metodu, která klasifikuje skutečných produktů&mdash;v tomto případě příznaky toys a pera. Postupujte podle pokynů [sestavení klasifikátoru](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) průvodce k vytvoření vlastní image klasifikátor k detekci přítomnosti příznaky, toys a pera (nebo cokoli, co jste zvolili jako vlastní značky) na obrázcích.

![Vlastní webovou stránku pro zpracování obrazu pomocí pera, toys a příznaky trénovacích obrázků](images/tutorial-ecommerce-custom-vision.PNG)

Po klasifikátoru mají školení, získat klíč předpovědi a adresu URL koncového bodu predikcí (naleznete v tématu [získání adresy URL a předpovědi klíče](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key) Pokud potřebujete pomoc, jejich načítání) a přiřadit tyto hodnoty na vaše `CustomVisionKey` a `CustomVisionUri` pole , v uvedeném pořadí. Metoda tyto hodnoty používá k dotazování třídění. Pokud třídění vyhledá jeden nebo více vlastních značek na obrázku, tato metoda nastaví odpovídající hodnoty v **ReviewTags** pole k **True**. 

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Vytvoření kontroly pro nástroj pro recenze

V předchozích částech jste si poznamenali metody, které prohledávají příchozí obrázky a hledají obsah pro dospělé nebo nevhodný obsah (Content Moderator), celebrity (Computer Vision) a různé další objekty (Custom Vision). V dalším kroku uvidíte metodu **CreateReview**, která nahrává obrázky se všemi na ně použitými značkami (předané jako _Metadata_ ) do nástroje Content Moderator, aby byly k dispozici pro kontroly prováděné lidmi. 

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

Image se zobrazí na kartě kontrolu [nástroj pro kontrolu Content Moderator](https://contentmoderator.cognitive.microsoft.com/).

![Snímek obrazovky nástroje Content Moderator revize s více bitových kopií a jejich zvýrazněné značky](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Seznam imagí testovací odeslání

Jak je vidět v **hlavní** metoda, tento program hledá "C:Test" adresář se _Urls.txt_ soubor, který obsahuje seznam adres URL obrázků. Vytvoření těchto souborů a adresářů, nebo změňte cestu na textový soubor a do tohoto souboru s adresami URL z imagí, které chcete testovat.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>Spuštění programu

Pokud jste provedli všechny výše uvedené kroky, program by měl zpracování jednotlivých obrázků (dotaz na všechny tři služby pro jejich odpovídající značky) a pak nahrajte obrázky s informacemi o značky na nástroj pro kontrolu Content Moderator.

## <a name="next-steps"></a>Další postup

V tomto kurzu nastavíte programu k analýze obrázků produktů pro účely označování podle typu produktu a povolení tým kontroly se informovaně rozhodnout o moderování obsahu. Další informace o podrobnosti o moderování obrázků v dalším kroku.

> [!div class="nextstepaction"]
> [Recenze moderování obrázků](./review-tool-user-guide/review-moderated-images.md)
