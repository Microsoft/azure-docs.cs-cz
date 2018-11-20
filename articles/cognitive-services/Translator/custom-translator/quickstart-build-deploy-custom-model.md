---
title: 'Rychlý start: Vytvořit, nasadit a používat vlastní model – vlastní Translator'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu projděte si podrobný postup vytváření překladový systém použitím překladač vlastní.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: quickstart
ms.openlocfilehash: cb713ac53b399d8ba79d003ee6d948bb5af5220b
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2018
ms.locfileid: "51975768"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>Rychlý start: Vytvořit, nasadit a používat vlastní model pro překlad

Tento článek obsahuje podrobné pokyny k sestavení překladový systém s vlastní překlady.

## <a name="prerequisites"></a>Požadavky

1. Použít [vlastní Translator](https://portal.customtranslator.azure.ai) portál, musíte [účtu Microsoft](https://signup.live.com) nebo [účtu Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (účet organizace hostované v Azure) pro přihlášení.

2. Předplatné Translator Text API prostřednictvím webu Azure portal. Budete potřebovat klíč předplatné Translator Text API pro přidružení k pracovního prostoru v Translatoru Custom. Zobrazit [registrace pro rozhraní Translator Text API](https://docs.microsoft.com/azure/cognitive-services/translator/translator-text-how-to-signup).

3. Až budete mít obě z výše uvedeného, přihlaste se k [vlastní Translator](https://portal.customtranslator.azure.ai) portálu. Jednou na portálu pro vlastní Translator, přejděte na stránku nastavení, kde můžete přidružit váš klíč předplatného Microsoft Translator Text API pracovního prostoru.

## <a name="create-a-project"></a>Vytvořit projekt

Na portálu cílové stránce vlastní Translator klikněte na nový projekt. V dialogovém okně můžete zadat název požadovaného projektu, dvojici jazyků a kategorie, jakož i další příslušná pole. Uložte projekt. Další podrobnosti najdete na webu [vytvořit projekt](how-to-create-project.md).

![Vytvoření projektu](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>Nahrání dokumentů

V dalším kroku nahrát [školení](training-and-model.md#training-dataset-for-custom-translator), [ladění](training-and-model.md#tuning-dataset-for-custom-translator) a [testování](training-and-model.md#testing-dataset-for-custom-translator) sady dokumentů. Můžete nahrát obě [paralelní](what-are-parallel-documents.md) a dokumenty – pole se seznamem. Můžete také nahrát [slovníku](what-is-dictionary.md).

Můžou nahrávat dokumenty, na kartě dokumenty nebo ze stránky s konkrétním projektu.

![Nahrání dokumentů](media/quickstart/ct-how-to-upload.png)

Při nahrávání dokumentů, zvolte typ dokumentu (školení, ladění a testování) a pár jazyka. Při nahrávání paralelní dokumenty, musíte taky zadat název dokumentu. Další podrobnosti najdete na webu [nahrání dokumentu](how-to-upload-document.md).

## <a name="create-a-model"></a>Vytvoření modelu

Když se nahraje všechny požadované dokumenty dalším krokem je k sestavení modelu.

Vyberte projekt, který jste vytvořili. Zobrazí všechny dokumenty, které jste odeslali, které sdílejí pár jazyk s tímto projektem. Vyberte dokumenty, které chcete zahrnout do modelu. Můžete vybrat [školení](training-and-model.md#training-dataset-for-custom-translator), [ladění](training-and-model.md#tuning-dataset-for-custom-translator), a [testování](training-and-model.md#testing-dataset-for-custom-translator) data nebo vyberte jen Cvičná data a automaticky nechat vlastní překladač sestavení, ladění a testování sad pro váš model.

![Vytvoření modelu](media/quickstart/ct-how-to-train.png)

Jakmile budete hotovi, vyberte požadovanou dokumentů, klepněte na tlačítko Vytvořit Model pro vytvoření modelu a spuštění školení. Zobrazí se stav školení a podrobnosti pro všechny modely, které jsme natrénovali, na kartě modely.

Další podrobnosti najdete na webu [vytvořit Model](how-to-train-model.md).

## <a name="analyze-your-model"></a>Analyzovat model

Po úspěšném dokončení trénování zkontrolujte výsledky. Skóre BLEU je jedna metrika označující kvalitu vaší překladu. Můžete také ručně porovnat překlady vytvořené pomocí vlastního modelu pro překlady v testu nastavit tak, že přejdete na kartu "Test" a kliknutím na "Výsledky systému." Ruční kontrola několik tyto překlady získáte představu o kvalitu překladu vytvořeného v systému. Další podrobnosti najdete na webu [výsledky testů systému](how-to-view-system-test-results.md).

## <a name="deploy-a-trained-model"></a>Nasazení trénovaného modelu

Až budete připravení nasadit trénovaného modelu, klikněte na tlačítko "Nasazení". Může mít jeden nasazený model a projektů, a stav nasazení se zobrazí ve sloupci stav. Další podrobnosti najdete na webu [nasazení modelu](how-to-view-system-test-results.md#deploy-a-model)

![Nasazení trénovaného modelu](media/quickstart/ct-how-to-deploy.png)

## <a name="use-a-deployed-model"></a>Použití nasazeného modelu

Nasazených modelů dá dostat pomocí Microsoft Translatoru [Text API V3 zadáním CategoryID](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl). Další informace o rozhraní Translator Text API můžete najít na [Reference k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) webové stránky.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak se orientovat [Translator vlastní pracovní prostor a spravovat vaše projekty](workspace-and-project.md).
