---
title: Migrovat pracovní prostor a projekty služby Microsoft Translator hub? – Vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Tento článek vysvětluje, jak migrovat pracovní prostor a projekty centra do Azure Cognitive Services vlastního překladatele.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 89658ce88b4f1ac9d5bacac7bd45511b4aa0a1be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895707"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Migrace pracovního prostoru centra a projektů do vlastního překladatele

Pracovní prostor a projekty [centra Microsoft Translator](https://hub.microsofttranslator.com/) můžete snadno migrovat do vlastního překladatele. Migrace se spouští z centra Microsoftu tak, že se vybere pracovní prostor nebo projekt, pak se vybere pracovní prostor ve vlastním překladateli a pak se vybere školení, která chcete přenést. Po zahájení migrace se vybraná nastavení školení přenesou se všemi relevantními dokumenty. Nasazené modely jsou učené a po dokončení se dají nasadit.

Tyto akce se provádějí během migrace:
* Všechny definice dokumentů a projektů budou mít přenesené názvy s předponou "hub_" s názvem. Automaticky generovaný test a data optimalizace budou pojmenovány hub_systemtune_ \<modelid> nebo hub_systemtest_ \<modelid> .
* Všechna školení, která byla v nasazeném stavu, když migrace proběhne, budou automaticky vyškolena pomocí dokumentů školení centra. Toto školení se nebude účtovat vašemu předplatnému. Pokud jste pro migraci vybrali možnost automatické nasazení, bude se po dokončení nasadit model trained. Použijí se pravidelné poplatky za hostování.
* Všechny migrované kurzy, které nebyly v nasazeném stavu, budou vloženy do migrovaného konceptu. V tomto stavu budete mít možnost školení modelu s migrovaných definicí, ale budou platit pravidelné poplatky za školení.
* Skóre BLEU migrováno z školení centra můžete kdykoli najít na stránce TrainingDetails modelu v záhlaví "Bleu skóre v MT hub".

> [!Note] 
> Pro úspěšné školení vyžaduje vlastní Překladatel minimálně 10 000 jedinečných extrahovaných vět. Vlastní Překladatel nemůže provádět školení s méně než [navrhovaným minimem](./sentence-alignment.md#suggested-minimum-number-of-sentences).

## <a name="find-custom-translator-workspace-id"></a>Najít ID vlastního pracovního prostoru překladatele

Pokud chcete migrovat pracovní prostor [centra Microsoft Translator](https://hub.microsofttranslator.com/) , budete potřebovat ID cílového pracovního prostoru ve vlastním překladateli. Cílový pracovní prostor ve vlastním překladateli je místo, kam se mají migrovat všechny pracovní prostory a projekty centra.

ID cílového pracovního prostoru najdete na stránce vlastní nastavení překladatele:

1. Přejít na stránku nastavení na portálu vlastního překladatele.

2. ID pracovního prostoru najdete v části základní informace.

    ![Jak najít ID cílového pracovního prostoru](media/how-to/how-to-find-destination-ws-id.png)

3. Zachovat ID cílového pracovního prostoru, aby se mohl v procesu migrace odkazovat.

## <a name="migrate-a-project"></a>Migrace projektu

Pokud chcete své projekty migrovat selektivně, Microsoft Translator hub vám dává možnost.

Migrace projektu:

1. Přihlaste se ke službě Microsoft Translator hub.

2. Přejít na stránku projekty.

3. Klikněte na odkaz migrovat pro příslušný projekt.

    ![Snímek obrazovky, který zvýrazní tlačítko migrace pro vybraný projekt.](media/how-to/how-to-migrate-from-hub.png)

4. Po kliknutí na odkaz migrace se zobrazí formulář, který vám umožní:
   * Zadejte pracovní prostor, na který se má přenést vlastní překladatel.
   * Určete, jestli chcete přenést všechny školení s úspěšnými kurzy, nebo jenom nasazená školení. Ve výchozím nastavení se přenesou všechna úspěšná školení.
   * Určete, jestli se vaše školení má po dokončení školení automaticky nasadit. Ve výchozím nastavení se vaše školení nebude po dokončení automaticky nasadit.

5. Klikněte na Odeslat žádost.

## <a name="migrate-a-workspace"></a>Migrace pracovního prostoru

Kromě migrace jednoho projektu můžete také migrovat všechny projekty s úspěšnými školeními v pracovním prostoru. To způsobí, že se každý projekt v pracovním prostoru vyhodnotí stejně, jako kdyby bylo stisknuto propojení migrace. Tato funkce je vhodná pro uživatele, kteří mají mnoho projektů, kteří chtějí migrovat všechny z nich na vlastní překladatele se stejnými nastaveními. Migraci pracovního prostoru můžete iniciovat na stránce nastavení v centru překladatelů.

Postup migrace pracovního prostoru:

1. Přihlaste se ke službě Microsoft Translator hub.

2. Přejít na stránku nastavení.

3. Na stránce nastavení klikněte na migrovat data pracovního prostoru na vlastní překladatel.

    ![Snímek obrazovky, který zvýrazní možnost migrace dat pracovního prostoru na vlastní překladatel.](media/how-to/how-to-migrate-workspace-from-hub.png)

4. Na další stránce vyberte jednu z těchto dvou možností:

    a. Jenom nasazené školení: Když vyberete tuto možnost, migrují se jenom nasazené systémy a související dokumenty.

    b. Všechna úspěšná školení: Když vyberete tuto možnost, migrují se všechna vaše úspěšná školení a související dokumenty.

    c. Do vlastního překladatele zadejte ID cílového pracovního prostoru.

    ![Postup migrace z centra](media/how-to/how-to-migrate-from-hub-screen.png)

5. Klikněte na Odeslat žádost.

## <a name="migration-history"></a>Historie migrace

Když jste si vyžádali migraci pracovního prostoru nebo projektu z centra, najdete historii migrace na stránce vlastní nastavení překladatele.

Chcete-li zobrazit historii migrace, postupujte podle následujících kroků:

1. Přejít na stránku nastavení na portálu vlastního překladatele.

2. V části Historie migrace na stránce nastavení klikněte na historie migrace.

    ![Historie migrace](media/how-to/how-to-migration-history.png)

Stránka historie migrace zobrazuje pro každou požadovanou migraci následující informace jako shrnutí.

1. Migrováno uživatelem: jméno a e-mail uživatele, který odeslal tuto žádost o migraci

2. Migrováno: datum a časové razítko migrace

3. Projekty: počet projektů vyžadovaných pro migraci v/s v projektech byl úspěšně migrován.

4. Školení: počet školení požadovaných k migraci v/s pro počet úspěšně migrovaných školení.

5. Dokumenty: počet dokumentů požadovaných k migraci v/s pro počet dokumentů, které byly úspěšně migrovány.

    ![Podrobnosti o historii migrace](media/how-to/how-to-migration-history-details.png)

Pokud potřebujete podrobnější zprávu o migraci týkající se vašich projektů, školení a dokumentů, máte možnost exportovat podrobnosti jako sdílený svazek clusteru.

## <a name="implementation-notes"></a>Poznámky k implementaci
* Systémy s páry jazyka, které ještě nejsou dostupné ve vlastním překladateli, budou k dispozici jenom pro přístup k datům nebo jejich nasazení prostřednictvím vlastního překladatele. Tyto projekty budou na stránce projekty označeny jako "nedostupné". Jak povolíme nové páry jazyků s vlastním překladatelem, projekty se aktivují pro vlak a nasazení. 
* Migrace projektu z centra na vlastní Překladatel nebude mít žádný vliv na školení nebo projekty vašeho centra. Během migrace neodstraňuji projekty ani dokumenty z centra a nebudeme nasazovat modely.
* Pouze jednou pro každý projekt je povolena migrace. Pokud potřebujete opakovat migraci projektu, kontaktujte nás prosím.
* Vlastní Překladatel podporuje páry jazyků NMT a z angličtiny. [Zobrazte úplný seznam podporovaných jazyků](../language-support.md#customization). Centrum nevyžaduje základní modely, a proto podporuje několik tisíc jazyků. Můžete migrovat nepodporovaný pár jazyků, ale provedeme pouze migraci dokumentů a definic projektů. Nový model nebudeme moct naučit. Kromě toho se tyto dokumenty a projekty zobrazí jako neaktivní, aby bylo možné určit, že v tuto chvíli nelze použít. Pokud je pro tyto projekty a/nebo dokumenty přidána podpora, stanou se aktivními a vlaky.
* Vlastní Překladatel v současné době nepodporuje monolingual školicí data. Podobně jako nepodporované páry jazyků můžete migrovat dokumenty monolingual, ale zobrazují se jako neaktivní, dokud se nepodporují data monolingual.
* Vlastní překladač vyžaduje až 10 000 paralelních vět, aby bylo možné vlaky. Centrum Microsoftu může vytvořit menší sadu dat. Pokud je migrováno školení, které tento požadavek nesplňuje, nebude vyškolené.

## <a name="custom-translator-versus-hub"></a>Vlastní Překladatel versus centrum

Tato tabulka porovnává funkce mezi centrem Microsoft Translator a vlastním překladatelem.

| Funkce | Rozbočovač | Custom Translator |
| ------- | :-: | :---------------: |
| Stav funkce přizpůsobení    | Obecná dostupnost    | Obecná dostupnost |
| Verze textového rozhraní API    | V2     | Technologie  |
| Přizpůsobení SMT    | Yes    | No |
| Přizpůsobení NMT    | No    | Yes |
| Nové přizpůsobení sjednocené služby pro rozpoznávání řeči    | No    | Yes |
| Žádné trasování | Yes | Yes |

## <a name="new-languages"></a>Nové jazyky

Pokud jste komunitou nebo organizací, která pracuje na vytváření nového jazykového systému pro překladatele, získáte [custommt@microsoft.com](mailto:custommt@microsoft.com) Další informace v.

## <a name="next-steps"></a>Další kroky

- [Výuka modelu](how-to-train-model.md).
- Začněte používat nasazený vlastní model překladu prostřednictvím [překladatele V3](../reference/v3-0-translate.md?tabs=curl).