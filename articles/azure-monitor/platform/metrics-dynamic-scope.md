---
title: Zobrazení více prostředků v Průzkumníkovi metrik Azure
description: Naučte se vizualizovat více prostředků pomocí Průzkumníka metrik Azure.
author: ritaroloff
services: azure-monitor
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: riroloff
ms.subservice: metrics
ms.openlocfilehash: 5f5f75e63a0b201144329a6e2b7c0d503de54692
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736469"
---
# <a name="view-multiple-resources-in-the-azure-metrics-explorer"></a>Zobrazení více prostředků v Průzkumníkovi metrik Azure

Výběr oboru prostředků umožňuje zobrazit metriky napříč několika prostředky, které jsou ve stejném předplatném a oblasti. V tomto článku se dozvíte, jak zobrazit víc prostředků pomocí funkce Azure Metrics Explorer Azure Monitor. 

## <a name="select-a-resource"></a>Výběr prostředku 

V nabídce **Azure monitor** vyberte **metriky** nebo v části **monitorování** v nabídce prostředku. Pak zvolte **Vybrat obor** a otevřete tak výběr oboru. 

Pomocí nástroje pro výběr oboru můžete vybrat prostředky, jejichž metriky chcete zobrazit. Pokud jste otevřeli Průzkumníka metrik z nabídky prostředku, měl by se tento obor naplnit. 

![Snímek obrazovky znázorňující otevření výběru oboru prostředků](./media/metrics-charts/019.png)

## <a name="select-multiple-resources"></a>Výběr více prostředků 

Některé typy prostředků se mohou dotazovat na metriky v několika prostředcích. Metrika musí být v rámci stejného předplatného a umístění. Tyto typy prostředků najdete v horní části nabídky **typy prostředků** .

![Snímek obrazovky zobrazující nabídku prostředků, které jsou kompatibilní s více prostředky.](./media/metrics-charts/020.png)

> [!WARNING] 
> Aby bylo možné vizualizovat metriky napříč několika prostředky, skupinami prostředků nebo předplatnými, musíte mít oprávnění čtenář monitoring na úrovni předplatného. Další informace najdete v tématu [Přidání nebo odebrání přiřazení rolí Azure pomocí Azure Portal](../../role-based-access-control/role-assignments-portal.md).

Pokud chcete vizualizovat metriky v několika prostředcích, Začněte výběrem několika prostředků v rámci výběru oboru prostředků. 

![Snímek obrazovky, který ukazuje, jak vybrat více prostředků.](./media/metrics-charts/021.png)

> [!NOTE]
> Prostředky, které vyberete, musí být v rámci stejného typu prostředku, umístění a předplatného. Prostředky, které se nevejdou do těchto kritérií, nejde vybrat. 

Po dokončení klikněte na tlačítko **použít** a uložte výběr. 

## <a name="select-a-resource-group-or-subscription"></a>Výběr skupiny prostředků nebo předplatného 

> [!WARNING]
> Aby bylo možné vizualizovat metriky napříč několika prostředky, skupinami prostředků nebo předplatnými, musíte mít oprávnění čtenář monitoring na úrovni předplatného. 

Pro typy, které jsou kompatibilní s více prostředky, můžete zadávat dotazy na metriky v rámci předplatného nebo několika skupin prostředků. Začněte tím, že vyberete předplatné nebo jednu nebo více skupin prostředků: 

![Snímek obrazovky, který ukazuje dotazování napříč více skupinami prostředků.](./media/metrics-charts/022.png)

Vyberte typ prostředku a umístění. 

![Snímek obrazovky, který zobrazuje vybrané skupiny prostředků.](./media/metrics-charts/023.png)

Vybrané obory můžete rozbalit a ověřit tak prostředky, na které se vaše výběry vztahují.

![Snímek obrazovky, který zobrazuje vybrané prostředky v rámci skupin.](./media/metrics-charts/024.png)

Po dokončení výběru oborů vyberte **použít**. 

## <a name="split-and-filter-by-resource-group-or-resources"></a>Rozdělení a filtrování podle skupin prostředků nebo prostředků

Po vykreslení prostředků můžete použít rozdělení a filtrování, abyste získali lepší přehled o vašich datech. 

Rozdělení vám umožní vizualizovat, jak různé segmenty metriky vzájemně porovnávají. Pokud například vykreslíte metriku pro více prostředků, můžete zvolit možnost **použít rozdělení** pro rozdělení podle ID prostředku nebo skupiny prostředků. Rozdělení umožňuje porovnat jednu metriku napříč několika prostředky nebo skupinami prostředků.  

Například následující graf ukazuje procentuální podíl procesoru napříč devět virtuálních počítačů. Po rozdělení podle ID prostředku se zobrazí informace o tom, jak se na virtuálním počítači liší procentuální hodnota procesoru. 

![Snímek obrazovky, který ukazuje, jak používat rozdělování k zobrazení procenta využití procesoru napříč virtuálními počítači.](./media/metrics-charts/026.png)

Spolu s rozdělením můžete použít filtrování k zobrazení pouze těch skupin prostředků, které chcete zobrazit.  Pokud například chcete zobrazit procentuální hodnotu procesoru pro virtuální počítače pro určitou skupinu prostředků, můžete vybrat **Přidat filtr** a filtrovat podle skupiny prostředků. 

V tomto příkladu filtrujeme podle TailspinToysDemo. V tomto případě filtr odebere metriky přidružené k prostředkům v TailspinToys. 

![Snímek obrazovky, který ukazuje, jak filtrovat podle skupiny prostředků.](./media/metrics-charts/027.png)

## <a name="pin-multiple-resource-charts"></a>Připnutí grafů s více prostředky 

Grafy s více prostředky, které vizualizují metriky napříč skupinami prostředků a předplatnými, vyžadují, aby měl uživatel oprávnění *Čtenář monitorování* na úrovni předplatného. Zajistěte, aby všichni uživatelé řídicích panelů, na které připnutí grafů s více prostředky mají dostatečná oprávnění. Další informace najdete v tématu [Přidání nebo odebrání přiřazení rolí Azure pomocí Azure Portal](../../role-based-access-control/role-assignments-portal.md).

Pokud chcete připnout graf s více prostředky na řídicí panel, přečtěte si téma [připnutí na řídicí panely](./metrics-charts.md#pinning-to-dashboards). 

## <a name="next-steps"></a>Další kroky

* [Řešení potíží s Průzkumníkem metrik](metrics-troubleshoot.md)
* [Seznam dostupných metrik pro služby Azure](metrics-supported.md)
* [Příklady nakonfigurovaných grafů](metric-chart-samples.md)