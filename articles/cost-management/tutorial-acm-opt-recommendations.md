---
title: Kurz – snížení nákladů na Azure s doporučeními pro optimalizaci | Microsoft Docs
description: Tento kurz vám pomůže snižovat náklady na Azure, když pracujete na doporučeních pro optimalizaci.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 603de4d9bed936ecb91f130b0e30f6d1383a9092
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935831"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Kurz: optimalizace nákladů z doporučení

Služba Azure Cost Management spolupracuje s Azure Advisorem a nabízí doporučení, která se týkají optimalizace nákladů. Azure Advisor provádí optimalizaci a zvyšuje efektivitu tím, že identifikuje nečinné a nevyužité prostředky. V tomto kurzu se seznámíte s příkladem, kde zjistíte nevyužité prostředky Azure a pak přijmete opatření, abyste snížili náklady.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zobrazit doporučení pro optimalizaci nákladů a zobrazit potenciální neefektivitu využití
> * Vyjednejte doporučení, jak změnit velikost virtuálního počítače na cenově výhodnější možnost.
> * Ověřte akci, aby se zajistilo, že se virtuálnímu počítači úspěšně změnila velikost.

## <a name="prerequisites"></a>Předpoklady
Doporučení jsou dostupná pro nejrůznější obory a typy účtů Azure. Úplný seznam podporovaných typů účtů najdete v tématu [pochopení cost management dat](understand-cost-mgt-data.md). Abyste mohli zobrazit data nákladů, musíte mít alespoň přístup pro čtení k nejméně jednomu z následujících oborů. Další informace o oborech najdete v tématu [pochopení a práce s obory](understand-work-scopes.md).

- Předplatné
- Skupina prostředků

Musíte mít aktivní virtuální počítače s alespoň 14 dny aktivity.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com/).

## <a name="view-cost-optimization-recommendations"></a>Zobrazit doporučení pro optimalizaci nákladů

Pokud chcete zobrazit doporučení pro optimalizaci nákladů pro předplatné, otevřete požadovaný obor v Azure Portal a vyberte **doporučení poradce**.

Chcete-li zobrazit doporučení pro skupinu pro správu, otevřete požadovaný obor v Azure Portal a v nabídce vyberte položku **Analýza nákladů** . Pro přepnutí do jiného oboru, jako je například skupina pro správu, použijte modul **mikroskop** . V nabídce vyberte **doporučení poradce** . Další informace o oborech najdete v tématu [pochopení a práce s obory](understand-work-scopes.md).

![Doporučení pro Cost Management Advisor zobrazená v Azure Portal](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

Seznam doporučení identifikuje neefektivní využití nebo zobrazuje doporučení pro nákup, která vám pomůžou ušetřit další peníze. Celkové **možné roční úspory** ukazují celkovou částku, kterou můžete uložit, pokud vypnete nebo zrušíte přidělení všech virtuálních počítačů, které splňují pravidla pro doporučení. Pokud je nechcete vypnout, měli byste zvážit jejich změnu velikosti na levnější SKU virtuálního počítače.

Kategorie **dopad** , společně s **možnými ročními úsporami**, je navržena tak, aby lépe identifikovala doporučení, která mají potenciál v co největší míře ukládat.

Mezi doporučení s vysokým dopadem patří:
- [Nákup rezervovaných instancí virtuálních počítačů za účelem úspory peněz nad náklady na průběžné platby](../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [Optimalizace útraty virtuálních počítačů změnou velikosti nebo vypnutí nevyužitých instancí](../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [Použití úložiště standard pro ukládání snímků Managed Disks](../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

Doporučení pro středně velkou dopady zahrnují:
- [Odstranění neúspěšných kanálů Azure Data Factory](../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [Snížení nákladů odstraněním nezajištěných okruhů ExpressRoute](../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [Snížení nákladů odstraněním nebo změnou konfigurace nečinných bran virtuální sítě](../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>Pracovat na doporučení

Azure Advisor monitoruje využití virtuálních počítačů po dobu sedmi dnů a pak identifikuje nevyužité virtuální počítače. Virtuální počítače s využitím procesoru jsou pět procent nebo méně a využití sítě je po dobu čtyř nebo víc dnů považováno za virtuální počítače s nízkým využitím.

Výchozí nastavení je 5% nebo méně využití procesoru, ale můžete nastavení upravit. Další informace o úpravě nastavení najdete v tématu [Konfigurace průměrného využití procesoru nebo doporučení pro virtuální počítače s nízkým využitím](../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation).

I když některé scénáře můžou mít za následek nízké využití podle návrhu, můžete často ušetřit peníze změnou velikosti virtuálních počítačů na levnější velikosti. Vaše skutečné úspory se mohou lišit, pokud zvolíte akci změny velikosti. Pojďme si projít příkladem změny velikosti virtuálního počítače.

V seznamu doporučení klikněte na doporučení **správné velikosti nebo vypnutí nevyužitých virtuálních počítačů** . V seznamu kandidátů na virtuální počítače vyberte virtuální počítač, který chcete změnit, a potom klikněte na virtuální počítač. Zobrazí se podrobnosti o virtuálním počítači, abyste mohli ověřit metriky využití. **Potenciální roční hodnota úspory** je to, co můžete uložit při vypnutí nebo odebrání virtuálního počítače. Změna velikosti virtuálního počítače vám pravděpodobně ušetří peníze, ale nebudete moci ukládat celé množství potenciálních ročních úspor.

![Příklad podrobností doporučení](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

V podrobnostech o virtuálním počítači Zkontrolujte využití virtuálního počítače a potvrďte, že se jedná o vhodný kandidát na změnu velikosti.

![Příklady podrobností o virtuálním počítači s historickým využitím](./media/tutorial-acm-opt-recommendations/vm-details.png)

Poznamenejte si velikost aktuálního virtuálního počítače. Až ověříte, že se má virtuální počítač změnit na velikost, zavřete Podrobnosti o virtuálním počítači, abyste viděli seznam virtuálních počítačů.

V seznamu kandidátů na vypnutí nebo změnu velikosti vyberte * * změnit velikost *&lt;FromVirtualMachineSKU&gt;* na *&lt;ToVirtualMachineSKU&gt;* * *.
![příklad doporučení s možností změny velikosti virtuálního počítače](./media/tutorial-acm-opt-recommendations/resize-vm.png)

V dalším kroku se zobrazí seznam dostupných možností změny velikosti. Vyberte si ten, který bude poskytovat nejlepší výkon a cenovou efektivitu pro váš scénář. V následujícím příkladu vybraná možnost mění velikost z **Standard_D8s_v3** na **Standard_D2s_v3**.

![Příklad seznamu dostupných velikostí virtuálních počítačů, kde můžete zvolit velikost](./media/tutorial-acm-opt-recommendations/choose-size.png)

Po výběru vhodné velikosti klikněte na tlačítko **změnit** velikost a spusťte akci změnit velikost.

Změna velikosti vyžaduje aktivně běžící virtuální počítač k restartování. Pokud se virtuální počítač nachází v produkčním prostředí, doporučujeme po pracovní době spustit operaci změny velikosti. Plánování restartování může snížit přerušení způsobené nedostupností.

## <a name="verify-the-action"></a>Ověřit akci

Po úspěšném dokončení změny velikosti virtuálního počítače se zobrazí oznámení Azure.

![Oznámení o změně velikosti virtuálního počítače se úspěšně změnilo.](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Zobrazit doporučení pro optimalizaci nákladů a zobrazit potenciální neefektivitu využití
> * Vyjednejte doporučení, jak změnit velikost virtuálního počítače na cenově výhodnější možnost.
> * Ověřte akci, aby se zajistilo, že se virtuálnímu počítači úspěšně změnila velikost.

Pokud jste si ještě neučinili článek o osvědčených postupech Cost Management, poskytne vám doprovodné materiály a principy, které vám pomůžou se správou nákladů.

> [!div class="nextstepaction"]
> [Cost Management osvědčené postupy](cost-mgt-best-practices.md)
