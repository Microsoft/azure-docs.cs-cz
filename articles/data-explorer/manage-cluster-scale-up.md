---
title: Škálování clusteru Průzkumník dat Azure tak, aby vyhovovaly měnících se požadavků
description: Tento článek popisuje kroky pro vertikální navýšení kapacity a vertikální snížení kapacity clusteru služby Azure Průzkumník dat na základě změny poptávky.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 71f1d197b4a3fb437607f876ea524e582e62c0fd
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756332"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Správa clusteru vertikálně navýšit kapacitu tak, aby vyhovovaly měnících se požadavků

Nastavování velikosti clusteru správně je důležité pro výkon Průzkumník dat Azure. Ale nemůže být předpovězen nároky na cluster s absolutní přesnost. Velikost statické clusteru může vést k nízkého využití nebo overutilization, ani jedno z nich je ideální.

Lepším řešením je *škálování* cluster, přidávání a odebírání kapacitu a prostředky procesoru s měnícími poptávky. Existují dva pracovní postupy pro škálování: škálování zatížení a škálování na víc systémů. Tento článek ukazuje, jak spravovat vertikálně navýšit kapacitu clusteru.

1. Přejděte ke svému clusteru. V části **nastavení**vyberte **vertikálně navýšit kapacitu**.

    Se zobrazí seznam dostupných skladových položek. Například následující obrázek, pouze čtyři skladové položky jsou k dispozici.

    ![Vertikální navýšení kapacity](media/manage-cluster-scale-up/scale-up.png)

    Skladové položky jsou zakázané, protože aktuální SKU jsou nebo nejsou k dispozici v oblasti, kde se nachází clusteru.

1. Chcete-li změnit skladovou jednotku, vyberte SKU chcete a zvolte **vyberte** tlačítko.

> [!NOTE]
> Vertikálně navýšit kapacitu proces může trvat několik minut a během této doby se vám váš cluster. Všimněte si, že škálování může poškodit svůj výkon clusteru.

Nyní jste provedli operaci vertikální navýšení nebo snížení pro váš cluster Průzkumník dat Azure. Můžete také [spravovat horizontální navýšení kapacity clusteru](manage-cluster-scale-out.md) pro dynamicky horizontálně navyšovat kapacitu počtu instancí na základě metrik, který zadáte.

Pokud potřebujete pomoc s problémy škálování clusterů [žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) na webu Azure Portal.
