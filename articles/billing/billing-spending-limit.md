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
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114437"
---
# <a name="azure-spending-limit"></a>Limit útraty Azure

Limit útraty v Azure brání útratě na částku vašeho kreditu. Všichni noví zákazníci, kteří si zaregistrují zkušební verzi Azure nebo nabídky, které zahrnují kredity za více měsíců, mají ve výchozím nastavení povolený limit útraty. Limit útraty je $0 a nedá se změnit. Například nemůžete změnit limit útraty na $100. Limit útraty ale můžete odebrat. To znamená, že buď nemáte žádné omezení, nebo máte limit nula, který vám znemožní z většiny druhů útraty. Limit útraty není v některých plánech k dispozici pro předplatná, jako jsou plány a plány závazku a ceny s průběžnými platbami. Podívejte se na [úplný seznam nabídek Azure a dostupnost limitu útraty](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Dosažení limitu útraty

Když se v důsledku použití účtují poplatky za vyčerpání měsíčních částek, které jsou součástí vašeho předplatného Azure, služby, které jste nasadili, se po zbytek tohoto fakturačního období zakážou.

Pokud například strávíte veškerý kredit, který je součástí vašeho předplatného, prostředky Azure, které jste nasadili, se odeberou z produkčního prostředí a virtuální počítače Azure se zastaví a zruší se přidělení. Data v účtech úložiště jsou k dispozici jen pro čtení.

Pokud vaše nabídka předplatného zahrnuje kredity za více měsíců, vaše předplatné se automaticky znovu aktivuje na začátku dalšího fakturačního období. Pak můžete znovu nasadit prostředky Azure a mít úplný přístup k vašim účtům a databázím úložiště.

Azure posílá e-mailová oznámení při dosažení limitu útraty pro vaše předplatné. Přihlaste se k [centru účtů](https://account.windowsazure.com/Subscriptions) , abyste viděli oznámení o předplatných, u kterých se dosáhlo limitu útraty.

Pokud máte bezplatné zkušební předplatné a dosáhnete limitu útraty, můžete upgradovat na plán s průběžnými [platbami](billing-upgrade-azure-subscription.md) a odebrat limit útraty a automaticky povolit předplatné.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Odebrat limit útraty v centru účtů

Limit útraty můžete kdykoli odebrat, pokud je k vašemu předplatnému Azure přidružený platný způsob platby. U nabídek, které mají kredit za více měsíců, můžete také povolit limit útraty na začátku dalšího fakturačního období.

Pokud chcete odebrat limit útraty, postupujte takto:

1. Přihlaste se k [centru účtů](https://account.windowsazure.com/Subscriptions).
1. Vyberte předplatné. Pokud je předplatné zakázané, protože se dosáhlo limitu útraty, klikněte na oznámení: **Předplatné dosáhlo limitu útraty a bylo zakázáno, aby nedocházelo k poplatkům.** V opačném případě klikněte v oblasti **stavu** předplatného na **Odebrat limit útraty** .
1. Vyberte vhodnou možnost.

![Výběr možnosti pro odebrání limitu útraty](./media/billing-spending-limit/remove-spending-limit.PNG)

| Možnost | Efekt |
| --- | --- |
| Odebrat limit útraty neomezeně | Odebere limit útraty. Na začátku dalšího fakturačního období se automaticky neaktivuje. |
| Odebrat limit útraty pro aktuální fakturační období | Odebere limit útraty. Na začátku dalšího fakturačního období se automaticky znovu aktivuje. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Proč možná budete chtít limit útraty odebrat

Limit útraty by vám mohl zabránit v nasazení nebo používání určitých služeb třetích stran a služeb Microsoftu. Tady jsou situace, kdy byste měli ve svém předplatném odebrat limit útraty.

-  Plánujete nasazení imagí první strany, jako je Oracle a služby, jako je například Azure DevOps Services. Tato situace způsobí, že byste překročili limit útraty téměř okamžitě a způsobí, že se vaše předplatné zakáže.
- Máte služby, které nechcete přerušit.
- Nechcete ztratit nastavení služeb a prostředků, jako jsou virtuální IP adresy. Tato nastavení se ztratí, když jsou služby a prostředky nepřiděleny.

## <a name="turn-on-the-spending-limit-after-removing"></a>Zapnout limit útraty po odebrání

Tato funkce je dostupná jenom v případě, že limit útraty se odebraly po neomezenou dobu. Změní se na automatické zapnutí na začátku dalšího fakturačního období.

1. Přihlaste se k [centru účtů](https://account.windowsazure.com/Subscriptions).
1. Kliknutím na žlutou hlavičku změňte možnost limit útraty.
1. **V \<dalším počátečním datu\>**  fakturačního období vyberte Zapnout limit útraty.

## <a name="custom-spending-limit"></a>Vlastní limit útraty

Vlastní limity útraty nejsou k dispozici.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>Limit útraty nebrání všem poplatkům.

[Některé externí služby, které jsou publikované v Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) , se nedají používat s kredity předplatného a můžou se účtovat za samostatné poplatky, i když je nastavený limit útraty. Mezi příklady patří licence pro Visual Studio, Azure Active Directory Premium, plány podpory a většina služeb, které jsou ve značkách třetích stran. Když zřizujete novou externí službu, zobrazí se upozornění, které vám umožní zjistit, že se služby účtují samostatně:

![Upozornění nákupu Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky
- Upgradujte na plán s [průběžnými platbami](billing-upgrade-azure-subscription.md) .
