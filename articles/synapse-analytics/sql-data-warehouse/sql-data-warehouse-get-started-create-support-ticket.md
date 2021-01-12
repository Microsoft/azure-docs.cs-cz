---
title: Zvýšení kvóty požadavků a získání podpory
description: Postup vytvoření žádosti o podporu v Azure Portal pro Azure synapse Analytics Požadavek zvyšuje kvótu nebo získá podporu pro řešení problémů.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 6042b5be48144ce6730a9bf4f342d7735a262643
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118060"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Zvýšení kvóty žádostí a získání podpory pro Azure synapse Analytics

Tento článek popisuje, jak odeslat lístek podpory v Azure Portal pro Azure synapse Analytics. Tento proces vám umožní požádat o zvýšení kvóty nebo odeslat žádost o technickou podporu pro tým technické podpory.

## <a name="create-a-support-ticket"></a>Vytvoření lístku podpory

Pomocí následujících kroků můžete vytvořit novou žádost o podporu z Azure Portal pro Azure synapse Analytics.

1. V nabídce [Azure Portal](https://portal.azure.com) vyberte možnost **help + podpora**.

   ![Odkaz Help + support](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. V **okně pomoc a podpora** vyberte **Nová žádost o podporu**.

    ![Vytvořit novou žádost o podporu](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Zkontrolujte svůj [plán podpory Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).

   * **Podpora k fakturaci, správě kvót a předplatného** je k dispozici na všech úrovních podpory.
   * Podpora **přerušení oprav** je poskytována prostřednictvím [podpory Developer](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)nebo [Premier](https://azure.microsoft.com/support/plans/premier/) . Problémy vyžadující opravu jsou problémy, které mají zákazníci při používání Azure a u kterých se dá předpokládat, že je způsobila společnosti Microsoft.
   * V rámci podpory na úrovni [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) a [Premier](https://azure.microsoft.com/support/plans/premier/) jsou k dispozici služby **mentorování vývojářů** a **poradenské služby**.

   Pokud máte plán podpory Premier Support, můžete také ohlásit problémy Azure synapse Analytics na [portálu Microsoft Premier Online](https://premier.microsoft.com/). Další informace o různých plánech podpory, včetně rozsahu, doby odezvy, cen atd. najdete v tématu věnovaném [plánům podpory Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) .  Nejčastější dotazy týkající se podpory Azure najdete v [nejčastějších](https://azure.microsoft.com/support/faq/)dotazech k podpoře Azure.

1. Jako **typ problému** vyberte příslušný typ problému. V případě problémů s opravou chyby vyberte možnost **Technical**. U žádostí o zvýšení kvóty vyberte **omezení služby a předplatné (kvóty)**.

   ![Vyberte typ problému.](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > Tento zbytek tohoto článku zaměřen o požadavcích na zvýšení kvóty. V případě žádostí o podporu pro řešení problémů ale můžete také vybrat **technickou** podporu. Pokud vyberete možnost **Technical**, budete požádáni o zadání souhrnu a poté určení typu problému výběrem možnosti **Vybrat typ problému**. Můžou se zobrazit řešení, která vám pomůžou problém vyřešit. Pokud řešení uvedená řešení nevyřeší váš problém, vyberte **Další: podrobnosti** a vyplňte formulář pro odeslání lístku podpory.

1. V případě požadavků na zvýšení kvóty vyberte **Azure synapse Analytics** pro **typ kvóty**. Pak vyberte **Další: řešení >>**.

   ![Vyberte typ kvóty.](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. V okně **podrobností** vyberte **zadat podrobnosti** a zadejte další informace.

   ![Odkaz "poskytnout podrobnosti"](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Typy žádostí o kvótu

Výběrem možnosti **zadat podrobnosti** zobrazíte okno **Podrobnosti kvóty** , které umožňuje přidat další informace. V následujících částech najdete popis různých požadavků na kvóty dostupných pro Azure synapse Analytics.

### <a name="synapse-sql-pool-data-warehouse-units-dwus-per-server"></a>Jednotky datového skladu synapse fondu SQL (DWU) na jeden server

Pomocí následujících kroků můžete požádat o zvýšení DWU na server.

1. Vyberte typ kvóty **synapse fondu SQL DWU na server** .

1. Pomocí rozevíracího seznamu vyberte **prostředek** , na který chcete použít zvýšení kvóty.

1. Do části **kvóta žádosti** zadejte novou kvótu.

1. Vyberte **Uložit a pokračovat**.

   ![Podrobnosti o kvótě DWU](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)


### <a name="servers-per-subscription"></a>Servery na předplatné

Pokud chcete požádat o zvýšení počtu serverů na předplatné, musíte provést následující kroky:

1. Jako typ kvóty vyberte **SQL servery na předplatné** .

1. V seznamu **umístění** vyberte oblast Azure, kterou chcete použít. Kvóta je vázaná na předplatné v každé oblasti.

1. Do pole **kvóta žádosti** zadejte svou žádost o maximální počet serverů v této oblasti.

   ![Podrobnosti o kvótě serverů](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)



1. Vyberte **Uložit a pokračovat**.

Některé typy nabídek nejsou dostupné v každé oblasti. Může se zobrazit následující chyba:

![Chyba přístupu k oblasti](./media/sql-data-warehouse-get-started-create-support-ticket/region-access-error.png)

### <a name="enable-subscription-access-to-a-region"></a>Povolit přístup k předplatnému pro oblast

Pokud chcete povolit přístup k oblasti pro předplatné, musíte provést následující kroky:  

1. Vyberte **synapse fond SQL (datový sklad) přístup k oblasti** typ kvóty přístupu.

1. Vyberte oblast výběrem **umístění** v rozevíracím seznamu.

1. Uveďte požadavek na výkon DWU v části **DWU Required** .

1. Zadejte **Popis obchodních požadavků**. 

1. Vyberte **Uložit a pokračovat**.

![Přístup k oblasti](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-region.png)


### <a name="for-other-quota-requests"></a>Pro jiné žádosti o kvótu

V rozevírací nabídce Typ kvóty vyberte **jinou žádost** o kvótu pro jiné typy žádostí o kvótu:

![Podrobnosti o jiné kvótě](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>Odeslání žádosti

Posledním krokem je vyplnit zbývající podrobnosti žádosti o podporu SQL Database. Pak vyberte **Další: zkontrolovat + vytvořit>>**.

![Kontrola vytvoření podrobností](./media/sql-data-warehouse-get-started-create-support-ticket/review-create-details.png)

Po zkontrolování podrobností žádosti vyberte **vytvořit** a odešlete žádost.

![Vytvořit lístek](./media/sql-data-warehouse-get-started-create-support-ticket/create-ticket.png)

## <a name="monitor-a-support-ticket"></a>Monitorování lístku podpory

Po odeslání žádosti o podporu vás tým podpory Azure vás bude kontaktovat. Pokud chcete zjistit stav žádosti a podrobnosti, vyberte **všechny žádosti o podporu** na řídicím panelu.

![Zkontrolování stavu](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Další prostředky

Můžete se také připojit k komunitě Azure synapse Analytics na [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) nebo prostřednictvím [stránky s otázkou Microsoft Q&pro Azure synapse Analytics](/answers/topics/azure-synapse-analytics.html).