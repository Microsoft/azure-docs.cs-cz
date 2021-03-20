---
title: Otočení přihlašovacích údajů v úlohách Azure Stream Analytics
description: Tento článek popisuje, jak aktualizovat přihlašovací údaje vstupních a výstupních umyvadel v úlohách Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: fd6c072f9783e8ff5d4d5e465b513c2e530bfd63
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98015228"
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Otočit přihlašovací údaje pro vstupy a výstupy Stream Analytics úlohy

Kdykoli znovu vygenerujete přihlašovací údaje pro vstup nebo výstup Stream Analytics úlohy, měli byste úlohu aktualizovat pomocí nových přihlašovacích údajů. Před aktualizací přihlašovacích údajů musíte úlohu zastavit. přihlašovací údaje nemůžete nahradit, dokud je úloha spuštěná. Chcete-li zkrátit prodlevu mezi zastavením a restartováním úlohy, Stream Analytics podporuje obnovení úlohy z posledního výstupu. Toto téma popisuje proces střídání přihlašovacích údajů a opětovném spuštění úlohy s novými přihlašovacími údaji.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Znovu vygenerujte nové přihlašovací údaje a aktualizujte svou úlohu pomocí nových přihlašovacích údajů. 

V této části Vás provedeme procesem opětovného generování přihlašovacích údajů pro Blob Storage, Event Hubs, SQL Database a Table Storage. 

### <a name="blob-storagetable-storage"></a>Úložiště objektů BLOB/úložiště tabulek
1. Přihlaste se k Azure Portal > v účtu úložiště, který jste použili jako vstup/výstup pro úlohu Stream Analytics, vyhledejte.    
2. V části Nastavení otevřete **přístupové klávesy**. Mezi dvěma výchozími klíči (klíč1, key2) vyberte ten, který nepoužívá vaše úloha a znovu ho vygeneruje:  
   ![Znovu vygenerovat klíče pro účet úložiště](media/stream-analytics-login-credentials-inputs-outputs/regenerate-storage-keys.png)
3. Zkopírujte nově vygenerovaný klíč.    
4. V Azure Portal Projděte úlohu Stream Analytics > vyberte **zastavit** a počkejte, než se úloha zastaví.    
5. Vyhledejte vstupní/výstupní úložiště objektů BLOB nebo tabulky, pro které chcete aktualizovat přihlašovací údaje.    
6. Vyhledejte pole **klíč účtu úložiště** a vložte nově vygenerovaný klíč > klikněte na **Uložit**.    
7. Test připojení se automaticky spustí při uložení změn, můžete ho zobrazit na kartě oznámení. Existují dvě oznámení – jedna odpovídá uložení aktualizace a druhá odpovídá testování připojení:  
   ![Oznámení po úpravě klíče](media/stream-analytics-login-credentials-inputs-outputs/edited-key-notifications.png)
8. Přejděte ke [spuštění úlohy z části čas posledního zastavení](#start-your-job-from-the-last-stopped-time) .

### <a name="event-hubs"></a>Event Hubs

1. Přihlaste se k Azure Portal > procházení centra událostí, které jste použili jako vstup/výstup pro úlohu Stream Analytics.    
2. V části Nastavení otevřete **zásady sdíleného přístupu** a vyberte požadované zásady přístupu. Mezi **primárním** a **sekundárním klíčem** vyberte ten, který nepoužívá vaše úloha a znovu ho vygeneruje:  
   ![Znovu vygenerovat klíče pro Event Hubs](media/stream-analytics-login-credentials-inputs-outputs/regenerate-event-hub-keys.png)
3. Zkopírujte nově vygenerovaný klíč.    
4. V Azure Portal Projděte úlohu Stream Analytics > vyberte **zastavit** a počkejte, než se úloha zastaví.    
5. Vyhledejte vstupní/výstupní centra událostí, pro které chcete aktualizovat přihlašovací údaje.    
6. Vyhledejte pole **klíč zásad centra událostí** a vložte nově vygenerovaný klíč > klikněte na **Uložit**.    
7. Test připojení se automaticky spustí při uložení změn, ujistěte se, že úspěšně proběhl.    
8. Přejděte ke [spuštění úlohy z části čas posledního zastavení](#start-your-job-from-the-last-stopped-time) .

### <a name="sql-database"></a>SQL Database

Abyste mohli aktualizovat přihlašovací údaje stávajícího uživatele, musíte se připojit k SQL Database. Přihlašovací údaje můžete aktualizovat pomocí Azure Portal nebo nástroje na straně klienta, jako je například SQL Server Management Studio. Tato část demonstruje proces aktualizace přihlašovacích údajů pomocí Azure Portal.

1. Přihlaste se k Azure Portal > v databázi SQL vyhledejte, kterou jste použili jako výstup pro úlohu Stream Analytics.    
2. V **Průzkumníku dat** se přihlaste nebo připojte k databázi > vyberte typ autorizace jako **ověřování serveru SQL** > v **přihlašovacích** údajích a **hesle** , > vyberte **OK**.  
   ![Znovu vygenerovat přihlašovací údaje pro SQL Database](media/stream-analytics-login-credentials-inputs-outputs/regenerate-sql-credentials.png)

3. Na kartě dotaz změňte heslo pro jednoho z uživatelů spuštěním následujícího dotazu (Nezapomeňte nahradit vaším uživatelským `<user_name>` jménem a `<new_password>` novým heslem):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Poznamenejte si nové heslo.    
5. V Azure Portal Projděte úlohu Stream Analytics > vyberte **zastavit** a počkejte, než se úloha zastaví.    
6. Vyhledejte výstup SQL Database, pro který chcete otočit přihlašovací údaje. Aktualizujte heslo a uložte změny.    
7. Test připojení se automaticky spustí při uložení změn, ujistěte se, že úspěšně proběhl.    
8. Přejděte ke [spuštění úlohy z části čas posledního zastavení](#start-your-job-from-the-last-stopped-time) .

### <a name="power-bi"></a>Power BI
1. Přihlaste se k Azure Portal > Projděte úlohu Stream Analytics > vyberte **zastavit** a počkejte, než se úloha zastaví.    
2. Vyhledejte výstup Power BI, pro který chcete obnovit přihlašovací údaje > klikněte na tlačítko **obnovit autorizaci** (měla by se zobrazit zpráva o úspěchu) > **Uložit** změny.    
3. Test připojení se automaticky spustí při uložení změn, takže se ujistěte, že se úspěšně předali.    
4. Přejděte ke [spuštění úlohy z části čas posledního zastavení](#start-your-job-from-the-last-stopped-time) .

## <a name="start-your-job-from-the-last-stopped-time"></a>Spustit úlohu z posledního času zastavení

1. Přejděte do podokna **Přehled** úlohy > výběrem **Spustit** spusťte úlohu.    
2. Vyberte, **kdy se naposledy zastavilo** > klikněte na **Spustit**. Všimněte si, že možnost "při posledním zastavení" se zobrazí pouze v případě, že jste úlohu dříve spustili a byl vygenerován nějaký výstup. Úloha se restartuje na základě času poslední výstupní hodnoty.
   ![Spustit úlohu Stream Analytics](media/stream-analytics-login-credentials-inputs-outputs/start-stream-analytics-job.png)

## <a name="next-steps"></a>Další kroky
* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](/rest/api/streamanalytics/)