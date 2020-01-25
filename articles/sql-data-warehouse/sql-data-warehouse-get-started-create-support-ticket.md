---
title: Postup vytvoření lístku podpory
description: Jak vytvořit lístek podpory v Azure SQL Data Warehouse
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 04fd6267782ab89e12288dadb64d28d1ef1746b5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717842"
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Jak vytvořit lístek podpory pro SQL Data Warehouse
Pokud máte nějaké problémy s vaším SQL Data Warehouse, vytvořte lístek podpory, aby vám tým technické podpory mohl pomoct.

## <a name="create-a-support-ticket"></a>Vytvoření lístku podpory
1. Otevřete [portál Azure](https://portal.azure.com/).
2. Na domovské obrazovce klikněte na dlaždici **Nápověda a podpora**.
   
    ![Nápověda a podpora](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. Na dlaždici Nápověda a podpora klikněte na možnost **Nová žádost o podporu** a vyplňte okno **Základní informace**.

   Vyberte svůj [plán podpory Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).
   
   * **Podpora k fakturaci, správě kvót a předplatného** je k dispozici na všech úrovních podpory.
   * Podpora **přerušení oprav** je poskytována prostřednictvím [podpory Developer](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)nebo [Premier](https://azure.microsoft.com/support/plans/premier/) . Problémy vyžadující opravu jsou problémy zaznamenané uživateli při používání Azure, když existuje důvodný předpoklad, že je příčina na straně Microsoftu.
   * V rámci podpory na úrovni [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) a [Premier](https://azure.microsoft.com/support/plans/premier/) jsou k dispozici služby **mentorování vývojářů** a **poradenské služby**. 
     
     Pokud máte plán podpory Premier Support, můžete také problémy týkající se SQL Data Warehouse nahlásit na [online portálu Microsoft Premier](https://premier.microsoft.com/). Další informace o různých plánech podpory, včetně rozsahu, doby odezvy, cen atd. najdete v tématu věnovaném [plánům podpory Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) .  Nejčastější dotazy týkající se podpory Azure najdete v [nejčastějších](https://azure.microsoft.com/support/faq/)dotazech k podpoře Azure.  
        
     ![Okno Základní informace](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
     ![Okno Základní informace1](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. Vyplňte okno **Problém**.

    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > Ve výchozím nastavení obsahuje každý server SQL (např. myserver.database.windows.net) **kvóty DTU** o hodnotě 45 000. Tato kvóta je jednoduše bezpečnostní omezení. Kvótu můžete zvýšit vytvořením lístku podpory a výběrem *kvóty* jako typu požadavku. Chcete-li vypočítat potřebné DTU, vynásobte hodnotu 7,5 celkovým [DWU](sql-data-warehouse-overview-what-is.md) . Pokud například chcete hostovat dvě DW6000 na jednom serveru SQL, měli byste požadovat kvóty DTU o hodnotě 90 000.  Vaši aktuální spotřebu DTU z okna serveru SQL můžete zobrazit na portálu. Pozastavené i nepozastavené databáze se započítávají do kvóty DTU. 
   > 
   > 
   
5. Vyplňte své **Kontaktní informace**.

   ![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. Kliknutím na **Vytvořit** odešlete žádost o podporu.

## <a name="monitor-a-support-ticket"></a>Monitorování lístku podpory
Po odeslání žádosti o podporu vás tým podpory Azure vás bude kontaktovat. Pokud chcete zkontrolovat stav a podrobnosti žádosti, klikněte na řídicím panelu na **Všechny žádosti o podporu**.

![Zkontrolování stavu](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>Další zdroje informací
Můžete se také připojit k SQL Data Warehouse komunitě na [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/) nebo prostřednictvím [fóra MSDN Azure SQL Data Warehouse](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).

 
