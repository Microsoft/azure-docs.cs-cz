---
title: Odstraňování běžných chyb
description: Informace o řešení problémů vytvářet a přiřazovat podrobné plány.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/11/2018
ms.topic: troubleshooting
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 04c038eb11cc40cec3552feff183bea55b22bb57
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54261923"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Řešení potíží s chybami pomocí Azure podrobné plány

Pravděpodobně dojde k chybám při vytváření nebo přiřazení podrobné plány. Tento článek popisuje různé chyby, které mohou nastat a způsob jejich řešení.

## <a name="finding-error-details"></a>Jak najít podrobnosti o chybě

Mnoho chyb bude výsledek přiřazení podrobný plán oboru. Při přiřazení selže, podrobný plán poskytuje podrobnosti o selhání nasazení. Tato informace indikuje problém tak, aby ho mohli opravit a úspěšném další nasazení.

1. Klikněte na **Všechny služby** a v levém podokně vyhledejte a vyberte **Zásady**. Na stránce **Zásady** klikněte na **plány**.

1. Vyberte **přiřazené podrobné plány** ze stránky na levé straně a použijte pole hledání k filtrování přiřazení podrobného plánu se najít přiřazení se nezdařilo. Můžete také řadit tabulce přiřazení podle **Stav zřizování** sloupec zobrazíte všechny neúspěšné přiřazení seskupené dohromady.

1. Klepněte na položku na podrobného plánu se _neúspěšné_ stavu nebo kliknutím pravým tlačítkem a vyberte **zobrazit podrobnosti o přiřazení**.

1. Červený nápis upozornění, že byla neúspěšná přiřazení je v horní části stránky přiřazení podrobného plánu. Klikněte na libovolné místo na banner, abyste získali více podrobností.

Je běžné, že chybu způsobuje artefakt a není podrobný plán jako celek. Pokud Key Vault vytvoří artefakt a Azure Policy brání vytváření služby Key Vault, celý přiřazení selže.

## <a name="general-errors"></a>Obecné chyby

### <a name="policy-violation"></a>Scénář: Porušení zásad

#### <a name="issue"></a>Problém

Nasazení šablony se nezdařilo z důvodu porušení zásad.

#### <a name="cause"></a>Příčina

Zásady dojít ke konfliktu se nasazení pro z několika důvodů:

- Prostředku vytvořeného omezil zásad (obvykle omezení SKU nebo umístění)
- Nasazení je nastavení polí, které jsou nakonfigurované zásady (běžné u značky)

#### <a name="resolution"></a>Řešení

Podrobný plán měnit, takže není v konfliktu se zásadami v podrobnostech o chybě. Pokud tuto změnu není možné, je alternativní možnost oboru přiřazení zásady změněna tak podrobný plán je již nejsou v konfliktu s zásady.

### <a name="escape-function-parameter"></a>Scénář: Parametr podrobného plánu se funkce

#### <a name="issue"></a>Problém

Parametry podrobného plánu, které jsou funkce se zpracovávají před předáním artefakty.

#### <a name="cause"></a>Příčina

Předá parametr podrobného plánu, který používá funkce, jako například `[resourceGroup().tags.myTag]`do artefakt výsledky zpracování výsledku funkce nastavena na artefakt místo dynamickou funkci.

#### <a name="resolution"></a>Řešení

Funkce prostřednictvím předat jako parametr, řídicí celý řetězec s `[` tak, aby parametr podrobného plánu vypadá jako `[[resourceGroup().tags.myTag]`. Řídicí znak způsobí, že podrobné plány. při zpracování podrobný plán považovat hodnotu jako řetězec. Podrobné plány. pak vloží funkci na artefakt, což umožňuje být dynamické podle očekávání.

## <a name="next-steps"></a>Další postup

Pokud nezobrazila váš problém nebo nelze vyřešit vaše potíže, navštíví některý z následujících kanálů pro další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
- Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
- Pokud potřebujete další pomoc, můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.