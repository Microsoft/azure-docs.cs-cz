---
title: Začínáme s Azure Advisor | Microsoft Docs
description: Začínáme s Azure Advisor.
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: makohli
ms.openlocfilehash: cd64515beabec43a5209d62dccf2b55b21702c16
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
ms.locfileid: "30230046"
---
# <a name="get-started-with-azure-advisor"></a>Začínáme se službou Azure Advisor

Zjistěte, jak přístup Advisor prostřednictvím portálu Azure, získat doporučení a implementujte doporučení.

## <a name="get-advisor-recommendations"></a>Doporučení Advisoru

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. V levém podokně klikněte na **Advisor**.  Pokud nevidíte Advisor v levém podokně, klikněte na tlačítko **všechny služby**.  V podokně nabídky služby v rámci **monitorování a správu**, klikněte na tlačítko **Advisor**.
 Se zobrazí řídicí panel služby Advisor.

   ![Přístup k Azure Advisor pomocí portálu Azure](./media/advisor-get-started/advisor-portal-menu.png) 

4. Řídicí panel služby Advisor se zobrazí souhrn vaše doporučení pro všechny vybrané odběry.  Je možné, že odběry, které chcete doporučení, který se má zobrazit pro používání odběru filtrovat rozevíracího seznamu.

5. Chcete-li získat doporučení pro určitou kategorii, klikněte na jednu z karty: **vysokou dostupnost**, **zabezpečení**, **výkonu**, nebo **náklady**. 

  ![Řídicí panel Azure Advisor](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-advisor-recommendation-details-and-implement-a-solution"></a>Získat podrobnosti o doporučení služby Advisor a implementovat řešení

Doporučení můžete vybrat v Advisor k zobrazení dalších podrobností – například akce doporučení a zasažené prostředky – a implementovat řešení doporučení.  

1. Přihlaste se k [portál Azure](https://portal.azure.com)a pak otevřete [Advisor](https://aka.ms/azureadvisordashboard).

2. Vyberte kategorii doporučení, která zobrazí seznam doporučení v rámci této kategorie, nebo vyberte **všechny** zobrazíte všechna doporučení.

3. Klikněte na tlačítko doporučení, které chcete zkontrolovat podrobně.

4. Zkontrolujte informace o doporučení a prostředky, které doporučení platí pro.

5. Klikněte na **Doporučená akce** provést doporučení.

## <a name="filter-advisor-recommendations"></a>Filtrovat doporučení služby Advisor

Můžete filtrovat doporučení, která přejít k podrobnostem a co je pro vás důležité.  Můžete filtrovat podle předplatného, typ prostředku nebo stavu doporučení.  

1. Přihlaste se k [portál Azure](https://portal.azure.com)a pak otevřete [Advisor](https://aka.ms/azureadvisordashboard).

2.  Pomocí rozevíracích seznamů na řídicím panelu služby Advisor můžete filtrovat podle předplatného, typ prostředku nebo stavu doporučení.

    ![Kritéria vyhledávací filtr služby Advisor](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-advisor-recommendations"></a>Odložit nebo zrušit doporučení služby Advisor

1. Přihlaste se k [portál Azure](https://portal.azure.com)a pak otevřete [Advisor](https://aka.ms/azureadvisordashboard).

2. Přejděte na doporučení, které chcete odložit nebo zrušit.

3. Klikněte na tlačítko doporučení.

4. Klikněte na tlačítko **odložit**. 

5. Zadejte časové období odložit, nebo vyberte **nikdy** zrušíte doporučení.

## <a name="exclude-subscriptions-or-resource-groups-from-advisor"></a>Vyloučit z Poradce pro předplatné nebo skupiny prostředků

Můžete mít skupiny prostředků nebo odběrů, pro které nechcete přijmout doporučení služby Advisor – například "test" prostředky.  Můžete nakonfigurovat Advisor pouze generovat doporučení pro konkrétní předplatné a skupiny prostředků.

> [!NOTE]
> Zahrnout nebo vyloučit z Poradce pro předplatné nebo skupinu prostředků, musíte být vlastníkem předplatného.  Pokud nemáte požadovaná oprávnění pro předplatné nebo skupinu prostředků, je možnost zahrnout nebo vyloučit je zakázána v uživatelském rozhraní.

1. Přihlaste se k [portál Azure](https://portal.azure.com)a pak otevřete [Advisor](https://aka.ms/azureadvisordashboard).

2. Klikněte na tlačítko **konfigurace** na panelu akcí.

3. Zrušte zaškrtnutí políčka všechny odběry nebo nechcete dostávat doporučení služby Advisor pro skupiny prostředků.

    ![Advisor nakonfigurovat příklad prostředků](./media/advisor-get-started/advisor-configure-resources.png)

4. Klikněte **použít** tlačítko.

## <a name="configure-the-average-cpu-utilization-rule-for-the-low-usage-virtual-machine-recommendation"></a>Nakonfigurujte pravidlo průměrné využití procesoru doporučení nízkou využití virtuálního počítače

Advisor monitoruje vaše využití virtuálního počítače po dobu 14 dnů a pak identifikuje nízké využití virtuálních počítačů. Virtuální počítače, jejichž průměrné využití procesoru je 5 % nebo méně a využití sítě je 7 MB nebo méně čtyři nebo více dní jsou považovány za nízké využití virtuálních počítačů.

Pokud chcete být agresivnější zjistit nízkém zatížení virtuálních počítačů, můžete upravit průměrná pravidlo využití procesoru na základě za předplatné.  Pravidlo průměrné využití procesoru můžete nastavit na 5 %, 10 %, 15 % nebo 20 %.

> [!NOTE]
> Upravit průměrná pravidlo využití procesoru pro identifikaci nízkém zatížení virtuálních počítačů, musí být předplatné *vlastníka*.  Pokud nemáte požadovaná oprávnění pro předplatné nebo skupinu prostředků, bude v uživatelském rozhraní zakázána možnost zahrnout nebo vyloučit. 

1. Přihlaste se k [portál Azure](https://portal.azure.com)a pak otevřete [Advisor](https://aka.ms/azureadvisordashboard).

2. Klikněte na tlačítko **konfigurace** na panelu akcí.

3. Klikněte **pravidla** kartě.

4. Vyberte předplatná, kterou chcete upravit pravidlo průměrné využití procesoru pro a pak klikněte na **upravit**.

5. Vyberte požadovanou hodnotu průměrné využití procesoru a klikněte na **použít**.

6. Klikněte na tlačítko **aktualizovat doporučení** aktualizovat vaše stávající doporučení použít nové pravidlo průměrné využití procesoru. 

   ![Příklad pravidla doporučení nakonfigurovat Advisor](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-your-advisor-recommendations"></a>Stáhněte si vaše doporučení služby Advisor

Advisor umožňuje stáhnout souhrn vaše doporučení.  Můžete si stáhnout vaše doporučení jako soubor ve formátu PDF nebo soubor CSV.  Stahování vaše doporučení umožňuje snadno sdílet se svými kolegy nebo provádět vlastní analýzu nad data doporučení.

1. Přihlaste se k [portál Azure](https://portal.azure.com)a pak otevřete [Advisor](https://aka.ms/azureadvisordashboard).

2. Klikněte na tlačítko **stáhnout jako sdílený svazek clusteru** nebo **stáhnout ve formátu PDF** na panelu akcí.

Možnost stažení respektuje všechny filtry, které jste použili k řídicímu panelu Advisor.  Pokud vyberete možnost stažení při zobrazení konkrétní doporučení kategorie nebo doporučení, zahrnuje stažené souhrn pouze informace pro tuto kategorii nebo doporučení. 

## <a name="next-steps"></a>Další postup

Další informace o službě Advisor najdete v tématu:
* [Úvod do Azure Advisor](advisor-overview.md)
* [Doporučení pro vysokou dostupnost služby Advisor](advisor-high-availability-recommendations.md)
* [Doporučení zabezpečení Advisor](advisor-security-recommendations.md)
-  [Poradce při hodnocení výkonu doporučení](advisor-performance-recommendations.md)
* [Náklady na doporučení služby Advisor](advisor-performance-recommendations.md)
