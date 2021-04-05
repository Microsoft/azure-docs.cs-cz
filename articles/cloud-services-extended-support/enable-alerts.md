---
title: Povolit monitorování v Cloud Services (Rozšířená podpora) pomocí Azure Portal
description: Povolit monitorování instancí služby Cloud Services (Rozšířená podpora) pomocí Azure Portal
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 4591253e1a305632b7f41afe692f297d71366382
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98744370"
---
# <a name="enable-monitoring-for-cloud-services-extended-support-using-the-azure-portal"></a>Povolit monitorování pro Cloud Services (Rozšířená podpora) pomocí Azure Portal

Tento článek vysvětluje, jak povolit upozornění na existující nasazení cloudové služby (Rozšířená podpora). 

## <a name="add-monitoring-rules"></a>Přidat pravidla monitorování
1. Přihlaste se na [Azure Portal](https://portal.azure.com). 
2. Vyberte nasazení cloudové služby (rozšířené podpory), pro které chcete povolit výstrahy. 
3. Vyberte okno **výstrahy** . 

    :::image type="content" source="media/enable-alerts-1.png" alt-text="Obrázek ukazuje, že se v Azure Portal vybere karta výstrahy.":::

4. Vyberte ikonu **nové výstrahy** .
     :::image type="content" source="media/enable-alerts-2.png" alt-text="Obrázek znázorňuje výběr možnosti Přidat novou výstrahu.":::

5. Zadejte požadované podmínky a požadované akce na základě metriky, které vás zajímají. Můžete definovat pravidla založená na jednotlivých metrikách nebo protokolu aktivit. 

     :::image type="content" source="media/enable-alerts-3.png" alt-text="Obrázek ukazuje, kam přidat podmínky pro výstrahy.":::

     :::image type="content" source="media/enable-alerts-4.png" alt-text="Image zobrazuje konfiguraci logiky signálu.":::

     :::image type="content" source="media/enable-alerts-5.png" alt-text="Image zobrazuje konfiguraci logiky skupiny akcí.":::

6. Po dokončení nastavení výstrah uložte změny a na základě vámi nakonfigurovaných metrik začnete zobrazovat okno **výstrahy** v průběhu času.

## <a name="next-steps"></a>Další kroky 
- Přečtěte si [Nejčastější dotazy](faq.md) k Cloud Services (Rozšířená podpora).
- Nasaďte cloudovou službu (rozšířenou podporu) pomocí [Azure Portal](deploy-portal.md), [PowerShellu](deploy-powershell.md), [šablony](deploy-template.md) nebo sady [Visual Studio](deploy-visual-studio.md).