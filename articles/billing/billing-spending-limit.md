---
title: Limit útraty Azure | Microsoft Docs
description: Tento článek popisuje, jak funguje limit útraty Azure a jak ho odebrat.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: banders
ms.openlocfilehash: 1324b60de05805cd409975358ff78e5b2b27eef1
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70114437"
---
# <a name="azure-spending-limit"></a>Limit útraty Azure

Limit útraty v Azure zabraňuje překročení částky kreditu. Všem novým zákazníkům, kteří si zaregistrují zkušební nabídku nebo nabídky, které zahrnují kredit na několik měsíců, se standardně aktivuje limit útraty. Limit útraty je 0 USD a nedá se změnit. Nemůžete například změnit limit útraty na 100 USD. Můžete ho ale kdykoliv zrušit. To znamená, že buď nemáte žádný limit, nebo máte limit nula, který vám znemožní provádět většinu typů útraty. Limit útraty není dostupný u předplatných v některých plánech, jako jsou třeba plány závazků nebo plány průběžných plateb. Prohlédněte si [úplný seznam nabídek Azure a dostupnost limitu útraty](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Dosažení limitu útraty

Jakmile poplatky spojené s využíváním služeb vyčerpají měsíční částku, která je zahrnutá ve vašem předplatném Azure, nasazené služby se do konce příslušného fakturačního období vypnou.

Když například utratíte veškerý kredit, který je součástí vašeho předplatného, nasazené prostředky Azure se odeberou z produkce a virtuální počítače Azure se zastaví a zruší se jejich přiřazení. Data v účtech úložiště jsou dostupná jen pro čtení.

Na začátku dalšího fakturačního období se vaše předplatné znovu automaticky aktivuje v případě, že vaše nabídka předplatného obsahuje kredit na několik měsíců. Potom můžete znovu nasadit prostředky Azure a získat úplný přístup k účtům úložiště a databázím.

Při dosažení limitu útraty předplatného Azure pošle e-mailové upozornění. Přihlaste se do [Centra účtů](https://account.windowsazure.com/Subscriptions), abyste viděli upozornění pro předplatná, u kterých se dosáhlo limitu útraty.

Pokud máte bezplatné zkušební předplatné a dosáhnete limitu útraty, můžete upgradovat na plán s [průběžnými](billing-upgrade-azure-subscription.md) platbami, abyste odebrali limit útraty a předplatné se automaticky aktivovalo.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Odebrání limitu útraty v Centru účtů

Limit útraty můžete odebrat kdykoli za předpokladu, že je k vašemu předplatnému Azure přidružen platný způsob platby. U nabídek s kreditem na několik měsíců můžete limit útraty také aktivovat na začátku dalšího fakturačního období.

Pokud chcete odebrat limit útraty, postupujte takto:

1. Přihlaste se do [Centra účtů](https://account.windowsazure.com/Subscriptions).
1. Vyberte předplatné. Pokud je předplatné vypnuté z důvodu dosažení limitu útraty, klikněte na toto oznámení: **Dosáhli jste limitu útraty a předplatné se vypnulo, aby nedošlo k účtování poplatků.** Jinak v oblasti **STAV PŘEDPLATNÉHO** klikněte na **Odebrat limit útraty**.
1. Vyberte vhodnou možnost.

![Výběr možnosti pro odebrání limitu útraty](./media/billing-spending-limit/remove-spending-limit.PNG)

| Možnost | Účinek |
| --- | --- |
| Trvale odebrat limit útraty | Odebere limit útraty. Na začátku dalšího fakturačního období se automaticky neaktivuje. |
| Odebrat limit útraty pro aktuální fakturační období | Odebere limit útraty. Na začátku dalšího fakturačního období se automaticky znovu aktivuje. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Proč byste mohli chtít odebrat limit útraty

Limit útraty vám může bránit v nasazení nebo použití některých služeb od externích dodavatelů a společnosti Microsoft. Tady jsou situace, kdy byste měli limit útraty pro svoje předplatné odebrat.

-  Chcete nasadit image Microsoftu, třeba Oracle, a služby, jako je Azure DevOps Services. To téměř okamžitě způsobí, že se váš limit útraty vyčerpá a vaše předplatné se deaktivuje.
- Máte služby, jejichž provoz nechcete přerušit.
- Nechcete ztratit nastavení služeb a prostředků, jako jsou virtuální IP adresy. Při zrušení přidělení služeb a prostředků se tato nastavení ztratí.

## <a name="turn-on-the-spending-limit-after-removing"></a>Zapnutí limitu útraty po odebrání

Tato funkce je dostupná, pouze pokud byl limit útraty odebrán trvale. Změňte ji tak, aby se automaticky zapnula na začátku dalšího fakturačního období.

1. Přihlaste se do [Centra účtů](https://account.windowsazure.com/Subscriptions).
1. Možnost limitu útraty můžete změnit kliknutím na žlutou informační zprávu.
1. Zvolte možnost **Turn on spending limit in the next billing period \<datum zahájení fakturačního období\>** (Zapnout limit útraty v dalším fakturačním období).

## <a name="custom-spending-limit"></a>Vlastní limit útraty

Vlastní limity útraty nejsou k dispozici.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>Limit útraty nezabrání všem poplatkům.

[Některé externí služby publikované na Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) nelze s kredity předplatného používat a mohou se účtovat samostatné poplatky, i když máte nastavený limit útraty. Mezi příklady patří licence k sadě Visual Studio, Azure Active Directory Premium, plány podpory a většina služeb jiných výrobců. Při zřizování nové externí služby se zobrazí upozornění na samostatnou fakturaci služeb:

![Upozornění související s nákupy na Azure Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky
- Upgrade na plán s [průběžnými](billing-upgrade-azure-subscription.md) platbami
