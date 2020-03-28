---
title: 'Kurz: Moderování obrázků produktů elektronického obchodování - Content Moderator'
titleSuffix: Azure Cognitive Services
description: Tento kurz ukazuje, jak nastavit aplikaci pro analýzu a klasifikaci ibikopií produktů se zadanými popisky (pomocí Azure Computer Vision a Custom Vision). Označte nevhodné obrázky k dalšímu zkontrolování (pomocí Moderátora obsahu Azure).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 5e74eda9e30c536c0eba4e847019344c87e10cce
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774339"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Kurz: Moderování bitových kopií produktů elektronického obchodování s moderátorem obsahu Azure

V tomto kurzu se dozvíte, jak používat Azure Cognitive Services, včetně Content Moderator, klasifikovat a moderovat image produktů pro scénář elektronického obchodování. Pomocí funkce Počítačové vidění a Vlastní vize použijete značky (štítky) na obrázky a pak vytvoříte týmovou recenzi, která kombinuje technologie založené na strojovém učení content moderatora s týmy pro lidské recenze a poskytuje inteligentní systém moderování.

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Zaregistrujte se do obsahu Moderátor a vytvořte revizní tým.
> * Vyhledání případného nevhodného obsahu a obsahu pro dospělé pomocí rozhraní API Content Moderatoru pro obrázky
> * Pomocí služby Počítačové vidění můžete hledat obsah celebrit (nebo jiné značky zjistitelné z počítačového vidění).
> * Pomocí služby Vlastní vize můžete hledat příznaky, hračky a pera (nebo jiné vlastní značky).
> * Prezentujte kombinované výsledky skenování pro lidské přezkoumání a konečné rozhodování.

Kompletní ukázkový kód je k dispozici v ukázkách úložiště [moderování katalogu elektronického obchodu](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) na GitHubu.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

- Klíč předplatného Content Moderatoru. Podle pokynů v [tématu Vytvoření účtu služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) se přihlaste ke službě Content Moderator a získejte klíč.
- Klíč předplatného počítačového vidění (stejné pokyny jako výše).
- Libovolné vydání [Visual Studia 2015 nebo 2017](https://www.visualstudio.com/downloads/).
- Sada obrázků pro každý popisek, který bude používat klasifikátor vlastní vize (v tomto případě hračky, pera a příznaky USA).

## <a name="create-a-review-team"></a>Vytvoření revizního týmu

Pokyny k registraci [nástroje Revize moderátora obsahu](https://contentmoderator.cognitive.microsoft.com/) na webu naleznete v rychlém spuštění nástroje [Vyzkoušet obsah](quick-start.md) a vytvořit tým pro recenze. Poznamenejte si hodnotu **ID týmu** na stránce **Přihlašovací údaje.**

## <a name="create-custom-moderation-tags"></a>Vytvoření vlastních značek moderování

Dále vytvořte vlastní značky v nástroji Revize (viz článek [Značky,](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) pokud potřebujete pomoc s tímto procesem). V tomto případě přidáme následující značky: **celebrity**, **USA**, **vlajka**, **toy**, a **pero**. Ne všechny značky musí být zjistitelné kategorie v počítačovém vidění (jako **celebrity**); můžete přidat vlastní značky tak dlouho, dokud trénujete vlastní vision třídění k jejich detekci později.

![Konfigurace vlastních značek](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Vytvoření projektu v sadě Visual Studio

1. V sadě Visual Studio otevřete dialogové okno Nový projekt. Rozbalte **Možnost Nainstalované**, potom **visual c#** a pak vyberte **konzolovou aplikaci (.NET Framework).**
1. Pojmenujte aplikaci **EcommerceModeration**a klepněte na tlačítko **OK**.
1. Pokud přidáváte tento projekt do existujícího řešení, vyberte tento projekt jako jeden projekt po spuštění.

Tento kurz zvýrazní kód, který je ústředním bodem projektu, ale nebude pokrývat každý řádek kódu. Zkopírujte celý obsah _Program.cs_ z ukázkového projektu[(Ukázky moderování katalogu elektronického obchodu)](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)do _Program.cs_ souboru nového projektu. Potom projděte následující části, abyste se dozvěděli, jak projekt funguje a jak ho používat sami.

## <a name="define-api-keys-and-endpoints"></a>Definování klíčů rozhraní API a koncových bodů

Tento kurz používá tři kognitivní služby; proto vyžaduje tři odpovídající klíče a koncové body rozhraní API. Viz následující pole ve třídě **Program:**

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

Budete muset aktualizovat `___Key` pole s hodnotami vašich klíčů předplatného a `___Uri` budete muset změnit pole na správné adresy URL koncového bodu (později získáte klíč vlastní vize a koncový bod). Tyto hodnoty najdete na kartách **Rychlý start** každého prostředku Azure. Vyplňte `YOURTEAMID` část `ReviewUri` pole ID týmu recenzentů, který jste vytvořili dříve. Závěrečnou část `CustomVisionUri` pole vyplníte později.

[!INCLUDE [subdomains note](../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="primary-method-calls"></a>Primární volání metod

Podívejte se na následující kód v **Main** metoda, která smyčky prostřednictvím seznamu adres URL obrázků. Analyzuje každý obrázek se třemi různými službami, zaznamenává použité značky v poli **ReviewTags** a pak vytvoří recenzi pro lidské moderátory odesláním obrázků do nástroje content moderator review. Tyto metody prozkoumáte v následujících částech. Pokud chcete, můžete určit, které obrázky jsou odesílány ke kontrole, pomocí **reviewtags** pole v podmíněném příkazu ke kontrole, které značky byly použity.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>Metoda EvaluateAdultRacy

Viz **metoda EvaluateAdultRacy** ve třídě **Program.** Tato metoda má adresu URL obrázku a pole párů klíč-hodnota jako parametry. To volá Content Moderator Image API (pomocí REST) získat dospělé a Pikantní skóre obrazu. Pokud je skóre pro buď větší než 0,4 (rozsah je mezi 0 a 1), nastaví odpovídající hodnotu v **reviewtags** pole **True**.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecomputervisiontags-method"></a>Metoda EvaluateComputerVisionTags

Další metoda pořídí adresu URL obrázku a informace o předplatném počítačového vidění a analyzuje obrázek na přítomnost celebrit. Pokud je nalezena jedna nebo více celebrit, nastaví odpovídající hodnotu v poli **ReviewTags** na **True**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>Metoda EvaluateCustomVisionTags

Dále naleznete **EvaluateCustomVisionTags** metoda, která klasifikuje skutečné produkty&mdash;v tomto případě příznaky, hračky a pera. Postupujte podle pokynů v [návodu k vytvoření příručky třídění](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) k vytvoření vlastního klasifikátoru obrázků a detekci příznaků, hraček a per (nebo čehokoli, co jste zvolili jako vlastní značky) v obrázcích. Obrázky ve složce **ukázkových obrazů** [úložiště GitHub](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) můžete rychle trénovat některé kategorie v tomto příkladu.

![Webová stránka Custom Vision s trénovacími obrázky per, hraček a vlajek](images/tutorial-ecommerce-custom-vision.PNG)

Po trénování třídění, získat klíč předpověď a předpověď koncový bod URL (viz [Získání URL a predikční klíč,](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key) `CustomVisionKey` pokud `CustomVisionUri` potřebujete pomoc s jejich načítání) a přiřadit tyto hodnoty a pole, resp. Metoda používá tyto hodnoty k dotazování třídění. Pokud třídění najde jednu nebo více vlastních značek v obraze, tato metoda nastaví odpovídající hodnoty v poli **ReviewTags** na **True**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Vytvořit recenze pro nástroj revize

V předchozích částech jste zkoumali, jak aplikace skenuje příchozí obrázky pro dospělé a pikantní obsah (Content Moderator), celebrity (Computer Vision) a různé další objekty (Vlastní vize). Dále naleznete v metodě **CreateReview,** která obrázky nahraje se všemi použitými značkami (předanými jako _metadata)_ do nástroje pro kontrolu moderátora obsahu.

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

Obrázky se zobrazí na kartě Revize [nástroje Kontrola moderátora obsahu](https://contentmoderator.cognitive.microsoft.com/).

![Snímek obrazovky nástroje Content Moderator Review s několika obrázky a zvýrazněnými značkami](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Odeslání seznamu testovacích obrázků

Jak můžete vidět v **Hlavní** metodě, tento program hledá adresář "C:Test" se souborem _Urls.txt,_ který obsahuje seznam adres URL obrázků. Vytvořte tento soubor a adresář nebo změňte cestu tak, aby ukazovala na textový soubor. Poté naplňte tento soubor adresy URL obrázků, které chcete otestovat.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>Spuštění programu

Pokud jste postupovali podle všech výše uvedených kroků, měl by program zpracovat každý obrázek (dotazování všech tří služeb na příslušné značky) a poté nahrát obrázky s informacemi o značce do nástroje pro kontrolu moderátora obsahu.

## <a name="next-steps"></a>Další kroky

V tomto kurzu nastavíte program pro analýzu bitových kopií produktů, označení podle typu produktu a povolení týmu pro kontrolu činit informovaná rozhodnutí o moderování obsahu. Dále se dozvíte více o podrobnostech moderování obrazu.

> [!div class="nextstepaction"]
> [Projděte si moderované obrázky](./review-tool-user-guide/review-moderated-images.md)
