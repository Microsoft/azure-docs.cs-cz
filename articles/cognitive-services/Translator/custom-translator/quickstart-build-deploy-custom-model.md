---
title: 'Rychlý Start: sestavení, nasazení a použití vlastního modelu – vlastní Překladatel'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu projdete podrobný postup sestavení systému překladu pomocí vlastního překladatele.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: swmachan
ms.topic: quickstart
ms.openlocfilehash: bb4e3be621e83ed66748e3eba683b983dbac98d7
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90528519"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>Rychlý Start: sestavení, nasazení a použití vlastního modelu pro překlad

Tento článek poskytuje podrobné pokyny k sestavení překladu systému pomocí vlastního překladatele.

## <a name="prerequisites"></a>Požadavky

1. Pokud chcete použít [vlastní portál překladatelů](https://portal.customtranslator.azure.ai) , budete pro přihlášení potřebovat účet [účet Microsoft](https://signup.live.com) nebo [Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (hostující účet organizace v Azure).

2. Předplatné Translator Text API přes Azure Portal. Pro přidružení k pracovnímu prostoru ve vlastním překladateli budete potřebovat Translator Text API klíč předplatného. Podívejte se, [jak se zaregistrovat k Translator text API](https://docs.microsoft.com/azure/cognitive-services/translator/translator-text-how-to-signup).

3. Když máte obě výše uvedené, přihlaste se k portálu  [vlastního překladatele](https://portal.customtranslator.azure.ai) , abyste mohli vytvářet pracovní prostory, projekty, nahrávat soubory a vytvářet a nasazovat modely.

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Pokud jste uživatelem poprvé, budete vyzváni k vyjádření souhlasu s podmínkami služby, k vytvoření pracovního prostoru a k přidružení pracovního prostoru k předplatnému Microsoft Translator Text API.

![Vytvoření pracovního prostoru vytvořit pracovní prostor ](media/quickstart/terms-of-service.png)
 ![ Obrázek 1 ](media/quickstart/create-workspace-1.png)
 ![ vytvořit pracovní prostor obrázek 2 vytvořit pracovní prostor obrázek 3 vytvořit pracovní prostor obrázek ](media/quickstart/create-workspace-2.png)
 ![ ](media/quickstart/create-workspace-3.png)
 ![ 4 ](media/quickstart/create-workspace-4.png)
 ![ vytvořit pracovní prostor obrázek 5 ](media/quickstart/create-workspace-5.png)
 ![ vytvořit obrázek v pracovním prostoru 6](media/quickstart/create-workspace-6.png)

Při následné návštěvě vlastního portálu překladatelů přejděte na stránku nastavení, kde můžete spravovat pracovní prostor, vytvořit další pracovní prostory, přidružit k vašemu pracovnímu prostoru klíč předplatného Microsoft Translator Text API, přidat spoluvlastníky a změnit klíč předplatného.

## <a name="create-a-project"></a>Vytvoření projektu

Na úvodní stránce portálu pro vlastní Překladatel klikněte na nový projekt. V dialogovém okně můžete zadat požadovaný název projektu, dvojici jazyků a kategorii a také další relevantní pole. Pak projekt uložte. Další podrobnosti najdete na stránce [Vytvoření projektu](how-to-create-project.md).

![Vytvoření projektu](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>Uložení dokumentů

Potom nahrajte [školení](training-and-model.md#training-document-type-for-custom-translator), [vyladění](training-and-model.md#tuning-document-type-for-custom-translator) a [testování](training-and-model.md#testing-dataset-for-custom-translator) sad dokumentů. Můžete nahrát [paralelní](what-are-parallel-documents.md) i kombinované dokumenty. Můžete také nahrát [slovník](what-is-dictionary.md).

Dokumenty můžete odeslat buď na kartě dokumenty, nebo na stránce konkrétního projektu.

![Uložení dokumentů](media/quickstart/ct-how-to-upload.png)

Při nahrávání dokumentů vyberte typ dokumentu (školení, ladění nebo testování) a dvojici jazyků. Při odesílání paralelních dokumentů budete muset zadat také název dokumentu. Další podrobnosti najdete na webu [odeslání dokumentu](how-to-upload-document.md).

## <a name="create-a-model"></a>Vytvoření modelu

Po nahrání všech požadovaných dokumentů je dalším krokem sestavení modelu.

Vyberte projekt, který jste vytvořili. Zobrazí se všechny dokumenty, které jste nahráli, a tento projekt sdílí dvojici jazyků. Vyberte dokumenty, které chcete zahrnout do modelu. Můžete vybrat [školení](training-and-model.md#training-document-type-for-custom-translator), [ladění](training-and-model.md#tuning-document-type-for-custom-translator)a [testování](training-and-model.md#testing-dataset-for-custom-translator) dat nebo vybrat pouze školicí data a umožnit vlastní překladateli automaticky sestavit optimalizace a sady testů pro váš model.

![Vytvoření modelu](media/quickstart/ct-how-to-train.png)

Po dokončení výběru požadovaných dokumentů klikněte na tlačítko vytvořit model a vytvořte svůj model a spusťte školení. Na kartě modely můžete zobrazit stav školení a podrobnosti o všech vámi vyškolených modelech.

Další podrobnosti najdete v [Vytvoření modelu](how-to-train-model.md).

## <a name="analyze-your-model"></a>Analýza modelu

Po úspěšném dokončení školení Zkontrolujte výsledky. BLEU skóre je jedna metrika, která označuje kvalitu vašeho překladu. Můžete také ručně porovnat překlady vytvořené s vlastním modelem s překlady uvedenými v sadě testů tak, že přejdete na kartu test a kliknete na možnost výsledky systému. Ruční kontrola několika z těchto překladů vám poskytne dobrou představu o kvalitě překladu, kterou váš systém vytvořil. Další podrobnosti najdete v [systému výsledky testů](how-to-view-system-test-results.md).

## <a name="deploy-a-trained-model"></a>Nasazení trained model

Až budete připraveni nasadit svůj vyškolený model, klikněte na tlačítko nasadit. Můžete mít jeden nasazený model na projekt a stav nasazení můžete zobrazit ve sloupci Stav. Další podrobnosti najdete v [nasazení modelu](how-to-view-system-test-results.md#deploy-a-model) .

![Nasazení trained model](media/quickstart/ct-how-to-deploy.png)

## <a name="swap-deployed-model"></a>Prohození nasazeného modelu

Chcete-li zaměnit nasazený model s jiným v rámci projektu, klikněte na tlačítko "prohodit" zobrazené vedle požadovaného modelu. Během procesu odkládacího souboru bude nasazený model i nadále k dispozici pro poskytování požadavků na překlad. 

![Prohození nasazeného modelu](media/quickstart/ct-how-to-swap-model.png)

## <a name="use-a-deployed-model"></a>Použití nasazeného modelu

K nasazeným modelům se dá dostat prostřednictvím [rozhraní Microsoft Translator text API V3 zadáním KódKategorie](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl). Další informace o Translator Text API najdete na webové stránce [referenčního rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) .

## <a name="next-steps"></a>Další kroky

- Naučte se procházet [vlastní pracovní prostor překladatelů a spravovat vaše projekty](workspace-and-project.md).
