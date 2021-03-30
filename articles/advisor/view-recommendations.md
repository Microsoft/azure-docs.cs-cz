---
title: Podívejte se na Azure Advisor doporučení, která vás zajímají.
description: Zobrazení a filtrování Azure Advisor doporučení pro snížení šumu
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 43bdaba7d774bba8857a6eb3ef296d7ab8e1c264
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90986860"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>Podívejte se na Azure Advisor doporučení, která vás zajímají.

Azure Advisor poskytuje doporučení, která vám pomůžou optimalizovat nasazení Azure. V rámci služby Advisor máte přístup k několika funkcím, které vám pomůžou zúžit vaše doporučení jenom na ty, které vás zajímají.

## <a name="configure-subscriptions-and-resource-groups"></a>Konfigurace předplatných a skupin prostředků

Advisor nabízí možnost výběru předplatných a skupin prostředků, které jsou pro vás a vaši organizaci. Zobrazí se jenom doporučení pro předplatná a skupiny prostředků, které vyberete. Ve výchozím nastavení jsou vybrány všechny. Nastavení konfigurace se vztahuje na předplatné nebo skupinu prostředků, takže stejné nastavení platí pro všechny uživatele, kteří mají přístup k tomuto předplatnému nebo skupině prostředků. Konfigurační nastavení lze změnit v Azure Portal nebo programově.

Chcete-li provést změny v Azure Portal:

1. Otevřete [Azure Advisor](https://aka.ms/azureadvisordashboard) v Azure Portal.

1. V nabídce vyberte **Konfigurace** .

   ![Nabídka konfigurace Advisoru](./media/view-recommendations/configuration.png)

1. Zaškrtněte políčko ve sloupci **Zahrnout** pro všechna předplatná nebo skupiny prostředků pro příjem doporučení služby Advisor. Pokud je toto políčko zakázané, možná nemáte oprávnění k provedení změny konfigurace u daného předplatného nebo skupiny prostředků. Přečtěte si další informace o [oprávněních v Azure Advisor](permissions.md).

1. Po provedení změny klikněte na **použít** v dolní části.

## <a name="filtering-your-view-in-the-azure-portal"></a>Filtrování zobrazení v Azure Portal

Nastavení konfigurace zůstane aktivní, dokud se nezmění. Pokud chcete omezit zobrazení doporučení pro jedno zobrazení, můžete použít rozevírací seznam, který je k dispozici v horní části panelu poradce. Na panelu přehled, vysoká dostupnost, zabezpečení, výkon, náklady a všechny panely doporučení můžete vybrat odběry, typy prostředků a stav doporučení, které chcete zobrazit.

   :::image type="content" source="./media/view-recommendations/filtering.png" alt-text="Snímek obrazovky Azure Advisor znázorňující možnosti filtrování":::

## <a name="dismissing-and-postponing-recommendations"></a>Chybějící doporučení a odložení

Azure Advisor umožňuje zrušit nebo odložit doporučení na jednom prostředku. Pokud doporučení zrušíte, neuvidíte ho znovu, pokud ho ručně neaktivujete. Odložení doporučení vám ale umožní zadat dobu trvání, po které se doporučení automaticky aktivuje. Odložení lze provést v Azure Portal nebo programově.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Odložit jedno doporučení v Azure Portal 

1. Otevřete [Azure Advisor](https://aka.ms/azureadvisordashboard) v Azure Portal.
1. Pokud si chcete zobrazit doporučení, vyberte kategorii doporučení.
1. Vyberte doporučení ze seznamu doporučení.
1. Pro doporučení, které chcete odložit nebo zavřít, vyberte posunout nebo zavřít.

     :::image type="content" source="./media/view-recommendations/postpone-dismiss.png" alt-text="Snímek obrazovky okna použít Managed Disks, který zobrazuje akce vybrat sloupec a odložit a zavřít pro jedno doporučení.":::

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Odložit nebo zavřít více doporučení v Azure Portal

1. Otevřete [Azure Advisor](https://aka.ms/azureadvisordashboard) v Azure Portal.
1. Pokud si chcete zobrazit doporučení, vyberte kategorii doporučení.
1. V seznamu doporučení vyberte doporučení.
1. Zaškrtněte políčko vlevo od řádku pro všechny prostředky, které chcete odložit, nebo toto doporučení zavřete.
1. V levém horním rohu tabulky vyberte **Posunout** nebo **Zavřít** .

     :::image type="content" source="./media/view-recommendations/postpone-dismiss-multiple.png" alt-text="Snímek obrazovky okna použít Managed Disks znázorňující akce vybrat sloupec a odložit a zavřít v levém horním rohu zvýrazněné tabulky":::

> [!NOTE]
> K zavření nebo odložení doporučení potřebujete oprávnění Přispěvatel nebo Owner. Přečtěte si další informace o oprávněních v Azure Advisor.

> [!NOTE]
> Pokud jsou pole výběru zakázaná, může se i nadále načítat doporučení. Počkejte prosím, než se všechna doporučení načtou, než zkusíte operaci odložit nebo zavřít.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>Opětovná aktivace odložených nebo neúspěšných doporučení

Můžete aktivovat doporučení, které bylo odloženo nebo zavřeno. Tuto akci lze provést v Azure Portal nebo programově. Na webu Azure Portal:

1. Otevřete [Azure Advisor](https://aka.ms/azureadvisordashboard) v Azure Portal.

1. Změňte filtr na panelu přehled na **odloženo**. Poradce pak zobrazí odložená nebo odložených doporučení.

    :::image type="content" source="./media/view-recommendations/activate-postponed.png" alt-text="Snímek obrazovky okna Azure Advisor znázorňující vybrané odložení rozevírací nabídky":::

1. Vyberte kategorii pro zobrazení **odložených** a **chybějících** doporučení.

1. V seznamu doporučení vyberte doporučení. Tím se otevře doporučení s **odloženou &ou** , která je už vybraná, aby se zobrazily prostředky, pro které se toto doporučení odložilo nebo zrušilo.

1. Na konci řádku klikněte na **aktivovat** . Po kliknutí se doporučení pro daný prostředek aktivuje a pak se z této tabulky odebere. Doporučení se teď zobrazuje na **aktivní** kartě.
 
     :::image type="content" source="./media/view-recommendations/activate-postponed-2.png" alt-text="Snímek obrazovky okna Povolit obnovitelné odstranění zobrazující odloženou & kartu s zvýrazněnou akcí aktivovat":::

## <a name="next-steps"></a>Další kroky

V tomto článku se dozvíte, jak můžete zobrazit doporučení, která vás zajímají v Azure Advisor. Další informace o službě Advisor najdete v těchto tématech: 

- [Co je Azure Advisor?](advisor-overview.md)
- [Začínáme pomocí Poradce](advisor-get-started.md)
- [Oprávnění v Azure Advisor](permissions.md)



