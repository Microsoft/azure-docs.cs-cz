---
title: Limit útraty Azure | Microsoft Docs
description: Tento článek popisuje, jak funguje limit útraty Azure a jak ho odebrat.
author: bandersmsft
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: banders
ms.openlocfilehash: 0983e21e5ab2e895b2c12fe7cc45c5b9c9f0a405
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223580"
---
# <a name="azure-spending-limit"></a>Limit útraty Azure

Limit útraty v Azure zabraňuje překročení částky kreditu. Všem novým zákazníkům, kteří si zaregistrují bezplatný účet Azure nebo typy předplatných, které zahrnují kredit na několik měsíců, se standardně aktivuje limit útraty. Limit útraty odpovídá výši kreditu a není možné ho změnit. Pokud jste si například zaregistrovali bezplatný účet Azure, váš limit útraty je 200 USD a není možné ho změnit na 500 USD. Můžete ho ale kdykoliv zrušit. To znamená, že buď nemáte žádný limit, nebo máte limit odpovídající výši kreditu. Tento limit vám znemožní provádět většinu typů útraty. Limit útraty není dostupný u předplatných s plány závazků nebo s průběžnými platbami. Prohlédněte si [úplný seznam typů předplatného Azure a dostupnost limitu útraty](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Dosažení limitu útraty

Jakmile poplatky spojené s využíváním služeb vyčerpají limit útraty, nasazené služby se do konce příslušného fakturačního období vypnou.

Když například utratíte veškerý kredit, který je součástí vašeho bezplatného účtu Azure, nasazené prostředky Azure se odeberou z produkčního prostředí a virtuální počítače Azure se zastaví a zruší se jejich přidělení. Data v účtech úložiště jsou dostupná jen pro čtení.

Pokud váš typ předplatného zahrnuje kredit na několik měsíců, vaše předplatné znovu automaticky aktivuje v případě na začátku dalšího fakturačního období. Potom můžete znovu nasadit prostředky Azure a získat úplný přístup k účtům úložiště a databázím.

Při dosažení limitu útraty Azure posílá e-mailová oznámení. Pokud chcete zobrazit oznámení o předplatných, u kterých došlo k dosažení limitu útraty, přihlaste se k webu [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

Pokud jste si zaregistrovali bezplatný účet Azure a dosáhnete limitu útraty, můžete upgradovat na [průběžné](billing-upgrade-azure-subscription.md) platby, aby se odebral limit útraty a předplatné se automaticky znovu aktivovalo.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-azure-portal"></a>Odebrání limitu útraty na webu Azure Portal

Limit útraty můžete odebrat kdykoli za předpokladu, že je k vašemu předplatnému Azure přidružen platný způsob platby. U typů předplatného s kreditem na několik měsíců, jako jsou předplatná sady Visual Studio Enterprise nebo Visual Studio Professional, můžete limit útraty povolit také na začátku dalšího fakturačního období.

Pokud chcete odebrat limit útraty, postupujte takto:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Vyberte předplatné. Pokud u předplatného dojde k dosažení limitu útraty, předplatné se deaktivuje.
1. V horní části stránky vyberte **Odebrat limit útraty**.
1. Vyberte vhodnou možnost.

![Výběr možnosti pro odebrání limitu útraty](./media/billing-spending-limit/remove-spending-limit.PNG)

| Možnost | Účinek |
| --- | --- |
| Trvale odebrat limit útraty | Odebere limit útraty. Na začátku dalšího fakturačního období se automaticky neaktivuje. |
| Odebrat limit útraty pro aktuální fakturační období | Odebere limit útraty. Na začátku dalšího fakturačního období se automaticky znovu aktivuje. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Proč byste mohli chtít odebrat limit útraty

Limit útraty vám může bránit v nasazení nebo použití některých služeb od externích dodavatelů a společnosti Microsoft. Tady jsou situace, kdy byste měli limit útraty pro svoje předplatné odebrat.

-  Chcete nasadit image Microsoftu, třeba Oracle, nebo služby, jako je Azure DevOps Services. To téměř okamžitě způsobí, že dosáhnete svého limitu útraty a vaše předplatné se deaktivuje.
- Máte služby, jejichž provoz nechcete přerušit. Když dosáhnete limitu útraty, nasazené prostředky Azure se odeberou z produkčního prostředí a virtuální počítače Azure se zastaví a zruší se jejich přidělení. Pokud máte služby, jejichž provoz nechcete přerušit, musíte limit útraty odebrat.
- Nechcete ztratit nastavení služeb a prostředků, jako jsou virtuální IP adresy. Při dosažení limitu útraty se zruší přidělení služeb a prostředků a tato nastavení se ztratí.

## <a name="turn-on-the-spending-limit-after-removing"></a>Zapnutí limitu útraty po odebrání

Tato funkce je k dispozici pouze v případě trvalého odebrání limitu útraty pro typy předplatného, které zahrnují kredit na několik měsíců. Pomocí této funkce můžete automaticky zapnout limit útraty na začátku dalšího fakturačního období.

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
