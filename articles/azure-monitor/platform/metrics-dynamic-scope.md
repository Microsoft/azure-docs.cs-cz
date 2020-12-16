---
title: Zobrazení více prostředků v Průzkumník metrik
description: Naučte se vizualizovat více prostředků v Azure Monitor Průzkumník metrik
author: ritaroloff
services: azure-monitor
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: riroloff
ms.subservice: metrics
ms.openlocfilehash: 724809dbce3ca1b5a36f4da0ba5c03d0f78897f5
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577644"
---
# <a name="viewing-multiple-resources-in-metrics-explorer"></a>Zobrazení více prostředků v Průzkumník metrik

Výběr oboru prostředků umožňuje zobrazit metriky napříč několika prostředky, které jsou ve stejném předplatném a oblasti. Níže najdete pokyny k zobrazení více prostředků v Azure Monitor Průzkumník metrik. 

## <a name="selecting-a-resource"></a>Výběr prostředku 

V nabídce **Azure monitor** vyberte **metriky** nebo v části **monitorování** v nabídce prostředku. Kliknutím na tlačítko Vybrat obor otevřete nástroj pro výběr oboru prostředků, který vám umožní vybrat prostředky, pro které chcete zobrazit metriky. Tato hodnota by již měla být naplněna, pokud jste spustili průzkumníka metrik z nabídky prostředku. 

![Snímek obrazovky pro výběr oboru prostředků zvýrazněný červeně](./media/metrics-charts/019.png)

## <a name="selecting-multiple-resources"></a>Výběr několika prostředků 

Některé typy prostředků mají povolenou možnost dotazování na metriky přes více prostředků, pokud jsou ve stejném předplatném a umístění. Tyto typy prostředků se dají najít v horní části rozevíracího seznamu "typy prostředků". 

![Snímek obrazovky zobrazující rozevírací seznam prostředků, které jsou kompatibilní s více prostředky ](./media/metrics-charts/020.png)

> [!WARNING] 
> Aby bylo možné vizualizovat metriky napříč několika prostředky, skupinami prostředků nebo předplatnými, musíte mít oprávnění čtenář monitoring na úrovni předplatného. Pokud to chcete provést, postupujte podle pokynů v [tomto dokumentu](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

Aby bylo možné vizualizovat metriky v několika prostředcích, Začněte výběrem několika prostředků v rámci výběru oboru prostředků. 

![Snímek obrazovky, který ukazuje, jak vybrat více prostředků](./media/metrics-charts/021.png)

> [!NOTE]
> Máte možnost vybrat jenom několik prostředků v rámci stejného typu prostředku, umístění a předplatného. Prostředky mimo tato kritéria nebudou možné vybírat. 

Po dokončení výběru klikněte na tlačítko použít a uložte výběr. 

## <a name="selecting-a-resource-group-or-subscription"></a>Výběr skupiny prostředků nebo předplatného 

> [!WARNING]
> Aby bylo možné vizualizovat metriky napříč několika prostředky, skupinami prostředků nebo předplatnými, musíte mít oprávnění čtenář monitoring na úrovni předplatného. 

Pro typy kompatibilní s více prostředky můžete také zadat dotaz na metriky v rámci předplatného nebo několika skupin prostředků. Začněte tím, že vyberete předplatné nebo jednu nebo více skupin prostředků: 

![Snímek obrazovky, který ukazuje dotazování napříč více skupinami prostředků ](./media/metrics-charts/022.png)

Pak budete muset vybrat typ prostředku a umístění, aby bylo možné pokračovat v použití nového oboru. 

![Snímek obrazovky zobrazující vybrané skupiny prostředků ](./media/metrics-charts/023.png)

Můžete také rozšířit vybrané obory, abyste ověřili, na kterých zdrojích se bude tato možnost vztahovat.

![Snímek obrazovky zobrazující vybrané prostředky v rámci skupin ](./media/metrics-charts/024.png)

Po dokončení výběru oborů klikněte na použít, aby se vaše výběry uložily. 

## <a name="splitting-and-filtering-by-resource-group-or-resources"></a>Rozdělení a filtrování podle skupiny prostředků nebo prostředků

Po vykreslení prostředků můžete pomocí nástroje pro dělení a filtrování získat lepší přehled o datech. 

Rozdělení vám umožní vizualizovat, jak různé segmenty metriky vzájemně porovnávají. Když například vykreslujete metriku pro více prostředků, můžete k rozdělení podle ID prostředku nebo skupiny prostředků použít nástroj rozdělení na více instancí. To vám umožní snadno porovnat jednu metriku napříč několika prostředky nebo skupinami prostředků.  

Níže je například graf procentuální podílu CPU v rámci 9VMs. Rozdělením podle ID prostředku můžete snadno zjistit, jak se procentuální podíl procesoru na virtuálním počítači liší. 

![Snímek obrazovky, který ukazuje, jak můžete použít rozdělování k zobrazení procenta využití procesoru na virtuální počítač](./media/metrics-charts/026.png)

Kromě rozdělení můžete pomocí funkce filtrování zobrazit jenom skupiny prostředků, které chcete zobrazit.  Pokud například chcete zobrazit procentuální hodnotu procesoru pro virtuální počítače pro určitou skupinu prostředků, můžete použít nástroj Přidat filtr pro filtrování podle skupiny prostředků. V tomto příkladu filtrujeme podle TailspinToys, čímž se odstraní metriky přidružené k prostředkům v TailspinToysDemo. 

![Snímek obrazovky, který ukazuje, jak můžete filtrovat podle skupiny prostředků](./media/metrics-charts/027.png)

## <a name="pinning-your-multi-resource-charts"></a>Připnutí grafů s více prostředky 

> [!WARNING] 
> Aby bylo možné vizualizovat metriky napříč několika prostředky, skupinami prostředků nebo předplatnými, musíte mít oprávnění čtenář monitoring na úrovni předplatného. Pokud to chcete provést, postupujte podle pokynů v [tomto dokumentu](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal). 

Pokud chcete svůj graf více zdrojů připnout, postupujte prosím podle [pokynů.](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts#create-alert-rules) 

## <a name="next-steps"></a>Další kroky

* [Řešení potíží s Průzkumníkem metrik](metrics-troubleshoot.md)
* [Seznam dostupných metrik pro služby Azure](metrics-supported.md)
* [Příklady nakonfigurovaných grafů](metric-chart-samples.md)

