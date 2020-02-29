---
title: Postup vytvoření lístku podpory
description: Postup vytvoření lístku podpory ve službě Azure synapse Analytics.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 9623a878ea009ec7363501c09c324baa6d0fa7ad
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195663"
---
# <a name="how-to-create-a-support-ticket-for-azure-synapse-analytics"></a>Postup vytvoření lístku podpory pro Azure synapse Analytics
Pokud máte nějaké problémy s Azure synapse Analytics, vytvořte lístek podpory, aby vám tým technické podpory mohl pomoct.

## <a name="create-a-support-ticket"></a>Vytvoření lístku podpory
1. Otevřete [portál Azure](https://portal.azure.com/).
1. Na domovské obrazovce klikněte na dlaždici **Nápověda a podpora**.
   
    ![Nápověda a podpora](./media/sql-data-warehouse-get-started-create-support-ticket/main-page.png)

1. Zkontrolujte svůj [plán podpory Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).
   
   * **Podpora k fakturaci, správě kvót a předplatného** je k dispozici na všech úrovních podpory.
   * Podpora **přerušení oprav** je poskytována prostřednictvím [podpory Developer](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)nebo [Premier](https://azure.microsoft.com/support/plans/premier/) . Problémy vyžadující opravu jsou problémy, které mají zákazníci při používání Azure a u kterých se dá předpokládat, že je způsobila společnosti Microsoft.
   * V rámci podpory na úrovni **Professional Direct** a **Premier** jsou k dispozici služby [mentorování vývojářů](https://azure.microsoft.com/support/plans/prodirect/) a [poradenské služby](https://azure.microsoft.com/support/plans/premier/). 
     
     Pokud máte plán podpory Premier Support, můžete také problémy týkající se SQL Data Warehouse nahlásit na [online portálu Microsoft Premier](https://premier.microsoft.com/). Další informace o různých plánech podpory, včetně rozsahu, doby odezvy, cen atd. najdete v tématu věnovaném [plánům podpory Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) .  Nejčastější dotazy týkající se podpory Azure najdete v [nejčastějších](https://azure.microsoft.com/support/faq/)dotazech k podpoře Azure.
1. Na stránce **pomoc a podpora** vyberte **Nová žádost o podporu**. Z rozevírací nabídky vyberte typ problému. Potom pokračujte vyplněním informací na kartě **základy** . zadejte **Souhrn** problému a potom v nabídce vyberte **typ problému** a vyberte Uložit.

    ![Nápověda a podpora](./media/sql-data-warehouse-get-started-create-support-ticket/issue-type.png)

   > [!NOTE]
   > Ve výchozím nastavení obsahuje každý server SQL (např. myserver.database.windows.net) **kvóty DTU** o hodnotě 45 000. Tato kvóta je jednoduše bezpečnostní omezení. Kvótu můžete zvýšit vytvořením lístku podpory a výběrem *kvóty* jako typu požadavku. Chcete-li vypočítat potřebné DTU, vynásobte hodnotu 7,5 celkovým [DWU](sql-data-warehouse-overview-what-is.md) . Pokud například chcete hostovat dvě DW6000 na jednom serveru SQL, měli byste požadovat kvóty DTU o hodnotě 90 000.  Vaši aktuální spotřebu DTU z okna serveru SQL můžete zobrazit na portálu. Pozastavené i nepozastavené databáze se započítávají do kvóty DTU. 
   > 

1. Můžou se zobrazit řešení, která vám pomůžou problém vyřešit. Pokud řešení uvedená řešení nevyřeší váš problém, vyberte **Další: podrobnosti**. Odešlete podrobnosti o vašem problému a kontaktní údaje. Vyberte **Další: zkontrolovat + vytvořit**
![podrobnosti](./media/sql-data-warehouse-get-started-create-support-ticket/details.png)

    
1. Zkontrolujte své údaje a v dolní části formuláře vyberte **vytvořit** a odešlete žádost o podporu.

## <a name="monitor-a-support-ticket"></a>Monitorování lístku podpory
Po odeslání žádosti o podporu vás tým podpory Azure vás bude kontaktovat. Pokud chcete zkontrolovat stav a podrobnosti žádosti, klikněte na řídicím panelu na **Všechny žádosti o podporu**.

![Zkontrolování stavu](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Další prostředky
Můžete se také připojit k SQL Data Warehouse komunitě na [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/) nebo prostřednictvím [fóra MSDN Azure SQL Data Warehouse](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).

 
