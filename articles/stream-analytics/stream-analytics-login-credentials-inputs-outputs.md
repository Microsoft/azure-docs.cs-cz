---
title: Otočit přihlašovací údaje v úlohy Azure Stream Analytics
description: Tento článek popisuje, jak aktualizovat přihlašovací údaje vstupy a jímky výstupu v Azure Stream Analytics úlohy.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/11/2018
ms.openlocfilehash: b49b4fecb6be70987e7e6736d78f224c03f719bf
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
ms.locfileid: "30907275"
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Otočit přihlašovací údaje pro vstupy a výstupy úlohy Stream Analytics

Vždy, když jste znovu vygenerovat přihlašovací údaje pro vstup nebo výstup úlohy Stream Analytics, by měl aktualizovat úlohy s novými pověřeními. Je nutné zastavit úlohu před aktualizací přihlašovací údaje, přihlašovací údaje nelze nahradit, při běhu úlohy. Aby se snížilo prodleva mezi zastavením a restartováním úlohu, Stream Analytics podporuje obnovení úlohy z posledního výstup. Toto téma popisuje proces otáčení přihlašovací údaje a restartování stav úlohy s novými pověřeními.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Znovu vygenerujte nové přihlašovací údaje a aktualizovat vaše úlohy s novými pověřeními 

V této části jsme vás provede opakované generování pověření pro úložiště objektů Blob, Event Hubs, databáze SQL a Table Storage. 

### <a name="blob-storagetable-storage"></a>Úložiště objektů BLOB úložiště/tabulky
1. Přihlaste se k portálu Azure > Procházet účet úložiště, který jste použili jako vstup/výstup pro úlohu služby Stream Analytics.    
2. V části nastavení, otevřete **přístupové klíče**. Mezi dvě výchozí klíče (key1, key2) vyberte ten, který nepoužívá úlohu a znovu:  
   ![Obnovit klíče účtu úložiště](media/stream-analytics-login-credentials-inputs-outputs/image1.png)
3. Zkopírujte nově vygenerovaný klíč.    
4. Z portálu Azure přejděte úlohu služby Stream Analytics > vyberte **Zastavit** a počkejte na zastavení úlohy.    
5. Vyhledejte objekt Blob nebo Table storage vstupu a výstupu, pro který chcete aktualizovat přihlašovací údaje.    
6. Najít **klíč účtu úložiště** pole a vložte nově vygenerovaný klíč > klikněte na tlačítko **Uložit**.    
7. Test připojení se automaticky spustí, když uložení změn, můžete ji zobrazit na kartě oznámení. Jsou dvě oznámení: 1 odpovídá ukládání aktualizace a jiné odpovídá testování připojení:  
   ![Oznámení po dokončení úprav klíč](media/stream-analytics-login-credentials-inputs-outputs/image4.png)
8. Pokračujte [spustit úlohu při posledním zastaven] (#start-your-job-from-the-last-stopped-time) oddílu.

### <a name="event-hubs"></a>Event Hubs

1. Přihlaste se k portálu Azure > Procházet centra událostí, které jste použili jako vstup/výstup pro úlohu služby Stream Analytics.    
2. V části nastavení, otevřete **zásady sdíleného přístupu** a vyberte zásadu, požadovaný přístup. Mezi **primární klíč** a **sekundární klíč**, vyberte ten, který nepoužívá úlohu a znovu:  
   ![Obnovit klíče pro centra událostí](media/stream-analytics-login-credentials-inputs-outputs/image2.png)
3. Zkopírujte nově vygenerovaný klíč.    
4. Z portálu Azure přejděte úlohu služby Stream Analytics > vyberte **Zastavit** a počkejte na zastavení úlohy.    
5. Vyhledejte události rozbočovače vstupu a výstupu, pro který chcete aktualizovat přihlašovací údaje.    
6. Najít **klíč události rozbočovače zásad** pole a vložte nově vygenerovaný klíč > klikněte na tlačítko **Uložit**.    
7. Test připojení se automaticky spustí, když uložíte změny, ujistěte se, že má úspěšně úspěšně.    
8. Pokračujte [spustit úlohu při posledním zastaven](#start-your-job-from-the-last-stopped-time) části.

### <a name="sql-database"></a>Databáze SQL

Potřebujete připojit k databázi SQL a aktualizovat přihlašovací údaje stávajícího uživatele. Přihlašovací údaje můžete aktualizovat pomocí portálu Azure nebo klienta nástroje, jako je SQL Server Management Studio. V této části ukážeme proces aktualizace přihlašovacích údajů pomocí portálu Azure.

1. Přihlaste se k portálu Azure > Procházet databázi SQL, který jste použili jako výstup pro úlohu služby Stream Analytics.    
2. Z **Průzkumníku dat**, přihlašovací údaje nebo připojení k vaší databázi > vyberte typ autorizace jako **ověřování systému SQL server** > zadejte vaše **přihlášení** a  **Heslo** podrobnosti > vyberte **Ok**.  
   ![Obnovit přihlašovací údaje pro databázi SQL.](media/stream-analytics-login-credentials-inputs-outputs/image3.png)

3. Na kartě dotaz změnit heslo pro jednoho uživatele spuštěním následujícího dotazu (Nezapomeňte nahradit `<user_name>` s vaše uživatelské jméno a `<new_password>` pomocí nového hesla):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Poznamenejte si nové heslo.    
5. Z portálu Azure přejděte úlohu služby Stream Analytics > vyberte **Zastavit** a počkejte na zastavení úlohy.    
6. Vyhledejte výstupu SQL databáze, pro který chcete otočit přihlašovací údaje. Aktualizace hesla a uložte změny.    
7. Test připojení se automaticky spustí, když uložíte změny, ujistěte se, že má úspěšně úspěšně.    
8. Pokračujte [spustit úlohu při posledním zastaven](#start-your-job-from-the-last-stopped-time) části.

### <a name="power-bi"></a>Power BI
1. Přihlaste se k portálu Azure > Procházet vaše úloha Stream Analytics > vyberte **Zastavit** a počkejte na zastavení úlohy.    
2. Vyhledejte výstup Power BI, pro kterou chcete obnovit přihlašovací údaje > klikněte na tlačítko **obnovit autorizace** (měla zobrazit zpráva úspěch) > **Uložit** změny.    
3. Test připojení se automaticky spustí, když uložíte změny, ujistěte se, že úspěšně úspěšně prošel.    
4. Pokračujte [spustit úlohu při posledním zastaven](#start-your-job-from-the-last-stopped-time) části.

## <a name="start-your-job-from-the-last-stopped-time"></a>Spustit úlohu při posledním zastaven

1. Přejděte do úlohy **přehled** podokně > vyberte **spustit** spustíte úlohu.    
2. Vyberte **při poslední zastavení** > klikněte na tlačítko **spustit**. Všimněte si, že "při poslední zastavení" možnost se zobrazí pouze pokud jste dříve spustil úlohu a měl některé výstup vygenerovat. Restartování úlohy na základě na poslední výstupní hodnotu času.
   ![Spustit úlohu](media/stream-analytics-login-credentials-inputs-outputs/image5.png)

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
