---
title: Jak vytvořit lístek podpory pro Azure SQL Data Warehouse | Microsoft Docs
description: Jak vytvořit lístek podpory v Azure SQL Data Warehouse
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/12/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 1485ff4392caa5df0851d46a964c7699c9f8e90b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Jak vytvořit lístek podpory pro SQL Data Warehouse
Pokud máte se službou SQL Data Warehouse problémy, vytvořte si lístek podpory, aby vám mohl náš technický tým pomoct.

## <a name="create-a-support-ticket"></a>Vytvoření lístku podpory
1. Otevřete [Azure Portal][Azure portal].
2. Na domovské obrazovce klikněte na dlaždici **Nápověda a podpora**.
   
    ![Nápověda a podpora](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. Na dlaždici Nápověda a podpora klikněte na možnost **Nová žádost o podporu** a vyplňte okno **Základní informace**.

   Vyberte svůj [plán podpory Azure][Azure support plan].
   
   * **Podpora k fakturaci, správě kvót a předplatného** je k dispozici na všech úrovních podpory.
   * **Podpora k problémům vyžadujícím opravu** se poskytuje prostřednictvím podpory[Developer][Developer], [Standard][Standard], [Professional Direct][Professional Direct] nebo [Premier][Premier]. Problémy vyžadující opravu jsou problémy, které mají zákazníci při používání Azure a u kterých se dá předpokládat, že je způsobila společnosti Microsoft.
   * V rámci podpory na úrovni plánů [Professional Direct][Professional Direct] a [Premier Support][Premier] jsou k dispozici služby **mentorování vývojářů** a **poradenské služby**. 
     
     Pokud máte plán podpory Premier Support, můžete také problémy týkající se služby SQL Data Warehouse nahlásit na [online portálu Microsoft Premier][Microsoft Premier online portal].  Další informace o různých plánech podpory, včetně rozsahu, doby odezvy, cen atd. najdete v tématu věnovaném [plánům podpory Azure][Azure support plan].  Nejčastější dotazy týkající se podpory Azure najdete v [nejčastějších dotazech týkajících se podpory k Azure][Azure support FAQs].  
        
    ![Okno Základní informace](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
    ![Okno Základní informace1](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. Vyplňte okno **Problém**.
    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > Ve výchozím nastavení obsahuje každý server SQL (např. myserver.database.windows.net) **kvóty DTU** o hodnotě 45 000. Tato kvóta je jednoduše bezpečnostní omezení. Kvótu můžete zvýšit vytvořením lístku podpory a výběrem *kvóty* jako typu požadavku. Chcete-li vypočítat potřebné DTU, vynásobte celkové potřebné [DWU][DWU] číslem 7,5. Pokud například chcete hostovat dvě DW6000 na jednom serveru SQL, měli byste požadovat kvóty DTU o hodnotě 90 000.  Vaši aktuální spotřebu DTU z okna serveru SQL můžete zobrazit na portálu. Pozastavené i nepozastavené databáze se započítávají do kvóty DTU. 
   > 
   > 
   
5. Vyplňte své **Kontaktní informace**.
![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. Kliknutím na **Vytvořit** odešlete žádost o podporu.

## <a name="monitor-a-support-ticket"></a>Monitorování lístku podpory
Po odeslání žádosti o podporu vás bude kontaktovat tým podpory Azure. Pokud chcete zkontrolovat stav a podrobnosti žádosti, klikněte na řídicím panelu na **Všechny žádosti o podporu**.

![Zkontrolování stavu](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>Další prostředky
Kromě toho se můžete spojit s komunitou SQL Data Warehouse na [Stack Overflow][Stack Overflow] nebo na [fóru pro Azure SQL Data Warehouse na webu MSDN][Azure SQL Data Warehouse MSDN forum].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md

<!--MSDN references--> 

<!--Other web references--> 
[Azure portal]: https://portal.azure.com/
[Azure support plan]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Developer]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Azure support FAQs]: https://azure.microsoft.com/support/faq/
[Microsoft Premier online portal]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL Data Warehouse MSDN forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/

