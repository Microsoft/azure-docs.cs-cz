---
title: Odstraňování běžných chyb
description: Naučte se řešit problémy při vytváření, přiřazování a odebírání podrobných plánů, jako jsou porušení zásad a funkce parametrů podrobného plánu.
ms.date: 06/29/2020
ms.topic: troubleshooting
ms.openlocfilehash: e8362e2a22317d73e0fd392bd497cd9f2c5ffe4f
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89651324"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Řešení chyb pomocí Azure modrotisky

Při vytváření, přiřazování nebo odebírání podrobných plánů můžete spustit chybu. Tento článek popisuje různé chyby, ke kterým může dojít, a jejich řešení.

## <a name="finding-error-details"></a>Hledání podrobností o chybě

Mnohé chyby budou výsledkem přiřazení podrobného plánu k oboru. Pokud přiřazení neproběhne úspěšně, podrobný plán poskytuje podrobnosti o neúspěšném nasazení. Tyto informace označují problém tak, aby se mohl opravit a další nasazení bude úspěšné.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **plány**.

1. Na stránce vlevo vyberte **přiřazené plány** a pomocí vyhledávacího pole vyfiltrujte přiřazení podrobného plánu, abyste našli neúspěšné přiřazení. Můžete také seřadit tabulku přiřazení podle sloupce **stav zřizování** a zobrazit tak všechna neúspěšná přiřazení seskupená dohromady.

1. Vyberte podrobný plán s _neúspěšným_ stavem nebo klikněte pravým tlačítkem a vyberte **Zobrazit podrobnosti o přiřazení**.

1. Červená zpráva s upozorněním, že přiřazení se nezdařilo, je v horní části stránky přiřazení podrobného plánu. Další podrobnosti získáte, když vyberete libovolné místo na informačním panelu.

Je běžné, že chyba je způsobena artefaktem a nikoli úplným plánem. Pokud artefakt vytvoří Key Vault a Azure Policy znemožňuje vytváření Key Vault, celé přiřazení selže.

## <a name="general-errors"></a>Obecné chyby

### <a name="scenario-policy-violation"></a><a name="policy-violation"></a>Scénář: porušení zásad

#### <a name="issue"></a>Problém

Nasazení šablony se nepovedlo kvůli porušení zásad.

#### <a name="cause"></a>Příčina

Zásada může být v konfliktu s nasazením z několika důvodů:

- Vytvářený prostředek je omezený zásadami (obvykle se jedná o omezení umístění SKU nebo místa).
- Nasazení je nastavené pole, která jsou nakonfigurovaná pomocí zásad (společné pomocí značek).

#### <a name="resolution"></a>Řešení

Změňte podrobný plán tak, aby nedošlo ke konfliktu se zásadami v podrobnostech o chybě. Pokud tuto změnu nemůžete udělat, je alternativním parametrem, že se změní rozsah přiřazení zásady, takže podrobný plán už není v konfliktu se zásadami.

### <a name="scenario-blueprint-parameter-is-a-function"></a><a name="escape-function-parameter"></a>Scénář: parametr podrobného plánu je funkce

#### <a name="issue"></a>Problém

Parametry podrobného plánu, které jsou funkce, jsou zpracovány před předáním artefaktům.

#### <a name="cause"></a>Příčina

Předání parametru podrobného plánu, který používá funkci, jako `[resourceGroup().tags.myTag]` je například, na výsledek artefaktu v důsledku zpracování funkce, která je nastavena na artefaktu namísto dynamické funkce.

#### <a name="resolution"></a>Řešení

Chcete-li funkci předat jako parametr, zařídí celý řetězec `[` tak, aby parametr podrobného plánu vypadal `[[resourceGroup().tags.myTag]` . Řídicí znak způsobí, že při zpracování podrobného plánu bude v sestavách zpracována hodnota jako řetězec. Modrotisky pak umístí funkci na artefakt, což umožňuje, aby byl dynamický, jak bylo očekáváno. Další informace najdete v tématu [syntaxe a výrazy v šablonách Azure Resource Manager](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="delete-errors"></a>Odstranit chyby

### <a name="scenario-assignment-deletion-timeout"></a><a name="assign-delete-timeout"></a>Scénář: časový limit pro odstranění přiřazení

#### <a name="issue"></a>Problém

Odstranění přiřazení podrobného plánu se nedokončilo.

#### <a name="cause"></a>Příčina

Přiřazení podrobného plánu se může při odstranění zablokovat v neterminálovém stavu. Tento stav je způsobený tím, že prostředky vytvořené přiřazením podrobného plánu ještě čekají na odstranění, nebo nevrátí stavový kód do Azure modrotisky.

#### <a name="resolution"></a>Řešení

Přiřazení podrobného plánu v neterminálovém stavu se po uplynutí _6 hodin_ automaticky označí jako **neúspěšná** . Jakmile časový limit upraví stav přiřazení podrobného plánu, můžete odstranění zkusit znovu.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
- Spojte se s nástrojem [@AzureSupport](https://twitter.com/azuresupport) – oficiální Microsoft Azure účet pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure s správnými zdroji: odpověďmi, podporou a odborníky.
- Pokud potřebujete další pomoc, můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.