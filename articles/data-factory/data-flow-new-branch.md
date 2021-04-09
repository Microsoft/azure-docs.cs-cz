---
title: Více větví v toku mapování dat
description: Replikace datových proudů v mapování toku dat s více větvemi
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: a11dbfbd6d6510b5c421e54cd2547c3aedb1bfb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100378192"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Vytvoření nové větve v mapování toku dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Přidejte novou větev a proveďte několik sad operací a transformací proti stejnému datovému proudu. Přidání nové větve je užitečné v případě, že chcete použít stejný zdroj pro pro více umyvadel nebo pro vzájemnou spojování dat.

Novou větev lze přidat ze seznamu transformací podobně jako jiné transformace. **Nová větev** bude k dispozici jako akce pouze v případě, že po transformaci, kterou zkoušíte vytvořit, existuje existující transformace.

![Snímek obrazovky ukazuje možnost Nová větev v nabídce Vícenásobné vstupy/výstupy.](media/data-flow/new-branch2.png "Přidávání nové větve")

V následujícím příkladu tok dat čte data taxislužby Trip. Výstup agregovaný podle dne a dodavatele je povinný. Místo vytváření dvou samostatných toků dat, které se čtou ze stejného zdroje, je možné přidat novou větev. Tímto způsobem lze obě agregace spustit v rámci stejného toku dat. 

![Snímek obrazovky znázorňující tok dat se dvěma větvemi ze zdroje.](media/data-flow/new-branch.png "Přidávání nové větve")
