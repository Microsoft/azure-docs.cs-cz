---
title: Doporučení Azure Advisoru zobrazení, které vás zajímají
description: Zobrazit a filtrovat doporučení Azure Advisoru ke snížení šumu.
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 04/03/2019
ms.author: kasparks
ms.openlocfilehash: 9f599a63fd5f52420f1b79e769d4f7bca9683b32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467844"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>Doporučení Azure Advisoru zobrazení, které vás zajímají

Azure Advisor poskytuje doporučení pro optimalizaci nasazení Azure. V rámci služby Advisor máte přístup k několik funkcí, které vám umožní omezit svoje doporučení tak ty, které vás zajímají.

## <a name="configure-subscriptions-and-resource-groups"></a>Konfigurace skupiny prostředků a předplatná

Advisor dává možnost a vyberte předplatné a skupiny prostředků, které jsou pro vás a vaši organizaci. Pouze zobrazit doporučení pro předplatná a skupiny prostředků, které jste vybrali. Ve výchozím nastavení jsou vybrány všechny. Konfigurace nastavení platí pro předplatné nebo skupinu prostředků, takže stejné nastavení platí pro všechny uživatele, který má přístup k této předplatné nebo skupinu prostředků. Konfigurace nastavení lze změnit na webu Azure Portal nebo prostřednictvím kódu programu.

Chcete-li provést změny na webu Azure Portal:

1. Otevřít [Azure Advisoru](https://aka.ms/azureadvisordashboard) na webu Azure Portal.

1. Vyberte **konfigurace** z nabídky.

   ![Nabídka konfigurace advisoru](./media/view-recommendations/configuration.png)

1. Zaškrtněte políčko **zahrnout** sloupec pro předplatná nebo skupiny prostředků pro příjem doporučení Advisoru. Pokud do pole je zakázaná, pravděpodobně nemáte oprávnění k provedení změn na této předplatné nebo skupinu prostředků v konfiguraci. Další informace o [oprávnění ve službě Azure Advisor](permissions.md).

1. Klikněte na tlačítko **použít** v dolní části po provedení změny.

## <a name="filtering-your-view-in-the-azure-portal"></a>Filtrování zobrazení na webu Azure Portal

Konfigurace nastavení zůstávají aktivní, dokud se nezmění. Pokud chcete omezit zobrazení doporučení pro jednotné zobrazení, můžete použít rozevírací nabídky uvedených v horní části panelu služby Advisor. Z panelů přehled, vysokou dostupnost, zabezpečení, výkon, náklady a všechna doporučení můžete vybrat předplatná, typy prostředků a stav doporučení, který chcete zobrazit.

   ![Nabídka filtrování Advisor](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>Zavření a odložení doporučení

Azure Advisor můžete zrušit nebo odložit doporučení na jediném prostředku. Pokud je chcete zavřít doporučení, se nezobrazí ho znovu není-li ruční aktivaci. Ale odložení doporučení můžete zadat dobu trvání, po jejímž uplynutí doporučení je automaticky znovu aktivovat. Odkládá se to provést na webu Azure Portal nebo prostřednictvím kódu programu.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Odložit jeden doporučení na webu Azure Portal 

1. Otevřít [Azure Advisoru](https://aka.ms/azureadvisordashboard) na webu Azure Portal.
1. Vyberte kategorii doporučení zobrazíte doporučení
1. Vyberte ze seznamu doporučení doporučení
1. Vyberte odložit nebo zavřít pro doporučení, které chcete odložit nebo zavřít

     ![Nabídka filtrování Advisor](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Posunout nebo zavřít několika doporučení na webu Azure Portal

1. Otevřít [Azure Advisoru](https://aka.ms/azureadvisordashboard) na webu Azure Portal.
1. Vyberte kategorii doporučení zobrazíte doporučení.
1. Vyberte ze seznamu doporučení doporučení.
1. Zaškrtněte políčko vlevo od řádku pro všechny prostředky, které chcete odložit nebo zavřít doporučení.
1. Vyberte **odložit** nebo **Zavřít** v levém horním rohu v tabulce.

     ![Nabídka filtrování Advisor](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> Potřebujete oprávnění přispěvatele nebo vlastníka zavřít nebo odložit doporučení. Další informace o oprávnění ve službě Azure Advisor.

> [!NOTE]
> Pokud zaškrtávací políčka jsou zakázaná, může stále načítá doporučení. Počkejte prosím všechna doporučení se načíst před pokusem o posunout nebo zavřít.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>Znovu aktivovat dismissed nebo odložená doporučení

Můžete aktivovat doporučení, která byla odložena nebo zamítnuto. Tuto akci můžete udělat na webu Azure Portal nebo prostřednictvím kódu programu. Na webu Azure Portal:

1. Otevřít [Azure Advisoru](https://aka.ms/azureadvisordashboard) na webu Azure Portal.

1. Změňte filtr na panelu přehled **odloženo**. Advisor zobrazí dismissed nebo odložená doporučení.

    ![Nabídka filtrování Advisor](./media/view-recommendations/activate-postponed.png)

1. Vyberte kategorii zobrazíte **odloženo** a **zamítnuto** doporučení.

1. Vyberte ze seznamu doporučení doporučení. Doporučení se otevře **odložené & zavře** již vybrána k zobrazení prostředků, pro které byla odložena toto doporučení nebo zavřeli kartu.

1. Klikněte na **aktivovat** na konci řádku. Po kliknutí na doporučení je aktivní pro daný prostředek a proto odebral z této tabulky. Doporučení se teď zobrazují v **aktivní** kartu.
 
     ![Nabídka filtrování Advisor](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>Další postup

Tento článek vysvětluje, jak můžete zobrazit doporučení, která vás zajímají v Azure Advisoru. Další informace o službě Advisor najdete v tématu: 

- [Co je Azure Advisor?](advisor-overview.md)
- [Začínáme se službou Advisor](advisor-get-started.md)
- [Oprávnění ve službě Azure Advisor](permissions.md)



