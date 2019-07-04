---
title: Limit útraty Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak funguje Azure limitu útraty a jeho odstranění.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: fb43f29827309fc8986ee6b4653f5edf303cc21d
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490601"
---
# <a name="azure-spending-limit"></a>Limit útraty Azure

Limit útraty v Azure brání za vašeho kreditu. Všem novým zákazníkům, kteří zaregistrovat zkušební verzi Azure nebo nabídek, které zahrnují kredit na několik měsíců mají limit útraty ve výchozím nastavení zapnutá. Limit útraty je hodnotou 0 USD. Nelze změnit. Limit útraty není dostupný pro předplatná v rámci některých plánů tyto závazky a plány díky plánu průběžných plateb. Zobrazit [úplný seznam nabídek Azure a dostupnost limitu útraty](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Dosažení limitu útraty

Při použití výsledky v poplatky, které vyčerpání měsíčních součástí vašeho předplatného Azure, služby, které jste nasadili jsou zakázané pro zbytek daném fakturačním období.

Například když strávíte veškerý kredit součástí vašeho předplatného, odeberou se prostředky Azure, které jste nasadili v produkčním prostředí a virtuální počítače Azure se zastaví a zrušit přidělení. Data v účtu úložiště jsou dostupné jen pro čtení.

Na začátku dalšího fakturačního období, pokud vaše předplatné nabídka zahrnuje kredit na několik měsíců, vaše předplatné se znovu povolí automaticky. Můžete znovu nasadit prostředky Azure a úplný přístup k účtům úložišť a databázím.

Azure odesílá e-mailové oznámení, když se dostanete útraty limit pro vaše předplatné. Přihlaste se do [centra pro účty](https://account.windowsazure.com/Subscriptions) umožníte zobrazování oznámení o předplatné, které bylo dosaženo limitu útraty.

Pokud máte bezplatnou zkušební verzi předplatného a dosáhne limitu útraty, můžete upgradovat na plán s [s průběžnými platbami](billing-upgrade-azure-subscription.md) ceny útraty odebrat omezení a automaticky povolí předplatné.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Odebrat limit útraty v centru účtů

Můžete odebrat limit útraty v každém okamžiku tak dlouho, dokud není platným způsobem platby přidružený k vašemu předplatnému Azure. U nabídek s kreditem na několik měsíců můžete také povolit limit útraty na začátku dalšího fakturačního období.

Pokud chcete odebrat limit útraty, postupujte takto:

1. Přihlaste se do [centra pro účty](https://account.windowsazure.com/Subscriptions).
1. Vyberte předplatné. Pokud předplatné je zakázané z důvodu dosažení limitu útraty, klikněte na oznámení: **Předplatné dosáhlo limitu útraty a byla zakázána, aby vám nezačaly nabíhat poplatky.** V opačném případě klikněte na tlačítko **odebrat limit útraty** v **stav PŘEDPLATNÉHO** oblasti.
1. Vyberte vhodnou možnost.

![Vyberete možnost pro odebrání limitu útraty](./media/billing-spending-limit/remove-spending-limit.PNG)

| Možnost | Efekt |
| --- | --- |
| Odebrat limit útraty neomezeně | Odebere limit útraty. Na začátku dalšího fakturačního období se automaticky neaktivuje. |
| Odebrat limit útraty pro aktuální fakturační období | Odebere limit útraty. Na začátku dalšího fakturačního období se automaticky znovu aktivuje. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Proč můžete chtít odebrat limit útraty

Limit útraty vám může bránit v nasazení nebo použití některých třetích stran a služby Microsoftu. Tady je situace, kdy byste měli odebrat limit útraty v rámci předplatného.

-  Máte v plánu nasadit Image Microsoftu jako jsou Oracle a služeb, jako je Azure DevOps služby. Tato situace způsobí, že překročíte limit útraty téměř okamžitě a způsobí, že vaše předplatné se deaktivuje.
- Máte služby, které nechcete přerušit.
- Nechcete ztratit nastavení služeb a prostředků, jako jsou virtuální IP adresy. Tato nastavení jsou ztraceny po zrušení přidělení služeb a prostředků.

## <a name="turn-on-the-spending-limit-after-removing"></a>Zapnout po odebrání limitu útraty

Tato funkce je dostupná pouze v případě, že byla odebrána limit útraty neomezeně dlouho. Změňte ho chcete zapnout automaticky na začátku dalšího fakturačního období.

1. Přihlaste se do [centra pro účty](https://account.windowsazure.com/Subscriptions).
1. Klikněte na tlačítko žlutý pruh, chcete-li změnit možnost limit útraty.
1. Zvolte **zapnout v dalším fakturačním obdobím limit útraty \<počáteční datum fakturačního období\>**

## <a name="custom-spending-limit"></a>Vlastní limit útraty

Vlastní limity útraty nejsou k dispozici.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>Limit útraty nezabrání všechny poplatky za

[Některé externí služby publikovaných na webu Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) nelze použít s kreditů předplatného a můžete samostatně účtovat poplatky i v případě, že svůj limit útraty nastavený. Mezi příklady patří licence Visual Studio, Azure Active Directory premium, plány podpory a většina služeb jiných třetích stran. Při zřizování nové externí služby se zobrazí upozornění, že služby se účtují samostatně:

![Marketplace zakoupit upozornění](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup
- Upgrade na plán s [s průběžnými platbami](billing-upgrade-azure-subscription.md) ceny.
