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
ms.openlocfilehash: daebd6a4a00d82706ceda68f94736f21b8fe5a84
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "81606395"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Vytvoření nové větve v mapování toku dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Přidejte novou větev a proveďte několik sad operací a transformací proti stejnému datovému proudu. Přidání nové větve je užitečné v případě, že chcete použít stejný zdroj pro pro více umyvadel nebo pro vzájemnou spojování dat.

Novou větev lze přidat ze seznamu transformací podobně jako jiné transformace. **Nová větev** bude k dispozici jako akce pouze v případě, že po transformaci, kterou zkoušíte vytvořit, existuje existující transformace.

![Přidávání nové větve](media/data-flow/new-branch2.png "Přidávání nové větve")

V následujícím příkladu tok dat čte data taxislužby Trip. Výstup agregovaný podle dne a dodavatele je povinný. Místo vytváření dvou samostatných toků dat, které se čtou ze stejného zdroje, je možné přidat novou větev. Tímto způsobem lze obě agregace spustit v rámci stejného toku dat. 

![Přidávání nové větve](media/data-flow/new-branch.png "Přidávání nové větve")
