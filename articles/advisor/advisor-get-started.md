---
title: Začínáme se službou Azure Advisor
description: Začínáme s Azure Advisorem.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: 8c2699030b1a6d428ddc2a4db40a66003824cf10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259627"
---
# <a name="get-started-with-azure-advisor"></a>Začínáme se službou Azure Advisor

Zjistěte, jak získat přístup k Advisorovi prostřednictvím portálu Azure, získat doporučení a implementovat doporučení.

> [!NOTE]
> Azure Advisor se automaticky spustí na pozadí a vyhledá nově vytvořené prostředky. Poskytnutí doporučení k těmto zdrojům může trvat až 24 hodin.

## <a name="get-recommendations"></a>Získat doporučení

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. V levém podokně klepněte na tlačítko **Poradce**.  Pokud poradce v levém podokně nevidíte, klepněte na tlačítko **Všechny služby**.  V podokně nabídky služby klepněte v části **Sledování a správa**na tlačítko **Poradce**. Zobrazí se řídicí panel Poradce.

   ![Přístup k Azure Advisoru pomocí portálu Azure](./media/advisor-get-started/advisor-portal-menu.png) 

1. Na řídicím panelu služby Advisor se zobrazí souhrn doporučení pro všechna vybraná předplatná.  Předplatná, pro která chcete zobrazit doporučení, můžete zvolit pomocí rozevíracího seznamu filtru předplatných.

1. Chcete-li získat doporučení pro určitou kategorii, klepněte na jednu z karet: **Vysoká dostupnost**, **Zabezpečení**, **Výkon**nebo **Náklady**. 

   ![Řídicí panel Azure Advisor](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-recommendation-details-and-implement-a-solution"></a>Získejte podrobnosti doporučení a implementujte řešení

V programu Advisor můžete vybrat doporučení, kde můžete zobrazit další podrobnosti – například akce doporučení a ovlivněné zdroje – a implementovat řešení doporučení.  

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete [poradce](https://aka.ms/azureadvisordashboard).

1. Vyberte kategorii doporučení, chcete-li zobrazit seznam doporučení v rámci této kategorie, nebo vyberte kartu **Vše,** chcete-li zobrazit všechna doporučení.

1. Klikněte na doporučení, které chcete podrobně zkontrolovat.

1. Zkontrolujte informace o doporučení a prostředky, které doporučení se vztahuje na.

1. Chcete-li doporučení implementovat, klepněte na **doporučenou akci.**

## <a name="filter-recommendations"></a>Doporučení filtru

Můžete filtrovat doporučení k přechodu k tomu, co je pro vás nejdůležitější.  Můžete filtrovat podle předplatného, typu prostředku nebo stavu doporučení.  

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete [poradce](https://aka.ms/azureadvisordashboard).

1. Pomocí rozevíracích seznamů na řídicím panelu Poradce můžete filtrovat podle předplatného, typu prostředku nebo stavu doporučení.

    ![Kritéria filtru vyhledávání poradce](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-recommendations"></a>Odložení nebo zamítnutí doporučení

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete [poradce](https://aka.ms/azureadvisordashboard).

1. Přejděte na doporučení, které chcete odložit nebo zavřít.

1. Klikněte na doporučení.

1. Klepněte na tlačítko **Odložit**. 

1. Zadejte časové období odložení nebo vyberte **možnost Nikdy** nezavřete doporučení.

## <a name="exclude-subscriptions-or-resource-groups"></a>Vyloučit předplatná nebo skupiny prostředků

Můžete mít skupiny prostředků nebo odběry, pro které nechcete přijímat doporučení poradce – například "test" prostředky.  Poradce můžete nakonfigurovat tak, aby generoval pouze doporučení pro konkrétní předplatná a skupiny prostředků.

> [!NOTE]
> Chcete-li zahrnout nebo vyloučit předplatné nebo skupinu prostředků z poradce, musíte být vlastníkem předplatného.  Pokud nemáte požadovaná oprávnění pro odběr nebo skupinu prostředků, možnost zahrnout nebo vyloučit je zakázána v uživatelském rozhraní.

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete [poradce](https://aka.ms/azureadvisordashboard).

1. Na panelu akcí klikněte na **Konfigurovat.**

1. Odškrtejte všechna předplatná nebo skupiny prostředků, pro které nechcete dostávat doporučení poradce.

    ![Příklad konfigurace prostředků poradce](./media/advisor-get-started/advisor-configure-resources.png)

1. Klikněte na tlačítko **Použít.**

## <a name="configure-low-usage-vm-recommendation"></a>Konfigurace doporučení virtuálního počítače s nízkým využitím

Tento postup konfiguruje pravidlo využití průměrného procesoru pro doporučení virtuálního počítače s nízkým využitím.

Advisor monitoruje využití virtuálního počítače po dobu 7 dnů a pak identifikuje virtuální počítače s nízkým využitím. Virtuální počítače jsou považovány za nízké využití, pokud jejich využití procesoru je 5 % nebo méně a jejich využití sítě je menší než 2 % nebo pokud aktuální zatížení může být přizpůsobeno menší velikosti virtuálního počítače.

Pokud chcete být agresivnější při identifikaci virtuálních počítačů s nízkým využitím, můžete upravit pravidlo průměrného využití procesoru na základě předplatného.  Pravidlo využití procesoru lze nastavit na 5 %, 10 %, 15 % nebo 20 %.

> [!NOTE]
> Chcete-li upravit pravidlo průměrného využití procesoru pro identifikaci virtuálních počítačů s nízkým využitím, musíte být *vlastníkem*předplatného .  Pokud nemáte požadovaná oprávnění pro předplatné nebo skupinu prostředků, možnost zahrnout nebo vyloučit bude zakázána v uživatelském rozhraní. 

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete [poradce](https://aka.ms/azureadvisordashboard).

1. Na panelu akcí klikněte na **Konfigurovat.**

1. Klikněte na kartu **Pravidla.**

1. Vyberte předplatná, pro která chcete upravit pravidlo průměrného využití procesoru, a klikněte na **Upravit**.

1. Vyberte požadovanou průměrnou hodnotu využití procesoru a klepněte na **tlačítko Použít**.

1. Chcete-li aktualizovat stávající doporučení a použít nové pravidlo průměrného využití procesoru, klepněte na tlačítko **Aktualizovat doporučení.** 

   ![Příklad konfigurace pravidel doporučení poradce](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-recommendations"></a>Stáhnout doporučení

Poradce vám umožní stáhnout souhrn vašich doporučení.  Doporučení si můžete stáhnout jako soubor PDF nebo soubor CSV.  Stažení doporučení vám umožní snadno sdílet se svými kolegy nebo provádět vlastní analýzu nad daty doporučení.

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete [poradce](https://aka.ms/azureadvisordashboard).

1. Na panelu akcí klikněte na **Stáhnout jako CSV** nebo **Stáhnout jako PDF.**

Možnost stahování respektuje všechny filtry, které jste použili na řídicí mase Poradce.  Pokud při zobrazení určité kategorie doporučení nebo doporučení vyberete možnost stažení, stažený souhrn obsahuje pouze informace pro danou kategorii nebo doporučení. 

## <a name="next-steps"></a>Další kroky

Další informace o poradci najdete v tématu:

- [Úvod do Azure Advisoru](advisor-overview.md)
- [Doporučení poradce pro vysokou dostupnost](advisor-high-availability-recommendations.md)
- [Doporučení pro zabezpečení poradce](advisor-security-recommendations.md)
- [Doporučení pro výkon poradce](advisor-performance-recommendations.md)
- [Doporučení nákladů poradce](advisor-performance-recommendations.md)
