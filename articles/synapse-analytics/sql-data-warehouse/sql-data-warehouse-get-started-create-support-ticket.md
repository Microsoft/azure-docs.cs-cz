---
title: Požádat o zvýšení kvóty a získat podporu
description: Jak vytvořit žádost o podporu na webu Azure Portal pro Azure Synapse Analytics. Požadavek na zvýšení kvóty nebo získání podpory řešení problémů.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: eaa6ceded9893b278b28d517cbddb303f8469e10
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350898"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Žádost o zvýšení kvóty a získání podpory pro Azure Synapse Analytics

Tento článek popisuje, jak odeslat lístek podpory na webu Azure Portal pro Azure Synapse Analytics. Tento proces umožňuje požádat o zvýšení kvóty nebo odeslat žádost o technickou podporu pro tým technické podpory.

## <a name="create-a-support-ticket"></a>Vytvoření lístku podpory

Pomocí následujících kroků vytvořte novou žádost o podporu z portálu Azure pro Azure Synapse Analytics.

1. V nabídce [Portál Azure](https://portal.azure.com) vyberte **Nápověda + podpora**.

   ![Odkaz Nápověda + podpora](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. V **nápovědě + podpoře**vyberte Nový **požadavek na podporu**.

    ![Vytvoření nové žádosti o podporu](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Zkontrolujte [plán podpory Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).

   * **Podpora k fakturaci, správě kvót a předplatného** je k dispozici na všech úrovních podpory.
   * **Podpora break-fix** je poskytována prostřednictvím [podpory Developer](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)nebo [Premier.](https://azure.microsoft.com/support/plans/premier/) Problémy vyžadující opravu jsou problémy, které mají zákazníci při používání Azure a u kterých se dá předpokládat, že je způsobila společnosti Microsoft.
   * V rámci podpory na úrovni [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) a [Premier](https://azure.microsoft.com/support/plans/premier/) jsou k dispozici služby **mentorování vývojářů** a **poradenské služby**.

   Pokud máte plán podpory Premier, můžete také nahlásit problémy azure synapse analytics na [online portálu Microsoft Premier](https://premier.microsoft.com/). Další informace o různých [plánech](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) podpory, včetně oboru, doby odezvy, cen atd.  Nejčastější dotazy týkající se podpory Azure najdete v [tématu Nejčastější dotazy k podpoře Azure](https://azure.microsoft.com/support/faq/).

1. V **yberte pro typ problému**příslušný typ problému. Problémy s opravami přerušení vyberte **možnost Technické**. Pro žádosti o zvýšení kvótvyberte **omezení služeb a předplatného (kvóty).**

   ![Výběr typu problému](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > Tento zbývající část tohoto článku se zaměřuje na žádosti o zvýšení kvóty. Ale můžete také vybrat **technické** zde pro žádosti o podporu řešení problémů. Pokud vyberete **možnost Technické**, budete vyzváni k zadání souhrnu a následné identifikaci typu problému výběrem **možnosti Vybrat typ problému**. Mohou se zobrazit řešení, která vám pomohou problém vyřešit. Pokud prezentovaná řešení problém nevyřeší, vyberte **další:Podrobnosti** a vyplňte formulář pro odeslání lístku podpory.

1. Pro požadavky na zvýšení kvóty vyberte **Azure Synapse Analytics** pro **typ kvóty**. Pak vyberte **Další: Řešení >>**.

   ![Výběr typu kvóty](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. V okně **Podrobnosti** vyberte **Zadat podrobnosti** a zadejte další informace.

   ![Odkaz "Poskytnout podrobnosti"](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Typy požadavků na kvótu

Kliknutím **na tlačítko Poskytnout podrobnosti** se zobrazí okno **Podrobnosti o kvótě,** které umožňuje přidat další informace. Následující části popisují různé požadavky na kvóty, které jsou k dispozici pro Azure Synapse Analytics.

### <a name="data-warehouse-units-dwus-per-server"></a>Jednotky datového skladu (DWUs) na server

Pomocí následujících kroků můžete požádat o zvýšení jednotky DWNa na server.

1. Vyberte **jednotky datového skladu (DTU) pro jeden typ kvóty serveru.**

1. V seznamu **Zdroje** vyberte zdroj, na který chcete cílit.

1. Do pole Požádat o **kvótu** zadejte nový limit DWU, který požadujete.

   ![Podrobnosti o kvótě DWU](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)

### <a name="servers-per-subscription"></a>Servery na předplatné

Pomocí následujících kroků můžete požádat o zvýšení počtu serverů na odběr.

1. Vyberte typ **kvóty Servery podle předplatného.**

1. V seznamu **Umístění** vyberte oblast Azure, kterou chcete použít. Kvóta je na předplatné v každé oblasti.

1. Do pole **Nová kvóta** zadejte požadavek na maximální počet serverů v této oblasti.

   ![Podrobnosti o kvótě serverů](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)

### <a name="enable-subscription-access-to-a-region"></a>Povolení přístupu předplatného do oblasti

Některé typy nabídek nejsou k dispozici ve všech oblastech. Může se zobrazit chyba, například následující:

`This location is not available for subscription`

Pokud vaše předplatné potřebuje přístup v určité oblasti, použijte možnost **Žádost o další kvótu** a požádejte o přístup. V žádosti zadejte nabídku a podrobnosti skladové položky, které chcete povolit pro oblast. Informace o nabídce a možnostech skladové položky najdete v [tématu Azure Synapse Analytics pricing](https://azure.microsoft.com/pricing/details/synapse-analytics/).

![Další podrobnosti o kvótách](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>Odešlete svůj požadavek

Posledním krokem je vyplnění zbývajících podrobností žádosti o podporu databáze SQL. Pak vyberte **Další: Kontrola + vytvoření>>** a po kontrole podrobností žádosti klikněte na **Vytvořit** a odešlete žádost.

## <a name="monitor-a-support-ticket"></a>Monitorování lístku podpory

Po odeslání žádosti o podporu vás bude kontaktovat tým podpory Azure. Pokud chcete zkontrolovat stav a podrobnosti žádosti, klikněte na řídicím panelu na **Všechny žádosti o podporu**.

![Zkontrolování stavu](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Další prostředky

Můžete se také spojit s komunitou Azure Synapse Analytics v [přetečení zásobníku](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) nebo prostřednictvím [fóra MSDN azure SQL data warehouse](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).

