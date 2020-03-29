---
title: Zobrazit výsledky systémových testů a nasazení – vlastní překladač
titleSuffix: Azure Cognitive Services
description: Když je vaše školení úspěšné, zkontrolujte testy systému a analyzujte výsledky školení. Pokud jste spokojeni s výsledky školení, umístěte požadavek na nasazení pro trénovaný model.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f5e1a21f24fdd2458d4803d6ed1675455fa5037d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595596"
---
# <a name="view-system-test-results"></a>Zobrazení výsledků systémových testů

Když je vaše školení úspěšné, zkontrolujte testy systému a analyzujte výsledky školení. Pokud jste spokojeni s výsledky školení, umístěte požadavek na nasazení pro trénovaný model.

## <a name="system-test-results-page"></a>Stránka s výsledky systémových testů

Vyberte projekt, pak vyberte kartu Modely tohoto projektu, vyhledejte model, který chcete použít, a nakonec vyberte testovací kartu.

Na kartě test se zobrazí:

1.  **Výsledky systémových testů:** Výsledek zkušebního procesu v tréninkech. Testovací proces vytváří skóre BLEU.

    **Počet vět:** Kolik paralelnívěty byly použity v testovací sadě.

     **SKÓRE BLEU:** SKÓRE BLEU generované pro model po dokončení školení.

    **Stav:** Označuje, zda je proces testu dokončen nebo probíhá.

    ![Výsledky systémových zkoušek](media/how-to/how-to-system-test-results.png)

2.  Klikněte na výsledky systémových testů, a to vás bude trvat, než otestujete stránku s podrobnostmi o výsledcích. Tato stránka zobrazuje strojový překlad vět, které byly součástí testovací datové sady.

3.  Tabulka na stránce podrobností o výsledku testu má dva sloupce - jeden pro každý jazyk v páru. Sloupec pro zdrojový jazyk zobrazuje větu, která má být přeložena. Sloupec pro cílový jazyk obsahuje dvě věty v každém řádku.

    **Ref:** Tato věta je referenční překlad zdrojové věty, jak je uvedeno v testovací datové sadě.

    **MT:** Tato věta je automatický překlad zdrojové věty provedené modelem postavený po školení byla provedena.

    ![Porovnání výsledků systémových testů](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>Test ke stažení

Kliknutím na odkaz Stáhnout překlady stáhněte soubor zip. Zip obsahuje strojové překlady zdrojových vět v testovací datové sadě.

![Test ke stažení](media/how-to/how-to-system-test-download.png)

Tento stažený zip archiv obsahuje tři soubory.

1.  **custom.mt.txt:** Tento soubor obsahuje strojové překlady vět zdrojového jazyka v cílovém jazyce provedeném modelem trénovaným s daty uživatele.

2.  **ref.txt:** Tento soubor obsahuje uživatelem poskytnuté překlady vět zdrojového jazyka v cílovém jazyce.

3.  **source.txt:** Tento soubor obsahuje věty ve zdrojovém jazyce.

    ![Výsledky stažených systémových testů](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Nasazení modelu

Chcete-li požádat o nasazení:

1.  Vyberte projekt, přejděte na kartu Modely.

2. Pro úspěšně trénovaný model se zobrazí tlačítko "Nasadit", pokud není nasazeno.

    ![Nasazení modelu](media/how-to/how-to-deploy-model.png)

3.  Klikněte na Nasadit.
4.  Vyberte **Nasazeno** pro oblasti, do kterých chcete model nasadit, a klikněte na Uložit. Můžete vybrat **Nasazeno** pro více oblastí.

    ![Nasazení modelu](media/how-to/how-to-deploy-model-regions.png)

5.  Stav modelu můžete zobrazit ve sloupci Stav.

>[!Note]
>Vlastní překladač podporuje 10 nasazených modelů v rámci pracovního prostoru v libovolném okamžiku.

## <a name="update-deployment-settings"></a>Aktualizovat nastavení nasazení

Aktualizace nastavení nasazení:

1.  Vyberte projekt a přejděte na kartu **Modely.**

2. U úspěšně nasazeného modelu se zobrazí tlačítko **Aktualizovat.**

    ![Nasazení modelu](media/how-to/how-to-update-undeploy-model.png)

3.  Vyberte **Aktualizovat**.
4.  Vyberte **Nasazené** nebo **Nenasazené** pro oblasti, kde chcete model nasadit nebo zrušit nasazení, klepněte na tlačítko **Uložit**.

    ![Nasazení modelu](media/how-to/how-to-undeploy-model.png)

>[!Note]
>Pokud vyberete **undeployed** pro všechny oblasti, model je undeployed ze všech oblastí a dát do nenasazeného stavu. Nyní není k dispozici pro použití.

## <a name="next-steps"></a>Další kroky

- Začněte používat nasazený vlastní model překladu prostřednictvím [rozhraní Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
- Přečtěte [si, jak spravovat nastavení](how-to-manage-settings.md) pro sdílení pracovního prostoru, spravovat klíč předplatného.
- [Zjistěte, jak migrovat pracovní prostor a projekt](how-to-migrate.md) z centra Microsoft Translator [Hub](https://hub.microsofttranslator.com)
