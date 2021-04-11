---
title: Integrace virtuální plochy Windows s Azure Advisor – Azure
description: Jak používat Azure Advisor s nasazením virtuálního počítače s Windows
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: b141d3829e9d98cb793d2038e93331804d2e6b8f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448283"
---
# <a name="use-azure-advisor-with-windows-virtual-desktop"></a>Použití Azure Advisor s virtuálním počítačem s Windows

Azure Advisor může uživatelům pomoci vyřešit běžné problémy sami, aniž by museli pořídit případy podpory. Doporučení omezují nutnost odesílat žádosti o podporu a ušetří vám čas a náklady.

V tomto článku se dozvíte, jak nastavit Azure Advisor v nasazení virtuálních klientských počítačů s Windows, aby mohli vaši uživatelé pomáhat.

## <a name="what-is-azure-advisor"></a>Co je Azure Advisor?

Azure Advisor analyzuje vaše konfigurace a telemetrii a nabízí přizpůsobená doporučení pro řešení běžných problémů. Pomocí těchto doporučení můžete optimalizovat prostředky Azure pro zajištění spolehlivosti, zabezpečení, provozní kvality, výkonu a nákladů. Další informace najdete na [webu Azure Advisor](https://azure.microsoft.com/services/advisor/).

## <a name="how-to-start-using-azure-advisor"></a>Jak začít používat Azure Advisor

Vše, co potřebujete, můžete začít je účet Azure na Azure Portal. Nejdřív otevřete Azure Portal na <https://portal.azure.com/#home> , pak v části **služby Azure** vyberte **Poradce** , jak je znázorněno na následujícím obrázku. Do panelu hledání v Azure Portal můžete zadat také text "Azure Advisor".

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky Azure Portal. Uživatel přesune ukazatel myši na odkaz Azure Advisor, což způsobí zobrazení rozevírací nabídky.](media/azure-advisor.png)

Po otevření Azure Advisor se zobrazí pět kategorií:

- Náklady
- Zabezpečení
- Spolehlivost
- Efektivita provozu
- Výkon

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky Azure Advisor zobrazující pět nabídek pro každou kategorii. Pět položek zobrazených ve vlastních polích je náklady, zabezpečení, spolehlivost, výkon provozu a výkon.](media/advisor-categories.png)

Když vyberete kategorii, přejdete na stránku její aktivní doporučení. Na této stránce můžete zobrazit, která doporučení Azure Advisor má za vás, jak je znázorněno na následujícím obrázku.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky se seznamem aktivních doporučení pro provozní výkon. V seznamu se zobrazí sedm doporučení s různou úrovní priority.](media/active-suggestions.png)

## <a name="additional-tips-for-azure-advisor"></a>Další tipy pro Azure Advisor

- Ujistěte se, že jste doporučení pravidelně kontrolovali aspoň jednou týdně. Azure Advisor aktualizuje svá aktivní doporučení několikrát denně. Vyhledávání nových doporučení může zabránit větším problémům tím, že vám pomůže s tím, že se budou pokládat a řešit menší.

- Vždy se pokuste vyřešit problémy s nejvyšší úrovní priority v Azure Advisor. Problémy s vysokou prioritou jsou označeny červeně. Vynechávání nevyřešených doporučení s vysokou prioritou může vést k potížím v řádku.

- Pokud se doporučení jeví jako méně důležité, můžete ho zavřít nebo odložit. Chcete-li zrušit nebo odložit doporučení, přejděte do sloupce **Akce** a změňte stav položky.

- Nenechávejte doporučení, dokud nebudete mít důvod na jejich zobrazení a nemusíte mít negativní dopad na vás ani na uživatele. Vždycky vyberte další **informace** a podívejte se, co je problém. Pokud problém vyřešíte podle pokynů v Azure Advisor, bude automaticky zmizí ze seznamu. Lepším řešením je řešit problémy, než je budete opakovaně odkládat.

- Pokaždé, když přijdete o problém s virtuálním počítačem s Windows, vždycky nejdřív ověřte Azure Advisor. Azure Advisor vám poskytne návod, jak tento problém vyřešit, nebo aspoň na prostředek, který vám může pomáhat.

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak vyřešit doporučení, přečtěte si téma [jak vyřešit Azure Advisor doporučení](azure-advisor-recommendations.md).
