---
title: Vysvětlení limitu útraty Azure | Dokumentace Microsoftu
description: Popisuje, jak funguje limit útraty Azure a jeho odstranění
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: genli
ms.openlocfilehash: 1eb2a3c13a1bbf7f2fd7a9691d833fc9c4b3a2b8
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44294030"
---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>Vysvětlení limitu útraty Azure a jeho odstranění

Aby se zabránilo za požadovaná částka v Azure limit útraty existuje. Všem novým zákazníkům, kteří se zaregistrují k vyzkoušení nebo nabídky, které zahrnují kredit na několik měsíců mají limit útraty ve výchozím nastavení zapnutá. Limit útraty je hodnotou 0 USD. Nelze změnit. Tento limit útraty není dostupný pro typy předplatného, jako jsou předplatná s průběžnými platbami a plány závazků. Zobrazit [úplný seznam nabídek Azure a dostupnost limitu útraty](https://azure.microsoft.com/support/legal/offer-details/).

**Hledáte upozornění fakturace?** Zobrazit [nastavení upozornění fakturace ani kredit nad rámec pro předplatná Azure](billing-set-up-alerts.md).

## <a name="what-happens-when-i-reach-the-spending-limit"></a>Co se stane, když se dosáhne limitu útraty?

Při použití výsledky v poplatky, které vyčerpání měsíčních součástí vašeho předplatného, jsou zakázané služby, které jste nasadili pro zbytek daném fakturačním období. 

Například když strávíte veškerý kredit součástí vašeho předplatného, cloudové služby, které jste nasadili, budou odebrány z produkčního prostředí a virtuální počítače Azure se zastaví a zrušit přidělení. Data v účtech úložišť a databází jsou dostupné v režimu jen pro čtení.

Na začátku dalšího fakturačního období, pokud vaše předplatné nabídka zahrnuje kredit na několik měsíců, vaše předplatné se znovu aktivuje, automaticky. Můžete znovu nasadit své cloudové služby a úplný přístup k účtům úložišť a databázím.

Při dosažení limitu útraty pro vaše předplatné se nám poslat e-mailová oznámení. Přihlaste se k [centra pro účty](https://account.windowsazure.com/Subscriptions), a najdete upozornění pro všechna předplatná, u kterých bylo dosaženo limitu útraty.

Pokud máte bezplatnou zkušební verzi a dosáhne limitu útraty, můžete si [upgradovat na průběžné platby](billing-upgrade-azure-subscription.md) odebrání limitu útraty a předplatné automaticky znovu povolena.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Odebrat limit útraty v centru účtů

Limit útraty můžete odebrat kdykoli za předpokladu, že je k vašemu předplatnému přidružen platný způsob platby. U nabídek s kreditem na několik měsíců můžete také znovu povolit limit útraty na začátku dalšího fakturačního období.

Pokud chcete odebrat limit útraty, postupujte takto:

1. Přihlaste se k [centra pro účty](https://account.windowsazure.com/Subscriptions).
1. Vyberte předplatné. Pokud předplatné je zakázané z důvodu dosažení limitu útraty, klikněte na toto oznámení: "Předplatné dosáhlo limitu útraty a byla zakázána, aby vám nezačaly nabíhat poplatky." V opačném případě klikněte na tlačítko **odebrat limit útraty** v **stav PŘEDPLATNÉHO** oblasti.
1. Vyberte vhodnou možnost.

![Vyberete možnost pro odebrání limitu útraty](./media/billing-spending-limit/remove-spending-limit.PNG)

|Možnost|Efekt|
|-------|-----|
|Odebrat limit útraty neomezeně|Odebere limit útraty. Na začátku dalšího fakturačního období se automaticky neaktivuje.|
|Odebrat limit útraty pro aktuální fakturační období|Odebere limit útraty. Na začátku dalšího fakturačního období se automaticky znovu aktivuje.|

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="why-would-i-want-to-remove-the-spending-limit"></a>Proč byste měli odebrat limit útraty?

Limit útraty vám může bránit v nasazení nebo použití některých třetích stran a služby Microsoftu. Tady jsou scénáře, kdy byste měli limit útraty pro svoje předplatné odebrat.

* Máte v plánu nasadit Image Microsoftu jako jsou Oracle a služeb, jako je Azure DevOps služby. V tomto scénáři způsobí, že překročíte limit útraty téměř okamžitě a způsobí, že vaše předplatné se deaktivuje.
* Používáte ale služby, které nejde přerušit.
* Nechcete ztratit nastavení služeb a prostředků, jako jsou virtuální IP adresy. Tato nastavení jsou ztraceny po zrušení přidělení služeb a prostředků.

### <a name="how-do-i-turn-on-the-spending-limit-after-removing-it"></a>Jak zapnout po odebrání limitu útraty?

Tato funkce je dostupná pouze v případě, že byla odebrána limit útraty neomezeně dlouho. Změňte ho chcete zapnout automaticky na začátku dalšího fakturačního období.

1. Přihlaste se k [centra pro účty](https://account.windowsazure.com/Subscriptions).
1. Klikněte na tlačítko žlutý pruh, chcete-li změnit možnost limit útraty.
1. Zvolte **zapnout v dalším fakturačním obdobím limit útraty \<počáteční datum fakturačního období\>**

### <a name="how-do-i-set-a-custom-spending-limit"></a>Jak nastavit vlastní limitu útraty?

Nemáme vlastní omezení výdajů ještě dnes. Však mohou přihlásit a [používat upozornění fakturace na ovládací prvek vaše věnovat](billing-set-up-alerts.md).

### <a name="does-the-spending-limit-prevent-all-charges-from-azure"></a>Nebrání limit útraty všechny poplatky z Azure?

[Některé externí služby publikovaných na webu Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) nelze použít s kreditů předplatného a můžete samostatně účtovat poplatky i v případě, že svůj limit útraty nastavený. Mezi příklady patří licence Visual Studio, Azure Active Directory premium, plány podpory a většina služeb jiných třetích stran. Při zřizování nové externí služby se zobrazí upozornění, že služby se účtují samostatně:

![Marketplace zakoupit upozornění](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.
