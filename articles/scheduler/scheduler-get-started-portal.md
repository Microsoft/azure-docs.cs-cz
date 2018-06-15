---
title: Začínáme se Schedulerem na webu Azure Portal | Dokumentace Microsoftu
description: Začínáme se Schedulerem na portálu Azure
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: e69542ec-d10f-4f17-9b7a-2ee441ee7d68
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/10/2016
ms.author: deli
ms.openlocfilehash: f03ddb475835b30e9b931b7f057c062b57ac45f3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31413426"
---
# <a name="get-started-with-azure-scheduler-in-azure-portal"></a>Začínáme se Schedulerem na portálu Azure
Ve službě Azure Scheduler je vytváření plánovaných úloh snadné. V tomto kurzu se naučíte, jak vytvořit úlohu. Taky poznáte možnosti Scheduleru pro sledování a správu.

## <a name="create-a-job"></a>Vytvoření úlohy
1. Přihlaste se k [portálu Azure](https://portal.azure.com/).  
2. Klikněte na **+Nový** > v poli pro vyhledávání zadejte *Scheduler* > ve výsledcích vyberte **Scheduler** > klikněte na **Vytvořit**.
   
    ![][marketplace-create]
3. Teď vytvoříme úlohu, která jednoduše vyšle požadavek GET na http://www.microsoft.com/. Na obrazovce **Úloha Scheduleru** zadejte následující informace:
   
   1. **Název:**`getmicrosoft`  
   2. **Předplatné:** Vaše předplatné Azure   
   3. **Kolekce úloh:** Vyberte existující kolekci úloh nebo klikněte na **Vytvořit novou** &gt; zadejte název.
4. Dál klikněte na **Nastavení akce** a definujte tyto hodnoty:
   
   1. **Typ akce:**` HTTP`  
   2. **Metoda:**`GET`  
   3. **Adresa URL:**` http://www.microsoft.com`  
      
      ![][action-settings]
5. Nakonec nastavíme plán. Úloha se může definovat jako jednorázová, ale my vytvoříme plán opakování:
   
   1. **Opakování**: `Recurring`
   2. **Začátek**: Dnešní datum
   3. **Opakovat každý**: `12 Hours`
   4. **Konec**: Dva dny po dnešním datu  
      
      ![][recurrence-schedule]
6. Klikněte na **Vytvořit**

## <a name="manage-and-monitor-jobs"></a>Správa a sledování úloh
Úloha se po vytvoření objeví v hlavním řídicím panelu Azure. Klikněte na úlohu a otevře se nové okno s těmito kartami:

1. Vlastnosti  
2. Nastavení akce  
3. Plán  
4. Historie
5. Uživatelé
   
   ![][job-overview]

### <a name="properties"></a>Vlastnosti
Tyto vlastnosti jsou jen pro čtení a popisují metadata správy pro úlohu Scheduleru.

   ![][job-properties]

### <a name="action-settings"></a>Nastavení akce
Pokud chcete nakonfigurovat úlohu, klikněte na ni na obrazovce **Úlohy**. Pokud jste pokročilá nastavení nenakonfigurovali v průvodci rychlým vytvořením podle svých představ, můžete je konfigurovat tady.

Pro všechny typy akcí můžete změnit zásady opakovaných pokusů a akci při chybě.

Pro akce úloh HTTP a HTTPS můžete změnit metodu na libovolnou povolenou operaci HTTP. Taky můžete přidat, odstranit nebo změnit hlavičky a základní ověřovací údaje.

Pro akce fronty úložiště můžete změnit účet úložiště, název fronty, token SAS a hlavní část.

Pro akce sběrnice můžete změnit obory názvů, cestu k tématu/frontě, nastavení ověření, typ přenosu, vlastnosti zprávy a hlavní část zprávy.

   ![][job-action-settings]

### <a name="schedule"></a>Plán
Pokud vám plán vytvořený v průvodci rychlým vytvořením nevyhovuje, můžete ho tady změnit.

To je příležitost k sestavení [komplexních plánů a pokročilého opakování ve vaší úloze](scheduler-advanced-complexity.md).

Můžete změnit datum a čas zahájení, plán opakování a datum a čas ukončení (pokud se úloha opakuje).

   ![][job-schedule]

### <a name="history"></a>Historie
Na kartě **Historie** jsou zobrazené vybrané metriky pro každé provedení vybrané úlohy v systému. Tyto metriky v reálném čase poskytují hodnoty ohledně kondice vašeho Scheduleru:

1. Status  
2. Podrobnosti  
3. Opakované pokusy
4. Výskyt: 1., 2., 3. atd.
5. Čas zahájení provedení  
6. Čas ukončení provedení
   
   ![][job-history]

Můžete kliknout na běh a zobrazí se **Podrobnosti historie** včetně kompletní odpovědi na každé provedení. Toto dialogové okno vám taky umožní zkopírovat odpověď do schránky.

   ![][job-history-details]

### <a name="users"></a>Uživatelé
Řízení přístupu na základě role ve službě Azure Scheduler umožňuje přesnou správu přístupu. Pokud se chcete naučit používat kartu Uživatelé, přečtěte si témě [Řízení přístupu Azure na základě rolí](../role-based-access-control/role-assignments-portal.md).

## <a name="see-also"></a>Viz také
 [Co je Scheduler?](scheduler-intro.md)

 [Koncepty, terminologie a hierarchie entit Scheduleru](scheduler-concepts-terms.md)

 [Plány a fakturace v Azure Scheduleru](scheduler-plans-billing.md)

 [Sestavení komplexních plánů a pokročilé opakování v Azure Scheduleru](scheduler-advanced-complexity.md)

 [Referenční materiály k rozhraní REST API Scheduleru](https://msdn.microsoft.com/library/mt629143)

 [Rutiny PowerShellu pro Scheduler – referenční informace](scheduler-powershell-reference.md)

 [Vysoká dostupnost a spolehlivost Scheduleru](scheduler-high-availability-reliability.md)

 [Omezení, výchozí hodnoty a kódy chyb Scheduleru](scheduler-limits-defaults-errors.md)

 [Odchozí ověření Scheduleru](scheduler-outbound-authentication.md)

[marketplace-create]: ./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png
[action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png
[recurrence-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png
[job-properties]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png
[job-overview]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png
[job-action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png
[job-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png
[job-history]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png
[job-history-details]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png


[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png
