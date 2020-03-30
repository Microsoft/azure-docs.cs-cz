---
title: Monitorování a správa relací Azure Bastion | Dokumenty společnosti Microsoft
description: V tomto článku se dozvíte, jak vybrat probíhající relaci a vynutit odpojení nebo odstranění.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: f56a724174b81966642b2870360014597436eade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76981085"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Monitorování a správa relací pro Azure Bastion

Jakmile se služba Bastion zřídí a nasadí ve vaší virtuální síti, můžete ji použít k bezproblémovému připojení k libovolnému virtuálnímu počítači v této virtuální síti. Když se uživatelé připojují k úlohám, Azure Bastion se dá použít ke sledování vzdálených relací a k rychlým akcím správy. Monitorování relací Azure Bastion umožňuje zobrazit, kteří uživatelé jsou připojeni ke kterým virtuálním počítačům. Zobrazuje IP adresu, ze které se uživatel připojil, jak dlouho byl připojen a kdy se připojil. Prostředí pro správu relací umožňuje vybrat probíhající relaci a vynutit odpojení nebo odstranění relace za účelem odpojení uživatele od probíhající relace.

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>Sledování vzdálených relací

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na prostředek Azure Bastion a na stránce Azure Bastion vyberte **Sessions.**

   ![Relace](./media/session-monitoring/sessions.png)
2. Na stránce **Relace** můžete zobrazit probíhající vzdálené relace na pravé straně.

   ![zobrazit relaci](./media/session-monitoring/view-session.png)
3. Výběrem **možnosti Aktualizovat** zobrazíte aktualizovaný seznam vzdálených relací. Když vyberete Aktualizovat, Azure Bastion načte nejnovější informace o monitorování a aktualizuje je na portálu.

   ![refresh](./media/session-monitoring/refresh.png)

>[!IMPORTANT]
> Povolte port 4443 pro příchozí provoz ze Správce brány pro monitorování relací pro práci.
>

## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>Odstranění nebo vynucení odpojení probíhající vzdálené relace

Můžete vybrat sadu relací a násilím je odpojit. Následující kroky ukazují, jak odstranit vzdálené relace:

1. Přejděte na prostředek Azure Bastion a na stránce Azure Bastion vyberte **relace.**

   ![navigate](./media/session-monitoring/navigate.png)
2. Po výběru možnosti Relace se zobrazí seznam vzdálených relací.

   ![relace seznamu](./media/session-monitoring/list.png)
3. Vyberte určitou vzdálenou relaci, potom vyberte tři elipsy na pravém konci řádku relace a pak vyberte **Odstranit**.

   ![delete](./media/session-monitoring/delete.png)
4. Vyberete-li možnost Odstranit, bude vzdálená relace odpojena a uživateli se ve vzdálené relaci zobrazí zpráva "Byli jste odpojeni".

   ![Odpojit](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si nejčastější dotazy k [baště](bastion-faq.md).