---
title: Streamování dat pomocí integrace Stream Analytics (preview)
description: Azure Stream Analytics slouží k streamování dat do databáze Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ajetasin
ms.author: ajetasi
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: d233d3c98cc495e4b9e84142781f5eb9faa7eec8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820845"
---
# <a name="stream-data-by-using-azure-sql-database-stream-analytics-integration-preview"></a>Streamování dat pomocí integrace Azure SQL Database Stream Analytics (preview)

Uživatelé teď můžou ingestovat, zpracovávat, zobrazovat a analyzovat streamovaná data v reálném čase do tabulky přímo z databáze SQL na webu Azure Portal pomocí [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md). Tato zkušenost umožňuje širokou škálu scénářů, jako je připojené auto, vzdálené monitorování, odhalování podvodů a mnoho dalších. Na webu Azure Portal můžete vybrat zdroj událostí (Event Hub/IoT Hub), zobrazit příchozí události v reálném čase a vybrat tabulku pro ukládání událostí. Můžete také napsat dotazy Stream Analytics dotazovací ho jazyka na portálu transformovat příchozí události a uložit je do vybrané tabulky. Tento nový vstupní bod je navíc k vytváření a konfiguraci prostředí, které již existují v Stream Analytics. Toto prostředí začíná z kontextu vaší databáze, což vám umožní rychle nastavit úlohu Stream Analytics a bezproblémově procházet mezi azure SQL database a stream analytics prostředí.

![Tok Stream Analytics](media/sql-database-stream-analytics/stream-analytics-flow.png)

## <a name="key-benefits"></a>Klíčové výhody

- Minimální přepínání kontextu: Můžete začít z databáze SQL na portálu a začít ingestovat data v reálném čase do tabulky bez přepnutí na jinou službu. 
- Snížený počet kroků: Kontext databáze a tabulky se používá k předběžné konfiguraci úlohy Stream Analytics.
- Další snadné použití s daty náhledu: Zobrazení náhledu příchozích dat ze zdroje událostí (Event Hub/IoT Hub) v kontextu vybrané tabulky 


## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto článku budete potřebovat následující:

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/). 
- Databáze SQL. Podrobnosti najdete [v tématu Vytvoření jedné databáze v Azure SQL Database](sql-database-single-database-get-started.md).
- Pravidlo brány firewall, které umožňuje počítači připojit se k serveru Azure SQL. Podrobnosti naleznete [v tématu Vytvoření pravidla brány firewall na úrovni serveru](sql-database-server-level-firewall-rule.md).


## <a name="configure-stream-analytics-integration"></a>Konfigurace integrace analýzy datového proudu

1. Přihlaste se k portálu Azure. 
2. Přejděte do databáze SQL, kde chcete ingestovat streamovaná data. Vyberte **Analýza datového proudu (náhled).** 

    ![Stream Analytics](media/sql-database-stream-analytics/stream-analytics.png)

3. Pokud chcete začít ingestovat streamovaná data do této databáze SQL, vyberte **Vytvořit** a pojmenujte úlohu streamování a pak vyberte **Další: Vstup**. 

    ![vytvořit úlohu Stream Analytics](media/sql-database-stream-analytics/create-job.png)

4. Zadejte podrobnosti o zdroji událostí a pak vyberte **Další: Výstup**.

   - **Typ vstupu**: Centrum událostí/Centrum IoT Hub
   - **Vstupní alias**: Zadejte název pro identifikaci zdroje událostí. 
   - **Předplatné**: Stejné jako předplatné databáze SQL 
   - **Obor názvů centra událostí**: Název oboru názvů 
   - **Název centra událostí**: Název centra událostí ve vybraném oboru názvů 
   - **Název zásad centra událostí** (Výchozí pro vytvoření nového): Zadejte název zásady 
   - **Skupina spotřebitelů Centra událostí** (Výchozí pro vytvoření nového): Přiřazujte název skupiny příjemce  
     - Doporučujeme vytvořit skupinu spotřebitelů a zásady pro každou novou úlohu Azure Stream Analytics, kterou vytvoříte zde. Skupiny spotřebitelů povolují pouze pět souběžných čtecích programů, takže poskytnutí vyhrazené skupiny spotřebitelů pro každou úlohu zabrání chybám, které by mohly vzniknout při překročení tohoto limitu. Vyhrazená zásada umožňuje otočit klíč nebo odvolat oprávnění bez ovlivnění jiných prostředků.

    ![vytvořit úlohu Stream Analytics](media/sql-database-stream-analytics/create-job-output.png)

5. Vyberte tabulku, do které chcete singestovat streamovaná data. Po dokončení vyberte **Vytvořit**.
   - **Uživatelské jméno**, **Heslo**: Zadejte pověření pro ověřování serveru SQL. Vyberte **Ověřit**.
   - **Tabulka**: Vyberte **Vytvořit nový** nebo **Použít existující**. V tomto toku vyberte **vytvořit**. Tím se vytvoří nová tabulka při spuštění úlohy Analytics datového proudu.

    ![vytvořit úlohu Stream Analytics](media/sql-database-stream-analytics/create.png)

6. Otevře se stránka dotazu s následujícími podrobnostmi:

   - **Váš vstup** (zdroj vstupních událostí), ze kterého budete ingestovat data  
   - **Výstup** (výstupní tabulka), ve kterém budou ukládána transformovaná data 
   - Ukázkový [dotaz SAQL](../stream-analytics/stream-analytics-stream-analytics-query-patterns.md) s příkazem SELECT. 
   - **Náhled vstupu**: Zobrazuje snímek nejnovějších příchozích dat ze zdroje vstupních událostí.  
     - Typ serializace v datech je automaticky rozpoznán (JSON/CSV). Můžete ručně změnit také JSON / CSV / AVRO. 
     - Můžete zobrazit náhled příchozích dat ve formátu Tabulka nebo Nezpracovaný. 
     - Pokud zobrazená data nejsou aktuální, kliknutím na **Aktualizovat** zobrazte nejnovější události. 
     - Výběrem **časového rozsahu** otestujete dotaz podle určitého časového rozsahu příchozích událostí. 
     - Vyberte **Nahrát ukázkový vstup** k otestování dotazu nahráním ukázkového souboru JSON/CSV. Další informace o testování dotazu SAQL najdete v [tématu Testování úlohy Azure Stream Analytics s ukázkovými daty](../stream-analytics/stream-analytics-test-query.md). 

    ![testovací dotaz](media/sql-database-stream-analytics/test-query.png)


   - **Výsledky testu**: Vyberte **testovací dotaz** a uvidíte výsledky streamovacího dotazu. 

    ![výsledky testu](media/sql-database-stream-analytics/test-results.png)

   - **Schéma výsledků testů**: Zobrazuje schéma výsledků dotazu streamování po testování. Ujistěte se, že schéma výsledků testu odpovídá výstupnímu schématu. 

    ![schéma výsledků testů](media/sql-database-stream-analytics/test-results-schema.png)


   - **Schéma výstupu**: Obsahuje schéma tabulky, kterou jste vybrali v kroku 5 (nové nebo existující).
     - Vytvořit nový: Pokud jste tuto možnost vybrali v kroku 5, schéma se ještě nezobrazí, dokud nespustíte úlohu streamování. Při vytváření nové tabulky vyberte příslušný index tabulky. Další informace o indexování tabulek naleznete v tématu [Clustered and Nonclustered Indexes Described](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/).
    - Použít existující: Pokud jste tuto možnost vybrali v kroku 5, zobrazí se schéma vybrané tabulky. 
 
7. Po dokončení vytváření & testování dotazu vyberte **Uložit dotaz**. Vyberte **Spustit úlohu Analýzy datového proudu,** chcete-li začít ingestovat transformovaná data do tabulky SQL. Po dokončení následujících polí **spusťte** úlohu. 
   - **Čas zahájení výstupu**: Definuje čas prvního výstupu úlohy.  
     - Nyní: Úloha bude nyní zahájena a bude zpracovávat nová příchozí data.
     - Vlastní: Úloha bude zahájena nyní, ale bude zpracovávat data z určitého bodu v čase (které mohou být v minulosti nebo budoucnosti). Další informace najdete v [tématu Jak spustit úlohu Azure Stream Analytics](../stream-analytics/start-job.md).
   - **Jednotky streamování**: Azure Stream Analytics se oceňuje podle počtu jednotek streamování potřebných ke zpracování dat do služby. Další informace najdete v tématu [Azure Stream Analytics ceny](https://azure.microsoft.com/pricing/details/stream-analytics/). 
   - **Zpracování chyb výstupních dat**:  
     - Opakování: Pokud dojde k chybě, Azure Stream Analytics opakuje zápis události neomezeně dlouho, dokud zápis proběhne úspěšně. Neexistuje žádný časový časový osývač pro opakování. Nakonec všechny následné události jsou blokovány zpracování událostí, která se pokouší znovu. Tato možnost je výchozí zásady zpracování výstupních chyb. 
     - Drop: Azure Stream Analytics klesne všechny výstupní události, která má za následek chybu převodu dat. Zahozené události není možné obnovit pro pozdější opětovné zpracování. Všechny přechodné chyby (například chyby sítě) jsou opakovány bez ohledu na konfiguraci zásad zpracování výstupních chyb. 
   - **Nastavení výstupu databáze SQL:** Možnost pro dědění schéma dělení předchozí krok dotazu, povolit plně paralelní topologie s více zapisovače do tabulky. Další informace najdete v tématu [Výstup Azure Stream Analytics do Azure SQL Database](../stream-analytics/stream-analytics-sql-output-perf.md).
   - **Maximální počet dávek**: Doporučený horní limit počtu záznamů odeslaných s každou transakcí hromadného vložení.  
    Další informace o zpracování chyb výstupu najdete [v tématu Zásady chyb výstupu ve službě Azure Stream Analytics](../stream-analytics/stream-analytics-output-error-policy.md).  

    ![zahájit úlohu](media/sql-database-stream-analytics/start-job.png)

8. Po spuštění úlohy se v seznamu zobrazí úloha Spuštěno a můžete provést následující akce: 
   - **Spuštění/zastavení úlohy**: Pokud je úloha spuštěna, můžete ji zastavit. Pokud je úloha zastavena, můžete ji spustit. 
   - **Upravit úlohu**: Dotaz můžete upravit. Pokud chcete provést další změny úlohy ex, přidejte další vstupy /výstupy a otevřete úlohu v Stream Analytics. Možnost úpravy je zakázána, pokud je úloha spuštěna. 
   - **Náhled výstupní tabulky**: Tabulku můžete zobrazit v editoru dotazů SQL. 
   - **Otevřít v Stream Analytics**: Otevřete úlohu ve službě Stream Analytics a zobrazte monitorování a laděte podrobnosti o úloze. 


    ![úlohy analýzy datového proudu](media/sql-database-stream-analytics/jobs.png)






## <a name="next-steps"></a>Další kroky

- [Dokumentace ke službě Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/)
- [Vzory řešení služby Azure Stream Analytics](../stream-analytics/stream-analytics-solution-patterns.md)
