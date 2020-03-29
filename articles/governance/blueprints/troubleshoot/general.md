---
title: Odstraňování běžných chyb
description: Zjistěte, jak řešit problémy s vytvářením, přiřazování mašit a odebíráním podrobných plánů, jako jsou porušení zásad a funkce parametrů podrobného plánu.
ms.date: 01/15/2020
ms.topic: troubleshooting
ms.openlocfilehash: 7306e344a479008a87164a954c4444d375950b0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157079"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Poradce při potížích pomocí azure blueprints

Při vytváření, přiřazování nebo odebírání podrobných plánů můžete narazit na chyby. Tento článek popisuje různé chyby, které mohou nastat a jak je vyřešit.

## <a name="finding-error-details"></a>Hledání podrobností o chybě

Mnoho chyb bude výsledkem přiřazení podrobného plánu k oboru. Pokud se přiřazení nezdaří, podrobný plán obsahuje podrobnosti o neúspěšném nasazení. Tyto informace označují problém tak, aby jej lze opravit a další nasazení úspěšné.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Na stránce vlevo vyberte **Přiřazené podrobné plány** a pomocí vyhledávacího pole vyfiltrujte přiřazení podrobného plánu k vyhledání neúspěšného přiřazení. Tabulku přiřazení můžete také seřadit podle sloupce **Stav zřizování,** abyste viděli všechna neúspěšná přiřazení seskupená.

1. Klikněte levým tlačítkem myši na podrobný plán se stavem _Selhání_ nebo klikněte pravým tlačítkem myši a vyberte **zobrazit podrobnosti o přiřazení**.

1. Červený nápis s upozorněním, že přiřazení selhalo, je v horní části stránky přiřazení podrobného plánu. Klikněte kdekoli na banner získat další podrobnosti.

Je běžné, že chyba je způsobena artefaktem a ne plánem jako celkem. Pokud artefakt vytvoří trezor klíčů a zásady Azure zabrání vytvoření trezoru klíčů, celé přiřazení se nezdaří.

## <a name="general-errors"></a>Obecné chyby

### <a name="scenario-policy-violation"></a><a name="policy-violation"></a>Scénář: Porušení zásad

#### <a name="issue"></a>Problém

Nasazení šablony se nezdařilo z důvodu porušení zásad.

#### <a name="cause"></a>Příčina

Zásada může být v konfliktu s nasazením z několika důvodů:

- Vytvořený prostředek je omezen zásadami (obvykle skladová položka nebo omezení umístění)
- Nasazení je nastavení polí, která jsou konfigurována podle zásad (společné s tagy)

#### <a name="resolution"></a>Řešení

Změňte podrobný plán tak, aby nebyl v konfliktu se zásadami v podrobnostech o chybě. Pokud tato změna není možná, alternativní možností je změnit rozsah přiřazení zásad, takže podrobný plán již není v rozporu se zásadou.

### <a name="scenario-blueprint-parameter-is-a-function"></a><a name="escape-function-parameter"></a>Scénář: Parametr Podrobného plánu je funkce

#### <a name="issue"></a>Problém

Podrobné parametry, které jsou funkce jsou zpracovány před předáním artefakty.

#### <a name="cause"></a>Příčina

Předání parametru podrobného plánu, `[resourceGroup().tags.myTag]`který používá funkci, například , artefaktu má za následek zpracovaný výsledek funkce nastavené na artefaktu namísto dynamické funkce.

#### <a name="resolution"></a>Řešení

Chcete-li předat funkci jako parametr, unikne temu celého řetězce tak, `[` aby parametr podrobného plánu vypadal jako `[[resourceGroup().tags.myTag]`. Řídicí znak způsobí, že podrobné plány považovat hodnotu jako řetězec při zpracování podrobného plánu. Podrobné plány pak umístí funkci na artefakt umožňuje, aby byl dynamický podle očekávání. Další informace najdete [v tématu Syntaxe a výrazy v šablonách Azure Resource Manager](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="delete-errors"></a>Odstranit chyby

### <a name="scenario-assignment-deletion-timeout"></a><a name="assign-delete-timeout"></a>Scénář: Časový limit odstranění přiřazení

#### <a name="issue"></a>Problém

Odstranění přiřazení podrobného plánu se nedokončí.

#### <a name="cause"></a>Příčina

Přiřazení podrobného plánu může uvíznout ve stavu bez terminálu při odstranění. Tento stav je způsoben, když prostředky vytvořené přiřazení podrobného plánu stále čekají na odstranění nebo nevracejí stavový kód do Azure Blueprints.

#### <a name="resolution"></a>Řešení

Přiřazení podrobného plánu v neterminálovém stavu jsou automaticky **označena jako Neúspěšná** po _6hodinovém_ časovém odpočitadě. Po upraví stav přiřazení podrobného plánu, odstranění lze zopakovat.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure .](https://azure.microsoft.com/support/forums/)
- Spojte [@AzureSupport](https://twitter.com/azuresupport) se s – oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí propojením komunity Azure se správnými prostředky: odpověďmi, podporou a odborníky.
- Pokud potřebujete další pomoc, můžete nastolet incident podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.