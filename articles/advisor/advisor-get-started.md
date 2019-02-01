---
title: Začínáme s Azure Advisor | Dokumentace Microsoftu
description: Začínáme s využitím Azure Advisoru.
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: kasparks
ms.openlocfilehash: aabb316cf564520e6ea5e8689f1c18c98b94ab3c
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55488471"
---
# <a name="get-started-with-azure-advisor"></a>Začínáme se službou Azure Advisor

Zjistěte, jak přistupovat k služby Advisor na webu Azure portal, doporučení a implementace doporučení.

> [!NOTE]
> Azure Advisor se automaticky spustí na pozadí k vyhledání nově vytvořené prostředky. Může trvat až 24 hodin se poskytovat doporučení týkající se na tyto prostředky.

## <a name="get-recommendations"></a>Získejte doporučení

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V levém podokně klikněte na tlačítko **Advisor**.  Pokud služby Advisor v levém podokně nevidíte, klikněte na tlačítko **všechny služby**.  Na panelu nabídky služby klikněte v části **monitorování a správu**, klikněte na tlačítko **Advisor**. Zobrazí se řídicí panel služby Advisor.

   ![Přístup k Azure Advisoru pomocí webu Azure portal](./media/advisor-get-started/advisor-portal-menu.png) 

1. Řídicí panel služby Advisor zobrazí přehled doporučení pro všechny vybrané odběry.  Můžete zvolit, že rozevírací nabídka filtrů odběrů, který má doporučení, která se zobrazí u pomocí svého předplatného.

1. Pokud chcete získat doporučení pro určitou kategorii, klikněte na jednu z karet: **Vysoká dostupnost**, **zabezpečení**, **výkonu**, nebo **náklady**. 

  ![Řídicí panel Azure Advisoru](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-recommendation-details-and-implement-a-solution"></a>Získat podrobnosti o doporučení a implementaci řešení

Doporučení můžete vybrat v Advisoru, chcete-li zobrazit další podrobnosti – například doporučení akce a ovlivněných prostředků – a k implementaci řešení pro doporučení.  

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a pak otevřete [Advisor](https://aka.ms/azureadvisordashboard).

1. Vyberte kategorii doporučení pro zobrazení seznamu doporučení v rámci dané kategorie, nebo vyberte **všechny** kartu, chcete-li zobrazit všechna doporučení.

1. Kliknutím na doporučení, který chcete zkontrolovat podrobně.

1. Přečtěte si informace o doporučení a prostředky, které platí doporučení pro.

1. Klikněte na **Doporučená akce** implementovat doporučení.

## <a name="filter-recommendations"></a>Filtrovat doporučení

Můžete filtrovat doporučení k podrobnostem a co je pro vás nejdůležitější.  Můžete filtrovat podle předplatného, typ prostředku nebo stav doporučení.  

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a pak otevřete [Advisor](https://aka.ms/azureadvisordashboard).

1. Použití rozevíracích seznamech na řídicí panel služby Advisor můžete filtrovat podle předplatného, typ prostředku nebo stav doporučení.

    ![Kritéria pro vyhledávací filtr služby Advisor](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-recommendations"></a>Posunout nebo zavřít doporučení

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a pak otevřete [Advisor](https://aka.ms/azureadvisordashboard).

1. Přejděte do doporučení, které chcete odložit nebo zavřít.

1. Kliknutím doporučení.

1. Klikněte na tlačítko **odložit**. 

1. Zadejte časové období odložit, nebo vyberte **nikdy** zavřete doporučení.

## <a name="exclude-subscriptions-or-resource-groups"></a>Vyloučit předplatných nebo skupinách prostředků

Můžete mít skupiny prostředků nebo předplatná, pro které nechcete pro příjem doporučení Advisoru – například "test" prostředky.  Můžete nakonfigurovat Advisor pouze generování doporučení pro konkrétní předplatné a skupiny prostředků.

> [!NOTE]
> Zahrnout nebo vyloučit z Poradce pro předplatné nebo skupinu prostředků, musíte být vlastníkem předplatného.  Pokud nemáte požadovaná oprávnění pro předplatné nebo skupinu prostředků, je zakázána možnost zahrnout nebo vyloučit v uživatelském rozhraní.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a pak otevřete [Advisor](https://aka.ms/azureadvisordashboard).

1. Klikněte na tlačítko **konfigurovat** na panelu akcí.

1. Zrušte zaškrtnutí políčka žádná předplatná ani nechcete dostávat doporučení Advisoru pro skupiny prostředků.

    ![Příklad prostředků konfigurace advisoru](./media/advisor-get-started/advisor-configure-resources.png)

1. Klikněte na tlačítko **použít** tlačítko.

## <a name="configure-low-usage-vm-recommendation"></a>Konfigurace s nízkým využitím doporučení pro virtuální počítače

Tento postup nakonfiguruje pravidlo průměrné využití procesoru doporučení pro virtuální počítače s nízkým využitím.

Advisor sleduje využívání virtuálních počítačů po dobu 14 dnů a pak identifikuje nízké využití virtuálních počítačů. Virtuální počítače, jejichž průměrné využití procesoru je 5 % nebo méně a využití sítě je 7 MB nebo méně čtyři nebo více dny jsou považovány za nízké využití virtuálních počítačů.

Pokud chcete být mnohem vyššími zjistit málo využívaných virtuálních počítačů, můžete upravit průměrné využití procesoru pravidlo na bázi předplatného.  Pravidlo průměrné využití procesoru můžete nastavit na 5 %, 10 %, 15 % nebo 20 %.

> [!NOTE]
> Chcete-li upravit průměrné pravidlo využití procesoru pro identifikaci málo využívaných virtuálních počítačů, musí být předplatné *vlastníka*.  Pokud nemáte požadovaná oprávnění pro skupinu prostředků nebo předplatného, bude v uživatelském rozhraní zakázána možnost zahrnout nebo vyloučit. 

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a pak otevřete [Advisor](https://aka.ms/azureadvisordashboard).

1. Klikněte na tlačítko **konfigurovat** na panelu akcí.

1. Klikněte na tlačítko **pravidla** kartu.

1. Vyberte předplatné, které chcete upravit průměrné pravidlo využití procesoru pro a pak klikněte na **upravit**.

1. Vyberte požadovanou hodnotu průměrné využití procesoru a klikněte na tlačítko **použít**.

1. Klikněte na tlačítko **aktualizovat doporučení** aktualizovat vaše stávající doporučení použít nové pravidlo průměrné využití procesoru. 

   ![Konfigurace příklad pravidla doporučení advisoru](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-recommendations"></a>Stáhnout doporučení

Advisor vám umožní stáhnout přehled doporučení.  Můžete stáhnout svá doporučení jako PDF soubor nebo soubor CSV.  Stáhnout svá doporučení umožňuje snadno sdílet se svými kolegy nebo provádět vlastní analýzy nad daty doporučení.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a pak otevřete [Advisor](https://aka.ms/azureadvisordashboard).

1. Klikněte na tlačítko **stáhnout jako soubor CSV** nebo **stáhnout jako soubor PDF** na panelu akcí.

Možnost stažení respektuje všechny filtry, které jste použili na řídicí panel služby Advisor.  Pokud vyberete možnost stažení při prohlížení konkrétní doporučení kategorie nebo doporučení, stažené souhrn obsahuje pouze informace pro tuto kategorii nebo doporučení. 

## <a name="next-steps"></a>Další postup

Další informace o službě Advisor najdete v tématu:

- [Úvod do Azure Advisoru](advisor-overview.md)
- [Poradce doporučení k vysoké dostupnosti](advisor-high-availability-recommendations.md)
- [Bezpečnostní doporučení advisoru](advisor-security-recommendations.md)
- [Poradce doporučení k výkonu](advisor-performance-recommendations.md)
- [Náklady na doporučení advisoru](advisor-performance-recommendations.md)
