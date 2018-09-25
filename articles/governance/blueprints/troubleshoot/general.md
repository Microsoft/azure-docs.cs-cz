---
title: Řešení potíží s chybami pomocí Azure podrobné plány
description: Informace o řešení problémů vytvářet a přiřazovat podrobné plány.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: dd1163ece225c2e9a9b082f5e8364f34b06a10ae
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982286"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Řešení potíží s chybami pomocí Azure podrobné plány

Může dojít k chybám při vytváření nebo přiřazení podrobné plány. Tento článek popisuje různé chyby, které mohou nastat a způsob jejich řešení.

## <a name="finding-error-details"></a>Jak najít podrobnosti o chybě

Mnoho chyb bude výsledek přiřazení podrobný plán oboru. Při selhání přiřazení podrobného plánu obsahuje podrobné informace o selhání nasazení. Tyto informace budou indikovat problém tak, aby ho mohli opravit a následné nasazení proběhne úspěšně.

1. Spusťte službu Azure plány na webu Azure Portal kliknutím na **všechny služby** a vyhledáte a vyberete **zásady** v levém podokně. Na **zásady** stránky, klikněte na **plány**.

1. Vyberte **přiřazené podrobné plány** ze stránky na levé straně a použijte pole hledání k filtrování přiřazení podrobného plánu se najít přiřazení se nezdařilo. Můžete také řadit tabulce přiřazení podle **Stav zřizování** sloupec zobrazíte všechny neúspěšné přiřazení seskupené dohromady.

1. Klepněte na položku na podrobného plánu se _neúspěšné_ stavu nebo kliknutím pravým tlačítkem a vyberte **zobrazit podrobnosti o přiřazení**.

1. V horní části podrobný plán přiřazení stránka je červený nápis upozornění, že má přiřazování neproběhlo úspěšně. Klikněte na libovolné místo na banner, abyste získali více podrobností.

Je běžné, že chybu způsobuje artefakt součástí podrobný plán a není podrobný plán jako celek. Například pokud podrobný plán obsahuje artefakt k vytvoření služby Key Vault, ale vytváření služby Key Vault je zabráněno Azure Policy, selže celý přiřazení.

## <a name="general-errors"></a>Obecné chyby

### <a name="policy-violation"></a>Scénář: Porušení zásad

#### <a name="issue"></a>Problém

Nasazení šablony se nezdařilo z důvodu porušení zásad.

#### <a name="cause"></a>Příčina

Zásady dojít ke konfliktu se nasazení pro z několika důvodů:

- Prostředku vytvořeného omezil zásad (obvykle omezení SKU nebo umístění)
- Nasazení je nastavení polí, které jsou nakonfigurované zásady (běžné u značky)

#### <a name="resolution"></a>Řešení

Upravte podrobný plán nebude v konfliktu s zásady uvedené v informacích o chybě. Pokud to není možné, je alternativní možnosti oboru přiřazení zásady změněna tak podrobný plán je již nejsou v konfliktu s zásady.

## <a name="next-steps"></a>Další postup

Pokud nenalezli váš problém nebo nepovedlo se vyřešit vaše potíže, navštíví některý z následujících kanálů pro další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
- Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
- Pokud potřebujete další pomoc, můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.