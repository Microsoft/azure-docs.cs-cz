---
title: Monitorování a Správa relací Azure bastionu | Microsoft Docs
description: V tomto článku se dozvíte, jak vybrat průběžnou relaci a vynutit – odpojit nebo odstranit.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: f56a724174b81966642b2870360014597436eade
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76981085"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Monitorování a Správa relací pro Azure bastionu

Jakmile se služba bastionu zřídí a nasadí ve vaší virtuální síti, můžete ji použít k bezproblémovému připojení k libovolnému virtuálnímu počítači v této virtuální síti. Uživatelům, kteří se připojují k úlohám, se dá Azure bastionu použít k monitorování vzdálených relací a k akcím v rychlé správě. Monitorování relací Azure bastionu umožňuje zobrazit, kteří uživatelé jsou připojení k virtuálním počítačům. Zobrazuje IP adresu, ze které se uživatel připojil, jak dlouho byla připojena a kdy se připojila. Prostředí pro správu relací umožňuje vybrat probíhající relaci a vynuceně odpojit nebo odstranit relaci, aby bylo možné odpojit uživatele od probíhající relace.

## <a name="monitor"></a>Monitorování vzdálených relací

1. V [Azure Portal](https://portal.azure.com)přejděte do svého prostředku Azure bastionu a vyberte **relace** na stránce Azure bastionu.

   ![rušování](./media/session-monitoring/sessions.png)
2. Na stránce **relace** uvidíte na pravé straně průběžné vzdálené relace.

   ![Zobrazit relaci](./media/session-monitoring/view-session.png)
3. Kliknutím na **aktualizovat** zobrazíte aktualizovaný seznam vzdálených relací. Když vyberete možnost aktualizovat, Azure bastionu načte nejnovější informace o monitorování a aktualizuje ji na portálu.

   ![téhle](./media/session-monitoring/refresh.png)

>[!IMPORTANT]
> Povolte port 4443 pro příchozí provoz z Správce brány, aby monitorování relace fungovalo.
>

## <a name="view"></a>Odstranění nebo vynucení odpojení probíhající vzdálené relace

Můžete vybrat sadu relací a vynutit jejich odpojení. Následující kroky ukazují, jak odstranit vzdálené relace:

1. Přejděte do svého prostředku Azure bastionu a vyberte **relace** na stránce Azure bastionu.

   ![otevře](./media/session-monitoring/navigate.png)
2. Po výběru relací se zobrazí seznam vzdálených relací.

   ![výpis relací](./media/session-monitoring/list.png)
3. Vyberte konkrétní vzdálenou relaci, vyberte tři tečky na konci řádku relace na pravé straně a pak vyberte **Odstranit**.

   ![delete](./media/session-monitoring/delete.png)
4. Když vyberete odstranit, Vzdálená relace se odpojí a uživateli se zobrazí zpráva, že jste byli odpojeni ve vzdálené relaci.

   ![dobu](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si [Nejčastější dotazy k bastionu](bastion-faq.md).