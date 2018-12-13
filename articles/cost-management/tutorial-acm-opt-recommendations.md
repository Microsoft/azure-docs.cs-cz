---
title: Kurz – snížit náklady na Azure s doporučeními optimalizace | Dokumentace Microsoftu
description: Tento kurz pomůže snížit náklady na Azure, pokud budete jednat doporučení pro optimalizaci.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/07/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 2b9702dbae0414ba597b6e1f6080d9de86f624fc
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53077068"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Kurz: Optimalizace nákladů od doporučení.

Azure Cost Management pracuje s využitím Azure Advisoru poskytnout doporučení pro optimalizaci nákladů. Azure Advisor vám pomůže optimalizovat a zvýšení efektivity díky identifikaci nečinnosti a nedostatečně využité prostředky. Tento kurz vás provede příkladem, kdy identifikaci nevyužitých prostředků Azure a pak přijmete opatření ke snížení nákladů.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zobrazení nákladů optimalizace doporučení zobrazíte potenciální nedostatečné efektivity využití
> * Reagovat na doporučení pro změnu velikosti virtuálního počítače do více cenově výhodnější variantou
> * Ověření akce a ujistěte se, že virtuální počítač byl úspěšně se změněnou velikostí

## <a name="prerequisites"></a>Požadavky
Doporučení jsou dostupná pro všechny zákazníky se [smlouvou Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/). Abyste mohli zobrazit data nákladů, musíte mít alespoň přístup pro čtení k nejméně jednomu z následujících oborů.

- Předplatné
- Skupina prostředků

Musíte mít aktivní virtuální počítače s aspoň 14 dnů od aktivity.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com/).

## <a name="view-cost-optimization-recommendations"></a>Zobrazit doporučení pro optimalizaci nákladů

Na webu Azure Portal klikněte v seznamu služeb na **Cost Management a fakturace**. Pak v seznamu **Cost Management**vyberte **doporučení Advisoru**. Doporučení advisoru náklady jsou zobrazeny.

![Doporučení k cenám Management Advisor webu Azure Portal](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

Seznam doporučení identifikuje nedostatečné efektivity využití, nebo obsahuje doporučení pro nákup, které vám pomůžou ušetřit Další. Součty **potenciální roční úspory** ukazuje celkovou velikost, který můžete uložit, pokud vypnete nebo uvolnit všechny vaše virtuální počítače, které splňují pravidla doporučení. Pokud nechcete, aby je vypnout, měli byste zvážit, změna velikosti je levnější skladovou Položku virtuálního počítače.

**Dopad** kategorie, spolu s **potenciální roční úspory**, slouží k identifikaci doporučení, které by mohly uložit co největší míře. Vysoký dopad doporučení jsou [nákup rezervované instance virtuálních počítačů ušetříte peníze, než byste platili s průběžnými platbami](../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) a [optimalizovat virtuální počítač výdaje a prostřednictvím Změna velikosti nebo vypnutí instance nedostatečně využité](../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances). Střední dopad doporučení jsou [snižovat náklady díky tomu zrušení zřízené okruhy ExpressRoute, že](../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits) a [snižují náklady na odstranění nebo změna konfigurace nečinných bran virtuální sítě](../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways).

## <a name="act-on-a-recommendation"></a>Reagovat na doporučení

Azure Advisor sleduje využívání virtuálních počítačů po dobu 14 dnů a pak identifikuje nedostatečně využité virtuální počítače. Virtuální počítače, jejichž využití procesoru je pět nebo míň procento a využívání sítě je 7 MB nebo méně čtyři nebo více dny jsou považovány za nízké využití virtuálních počítačů.

5 % nebo méně nastavení využití procesoru je výchozí nastavení, ale můžete upravit nastavení. Další informace o úpravě nastavení, najdete v článku [konfiguraci pravidla průměrné využití procesoru](../advisor/advisor-get-started.md#configure-the-average-cpu-utilization-rule-for-the-low-usage-virtual-machine-recommendation) článku [pro doporučení pro virtuální počítače s nízkým využitím](../advisor/advisor-get-started.md#configure-the-average-cpu-utilization-rule-for-the-low-usage-virtual-machine-recommendation).

I když některé scénáře, může způsobit nízké využití podle návrhu, můžete často ušetřit peníze změnou velikosti virtuálních počítačů na levnější velikosti. Vaše skutečné úspory se můžou lišit, pokud zvolíte akce změny velikosti. Projděme si příklad změny velikosti virtuálního počítače.

V seznamu doporučení, klikněte **nastavení správné velikosti nebo vypnutí nevyužitých virtuálních počítačů** doporučení. V seznamu kandidátů na virtuální počítač vyberte virtuální počítač, aby změna velikosti a potom klikněte na virtuální počítač. Takže můžete ověřit metriky využití jsou uvedeny podrobnosti virtuálního počítače. **Potenciální roční úspory** hodnota je, co můžete uložit, pokud vypnete nebo odeberte virtuální počítač. Změna velikosti virtuálního počítače se pravděpodobně můžete ušetřit, ale nebude ukládat v plné výši potenciální roční úspory.

![Příklad podrobnosti doporučení](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

V podrobnostech o virtuálním počítači zkontrolujte využití virtuálního počítače pro potvrzení, že se jedná o kandidát vhodný změny velikosti.

![Příkladu virtuálního počítače je podrobně popsán zobrazení historie využití](./media/tutorial-acm-opt-recommendations/vm-details.png)

Poznámka: velikost aktuálního virtuálního počítače. Po ověření, že by měl změnit velikost virtuálního počítače, zavřete podrobnosti o virtuálním počítači, kde můžete zobrazit seznam virtuálních počítačů.

V seznamu kandidátů vypnutí nebo změně velikosti, vyberte **velikosti virtuálního počítače**.
![Příklad doporučení s možností pro změnu velikosti virtuálního počítače](./media/tutorial-acm-opt-recommendations/resize-vm.png)

V dalším kroku se zobrazí se seznam možností změny velikosti k dispozici. Vyberte si ten, který vám poskytne nejlepší výkon a nákladovou efektivitu pro váš scénář. V následujícím příkladu zvolená možnost změní z **DS14\_V2** k **DS13\_V2**. Doporučení se uloží 551.30 $za měsíc nebo 6,615.60 $/ rok.

![Příklad seznam dostupných velikostí virtuálních počítačů, ve kterém můžete zvolit velikost](./media/tutorial-acm-opt-recommendations/choose-size.png)

Jakmile zvolíte vhodné velikosti, klikněte na tlačítko **vyberte** k zahájení změny velikosti.

Změna velikosti vyžaduje aktivně spuštěný virtuální počítač restartovat. Pokud virtuální počítač je v produkčním prostředí, doporučujeme spustit operace změny velikosti po pracovní době. Plánování restartování může zkrátit výpadky způsobené dočasné nedostupnosti.

## <a name="verify-the-action"></a>Ověřte, akce

Při změně velikosti virtuálního počítače úspěšně dokončí, se zobrazí oznámení o Azure.

![Oznámení úspěšná změněnou velikostí virtuálního počítače](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Zobrazení nákladů optimalizace doporučení zobrazíte potenciální nedostatečné efektivity využití
> * Reagovat na doporučení pro změnu velikosti virtuálního počítače do více cenově výhodnější variantou
> * Ověření akce a ujistěte se, že virtuální počítač byl úspěšně se změněnou velikostí

Pokud jste si ještě nepřečetli článek Cost Management o osvědčených postupech, poskytuje obecné pokyny a zásady, které je třeba zvážit při správě nákladů.

> [!div class="nextstepaction"]
> [Osvědčené postupy správy nákladů](cost-mgt-best-practices.md)
