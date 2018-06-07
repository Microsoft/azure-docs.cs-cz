---
title: Přehled o výkonu dotazu pro Azure SQL Database | Microsoft Docs
description: Monitorování výkonu dotazu identifikuje většinu využívání procesoru dotazů pro databázi SQL Azure.
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: f608beb0834b1c838b082e92340ebf9b650d8b3f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648730"
---
# <a name="azure-sql-database-query-performance-insight"></a>Informace o výkonu dotazů databáze Azure SQL
Správa a ladění výkonu relačních databází je náročné úlohu, která vyžaduje značné znalosti a investice čas. Informace o výkonu dotazů umožňuje trávit méně času řešení potíží s výkonem databáze tím, že poskytuje následující:

* Podrobnější přehled o vaší spotřeby prostředků (DTU) databází. 
* Nejčastějších dotazů podle počtu procesorů a doba trvání nebo provádění, který lze ladit potenciálně pro zlepšení výkonu.
* Možnost k podrobnostem na podrobné informace o dotazu, zobrazit jeho historie využití prostředků a text. 
* Poznámky, které se zobrazí akce prováděné při ladění výkonu [Advisor databáze SQL Azure](sql-database-advisor.md)  



## <a name="prerequisites"></a>Požadavky
* Query Performance Insight vyžaduje, aby [úložiště dotazů](https://msdn.microsoft.com/library/dn817826.aspx) je aktivní databáze. Pokud není spuštěné úložiště dotazů, vyzve k portálu můžete zapnout.

## <a name="permissions"></a>Oprávnění
Následující [řízení přístupu na základě role](../role-based-access-control/overview.md) používat informace o výkonu dotazů jsou potřeba oprávnění: 

* **Čtečka**, **vlastníka**, **Přispěvatel**, **Přispěvatel databází SQL**, nebo **Přispěvatel serveru SQL** jsou požadována oprávnění Chcete-li zobrazit nejvyšší prostředků využívání dotazy a grafy. 
* **Vlastník**, **Přispěvatel**, **Přispěvatel databází SQL**, nebo **SQL serveru Přispěvatel** oprávnění jsou vyžadována k zobrazení text dotazu.

## <a name="using-query-performance-insight"></a>Pomocí Query Performance Insight
Informace o výkonu dotazů se snadno používá:

* Otevřete [portál Azure](https://portal.azure.com/) a najít databázi, které chcete prověřit. 
  * Z nabídky na levé straně, v rámci podpory a řešení potíží vyberte "Query Performance Insight".
* Na první kartě projděte si seznam nejčastějších dotazů využívání prostředků.
* Vyberte jednotlivé dotazy zobrazíte její podrobnosti.
* Otevřete [Poradce pro funkci SQL Azure Database](sql-database-advisor.md) a zkontrolujte, zda jsou k dispozici žádná doporučení.
* Pomocí posuvníků nebo zvětšení ikon do zjištěnou interval změnit.
  
    ![řídicí panel výkonu](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Několik hodin dat musí být zachyceny úložiště dotazů pro databázi SQL zajistit přehled o výkonu dotazu. Pokud databáze nemá žádnou aktivitu nebo úložiště dotazů nebyl aktivní během určitého časového období, grafy bude prázdný, při zobrazení toto časové období. Pokud není spuštěna, může kdykoli povolit úložiště dotazů.   
> 
> 

## <a name="review-top-cpu-consuming-queries"></a>Zkontrolujte nejvíce využívají dotazy procesor
V [portál](http://portal.azure.com) postupujte takto:

1. Přejděte do databáze SQL a klikněte na tlačítko **všechna nastavení** > **podporu + Poradce při potížích s** > **dotaz na informace o výkonu**. 
   
    ![Query Performance Insight][1]
   
    Otevře zobrazení nejčastějších dotazů a jsou uvedeny nejčastějších dotazů využívání procesoru.
2. Klikněte na kolem grafu podrobnosti.<br>Na začátek řádku zobrazuje celkový počet jednotek DTU % pro databázi, zatímco pruhy zobrazit využití procesoru % spotřebovávají vybrané dotazy během vybrané intervalu (například pokud **za minulý týden** je vybrána každý řádek představuje jeden den).
   
    ![nejčastějších dotazů][2]
   
    Mřížky dolní představuje souhrnné informace pro viditelné dotazy.
   
   * ID dotazu – jedinečný identifikátor dotazu v databázi.
   * Procesor na dotaz během pozorovatelné intervalu (záleží na agregační funkce).
   * Doba trvání za dotazu (záleží na agregační funkce).
   * Celkový počet spuštěních pro konkrétní dotaz.
     
     Vyberte nebo zrušte jednotlivé dotazy zahrnout nebo vyloučit z grafu pomocí zaškrtávacích políček.
3. Pokud vaše data zastaralá, klikněte na tlačítko **aktualizovat** tlačítko.
4. Pomocí posuvníků a přiblížení tlačítka změnit interval pozorování a prozkoumat špičky: ![nastavení](./media/sql-database-query-performance/zoom.png)
5. Případně, pokud chcete jiné zobrazení, můžete vybrat **vlastní** kartě a nastavte:
   
   * Metrika (procesor, doba trvání, počet provedení)
   * Časový interval (poslední týden minulý měsíc posledních 24 hodin). 
   * Počet dotazů.
   * Agregační funkce.
     
     ![settings](./media/sql-database-query-performance/custom-tab.png)

## <a name="viewing-individual-query-details"></a>Zobrazení podrobností o jednotlivých dotazů
Chcete-li zobrazit podrobnosti o dotazu:

1. Klikněte na jakýkoli dotaz ve seznam nejčastějších dotazů.
   
    ![Podrobnosti](./media/sql-database-query-performance/details.png)
2. Otevře zobrazení podrobností a počet procesorů energie a doba trvání nebo provádění dotazů je rozdělena v čase.
3. Klikněte na kolem grafu podrobnosti.
   
   * Horní graf znázorňuje řádek s celkovou % DTU databáze a pruhy jsou využívány službou vybraný dotaz % využití procesoru.
   * Druhý graf zobrazuje celkovou dobu trvání podle vybraný dotaz.
   * Dolní graf zobrazuje celkový počet spuštěních podle vybraný dotaz.
     
     ![Podrobnosti o dotazu][3]
4. V případě potřeby pomocí posuvníků, přiblíží tlačítka nebo klikněte na tlačítko **nastavení** přizpůsobit zobrazení dat dotazu, nebo vyberte jiné časové období.

## <a name="review-top-queries-per-duration"></a>Zkontrolujte nejčastějších dotazů za doba trvání
V poslední aktualizaci Query Performance Insight, zavedli jsme dvě nové metriky, které pomáhají identifikovat potenciální problémová místa: doba trvání a provádění count.<br>

Dlouho běžící dotazy mít největší potenciálně pro uzamčení prostředky delší blokování jiných uživatelů a omezení škálovatelnosti. Jsou také nejlepší kandidáty pro optimalizaci.<br>

K identifikaci dlouho běžící dotazy:

1. Otevřete **vlastní** kartě v Query Performance Insight pro vybrané databáze
2. Změnit metriky být **doba trvání**
3. Vyberte počet dotazů a pozorovacím intervalu.
4. Vyberte funkci agregace
   
   * **Součet** sečte všechny doba provádění dotazu během celého pozorovacím intervalu.
   * **Maximální počet** vyhledá dotazy byl v celé pozorovacím intervalu maximální dobu spuštění.
   * **Průměr** najde průměrný čas provádění všech spuštění dotazu a zobrazit je nejlepší mimo tyto průměry. 
     
     ![Doba trvání dotazu][4]

## <a name="review-top-queries-per-execution-count"></a>Zkontrolujte nejčastějších dotazů na počet provedení
Vysoký počet spuštěních nemusí ovlivňovat samotná databáze a může být využití prostředků nízké, ale může získat celkový aplikace pomalé.

V některých případech počet velmi vysoká provedení může vést ke zvýšení z síťových přenosů. Odezev výrazně ovlivnit výkon. Jsou předmětem latence sítě a podřízený server latence. 

Například mnoho řízené daty weby výraznou přístup k databázi pro každý požadavek uživatele. Při připojení sdružování pomůže, zvýšená síťový provoz a zpracování na serveru databáze může nepříznivě ovlivnit výkon.  Obecné Rady, jak se má zachovat odezev na absolutním minimu.

Chcete-li identifikovat často spustit dotazy dotazy ("chatty"):

1. Otevřete **vlastní** kartě v Query Performance Insight pro vybrané databáze
2. Změnit metriky být **počet provedení**
3. Vyberte počet dotazů a pozorovacím intervalu.
   
    ![počet provedení dotazu][5]

## <a name="understanding-performance-tuning-annotations"></a>Principy poznámky vyladění výkonu
Při prohlížení zatížení Query Performance Insight, můžete si všimnout ikony s svislá čára nad grafu.<br>

Tyto ikony jsou poznámky; představují výkonu by to ovlivnilo provedené akce [Poradce pro funkci SQL Azure Database](sql-database-advisor.md). Ve výběru ukázáním poznámky získáte základní informace o akci:

![dotaz – Poznámka][6]

Pokud chcete další informace nebo použít doporučení služby advisor, klikněte na ikonu. Otevře se podrobnosti akce. Pokud je aktivní doporučení můžete provést okamžitou pomocí příkazu.

![Podrobnosti o dotazu – Poznámka][7]

### <a name="multiple-annotations"></a>Více poznámek.
Je možné, že kvůli úroveň přiblížení, bude získat poznámky, které mezi sebou blíží sbalené do jednoho. To bude reprezentovat speciální ikonou, kliknutím ji bude otevřete nové okno, kde seznam seskupené poznámky se zobrazí.
Korelace dotazy a ladění akce výkonu vám může pomoct lépe pochopit, vaše úlohy. 

## <a name="optimizing-the-query-store-configuration-for-query-performance-insight"></a>Optimalizace konfigurace úložiště dotazů pro Query Performance Insight
Během používání Query Performance Insight může dojít k následující zprávy úložiště dotazů:

* "Úložiště dotazů není správně nakonfigurována v této databázi. Kliknutím sem získáte další informace."
* "Úložiště dotazů není správně nakonfigurována v této databázi. Kliknutím zde proveďte změnu nastavení." 

Tyto zprávy se obvykle zobrazují, když úložiště dotazů není moct shromažďovat nová data. 

Prvním případě se stane, když úložiště dotazů je ve stavu jen pro čtení a parametry nejsou nastavené optimálně. Můžete opravit zvýšit velikost úložiště dotazů nebo vymazání úložiště dotazů.

![tlačítko qds][8]

Druhém případě se stane, když úložiště dotazů je vypnutý nebo parametry nejsou nastavené optimálně. <br>Můžete změnit zásady uchovávání informací a zachycení a povolit úložiště dotazů spuštěním níže uvedené příkazy nebo přímo z portálu:

![tlačítko qds][9]

### <a name="recommended-retention-and-capture-policy"></a>Doporučené zásady uchovávání informací a zachycení
Existují dva typy zásad uchovávání informací:

* Velikost na základě - li nastavit na hodnotu AUTO ho bude vyčistit data automaticky, když je dosaženo blíží maximální velikost.
* Na základě času – ve výchozím nastavení, které jsme bude nastavena na 30 dní, což znamená, pokud úložiště dotazů se dostatek místa, odstraní se dotazu informace, které jsou starší než 30 dní

Zaznamenat zásad může být nastaven na:

* **Všechny** -zaznamená všechny dotazy.
* **Automatické** -málo časté dotazy a dotazy s zanedbatelný kompilace a provádění trvání jsou ignorovány. Interně jsou určit prahové hodnoty pro počet, kompilace a runtime dobu provádění. Toto je výchozí možnost.
* **Žádný** -úložiště dotazů zastaví sběr nové dotazy, ale jsou stále shromážděných statistik modulu runtime pro již zaznamenané dotazy.

Doporučujeme nastavení všech zásad AUTOMATICKY a vyčištění zásad do 30 dnů:

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

Zvětšete velikost úložiště dotazů. To můžete provést připojení k databázi a vydání následující dotaz:

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

Použití těchto nastavení budou nakonec úložiště dotazů shromažďování nové dotazy, ale pokud nechcete čekat můžete vymazat úložiště dotazů. 

> [!NOTE]
> Provádění následující dotaz odstraní všechny aktuální informace v úložišti dotazů. 
> 
> 

    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## <a name="summary"></a>Souhrn
Informace o výkonu dotazů vám pomůže pochopit dopad dotazu úlohy a jak se týká spotřeby prostředků databáze. Pomocí této funkce se další informace o nejnáročnější dotazy a snadno identifikovat ty opravit dřív, než narostou problém.

## <a name="next-steps"></a>Další postup
Další doporučení týkající se vylepšení výkonu SQL databáze, klikněte na tlačítko [doporučení](sql-database-advisor.md) na **Query Performance Insight** okno.

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

