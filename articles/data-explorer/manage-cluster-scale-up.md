---
title: Škálování clusteru Průzkumník dat Azure tak, aby vyhovovaly měnících se požadavků
description: Tento článek popisuje postup horizontálního navýšení nebo snížení cluster Průzkumník dat Azure na základě změny poptávky.
author: radennis
ms.author: radennis
ms.reviewer: v-orspod
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 9d265ec7f0ce2030874f38b99b07343f1d4a3f4d
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408642"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Správa clusteru vertikálně navýšit kapacitu tak, aby vyhovovaly měnících se požadavků

Nastavování velikosti clusteru správně je důležité pro výkon Průzkumník dat Azure. Ale nemůže být předpovězen nároky na cluster s 100 % přesností. Velikost statické clusteru může vést k snížení využití nebo nadměrné využívání, ani jedno z nich je ideální. Lepším řešením je *škálování* cluster, přidávání a odebírání s měnícími poptávky kapacitu a využití procesoru. Existují dva pracovní postupy pro škálování, škálování zatížení a škálování na víc systémů. Tento článek ukazuje, jak spravovat vertikálně navýšit kapacitu clusteru.

1. Přejděte ke svému clusteru a v části **nastavení** vyberte **vertikálně navýšit kapacitu**.

    Už daný seznam dostupných skladových položek. Například na následujícím obrázku je jediné dostupné SKU: D14_V2.

    ![Vertikální navýšení kapacity](media/manage-cluster-scale-up/scale-up.png)

    D13_V2 je zakázaná, protože je aktuální skladová položka clusteru. L8 a L16 jsou zakázané, protože jejich není k dispozici v oblasti, kde se nachází clusteru.

1. Chcete-li změnit skladovou jednotku, vyberte skladovou Položku a stiskněte klávesu **vyberte** tlačítko.

> [!NOTE]
> Vertikální navýšení proces může trvat několik minut. Během této doby se pozastaví vašeho clusteru.
>
> Škálování může poškodit svůj výkon clusteru.
>

Nyní jste provedli operaci vertikální navýšení nebo snížení pro váš cluster Průzkumník dat Azure. Můžete také provést [horizontální navýšení kapacity clusteru](manage-cluster-scale-out.md), označovaný také jako automatické škálování, škálování dynamicky na základě metrik, který zadáte.

Pokud potřebujete pomoc s problémy škálování clusteru, otevřete žádost o podporu v [webu Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
