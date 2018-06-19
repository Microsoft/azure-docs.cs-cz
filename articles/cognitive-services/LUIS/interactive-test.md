---
title: Trénování a testování aplikace LEOŠ - Azure | Microsoft Docs
description: Pomocí jazyka Principy (LEOŠ) nepřetržitě fungovat ve vaší aplikaci ji upravit a zlepšovat její znalosti jazyka.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 760434253a3ece14352154a22cc68142ec2b0531
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35760212"
---
# <a name="test-your-luis-app"></a>Testování aplikace s LEOŠ
<a name="train-your-app"></a>
[Cvičení](luis-how-to-train.md) a [testování](luis-concept-test.md) aplikace je iterativní proces. Po cvičení LEOŠ aplikaci, otestujte ji s ukázka utterances a zjistěte, zda jsou správně rozpoznány tříd Intent a entity. Pokud nejsou, proveďte aktualizace znovu pro aplikaci LEOŠ, train a testu. 

<!-- anchors for H2 name changes -->
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="interactive-testing"></a>
## <a name="test-an-utterance"></a>Testování utterance

1. Přístup k aplikaci tak, že vyberete na jeho název **Moje aplikace** stránky. 

2. Pro přístup k **Test** panely snímku na více systémů, vyberte **Test** v horním panelu vaší aplikace.

    ![Stránka Train a testování aplikace](./media/luis-how-to-interactive-test/test.png)

3. Zadejte utterance do textového pole a vyberte Enter. Můžete zadat jak mnoho testovací utterances jako chcete v **testování**, ale jenom jeden utterance najednou.

4. Utterance, jeho horní záměr a skóre se přidají do seznamu utterances v části textového pole.

    ![Interaktivní testování identifikuje nesprávný záměru](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="clear-test-panel"></a>Zrušte testovací panely
Odstranit všechny zadané testovací utterances a jejich výsledky z konzoly pro test, zaškrtněte **začít znovu** v levém horním rohu **testovací panely**. 

## <a name="close-test-panel"></a>Zavřete testovací panely
Zavřete **Test** panel, vyberte **Test** tlačítko znovu.

## <a name="inspect-score"></a>Zkontrolujte skóre
Zkontrolujte podrobnosti o výsledků testů v **kontroly** panelu. 
 
1. Pomocí **testovací** otevřete panel snímku na více systémů, vyberte **kontroly** pro utterance chcete porovnat. 

    ![Zkontrolujte tlačítko](./media/luis-how-to-interactive-test/inspect.png)

2. **Kontroly** se zobrazí panel. Na panelu zahrnuje horní vyhodnocování záměr, jakož i všechny zjištěné entity. Panel zobrazuje výsledek vybrané utterance.

    ![Zkontrolujte tlačítko](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Správné horní vyhodnocování záměru

1. Pokud horní vyhodnocování záměr není v pořádku, vyberte **upravit** tlačítko.

2.  V rozevíracím seznamu vyberte správné záměr pro utterance.

    ![Vyberte správný záměr](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Zobrazení výsledků postojích

Pokud **postojích analysis** je nakonfigurovaná na **[publikovat](publishapp.md#enable-sentiment-analysis)** stránky, výsledky testů zahrnout postojích v utterance nalezen. 

![Obrázek testovací podokno postojích analýzy](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Opravte záměr odpovídající vzor
Pokud používáte [vzory](luis-concept-patterns.md) a utterance shodná vzor, ale byl nesprávný záměr předpovědět, vyberte **upravit** propojit podle vzoru a pak vyberte správné záměr.

## <a name="compare-with-published-version"></a>Porovnání s publikovanou verzi
Můžete otestovat active verzi vaší aplikace pomocí k publikovanému [koncový bod](luis-glossary.md#endpoint) verze. V **kontroly** panel, vyberte **publikovaná porovnat s**. Testování u publikovaných modelu je odečtena od vyrovnávání kvóty vašeho předplatného Azure. 

![Porovnání s publikována](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Zobrazit koncový bod JSON panelu testu
Můžete zobrazit koncový bod JSON pro porovnání výběrem vrátit **zobrazení zobrazit JSON**.

![Publikované odpověď JSON](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>Další nastavení panelu testu

### <a name="luis-endpoint"></a>Koncový bod LEOŠ
Pokud máte několik koncových bodů LEOŠ, použijte **další nastavení** odkaz na Test je publikována podokně změnit koncový bod používat pro testování. Pokud si nejste jisti, kterému koncovému bodu použít, vyberte výchozí **Starter_Key**. 

![Panel testu se zvýrazněnou odkaz Další nastavení](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)


### <a name="view-bing-spell-check-corrections-in-test-panel"></a>Zobrazení kontrolu pravopisu v Bingu oprav panelu testu
Můžete zobrazit pravopisu poskytované [kontrola pravopisu Bing v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) rozhraní API v zobrazení JSON **publikováno** panelu testovací podokno. 

Chcete-li tuto funkci používat, musí jste publikovali aplikaci, a mít kontrolu pravopisu Bing [klíč služby](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api). Klíč služby nejsou uloženy a je třeba obnovit pro každou relaci prohlížeče. 

Použijte následující používat klíč v7 služby Bing kontrola pravopisu v testovací podokno. 

1. V panelu testu na **publikováno** podokně, vyberte **další nastavení**.

2. V dialogovém okně automaticky otevírané okno, zadejte vaše **kontrola pravopisu Bing** klíč služby. 
    ![Zadejte klíč služby Bing kontrola pravopisu](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)

3. Zadejte dotaz s nesprávné pravopis například `book flite to seattle` a zadejte vyberte. Nesprávný znění slova `flite` je nahrazena v dotazu posílá LEOŠ a výsledný JSON zobrazuje obě původní dotaz, jako `query`a opravené pravopisu v dotazu, jako `alteredQuery`.

    ![Opravě pravopis JSON](./media/luis-how-to-interactive-test/interactive-with-spell-check-results.png)

<a name="json-file-with-no-duplicates"></a>
<a name="import-a-dataset-file-for-batch-testing"></a>
<a name="export-rename-delete-or-download-dataset"></a>
<a name="run-a-batch-test-on-your-trained-app"></a>
<a name="access-batch-test-result-details-in-a-visualized-view"></a>
<a name="filter-chart-results-by-intent-or-entity"></a>
<a name="investigate-false-sections"></a>
<a name="view single-point utterance data"></a>
<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>
## <a name="batch-testing"></a>Testování batch
Najdete v části testování batch [koncepty](luis-concept-batch-test.md) a další [postup](luis-how-to-batch-test.md) testování dávky utterances.

## <a name="next-steps"></a>Další postup

Pokud testování uvádí, že aplikace LEOŠ nerozpoznal správné záměry a entity, můžete pracovat a zvyšte tak přesnost aplikace LEOŠ označování další utterances nebo přidáním funkcí. 

* [Označení navrhované utterances s LEOŠ](Label-Suggested-Utterances.md) 
* [Funkce lze použít ke zlepšení výkonu aplikace LEOŠ](luis-how-to-add-features.md) 
