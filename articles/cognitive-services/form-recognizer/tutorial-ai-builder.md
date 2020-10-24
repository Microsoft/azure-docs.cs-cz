---
title: 'Kurz: Vytvoření aplikace pro zpracování formulářů pomocí Tvůrce AI – Nástroj pro rozpoznávání formulářů'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu použijete tvůrce AI k vytvoření a školení aplikace pro zpracování formuláře.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 10/23/2020
ms.author: pafarley
ms.openlocfilehash: 0261c495ad08000f9041390658e0f19954b71669
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517017"
---
# <a name="tutorial-create-a-form-processing-app-with-ai-builder"></a>Kurz: Vytvoření aplikace pro zpracování formulářů pomocí Tvůrce AI

Nástroj pro [sestavovatele AI](https://docs.microsoft.com/ai-builder/overview) je funkce Power Platform, která umožňuje automatizovat procesy a předpovídat výsledky pro zlepšení výkonu firmy. Pomocí zpracování formuláře pro sestavovatele AI můžete vytvořit modely AI, které identifikují a extrahují páry klíč-hodnota a tabulková data z dokumentů formuláře.

> [!NOTE]
> Tento projekt je také k dispozici jako [modul Microsoft Learn](https://docs.microsoft.com/learn/modules/get-started-with-form-processing/).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření modelu AI pro zpracování formulářů
> * Trénování modelu
> * Publikování modelu pro použití ve službě Azure Power apps nebo automatizace Power

## <a name="prerequisites"></a>Předpoklady

* Sada alespoň pěti forem stejného typu, která se má použít pro data školení a testování. Tipy a možnosti pro vložení sady školicích dat najdete v tématu [Vytvoření školicích dat](./build-training-data-set.md) . Pro účely tohoto rychlého startu můžete použít soubory ve složce **výuka** [ukázkové sady dat](https://go.microsoft.com/fwlink/?linkid=2128080).
* Power apps nebo Power automatizuje License – viz [Průvodce licencováním](https://go.microsoft.com/fwlink/?linkid=2085130). Licence musí zahrnovat [Common data Service](https://powerplatform.microsoft.com/en-us/common-data-service/).
* [Doplněk nebo zkušební verze](https://go.microsoft.com/fwlink/?LinkId=2113956&clcid=0x409)tvůrce AI.


## <a name="create-a-form-processing-project"></a>Vytvořit projekt zpracování formuláře

1. Přejít na [Power Apps](https://make.powerapps.com/) nebo [Power Automatizujte](https://flow.microsoft.com/signin)a přihlaste se pomocí účtu organizace.
1. V levém podokně vyberte sestavení pro **Tvůrce AI**  >  **Build**.
1. Vyberte kartu pro **zpracování formuláře** .
1. Zadejte název pro svůj model.
1. Vyberte **Vytvořit**.

## <a name="upload-and-analyze-documents"></a>Nahrání a analýza dokumentů

Na stránce **Přidat dokumenty** musíte poskytnout ukázkové dokumenty, které budou vyškolit model pro typ formuláře, ze kterého chcete extrahovat informace. Po nahrání dokumentů Nástroj pro sestavovatele AI je analyzuje, aby zkontroloval, jestli jsou dostatečné pro výuku modelu.

> [!NOTE]
> AI Builder v současnosti nepodporuje následující typy vstupních dat pro zpracování formulářů:
>
> - Komplexní tabulky (vnořené tabulky, složená záhlaví nebo buňky atd.)
> - Zaškrtávací políčka a přepínače
> - Dokumenty PDF delší než 50 stránek
> - Vyplnitelné soubory PDF
>
> Další informace o požadavcích na vstupní dokumenty najdete v tématu [požadavky na vstup](./overview.md#input-requirements).

### <a name="upload-your-documents"></a>Nahrání dokumentů

1. Vyberte **Přidat dokumenty**, vyberte aspoň pět dokumentů a pak vyberte **Nahrát**.
1. Po dokončení nahrávání vyberte **Zavřít**.
1. Pak vyberte **analyzovat**.

> [!NOTE] 
> Po nahrání dokumentů můžete některé z nich odebrat nebo nahrát další.

> [!div class="mx-imgBorder"]
> ![Přidat stránku dokumentů](./media/tutorial-ai-builder/add-documents-page.png)

### <a name="analyze-your-documents"></a>Analýza dokumentů

V průběhu analýzy projde tvůrce AI dokumenty, které jste nahráli, a zjistí pole a tabulky. Doba potřebná k dokončení této operace závisí na počtu poskytnutých dokumentů, ale ve většině případů může trvat jen několik minut.

Po dokončení analýzy vyberte miniaturu a otevřete tak možnosti výběru polí.

> [!IMPORTANT]
> Pokud se analýza nezdařila, je pravděpodobně způsobena tím, že tvůrce AI nedokázal v dokumentech najít strukturovaný text. Ověřte, že se aktualizované dokumenty řídí [požadavky na vstup](./overview.md#input-requirements).

## <a name="select-your-form-fields"></a>Vybrání polí formuláře

Na stránce Výběr polí vyberte pole, která vás zajímají:

1. Chcete-li vybrat pole, klikněte na obdélník, který označuje zjištěné pole v dokumentu, nebo vyberte více polí kliknutím a přetažením. Můžete také vybrat pole z podokna na pravé straně.
1. Klikněte na název vybraného pole, chcete-li jej přejmenovat, aby odpovídal vašim potřebám, nebo Normalizujte extrahované popisky.

    Po kliknutí na zjištěné pole se zobrazí následující informace:

    - **Název pole**: název popisku zjištěného pole.
    - **Hodnota pole**: hodnota zjištěného pole.

> [!div class="mx-imgBorder"]
> ![Stránka označování](./media/tutorial-ai-builder/select-fields-page.png)

### <a name="label-undetected-fields"></a>Nezjištěná pole popisku

Pokud pole, které chcete označit popiskem, nebylo automaticky zjištěno modelem, můžete kolem jeho hodnoty nakreslit obdélník a v zobrazeném dialogovém okně zadat název popisku.

## <a name="train-your-model"></a>Trénování modelu

1. Vyberte **Další**, abyste zkontrolovali vybraná pole formuláře. Pokud vše vypadá dobře, vyberte **Trénovat** k trénování modelu.

    > [!div class="mx-imgBorder"]
    > ![Stránka školení](./media/tutorial-ai-builder/summary-train-page.png)
1. Po dokončení trénování vyberte **Přejít na stránku s podrobnostmi** na obrazovce **Trénování dokončeno**.
## <a name="quick-test-your-model"></a>Rychlé otestování modelu

Stránka s podrobnostmi vám umožní otestovat model před jeho publikováním nebo použitím:

1. Na stránce s podrobnostmi vyberte **Rychlý test**.
2. Můžete buď přetáhnout dokument, nebo vybrat **Odeslat ze zařízení** a nahrát testovací soubor. Rychlý test by měl výsledky zobrazit během pár sekund.
3. Můžete vybrat možnost **začít znovu** a spustit další test nebo **Zavřít** , pokud jste hotovi.

### <a name="troubleshooting-tips"></a>Rady pro řešení potíží

Pokud pro určitá pole vyzkoušíte špatné výsledky nebo nízkou spolehlivost, zkuste následující tipy:

- Přeučení pomocí formulářů s různými hodnotami v každém poli.
- Přeučení s využitím větší sady školicích dokumentů. Další dokumenty, které označíte, se seznámí s tím, jak lépe rozpoznávat pole.
- Soubory PDF můžete optimalizovat tak, že vyberete jenom určité stránky, se kterými se naučíte. Pokud **Print**  >  chcete vybrat určité stránky v dokumentu, použijte možnost Tisk**Tisk do PDF** .

## <a name="publish-your-model"></a>Publikování modelu

Pokud máte s modelem spokojeni, vyberte **publikovat**  a publikujte ho. Po dokončení publikování se model povýší na **Publikovaný** a je připravený k použití.

> [!div class="mx-imgBorder"]
> ![Stránka publikovat model](./media/tutorial-ai-builder/model-page.png)

Po publikování modelu zpracování formuláře ho můžete použít v [aplikaci plátna Power Apps](https://docs.microsoft.com/ai-builder/form-processor-component-in-powerapps) nebo v [Power](https://docs.microsoft.com/ai-builder/form-processing-model-in-flow)automatu.

## <a name="next-steps"></a>Další kroky

Použijte dokumentaci Tvůrce AI pro použití modelu zpracování formulářů.

* [Použití komponenty Form-Processor v Power Apps](https://docs.microsoft.com/ai-builder/form-processor-component-in-powerapps)
* [Použití modelu zpracování formulářů v Power Automate](https://docs.microsoft.com/ai-builder/form-processing-model-in-flow)