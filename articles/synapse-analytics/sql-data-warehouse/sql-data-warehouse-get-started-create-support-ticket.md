---
title: Zvýšení kvóty požadavků a získání podpory
description: Postup vytvoření žádosti o podporu v Azure Portal pro Azure synapse Analytics Požadavek zvyšuje kvótu nebo získá podporu pro řešení problémů.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: e0788f978fd25356b230a7923def6cbbea3dc305
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835457"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Zvýšení kvóty žádostí a získání podpory pro Azure synapse Analytics

Tento článek popisuje, jak odeslat lístek podpory v Azure Portal pro Azure synapse Analytics. Tento proces vám umožní požádat o zvýšení kvóty nebo odeslat žádost o technickou podporu pro tým technické podpory.

## <a name="create-a-support-ticket"></a>Vytvoření lístku podpory

Pomocí následujících kroků můžete vytvořit novou žádost o podporu z Azure Portal pro Azure synapse Analytics.

1. V nabídce [Azure Portal](https://portal.azure.com) vyberte možnost **help + podpora**.

   ![Odkaz Help + support](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. V **okně pomoc a podpora**vyberte **Nová žádost o podporu**.

    ![Vytvořit novou žádost o podporu](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Zkontrolujte svůj [plán podpory Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).

   * **Podpora k fakturaci, správě kvót a předplatného** je k dispozici na všech úrovních podpory.
   * Podpora **přerušení oprav** je poskytována prostřednictvím [podpory Developer](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)nebo [Premier](https://azure.microsoft.com/support/plans/premier/) . Problémy vyžadující opravu jsou problémy, které mají zákazníci při používání Azure a u kterých se dá předpokládat, že je způsobila společnosti Microsoft.
   * V rámci podpory na úrovni [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) a [Premier](https://azure.microsoft.com/support/plans/premier/) jsou k dispozici služby **mentorování vývojářů** a **poradenské služby**.

   Pokud máte plán podpory Premier Support, můžete také ohlásit problémy Azure synapse Analytics na [portálu Microsoft Premier Online](https://premier.microsoft.com/). Další informace o různých plánech podpory, včetně rozsahu, doby odezvy, cen atd. najdete v tématu věnovaném [plánům podpory Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) .  Nejčastější dotazy týkající se podpory Azure najdete v [nejčastějších](https://azure.microsoft.com/support/faq/)dotazech k podpoře Azure.

1. Jako **typ problému**vyberte příslušný typ problému. V případě problémů s opravou chyby vyberte možnost **Technical**. U žádostí o zvýšení kvóty vyberte **omezení služby a předplatné (kvóty)**.

   ![Vyberte typ problému.](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > Tento zbytek tohoto článku zaměřen o požadavcích na zvýšení kvóty. V případě žádostí o podporu pro řešení problémů ale můžete také vybrat **technickou** podporu. Pokud vyberete možnost **Technical**, budete požádáni o zadání souhrnu a poté určení typu problému výběrem možnosti **Vybrat typ problému**. Můžou se zobrazit řešení, která vám pomůžou problém vyřešit. Pokud řešení uvedená řešení nevyřeší váš problém, vyberte **Další: podrobnosti** a vyplňte formulář pro odeslání lístku podpory.

1. V případě požadavků na zvýšení kvóty vyberte **Azure synapse Analytics** pro **typ kvóty**. Pak vyberte **Další: řešení >>**.

   ![Vyberte typ kvóty.](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. V okně **podrobností** vyberte **zadat podrobnosti** a zadejte další informace.

   ![Odkaz "poskytnout podrobnosti"](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Typy žádostí o kvótu

Kliknutím na Zobrazit **Podrobnosti** zobrazíte okno **Podrobnosti o kvótě** , které vám umožní přidat další informace. V následujících částech najdete popis různých požadavků na kvóty dostupných pro Azure synapse Analytics.

### <a name="data-warehouse-units-dwus-per-server"></a>Jednotky datového skladu (DWU) na server

Pomocí následujících kroků můžete požádat o zvýšení DWU na server.

1. Vyberte typ kvóty **jednotky datového skladu (DTU) na jeden server** .

1. V seznamu **prostředků** vyberte prostředek, který chcete cílit.

1. Do pole **kvóta žádosti** zadejte nový DWU limit, který požadujete.

   ![Podrobnosti o kvótě DWU](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)

### <a name="servers-per-subscription"></a>Servery na předplatné

Pomocí následujících kroků můžete požádat o zvýšení počtu serverů na předplatné.

1. Vyberte typ kvóty **servery podle předplatného** .

1. V seznamu **umístění** vyberte oblast Azure, kterou chcete použít. Kvóta je vázaná na předplatné v každé oblasti.

1. Do pole **Nová kvóta** zadejte žádost o maximální počet serverů v této oblasti.

   ![Podrobnosti o kvótě serverů](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)

### <a name="enable-subscription-access-to-a-region"></a>Povolit přístup k předplatnému pro oblast

Některé typy nabídek nejsou k dispozici v každé oblasti. Může se zobrazit chyba, například následující:

`This location is not available for subscription`

Pokud vaše předplatné potřebuje přístup v konkrétní oblasti, požádejte prosím o přístup **jinou možnost žádosti o kvótu** . V žádosti zadejte podrobnosti nabídky a skladové položky, které chcete pro oblast povolit. Pokud chcete prozkoumat možnosti nabídky a SKU, přečtěte si téma [ceny Azure synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

![Podrobnosti o jiné kvótě](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>Odeslání žádosti

Posledním krokem je vyplnit zbývající podrobnosti žádosti o podporu SQL Database. Pak vyberte **Další: Zkontrolovat a vytvořit >>** a po zkontrolování podrobností o žádosti kliknutím na **Vytvořit** žádost odešlete.

## <a name="monitor-a-support-ticket"></a>Monitorování lístku podpory

Po odeslání žádosti o podporu vás tým podpory Azure vás bude kontaktovat. Pokud chcete zkontrolovat stav a podrobnosti žádosti, klikněte na řídicím panelu na **Všechny žádosti o podporu**.

![Zkontrolování stavu](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Další prostředky

Můžete se také připojit k komunitě Azure synapse Analytics na [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) nebo na [stránce s dotazem Microsoft Q&na Azure SQL Data Warehouse](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html).

