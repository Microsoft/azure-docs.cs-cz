---
title: 'Kurz: monitorování a ladění – Azure Database for PostgreSQL – jeden server'
description: Tento kurz vás provede monitorováním a optimalizací na Azure Database for PostgreSQL jednom serveru.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: tutorial
ms.date: 5/6/2019
ms.openlocfilehash: a12068259d82e833826bcac5e6c58059fb51c56c
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336975"
---
# <a name="tutorial-monitor-and-tune-azure-database-for-postgresql---single-server"></a>Kurz: monitorování a ladění Azure Database for PostgreSQL – jeden server

Azure Database for PostgreSQL obsahuje funkce, které vám pomohou pochopit a zlepši výkon vašeho serveru. V tomto kurzu se naučíte, jak:
> [!div class="checklist"]
> * Povolit dotaz a počkat na kolekci statistik
> * Přístup a využívání shromážděných dat
> * Zobrazení dotazování na výkon a statistiky čekání v čase
> * Analýza databáze a získání doporučení k výkonu
> * Použití doporučení k výkonu

## <a name="prerequisites"></a>Předpoklady
Musíte mít server Azure Database for PostgreSQL s PostgreSQL verze 9.6 nebo 10. Chcete-li vytvořit server, můžete sledovat postup v části [Vytvořit kurz](tutorial-design-database-using-azure-portal.md).

> [!IMPORTANT]
> **Query Store** , **Query Performance Insight** a **doporučení k výkonu** jsou ve verzi Public Preview.

## <a name="enabling-data-collection"></a>Povolení shromažďování dat
[Query Store](concepts-query-store.md) na váš server zaznamenává historii dotazů a statistické údaje čekání a ukládá je do databáze na serveru **azure_sys**. Je to funkce vyžadující váš souhlas. Chcete-li ji aktivovat:

1. Otevřete web Azure Portal.

2. Vyberte svůj server Azure Database for PostgreSQL.

3. Vyberte **Parametry serveru** , které najdete v části **Nastavení** na levé straně.

4. Nastavte **pg_qs.query_capture_mode** na **TOP** , aby se začala shromažďovat data o výkonu. Nastavte **pgms_wait_sampling.query_capture_mode** na **VŠE** , aby začaly shromažďovat statistiky čekání. Uložte.
   
   :::image type="content" source="./media/tutorial-performance-intelligence/query-store-parameters.png" alt-text="Parametry serveru Query Store":::

5. Umožňuje až 20 minut první dávky dat pro uchování v databázi **azure_sys**.


## <a name="performance-insights"></a>Informace o výkonu
Zobrazení [Query Performance Insight](concepts-query-performance-insight.md) na portálu Azure, bude přinášet vizualizace o klíčových informacích z Query Storu. 

1. Na stránce portálu vašeho serveru Azure Database for PostgreSQL vyberte **Query Performace Insight** v části nabídky **Podpora a řešení potíží** na levé straně.

2. Karta **Dlouho běžících dotazů** zobrazuje 5 nejčastějších dotazů podle průměrné doby trvání na spuštění, agregované do 15minutových intervalů. 
   
   :::image type="content" source="./media/tutorial-performance-intelligence/query-performance-insight-landing-page.png" alt-text="Úvodní stránka Query Performance Insight":::

   Další dotazy můžete zobrazit tak, že v rozevíracím seznamu vyberete **Počet dotazů**. Barvy grafu se mohou při této akci změnit pro konkrétní ID dotazu.

3. Můžete kliknout a přetáhnout v grafu, abyste zmenšili konkrétní časové okno.

4. Použijte ikony zvětšení a zmenšení, abyste si zobrazili menší nebo větší časový úsek.

5. Zobrazte v tabulce pod grafem, kde zobrazíte další podrobnosti o dlouho běžících dotazech v tomto časovém okně.

6. Vyberte kartu **Statistiky čekání** k zobrazení odpovídající vizualizace týkající se čekání na serveru.
   
   :::image type="content" source="./media/tutorial-performance-intelligence/query-performance-insight-wait-statistics.png" alt-text="Statistiky čekání Query Performance Insight":::

### <a name="permissions"></a>Oprávnění
K zobrazení textu dotazů v Query Performance Insight jsou nutná oprávnění **vlastníka** nebo **přispěvatele**. **Čtenář** může zobrazit grafy a tabulky, ale ne text dotazu.


## <a name="performance-recommendations"></a>Doporučení k výkonu
Funkce [Doporučení k výkonu](concepts-performance-recommendations.md) analyzuje úlohy na serveru, aby identifikoval indexy a případně zlepšil výkon.

1. Otevřete **Doporučení k výkonu** z části nabídky **Podpora a řešení potíží** na stránce portálu Azure pro váš server PostgreSQL.
   
   :::image type="content" source="./media/tutorial-performance-intelligence/performance-recommendations-landing-page.png" alt-text="Úvodní stránka Doporučení k výkonu":::

2. Vyberte **Analyzovat** a zvolte databázi. Tím se spustí analýza.

3. V závislosti na úlohách to může trvat několik minut. Po dokončení analýzy se zobrazí oznámení na portálu.

4. Okno **Doporučení k výkonu** zobrazí seznam doporučení, jestliže byla nějaká nalezena. 

5. Doporučení zobrazí informace o příslušné **databázi** , **tabulce** , **sloupci** a **velikosti indexu**.

   :::image type="content" source="./media/tutorial-performance-intelligence/performance-recommendations-result.png" alt-text="Výsledky Doporučení k výkonu":::

6. Chcete-li implementovat doporučení, zkopírujte text dotazu a spusťte z klienta svého výběru.

### <a name="permissions"></a>Oprávnění
Pro spuštění funkce analýzy za použití funkce Doporučení k výkonu potřebujete oprávnění **vlastníka** nebo **přispěvatele**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste vytvořili prostředky Azure ve skupině serverů. Pokud neočekáváte, že tyto prostředky budete potřebovat v budoucnu, odstraňte skupinu serverů. Stiskněte tlačítko *Odstranit* na stránce *Přehled* pro skupinu serverů. Po zobrazení výzvy na místní stránce potvrďte název skupiny serverů a klikněte na tlačítko poslední *Odstranit* .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> Další informace o [sledování a ladění ](concepts-monitoring.md) ve službě Azure Database for PostgreSQL.