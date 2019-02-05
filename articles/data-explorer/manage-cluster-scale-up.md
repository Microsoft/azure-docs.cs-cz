---
title: Škálování clusteru Průzkumník dat Azure tak, aby vyhovovaly měnících se požadavků
description: Tento článek popisuje kroky pro horizontální navýšení kapacity a škálování na méně instancí clusteru Průzkumník dat Azure na základě změny poptávky.
author: radennis
ms.author: radennis
ms.reviewer: v-orspod
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 213a49d87d5e9f801bb17604a322c231a4e3dee2
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735813"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Správa na úrovni clusterů až podle měnících se požadavků

Nastavování velikosti clusteru správně je důležité pro výkon Průzkumník dat Azure. Ale nemůže být předpovězen nároky na cluster s 100 % přesností. Velikost statické clusteru může vést k snížení využití nebo nadměrné využívání, ani jedno z nich je ideální. Lepším řešením je *škálování* cluster, přidávání a odebírání s měnícími poptávky kapacitu a využití procesoru. V tomto článku se dozvíte, jak spravovat škálování clusteru nahoru.

Přejděte ke svému clusteru a v části **nastavení** vyberte **vertikálně navýšit kapacitu**.

Můžete by pak získat seznam skladových položek, které jsou k dispozici. Můžete vybrat ze seznamu povolených karty. Například v následující obrázek je pouze jedna skladová položka, která můžete zvolit D14_vs.

![Vertikální navýšení kapacity](media/manage-cluster-scale-up/scale-up.png)

D13_v2 je zakázaná, protože toto je aktuální skladová položka clusteru. L8 a L16 jsou zakázané, protože jejich není k dispozici na na oblast, kde se cluster.

Stačí kliknout na skladovou Položku změnit skladovou Položku, byste chtěli použít a klikněte na tlačítko **vyberte** tlačítko.

[!NOTE] Vertikální navýšení proces může trvat několik minut a během této doby se vám váš cluster. Všimněte si, že škálování může poškodit svůj výkon clusteru.

Pokud potřebujete pomoc s problémy škálování clusteru, otevřete žádost o podporu v [webu Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).