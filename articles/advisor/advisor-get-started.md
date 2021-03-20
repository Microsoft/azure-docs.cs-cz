---
title: Začínáme se službou Azure Advisor
description: Začínáme s Azure Advisor.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: e91049077502a6c0eedf0a4f979c073690c214da
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85117858"
---
# <a name="get-started-with-azure-advisor"></a>Začínáme se službou Azure Advisor

Naučte se, jak získat přístup ke službě Advisor prostřednictvím Azure Portal, získat doporučení a implementovat doporučení.

> [!NOTE]
> Azure Advisor se na pozadí automaticky spustí a najde nově vytvořené prostředky. Poskytnutí doporučení na tyto prostředky může trvat až 24 hodin.

## <a name="get-recommendations"></a>Získat doporučení

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. V levém podokně klikněte na možnost **Poradce**.  Pokud v levém podokně nevidíte poradce, klikněte na **všechny služby**.  V podokně nabídky služby v části **monitorování a Správa** klikněte na možnost **Poradce**. Zobrazí se řídicí panel Advisor.

   ![Přístup k Azure Advisor pomocí Azure Portal](./media/advisor-get-started/advisor-portal-menu.png) 

1. Na řídicím panelu služby Advisor se zobrazí souhrn doporučení pro všechna vybraná předplatná.  Předplatná, pro která chcete zobrazit doporučení, můžete zvolit pomocí rozevíracího seznamu filtru předplatných.

1. Pokud chcete získat doporučení pro konkrétní kategorii, klikněte na jednu z karet: **spolehlivost**, **zabezpečení**, **výkon** nebo **náklady**. 

   ![Řídicí panel Azure Advisor](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-recommendation-details-and-implement-a-solution"></a>Získání podrobných informací o doporučeních a implementace řešení

Můžete vybrat doporučení v Advisoru a zobrazit další podrobnosti, například akce doporučení a ovlivněné prostředky – a implementovat řešení do doporučení.  

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a pak otevřete [Poradce](https://aka.ms/azureadvisordashboard).

1. Vyberte kategorii doporučení, abyste zobrazili seznam doporučení v rámci této kategorie, nebo vyberte kartu **vše** , abyste zobrazili všechna vaše doporučení.

1. Klikněte na doporučení, které chcete zkontrolovat podrobněji.

1. Projděte si informace o doporučení a prostředcích, na které se doporučení vztahuje.

1. Chcete-li implementovat doporučení, klikněte na **doporučenou akci** .

## <a name="filter-recommendations"></a>Doporučení pro filtrování

Můžete filtrovat doporučení a přejít k podrobnostem o tom, co je pro vás nejdůležitější.  Můžete filtrovat podle předplatného, typu prostředku nebo stavu doporučení.  

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a pak otevřete [Poradce](https://aka.ms/azureadvisordashboard).

1. Pomocí rozevíracích seznamů na řídicím panelu Advisor můžete filtrovat podle předplatného, typu prostředku nebo stavu doporučení.

    ![Kritéria vyhledávacího filtru pro poradce](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-recommendations"></a>Odložit nebo zavřít doporučení

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a pak otevřete [Poradce](https://aka.ms/azureadvisordashboard).

1. Přejděte na doporučení, které chcete odložit nebo zrušit.

1. Klikněte na doporučení.

1. Klikněte na tlačítko **odložit**. 

1. Zadejte časový interval odložení nebo vyberte možnost **nikdy** nezrušit doporučení.

## <a name="exclude-subscriptions-or-resource-groups"></a>Vyloučení předplatných nebo skupin prostředků

Můžete mít skupiny prostředků nebo předplatná, pro které nechcete přijímat doporučení služby Advisor, například prostředky test.  Službu Advisor můžete nakonfigurovat tak, aby vygenerovala jenom doporučení pro konkrétní předplatná a skupiny prostředků.

> [!NOTE]
> Pokud chcete zahrnout nebo vyloučit předplatné nebo skupinu prostředků z Advisoru, musíte být vlastníkem předplatného.  Pokud nemáte požadovaná oprávnění pro předplatné nebo skupinu prostředků, možnost zahrnout nebo vyloučit je v uživatelském rozhraní zakázaná.

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a pak otevřete [Poradce](https://aka.ms/azureadvisordashboard).

1. Na panelu akcí klikněte na **Konfigurovat** .

1. Zrušte kontrolu předplatných nebo skupin prostředků, pro které nechcete dostávat doporučení služby Advisor.

    ![Příklad konfigurace prostředků Advisoru](./media/advisor-get-started/advisor-configure-resources.png)

1. Klikněte na tlačítko **použít** .

## <a name="configure-low-usage-vm-recommendation"></a>Konfigurace doporučení pro virtuální počítače s nízkou spotřebou

Tento postup nakonfiguruje průměrné pravidlo využití procesoru pro doporučení pro virtuální počítače s nízkým využitím.

Advisor monitoruje využití virtuálních počítačů po dobu 7 dní a pak identifikuje virtuální počítače s nízkým využitím. Virtuální počítače se považují za nízké využití, pokud jejich využití CPU je 5% nebo méně a jejich využití sítě je méně než 2%, nebo pokud je možné aktuální zatížení přizpůsobit menší velikosti virtuálního počítače.

Pokud chcete mít více agresivní při identifikaci virtuálních počítačů s nízkým využitím, můžete pro každé předplatné upravit průměrné pravidlo využití procesoru.  Pravidlo využití CPU lze nastavit na 5%, 10%, 15% nebo 20%.

> [!NOTE]
> Chcete-li upravit průměrné pravidlo využití procesoru pro identifikaci virtuálních počítačů s nízkým využitím, musíte být *vlastníkem* předplatného.  Pokud nemáte požadovaná oprávnění pro předplatné nebo skupinu prostředků, možnost zahrnutí nebo vyloučení se v uživatelském rozhraní zakáže. 

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a pak otevřete [Poradce](https://aka.ms/azureadvisordashboard).

1. Na panelu akcí klikněte na **Konfigurovat** .

1. Klikněte na kartu **pravidla** .

1. Vyberte předplatné, pro které chcete upravit průměrné pravidlo využití procesoru pro, a pak klikněte na **Upravit**.

1. Vyberte požadovanou průměrnou hodnotu využití procesoru a klikněte na **použít**.

1. Kliknutím na **aktualizovat doporučení** aktualizujete stávající doporučení, aby používala nové průměrné pravidlo využití procesoru. 

   ![Příklad pravidel doporučení pro konfiguraci poradce](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-recommendations"></a>Doporučení ke stažení

Poradce umožňuje stáhnout souhrn vašich doporučení.  Doporučení si můžete stáhnout jako soubor PDF nebo soubor CSV.  Stažení vašich doporučení vám umožní snadnou sdílení s kolegy nebo provádět vlastní analýzu nad daty doporučení.

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a pak otevřete [Poradce](https://aka.ms/azureadvisordashboard).

1. Na panelu akcí klikněte na **Stáhnout jako sdílený svazek clusteru** nebo **Stáhnout jako PDF** .

Možnost stažení respektuje všechny filtry, které jste použili na řídicí panel poradce.  Pokud vyberete možnost stáhnout při prohlížení konkrétní kategorie nebo doporučení doporučení, stažený souhrn obsahuje jenom informace o této kategorii nebo doporučení. 

## <a name="next-steps"></a>Další kroky

Další informace o službě Advisor najdete v těchto tématech:

- [Seznámení s Azure Advisorem](advisor-overview.md)
- [Doporučení pro spolehlivost Advisoru](advisor-high-availability-recommendations.md)
- [Doporučení pro zabezpečení Advisoru](advisor-security-recommendations.md)
- [Doporučení pro výkon Advisoru](advisor-performance-recommendations.md)
- [Doporučení pro náklady na poradce](advisor-cost-recommendations.md)
- [Doporučení provozní kvality Advisoru](advisor-operational-excellence-recommendations.md)
