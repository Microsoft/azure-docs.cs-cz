---
title: Otočení přihlašovacích údajů v pracovních úlohách Azure Stream Analytics
description: Tento článek popisuje, jak aktualizovat přihlašovací údaje vstupů a výstupní chýupací mise v azure stream analytics úlohy.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 3ae639dd7c5a42fc6880240988f0fb2817b09f43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75425977"
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Otočení přihlašovacích údajů pro vstupy a výstupy úlohy Stream Analytics

Kdykoli znovu vygenerujete přihlašovací údaje pro vstup nebo výstup úlohy Stream Analytics, měli byste úlohu aktualizovat novými přihlašovacími údaji. Úlohu je nutné zastavit před aktualizací pověření, pověření nelze nahradit, když je úloha spuštěna. Chcete-li snížit prodlevy mezi zastavení a restartování úlohy, Stream Analytics podporuje obnovení úlohy z jeho poslední ho výstupu. Toto téma popisuje proces otáčení přihlašovacích údajů a restartování úlohy s novými pověřeními.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Obnova nových přihlašovacích údajů a aktualizace úlohy pomocí nových přihlašovacích údajů 

V této části vás provedeme obnovením přihlašovacích údajů pro úložiště objektů Blob, centra událostí, databázi SQL a úložiště tabulek. 

### <a name="blob-storagetable-storage"></a>Úložiště objektů blob nebo úložiště tabulek
1. Přihlaste se k portálu Azure > procházet účet úložiště, který jste použili jako vstup a výstup pro úlohu Stream Analytics.    
2. V části nastavení otevřete **přístupové klávesy**. Mezi dvěma výchozími klávesami (key1, key2) vyberte ten, který vaše úloha nepoužívá, a obnovte ji:  
   ![Obnovení klíčů pro účet úložiště](media/stream-analytics-login-credentials-inputs-outputs/regenerate-storage-keys.png)
3. Zkopírujte nově generovaný klíč.    
4. Na portálu Azure projděte úlohu Stream Analytics > vyberte **Zastavit** a počkejte, až se úloha zastaví.    
5. Vyhledejte vstup/výstup úložiště blob/table, pro který chcete aktualizovat pověření.    
6. Najděte pole **Klíč účtu úložiště** a vložte nově vygenerovaný klíč > klepněte na **uložit**.    
7. Test připojení se automaticky spustí při uložení změn, můžete jej zobrazit na kartě oznámení. Existují dvě oznámení- jeden odpovídá uložení aktualizace a další odpovídá testování připojení:  
   ![Oznámení po úpravě klíče](media/stream-analytics-login-credentials-inputs-outputs/edited-key-notifications.png)
8. Pokračujte a [začněte úlohu z poslední hodové části.](#start-your-job-from-the-last-stopped-time)

### <a name="event-hubs"></a>Event Hubs

1. Přihlaste se k portálu Azure > procházet Centrum událostí, které jste použili jako vstup a výstup pro úlohu Stream Analytics.    
2. V části nastavení otevřete **zásady sdíleného přístupu** a vyberte požadované zásady přístupu. Mezi **primárním** a **sekundárním klíčem**vyberte ten, který vaše úloha nepoužívá, a obnovte jej:  
   ![Obnovení klíčů pro centra událostí](media/stream-analytics-login-credentials-inputs-outputs/regenerate-event-hub-keys.png)
3. Zkopírujte nově generovaný klíč.    
4. Na portálu Azure projděte úlohu Stream Analytics > vyberte **Zastavit** a počkejte, až se úloha zastaví.    
5. Vyhledejte vstup a výstup centra událostí, pro který chcete aktualizovat přihlašovací údaje.    
6. Najděte pole **Klíč zásad centra událostí** a vložte nově vygenerovaný klíč > klepněte na tlačítko **Uložit**.    
7. Test připojení se automaticky spustí při uložení změn, ujistěte se, že úspěšně prošel.    
8. Pokračujte a [začněte úlohu z poslední hodové části.](#start-your-job-from-the-last-stopped-time)

### <a name="sql-database"></a>Databáze SQL

Chcete-li aktualizovat přihlašovací údaje existujícího uživatele, musíte se připojit k databázi SQL. Pověření můžete aktualizovat pomocí portálu Azure nebo pomocí nástroje na straně klienta, jako je například SQL Server Management Studio. Tato část ukazuje proces aktualizace přihlašovacích údajů pomocí portálu Azure.

1. Přihlaste se k portálu Azure > procházet databázi SQL, kterou jste použili jako výstup pro úlohu Stream Analytics.    
2. Z **průzkumníka dat**> možnost Přihlásit se/připojit k databázi > vyberte typ autorizace jako **typ ověřování serveru SQL** server > typ v podrobnostech o **přihlášení** a **hesle** > možnost **IOk**.  
   ![Obnovení pověření pro databázi SQL](media/stream-analytics-login-credentials-inputs-outputs/regenerate-sql-credentials.png)

3. Na kartě dotazu změňte heslo pro jednoho z uživatelů spuštěním následujícího dotazu (nezapomeňte nahradit `<user_name>` uživatelské jméno a `<new_password>` nové heslo):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Poznamenejte si nové heslo.    
5. Na portálu Azure projděte úlohu Stream Analytics > vyberte **Zastavit** a počkejte, až se úloha zastaví.    
6. Vyhledejte výstup databáze SQL, pro který chcete otočit pověření. Aktualizujte heslo a uložte změny.    
7. Test připojení se automaticky spustí při uložení změn, ujistěte se, že úspěšně prošel.    
8. Pokračujte a [začněte úlohu z poslední hodové části.](#start-your-job-from-the-last-stopped-time)

### <a name="power-bi"></a>Power BI
1. Přihlaste se k portálu Azure > procházet úlohu Stream Analytics > vyberte **Zastavit** a počkejte, až se úloha zastaví.    
2. Vyhledejte výstup Power BI, pro který chcete obnovit přihlašovací údaje > klikněte na **autorizaci Obnovit** (měla by se zobrazit zpráva o úspěchu) > **Uložit** změny.    
3. Test připojení se automaticky spustí při uložení změn, ujistěte se, že úspěšně prošel.    
4. Pokračujte a [začněte úlohu z poslední hodové části.](#start-your-job-from-the-last-stopped-time)

## <a name="start-your-job-from-the-last-stopped-time"></a>Začněte svou práci od posledního zastaveného času

1. Přejděte do podokna **Přehled** úlohy > výběrem **možnosti Start** spusťte úlohu.    
2. Vyberte **Při posledním zastavení** > klepněte na tlačítko **Start**. Všimněte si, že možnost "Při posledním zastavení" se zobrazí pouze v případě, že jste dříve spustili úlohu a vygenerovali nějaký výstup. Úloha je restartována na základě času poslední výstupní hodnoty.
   ![Spuštění úlohy Stream Analytics](media/stream-analytics-login-credentials-inputs-outputs/start-stream-analytics-job.png)

## <a name="next-steps"></a>Další kroky
* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
