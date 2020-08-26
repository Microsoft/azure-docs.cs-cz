---
title: Kurz – Snížení nákladů na Azure pomocí doporučení
description: Tento kurz vám pomůže snížit náklady na Azure, pokud se budete řídit doporučeními ohledně optimalizace.
author: bandersmsft
ms.author: banders
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 28f9369d044cf9b2713cb2f2ad42f744e517918b
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88683279"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Kurz: Optimalizace nákladů na základě doporučení

Služba Azure Cost Management spolupracuje s Azure Advisorem a nabízí doporučení, která se týkají optimalizace nákladů. Azure Advisor provádí optimalizaci a zvyšuje efektivitu tím, že identifikuje nečinné a nevyužité prostředky. Tento kurz vás provede příkladem, ve kterém určíte nevyužité prostředky Azure a potom provedete akci ke snížení nákladů.

Podívejte se na video věnované [optimalizaci cloudových investic v Azure Cost Managementu](https://www.youtube.com/watch?v=cSNPoAb-TNc), kde se dozvíte víc o využití Advisoru k optimalizaci vašich nákladů. Další videa najdete v [kanálu služby Cost Management na YouTube](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/cSNPoAb-TNc]

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zobrazit doporučení k optimalizaci nákladů a podívat se na možné neefektivní využití prostředků.
> * Podle doporučení změnit velikost virtuálního počítače na nákladově výhodnější možnost.
> * Ověřit akci pro zajištění úspěšné změny velikosti virtuálního počítače.

## <a name="prerequisites"></a>Požadavky
Doporučení jsou dostupná pro nejrůznější obory a typy účtů Azure. Úplný seznam podporovaných typů účtů si můžete prohlédnout v článku [Vysvětlení dat služby Cost Management](understand-cost-mgt-data.md). Abyste mohli zobrazit data nákladů, musíte mít alespoň přístup pro čtení k nejméně jednomu z následujících oborů. Další informace o oborech najdete v tématu [Principy oborů a práce s nimi](understand-work-scopes.md).

- Předplatné
- Skupina prostředků

Pokud máte nové předplatné, nemůžete rovnou využívat funkce služby Cost Management. Může trvat až 48 hodin, než budete moct využívat všechny funkce služby Cost Management. Také musíte mít aktivní virtuální počítače, které jsou aktivní nejméně 14 dnů.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com/).

## <a name="view-cost-optimization-recommendations"></a>Zobrazení doporučení pro optimalizaci nákladů

Pokud se chcete podívat na doporučení pro optimalizaci nákladů u předplatného, otevřete požadovaný obor na webu Azure Portal a vyberte **Doporučení Advisoru**.

Pokud se chcete podívat na doporučení pro skupinu pro správu, otevřete požadovaný obor na webu Azure Portal a v nabídce vyberte **Analýza nákladů**. Pomocí možnosti **Obor** můžete přepnout na jiný obor, například na skupinu pro správu. V nabídce vyberte **Doporučení Advisoru**. Další informace o oborech najdete v tématu [Principy oborů a práce s nimi](understand-work-scopes.md).

![Doporučení Advisoru ke správě nákladů zobrazená na webu Azure Portal](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

Seznam doporučení určuje neefektivní využití nebo zobrazuje doporučení k nákupu, která vám mohou pomoci ušetřit další peníze. Celkové **možné úspory za rok** zobrazují celkovou částku, kterou můžete ušetřit, pokud vypnete nebo zrušíte přiřazení všech virtuálních počítačů splňujících pravidla doporučení. Pokud je nechcete vypínat, zvažte změnu jejich velikosti na méně nákladnou skladovou položku virtuálního počítače.

Kategorie **Dopad** a **Možné úspory za rok** jsou navrženy tak, aby vám pomohly určit doporučení, které mají potenciál nejvyšší úspory.

Mezi doporučení s vysokým dopadem patří:
- [Nákup rezervovaných instancí virtuálních počítačů a úspora nákladů oproti průběžným platbám](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [Optimalizace výdajů za virtuální počítače prostřednictvím změny velikosti nebo vypnutí nevyužitých instancí](../../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [Využití služby Standard Storage k ukládání snímků spravovaných disků](../../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

Mezi doporučení se středním dopadem patří:
- [Odstranění kanálů Azure Data Factory s chybami](../../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [Snížení nákladů eliminací nezajišťovaných okruhů ExpressRoute](../../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [Snížení nákladů odstraněním nebo překonfigurováním nečinných bran virtuální sítě](../../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>Využití doporučení

Po dobu sedmi dnů Azure Advisor monitoruje využití virtuálních počítačů a potom určí ty, které nejsou využité. Virtuální počítače s maximálně 5% využitím procesoru a s maximálně 7MB využitím sítě po dobu minimálně čtyř dnů se považují za virtuální počítače s nízkým využitím.

Nastavení hranice maximálně 5% využití CPU je výchozí, ale můžete si ho upravit. Další informace o úpravě nastavení najdete v tématu, které se věnuje [konfiguraci pravidla průměrného využití procesoru nebo doporučení k virtuálním počítačům s nízkým využitím](../../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation).

V některých scénářích se sice s nízkým využitím počítá, ale změnou velikosti virtuálních počítačů na méně nákladné velikosti můžete často ušetřit. Pokud zvolíte změnu velikosti, vaše skutečné úspory se mohou lišit. Projděme si příklad změny velikosti virtuálního počítače.

V seznamu doporučení klikněte na doporučení **Nastavení správné velikosti nebo vypnutí nevyužitých virtuálních počítačů**. V seznamu kandidátských virtuálních počítačů zvolte virtuální počítač, jehož velikost chcete změnit, a potom na něj klikněte. Zobrazí se podrobnosti virtuálního počítače, abyste mohli ověřit metriky využití. Hodnota **možných úspor za rok** je částka, kterou ušetříte, když vypnete nebo odeberete virtuální počítač. Změnou velikosti virtuálního počítače pravděpodobně ušetříte, ale nebude se jednat o celou částku možných úspor za rok.

![Příklad podrobností doporučení](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

V podrobnostech virtuálního počítače se podívejte na využití virtuálního počítače a ověřte, že se jedná o vhodného kandidáta na změnu velikosti.

![Příklad podrobností virtuálního počítače znázorňující historické využití](./media/tutorial-acm-opt-recommendations/vm-details.png)

Všimněte si aktuální velikosti virtuálního počítače. Po kontrole, že velikost virtuálního počítače lze změnit, zavřete podrobnosti virtuálního počítače, abyste viděli seznam virtuálních počítačů.

Pokud chcete změnit velikost nebo vypnout virtuální počítač, vyberte v seznamu kandidátů **Změnit velikost _&lt;FromVirtualMachineSKU&gt;_ na _&lt;ToVirtualMachineSKU&gt;_** .
![Příklad doporučení s možností změny velikosti virtuálního počítače](./media/tutorial-acm-opt-recommendations/resize-vm.png)

Dále se zobrazí seznam dostupných možností změny velikosti. Zvolte možnost, která pro váš scénář poskytuje nejvyšší výkon a která je nákladově nejefektivnější. V následujícím příkladu vybraná možnost změní velikost z **Standard_D8s_v3** na **Standard_D2s_v3**.

![Příklad seznamu dostupných velikostí virtuálních počítačů, ve kterém můžete zvolit velikost](./media/tutorial-acm-opt-recommendations/choose-size.png)

Po výběru vhodné velikosti klikněte na **Změnit velikost** a spusťte akci změny velikosti.

Změna velikosti vyžaduje restartování aktivně běžícího virtuálního počítače. Pokud je virtuální počítač v produkčním prostředí, doporučujeme spustit operaci změny velikosti až po pracovní době. Naplánováním restartu můžete snížit výpadky způsobené momentální nedostupností.

## <a name="verify-the-action"></a>Ověření akce

Po úspěšném dokončení změny velikosti virtuálního počítače se zobrazí oznámení Azure.

![Oznámení o úspěšné změně velikosti virtuálního počítače](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Zobrazit doporučení k optimalizaci nákladů a podívat se na možné neefektivní využití prostředků.
> * Podle doporučení změnit velikost virtuálního počítače na nákladově výhodnější možnost.
> * Ověřit akci pro zajištění úspěšné změny velikosti virtuálního počítače.

Pokud jste si ještě nepřečetli článek s osvědčenými postupy Cost Managementu, najdete v něm základní vodítko a zásady, které vám mohou pomoci se správou nákladů.

> [!div class="nextstepaction"]
> [Osvědčené postupy Cost Managementu](cost-mgt-best-practices.md)
