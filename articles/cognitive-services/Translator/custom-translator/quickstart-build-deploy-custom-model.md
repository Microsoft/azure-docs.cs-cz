---
title: 'Úvodní příručka: Sestavení, nasazení a použití vlastního modelu – vlastní překladač'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu procházíte krok za krokem procesem vytváření překladatelského systému pomocí vlastního překladače.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: swmachan
ms.topic: quickstart
ms.openlocfilehash: a22ae3b4b11a4eac7da147cf3ad896bbad435448
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370159"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>Úvodní příručka: Sestavení, nasazení a použití vlastního modelu pro překlad

Tento článek obsahuje podrobné pokyny k vytvoření překladového systému pomocí vlastního překladače.

## <a name="prerequisites"></a>Požadavky

1. K použití [portálu vlastnípřekladace](https://portal.customtranslator.azure.ai) budete k přihlášení potřebovat [účet Microsoft](https://signup.live.com) nebo účet Azure [AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (účet organizace hostovaný v Azure).

2. Předplatné rozhraní Translator Text API prostřednictvím portálu Azure. Budete potřebovat klíč předplatného překladače text api pro přidružit k pracovnímu prostoru v vlastní překladač. Podívejte [se, jak se zaregistrovat do rozhraní Translator Text API](https://docs.microsoft.com/azure/cognitive-services/translator/translator-text-how-to-signup).

3. Pokud máte obě výše uvedené, přihlaste se k portálu [Vlastní překladač](https://portal.customtranslator.azure.ai) a vytvořte pracovní prostory, projekty, nahrávejte soubory a vytvářejte nebo nasazujte modely.

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Pokud jste prvním uživatelem, budete požádáni o souhlas s podmínkami služby, vytvoření pracovního prostoru a přidružení pracovního prostoru k předplatnému rozhraní Microsoft Translator Text API.

![](media/quickstart/terms-of-service.png)
![Vytvoření pracovního prostoru](media/quickstart/create-workspace-1.png)
![Vytvoření](media/quickstart/create-workspace-2.png)
![pracovního](media/quickstart/create-workspace-3.png)
![prostoru Vytvoření](media/quickstart/create-workspace-4.png)
![pracovního](media/quickstart/create-workspace-5.png)
![prostoru Vytvoření pracovního prostoru Vytvoření pracovního prostoru Vytvoření pracovního prostoru Vytvoření pracovního prostoru](media/quickstart/create-workspace-6.png)

Při dalších návštěvách portálu Vlastní překladač přejděte na stránku Nastavení, kde můžete spravovat pracovní prostor, vytvořit více pracovních prostorů, přidružit klíč předplatného microsoft translatorového textového rozhraní k vašim pracovním prostorům, přidat spoluvlastníky a změnit předplatného.

## <a name="create-a-project"></a>Vytvoření projektu

Na vstupní stránce portálu Vlastní překladač klikněte na Nový projekt. V dialogovém okně můžete zadat požadovaný název projektu, dvojici jazyků a kategorii a další relevantní pole. Potom uložte projekt. Další podrobnosti naleznete na stránce [Vytvořit projekt](how-to-create-project.md).

![Vytvoření projektu](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>Uložení dokumentů

Dále nahrajte [školení](training-and-model.md#training-document-type-for-custom-translator), [ladění](training-and-model.md#tuning-document-type-for-custom-translator) a [testování](training-and-model.md#testing-dataset-for-custom-translator) sad dokumentů. Můžete nahrát [paralelní](what-are-parallel-documents.md) i kombinované dokumenty. Můžete také nahrát [slovník](what-is-dictionary.md).

Dokumenty můžete nahrát buď z karty Dokumenty, nebo ze stránky konkrétního projektu.

![Uložení dokumentů](media/quickstart/ct-how-to-upload.png)

Při nahrávání dokumentů zvolte typ dokumentu (školení, ladění nebo testování) a dvojici jazyků. Při nahrávání paralelních dokumentů budete muset zadat název dokumentu. Další podrobnosti naleznete na [stránce Nahrát dokument](how-to-upload-document.md).

## <a name="create-a-model"></a>Vytvoření modelu

Při nahrání všech požadovaných dokumentů je dalším krokem sestavení modelu.

Vyberte projekt, který jste vytvořili. Zobrazí se všechny dokumenty, které jste nahráli a které sdílejí pár jazyků s tímto projektem. Vyberte dokumenty, které chcete zahrnout do modelu. Můžete vybrat [školení](training-and-model.md#training-document-type-for-custom-translator), [ladění](training-and-model.md#tuning-document-type-for-custom-translator)a [testování](training-and-model.md#testing-dataset-for-custom-translator) dat nebo vybrat pouze trénovací data a nechat vlastní překladač automaticky vytvářet tuning a testovací sady pro váš model.

![Vytvoření modelu](media/quickstart/ct-how-to-train.png)

Po dokončení výběru požadovaných dokumentů klikněte na tlačítko Vytvořit model, abyste vytvořili model a zahájili školení. Stav školení a podrobnosti o všech modelech, které jste trénovali, najdete na kartě Modely.

Další podrobnosti naleznete na stránce [Vytvoření modelu](how-to-train-model.md).

## <a name="analyze-your-model"></a>Analýza modelu

Po úspěšném dokončení školení zkontrolujte výsledky. Skóre BLEU je jedna metrika, která označuje kvalitu vašeho překladu. Můžete také ručně porovnat překlady provedené s vlastním modelem s překlady uvedenými v testovací sadě tak, že přejdete na kartu "Test" a kliknete na "Systémové výsledky". Ruční kontrola několika z těchto překladů vám dá dobrou představu o kvalitě překladu vyrobeného vaším systémem. Další podrobnosti naleznete na stránce [Výsledky systémových testů](how-to-view-system-test-results.md).

## <a name="deploy-a-trained-model"></a>Nasazení trénovaného modelu

Až budete připraveni nasadit trénovaný model, klikněte na tlačítko "Nasadit". Můžete mít jeden nasazený model na projekt a můžete zobrazit stav nasazení ve sloupci Stav. Další podrobnosti naleznete na webu [Nasazení modelu](how-to-view-system-test-results.md#deploy-a-model)

![Nasazení trénovaného modelu](media/quickstart/ct-how-to-deploy.png)

## <a name="use-a-deployed-model"></a>Použití nasazeného modelu

K nasazeným modelům lze přistupovat prostřednictvím rozhraní Microsoft Translator [Text API V3 zadáním id kategorie](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl). Další informace o rozhraní Translator Text API naleznete na webové stránce [REFERENCE rozhraní API.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak se orientovat v [pracovním prostoru Vlastní překladač a spravovat projekty](workspace-and-project.md).
