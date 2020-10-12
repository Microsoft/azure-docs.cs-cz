---
title: Sešity Azure Monitor – přesunutí oblastí
description: Postup přesunutí sešitu do jiné oblasti
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: d3b8bfbef544e754f684421daa847f1724435d53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88875617"
---
# <a name="move-an-azure-workbook-to-another-region"></a>Přesunutí sešitu Azure do jiné oblasti

Tento článek popisuje, jak přesunout prostředky Azure Workbook do jiné oblasti Azure. Prostředky můžete přesunout do jiné oblasti z řady důvodů. Například pro využití nové oblasti Azure, k nasazení funkcí nebo služeb dostupných pouze v konkrétních oblastech, pro splnění požadavků na požadavky na interní zásady a zásady správného řízení nebo v reakci na požadavky na plánování kapacity.

## <a name="prerequisites"></a>Požadavky

* Ujistěte se, že jsou sešity podporovány v cílové oblasti.

* Tyto pokyny platí pro sdílené sešity ( `microsoft.insights/workbooks` ) i pro soukromé sešity ( `microsoft.insights/myworkbooks` ) uložené v Azure monitor a na většině typů prostředků.

  V sešitech konkrétně propojených s typem prostředku Application Insights však tyto sešity jsou uloženy v oblasti Azure, ve které je uložen prostředek Application Insights.

  Tyto sešity nelze jednotlivě přesunout do jiné oblasti.

## <a name="move"></a>Přesunout

Nejjednodušším způsobem, jak přesunout sešit Azure, je použít možnost Uložit jako v nástroji sešity v uživatelském rozhraní portálu:

1. V prohlížeči sešitu Otevřete cílový sešit.
2. Použijte tlačítko Upravit na panelu nástrojů k zadání režimu úprav.
3. Použijte tlačítko Uložit jako na panelu nástrojů.
4. Ve formuláři Uložit vyberte název a požadovanou oblast pro sešit. Ujistěte se, že ostatní pole pro předplatné, skupinu prostředků a sdílení jsou vhodná.

   > [!NOTE]
   > Možná budete muset pro sešit použít nový název, abyste se vyhnuli jakýmkoli duplicitním názvům.

5. Uložte. 

## <a name="verify"></a>Ověření

K vyhledání nového sešitu použijte uživatelské rozhraní pro prohlížení sešitů Azure. Zajistěte, aby umístění bylo cílovým umístěním.

## <a name="clean-up"></a>Vyčištění

Po vytvoření sešitu v nové oblasti odstraňte původní sešit v předchozí oblasti.
1. Otevřete původní sešit v prohlížeči sešitu.
2. Použijte tlačítko Upravit na panelu nástrojů k zadání režimu úprav.
3. V rozevíracím seznamu nástroje pro úpravy (ikona tužky) vyberte Odstranit sešit.

Pokud jste sešit přejmenovali, aby se importoval do nové oblasti, můžete ho přejmenovat na předchozí název poté, co byl původní sešit odstraněn pomocí rozevírací nabídky upravit nástroje v režimu úprav na panelu nástrojů pro úpravy.

## <a name="next-steps"></a>Další kroky

Potřebujete místo sešitu přesunout šablonu sešitu? Podívejte se, jak [přesunout šablonu sešitu Azure do jiné oblasti](./workbook-templates-move-region.md).

Podívejte se, jak [nasadit sešity a šablony sešitu](./workbooks-automate.md) prostřednictvím šablon ARM.
