---
title: Zobrazení výsledků a nasazení systémových testů – vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Po úspěšném školení si přečtěte systémové testy, které vám umožní analyzovat výsledky školení. Pokud jste spokojeni s výsledky školení, umístěte žádost o nasazení na školicí model.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 542f62f04adc7d4858e4b4621b0ae85e906434ad
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369320"
---
# <a name="view-system-test-results"></a>Zobrazení výsledků systémových testů

Po úspěšném školení si přečtěte systémové testy, které vám umožní analyzovat výsledky školení. Pokud jste spokojeni s výsledky školení, umístěte žádost o nasazení na školicí model.

## <a name="system-test-results-page"></a>Stránka s výsledky testu systému

Vyberte projekt, pak vyberte kartu modely daného projektu, vyhledejte model, který chcete použít, a nakonec vyberte kartu test.

Karta test vám ukáže:

1.  **Výsledky testů systému:** Výsledek testovacího procesu ve školicích kurzech. Proces testu generuje BLEU skóre.

    **Počet vět:** Kolik paralelních vět bylo použito v sadě testů.

     **Bleu skóre:** BLEU skóre vygenerované pro model po dokončení školení.

    **Stav:** Určuje, zda je proces testu dokončen nebo probíhá.

    ![Výsledky systémových testů](media/how-to/how-to-system-test-results.png)

2.  Klikněte na výsledky testu systému, které vás převedou na stránku s podrobnostmi výsledků testů. Tato stránka zobrazuje strojový překlad vět, které byly součástí testovací sady dat.

3.  Tabulka na stránce s podrobnostmi výsledků testů má dva sloupce – jeden pro každý jazyk ve dvojici. Sloupec pro zdrojový jazyk zobrazuje větu, která se má přeložit. Sloupec pro cílový jazyk obsahuje dvě věty v každém řádku.

    **Odkaz:** Tato věta je referenčním převodem zdrojové věty, jak je uvedeno v testovací sadě dat.

    **MT:** Tato věta je automatický překlad zdrojové věty provedené v modelu sestaveném po provedení školení.

    ![Porovnání výsledků testů systému](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>Stáhnout test

Kliknutím na odkaz Stáhnout překlady Stáhněte soubor zip. PSČ obsahuje strojové překlady zdrojových vět v sadě testů dat.

![Stáhnout test](media/how-to/how-to-system-test-download.png)

Stažený archiv zip obsahuje tři soubory.

1.  **custom.mt.txt:** Tento soubor obsahuje překlady strojových vět zdrojové jazykové verze v cílovém jazyce, který provede model vyškolený daty uživatele.

2.  **ref.txt:** Tento soubor obsahuje překlady uživatelsky zadaných překladů zdrojových vět v cílovém jazyce.

3.  **source.txt:** Tento soubor obsahuje ve zdrojovém jazyce věty.

    ![Výsledky staženého systému testů](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Nasazení modelu

Požadavek na nasazení:

1.  Vyberte projekt, přejít na kartu modely.

2. V případě úspěšného školení modelu zobrazuje tlačítko "nasadit", pokud není nasazeno.

    ![Snímek obrazovky, který zvýrazní tlačítko nasadit pro nasazení modelu.](media/how-to/how-to-deploy-model.png)

3.  Klikněte na nasadit.
4.  Vyberte **nasazené** pro oblasti, kde chcete model nasadit, a klikněte na Uložit. Můžete vybrat možnost **nasazeno** pro více oblastí.

    ![Snímek obrazovky, který ukazuje, kde můžete model nasadit nebo zrušit jeho nasazení.](media/how-to/how-to-deploy-model-regions.png)

5.  Stav modelu můžete zobrazit ve sloupci "stav".

>[!Note]
>Vlastní Překladatel podporuje v libovolném bodě v čase 10 nasazených modelů v rámci pracovního prostoru.

## <a name="update-deployment-settings"></a>Nastavení nasazení aktualizace

Aktualizace nastavení nasazení:

1.  Vyberte projekt a přejít na kartu **modely** .

2. V případě úspěšného nasazení modelu se zobrazí tlačítko **aktualizovat** .

    ![Snímek obrazovky, který zvýrazní tlačítko aktualizace pro nastavení aktualizace nasazení.](media/how-to/how-to-update-undeploy-model.png)

3.  Vyberte **Aktualizovat**.
4.  Vyberte **nasazené** nebo **nenasazené pro oblasti** , kde chcete, aby byl model nasazený nebo nenasazený, a pak klikněte na **Uložit**.

    ![Nasadit model](media/how-to/how-to-undeploy-model.png)

>[!Note]
>Pokud vyberete možnost **nenasazeno** pro všechny oblasti, model se odsadí ze všech oblastí a přepne do nenasazeného stavu. Není nyní k dispozici pro použití.

## <a name="next-steps"></a>Další kroky

- Začněte používat nasazený vlastní model překladu přes [Microsoft Translator text API V3](../reference/v3-0-translate.md?tabs=curl).
- Naučte se [Spravovat nastavení](how-to-manage-settings.md) , abyste mohli sdílet svůj pracovní prostor, spravovat klíč předplatného.
- Naučte [se migrovat pracovní prostor a projekt](how-to-migrate.md) z [centra Microsoft Translator](https://hub.microsofttranslator.com) .