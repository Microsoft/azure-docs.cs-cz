---
title: Pro službu Azure SQL Database Query Performance Insight | Dokumentace Microsoftu
description: Sledování výkonu dotazů identifikuje většina využívání procesoru dotazů pro databázi Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 5d892005881436dec89c0d0d010f7f02e7bdebf9
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54039193"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Query Performance Insight pro službu Azure SQL Database

Správa a optimalizace výkonu relačních databází trvá odborných znalostí a čas. Query Performance Insight je součástí produktovou řadu inteligentních výkonu Azure SQL Database. Pomůže vám strávit míň času tím, že poskytuje řešení potíží s výkonem databáze:

* Podrobnější přehledy o využití prostředků (DTU) databáze.
* Podrobnosti o nejvyšší databázové dotazy podle počtu CPU, doba trvání a spuštění (potenciál optimalizace kandidáty pro zlepšení výkonu).
* Možnost Přejít na podrobnosti o dotazu, chcete-li zobrazit text dotazu a historie využití prostředků.
* Poznámky, které ukazují doporučení k výkonu z [služby SQL Database Advisor](sql-database-advisor.md).

![Query Performance Insight](./media/sql-database-query-performance/opening-title.png)

> [!TIP]
> Monitorování základního výkonu pomocí Azure SQL Database, doporučujeme Query Performance Insight. Poznámka: omezení produkt publikovat v tomto článku. Pro pokročilé monitorování výkonu databáze ve velkém měřítku, doporučujeme [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md). To má vestavěné inteligentní algoritmy pro řešení potíží s výkonem automatizované. Automaticky vyladit některé z nejběžnějších problémů s výkonem databáze, doporučujeme [automatické ladění](sql-database-automatic-tuning.md).

## <a name="prerequisites"></a>Požadavky

Query Performance Insight vyžaduje, aby [Query Store](https://msdn.microsoft.com/library/dn817826.aspx) aktivní ve vaší databázi. Pro všechny databáze Azure SQL je automaticky povolen ve výchozím nastavení. Pokud Store dotaz není spuštěn, na webu Azure portal vás vyzve, aby je.

> [!NOTE]
> Pokud na portálu se zobrazí zpráva "Query Store není správně nakonfigurována v této databázi", přečtěte si téma [optimalizace konfigurace Query Store](#optimize-the-query-store-configuration-for-query-performance-insight).
>

## <a name="permissions"></a>Oprávnění

Budete potřebovat následující [řízení přístupu na základě rolí](../role-based-access-control/overview.md) oprávnění k použití nástroje Query Performance Insight:

* **Čtečka**, **vlastníka**, **Přispěvatel**, **Přispěvatel databází SQL**, nebo **Přispěvatel SQL serveru** jsou vyžadována oprávnění Chcete-li zobrazit nejčastější dotazy využívání prostředků a grafy.
* **Vlastník**, **Přispěvatel**, **Přispěvatel databází SQL**, nebo **Přispěvatel SQL serveru** oprávnění nejsou vyžadována k zobrazení textu dotazu.

## <a name="use-query-performance-insight"></a>Použití nástroje Query Performance Insight

Query Performance Insight se snadno používá:

1. Otevřít [webu Azure portal](https://portal.azure.com/) a najít databázi, kterou chcete prozkoumat.
2. Z nabídky levé straně otevřete **inteligentní výkonu** > **Query Performance Insight**.
  
   ![Query Performance Insight v nabídce](./media/sql-database-query-performance/tile.png)

3. Na první kartě projděte si seznam hlavní dotazy využívání prostředků.
4. Vyberte jednotlivé dotazy zobrazíte její podrobnosti.
5. Otevřít **inteligentní výkonu** > **doporučení k výkonu** a zkontrolujte, jestli jsou k dispozici žádná doporučení k výkonu. Další informace o integrovaných výkonu doporučení najdete v tématu [služby SQL Database Advisor](sql-database-advisor.md).
6. Pomocí jezdců nebo zvětšení ikony zjištěnou interval změnit.

   ![řídicí panel výkon](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Pro službu SQL Database mají vykreslit informace v Query Performance Insight Query Store musí zachytit pár hodin data. Pokud databáze nemá žádné aktivity nebo Query Store nebyl aktivní v určité období, grafy bude prázdný, když Query Performance Insight zobrazí tento časový rozsah. Query Store můžete kdykoli povolit, pokud není spuštěná. Další informace najdete v tématu [osvědčené postupy s Query Store](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store).

## <a name="review-top-cpu-consuming-queries"></a>Zkontrolovat hlavní dotazy využívání procesoru

Ve výchozím nastavení Query Performance Insight ukazuje prvních pěti dotazech využívání procesoru při prvním otevření.

1. Vyberte nebo zrušte zaškrtnutí jednotlivých dotazů, které chcete zahrnout nebo vyloučit z grafu pomocí zaškrtávacích políček.

    Na horní zobrazený řádek zobrazuje celkové procentní hodnoty DTU databáze. Pruhy zobrazují procento využití procesoru, vybrané dotazy spotřebovaná při zvoleném intervalu. Například pokud **poslední týden** je vybrána, každý řádek představuje jeden den.

    ![Nejčastější dotazy](./media/sql-database-query-performance/top-queries.png)

   > [!IMPORTANT]
   > DTU řádku zobrazeném shromažďovány na hodnotu maximální využití v obdobích hodinová. Je určená pro vysoké úrovně porovnání pouze s statistiky provádění dotazu. V některých případech se využití DTU zdát, že příliš vysoká. ve srovnání s spouštěné dotazy, ale to nemusí být případ.
   >
   > Například pokud dotaz maxed si jednotek DTU na 100 % na jenom pár minut, řádku DTU v Query Performance Insight se zobrazí celou hodinu jako 100 % (důsledkem maximální agregovaná hodnota).
   >
   > Lepší porovnání (až minutu) zvažte možnost vytvořit vlastní graf využití DTU:
   >
   > 1. Na webu Azure Portal, vyberte **Azure SQL Database** > **monitorování**.
   > 2. Vyberte **Metriky**.
   > 3. Vyberte **+ přidat graf**.
   > 4. Vyberte DTU procenta v grafu.
   > 5. Kromě toho vyberte **posledních 24 hodin** v nabídce vlevo nahoře a změňte ji na jednu minutu.
   >
   > Pomocí vlastního grafu jednotek DTU na jemnější úrovni podrobností k porovnání s grafem provádění dotazu.

   Mřížka dole ukazuje agregované informace pro viditelné dotazy:

   * ID dotazu, který je jedinečný identifikátor pro dotaz do databáze.
   * Využití procesoru podle dotazu během pozorovatelných intervalu, což závisí na agregační funkce.
   * Doba trvání za dotaz, který také závisí na agregační funkce.
   * Celkový počet spuštění pro konkrétní dotaz.

2. Pokud vaše data zastaralá, vyberte **aktualizovat** tlačítko.

3. Pomocí jezdců a přiblížení tlačítek na hodnotu změnit interval zjišťování a vyšetřování špičky využití:

   ![Posuvníky a přiblížení tlačítka pro změnu intervalu](./media/sql-database-query-performance/zoom.png)

4. Volitelně můžete vybrat **vlastní** kartu pro přizpůsobení zobrazení:

   * Metriky (procesor, doba trvání, počet spuštění).
   * Časový interval (posledních 24 hodin, poslední týden nebo měsíc).
   * Počet dotazů.
   * Agregační funkce.
  
   ![Vlastní karty](./media/sql-database-query-performance/custom-tab.png)
  
5. Vyberte **Přejít >** tlačítko přizpůsobené zobrazení.

   > [!IMPORTANT]
   > Query Performance Insight je omezena na zobrazení náročné dotazy 5-20, v závislosti na výběru na začátek. Databázi můžete spustit mnoho více dotazů nad rámec těch, které jsou zobrazeny největších a tyto dotazy se nezahrnuly do grafu.
   >
   > Pravděpodobně existují úlohy typ databáze, ve kterém velké množství menších dotazů, kromě těch, které jsou zobrazeny, největších častěji a použít většinou DTU. Tyto dotazy nejsou zobrazeny v grafu výkonu.
   >
   > Například dotaz může spotřebovaly vyžadovat značné množství jednotek DTU na dobu i jeho celkovou spotřebu v zjištěnou období je menší než horní využívání dotazy. V takovém případě se nezobrazilo využití prostředků tohoto dotazu v grafu.
   >
   > Pokud je potřeba pochopit, provádění dotazu top nad rámec omezení Query Performance Insight, zvažte použití [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) pro zajištění výkonu databází rozšířené monitorování a řešení potíží.
   >

## <a name="view-individual-query-details"></a>Zobrazení podrobností o jednotlivých dotazech

Chcete-li zobrazit podrobnosti o dotazu:

1. Vyberte libovolný dotaz v seznamu Nejčastější dotazy.

    ![Seznam nejčastější dotazy](./media/sql-database-query-performance/details.png)

   Otevře podrobné zobrazení. Zobrazuje využití procesoru, doba trvání a počet spuštění v čase.

2. Vyberte graf funkce podrobnosti.

   * Horního grafu zobrazí čáry s celkové procento DTU databáze. Pruhy jsou procento využití procesoru, které vybraného dotazu.
   * Druhý graf ukazuje celkové doby trvání vybraného dotazu.
   * V dolní části grafu zobrazuje celkový počet spuštění podle vybraného dotazu.

   ![Podrobnosti dotazu](./media/sql-database-query-performance/query-details.png)

3. Volitelně můžete pomocí posuvníků, použijte tlačítka lupy nebo vyberte **nastavení** přizpůsobit, jak se zobrazí dotaz na data nebo vyberte jiný časový rozsah.

   > [!IMPORTANT]
   > Query Performance Insight nezachytává všechny dotazy jazyka DDL. V některých případech se nemusí zachycovat všechny dotazy ad hoc.
   >

## <a name="review-top-queries-per-duration"></a>Zkontrolovat hlavní dotazy podle doby trvání

Dvě metriky v Query Performance Insight vám může pomoct najít potenciální slabá místa: počet dobu trvání a spuštění.

Dlouho běžící dotazy mohou mít největší potenciálně zamknutí prostředků delší dobu, blokování jiných uživatelů a omezení škálovatelnosti. Jsou to taky nejlepší kandidáty na optimalizaci.

K identifikaci dlouho běžící dotazy:

1. Otevřít **vlastní** kartu v Query Performance Insight pro vybranou databázi.
2. Změnit metriky pro **doba trvání**.
3. Vyberte počet dotazů a intervalu zjišťování.
4. Vyberte funkci agregace:

   * **Součet** sečte všechny doba provádění dotazu pro interval celý pozorování.
   * **Maximální počet** najde dotazuje, v které doba spuštění byla maximální intervalu celý pozorování.
   * **AVG** najde Průměrná doba zpracování všech provedení dotazu, se dozvíte těch největších tyto průměry.

   ![Doba trvání dotazu](./media/sql-database-query-performance/top-duration.png)

5. Vyberte **Přejít >** tlačítko přizpůsobené zobrazení.

   > [!IMPORTANT]
   > Úprava zobrazení dotazu neaktualizuje řádku DTU. Řádek DTU vždycky zobrazí hodnotu maximální využití pro interval.
   >
   > Abyste pochopili spotřebu DTU databáze s více podrobnostmi (až minutu), zvažte vytvoření vlastního grafu na webu Azure Portal:
   >
   > 1. Vyberte **Azure SQL Database** > **monitorování**.
   > 2. Vyberte **Metriky**.
   > 3. Vyberte **+ přidat graf**.
   > 4. Vyberte DTU procenta v grafu.
   > 5. Kromě toho vyberte **posledních 24 hodin** v nabídce vlevo nahoře a změňte ji na jednu minutu.
   >
   > Doporučujeme používat vlastní graf DTU pro porovnání s grafem výkonu dotazů.
   >

## <a name="review-top-queries-per-execution-count"></a>Projděte si nejčastější dotazy na počet spuštění

Uživatelská aplikace, která používá databázi, může být get pomalé, i když vysoký počet spuštění nemusí být by to ovlivnilo samotná databáze a bude malé využití prostředků.

V některých případech se počet vysokou provedení může vést k větší síti, zpomalí. Má zpáteční převod vliv na výkon. Jsou to v souladu s latence sítě a podřízený server latenci.

Například mnoho webů řízených daty silně přístup k databázi pro každý požadavek uživatele. Přestože sdružování připojení pomáhá zvýšení síťových přenosů a zatížení při zpracování na databázový server může snížit výkon. Obecně platí zachovejte má zpáteční převod na minimum.

K identifikaci často spouštěné dotazy (příliš "upovídaným"):

1. Otevřít **vlastní** kartu v Query Performance Insight pro vybranou databázi.
2. Změnit metriky pro **počet spuštění**.
3. Vyberte počet dotazů a intervalu zjišťování.
4. Vyberte **Přejít >** tlačítko přizpůsobené zobrazení.

   ![počet spuštění dotazu](./media/sql-database-query-performance/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>Výkon ladění poznámky

Při průzkumu vašich úloh v Query Performance Insight, můžete si všimnout ikony se zobrazuje svislá čára nad grafem.

Tyto ikony jsou poznámky. Zobrazí se doporučení k výkonu z [služby SQL Database Advisor](sql-database-advisor.md). Podržením ukazatele myši na poznámku, můžete získat souhrnné informace o doporučení k výkonu.

   ![dotaz poznámky](./media/sql-database-query-performance/annotation.png)

Pokud chcete lépe pochopit, nebo použijte doporučení poradce, vyberte ikonu se otevřít podrobnosti o doporučené akce. Pokud se jedná aktivní doporučení, můžete jej použít hned z portálu.

   ![Podrobnosti dotazu poznámky](./media/sql-database-query-performance/annotation-details.png)

V některých případech se z důvodu úroveň zvětšení je možné, že poznámky blízko u sebe, jsou sbaleny do jedné poznámky. Query Performance Insight představuje to jako ikonu poznámky skupiny. Vyberte ikonu poznámky skupiny otevře nové okno se seznamem poznámky.

Korelace dotazů a optimalizace výkonu akcí vám můžou pomoct lépe pochopit vaše úlohy.

## <a name="optimize-the-query-store-configuration-for-query-performance-insight"></a>Optimalizovat konfiguraci Query Store pro Query Performance Insight

Při používání Query Performance Insight, může se zobrazit následující chybové zprávy Query Store:

* "Query Store není správně nakonfigurované v této databázi. Kliknutím sem se dozvíte víc."
* "Query Store není správně nakonfigurované v této databázi. Kliknutím sem můžete změnit nastavení. "

Tyto zprávy se obvykle zobrazují, když Store dotazu nelze shromažďovat nová data.

Prvním případě se stane, když Query Store je ve stavu jen pro čtení a parametry jsou optimálně nastavené. Můžete to vyřešit zvětšením velikosti úložiště dat nebo zrušením zaškrtnutí Query Store. (Pokud zrušíte dotaz Store, všechny dříve shromážděná telemetrická data se ztratí.)

   ![Podrobnosti dotazu Store](./media/sql-database-query-performance/qds-off.png)

Druhý případ se stane, když Store dotazu není povolena, nebo parametry nejsou nastavené optimálně. Můžete změnit zásady uchovávání informací a sběr dat a také povolit Query Store spuštěním následujících příkazů poskytované [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) nebo na webu Azure portal.

### <a name="recommended-retention-and-capture-policy"></a>Doporučené zásady uchovávání informací a zachycení

Existují dva typy zásad uchovávání informací:

* **Velikost na základě**: Pokud tato zásada je nastavená na **AUTOMATICKY**, vyčistí data automaticky, když je dosaženo téměř maximální velikost.
* **Čas na základě**: Ve výchozím nastavení tato zásada nastavená na 30 dnů. Pokud dotaz Store místa, odstraní dotazu informace, které jsou starší než 30 dní.

Zachycení zásady můžete nastavit na:

* **Všechny**: Query Store zachycuje všechny dotazy.
* **Automatické**: Query Store ignoruje málo časté dotazy a dotazy s dobou trvání nevýznamné kompilace a spuštění. Prahové hodnoty pro počet spuštění kompilace doba trvání a doba trvání běhu se určují interně. Toto je výchozí možnost.
* **Žádný**: Query Store zastaví sběr nové dotazy, ale budou stále shromažďovat statistické údaje o již zachycené dotazů.

Doporučujeme nastavit všechny zásady na **AUTOMATICKY** a čisticí zásadu do 30 dnů spuštěním následujících příkazů z [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) nebo na webu Azure portal. (Nahradit `YourDB` s názvem databáze.)

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

Zvětšete velikost Query Store propojíte databázi prostřednictvím [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) nebo na webu Azure portal a spusťte následující dotaz. (Nahradit `YourDB` s názvem databáze.)

```T-SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

Použití těchto nastavení bude nakonec Query Store shromažďovat telemetrii pro nové dotazy. Pokud potřebujete Query Store byla okamžitě funkční, můžete volitelně vymazat Query Store spuštěním následujícího dotazu pomocí aplikace SSMS nebo na webu Azure portal. (Nahradit `YourDB` s názvem databáze.)

> [!NOTE]
> Spuštění následujícího dotazu se odstraní všechny dříve shromážděná monitorovaných telemetrie v Query Store.

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="summary"></a>Souhrn

Query Performance Insight vám pomůže pochopit vliv zatížení dotazy a toho, jak souvisí spotřeba databázových prostředků. Pomocí této funkce získáte informace o využívání horní dotazy na vaši databázi a najdete tu dotazy k optimalizaci předtím, než začnou způsobovat potíže.

## <a name="next-steps"></a>Další postup

* Doporučení k výkonu databáze, vyberte [doporučení](sql-database-advisor.md) v navigačním podokně Query Performance Insight.

    ![Na kartě Recommendations](./media/sql-database-query-performance/ia.png)

* Zvažte povolení [automatické ladění](sql-database-automatic-tuning.md) pro běžné problémy s výkonem databáze.
* Zjistěte, jak [Intelligent Insights](sql-database-intelligent-insights.md) pomáhá automaticky řešit problémy s výkonem databáze.
* Zvažte použití [Azure SQL Analytics]( ../azure-monitor/insights/azure-sql.md) Upřesnit monitorování velkého počtu databáze, elastické fondy a spravované instance SQL s integrovanými inteligentními funkcemi.
