---
title: Správa instancí výstrah v Azure Monitor
description: Správa instancí výstrah napříč Azure
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 82905cba5f391365ada13f4e5df5ad139f4c121e
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102866"
---
# <a name="manage-alert-instances-with-unified-alerts"></a>Správa instancí výstrah pomocí sjednocených výstrah

Díky [jednotnému prostředí upozornění](./alerts-overview.md) v Azure monitor uvidíte všechny vaše různé typy výstrah v rámci Azure. To zahrnuje více předplatných v jednom podokně. V tomto článku se dozvíte, jak můžete zobrazit instance výstrah a jak najít konkrétní instance výstrahy pro řešení potíží.

> [!NOTE]
> Můžete přistupovat jenom k výstrahám generovaným za posledních 30 dní.

## <a name="go-to-the-alerts-page"></a>Přejít na stránku s výstrahami

Na stránku výstrahy můžete přejít některým z následujících způsobů:

- V [Azure Portal](https://portal.azure.com/)vyberte **monitorovat**  >  **výstrahy**.  

     ![Snímek obrazovky s výstrahami monitorování](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
- Použijte kontext konkrétního prostředku. Otevřete prostředek, v části **monitorování** a vyberte **výstrahy**. Cílová stránka je předem filtrována pro výstrahy tohoto konkrétního prostředku.

     ![Snímek obrazovky s výstrahami monitorování prostředků](media/alerts-managing-alert-instances/alert-resource.JPG)

- Použijte kontext konkrétní skupiny prostředků. Otevřete skupinu prostředků, v části **monitorování** a vyberte **výstrahy**. Cílová stránka je předem filtrována pro výstrahy v příslušné skupině prostředků.    

     ![Snímek obrazovky s výstrahami monitorování skupiny prostředků](media/alerts-managing-alert-instances/alert-rg.JPG)

## <a name="find-alert-instances"></a>Najít instance výstrah

Stránka **Souhrn výstrah** poskytuje přehled všech instancí upozornění v rámci Azure. Souhrnné zobrazení můžete upravit tak, že vyberete **více předplatných** (maximálně 5), nebo filtrováním mezi **skupinami prostředků**, konkrétními **prostředky**nebo **časovými rozsahy**. Výběrem možnosti **Celkový počet výstrah**nebo kteréhokoli pásma závažnosti přejdete do zobrazení seznamu výstrah.     

![Snímek stránky se souhrnem výstrah](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
Na stránce **všechny výstrahy** jsou uvedeny všechny instance výstrah v rámci Azure. Pokud přejdete na portál z oznámení výstrahy, můžete použít filtry, které jsou k dispozici pro zúžení v konkrétní instanci výstrahy.

> [!NOTE]
> Pokud jste na stránku dostali výběr kteréhokoli pásma závažnosti, seznam se pro tuto závažnost předem vyfiltruje.

Kromě filtrů dostupných na předchozí stránce můžete také filtrovat na základě služby monitorování (například platformy pro metriky), podmínky monitorování (aktivováno nebo Vyřešeno), závažnosti, stavu výstrahy (nové/potvrzené/uzavřené) nebo ID inteligentní skupiny.

![Snímek stránky všech výstrah](media/alerts-managing-alert-instances/all-alerts.jpg)

> [!NOTE]
> Pokud jste na stránku dostali výběr kteréhokoli pásma závažnosti, seznam se pro tuto závažnost předem vyfiltruje.

Výběrem libovolné instance výstrahy otevřete stránku **Podrobnosti výstrahy** , která vám umožní zobrazit další podrobnosti o konkrétní instanci výstrahy.   

![Snímek stránky s podrobnostmi výstrahy](media/alerts-managing-alert-instances/alert-details.jpg)