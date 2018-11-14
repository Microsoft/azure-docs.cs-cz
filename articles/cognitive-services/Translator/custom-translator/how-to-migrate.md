---
title: Migrace pracovního prostoru Microsoft Translator Hub a projekty? – Vlastní Translator
titleSuffix: Azure Cognitive Services
description: Migrace do vlastní Translator váš pracovní prostor centra a projekty.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 378baad0735238dc0921e5e78e2a27b3ae907e19
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51627417"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Migrace na Translator vlastní pracovní prostor centra a projekty

Můžete migrovat vaše [Microsoft Translator Hub](https://hub.microsofttranslator.com/) pracovní prostor a projekty, které mají vlastní překladač. Migrace začíná od rozbočovače.


Během procesu jsou migrovány tyto položky:

1.  Projekty definic.

2.  Definice školení se použije k vytvoření nové definice modelu na vlastní překladač.

3.  Všechny paralelní aplikace a jeden jazyk soubory používané v rámci školení se migrují jako nové dokumenty ve vlastní překladač.

4.  Testovací systém automaticky generované a optimalizaci dat bude exportovat a vytvořit jako nové dokumenty ve vlastní Translator.

Pro všechny nasazené školení bude vlastní Translator trénování modelu bez žádné poplatky. Máte možnost ručně je nasadit.

>[!Note]
>Školení na úspěšné, vlastní Translator vyžaduje minimální 10 000 extrahované věty. Pro menší počet extrahované věty než [navrhované minimální](sentence-alignment.md#suggested-minimum-number-of-extracted-and-aligned-sentences), Translator vlastní nelze provést školení.

Pro všechny úspěšné školení, které nejsou nasazeni, se budou migrovat jako koncept v Translatoru Custom.

## <a name="find-custom-translator-workspace-id"></a>Najít vlastní Translator Id pracovního prostoru

K migraci [Microsoft Translator Hub](https://hub.microsofttranslator.com/) pracovního prostoru budete potřebovat cílové Id pracovního prostoru v Translatoru Custom. Cílový pracovní prostor v vlastní Translator je, kde Centrum pracovních prostorů a projektů se dají migrovat do.

Cílové Id pracovního prostoru na stránce nastavení Translator vlastní najdete: 

1.  Přejděte na stránku "Nastavení" portálu Translatoru Custom.

2.  Id pracovního prostoru najdete v části základní údaje.

    ![Jak najít ID cílového pracovního prostoru](media/how-to/how-to-find-destination-ws-id.png)

3. Ponechte cíl Id pracovního prostoru odkazovat během procesu migrace.

## <a name="migrate-workspace"></a>Migrace pracovního prostoru

Při migraci pracovního prostoru kompletní centra translator vlastní projekty, dokumenty a školení migrují do vlastní překladač. Před migrací budete muset zvolit, pokud chcete migrovat pouze nasazené školení nebo chcete migrovat všechny úspěšné školení.

K migraci pracovního prostoru:

1.  Přihlaste se k Microsoft Translatoru rozbočovače.

2.  Přejděte na stránku "Nastavení".

3.  Na stránce "Nastavení" Klepnutím na tlačítko "Pracovního prostoru migrovat data Translator vlastní".

    ![Jak migrovat z centra](media/how-to/how-to-migrate-workspace-from-hub.png)

4.  Na další stránce vyberte jednu z těchto dvou možností:

    a.  Pouze nasazení školení: Výběrem této možnosti bude migrovat pouze nasazené systémy a související dokumenty.

    b.  Všechny úspěšné školení: Tato volba provede migraci úspěšné školení a související dokumenty.

    c.  Zadejte vlastní Translator cíl ID pracovního prostoru.

    ![Jak migrovat z centra](media/how-to/how-to-migrate-from-hub-screen.png)

5.  Klikněte na tlačítko Odeslat žádost.

## <a name="migrate-project"></a>Migrovat projekt

Pokud chcete selektivně migraci vašich projektů, Microsoft Translator Hub vám tuto možnost.

Migrace projektu:

1.  Přihlaste se k Microsoft Translatoru rozbočovače.

2.  Přejděte na stránku "Projekty".

3.  Klikněte na možnost "Přenést" odkaz na příslušný projekt.

    ![Jak migrovat z centra](media/how-to/how-to-migrate-from-hub.png)

4.  Na další stránce vyberte jednu z těchto dvou možností:

    a.  Pouze nasazení školení: Výběrem této možnosti bude migrovat pouze nasazené systémy a související dokumenty. 

    b.  Všechny úspěšné školení: Tato volba provede migraci úspěšné školení a související dokumenty.

    c.  Zadejte vlastní Translator cíl ID pracovního prostoru.

    ![Jak migrovat z centra](media/how-to/how-to-migrate-from-hub-screen.png)

5.  Klikněte na tlačítko "Odeslat žádost o".

## <a name="migration-history"></a>Migrace historie

Při požadovaný pracovní prostor / projekt migrace z centra, najdete na stránce nastavení Translator vlastní historii migrace. 

Chcete-li zobrazit historii migrace, postupujte podle těchto kroků:

1.  Přejděte na stránku "Nastavení" portálu Translatoru Custom.

2.  V části migrace historie na stránce nastavení klikněte na historii migrace.

    ![Migrace historie](media/how-to/how-to-migration-history.png)

Migrace historie stránce se zobrazí následující informace jako souhrn pro každou migraci, o kterou žádáte.

1.  Migrované podle: Jméno a e-mailu uživatele odeslání této žádosti o migraci

2.  Migrovat na: Datum a časové razítko migrace

3.  Projekty: Počet projektů požadovaná pro migraci v/s počtu projektů úspěšné migraci.

4.  Školení: Počet školení požadovaná pro migraci v/s počet školení úspěšné migraci.

5.  Dokumenty: Počet dokumentů požadovaná pro migraci v/s počtem dokumentů úspěšné migraci.

    ![Podrobnosti o migraci historie](media/how-to/how-to-migration-history-details.png)

Pokud chcete podrobnější sestavu migrace o projekty, školení a dokumenty, máte možnost Exportovat podrobnosti jako sdílený svazek clusteru.

>[!Note]
>Migrace se podporuje jenom pro dvojice jazyků, pokud existují NMT jazyky. Zkontrolujte seznam aktuálně [podporované jazyky NMT](https://www.microsoft.com/translator/business/languages/). Pro dvojice jazyků, kde NMT jazyce neexistuje data se přesunou z centra pro vlastní Translator, ale nemohou být provedeny školení zaměřených na tyto dvojice jazyků.

## <a name="next-steps"></a>Další postup

- [Trénování modelu](how-to-train-model.md).
- Začněte používat váš model nasazený vlastní překlad prostřednictvím [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).