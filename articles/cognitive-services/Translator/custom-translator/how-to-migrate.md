---
title: Migrovat pracovní prostor a projekty centra Microsoft Translator Hub? - Vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Tento článek vysvětluje, jak migrovat pracovní prostor centra a projekty do vlastního překladače služby Azure Cognitive Services.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 2fa90a8099778bf37ce8534e968a2b1b4345c2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75446783"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Migrace pracovního prostoru centra a projektů do vlastního překladače

Pracovní prostor [centra Microsoft Translator Hub](https://hub.microsofttranslator.com/) a projekty můžete snadno migrovat do vlastního překladače. Migrace je zahájena z Microsoft Hubu výběrem pracovního prostoru nebo projektu, výběrem pracovního prostoru v aplikaci Custom Translator a výběrem školení, která chcete přenést. Po zahájení migrace budou vybraná nastavení školení přenesena se všemi příslušnými dokumenty. Nasazené modely jsou trénované a lze automaticky nasadit po dokončení.

Tyto akce se provádějí během migrace:
* Všechny dokumenty a definice projektu budou mít své názvy převedeny s přidáním "hub_" předponou názvu. Automaticky generovaná data testů a optimalizací budou pojmenována hub_systemtune_\<> modelovaných nebo hub_systemtest_\<modelových>.
* Všechna školení, která byla v nasazeném stavu, když probíhá migrace, budou automaticky trénována pomocí dokumentů školení centra. Toto školení se nebude účtovat z vašeho předplatného. Pokud bylo pro migraci vybráno automatické nasazení, bude trénovaný model nasazen po dokončení. Budou účtovány pravidelné poplatky za hostování.
* Všechna migrovaná školení, která nebyla v nasazeném stavu, budou převedena do migrovaného stavu konceptu. V tomto stavu budete mít možnost školení modelu s migrované definice, ale pravidelné školení poplatky budou platit.
* V každém okamžiku skóre BLEU migrované z školení hubu lze nalézt na stránce TrainingDetails modelu v záhlaví "Bleu skóre v MT Hub".

> [!Note] 
> Pro školení uspět, vlastní překladač vyžaduje minimálně 10 000 jedinečných extrahovaných vět. Vlastní překladač nemůže provádět školení s méně než [navrhované minimum](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment#suggested-minimum-number-of-sentences).

## <a name="find-custom-translator-workspace-id"></a>Najít ID vlastního překladače pracovního prostoru

Chcete-li migrovat pracovní prostor [centra Microsoft Translator Hub,](https://hub.microsofttranslator.com/) potřebujete cílové ID pracovního prostoru ve vlastním překladači. Cílový pracovní prostor v aplikaci Custom Translator je místo, kam se migrují všechny pracovní prostory a projekty centra.

Cílové ID pracovního prostoru najdete na stránce Vlastní nastavení překladače:

1. Přejděte na stránku "Nastavení" na portálu Vlastní překladač.

2. ID pracovního prostoru najdete v části Základní informace.

    ![Jak najít ID cílového pracovního prostoru](media/how-to/how-to-find-destination-ws-id.png)

3. Udržujte své cílové ID pracovního prostoru, které bude během procesu migrace odkazovat.

## <a name="migrate-a-project"></a>Migrace projektu

Pokud chcete migrovat své projekty selektivně, Microsoft Translator Hub vám tuto možnost.

Migrace projektu:

1. Přihlaste se do centra Microsoft Translator Hub.

2. Přejděte na stránku Projekty.

3. Klikněte na odkaz "Migrovat" pro příslušný projekt.

    ![Jak migrovat z centra](media/how-to/how-to-migrate-from-hub.png)

4. Po stisknutí odkazu pro migraci vám bude předložen formulář, který vám umožní:
   * Zadejte pracovní prostor, do který chcete přenést na vlastní překladač
   * Uveďte, zda chcete převést všechna školení s úspěšnými školeními nebo pouze naváděnými školeními. Ve výchozím nastavení budou převedena všechna úspěšná školení.
   * Označte, zda chcete, aby se trénink automaticky nasazoval po dokončení školení. Ve výchozím nastavení nebude vaše školení automaticky nasazeno po dokončení.

5. Klikněte na "Odeslat žádost".

## <a name="migrate-a-workspace"></a>Migrace pracovního prostoru

Kromě migrace jednoho projektu můžete také migrovat všechny projekty s úspěšnými školeními v pracovním prostoru. To způsobí, že každý projekt v pracovním prostoru, které mají být vyhodnoceny, jako by bylo stisknuto odkaz migrace. Tato funkce je vhodná pro uživatele s mnoha projekty, kteří chtějí migrovat všechny z nich na vlastní překladač se stejným nastavením. Migraci pracovního prostoru lze zahájit ze stránky nastavení centra Překladač.

Migrace pracovního prostoru:

1. Přihlaste se do centra Microsoft Translator Hub.

2. Přejděte na stránku Nastavení.

3. Na stránce "Nastavení" klikněte na "Migrovat data pracovního prostoru do vlastního překladače".

    ![Jak migrovat z centra](media/how-to/how-to-migrate-workspace-from-hub.png)

4. Na další stránce vyberte jednu z těchto dvou možností:

    a. Pouze nasazená školení: Výběrem této možnosti se budou migrovat pouze nasazené systémy a související dokumenty.

    b. Všechna úspěšná školení: Výběrem této možnosti migrujete všechna úspěšná školení a související dokumenty.

    c. Do vlastního překladače zadejte ID cílového pracovního prostoru.

    ![Jak migrovat z centra](media/how-to/how-to-migrate-from-hub-screen.png)

5. Klikněte na Odeslat žádost.

## <a name="migration-history"></a>Historie migrace

Když jste požádali o migraci pracovního prostoru/ projektu z centra, najdete historii migrace na stránce Vlastní nastavení překladače.

Chcete-li zobrazit historii migrace, postupujte takto:

1. Přejděte na stránku "Nastavení" na portálu Vlastní překladač.

2. V části Historie migrace na stránce Nastavení klikněte na Historie migrace.

    ![Historie migrace](media/how-to/how-to-migration-history.png)

Stránka Historie migrace zobrazuje následující informace jako souhrn pro každou požadovanou migraci.

1. Migrovat podle: Jméno a e-mail uživatele odeslal tento požadavek na migraci

2. Migrace zapnuto: Datum a časové razítko migrace

3. Projekty: Počet projektů požadovaných pro migraci v/s počet úspěšně migrovaných projektů.

4. Školení: Počet školení požadovaných pro migraci v/s počet úspěšně migrovaných školení.

5. Dokumenty: Počet dokumentů požadovaných pro migraci v/s počet úspěšně přenesených dokumentů.

    ![Podrobnosti historie migrace](media/how-to/how-to-migration-history-details.png)

Pokud chcete podrobnější zprávu o migraci o vašich projektech, školeních a dokumentech, máte možnost exportovat podrobnosti jako CSV.

## <a name="implementation-notes"></a>Poznámky k provádění
* Systémy s páry jazyků, které ještě nejsou k dispozici ve vlastním překladači, budou k dispozici pouze pro přístup k datům nebo pro zrušení nasazení prostřednictvím vlastního překladače. Tyto projekty budou na stránce Projekty označeny jako "Nedostupné". Jak jsme se povolit nové jazykové dvojice s vlastní překladač, projekty se stanou aktivní trénovat a nasazovat. 
* Migrace projektu z centra do vlastního překladače nebude mít žádný vliv na vaše centra školení nebo projekty. Během migrace neodstraňujeme projekty ani dokumenty z centra a nerušíme nasazení modelů.
* Je povoleno migrovat pouze jednou na projekt. Pokud potřebujete migraci na projekt zopakovat, kontaktujte nás.
* Custom Translator podporuje jazykové páry NMT do a z angličtiny. [Prohlédněte si úplný seznam podporovaných jazyků](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization). Rozbočovač nevyžaduje základní modely, a proto podporuje několik tisíc jazyků. Můžete migrovat nepodporovaný jazykový pár, ale budeme provádět pouze migraci dokumentů a definic projektů. Nebudeme schopni trénovat nový model. Kromě toho tyto dokumenty a projekty budou zobrazeny jako neaktivní, aby bylo zřejmé, že je v tuto chvíli nelze použít. Pokud je přidána podpora pro tyto projekty nebo dokumenty, stanou se aktivními a trénovatelnými.
* Vlastní překladač v současné době nepodporuje jednojazyčná trénovací data. Podobně jako nepodporované jazykové páry můžete migrovat jednojazyčné dokumenty, ale zobrazují se jako neaktivní, dokud nejsou podporována jednojazyčná data.
* Vlastní Překladač vyžaduje 10k paralelní věty, aby se trénování. Služba Microsoft Hub může trénovat na menší sadě dat. Pokud je migrováno školení, které nesplňuje tento požadavek, nebude vyškoleno.

## <a name="custom-translator-versus-hub"></a>Vlastní překladač versus rozbočovač

Tato tabulka porovnává funkce mezi microsoft translator hub a vlastní překladač.

|   | Rozbočovač | Custom Translator |
|:-----|:----:|:----:|
|Stav funkce přizpůsobení   | Obecná dostupnost  | Obecná dostupnost |
| Verze textového rozhraní API  | V2    | V3  |
| Přizpůsobení SMT | Ano   | Ne |
| Přizpůsobení NMT | Ne    | Ano |
| Nové jednotné přizpůsobení služeb rozpoznávání řeči | Ne    | Ano |
| Bez trasování | Ano | Ano |

## <a name="new-languages"></a>Nové jazyky

Pokud jste komunita nebo organizace, která pracuje na vytvoření nového [custommt@microsoft.com](mailto:custommt@microsoft.com) jazykového systému pro microsoft translator, oslovte další informace.

## <a name="next-steps"></a>Další kroky

- [Trénování modelu](how-to-train-model.md).
- Začněte používat nasazený vlastní model překladu prostřednictvím [rozhraní Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
