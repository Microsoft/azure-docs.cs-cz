---
title: Query Performance Insight
description: Dotaz na sledování výkonu identifikuje nejvíce náročné a dlouhotrvající dotazy pro databáze s jednou a ve fondu v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 03/10/2020
ms.openlocfilehash: a016781e5b05c8f43c043bf1f3368a58064e43ad
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501256"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Query Performance Insight pro Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Query Performance Insight nabízí inteligentní analýzu dotazů pro izolované databáze i databáze ve fondu. Pomáhá v úlohách identifikovat dotazy s nejvyšším využitím prostředků a nejdéle trvající dotazy. To vám pomůže najít dotazy, které byste měli optimalizovat, abyste zlepšili celkový výkon úloh a efektivně využívali prostředky, za které platíte. Query Performance Insight pomáhá věnovat méně času řešení potíží s výkonem databáze tím, že poskytuje:

* Hlubší přehled o spotřebě prostředků databází (DTU)
* Podrobnosti o hlavních databázových dotazech podle procesoru, doby trvání a počtu spuštění (potenciální kandidáti na optimalizaci výkonu pro zlepšení výkonu)
* Možnost přechodu k podrobnostem o dotazu, zobrazení textu dotazu a historie využití prostředků
* Poznámky, které ukazují doporučení týkající se výkonu z [databázových poradců](database-advisor-implement-performance-recommendations.md)

![Query Performance Insight](./media/query-performance-insight-use/opening-title.png)

## <a name="prerequisites"></a>Předpoklady

Query Performance Insight vyžaduje, aby [úložiště dotazů](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) bylo ve vaší databázi aktivní. Ve výchozím nastavení se automaticky povolí pro všechny databáze ve Azure SQL Database. Pokud není úložiště dotazů spuštěné, Azure Portal vás vyzve, abyste ho povolili.

> [!NOTE]
> Pokud se v této databázi na portálu zobrazí zpráva "úložiště dotazů není správně nakonfigurované", přečtěte si téma [Optimalizace konfigurace úložiště dotazů](#optimize-the-query-store-configuration).

## <a name="permissions"></a>Oprávnění

Pro použití Query Performance Insight potřebujete následující oprávnění [řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md) :

* Aby bylo možné zobrazit nejlepší dotazy a grafy náročné na prostředky, je nutné, aby byl **Čtenář**, **vlastník**, **Přispěvatel**, **Přispěvatel databáze SQL** nebo **SQL Server oprávnění přispěvatele** .
* K zobrazení textu dotazu jsou nutná oprávnění **vlastník**, **Přispěvatel**, **Přispěvatel databáze SQL** nebo **SQL Server oprávnění přispěvatele** .

## <a name="use-query-performance-insight"></a>Použití nástroje Query Performance Insight

Query Performance Insight lze snadno použít:

1. Otevřete [Azure Portal](https://portal.azure.com/) a vyhledejte databázi, kterou chcete prošetřit.
2. V nabídce na levé straně otevřete **inteligentní**  >  **Query Performance Insight** výkonu.
  
   ![Query Performance Insight v nabídce](./media/query-performance-insight-use/tile.png)

3. Na první kartě si Projděte seznam nejčastějších dotazů, které jsou náročné na prostředky.
4. Vyberte jednotlivý dotaz, ve kterém chcete zobrazit jeho podrobnosti.
5. Otevřete **inteligentní**  >  **doporučení** výkonu výkonu a ověřte, jestli jsou k dispozici nějaká doporučení pro výkon. Další informace o předdefinovaných doporučeních výkonu najdete v tématu [Azure SQL Database Advisor](database-advisor-implement-performance-recommendations.md).
6. Pro změnu pozorovaného intervalu použijte posuvníky nebo ikony lupy.

   ![Řídicí panel výkonu](./media/query-performance-insight-use/performance.png)

> [!NOTE]
> Aby Azure SQL Database vykreslila informace v Query Performance Insight, úložiště dotazů musí zachytit několik hodin dat. Pokud databáze nemá žádnou aktivitu nebo pokud nebylo úložiště dotazů v určitém období aktivní, grafy budou prázdné, pokud Query Performance Insight zobrazí tento časový rozsah. Úložiště dotazů můžete kdykoli povolit, pokud není spuštěno. Další informace najdete v tématu [osvědčené postupy s úložištěm dotazů](/sql/relational-databases/performance/best-practice-with-the-query-store).
>

V případě doporučení pro výkon databáze vyberte v okně Query Performance Insight navigační okno [doporučení](database-advisor-implement-performance-recommendations.md) .

![Karta doporučení](./media/query-performance-insight-use/ia.png)

## <a name="review-top-cpu-consuming-queries"></a>Přečtěte si nejčastější dotazy náročné na procesor

Ve výchozím nastavení Query Performance Insight zobrazuje pět nejlepších dotazů využívajících procesor při prvním otevření.

1. Zaškrtněte nebo zrušte zaškrtnutí jednotlivých dotazů, které chcete zahrnout nebo vyloučit z grafu pomocí zaškrtávacích políček.

   Horní řádek ukazuje celkové procento DTU pro databázi. Na pruzích se zobrazí procento využití procesoru, které vybrané dotazy spotřebují během zvoleného intervalu. Například pokud je vybrán **minulý týden** , každý řádek představuje jeden den.

   ![Nejčastější dotazy](./media/query-performance-insight-use/top-queries.png)

   > [!IMPORTANT]
   > Zobrazená čára DTU je agregovaná na maximální hodnotu spotřeby v jedné hodinové periodě. Je určen pro porovnání na vysoké úrovni pouze s statistikami spouštění dotazů. V některých případech se využití DTU může zdát příliš vysoké ve srovnání s provedenými dotazy, ale nemusí to být případ.
   >
   > Pokud například dotaz vyčerpáním z DTU na 100% jenom na několik minut, zobrazí se na řádku DTU v Query Performance Insight celá hodina spotřeby jako 100% (důsledek maximální agregované hodnoty).
   >
   > Pro přesnější porovnání (až jednu minutu) zvažte vytvoření vlastního grafu využití DTU:
   >
   > 1. V Azure Portal vyberte **Azure SQL Database**  >  **monitorování**.
   > 2. Vyberte **Metriky**.
   > 3. Vyberte **+ přidat graf**.
   > 4. Vyberte procento DTU v grafu.
   > 5. Kromě toho vyberte v levé horní nabídce **Poslední 24 hodiny** a změňte ji na jednu minutu.
   >
   > Pomocí vlastního grafu DTU s jemnější úrovní podrobností můžete porovnat s grafem spuštění dotazu.

   Dolní Mřížka zobrazuje agregované informace pro viditelné dotazy:

   * ID dotazu, což je jedinečný identifikátor dotazu v databázi.
   * PROCESOR na dotaz během pozorovatelního intervalu, který závisí na agregační funkci.
   * Doba trvání na dotaz, která také závisí na agregační funkci.
   * Celkový počet spuštění pro určitý dotaz.

2. Pokud se data zastará, vyberte tlačítko **aktualizovat** .

3. Pomocí posuvníků a tlačítek lupy můžete změnit interval sledování a prozkoumat špičky spotřeby:

   ![Posuvníky a tlačítka lupy pro změnu intervalu](./media/query-performance-insight-use/zoom.png)

4. Volitelně můžete vybrat **vlastní** kartu pro přizpůsobení zobrazení:

   * Metrika (CPU, doba trvání, počet spuštění).
   * Časový interval (posledních 24 hodin, minulý týden nebo minulý měsíc).
   * Počet dotazů.
   * Agregační funkce
  
   ![Vlastní karta](./media/query-performance-insight-use/custom-tab.png)
  
5. Kliknutím na tlačítko **přejít >** zobrazíte přizpůsobené zobrazení.

   > [!IMPORTANT]
   > Query Performance Insight je omezené na zobrazení prvních 5-20 náročných dotazů v závislosti na vašem výběru. Vaše databáze může spustit mnoho dalších dotazů nad rámec zobrazených a tyto dotazy nebudou do grafu zahrnuty.
   >
   > Je možné, že existuje typ databázové úlohy, ve kterém se nachází spousta menších dotazů, a to nad rámec těch, které jsou zobrazené, spouštějte často a používejte většinu DTU. Tyto dotazy se nezobrazují v grafu výkonu.
   >
   > Dotaz může například spotřebovat značnou jednotku DTU za chvíli, i když je celková spotřeba v pozorovaném období menší než ostatní dotazy, které jsou v nejvyšší době k dispozici. V takovém případě se využití prostředků tohoto dotazu nezobrazí v grafu.
   >
   > Pokud potřebujete pochopit hlavní provádění dotazů nad rámec omezení Query Performance Insight, zvažte použití [Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md) pro pokročilé monitorování výkonu databáze a řešení potíží.
   >

## <a name="view-individual-query-details"></a>Zobrazit podrobnosti o jednotlivých dotazech

Zobrazení podrobností dotazu:

1. V seznamu nejčastějších dotazů vyberte libovolný dotaz.

    ![Seznam nejčastějších dotazů](./media/query-performance-insight-use/details.png)

   Otevře se podrobné zobrazení. Zobrazuje spotřebu procesoru, dobu trvání a počet spuštění v průběhu času.

2. Pro podrobnosti vyberte funkce grafu.

   * V horním grafu je zobrazen řádek s celkovým procentem DTU databáze. Pruhy představují procento využití procesoru, které vybraný dotaz spotřeboval.
   * Druhý graf znázorňuje celkovou dobu trvání vybraného dotazu.
   * Dolní graf znázorňuje celkový počet spuštění zvoleným dotazem.

   ![Podrobnosti dotazu](./media/query-performance-insight-use/query-details.png)

3. Volitelně můžete použít posuvníky, použít tlačítka zvětšení nebo vybrat **Nastavení** , abyste mohli přizpůsobit způsob zobrazení dat dotazu, nebo vybrat jiný časový rozsah.

   > [!IMPORTANT]
   > Query Performance Insight nezachycují žádné dotazy DDL. V některých případech nemusí zachytit všechny dotazy ad hoc.
   >

## <a name="review-top-queries-per-duration"></a>Zobrazit nejčastější dotazy na dobu trvání

Dvě metriky v Query Performance Insight vám pomůžou najít potenciální problémová místa: trvání a počet spuštění.

Dlouhotrvající dotazy mají největší potenciál na uzamykání prostředků déle, blokují jiné uživatele a omezují škálovatelnost. Jsou to také nejlepší kandidáty na optimalizaci.

Identifikace dlouhotrvajících dotazů:

1. Otevřete **vlastní** kartu v Query Performance Insight pro vybranou databázi.
2. Změňte metriky na **Trvání**.
3. Vyberte počet dotazů a interval sledování.
4. Vyberte agregační funkci:

   * **Sum** sečte veškerou dobu provádění dotazu pro celý interval pozorování.
   * **Maximální počet** vyhledá dotazy, ve kterých bylo doba spuštění pro celý interval pozorování maximum.
   * **Prům** vyhledá průměrnou dobu provádění všech provedení dotazů a v těchto průměrech zobrazuje nejvyšší hodnoty.

   ![Doba trvání dotazu](./media/query-performance-insight-use/top-duration.png)

5. Kliknutím na tlačítko **přejít >** zobrazíte přizpůsobené zobrazení.

   > [!IMPORTANT]
   > Úprava zobrazení dotazu neaktualizuje čáru DTU. Řádek DTU vždy zobrazuje maximální hodnotu spotřeby pro daný interval.
   >
   > Pokud chcete pochopit využití DTU databáze s více podrobnostmi (až 1 minutu), zvažte vytvoření vlastního grafu v Azure Portal:
   >
   > 1. Vyberte **Azure SQL Database**  >  **monitorování**.
   > 2. Vyberte **Metriky**.
   > 3. Vyberte **+ přidat graf**.
   > 4. Vyberte procento DTU v grafu.
   > 5. Kromě toho vyberte v levé horní nabídce **Poslední 24 hodiny** a změňte ji na jednu minutu.
   >
   > Pro porovnání s grafem Performance Query doporučujeme použít vlastní graf DTU.
   >

## <a name="review-top-queries-per-execution-count"></a>Zobrazit nejčastější dotazy na počet spuštění

Uživatelská aplikace, která používá databázi, může být pomalá, i když vysoký počet spuštění nemusí ovlivnit vlastní databázi a využití prostředků je nízké.

V některých případech může vysoký počet spuštění vést k většímu počtu zpátečních cyklů sítě. Výměna cest má vliv na výkon. Vztahují se na latenci sítě a latenci podřízeného serveru.

Mnoho webů založených na datech má například vysoce přístup k databázi pro každý požadavek uživatele. I když sdružování připojení pomáhá, zvýšené zatížení sítě a zatížení na serveru může zpomalit výkon. Obecně platí, že je potřeba se na minimum zacyklovat.

Identifikujte často spouštěné dotazy ("konverzace"):

1. Otevřete **vlastní** kartu v Query Performance Insight pro vybranou databázi.
2. Změňte metriky na **počet spuštění**.
3. Vyberte počet dotazů a interval sledování.
4. Kliknutím na tlačítko **přejít >** zobrazíte přizpůsobené zobrazení.

   ![Počet spuštění dotazu](./media/query-performance-insight-use/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>Vysvětlení poznámek k ladění výkonu

Při prozkoumávání úloh v Query Performance Insight můžete všimnout ikon se svislou čárou nad grafem.

Tyto ikony jsou poznámky. Ukazují doporučení týkající se výkonu z [Azure SQL Database Advisor](database-advisor-implement-performance-recommendations.md). Najetím myší na poznámku můžete získat souhrnné informace o doporučeních pro výkon.

   ![Poznámka k dotazu](./media/query-performance-insight-use/annotation.png)

Pokud chcete pochopit více nebo použít doporučení poradce, vyberte ikonu pro otevření podrobností o doporučené akci. Pokud se jedná o aktivní doporučení, můžete ho použít hned z portálu.

   ![Podrobnosti poznámky k dotazu](./media/query-performance-insight-use/annotation-details.png)

V některých případech je vzhledem k úrovni přiblížení možné, že poznámky blízko sebe jsou sbaleny do jediné poznámky. Query Performance Insight představuje ikonu poznámky skupiny. Když vyberete ikonu poznámky skupiny, otevře se nové okno se seznamem poznámek.

Korelace dotazů a akcí ladění výkonu vám můžou usnadnit lepší pochopení vašich úloh.

## <a name="optimize-the-query-store-configuration"></a>Optimalizovat konfiguraci úložiště dotazů

Při použití Query Performance Insight se může zobrazit následující chybová zpráva v úložišti dotazů:

* "Úložiště dotazů není v této databázi správně nakonfigurováno. Pokud se chcete dozvědět víc, klikněte sem.
* "Úložiště dotazů není v této databázi správně nakonfigurováno. Chcete-li změnit nastavení, klikněte sem. "

Tyto zprávy se obvykle zobrazují, když úložiště dotazů nemůže shromažďovat nová data.

První případ se stane, když je úložiště dotazů ve stavu jen pro čtení a nastavení parametrů je optimální. Můžete to opravit zvětšením velikosti úložiště dat nebo vymazáním úložiště dotazů. (Pokud smažete úložiště dotazů, všechna dřív shromážděná telemetrie budou ztracena.)

   ![Podrobnosti úložiště dotazů](./media/query-performance-insight-use/qds-off.png)

Druhý případ nastane, pokud není úložiště dotazů povolené, nebo nejsou parametry nastavené optimálně. Můžete změnit zásady uchovávání a zachytávání a také povolit úložiště dotazů spuštěním následujících příkazů poskytovaných z [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) nebo Azure Portal.

### <a name="recommended-retention-and-capture-policy"></a>Doporučené zásady uchovávání a zachytávání

Existují dva typy zásad uchovávání informací:

* **Na základě velikosti**: Pokud je tato zásada nastavená na hodnotu **automaticky**, vyčistí data automaticky při dosažení maximální velikosti.
* **Čas na základě**: ve výchozím nastavení je tato zásada nastavená na 30 dní. Pokud dojde místo na úložišti dotazů, odstraní se informace o dotazu starší než 30 dní.

Zásady zachytávání můžete nastavit na:

* **Vše**: úložiště dotazů zachycuje všechny dotazy.
* **Automatické**: úložiště dotazů ignoruje nečasté dotazy a dotazy s nevýznamnou kompilací a dobou trvání spuštění. Prahové hodnoty pro počet spuštění, dobu trvání kompilace a dobu běhu jsou interně určeny. Toto je výchozí možnost.
* **Žádné**: úložiště dotazů zastaví zachytávání nových dotazů, ale statistiky za běhu pro už zachycené dotazy se shromažďují stále.

Doporučujeme nastavit všechny zásady na **Automatické** a zásady čištění na 30 dní, a to spuštěním následujících příkazů z [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) nebo Azure Portal. (Nahraďte `YourDB` názvem databáze.)

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

Zvyšte velikost úložiště dotazů připojením k databázi prostřednictvím [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) nebo Azure Portal a spuštěním následujícího dotazu. (Nahraďte `YourDB` názvem databáze.)

```SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

Použití těchto nastavení způsobí, že úložiště dotazů shromáždí telemetrii pro nové dotazy. Pokud potřebujete, aby úložiště dotazů bylo hned funkční, můžete volitelně zvolit vymazání úložiště dotazů spuštěním následujícího dotazu prostřednictvím SSMS nebo Azure Portal. (Nahraďte `YourDB` názvem databáze.)

> [!NOTE]
> Spuštěním následujícího dotazu se odstraní všechna dříve shromážděná telemetrie v úložišti dotazů.

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="next-steps"></a>Další kroky

Zvažte použití [Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md) pro pokročilé monitorování výkonu velkého loďstva databází s jednou a fondem, elastických fondů, spravovaných instancí a databází instancí.