---
title: Odstraňování běžných chyb
description: Naučte se řešit problémy při vytváření, přiřazování a odebírání podrobných plánů, jako jsou porušení zásad a funkce parametrů podrobného plánu.
ms.date: 11/22/2019
ms.topic: troubleshooting
ms.openlocfilehash: 4e7ea1760e000a167c4329d6f12f3acc18d18f7c
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406614"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Řešení chyb pomocí Azure modrotisky

Při vytváření nebo přiřazování podrobných plánů můžete spustit chybu. Tento článek popisuje různé chyby, ke kterým může dojít, a jejich řešení.

## <a name="finding-error-details"></a>Hledání podrobností o chybě

Mnohé chyby budou výsledkem přiřazení podrobného plánu k oboru. Pokud přiřazení neproběhne úspěšně, podrobný plán poskytuje podrobnosti o neúspěšném nasazení. Tyto informace označují problém tak, aby se mohl opravit a další nasazení bude úspěšné.

1. V levém podokně vyberte **všechny služby** . Vyhledejte a vyberte **plány**.

1. Na stránce vlevo vyberte **přiřazené plány** a pomocí vyhledávacího pole vyfiltrujte přiřazení podrobného plánu, abyste našli neúspěšné přiřazení. Můžete také seřadit tabulku přiřazení podle sloupce **stav zřizování** a zobrazit tak všechna neúspěšná přiřazení seskupená dohromady.

1. Klikněte levým tlačítkem na podrobný plán s _neúspěšným_ stavem nebo klikněte pravým tlačítkem a vyberte **Zobrazit podrobnosti o přiřazení**.

1. Červená zpráva s upozorněním, že přiřazení se nezdařilo, je v horní části stránky přiřazení podrobného plánu. Kliknutím kamkoli na informační proužek získáte další podrobnosti.

Je běžné, že chyba je způsobena artefaktem a nikoli úplným plánem. Pokud artefakt vytvoří Key Vault a Azure Policy znemožňuje vytváření Key Vault, celé přiřazení selže.

## <a name="general-errors"></a>Obecné chyby

### <a name="policy-violation"></a>Scénář: porušení zásad

#### <a name="issue"></a>Problém

Nasazení šablony se nepovedlo kvůli porušení zásad.

#### <a name="cause"></a>Příčina

Zásada může být v konfliktu s nasazením z několika důvodů:

- Vytvářený prostředek je omezený zásadami (obvykle se jedná o omezení umístění SKU nebo místa).
- Nasazení je nastavené pole, která jsou nakonfigurovaná pomocí zásad (společné pomocí značek).

#### <a name="resolution"></a>Řešení

Změňte podrobný plán tak, aby nedošlo ke konfliktu se zásadami v podrobnostech o chybě. Pokud tuto změnu nemůžete udělat, je alternativním parametrem, že se změní rozsah přiřazení zásady, takže podrobný plán už není v konfliktu se zásadami.

### <a name="escape-function-parameter"></a>Scénář: parametr podrobného plánu je funkce

#### <a name="issue"></a>Problém

Parametry podrobného plánu, které jsou funkce, jsou zpracovány před předáním artefaktům.

#### <a name="cause"></a>Příčina

Předáním parametru podrobného plánu, který používá funkci, jako je například `[resourceGroup().tags.myTag]`, do výsledku artefaktu dojde ke zpracování výsledku funkce, která je nastavena na artefaktu namísto dynamické funkce.

#### <a name="resolution"></a>Řešení

Chcete-li funkci předat jako parametr, zařídí celý řetězec `[` tak, aby parametr podrobného plánu vypadal jako `[[resourceGroup().tags.myTag]`. Řídicí znak způsobí, že při zpracování podrobného plánu bude v sestavách zpracována hodnota jako řetězec. Modrotisky pak umístí funkci na artefakt, což umožňuje, aby byl dynamický, jak bylo očekáváno. Další informace najdete v tématu [syntaxe a výrazy v šablonách Azure Resource Manager](../../../azure-resource-manager/template-expressions.md).

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
- Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
- Pokud potřebujete další pomoc, můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.