---
title: Více větví v toku mapování dat
description: Replikace datových proudů v mapování toku dat s více větvemi
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 04/16/2021
ms.openlocfilehash: f9f2bf2e2204e6b74bb8a31ac856dbe276a6e983
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588747"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Vytvoření nové větve v mapování toku dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Přidejte novou větev a proveďte několik sad operací a transformací proti stejnému datovému proudu. Přidání nové větve je užitečné v případě, že chcete použít stejný zdroj pro pro více umyvadel nebo pro vzájemnou spojování dat.

Novou větev lze přidat ze seznamu transformací podobně jako jiné transformace. **Nová větev** bude k dispozici jako akce pouze v případě, že po transformaci, kterou zkoušíte vytvořit, existuje existující transformace.

![Snímek obrazovky ukazuje možnost Nová větev v nabídce Vícenásobné vstupy/výstupy.](media/data-flow/new-branch2.png "Přidávání nové větve")

V následujícím příkladu tok dat čte data taxislužby Trip. Výstup agregovaný podle dne a dodavatele je povinný. Místo vytváření dvou samostatných toků dat, které se čtou ze stejného zdroje, je možné přidat novou větev. Tímto způsobem lze obě agregace spustit v rámci stejného toku dat. 

![Snímek obrazovky znázorňující tok dat se dvěma větvemi ze zdroje.](media/data-flow/new-branch.png "Přidávání nové větve")

> [!NOTE]
> Když po kliknutí na symbol plus (+) přidáte transformace do grafu, zobrazí se pouze možnost Nová větev, pokud jsou k dispozici následné transformační bloky. Je to proto, že nová větev vytvoří odkaz na existující datový proud a vyžaduje další nadřazené zpracování, aby fungovalo. Pokud nevidíte možnost Nová větev, nejprve přidejte odvozený sloupec nebo jinou transformaci a pak se vraťte k předchozímu bloku a jako možnost se zobrazí nová větev.

## <a name="next-steps"></a>Další kroky

Po rozvětvení můžete chtít použít [transformace toku dat](data-flow-transformation-overview.md) .
