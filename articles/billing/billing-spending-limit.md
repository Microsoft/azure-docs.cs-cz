---
title: Pochopení Azure útrat | Microsoft Docs
description: Popisuje, jak funguje Azure útrat a jak ho odebrat
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
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 6049e3614b63bfabee6721dcaa83008eb3306493
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>Pochopení omezení a jak ho odebrat útraty u Azure

Aby se zabránilo výdaje po dobu vaší platební útrat v Azure existuje. Všechny nové zákazníci, kteří si zaregistrovat k vyzkoušení nebo nabídky, které zahrnuje kredity přes několik měsíců mají limit útraty ve výchozím nastavení zapnuto. Limit útraty je $0. Nelze změnit. Tento limit útraty není dostupný pro typy předplatného, jako jsou předplatná s průběžnými platbami a plány závazků. Najdete v článku [úplný seznam Azure nabízí a dostupnost limit útraty](https://azure.microsoft.com/support/legal/offer-details/).

**Hledáte fakturace výstrahy?** V tématu [nastavit fakturace nebo platební výstrahy pro předplatná Azure](billing-set-up-alerts.md).

## <a name="what-happens-when-i-reach-the-spending-limit"></a>Co se stane, když se dosáhne limitu útraty?

Při použití výsledky v poplatky, které vyčerpat měsíční množství součástí vašeho předplatného, jsou zakázané služby, které jste nasadili pro zbytek této fakturačního období. 

Například když věnujete se kredit součástí vašeho předplatného, cloudové služby, které jste nasadili se odeberou z výroby a virtuální počítače Azure se zastaví a zrušte přidělené. Data v účtech úložiště a databáze jsou k dispozici v režimu jen pro čtení.

Na začátku další fakturační období, pokud vaše předplatné nabídka zahrnuje kredity přes několik měsíců, vaše předplatné by být funkce automaticky znovu povolena. Pak můžete znovu nasadit cloudové služby a mají plný přístup ke své účty úložiště a databáze.

Můžeme zasílat e-mailová oznámení, když dosáhl limitu útraty pro předplatné. Přihlaste se k [centra účtů](https://account.windowsazure.com/Subscriptions), a zobrazit oznámení o předplatných, které bylo dosaženo limitu útraty.

Pokud máte bezplatnou zkušební verzi a dosažení limitu útraty, můžete [upgradovat na průběžné platby](billing-upgrade-azure-subscription.md) limit útraty odebrat a mít předplatné automaticky znovu povolena.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Odeberte limit útraty v centru účtů

Limit útraty můžete odebrat kdykoli za předpokladu, že je k vašemu předplatnému přidružen platný způsob platby. Nabídky, které mají platební přes několik měsíců můžete také znovu povolit limit útraty na začátku vaší další fakturačního období.

Pokud chcete odebrat limit útraty, postupujte takto:

1. Přihlaste se k [centra účtů](https://account.windowsazure.com/Subscriptions).
1. Vyberte předplatné.
. Pokud předplatné je zakázané kvůli dosažení limit útraty, klikněte na toto oznámení: "Předplatné dosáhla limitu útraty a byl zakázán nabíhat poplatky." Jinak, klikněte na tlačítko **odebrat útrat** v **stav PŘEDPLATNÉHO** oblasti.
1. Vyberte vhodnou možnost.

![Vyberete možnost pro odebrání útrat](./media/billing-spending-limit/remove-spending-limit.PNG)

|Možnost|Efekt|
|-------|-----|
|Odebrat limit útraty neomezeně|Odebere limit útraty. Na začátku dalšího fakturačního období se automaticky neaktivuje.|
|Odebrat limit útraty pro aktuální fakturační období|Odebere limit útraty. Na začátku dalšího fakturačního období se automaticky znovu aktivuje.|

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="why-would-i-want-to-remove-the-spending-limit"></a>Proč by se limit útraty odebrat?

Limit útraty může zabránit vám v nasazení nebo pomocí určitých třetích stran a služby společnosti Microsoft. Tady jsou scénáře, kdy byste měli limit útraty pro svoje předplatné odebrat.

* Chcete nasadit image Microsoftu, třeba Oracle, a služby, jako je Visual Studio Team Services. V tomto scénáři způsobí, že je delší než limit útraty téměř okamžitě a způsobí, že vaše předplatné se zakáže.
* Používáte ale služby, které nejde přerušit.
* Nechcete ztratit nastavení služeb a prostředků, jako jsou virtuální IP adresy. Tato nastavení jsou ztraceny, když jsou navrátit služby a prostředky.

### <a name="how-do-i-turn-on-the-spending-limit-after-removing-it"></a>Jak se zapíná limit útraty po odebrání?

Tato funkce je dostupná jenom v případě, že limit útraty byla odebrána po neomezenou dobu. Změňte ho chcete zapnout automaticky při spuštění další fakturačního období.

1. Přihlaste se k [centra účtů](https://account.windowsazure.com/Subscriptions).
1. Klikněte na tlačítko žlutý informační zpráva, chcete-li změnit možnosti limit útraty.
1. Zvolte **zapnout útrat v další fakturační období \<počáteční datum fakturační období\>**

### <a name="how-do-i-set-a-custom-spending-limit"></a>Jak lze nastavit vlastní limitu útraty a automaticky?

Nemáme vlastní tráví omezení ještě dnes. Ale můžete se rozhodnout v [použít fakturace výstrahy na ovládací prvek vaší tráví](billing-set-up-alerts.md).

### <a name="does-the-spending-limit-prevent-all-charges-from-azure"></a>Limit útraty zabránit všechny poplatky z Azure?

[Některé externí služby v Azure Marketplace publikovat](billing-understand-your-azure-marketplace-charges.md) nelze použít s vaší kreditů předplatného a může způsobit samostatné poplatky, i když je nastaven limit útraty. Mezi příklady patří licence Visual Studio, Azure Active Directory premium, plánům podpory a většina značky služby třetích stran. Při zřizování nové externí služby se zobrazí varování, vám oznamuje, že služby se účtují samostatně:

![Marketplace zakoupit upozornění](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.
