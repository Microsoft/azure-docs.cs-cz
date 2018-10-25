---
title: Pravidelná změna přihlašovacích údajů přihlášení v úlohách Azure Stream Analytics
description: Tento článek popisuje, jak aktualizovat přihlašovací údaje vstupů a výstupní jímky ve službě Azure Stream Analytics úlohy.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/11/2018
ms.openlocfilehash: 2dd5721ad0f45080a73172e150891cfb5f48d329
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025765"
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Otočit o přihlašovací údaje pro vstupy a výstupy úlohy Stream Analytics

Pokaždé, když vygenerujete přihlašovacích údajů pro vstup nebo výstup úlohy Stream Analytics, měli byste aktualizovat úlohu s novými pověřeními. Je nutné zastavit úlohu před aktualizací přihlašovací údaje, nelze nahradit přihlašovací údaje, zatímco úloha běží. Pokud chcete snížit interval mezi zastavení a restartování úlohy, Stream Analytics podporuje obnovení úlohu z jejího posledního výstupu. Toto téma popisuje proces otočení přihlašovací údaje a restartování úlohy pomocí nových přihlašovacích údajů.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Znovu vygenerujte nové přihlašovací údaje a aktualizovat vaše úlohy s novými přihlašovacími údaji 

V této části jsme vás provede vygenerovalo znovu přihlašovací údaje pro úložiště objektů Blob, Služba Event Hubs, SQL Database a Table Storage. 

### <a name="blob-storagetable-storage"></a>BLOB storage nebo Table storage
1. Přihlaste se k webu Azure portal > vyhledejte účet úložiště, který jste použili jako vstup/výstup pro úlohu Stream Analytics.    
2. V části nastavení, otevřete **přístupové klíče**. Mezi dvěma výchozí klíče (klíč1, klíč2) vyberte si ten, který se nepoužije, úlohy a obnovit ji:  
   ![Znovu vygenerovat klíče účtu úložiště](media/stream-analytics-login-credentials-inputs-outputs/image1.png)
3. Zkopírování nově vygenerovaný klíč.    
4. Na webu Azure Portal, procházet vaší úlohy Stream Analytics > vyberte **Zastavit** a počkejte na zastavení úlohy.    
5. Vyhledejte tabulek/objektů Blob úložiště vstupní a výstupní, pro které chcete aktualizovat přihlašovací údaje.    
6. Najít **klíč účtu úložiště** pole a vložíte nově vygenerovaný klíč > klikněte na tlačítko **Uložit**.    
7. Test připojení se automaticky spustí, když svoje změny uložíte, můžete je zobrazit na kartě upozornění. Jsou dva oznámení: 1 odpovídá ukládání aktualizace a jiné odpovídá testování připojení:  
   ![Oznámení po dokončení úprav klíč](media/stream-analytics-login-credentials-inputs-outputs/image4.png)
8. Přejít k [spuštění vaší úlohy z posledního času zastavené](#start-your-job-from-the-last-stopped-time) oddílu.

### <a name="event-hubs"></a>Event Hubs

1. Přihlaste se k webu Azure portal > Procházet Centrum událostí, který jste použili jako vstup/výstup pro úlohu Stream Analytics.    
2. V části nastavení, otevřete **zásady sdíleného přístupu** a vyberte zásadu, požadovaný přístup. Mezi **primární klíč** a **sekundární klíč**, vyberte si ten, který se nepoužije, úlohy a obnovit ji:  
   ![Znovu vygenerovat klíče pro Centrum událostí](media/stream-analytics-login-credentials-inputs-outputs/image2.png)
3. Zkopírování nově vygenerovaný klíč.    
4. Na webu Azure Portal, procházet vaší úlohy Stream Analytics > vyberte **Zastavit** a počkejte na zastavení úlohy.    
5. Vyhledejte Event hubs vstupní a výstupní, pro které chcete aktualizovat přihlašovací údaje.    
6. Najít **klíč zásad centra událostí** pole a vložíte nově vygenerovaný klíč > klikněte na tlačítko **Uložit**.    
7. Test připojení se automaticky spustí při uložení změn, ujistěte se, že je úspěšně prošel.    
8. Přejít k [spuštění vaší úlohy z posledního času zastavené](#start-your-job-from-the-last-stopped-time) oddílu.

### <a name="sql-database"></a>SQL Database

Musíte se připojit k SQL database a aktualizovat přihlašovací údaje stávajícího uživatele. Aktualizovat přihlašovací údaje pomocí webu Azure portal nebo na straně klienta nástroje, jako je SQL Server Management Studio. V této části ukážeme proces aktualizace přihlašovacích údajů pomocí webu Azure portal.

1. Přihlaste se k webu Azure portal > Procházet databáze SQL, který jste použili jako výstup úlohy Stream Analytics.    
2. Z **Průzkumník dat**, přihlášení/připojení k databázi > vyberte typ autorizace jako **ověřování systému SQL server** > zadejte vaší **přihlášení** a  **Heslo** podrobnosti > vyberte **Ok**.  
   ![Znovu vygenerovat přihlašovací údaje pro službu SQL database](media/stream-analytics-login-credentials-inputs-outputs/image3.png)

3. Na kartě dotazu změnit heslo pro jeden z vašich uživatelů spuštěním následujícího dotazu (Nezapomeňte nahradit `<user_name>` se svým uživatelským jménem a `<new_password>` pomocí nového hesla):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Poznamenejte si nové heslo.    
5. Na webu Azure Portal, procházet vaší úlohy Stream Analytics > vyberte **Zastavit** a počkejte na zastavení úlohy.    
6. Vyhledejte výstupní databáze SQL, pro kterou chcete pravidelná Změna přihlašovacích údajů. Aktualizujte heslo a uložte změny.    
7. Test připojení se automaticky spustí při uložení změn, ujistěte se, že je úspěšně prošel.    
8. Přejít k [spuštění vaší úlohy z posledního času zastavené](#start-your-job-from-the-last-stopped-time) oddílu.

### <a name="power-bi"></a>Power BI
1. Přihlaste se k webu Azure portal > Procházet vaší úlohy Stream Analytics > vyberte **Zastavit** a počkejte na zastavení úlohy.    
2. Vyhledejte výstup Power BI, pro kterou chcete prodloužit platnost přihlašovacích údajů > klikněte na tlačítko **obnovit autorizaci** (zobrazí se zpráva o úspěšném provedení) > **Uložit** změny.    
3. Test připojení se automaticky spustí při uložení změn, ujistěte se, že úspěšně úspěšně prošel.    
4. Přejít k [spuštění vaší úlohy z posledního času zastavené](#start-your-job-from-the-last-stopped-time) oddílu.

## <a name="start-your-job-from-the-last-stopped-time"></a>Spustit úlohu z posledního času zastaveno

1. Přejděte do projektu **přehled** podokně > vyberte **Start** spustíte úlohu.    
2. Vyberte **při posledním zastavení** > klikněte na tlačítko **Start**. Všimněte si, že "při posledním zastavení" možnost se zobrazí pouze pokud jste dříve spustili a má některé výstup vygenerována. Restartování úlohy závislosti na času poslední výstupní hodnota.
   ![Spustit úlohu](media/stream-analytics-login-credentials-inputs-outputs/image5.png)

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
