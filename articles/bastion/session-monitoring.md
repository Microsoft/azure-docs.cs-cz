---
title: Monitorování a Správa relací Azure bastionu | Microsoft Docs
description: V tomto článku se dozvíte, jak vybrat průběžnou relaci a vynutit – odpojit nebo odstranit.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: a4a97ebd0e44bfd3b0ee167a2f3a7da435ac5087
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513000"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Monitorování a Správa relací pro Azure bastionu

Jakmile se služba bastionu zřídí a nasadí ve vaší virtuální síti, můžete ji použít k bezproblémovému připojení k libovolnému virtuálnímu počítači v této virtuální síti. Uživatelům, kteří se připojují k úlohám, se dá Azure bastionu použít k monitorování vzdálených relací a k akcím v rychlé správě. Monitorování relací Azure bastionu umožňuje zobrazit, kteří uživatelé jsou připojení k virtuálním počítačům. Zobrazuje IP adresu, ze které se uživatel připojil, jak dlouho byla připojena a kdy se připojila. Prostředí pro správu relací umožňuje vybrat probíhající relaci a vynuceně odpojit nebo odstranit relaci, aby bylo možné odpojit uživatele od probíhající relace.

## <a name="monitor"></a>Monitorování vzdálených relací

1. V [Azure Portal](https://portal.azure.com)přejděte do svého prostředku Azure bastionu a vyberte **relace** na stránce Azure bastionu.

   ![rušování](./media/session-monitoring/sessions.png)
2. Na stránce **relace** uvidíte na pravé straně průběžné vzdálené relace.

   ![Zobrazit relaci](./media/session-monitoring/view-session.png)
3. Kliknutím na **aktualizovat** zobrazíte aktualizovaný seznam vzdálených relací. Když vyberete možnost aktualizovat, Azure bastionu načte nejnovější informace o monitorování a aktualizuje ji na portálu.

   ![Téhle](./media/session-monitoring/refresh.png)

>[!IMPORTANT]
> Povolte port 4443 pro příchozí provoz z Správce brány, aby monitorování relace fungovalo.
>

## <a name="view"></a>Odstranění nebo vynucení odpojení probíhající vzdálené relace

Můžete vybrat sadu relací a vynutit jejich odpojení. Následující kroky ukazují, jak odstranit vzdálené relace:

1. Přejděte do svého prostředku Azure bastionu a vyberte **relace** na stránce Azure bastionu.

   ![Otevře](./media/session-monitoring/navigate.png)
2. Po výběru relací se zobrazí seznam vzdálených relací.

   ![výpis relací](./media/session-monitoring/list.png)
3. Vyberte konkrétní vzdálenou relaci, vyberte tři tečky na konci řádku relace na pravé straně a pak vyberte **Odstranit**.

   ![delete](./media/session-monitoring/delete.png)
4. Když vyberete odstranit, Vzdálená relace se odpojí a uživateli se zobrazí zpráva, že jste byli odpojeni ve vzdálené relaci.

   ![Dobu](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si [Nejčastější dotazy k bastionu](bastion-faq.md).