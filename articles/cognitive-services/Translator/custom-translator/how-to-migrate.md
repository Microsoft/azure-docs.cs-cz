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
ms.openlocfilehash: a02be1ba29ad43b90c99a5d1e275b96b3b4023d6
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53718720"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Migrace na Translator vlastní pracovní prostor centra a projekty

Můžete snadno migrovat vaše [Microsoft Translator Hub](https://hub.microsofttranslator.com/) pracovní prostor a projekty, které mají vlastní překladač. Migrace je zahájeno z Microsoft Hub vyberete pracovní prostor nebo projektu, pak výběrem pracovního prostoru v Translator vlastní a pak vyberete školení, které chcete převést.  Po zahájení migrace nastavení vybrané školení bude převeden pomocí všechny související dokumenty.  Nasazených modelů jsou trénované a může být autodeployed po dokončení.

Během migrace se provádí tyto akce:
* Všechny dokumenty a definice projektu budou mít jejich názvy přenesená a uveďte "hub_" předponou názvu.  Automaticky generované testování a ladění data se budou pojmenovávat hub_systemtune_<modelid> nebo hub_systemtest_<modelid>.  
* Jakékoli školení, které byly v nasazeném stavu místo pořízením migrace bude automaticky trénuje použití dokumentů Centrum školení.  Toto školení nebude účtovat do vašeho předplatného.  Pokud automatické nasazení byla vybrána pro migraci se po dokončení nasadí trénovaného modelu. Aplikují se standardní poplatky za hostování.  
* Všechny migrované školení, které nebyly v nasazeném stavu zařadí do stavu migrovaných konceptu.   V tomto stavu budete mít možnost trénujete model pro migrované definicí, ale poplatky za regulární školení.
* V libovolném bodě skóre BLEU migrovat z centra školení najdete na stránce TrainingDetails modelu v "Bleu skóre v centru MT" záhlaví.

>[!Note]
>Školení na úspěšné, vlastní Translator vyžaduje minimální 10 000 extrahované věty. Pro menší počet extrahované věty než [navrhované minimální](sentence-alignment.md#suggested-minimum-number-of-extracted-and-aligned-sentences), Translator vlastní nelze provést školení.

## <a name="enable-account-migration"></a>Povolení migrace účtu 

Chcete-li použít nástroj pro migraci, budete muset mít vaše Centrum účtu migrace je povolená.  K tomuto účelu e-mailu [ custommt@microsoft.com ](mailto:custommt@microsoft.com) se seznamem všech liveid účtů, které byste chtěli povolena. Tyto účty by měl být e-mailové adresy, které jste přihlášení.

## <a name="find-custom-translator-workspace-id"></a>Najít vlastní Translator ID pracovního prostoru

K migraci [Microsoft Translator Hub](https://hub.microsofttranslator.com/) pracovního prostoru, budete potřebovat cílové ID pracovního prostoru v Translatoru Custom. Cílový pracovní prostor v vlastní Translator je, kde Centrum pracovních prostorů a projektů se dají migrovat do.

Cílové ID pracovního prostoru na stránce nastavení Translator vlastní najdete: 

1.  Přejděte na stránku "Nastavení" portálu Translatoru Custom.

2.  ID pracovního prostoru najdete v části základní údaje.

    ![Jak najít ID cílového pracovního prostoru](media/how-to/how-to-find-destination-ws-id.png)

3. Ponechte cíl ID pracovního prostoru odkazovat během procesu migrace.

## <a name="migrate-a-project"></a>Migrace projektu

Pokud chcete selektivně migraci vašich projektů, Microsoft Translator Hub vám tuto možnost.

Migrace projektu:

1.  Přihlaste se k Microsoft Translatoru rozbočovače.

2.  Přejděte na stránku "Projekty".

3.  Klikněte na možnost "Přenést" odkaz na příslušný projekt.

    ![Jak migrovat z centra](media/how-to/how-to-migrate-from-hub.png)

4.  Po stisknutí odkaz migrate zobrazí formulář umožňuje:
   * Zadejte pracovní prostor, který chcete převést na vlastní Translator
   * Označuje, zda chcete převést všechna školení s úspěšné školení nebo právě nasazené školení. Ve výchozím nastavení se přesunou všechny úspěšné školení.
   * Označuje, jestli byste chtěli vašich školení automaticky nasazena po dokončení školení. Ve výchozím nastavení nebude trénování automaticky nasazena po dokončení.


5.  Klikněte na tlačítko "Odeslat žádost o".

## <a name="migrate-a-workspace"></a>Migrace s pracovním prostorem

Navíc k migraci jednoho projektu, může také migrovat všechny projekty s úspěšné školení v pracovním prostoru.  To způsobí každý projekt v pracovním prostoru, který se má vyhodnotit, jako by měl byla stisknuta klávesa odkaz migrace.  Tato funkce je vhodný pro uživatele s mnoha projektů, kteří chtějí migrovat všechny z nich k Translatoru Custom se stejným nastavením.  Na stránce nastavení centra Translator, nepůjdou migrace pracovní oblasti.

K migraci pracovního prostoru:

1.  Přihlaste se k Microsoft Translatoru rozbočovače.

2.  Přejděte na stránku "Nastavení".

3.  Na stránce "Nastavení" Klepnutím na tlačítko "Pracovního prostoru migrovat data Translator vlastní".

    ![Jak migrovat z centra](media/how-to/how-to-migrate-workspace-from-hub.png)

4.  Na další stránce vyberte jednu z těchto dvou možností:

    a.  Pouze nasazené školení: Výběrem této možnosti bude migrovat pouze nasazené systémy a související dokumenty.

    b.  Všechny úspěšné školení: Výběrem této možnosti bude migrace úspěšná školení a související dokumenty.

    c.  Zadejte vlastní Translator cíl ID pracovního prostoru.

    ![Jak migrovat z centra](media/how-to/how-to-migrate-from-hub-screen.png)

5.  Klikněte na tlačítko Odeslat žádost.



## <a name="migration-history"></a>Migrace historie

Při požadovaný pracovní prostor / projekt migrace z centra, najdete na stránce nastavení Translator vlastní historii migrace. 

Chcete-li zobrazit historii migrace, postupujte podle těchto kroků:

1.  Přejděte na stránku "Nastavení" portálu Translatoru Custom.

2.  V části migrace historie na stránce nastavení klikněte na historii migrace.

    ![Migrace historie](media/how-to/how-to-migration-history.png)

Migrace historie stránce se zobrazí následující informace jako souhrn pro každou migraci, o kterou žádáte.

1.  Při migraci: Jméno a e-mailu uživatele odeslání této žádosti o migraci

2.  Migrovat na: Datum a časové razítko migrace

3.  Projekty: Počet projektů požadovaná pro migraci v/s počtu projektů úspěšné migraci.

4.  Školení: Počet školení požadovaná pro migraci v/s počet školení úspěšné migraci.

5.  Dokumenty: Počet dokumentů požadovaná pro migraci v/s počtem dokumentů úspěšné migraci.

    ![Podrobnosti o migraci historie](media/how-to/how-to-migration-history-details.png)

Pokud chcete podrobnější sestavu migrace o projekty, školení a dokumenty, máte možnost Exportovat podrobnosti jako sdílený svazek clusteru.

## <a name="implementation-notes"></a>Poznámky k implementaci
* Migrace projektu z centra na vlastní Translator nebude mít žádný vliv na vaše Centrum školení nebo projekty. Jsme neodstraňujte projekty nebo dokumenty z centra během migrace a jsme není zrušení modely.
* Jsou povolené jenom po migraci na projekt.  Pokud je nutné opakovat migraci na projekt, kontaktujte nás prosím.
* V současné době vlastní Translator podporuje 36 jazyků převod z a do angličtiny a usilovně pracujeme na přidání dalších jazyků.  Centrum nevyžaduje základní modely a proto podporuje několik jazyků tisíců.  Dvojici nepodporovaný jazyk, můžete migrovat, ale My pouze provést migraci dokumenty a projektu definice.  Společnost Microsoft nebude moci natrénovat nový model.  Kromě toho tyto dokumenty a projektů se zobrazí jako neaktivní indikaci, že nelze použít v tuto chvíli. Pokud je přidána podpora pro tyto projekty a/nebo dokumenty, budou aktivní a trainable.
* Vlastní Translator v současné době nepodporuje jeden jazyk trénovací data.  Podobně jako páry nepodporovaný jazyk můžete migrovat jeden jazyk dokumenty, ale zobrazí jako neaktivní, dokud jeden jazyk data se podporuje.  
* Vlastní Translator vyžaduje 10 tisíc paralelní věty k trénování.  Na menší sadu dat může trénování Microsoft Hub. Školicí migrována, který nesplňuje tento požadavek, nebude Trénink.


## <a name="custom-translator-versus-hub"></a>Vlastní Translator oproti centra

Tato tabulka obsahuje porovnání funkcí mezi centrem Microsoft Translator a vlastní překladač.

|   | Centrum | Custom Translator |
|:-----|:----:|:----:|
|Stav funkce vlastního nastavení   | Všeobecná dostupnost  | Všeobecná dostupnost |
| Verze text API  | V2    | V3  |
| Přizpůsobení SMT | Ano   | Ne |
| Přizpůsobení NMT | Ne    | Ano |
| Nové sjednocené přizpůsobení služby řeči | Ne    | Ano |
| Bez trasování | Ano | Ano |

## <a name="next-steps"></a>Další postup

- [Trénování modelu](how-to-train-model.md).
- Začněte používat váš model nasazený vlastní překlad prostřednictvím [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
