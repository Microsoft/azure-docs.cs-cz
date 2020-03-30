---
title: Zobrazení doporučení Azure Advisor, která jsou pro vás důležitá
description: Zobrazte a filtrujte doporučení Azure Advisorke snížení šumu.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 10d7b16864f8e449dc51e870c5ff9f20d8c0dc87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422377"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>Zobrazení doporučení Azure Advisor, která jsou pro vás důležitá

Azure Advisor poskytuje doporučení, která vám pomůžou optimalizovat nasazení Azure. V rámci poradce máte přístup k několika funkcím, které vám pomohou zúžit doporučení pouze na ty, které jsou pro vás důležité.

## <a name="configure-subscriptions-and-resource-groups"></a>Konfigurace předplatných a skupin prostředků

Poradce vám dává možnost vybrat předplatná a skupiny prostředků, které jsou důležité pro vás a vaši organizaci. Zobrazí se pouze doporučení pro předplatná a skupiny prostředků, které vyberete. Ve výchozím nastavení jsou vybrány všechny. Nastavení konfigurace platí pro předplatné nebo skupinu prostředků, takže stejné nastavení platí pro všechny uživatele, kteří mají přístup k tomuto předplatnému nebo skupině prostředků. Nastavení konfigurace lze změnit na portálu Azure nebo programově.

Provádění změn na webu Azure Portal:

1. Otevřete [Azure Advisor](https://aka.ms/azureadvisordashboard) na webu Azure Portal.

1. V nabídce vyberte **Konfigurace.**

   ![Nabídka konfigurace poradce](./media/view-recommendations/configuration.png)

1. Zaškrtněte políčko **zahrnout ve** sloupci Zahrnout pro všechna předplatná nebo skupiny prostředků, které chcete dostávat doporučení poradců. Pokud je pole zakázáno, pravděpodobně nemáte oprávnění k tomu, abyste u tohoto předplatného nebo skupiny prostředků změnili konfiguraci. Další informace o [oprávněních v Azure Advisoru](permissions.md).

1. Po **provázku** klikněte dole.

## <a name="filtering-your-view-in-the-azure-portal"></a>Filtrování zobrazení na webu Azure Portal

Nastavení konfigurace zůstávají aktivní, dokud se nezmění. Pokud chcete omezit zobrazení doporučení pro jedno zobrazení, můžete použít rozevírací přehledy uvedené v horní části panelu Poradce. Na panelech Přehled, Vysoká dostupnost, Zabezpečení, Výkon, Náklady a Všechna doporučení můžete vybrat předplatná, typy prostředků a stav doporučení, který chcete zobrazit.

   ![Nabídka filtrování poradců](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>Zamítnutí a odložení doporučení

Azure Advisor umožňuje zrušit nebo odložit doporučení na jeden prostředek. Pokud doporučení odmítnete, nezobrazí se znovu, dokud jej ručně neaktivujete. Odložení doporučení však umožňuje určit dobu trvání, po jejímž uplynutí je doporučení znovu automaticky aktivováno. Odložení lze provést na webu Azure Portal nebo programově.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Odložení jednoho doporučení na portálu Azure 

1. Otevřete [Azure Advisor](https://aka.ms/azureadvisordashboard) na webu Azure Portal.
1. Výběrem kategorie doporučení zobrazíte doporučení.
1. Výběr doporučení ze seznamu doporučení
1. Vyberte odložit nebo odmítnout pro doporučení, které chcete odložit nebo zavřít.

     ![Nabídka filtrování poradců](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Odložení nebo zamítnutí několika doporučení na webu Azure Portal

1. Otevřete [Azure Advisor](https://aka.ms/azureadvisordashboard) na webu Azure Portal.
1. Vyberte kategorii doporučení a zobrazte si doporučení.
1. Vyberte doporučení ze seznamu doporučení.
1. Zaškrtněte políčko v levé části řádku pro všechny prostředky, které chcete odložit nebo zrušit doporučení.
1. V levém horním rohu tabulky vyberte **Odložit** nebo **Zavřít.**

     ![Nabídka filtrování poradců](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> K odmítnutí nebo odložení doporučení potřebujete oprávnění přispěvatele nebo vlastníka. Další informace o oprávněních v Azure Advisoru.

> [!NOTE]
> Pokud jsou výběrová pole zakázána, je možné, že doporučení se stále načítají. Počkejte, než se všechna doporučení načtou, než se pokusíte odložit nebo zavřít.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>Opětovná aktivace odloženého nebo zamítnutého doporučení

Můžete aktivovat doporučení, které bylo odloženo nebo zamítnuto. Tuto akci lze provést na portálu Azure nebo programově. Na webu Azure Portal:

1. Otevřete [Azure Advisor](https://aka.ms/azureadvisordashboard) na webu Azure Portal.

1. Změňte filtr na panelu Přehled na **Odloženo**. Poradce pak zobrazí odložená nebo zamítnutá doporučení.

    ![Nabídka filtrování poradců](./media/view-recommendations/activate-postponed.png)

1. Vyberte kategorii, chcete-li zobrazit **Odložená** a **Zamítnutá** doporučení.

1. Vyberte doporučení ze seznamu doporučení. Tím se otevřou doporučení s již vybranou kartou **Odloženo &,** která zobrazuje zdroje, pro které bylo toto doporučení odloženo nebo odmítnuto.

1. Klikněte na **Aktivovat** na konci řádku. Po kliknutí je doporučení aktivní pro tento prostředek a tak odebráno z této tabulky. Doporučení je nyní viditelné na kartě **Aktivní.**
 
     ![Nabídka filtrování poradců](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>Další kroky

Tento článek vysvětluje, jak můžete zobrazit doporučení, která jsou pro vás důležitá v Azure Advisor. Další informace o poradci najdete v tématu: 

- [Co je Azure Advisor?](advisor-overview.md)
- [Začínáme s poradcem](advisor-get-started.md)
- [Oprávnění v Azure Advisoru](permissions.md)



