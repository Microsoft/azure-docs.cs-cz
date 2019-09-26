---
title: Monitorování zpráv B2B pomocí protokolů Azure Monitor – Azure Logic Apps | Microsoft Docs
description: Sledujte AS2, X12 a EDIFACT zprávy pro účty pro integraci a Azure Logic Apps a nastavte protokolování diagnostiky pomocí protokolů Azure Monitor
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/23/2018
ms.openlocfilehash: a4a7f951d34455f2e333f2c11e30d24efdfd22c1
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261207"
---
# <a name="monitor-b2b-messages-with-azure-monitor-logs-in-azure-logic-apps"></a>Monitorování zpráv B2B pomocí protokolu Azure Monitor v Azure Logic Apps

Po nastavení komunikace B2B mezi obchodními partnery v účtu integrace můžou tyto partneři vzájemně vyměňovat zprávy. Chcete-li ověřit, že tato komunikace funguje očekávaným způsobem, můžete monitorovat zprávy AS2, X12 a EDIFACT a nastavit protokolování diagnostiky pro účet pro integraci pomocí [protokolů Azure monitor](../log-analytics/log-analytics-overview.md). Tato služba monitoruje vaše cloudové a místní prostředí, umožňuje udržovat jejich dostupnost a výkon a shromažďuje podrobnosti a události modulu runtime pro rozsáhlejší ladění. Tato data můžete také použít u jiných služeb, jako je Azure Storage a Azure Event Hubs.

> [!NOTE]
> Tato stránka může mít pořád odkazy na Microsoft Operations Management Suite (OMS), která vychází [z ledna 2019](../azure-monitor/platform/oms-portal-transition.md), ale v případě potřeby nahrazuje tyto kroky v Azure Log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Požadavky

* Aplikace logiky, která je nastavená pomocí diagnostického protokolování. Naučte [se, jak vytvořit aplikaci logiky](quickstart-create-first-logic-app-workflow.md) a [jak nastavit protokolování pro tuto aplikaci logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Po splnění předchozích požadavků budete také potřebovat Log Analytics pracovní prostor, který používáte pro monitorování a sledování komunikace B2B prostřednictvím protokolů Azure Monitor. Pokud nemáte pracovní prostor Log Analytics, přečtěte si, [jak vytvořit pracovní prostor Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

* Účet pro integraci, který je propojený s vaší aplikací logiky. Naučte se [vytvořit účet pro integraci s odkazem na aplikaci logiky](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging"></a>Zapnout protokolování diagnostiky

Protokolování můžete zapnout přímo z účtu pro integraci nebo [prostřednictvím služby Azure monitor](#azure-monitor-service). Azure Monitor poskytuje základní monitorování s využitím dat na úrovni infrastruktury. Přečtěte si další informace o [Azure monitor](../azure-monitor/overview.md).

### <a name="turn-on-logging-from-integration-account"></a>Zapnout protokolování z účtu pro integraci

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte účet pro integraci. V části **monitorování**vyberte **nastavení diagnostiky**.

   ![Vyhledejte a vyberte účet pro integraci, vyberte nastavení diagnostiky.](media/logic-apps-monitor-b2b-message/find-integration-account.png)

1. Nyní vyhledejte a vyberte účet pro integraci. V seznamu filtrovat seznamy vyberte hodnoty, které platí pro váš účet integrace.
Až budete hotovi, vyberte **Přidat nastavení diagnostiky**.

   | Vlastnost | Value | Popis | 
   |----------|-------|-------------|
   | **Předplatné** | <*název_předplatného_Azure*> | Předplatné Azure přidružené k vašemu účtu integrace | 
   | **Skupina prostředků** | <*Azure-resource-group-name*> | Skupina prostředků Azure pro váš účet pro integraci | 
   | **Typ prostředku** | **Účty pro integraci** | Typ prostředku Azure, pro který chcete zapnout protokolování. | 
   | **Prostředek** | <*integration-account-name*> | Název vašeho prostředku Azure, ve kterém chcete zapnout protokolování. | 
   ||||  

   Příklad:

   ![Nastavení diagnostiky pro účet pro integraci](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Zadejte název nového diagnostického nastavení a vyberte svůj pracovní prostor Log Analytics a data, která chcete protokolovat.

   1. Vyberte **odeslat Log Analytics**. 

   1. V části **Log Analytics**vyberte **Konfigurovat**. 

   1. V části **pracovní prostory OMS**vyberte pracovní prostor Log Analytics, který chcete použít pro protokolování. 

      > [!NOTE]
      > Pracovní prostory OMS se nahrazují Log Analyticsmi pracovními prostory. 

   1. V části **protokol**vyberte kategorii **IntegrationAccountTrackingEvents** a klikněte na **Uložit**.

   Příklad: 

   ![Nastavte protokoly Azure Monitor, abyste mohli odesílat diagnostická data do protokolu.](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Teď [v protokolech Azure monitor nastavili sledování zpráv B2B](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-logging-through-azure-monitor"></a>Zapnout protokolování prostřednictvím Azure Monitor

1. V [Azure Portal](https://portal.azure.com)v hlavní nabídce Azure vyberte **monitor**. V části **Nastavení**vyberte **nastavení diagnostiky**. 

   ![Vyberte "monitor" > "nastavení diagnostiky" > účet pro integraci.](media/logic-apps-monitor-b2b-message/monitor-diagnostics-settings.png)

1. Nyní vyhledejte a vyberte účet pro integraci. V seznamu filtrovat seznamy vyberte hodnoty, které platí pro váš účet integrace.
Až budete hotovi, vyberte **Přidat nastavení diagnostiky**.

   | Vlastnost | Value | Popis | 
   |----------|-------|-------------|
   | **Předplatné** | <*název_předplatného_Azure*> | Předplatné Azure přidružené k vašemu účtu integrace | 
   | **Skupina prostředků** | <*Azure-resource-group-name*> | Skupina prostředků Azure pro váš účet pro integraci | 
   | **Typ prostředku** | **Účty pro integraci** | Typ prostředku Azure, pro který chcete zapnout protokolování. | 
   | **Prostředek** | <*integration-account-name*> | Název vašeho prostředku Azure, ve kterém chcete zapnout protokolování. | 
   ||||  

   Příklad:

   ![Nastavení diagnostiky pro účet pro integraci](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Zadejte název nového diagnostického nastavení a vyberte svůj pracovní prostor Log Analytics a data, která chcete protokolovat.

   1. Vyberte **odeslat Log Analytics**. 

   1. V části **Log Analytics**vyberte **Konfigurovat**. 

   1. V části **pracovní prostory OMS**vyberte pracovní prostor Log Analytics, který chcete použít pro protokolování. 

      > [!NOTE]
      > Pracovní prostory OMS se nahrazují Log Analyticsmi pracovními prostory. 

   1. V části **protokol**vyberte kategorii **IntegrationAccountTrackingEvents** a klikněte na **Uložit**.

   Příklad: 

   ![Nastavte protokoly Azure Monitor, abyste mohli odesílat diagnostická data do protokolu.](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Teď [v protokolech Azure monitor nastavili sledování zpráv B2B](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="use-diagnostic-data-with-other-services"></a>Použití diagnostických dat s jinými službami

Společně s protokoly Azure Monitor můžete pomocí diagnostických dat aplikace logiky navzájem využít jiné služby Azure, například: 

* [Archivace Azure Diagnostics protokolů v Azure Storage](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Streamování protokolů Azure Diagnostics do Azure Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md) 

Sledování v reálném čase můžete získat pomocí telemetrie a analýz z jiných služeb, jako je [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) a [Power BI](../azure-monitor/platform/powerbi.md). Příklad:

* [Streamování dat z Event Hubs do Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analyzujte streamovaná data pomocí Stream Analytics a vytvořte řídicí panel analýzy v reálném čase v Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Na základě možností, které chcete nastavit, se ujistěte, že jste nejdřív [vytvořili účet Azure Storage](../storage/common/storage-create-storage-account.md) , nebo [vytvoříte centrum událostí Azure](../event-hubs/event-hubs-create.md). Pak můžete vybrat cílové umístění, kam chcete odeslat diagnostická data.
Doba uchování platí jenom v případě, že se rozhodnete použít účet úložiště.

![Odeslání dat do účtu služby Azure Storage nebo centra událostí](./media/logic-apps-monitor-b2b-message/diagnostics-storage-event-hub-log-analytics.png)

## <a name="supported-tracking-schemas"></a>Podporovaná schémata sledování

Azure podporuje tyto typy schématu sledování, které mají pevná schémata s výjimkou vlastního typu.

* [Schéma sledování AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schéma sledování X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Vlastní schémata sledování](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Další kroky

* [Sledování zpráv B2B v protokolech Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "Sledování zpráv B2B v protokolech Azure Monitor")
* [Další informace o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informace o Enterprise Integration Pack")

