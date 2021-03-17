---
title: Monitorování zařízení Azure Data Box Gateway | Microsoft Docs
description: Popisuje, jak pomocí Azure Portal a místního webového uživatelského rozhraní monitorovat Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: ed05f3d60f8ba4fbb06327136c7a117ae1d1d2db
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581692"
---
# <a name="monitor-your-azure-data-box-gateway"></a>Monitorování Azure Data Box Gateway

Tento článek popisuje, jak monitorovat Azure Data Box Gateway. Pokud chcete monitorovat své zařízení, můžete použít Azure Portal nebo místní webové uživatelské rozhraní. Pomocí Azure Portal můžete zobrazit události zařízení, konfigurovat a spravovat výstrahy a zobrazovat metriky.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Zobrazení událostí zařízení a odpovídajících výstrah
> * Zobrazit kapacitu a metriky transakcí pro vaše zařízení
> * Konfigurace a správa výstrah

## <a name="view-device-events"></a>Zobrazit události zařízení

[!INCLUDE [data-box-gateway-view-device-events](../../includes/data-box-gateway-view-device-events.md)]

## <a name="view-metrics"></a>Zobrazit metriky

[!INCLUDE [data-box-gateway-view-metrics](../../includes/data-box-gateway-view-metrics.md)]

### <a name="metrics-on-your-device"></a>Metriky na vašem zařízení

Tato část popisuje metriky monitorování na vašem zařízení. Metriky mohou být:

* Metriky kapacity. Metriky kapacity se týkají kapacity zařízení.

* Metriky transakcí. Metriky transakcí souvisejí s operacemi čtení a zápisu, které jsou Azure Storage.

Úplný seznam metrik je uveden v následující tabulce:

|Kapacitní metriky                     |Popis  |
|-------------------------------------|-------------|
|**Dostupná kapacita**               | Odkazuje na velikost dat, která se dají zapsat do zařízení. Jinými slovy, to je kapacita, kterou lze v zařízení zpřístupnit. <br></br>Kapacitu zařízení můžete uvolnit tak, že odstraníte místní kopii souborů, která má kopii na zařízení i v cloudu.        |
|**Celková kapacita**                   | Odkazuje na celkový počet bajtů na zařízení, do kterého se mají zapisovat data. To se také označuje jako celková velikost místní mezipaměti. <br></br> Kapacitu stávajícího virtuálního zařízení teď můžete zvýšit přidáním datového disku. Přidejte datový disk prostřednictvím správy hypervisoru pro virtuální počítač a pak restartujte virtuální počítač. Místní fond úložiště zařízení brány se rozšíří tak, aby odpovídal nově přidanému datovému disku. <br></br>Další informace získáte, když přejdete na [Přidat pevný disk pro virtuální počítač Hyper-V](https://www.youtube.com/watch?v=EWdqUw9tTe4). |

|Transakční metriky              | Popis         |
|-------------------------------------|---------|
|**Odeslané bajty v cloudu (zařízení)**    | Součet všech bajtů odeslaných napříč všemi sdílenými složkami ve vašem zařízení        |
|**Odeslané bajty v cloudu (sdílená složka)**     | Počet odeslaných bajtů na sdílenou složku. Může to být: <br></br> Střední, což je (součet všech odeslaných bajtů na sdílenou položku/počet sdílených složek),  <br></br>Max, což je maximální počet bajtů odeslaných ze sdílené složky <br></br>Min, což je minimální počet bajtů odeslaných ze sdílené složky      |
|**Propustnost stahování do cloudu (sdílení)**| Počet stažených bajtů na sdílenou složku. Může to být: <br></br> Střední, což je (součet všech přečtených nebo stažených bajtů do sdílené složky/počtu sdílených složek) <br></br> Max, což je maximální počet bajtů stažených ze sdílené složky<br></br> a min, což je minimální počet bajtů stažených ze sdílené složky  |
|**Propustnost čtení v cloudu**            | Součet všech přečtených bajtů z cloudu napříč všemi sdílenými složkami v zařízení     |
|**Propustnost nahrávání do cloudu**          | Součet všech bajtů zapsaných do cloudu napříč všemi sdílenými složkami v zařízení     |
|**Propustnost nahrávání do cloudu (sdílení)**  | Součet všech bajtů zapsaných do cloudu ze sdílené složky/počtu sdílených složek je průměrně, Max a min na sdílenou složku.      |
|**Propustnost čtení (síť)**           | Zahrnuje propustnost systémové sítě pro všechny přečtené bajty z cloudu. Toto zobrazení může obsahovat data, která nejsou omezená na sdílené složky. <br></br>Při rozdělování se zobrazí přenos všech síťových adaptérů na zařízení. To zahrnuje adaptéry, které nejsou připojené nebo nejsou povolené.      |
|**Propustnost zápisu (síť)**       | Zahrnuje propustnost systémové sítě pro všechny bajty zapsané do cloudu. Toto zobrazení může obsahovat data, která nejsou omezená na sdílené složky. <br></br>Při rozdělování se zobrazí přenos všech síťových adaptérů na zařízení. To zahrnuje adaptéry, které nejsou připojené nebo nejsou povolené.          |

## <a name="manage-alerts"></a>Správa výstrah

[!INCLUDE [data-box-gateway-manage-alerts](../../includes/data-box-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Další kroky

Další informace o [správě šířky pásma](data-box-gateway-manage-bandwidth-schedules.md).
