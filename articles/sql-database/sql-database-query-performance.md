---
title: Informace o výkonu dotazování pro službu Azure SQL Database | Dokumentace Microsoftu
description: Sledování výkonu dotazů identifikuje většina využívání procesoru dotazů pro databázi SQL Azure.
services: sql-database
author: danimir
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: v-daljep
ms.reviewer: carlrab
ms.openlocfilehash: 5069b4e69c53ed93e9018cef2517f6125b838d12
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39524161"
---
# <a name="azure-sql-database-query-performance-insight"></a>Azure SQL Database Query Performance Insight
Správa a optimalizace výkonu relačních databází je náročné úlohy vyžadující značné znalosti a investice vkládané čas. Query Performance Insight můžete strávit míň času řešení potíží s výkonem databáze tím, že poskytuje následující:

* Podrobnější přehledy o využití prostředků (DTU) databáze. 
* Nejčastější dotazy podle počtu CPU/doba trvání/spuštění, který lze ladit potenciálně za účelem vylepšení výkonu.
* Možnost Přejít na podrobnosti o dotazu, zobrazit jeho historii využití prostředků a text. 
* Ladění výkonů poznámky, které ukazují akce prováděné [SQL Azure Database Advisor](sql-database-advisor.md)  



## <a name="prerequisites"></a>Požadavky
* Query Performance Insight vyžaduje, aby [Query Store](https://msdn.microsoft.com/library/dn817826.aspx) aktivní ve vaší databázi. Pokud Query Store není spuštěna, portálu vás vyzve k zapnutí.

## <a name="permissions"></a>Oprávnění
Následující [řízení přístupu na základě rolí](../role-based-access-control/overview.md) oprávnění nejsou vyžadována k použití nástroje Query Performance Insight: 

* **Čtečka**, **vlastníka**, **Přispěvatel**, **Přispěvatel databází SQL**, nebo **Přispěvatel SQL serveru** jsou vyžadována oprávnění Chcete-li zobrazit nejčastější využívající dotazy a grafy prostředky. 
* **Vlastník**, **Přispěvatel**, **Přispěvatel databází SQL**, nebo **Přispěvatel SQL serveru** oprávnění nejsou vyžadována k zobrazení textu dotazu.

## <a name="using-query-performance-insight"></a>Pomocí Query Performance Insight
Query Performance Insight se snadno používá:

* Otevřít [webu Azure portal](https://portal.azure.com/) a najít databáze, kterou chcete prozkoumat. 
  * V nabídce na levé straně v části Podpora a řešení potíží vyberte "Query Performance Insight".
* Na první kartě projděte si seznam hlavní dotazy využívání prostředků.
* Vyberte jednotlivé dotazy zobrazíte její podrobnosti.
* Otevřít [SQL Azure Database Advisor](sql-database-advisor.md) a zkontrolujte, jestli jsou k dispozici žádná doporučení.
* Pomocí jezdců nebo zvětšení ikony zjištěnou interval změnit.
  
    ![řídicí panel výkon](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Pár hodin dat musí být zachyceno Query Store pro službu SQL Database dostupné informace o výkonu dotazů. Pokud databáze nemá žádné aktivity nebo Query Store nebyl během určitého časového období aktivní, grafy bude prázdný, při zobrazení tohoto časového období. Query Store může kdykoli povolit, pokud není spuštěná.   
> 
> 

## <a name="review-top-cpu-consuming-queries"></a>Dotazy využívající procesor
V [portál](http://portal.azure.com) postupujte takto:

1. Přejděte do služby SQL database a klikněte na tlačítko **všechna nastavení** > **podpora a řešení potíží** > **přehled o výkonu dotazování**. 
   
    ![Query Performance Insight][1]
   
    Otevře se zobrazení nejčastější dotazy a jsou uvedené hlavní dotazy využívání procesoru.
2. Klikněte na tlačítko kolem grafu podrobnosti.<br>Na horní zobrazený řádek zobrazuje celkové DTU % pro databáze, zatímco pruhy zobrazují využití procesoru % využívaný vybraných dotazů během vybraného intervalu (například pokud **poslední týden** je vybraná každý řádek představuje jeden den).
   
    ![Nejčastější dotazy][2]
   
    Mřížka dolní představuje agregované informace viditelné dotazy.
   
   * ID dotazu – jedinečný identifikátor dotaz do databáze.
   * Využití procesoru podle dotazu během pozorovatelných intervalu (závisí na agregační funkce).
   * Doba trvání jednotlivých dotazů (závisí na agregační funkce).
   * Celkový počet spuštění pro konkrétní dotaz.
     
     Vyberte nebo zrušte zaškrtnutí jednotlivých dotazů pro zahrnutí nebo vyloučení z grafu pomocí zaškrtávacích políček.
3. Pokud zastarávají vaše data, klikněte na tlačítko **aktualizovat** tlačítko.
4. Můžete použít posuvníky a přiblížení tlačítek na hodnotu změnit interval zjišťování a vyšetřování špičky: ![nastavení](./media/sql-database-query-performance/zoom.png)
5. Případně, pokud chcete jiné zobrazení, můžete vybrat **vlastní** kartě a nastavte:
   
   * Metriky (procesor, doba trvání, počet spuštění)
   * Časový interval (posledních 24 hodin, poslední týden poslední měsíc). 
   * Počet dotazů.
   * Agregační funkce.
     
     ![settings](./media/sql-database-query-performance/custom-tab.png)

## <a name="viewing-individual-query-details"></a>Zobrazení podrobností o jednotlivých dotazech
Chcete-li zobrazit podrobnosti o dotazu:

1. Klikněte na libovolný dotaz v seznamu Nejčastější dotazy.
   
    ![Podrobnosti](./media/sql-database-query-performance/details.png)
2. Otevře zobrazení podrobností a počet spotřeby/Doba trvání/spuštění procesoru dotazů je rozdělena v čase.
3. Klikněte na tlačítko kolem grafu podrobnosti.
   
   * Horního grafu zobrazí řádek s celkovou % DTU databáze a pruhy se využití procesoru % využívaný vybraného dotazu.
   * Druhý graf ukazuje celková doba trvání podle vybraného dotazu.
   * Dolní graf zobrazuje celkový počet spuštění podle vybraného dotazu.
     
     ![Podrobnosti dotazu][3]
4. Volitelně můžete pomocí posuvníků, tlačítka lupy nebo klikněte na tlačítko **nastavení** přizpůsobit, jak se zobrazí dotaz na data nebo vyberte jiné časové období.

## <a name="review-top-queries-per-duration"></a>Zkontrolovat hlavní dotazy podle doby trvání
V nejnovější aktualizaci Query Performance Insight, zavedli jsme dvě nové metriky, které mohou pomoci identifikovat potenciální problémová místa: počet dobu trvání a spuštění.<br>

Dlouho běžící dotazy mohou mít největší potenciálně zamknutí prostředků delší dobu, blokování jiných uživatelů a omezení škálovatelnosti. Jsou také nejlepší kandidáty na optimalizaci.<br>

K identifikaci dlouho běžící dotazy:

1. Otevřít **vlastní** kartu pro vybranou databázi v Query Performance Insight
2. Změnit metriky, které se **doba trvání**
3. Vyberte počet dotazů a interval zjišťování
4. Vyberte funkci agregace
   
   * **Součet** Sečte všechna spuštění dotazu během intervalu celý pozorování.
   * **Maximální počet** najde dotazy v celé pozorování intervalu byl maximální dobu provádění.
   * **AVG** najde Průměrná doba provádění všech provedení dotazu a zobrazit horní mimo tyto průměry. 
     
     ![Doba trvání dotazu][4]

## <a name="review-top-queries-per-execution-count"></a>Projděte si nejčastější dotazy na počet spuštění
Vysoký počet spuštění nemusí být by to ovlivnilo samotná databáze a může být využití prostředků nízké, ale celkové aplikace mohou získat pomalé.

V některých případech může způsobit zvyšování spuštění velkého počtu síťových přenosů. Má zpáteční převod výrazně ovlivnit výkon. Jsou v souladu s latence sítě a podřízený server latenci. 

Například mnoho webových stránek s daty silně přístup k databázi pro každý požadavek uživatele. Při připojení sdružování pomáhá zvýšenou síťový provoz a zpracování na databázový server může nepříznivě ovlivnit výkon.  Obecné Rady o výměny zpráv na absolutním minimu.

K identifikaci často spouštěné dotazy dotazy (příliš "upovídaným"):

1. Otevřít **vlastní** kartu pro vybranou databázi v Query Performance Insight
2. Změnit metriky, které se **počet spuštění**
3. Vyberte počet dotazů a interval zjišťování
   
    ![počet spuštění dotazu][5]

## <a name="understanding-performance-tuning-annotations"></a>Principy poznámky k optimalizaci výkonu
Při průzkumu vašich úloh v Query Performance Insight, můžete si všimnout ikony se zobrazuje svislá čára nad grafem.<br>

Tyto ikony jsou poznámky; představují výkonu by to ovlivnilo akce prováděné [SQL Azure Database Advisor](sql-database-advisor.md). Ukázání poznámky získáte základní informace o akci:

![dotaz poznámky][6]

Pokud se chcete dozvědět víc nebo použijte doporučení advisoru, klikněte na ikonu. Otevře se podrobnosti akce. Pokud je aktivní doporučení můžete použít přímo okamžitě pomocí příkazu.

![Podrobnosti dotazu poznámky][7]

### <a name="multiple-annotations"></a>Více poznámek.
Je možné, že z důvodu úroveň přiblížení bude získat poznámky, které jsou blízko u sebe sbaleny do jednoho. To se bude reprezentovat pomocí speciální ikonu, kliknutím otevřete nové okno, kde seznam seskupené poznámky se zobrazí.
Korelace dotazů a akce pro optimalizaci výkonu vám může pomoct lépe pochopit vaše úlohy. 

## <a name="optimizing-the-query-store-configuration-for-query-performance-insight"></a>Optimalizace dotazů Store konfigurace pro Query Performance Insight
Při používání nástroje Query Performance Insight může dojít k následující zprávy Query Store:

* "Query Store není správně nakonfigurované v této databázi. Kliknutím sem se dozvíte víc."
* "Query Store není správně nakonfigurované v této databázi. Kliknutím sem můžete změnit nastavení. " 

Tyto zprávy se obvykle zobrazují při Query Store není možné shromažďovat nová data. 

Prvním případě se stane, když Query Store je ve stavu jen pro čtení a parametry jsou optimálně nastavené. Problém můžete vyřešit zvětšením Query Store nebo zrušením zaškrtnutí Query Store.

![tlačítko qds][8]

Druhý případ se stane, když Query Store je vypnutý nebo parametry nejsou nastavené optimálně. <br>Můžete změnit zásady uchovávání informací a zachycení a povolit Query Store spuštěním následujících příkazů nebo přímo z portálu:

![tlačítko qds][9]

### <a name="recommended-retention-and-capture-policy"></a>Doporučené zásady uchovávání informací a zachycení
Existují dva typy zásad uchovávání informací:

* Velikost na základě - li nastavena na AUTO ho se vyčistit data automaticky, když je dosaženo téměř maximální velikost.
* Časovou – ve výchozím nastavení jsme bude nastavený na 30 dní, což znamená, pokud Query Store se vyčerpala volné místo, odstraní dotazu informace, které jsou starší než 30 dní.

Zachycení zásad může být nastaven na:

* **Všechny** -všechny dotazy zachytí.
* **Automatické** – málo časté dotazy a dotazy s dobou trvání nevýznamné kompilace a spuštění se ignorují. Interně se určují prahové hodnoty pro dobu trvání spuštění počet, kompilace a modulu runtime. Toto je výchozí možnost.
* **Žádný** -Query Store zastaví sběr nové dotazy, ale budou stále shromažďovat statistiky běhu již zachycené dotazů.

Doporučujeme nastavit všechny zásady na AUTOMATICKÝ a vyčistit zásad na 30 dnů:

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

Zvětšete velikost Query Store. To lze provést tak, že připojení k databázi a vydávání následující dotaz:

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

Použití těchto nastavení bude nakonec Query Store shromažďování nové dotazy, ale pokud nechcete čekat, můžete vymazat Query Store. 

> [!NOTE]
> Provádění následujícího dotazu se odstraní všechny aktuální informace v Query Store. 
> 
> 

    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## <a name="summary"></a>Souhrn
Query Performance Insight vám pomůže pochopit vliv zatížení dotazy a toho, jak souvisí spotřeba databázových prostředků. Pomocí této funkce přečtěte si o hlavních dotazy využívající a snadno identifikovat ty vyřešit předtím, než začnou způsobovat potíže.

## <a name="next-steps"></a>Další postup
Další doporučení týkající se vylepšení výkonu SQL database, klikněte na tlačítko [doporučení](sql-database-advisor.md) na **Query Performance Insight** okno.

![Poradce pro výkon](./media/sql-database-query-performance/ia.png)

<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png
[4]: ./media/sql-database-query-performance/top-duration.png
[5]: ./media/sql-database-query-performance/top-execution.png
[6]: ./media/sql-database-query-performance/annotation.png
[7]: ./media/sql-database-query-performance/annotation-details.png
[8]: ./media/sql-database-query-performance/qds-off.png
[9]: ./media/sql-database-query-performance/qds-button.png

