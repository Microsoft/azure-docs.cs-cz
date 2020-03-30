---
title: Query Performance Insight
description: Monitorování výkonu dotazů identifikuje nejvíce náročné procesoru a dlouhotrvající dotazy pro jednu a sdružené databáze v databázi Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: f5998fde6659715de4fcb533cb0f41a8939b1c48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214060"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Přehled výkonu dotazu pro Azure SQL Database

Přehled výkonu dotazu poskytuje inteligentní analýzu dotazů pro jednu a sdruženou databázi. Pomáhá identifikovat hlavní prostředky náročné a dlouhotrvající dotazy ve vaší úlohě. To vám pomůže najít dotazy pro optimalizaci pro zlepšení celkového výkonu pracovního vytížení a efektivně využívat prostředek, který platíte. Přehled výkonu dotazu vám pomůže strávit méně času odstraňováním problémů s výkonem databáze tím, že poskytuje:

* Hlubší přehled o spotřebě prostředků databáze (DTU)
* Podrobnosti o nejvyšší databázové dotazy podle procesoru, trvání a počet spuštění (potenciální optimalizace kandidátů pro zlepšení výkonu)
* Možnost přejít k podrobnostem dotazu a zobrazit text dotazu a historii využití prostředků
* Poznámky, které zobrazují doporučení výkonu od [poradců pro databázi](sql-database-advisor.md)

![Query Performance Insight](./media/sql-database-query-performance/opening-title.png)

## <a name="prerequisites"></a>Požadavky

Přehled výkonu dotazu vyžaduje, aby bylo [úložiště dotazů](https://msdn.microsoft.com/library/dn817826.aspx) aktivní ve vaší databázi. Ve výchozím nastavení je automaticky povolená pro všechny databáze Azure SQL. Pokud query store není spuštěn, portál Azure vás vyzve k jeho povolení.

> [!NOTE]
> Pokud se na portálu zobrazí zpráva "Úložiště dotazů není správně nakonfigurováno v této databázi", přečtěte si téma [Optimalizace konfigurace úložiště dotazů](#optimize-the-query-store-configuration).

## <a name="permissions"></a>Oprávnění

K použití přehledu výkonu dotazu potřebujete následující oprávnění [k řízení přístupu na základě rolí:](../role-based-access-control/overview.md)

* **K**zobrazení nejvyšších dotazů a grafů náročných na prostředky jsou vyžadována oprávnění aplikace Reader , **Owner**, **Contributor**, **SQL DB Contributor**nebo SQL Server **Contributor.**
* K zobrazení textu **dotazu**jsou vyžadována oprávnění **vlastníka**, přispěvatele **, přispěvatele SQL DB**nebo **přispěvatele serveru SQL Server.**

## <a name="use-query-performance-insight"></a>Použití nástroje Query Performance Insight

Přehled výkonu dotazu se snadno používá:

1. Otevřete [portál Azure](https://portal.azure.com/) a najděte databázi, kterou chcete prozkoumat.
2. V nabídce na levé straně otevřete **přehled** > **výkonu dotazů inteligentního**výkonu .
  
   ![Přehled výkonu dotazu v nabídce](./media/sql-database-query-performance/tile.png)

3. Na první kartě zkontrolujte seznam hlavních dotazů náročných na prostředky.
4. Vyberte jednotlivé dotazy, chcete-li zobrazit jeho podrobnosti.
5. Otevřete**doporučení pro výkon inteligentního** **výkonu** > a zkontrolujte, zda jsou k dispozici nějaká doporučení pro výkon. Další informace o předdefinovaných doporučeních výkonu naleznete v [tématu SQL Database Advisor](sql-database-advisor.md).
6. Ke změně pozorovaného intervalu použijte posuvníky nebo ikony přiblížení.

   ![Řídicí panel výkonu](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Pro SQL Database k vykreslení informací v přehledu výkonu dotazu, query Store potřebuje zachytit několik hodin dat. Pokud databáze nemá žádnou aktivitu nebo pokud úložiště dotazů nebylo během určitého období aktivní, budou grafy prázdné, když přehled výkonu dotazu zobrazí tento časový rozsah. Úložiště dotazů můžete kdykoli povolit, pokud není spuštěno. Další informace naleznete v [tématu Doporučené postupy v úložišti dotazů](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store).
>

Pro doporučení výkonu databáze vyberte [doporučení](sql-database-advisor.md) na navigačním okně Přehled výkonu dotazu.

![Karta Doporučení](./media/sql-database-query-performance/ia.png)

## <a name="review-top-cpu-consuming-queries"></a>Kontrola nejlepších dotazů náročných na procesor

Ve výchozím nastavení přehled výkonu dotazů zobrazuje prvních pět dotazů náročných na procesor při prvním otevření.

1. Vyberte nebo zrušte zaškrtnutí jednotlivých dotazů, které chcete zahrnout nebo vyloučit z grafu pomocí zaškrtávacích políček.

   Horní řádek zobrazuje celkové procento DTU pro databázi. Pruhy zobrazují procento procesoru, které vybrané dotazy spotřebované během vybraného intervalu. Pokud je například vybrán a vybrán **minulý týden,** představuje každý pruh jeden den.

   ![Hlavní dotazy](./media/sql-database-query-performance/top-queries.png)

   > [!IMPORTANT]
   > Zobrazený řádek DTU je agregován na hodnotu maximální spotřeby v jednohodinových obdobích. Je určen pro porovnání na vysoké úrovni pouze se statistikami provádění dotazů. V některých případech využití DTU může zdát příliš vysoká ve srovnání s provedené dotazy, ale to nemusí být případ.
   >
   > Například pokud dotaz maximální množství DTU na 100 % pouze několik minut, řádek DTU v přehledu výkonu dotazu zobrazí celou hodinu spotřeby jako 100 % (důsledkem maximální agregované hodnoty).
   >
   > Pro jemnější porovnání (až jednu minutu), zvažte vytvoření vlastního grafu využití DTU:
   >
   > 1. Na webu Azure Portal vyberte **Azure SQL Database** > **Monitoring**.
   > 2. Vyberte **Metriky**.
   > 3. Vyberte **+Přidat graf**.
   > 4. V grafu vyberte procento DTU.
   > 5. Kromě toho v levém horním rohu vyberte **poslední 24 hodin** a změňte ji na jednu minutu.
   >
   > Použijte vlastní graf DTU s jemnější úrovní podrobností pro porovnání s grafem spuštění dotazu.

   Dolní mřížka zobrazuje souhrnné informace pro viditelné dotazy:

   * ID dotazu, což je jedinečný identifikátor pro dotaz v databázi.
   * Procesor na dotaz během pozorovatelného intervalu, který závisí na funkci agregace.
   * Doba trvání na dotaz, která také závisí na funkci agregace.
   * Celkový počet spuštění pro konkrétní dotaz.

2. Pokud budou data zastaralá, vyberte tlačítko **Aktualizovat.**

3. Pomocí posuvníků a tlačítek lupy můžete změnit interval pozorování a prozkoumat špičky spotřeby:

   ![Posuvníky a tlačítka lupy pro změnu intervalu](./media/sql-database-query-performance/zoom.png)

4. Volitelně můžete vybrat kartu **Vlastní a** přizpůsobit zobrazení pro:

   * Metrika (CPU, doba trvání, počet spuštění).
   * Časový interval (posledních 24 hodin, minulý týden nebo minulý měsíc).
   * Počet dotazů.
   * Agregační funkce.
  
   ![Vlastní karta](./media/sql-database-query-performance/custom-tab.png)
  
5. Chcete-li zobrazit přizpůsobené zobrazení, vyberte tlačítko **Přejít >.**

   > [!IMPORTANT]
   > Přehled výkonu dotazu je omezen na zobrazení prvních 5-20 náročných dotazů v závislosti na vašem výběru. Databáze může spustit mnohem více dotazů mimo ty hlavní zobrazené a tyto dotazy nebudou zahrnuty do grafu.
   >
   > Může existovat typ úlohy databáze, ve kterém velké množství menších dotazů, mimo ty nejlepší zobrazené, často běží a používá většinu DTU. Tyto dotazy se nezobrazují v grafu výkonu.
   >
   > Například dotaz může mít spotřebované značné množství DTU na chvíli, i když jeho celková spotřeba v sledovaném období je menší než ostatní dotazy s nejvyšší spotřebou. V takovém případě by se využití prostředků tohoto dotazu v grafu neobjevilo.
   >
   > Pokud potřebujete porozumět spuštění horní dotazů nad rámec omezení přehledu výkonu dotazu, zvažte použití [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) pro pokročilé monitorování výkonu databáze a řešení potíží.
   >

## <a name="view-individual-query-details"></a>Zobrazit podrobnosti o jednotlivých dotazech

Zobrazení podrobností o dotazu:

1. Vyberte libovolný dotaz v seznamu hlavních dotazů.

    ![Seznam hlavních dotazů](./media/sql-database-query-performance/details.png)

   Otevře se podrobný pohled. Zobrazuje spotřebu procesoru, dobu trvání a počet spuštění v průběhu času.

2. Vyberte funkce grafu pro podrobnosti.

   * Horní graf zobrazuje čáru s celkovým procentem Databáze DTU. Pruhy jsou procento procesoru, které vybraný dotaz spotřebovává.
   * Druhý graf zobrazuje celkovou dobu trvání vybraného dotazu.
   * Dolní graf zobrazuje celkový počet spuštění podle vybraného dotazu.

   ![Podrobnosti o dotazu](./media/sql-database-query-performance/query-details.png)

3. Volitelně můžete použít posuvníky, použít tlačítka lupy nebo vybrat **Nastavení** pro přizpůsobení způsobu zobrazení dat dotazu nebo výběr jiného časového rozsahu.

   > [!IMPORTANT]
   > Přehled výkonu dotazu nezachycuje žádné dotazy DDL. V některých případech nemusí zachytit všechny dotazy ad hoc.
   >

## <a name="review-top-queries-per-duration"></a>Kontrola hlavních dotazů na dobu trvání

Dvě metriky v přehledu výkonu dotazů vám můžou pomoct najít potenciální kritická místa: počet trvání a spuštění.

Dlouhotrvající dotazy mají největší potenciál pro uzamčení prostředků déle, blokování ostatních uživatelů a omezení škálovatelnosti. Jsou také nejlepšími kandidáty na optimalizaci.

Identifikace dlouhotrvajících dotazů:

1. Otevřete kartu **Vlastní** v přehledu výkonu dotazu pro vybranou databázi.
2. Změňte metriky na **dobu trvání**.
3. Vyberte počet dotazů a interval pozorování.
4. Vyberte funkci agregace:

   * **Součet** sečte všechny čas spuštění dotazu pro celý interval pozorování.
   * **Max** vyhledá dotazy, ve kterých byla doba provádění maximální pro celý interval pozorování.
   * **Avg** vyhledá průměrnou dobu provádění všech spuštění dotazu a zobrazí ty nejlepší pro tyto průměry.

   ![Doba trvání dotazu](./media/sql-database-query-performance/top-duration.png)

5. Chcete-li zobrazit přizpůsobené zobrazení, vyberte tlačítko **Přejít >.**

   > [!IMPORTANT]
   > Úprava zobrazení dotazu neaktualizuje řádek DTU. Řádek DTU vždy zobrazuje hodnotu maximální spotřeby intervalu.
   >
   > Chcete-li pochopit spotřebu databáze DTU s více podrobnostmi (až jednu minutu), zvažte vytvoření vlastního grafu na webu Azure Portal:
   >
   > 1. Vyberte **Azure SQL Database** > **Monitoring**.
   > 2. Vyberte **Metriky**.
   > 3. Vyberte **+Přidat graf**.
   > 4. V grafu vyberte procento DTU.
   > 5. Kromě toho v levém horním rohu vyberte **poslední 24 hodin** a změňte ji na jednu minutu.
   >
   > Doporučujeme použít vlastní graf DTU pro porovnání s grafem výkonu dotazu.
   >

## <a name="review-top-queries-per-execution-count"></a>Kontrola hlavních dotazů na počet spuštění

Uživatelská aplikace, která používá databázi může získat pomalé, i když vysoký počet spuštění nemusí mít vliv na samotnou databázi a využití prostředků je nízká.

V některých případech může vysoký počet spuštění vést k další síti zpáteční cesty. Okružní jízdy ovlivňují výkon. Podléhají latenci sítě a latenci serveru pro příjem dat.

Například mnoho webových stránek založených na datech silně přistupuje k databázi pro každý požadavek uživatele. Přestože pomáhá sdružování připojení, zvýšené zatížení sítě a zatížení zpracování na databázovém serveru může zpomalit výkon. Obecně platí, že udržet zpáteční cesty na minimum.

Chcete-li identifikovat často prováděné ("upovídaný") dotazy:

1. Otevřete kartu **Vlastní** v přehledu výkonu dotazu pro vybranou databázi.
2. Změňte metriky na **počet spuštění**.
3. Vyberte počet dotazů a interval pozorování.
4. Chcete-li zobrazit přizpůsobené zobrazení, vyberte tlačítko **Přejít >.**

   ![Počet spuštění dotazu](./media/sql-database-query-performance/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>Principy anotací ladění výkonu

Při zkoumání úlohy v přehledu výkonu dotazu si můžete všimnout ikon se svislou čárou v horní části grafu.

Tyto ikony jsou poznámky. Zobrazují doporučení výkonu od [nástroje SQL Database Advisor](sql-database-advisor.md). Najetím ukazatele nad poznámku můžete získat souhrnné informace o doporučeních výkonu.

   ![Anotace dotazu](./media/sql-database-query-performance/annotation.png)

Pokud chcete více porozumět doporučení poradce nebo použít doporučení poradce, vyberte ikonu a otevřete podrobnosti o doporučené akci. Pokud se jedná o aktivní doporučení, můžete jej použít ihned z portálu.

   ![Podrobnosti poznámky k dotazu](./media/sql-database-query-performance/annotation-details.png)

V některých případech je vzhledem k úrovni přiblížení možné, že poznámky blízko sebe jsou sbaleny do jedné poznámky. Přehled výkonu dotazu představuje tuto ikonu poznámky skupiny. Když vyberete ikonu anotace skupiny, otevře se nové okno se seznamem anotací.

Korelační dotazy a akce optimalizace výkonu vám mohou pomoci lépe porozumět vaší pracovní zátěži.

## <a name="optimize-the-query-store-configuration"></a>Optimalizace konfigurace úložiště dotazů

Při použití přehledu výkonu dotazu se mohou zobrazit následující chybové zprávy úložiště dotazů:

* "Úložiště dotazů není v této databázi správně nakonfigurováno. Klikněte zde se dozvíte více."
* "Úložiště dotazů není v této databázi správně nakonfigurováno. Chcete-li změnit nastavení, klepněte sem."

Tyto zprávy se obvykle zobrazí, když úložiště dotazů nemůže shromažďovat nová data.

První případ se stane, když je úložiště dotazů ve stavu jen pro čtení a parametry jsou nastaveny optimálně. Tento problém můžete vyřešit zvětšením velikosti úložiště dat nebo zrušením zaškrtnutí úložiště dotazů. (Pokud vymažete úložiště dotazů, dojde ke ztrátě všech dříve shromážděných telemetrických dat.)

   ![Podrobnosti o úložišti dotazů](./media/sql-database-query-performance/qds-off.png)

Druhý případ se stane, když úložiště dotazů není povoleno nebo parametry nejsou nastaveny optimálně. Můžete změnit zásady uchovávání a zachycení a také povolit Úložiště dotazů spuštěním následujících příkazů poskytnutých z [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) nebo portálu Azure.

### <a name="recommended-retention-and-capture-policy"></a>Doporučená zásada uchovávání a sběru

Existují dva typy zásad uchovávání informací:

* **Na základě velikosti**: Pokud je tato zásada nastavena na **hodnotu AUTO**, automaticky vyčistí data, když je dosaženo téměř maximální velikosti.
* **Čas na základě**: Ve výchozím nastavení je tato zásada nastavena na 30 dní. Pokud v úložišti dotazů dojde místo, odstraní informace o dotazu starší než 30 dní.

Zásady sběru můžete nastavit takto:

* **Vše**: Úložiště dotazů zachycuje všechny dotazy.
* **Auto**: Úložiště dotazů ignoruje méně časté dotazy a dotazy s nevýznamnou dobou kompilace a spuštění. Prahové hodnoty pro počet spuštění, dobu trvání kompilace a dobu trvání běhu jsou interně určeny. Toto je výchozí možnost.
* **Žádné**: Úložiště dotazů zastaví zachytávání nových dotazů, ale statistiky běhu pro již zachycené dotazy jsou stále shromažďovány.

Doporučujeme nastavit všechny zásady **auto** a zásady čištění na 30 dní provedením následujících příkazů z [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) nebo portálu Azure. (Nahraďte `YourDB` název databáze.)

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

Zvětšete velikost úložiště dotazů připojením k databázi prostřednictvím [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) nebo portálu Azure a spuštěním následujícího dotazu. (Nahraďte `YourDB` název databáze.)

```SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

Použití těchto nastavení nakonec způsobí, že úložiště dotazů shromáždí telemetrická data pro nové dotazy. Pokud potřebujete, aby query store fungovalhned, můžete volitelně vymazat Úložiště dotazů spuštěním následujícího dotazu prostřednictvím SSMS nebo portálu Azure. (Nahraďte `YourDB` název databáze.)

> [!NOTE]
> Spuštěním následujícího dotazu odstraníte všechny dříve shromážděné monitorované telemetrie v úložišti dotazů.

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="next-steps"></a>Další kroky

Zvažte použití [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) pro pokročilé monitorování výkonu velké flotily jednoa sdružených databází, elastických fondů, spravovaných instancí a databází instancí.
