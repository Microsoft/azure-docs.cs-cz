---
title: Zobrazení výsledků testu v systému a nasazení – vlastní Translator
titleSuffix: Azure Cognitive Services
description: Po úspěšném trénování, zkontrolujte systémové testy k analýze výsledky školení. Pokud jste spokojeni s výsledky školení, umístěte žádost o nasazení pro trénovaného modelu.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 61d3869559d88e14c0b9a3c3e23cd8a1f9c2b6c4
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51627323"
---
# <a name="view-system-test-results"></a>Zobrazení výsledků testu v systému

Po úspěšném trénování, zkontrolujte systémové testy k analýze výsledky školení. Pokud jste spokojeni s výsledky školení, umístěte žádost o nasazení pro trénovaného modelu. 

## <a name="system-test-results-page"></a>Stránka s výsledky testu systému

Vyberte projekt a pak vyberte kartu modely tohoto projektu, vyhledejte model, který chcete použít a nakonec vyberte kartu test.

Karta testu se dozvíte:

1.  Výsledky testů systému: Výsledek pro testovací proces v školení. Testovací proces vytvoří BLEU skóre.

    **Počet větu:** kolik paralelní věty byly použity v testovací sadě.

     **Skóre BLEU:** BLEU skóre generovaný pro model po dokončení školení.

    **Stav:** označuje, zda byl dokončen proces testu nebo v průběhu.

    ![Výsledky testů systému](media/how-to/how-to-system-test-results.png)

2.  Klikněte na výsledky testů systému a, který se dostanete na stránce s podrobnostmi o výsledek testu. Tato stránka zobrazuje obsah přeložený počítačem vět, které byly součástí datové sady testů.

3.  Na stránce s podrobnostmi výsledků testu v tabulce obsahuje dva sloupce – jeden pro každý jazyk v páru. Ve sloupci pro zdrojový jazyk zobrazí věty k převodu. Sloupec pro cílový jazyk obsahuje dvě věty v jednotlivých řádcích.

    **REF:** tuto větu je odkaz překladu věty zdroje, jak je uvedeno v datové sadě testů.

    **MT:** tuto větu je automatický překlad věty zdroj provádí model sestavený, po které školení.

    ![Porovnání výsledků testů systému](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>Stáhněte si test

Klikněte na odkaz Stáhnout překlady se stáhnout soubor zip. Zip obsahuje strojových překladů vět zdroje v datové sadě testů.

![Stáhněte si test](media/how-to/how-to-system-test-download.png)

Tento archiv zip staženého obsahuje tři soubory.

1.  Custom.mt.txt: Tento soubor obsahuje strojových překladů vět Zdrojový jazyk v cílovém jazyce provádí model natrénovaný pomocí údajů uživatele.

2.  REF.txt: Tento soubor obsahuje uživatelem zadané překlady vět Zdrojový jazyk v cílovém jazyce.

3.  zdroj.txt: Tento soubor obsahuje vět Zdrojový jazyk.

    ![Výsledky testu stažené systému](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Nasazení modelu

Požádat o nasazení:

1.  Vyberte projekt, přejděte na kartu modely.

2. Pro úspěšně trénovaného modelu se zobrazí tlačítko "Nasazení", pokud nejsou nasazené.

    ![Nasazení modelu](media/how-to/how-to-deploy-model.png)

3.  Klikněte na nasadit.
4.  Vyberte oblasti, kde chcete model nasadit a klikněte na Uložit. Můžete vybrat víc oblastí.

    ![Nasazení modelu](media/how-to/how-to-deploy-model-regions.png)

5.  Stav modelu se zobrazí ve sloupci "Stavu".

>[!Note]
>Pokud model je již nasazen, zobrazí se vám na tlačítko "Undeploy" pro tento model. Chcete-li zrušení model, klikněte na tlačítko "Undeploy".

## <a name="next-steps"></a>Další postup

- Začněte používat váš model nasazený vlastní překlad prostřednictvím [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
- Přečtěte si [jak spravovat nastavení](how-to-manage-settings.md) sdílení pracovního prostoru, správě klíč předplatného.
- Přečtěte si [migrace váš pracovní prostor a projekt](how-to-migrate.md) z [centra Microsoft Translator](https://hub.microsofttranslator.com)