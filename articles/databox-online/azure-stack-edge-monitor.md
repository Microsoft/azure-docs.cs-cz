---
title: Monitorujte zařízení Azure Stack Edge pro | Microsoft Docs
description: Popisuje způsob použití Azure Portal a místního webového uživatelského rozhraní k monitorování Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: 388be1b13f89a50bed003731c01c6ab6287faaf9
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491060"
---
# <a name="monitor-your-azure-stack-edge-pro"></a>Monitorování Azure Stack Edge pro

Tento článek popisuje, jak monitorovat Azure Stack Edge pro. Pokud chcete monitorovat své zařízení, můžete použít Azure Portal nebo místní webové uživatelské rozhraní. Pomocí Azure Portal můžete zobrazit události zařízení, konfigurovat a spravovat výstrahy a zobrazovat metriky. Pomocí místního webového uživatelského rozhraní na fyzickém zařízení můžete zobrazit stav hardwaru různých součástí zařízení.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Zobrazení událostí zařízení a odpovídajících výstrah
> * Zobrazit stav hardwaru pro součásti zařízení
> * Zobrazit kapacitu a metriky transakcí pro vaše zařízení

## <a name="view-device-events"></a>Zobrazit události zařízení

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Zobrazit stav hardwaru

Pokud chcete zobrazit stav hardwaru komponent zařízení, v místním webovém uživatelském rozhraní postupujte následovně.

1. Připojte se k místnímu webovému uživatelskému rozhraní vašeho zařízení.
2. **> stav hardwaru** přejít na údržba. Tady můžete zobrazit stav různých komponent zařízení.

    ![Zobrazit stav hardwaru](media/azure-stack-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Zobrazit metriky

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

### <a name="metrics-on-your-device"></a>Metriky na vašem zařízení

Tato část popisuje metriky monitorování na vašem zařízení. Metriky mohou být:

* Metriky kapacity. Metriky kapacity se týkají kapacity zařízení.

* Metriky transakcí. Metriky transakcí souvisejí s operacemi čtení a zápisu, které jsou Azure Storage.

* Hraniční výpočetní metriky Hraniční výpočetní metrika se vztahuje na využití hraničních výpočtů na vašem zařízení.

Úplný seznam metrik je uveden v následující tabulce:

|Kapacitní metriky                     |Description  |
|-------------------------------------|-------------|
|**Dostupná kapacita**               | Odkazuje na velikost dat, která se dají zapsat do zařízení. Jinými slovy je tato metrika kapacitou, která se dá na zařízení zpřístupnit. <br></br>Kapacitu zařízení můžete uvolnit tak, že odstraníte místní kopii souborů, která má kopii na zařízení i v cloudu.        |
|**Celková kapacita**                   | Odkazuje na celkové bajty na zařízení, na které se zapisují data, což se také označuje jako celková velikost místní mezipaměti. <br></br> Kapacitu stávajícího virtuálního zařízení teď můžete zvýšit přidáním datového disku. Přidejte datový disk prostřednictvím správy hypervisoru pro virtuální počítač a pak restartujte virtuální počítač. Místní fond úložiště zařízení brány se rozšíří tak, aby odpovídal nově přidanému datovému disku. <br></br>Další informace získáte, když přejdete na [Přidat pevný disk pro virtuální počítač Hyper-V](https://www.youtube.com/watch?v=EWdqUw9tTe4). |

|Transakční metriky              | Description         |
|-------------------------------------|---------|
|**Odeslané bajty v cloudu (zařízení)**    | Součet všech bajtů odeslaných napříč všemi sdílenými složkami ve vašem zařízení        |
|**Odeslané bajty v cloudu (sdílená složka)**     | Počet odeslaných bajtů na sdílenou složku. Tato metrika může být: <br></br> Střední, což je (součet všech odeslaných bajtů na sdílenou položku/počet sdílených složek),  <br></br>Max, což je maximální počet bajtů odeslaných ze sdílené složky <br></br>Min, což je minimální počet bajtů odeslaných ze sdílené složky      |
|**Propustnost stahování do cloudu (sdílení)**| Počet stažených bajtů na sdílenou složku. Tato metrika může být: <br></br> Střední, což je (součet všech přečtených nebo stažených bajtů do sdílené složky/počtu sdílených složek) <br></br> Max, což je maximální počet bajtů stažených ze sdílené složky<br></br> a min, což je minimální počet bajtů stažených ze sdílené složky  |
|**Propustnost čtení v cloudu**            | Součet všech přečtených bajtů z cloudu napříč všemi sdílenými složkami v zařízení     |
|**Propustnost nahrávání do cloudu**          | Součet všech bajtů zapsaných do cloudu napříč všemi sdílenými složkami v zařízení     |
|**Propustnost nahrávání do cloudu (sdílení)**  | Součet všech bajtů zapsaných do cloudu ze sdílené složky/počtu sdílených složek je průměrně, Max a min na sdílenou složku.      |
|**Propustnost čtení (síť)**           | Zahrnuje propustnost systémové sítě pro všechny přečtené bajty z cloudu. Toto zobrazení může obsahovat data, která nejsou omezená na sdílené složky. <br></br>Při rozdělování se zobrazí přenos přes všechny síťové adaptéry v zařízení, včetně adaptérů, které nejsou připojené nebo povolené.      |
|**Propustnost zápisu (síť)**       | Zahrnuje propustnost systémové sítě pro všechny bajty zapsané do cloudu. Toto zobrazení může obsahovat data, která nejsou omezená na sdílené složky. <br></br>Při rozdělování se zobrazí přenos přes všechny síťové adaptéry v zařízení, včetně adaptérů, které nejsou připojené nebo povolené.          |

| Hraniční výpočetní metriky              | Description         |
|-------------------------------------|---------|
|**Výpočet využití paměti na hraničních zařízeních**      |           |
|**Výpočetní prostředí Edge – procento využití procesoru**    |         |

## <a name="next-steps"></a>Další kroky

Další informace o [správě šířky pásma](azure-stack-edge-manage-bandwidth-schedules.md).
Naučte se [spravovat oznámení o výstrahách událostí zařízení](azure-stack-edge-gpu-manage-device-event-alert-notifications.md).
