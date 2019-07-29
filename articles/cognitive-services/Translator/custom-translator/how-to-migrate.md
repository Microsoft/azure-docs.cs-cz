---
title: Migrace pracovního prostoru Microsoft Translator Hub a projekty? – Vlastní Translator
titleSuffix: Azure Cognitive Services
description: Migrace do vlastní Translator váš pracovní prostor centra a projekty.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: cd821ad8fce813d269ace8fb4945cb796c2ae758
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595739"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Migrace na Translator vlastní pracovní prostor centra a projekty

Můžete snadno migrovat vaše [Microsoft Translator Hub](https://hub.microsofttranslator.com/) pracovní prostor a projekty, které mají vlastní překladač. Migrace je zahájeno z Microsoft Hub vyberete pracovní prostor nebo projektu, pak výběrem pracovního prostoru v Translator vlastní a pak vyberete školení, které chcete převést. Po zahájení migrace nastavení vybrané školení bude převeden pomocí všechny související dokumenty. Nasazených modelů jsou trénované a může být autodeployed po dokončení.

Během migrace se provádí tyto akce:
* Všechny dokumenty a definice projektu budou mít jejich názvy přenesená a uveďte "hub_" předponou názvu. Automaticky generovaný test a data optimalizace budou mít název hub_systemtune_\<ModelID > nebo hub_systemtest_\<ModelID >.
* Jakékoli školení, které byly v nasazeném stavu místo pořízením migrace bude automaticky trénuje použití dokumentů Centrum školení. Toto školení nebude účtovat do vašeho předplatného. Pokud automatické nasazení byla vybrána pro migraci se po dokončení nasadí trénovaného modelu. Aplikují se standardní poplatky za hostování.
* Všechny migrované školení, které nebyly v nasazeném stavu zařadí do stavu migrovaných konceptu. V tomto stavu budete mít možnost trénujete model pro migrované definicí, ale poplatky za regulární školení.
* V libovolném bodě skóre BLEU migrovat z centra školení najdete na stránce TrainingDetails modelu v "Bleu skóre v centru MT" záhlaví.

> [!Note]
> Pro úspěšné školení vyžaduje vlastní Překladatel minimálně 10 000 jedinečných extrahovaných vět. Vlastní Překladatel nemůže provádět školení s méně než navrhovaným [minimem](sentence-alignment.md#suggested-minimum-number-of-extracted-and-aligned-sentences).

## <a name="find-custom-translator-workspace-id"></a>Najít vlastní Translator ID pracovního prostoru

K migraci [Microsoft Translator Hub](https://hub.microsofttranslator.com/) pracovního prostoru, budete potřebovat cílové ID pracovního prostoru v Translatoru Custom. Cílový pracovní prostor v vlastní Translator je, kde Centrum pracovních prostorů a projektů se dají migrovat do.

Cílové ID pracovního prostoru na stránce nastavení Translator vlastní najdete:

1. Přejděte na stránku "Nastavení" portálu Translatoru Custom.

2. ID pracovního prostoru najdete v části základní údaje.

    ![Jak najít ID cílového pracovního prostoru](media/how-to/how-to-find-destination-ws-id.png)

3. Ponechte cíl ID pracovního prostoru odkazovat během procesu migrace.

## <a name="migrate-a-project"></a>Migrace projektu

Pokud chcete selektivně migraci vašich projektů, Microsoft Translator Hub vám tuto možnost.

Migrace projektu:

1. Přihlaste se k Microsoft Translatoru rozbočovače.

2. Přejděte na stránku "Projekty".

3. Klikněte na možnost "Přenést" odkaz na příslušný projekt.

    ![Jak migrovat z centra](media/how-to/how-to-migrate-from-hub.png)

4. Po stisknutí odkaz migrate zobrazí formulář umožňuje:
   * Zadejte pracovní prostor, který chcete převést na vlastní Translator
   * Označuje, zda chcete převést všechna školení s úspěšné školení nebo právě nasazené školení. Ve výchozím nastavení se přesunou všechny úspěšné školení.
   * Označuje, jestli byste chtěli vašich školení automaticky nasazena po dokončení školení. Ve výchozím nastavení nebude trénování automaticky nasazena po dokončení.

5. Klikněte na tlačítko "Odeslat žádost o".

## <a name="migrate-a-workspace"></a>Migrace s pracovním prostorem

Navíc k migraci jednoho projektu, může také migrovat všechny projekty s úspěšné školení v pracovním prostoru. To způsobí každý projekt v pracovním prostoru, který se má vyhodnotit, jako by měl byla stisknuta klávesa odkaz migrace. Tato funkce je vhodný pro uživatele s mnoha projektů, kteří chtějí migrovat všechny z nich k Translatoru Custom se stejným nastavením. Na stránce nastavení centra Translator, nepůjdou migrace pracovní oblasti.

K migraci pracovního prostoru:

1. Přihlaste se k Microsoft Translatoru rozbočovače.

2. Přejděte na stránku "Nastavení".

3. Na stránce "Nastavení" Klepnutím na tlačítko "Pracovního prostoru migrovat data Translator vlastní".

    ![Jak migrovat z centra](media/how-to/how-to-migrate-workspace-from-hub.png)

4. Na další stránce vyberte jednu z těchto dvou možností:

    a. Jenom nasazené školení: Výběrem této možnosti dojde k migraci pouze nasazených systémů a souvisejících dokumentů.

    b. Všechna úspěšná školení: Výběrem této možnosti se migrují všechna vaše úspěšná školení a související dokumenty.

    c. Zadejte vlastní Translator cíl ID pracovního prostoru.

    ![Jak migrovat z centra](media/how-to/how-to-migrate-from-hub-screen.png)

5. Klikněte na tlačítko Odeslat žádost.

## <a name="migration-history"></a>Migrace historie

Při požadovaný pracovní prostor / projekt migrace z centra, najdete na stránce nastavení Translator vlastní historii migrace.

Chcete-li zobrazit historii migrace, postupujte podle těchto kroků:

1. Přejděte na stránku "Nastavení" portálu Translatoru Custom.

2. V části migrace historie na stránce nastavení klikněte na historii migrace.

    ![Migrace historie](media/how-to/how-to-migration-history.png)

Migrace historie stránce se zobrazí následující informace jako souhrn pro každou migraci, o kterou žádáte.

1. Migrovali: Jméno a e-mail uživatele, který odeslal tuto žádost o migraci

2. Migrováno: Datum a časové razítko migrace

3. Projekty: Počet projektů požadovaných pro migraci počtu projektů v/s, které byly úspěšně migrovány.

4. Získejte školení Počet školení požadovaných pro migraci v/s pro počet úspěšně migrovaných školení.

5. Document Počet dokumentů požadovaných pro migraci v/s pro počet dokumentů, které byly úspěšně migrovány.

    ![Podrobnosti o migraci historie](media/how-to/how-to-migration-history-details.png)

Pokud chcete podrobnější sestavu migrace o projekty, školení a dokumenty, máte možnost Exportovat podrobnosti jako sdílený svazek clusteru.

## <a name="implementation-notes"></a>Poznámky k implementaci
* Systémy s páry jazyka, které ještě nejsou dostupné ve vlastním překladateli, budou k dispozici jenom pro přístup k datům nebo jejich nasazení prostřednictvím vlastního překladatele. Tyto projekty budou na stránce projekty označeny jako "nedostupné". Jak povolíme nové páry jazyků s vlastním překladatelem, projekty se aktivují pro vlak a nasazení. 
* Migrace projektu z centra na vlastní Translator nebude mít žádný vliv na vaše Centrum školení nebo projekty. Jsme neodstraňujte projekty nebo dokumenty z centra během migrace a jsme není zrušení modely.
* Jsou povolené jenom po migraci na projekt. Pokud je nutné opakovat migraci na projekt, kontaktujte nás prosím.
* Vlastní Překladatel podporuje páry jazyků NMT a z angličtiny. [Úplný seznam podporovaných langauges najdete v](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization)části. Centrum nevyžaduje základní modely a proto podporuje několik jazyků tisíců. Dvojici nepodporovaný jazyk, můžete migrovat, ale My pouze provést migraci dokumenty a projektu definice. Společnost Microsoft nebude moci natrénovat nový model. Kromě toho tyto dokumenty a projektů se zobrazí jako neaktivní indikaci, že nelze použít v tuto chvíli. Pokud je přidána podpora pro tyto projekty a/nebo dokumenty, budou aktivní a trainable.
* Vlastní Translator v současné době nepodporuje jeden jazyk trénovací data. Podobně jako páry nepodporovaný jazyk můžete migrovat jeden jazyk dokumenty, ale zobrazí jako neaktivní, dokud jeden jazyk data se podporuje.
* Vlastní Translator vyžaduje 10 tisíc paralelní věty k trénování. Na menší sadu dat může trénování Microsoft Hub. Pokud je migrováno školení, které tento požadavek nesplňuje, nebude vyškolené.

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

## <a name="new-languages"></a>Nové jazyky

Pokud jste komunitou nebo organizací, která pracuje na vytváření nového jazykového systému pro Microsoft Translator, získáte [custommt@microsoft.com](mailto:custommt@microsoft.com) Další informace v.

## <a name="next-steps"></a>Další postup

- [Trénování modelu](how-to-train-model.md).
- Začněte používat váš model nasazený vlastní překlad prostřednictvím [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
