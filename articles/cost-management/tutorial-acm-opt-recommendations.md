---
title: Kurz – snížit náklady na Azure s doporučeními optimalizace | Dokumentace Microsoftu
description: Tento kurz pomůže snížit náklady na Azure, pokud budete jednat doporučení pro optimalizaci.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: a9dbb121cab49024aaf0dc65bbac938764d9f8b2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229834"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Kurz: Optimalizace nákladů od doporučení.

Azure Cost Management pracuje s využitím Azure Advisoru poskytnout doporučení pro optimalizaci nákladů. Azure Advisor vám pomůže optimalizovat a zvýšení efektivity díky identifikaci nečinnosti a nedostatečně využité prostředky. Tento kurz vás provede příkladem, kdy identifikaci nevyužitých prostředků Azure a pak přijmete opatření ke snížení nákladů.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zobrazení nákladů optimalizace doporučení zobrazíte potenciální nedostatečné efektivity využití
> * Reagovat na doporučení pro změnu velikosti virtuálního počítače do více cenově výhodnější variantou
> * Ověření akce a ujistěte se, že virtuální počítač byl úspěšně se změněnou velikostí

## <a name="prerequisites"></a>Požadavky
Doporučení jsou dostupná pro nejrůznější obory a typy účtů Azure. Úplný seznam podporovaných typů účtů najdete v tématu [pochopení cost management dat](understand-cost-mgt-data.md). Abyste mohli zobrazit data nákladů, musíte mít alespoň přístup pro čtení k nejméně jednomu z následujících oborů. Další informace o oborech najdete v tématu [pochopení a práce s obory](understand-work-scopes.md).

- Předplatné
- Skupina prostředků

Musíte mít aktivní virtuální počítače s aspoň 14 dnů od aktivity.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com/).

## <a name="view-cost-optimization-recommendations"></a>Zobrazit doporučení pro optimalizaci nákladů

Pokud chcete zobrazit doporučení pro optimalizaci nákladů pro předplatné, otevřete požadovaný obor v Azure Portal a vyberte **doporučení poradce**.

Chcete-li zobrazit doporučení pro skupinu pro správu, otevřete požadovaný obor v Azure Portal a v nabídce vyberte položku **Analýza nákladů** . Pro přepnutí do jiného oboru, jako je například skupina pro správu, použijte modul **mikroskop** . V nabídce vyberte **doporučení poradce** . Další informace o oborech najdete v tématu [pochopení a práce s obory](understand-work-scopes.md).

![Doporučení k cenám Management Advisor webu Azure Portal](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

Seznam doporučení identifikuje nedostatečné efektivity využití, nebo obsahuje doporučení pro nákup, které vám pomůžou ušetřit Další. Celkové **možné roční úspory** ukazují celkovou částku, kterou můžete uložit, pokud vypnete nebo zrušíte přidělení všech virtuálních počítačů, které splňují pravidla pro doporučení. Pokud nechcete, aby je vypnout, měli byste zvážit, změna velikosti je levnější skladovou Položku virtuálního počítače.

Kategorie **dopad** , společně s **možnými ročními úsporami**, je navržena tak, aby lépe identifikovala doporučení, která mají potenciál v co největší míře ukládat.

Mezi doporučení s vysokým dopadem patří:
- [Nákup rezervovaných instancí virtuálních počítačů za účelem úspory peněz nad náklady na průběžné platby](../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [Optimalizace útraty virtuálních počítačů změnou velikosti nebo vypnutí nevyužitých instancí](../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [Použití úložiště standard pro ukládání snímků Managed Disks](../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

Doporučení pro středně velkou dopady zahrnují:
- [Odstranění neúspěšných kanálů Azure Data Factory](../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [Snížení nákladů odstraněním nezajištěných okruhů ExpressRoute](../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [Snížení nákladů odstraněním nebo změnou konfigurace nečinných bran virtuální sítě](../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>Reagovat na doporučení

Azure Advisor monitoruje využití virtuálních počítačů po dobu sedmi dnů a pak identifikuje nevyužité virtuální počítače. Virtuální počítače, jejichž využití procesoru je pět nebo míň procento a využívání sítě je 7 MB nebo méně čtyři nebo více dny jsou považovány za nízké využití virtuálních počítačů.

5 % nebo méně nastavení využití procesoru je výchozí nastavení, ale můžete upravit nastavení. Další informace o úpravě nastavení najdete v tématu [Konfigurace průměrného využití procesoru nebo doporučení pro virtuální počítače s nízkým využitím](../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation).

I když některé scénáře, může způsobit nízké využití podle návrhu, můžete často ušetřit peníze změnou velikosti virtuálních počítačů na levnější velikosti. Vaše skutečné úspory se můžou lišit, pokud zvolíte akce změny velikosti. Projděme si příklad změny velikosti virtuálního počítače.

V seznamu doporučení klikněte na doporučení **správné velikosti nebo vypnutí nevyužitých virtuálních počítačů** . V seznamu kandidátů na virtuální počítač vyberte virtuální počítač, aby změna velikosti a potom klikněte na virtuální počítač. Takže můžete ověřit metriky využití jsou uvedeny podrobnosti virtuálního počítače. **Potenciální roční hodnota úspory** je to, co můžete uložit při vypnutí nebo odebrání virtuálního počítače. Změna velikosti virtuálního počítače se pravděpodobně můžete ušetřit, ale nebude ukládat v plné výši potenciální roční úspory.

![Příklad podrobnosti doporučení](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

V podrobnostech o virtuálním počítači zkontrolujte využití virtuálního počítače pro potvrzení, že se jedná o kandidát vhodný změny velikosti.

![Příkladu virtuálního počítače je podrobně popsán zobrazení historie využití](./media/tutorial-acm-opt-recommendations/vm-details.png)

Poznámka: velikost aktuálního virtuálního počítače. Po ověření, že by měl změnit velikost virtuálního počítače, zavřete podrobnosti o virtuálním počítači, kde můžete zobrazit seznam virtuálních počítačů.

V seznamu kandidátů na vypnutí nebo změnu velikosti vyberte * * změnit velikost *&lt;FromVirtualMachineSKU&gt;* na *&lt;ToVirtualMachineSKU&gt;* * *.
![příklad doporučení s možností změny velikosti virtuálního počítače](./media/tutorial-acm-opt-recommendations/resize-vm.png)

V dalším kroku se zobrazí se seznam možností změny velikosti k dispozici. Vyberte si ten, který vám poskytne nejlepší výkon a nákladovou efektivitu pro váš scénář. V následujícím příkladu vybraná možnost mění velikost z **Standard_D8s_v3** na **Standard_D2s_v3**.

![Příklad seznam dostupných velikostí virtuálních počítačů, ve kterém můžete zvolit velikost](./media/tutorial-acm-opt-recommendations/choose-size.png)

Po výběru vhodné velikosti klikněte na tlačítko **změnit** velikost a spusťte akci změnit velikost.

Změna velikosti vyžaduje aktivně spuštěný virtuální počítač restartovat. Pokud virtuální počítač je v produkčním prostředí, doporučujeme spustit operace změny velikosti po pracovní době. Plánování restartování může zkrátit výpadky způsobené dočasné nedostupnosti.

## <a name="verify-the-action"></a>Ověřte, akce

Při změně velikosti virtuálního počítače úspěšně dokončí, se zobrazí oznámení o Azure.

![Oznámení úspěšná změněnou velikostí virtuálního počítače](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Zobrazení nákladů optimalizace doporučení zobrazíte potenciální nedostatečné efektivity využití
> * Reagovat na doporučení pro změnu velikosti virtuálního počítače do více cenově výhodnější variantou
> * Ověření akce a ujistěte se, že virtuální počítač byl úspěšně se změněnou velikostí

Pokud jste si ještě nepřečetli článek Cost Management o osvědčených postupech, poskytuje obecné pokyny a zásady, které je třeba zvážit při správě nákladů.

> [!div class="nextstepaction"]
> [Cost Management osvědčené postupy](cost-mgt-best-practices.md)
