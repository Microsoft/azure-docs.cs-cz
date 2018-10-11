---
title: Vytváření plánovaných úloh pomocí služby Azure Scheduler – Azure Portal | Microsoft Docs
description: Zjistěte, jak vytvořit, naplánovat a spustit první automatizovanou úlohu pomocí služby Azure Scheduler na webu Azure Portal.
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: e69542ec-d10f-4f17-9b7a-2ee441ee7d68
ms.topic: hero-article
ms.date: 09/17/2018
ms.openlocfilehash: f1f7e67fbd5d8a9ebfae03c00eb0de36e86d9a97
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46949583"
---
# <a name="create-and-schedule-your-first-job-with-azure-scheduler---azure-portal"></a>Vytvoření a naplánování první úlohy pomocí služby Azure Scheduler – Azure Portal

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje službu Azure Scheduler, která se vyřazuje z provozu. K plánování úloh [místo ní zkuste použít Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

V tomto kurzu se dozvíte, jak snadné je vytvořit a naplánovat úlohu a následně ji monitorovat a spravovat. 

Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>.

## <a name="create-job"></a>Vytvoření úlohy

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).  

1. V hlavní nabídce Azure vyberte **Vytvořit prostředek**. Do vyhledávacího pole zadejte scheduler. V seznamu výsledků vyberte **Scheduler** a pak zvolte **Vytvořit**.

   ![Vytvoření prostředku služby Scheduler](./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png)

   Teď vytvořte úlohu, která odešle požadavek GET na tuto adresu URL: `http://www.microsoft.com/` 

1. V části **Úloha Scheduleru** zadejte tyto informace:

   | Vlastnost | Příklad hodnoty | Popis |
   |----------|---------------|-------------| 
   | **Název** | getMicrosoft | Název vaší úlohy | 
   | **Kolekce úloh** | <*název_kolekce_úloh*> | Vytvořte kolekci úloh nebo vyberte existující kolekci. | 
   | **Předplatné** | <*název_předplatného_Azure*> | Název vašeho předplatného Azure | 
   |||| 

1. Vyberte **Nastavení akce – Konfigurace**, zadejte tyto informace a jakmile budete hotovi, vyberte **OK**:

   | Vlastnost | Příklad hodnoty | Popis |
   |----------|---------------|-------------| 
   | **Akce** | **Http** | Typ akce, která se má spustit | 
   | **Metoda** | **Get** | Metoda, která se má zavolat | 
   | **Adresa URL** | **http://www.microsoft.com** | Cílová adresa URL | 
   |||| 
   
   ![Definice úlohy](./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png)

1. Vyberte **Plán – Konfigurace**, definujte plán a jakmile budete hotovi, vyberte **OK**:

   Přestože můžete vytvořit jednorázovou úlohu, v tomto příkladu se nastaví plán opakování.

   | Vlastnost | Příklad hodnoty | Popis |
   |----------|---------------|-------------| 
   | **Opakování** | **Opakující se** | Jednorázová, nebo opakující se úloha | 
   | **Zahájení** | <*dnešní_datum*> | Datum zahájení úlohy | 
   | **Frekvence opakování** | **1 hodina** | Interval a frekvence opakování | 
   | **Ukončení** | **Ukončit do** dvou dnů po dnešním datu | Datum ukončení úlohy | 
   | **Posun oproti času UTC** | **UTC + 08:00** | Časový rozdíl mezi časem UTC (Coordinated Universal Time) a časem ve vašem umístění | 
   |||| 

   ![Definice plánu](./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png)

1. Jakmile budete připraveni, zvolte **Vytvořit**.

   Po vytvoření úlohy ji Azure nasadí a úloha se zobrazí na řídicím panelu Azure. 

1. Když Azure zobrazí oznámení o úspěšném nasazení, zvolte **Připnout na řídicí panel**. Jinak zvolte ikonu **Oznámení** (zvonek) na panelu nástrojů Azure a pak zvolte **Připnout na řídicí panel**.

## <a name="monitor-and-manage-jobs"></a>Monitorování a správa úloh

Pokud chcete zkontrolovat, monitorovat a spravovat svou úlohu, vyberte ji na řídicím panelu Azure. V části **Nastavení** je několik oblastí, které můžete pro svou úlohu zkontrolovat a spravovat:

![Nastavení úlohy](./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png)

Další informace o těchto oblastech získáte výběrem příslušné oblasti:

* [**Vlastnosti**](#properties)
* [**Nastavení akce**](#action-settings)
* [**Plán**](#schedule)
* [**Historie**](#history)
* [**Uživatelé**](#users)

<a name="properties"></a>

### <a name="properties"></a>Vlastnosti

Pokud chcete zobrazit vlastnosti jen pro čtení, které popisují metadata správy pro vaši úlohu, vyberte **Vlastnosti**.

![Zobrazení vlastností úlohy](./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png)

<a name="action-settings"></a>

### <a name="action-settings"></a>Nastavení akce

Pokud chcete změnit upřesňující nastavení vaší úlohy, vyberte **Nastavení akce**. 

![Kontrola nastavení akce](./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png)

| Typ akce | Popis | 
|-------------|-------------| 
| Všechny typy | Můžete změnit nastavení **Zásady opakování** a **Akce při chybě**. | 
| Protokoly HTTP a HTTPS | Můžete změnit **Metodu** na libovolnou povolenou metodu. Také můžete přidat, odstranit nebo změnit hlavičky a základní ověřovací údaje. | 
| Fronta úložiště| Můžete změnit účet úložiště, název fronty, token SAS a text. | 
| Service Bus | Můžete změnit obor názvů, cestu k tématu nebo frontě, nastavení ověření, typ přenosu, vlastnosti zprávy a text zprávy. | 
||| 

<a name="schedule"></a>

### <a name="schedule"></a>Plán

Pokud nastavíte plán pomocí průvodce úlohou, můžete tento plán změnit například úpravou data a času zahájení, plánu opakování a data a času ukončení opakujících se úloh.
Můžete také vytvářet [složitější plány a pokročilá opakování](scheduler-advanced-complexity.md).

Pokud chcete změnit zobrazení nebo plán úlohy, vyberte **Plán**:

![Zobrazení plánu úlohy](./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png)

<a name="history"></a>

### <a name="history"></a>Historie

Pokud chcete zobrazit metriky všech spuštění vybrané úlohy, vyberte **Historie**. Tyto metriky poskytují v reálném čase hodnoty vypovídající o stavu vaší úlohy, například stav, počet opakovaných pokusů, počet opakování, čas spuštění a čas ukončení.

![Zobrazení metrik a historie úlohy](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png)

Pokud chcete zobrazit podrobnosti historie jednotlivých spuštění, například úplnou odpověď, v části **Historie** vyberte požadované spuštění. 

![Zobrazení podrobností historie úlohy](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png)

<a name="users"></a>

### <a name="users"></a>Uživatelé

Přístup ke službě Azure Scheduler můžete pro všechny uživatele podrobně spravovat pomocí řízení přístupu na základě role Azure. Informace o nastavení přístupu na základě rolí najdete v tématu [Správa přístupu pomocí řízení přístupu na základě role](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Další kroky

* Informace o [konceptech, terminologii a hierarchii entit](scheduler-concepts-terms.md)
* [Vytváření složitých plánů a pokročilých opakování](scheduler-advanced-complexity.md)
* Informace o [vysoké dostupnosti a spolehlivosti služby Azure Scheduler](scheduler-high-availability-reliability.md)
* Informace o [omezeních, kvótách, výchozích hodnotách a kódech chyb](scheduler-limits-defaults-errors.md)
