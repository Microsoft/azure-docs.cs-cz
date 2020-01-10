---
title: Více větví v toku mapování dat
description: Replikace datových proudů v mapování toku dat s více větvemi
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 71fb9f1ba9952be0e6b3910dd1079aa6d3c0482d
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834514"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Vytvoření nové větve v mapování toku dat

Přidejte novou větev a proveďte několik sad operací a transformací proti stejnému datovému proudu. Přidání nové větve je užitečné v případě, že chcete použít stejný zdroj pro pro více umyvadel nebo pro vzájemnou spojování dat.

Novou větev lze přidat ze seznamu transformací podobně jako jiné transformace. **Nová větev** bude k dispozici jako akce pouze v případě, že po transformaci, kterou zkoušíte vytvořit, existuje existující transformace.

![Přidávání nové větve](media/data-flow/new-branch2.png "Přidávání nové větve")

V následujícím příkladu tok dat čte data taxislužby Trip. Výstup agregovaný podle dne a dodavatele je povinný. Místo vytváření dvou samostatných toků dat, které se čtou ze stejného zdroje, je možné přidat novou větev. Tímto způsobem lze obě agregace spustit v rámci stejného toku dat. 

![Přidávání nové větve](media/data-flow/new-branch.png "Přidávání nové větve")
