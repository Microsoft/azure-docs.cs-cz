---
title: Monitorování zpráv B2B pomocí Log Analytics – Azure Logic Apps | Dokumentace Microsoftu
description: Sledování AS2, X 12 a EDIFACT a zprávy pro účty pro integraci Azure Logic Apps a nastavit protokolování diagnostiky pomocí Azure Log Analytics
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/23/2018
ms.openlocfilehash: d7a32abe52e6f1109becc18c84b19d0ccd69719c
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53385461"
---
# <a name="monitor-b2b-messages-with-azure-log-analytics-in-azure-logic-apps"></a>Monitorování zpráv B2B s Azure Log Analytics v Azure Logic Apps

Po nastavení komunikace B2B mezi obchodními partnery v účtu integrace těchto partnerů si mohou vyměňovat zprávy mezi sebou. Chcete-li zkontrolovat, že tato komunikace funguje očekávaným způsobem, AS2, X12, můžete monitorovat a zprávy EDIFACT a nastavit Diagnostika protokolování pro váš účet integrace s [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Tato služba monitoruje cloudové a místní prostředí, umožňují zachovat jejich dostupnost a výkon a shromažďuje podrobnosti modulu CLR a události pro rozsáhlejší ladění. Můžete také [pomocí těchto dat s jinými službami](#extend-diagnostic-data) jako je například Azure Storage a Azure Event Hubs.

> [!NOTE]
> Tato stránka pravděpodobně stále odkazy k Microsoft Operations Management Suite (OMS), což je [vyřazení z provozu v lednu 2019](../azure-monitor/platform/oms-portal-transition.md), ale nahradí tyto kroky pomocí Azure Log Analytics, kde je to možné. 

## <a name="prerequisites"></a>Požadavky

* Aplikace logiky, která je nastavená pomocí diagnostického protokolování. Přečtěte si [jak vytvořit aplikaci logiky](quickstart-create-first-logic-app-workflow.md) a [jak nastavit protokolování pro tuto aplikaci logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Po předchozí požadavky splňujete, budete potřebovat pracovní prostor Log Analytics, který používáte pro monitorování a sledování B2B komunikace prostřednictvím Log Analytics. Pokud nemáte pracovní prostor Log Analytics, přečtěte si [jak vytvořit pracovní prostor Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

* Integrace účtu, který je propojený s vaší aplikací logiky. Přečtěte si [postup vytvoření účtu pro integraci s odkazem na aplikaci logiky](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging"></a>Zapnutí protokolování diagnostiky

Můžete zapnout protokolování buď přímo z účtu pro integraci nebo [prostřednictvím služby Azure Monitor](#azure-monitor-service). Platforma Azure Monitor poskytuje základní monitorování s využitím dat na úrovni infrastruktury. Další informace o [Azure Monitor](../azure-monitor/overview.md).

### <a name="turn-on-logging-from-integration-account"></a>Zapnutí protokolování z účtu pro integraci

1. V [webu Azure portal](https://portal.azure.com)vyhledejte a vyberte svůj účet integrace. V části **monitorování**vyberte **nastavení diagnostiky**.

   ![Vyhledejte a vyberte svůj účet integrace, vyberte "Nastavení diagnostiky"](media/logic-apps-monitor-b2b-message/find-integration-account.png)

1. Nyní vyhledejte a vyberte svůj účet integrace. V seznamech filtr vyberte hodnoty, které se vztahují k vašemu účtu integrace.
Jakmile budete hotovi, zvolte **přidejte nastavení diagnostiky**.

   | Vlastnost | Hodnota | Popis | 
   |----------|-------|-------------|
   | **Předplatné** | <*název_předplatného_Azure*> | Předplatné Azure, přidružené k účtu pro integraci | 
   | **Skupina prostředků** | <*Azure-resource-group-name*> | Skupina prostředků Azure pro váš účet integrace | 
   | **Typ prostředku** | **Účty pro integraci** | Typ pro prostředky Azure, ve které chcete zapnout protokolování | 
   | **Prostředek** | <*název účtu integrace*> | Název pro váš prostředek Azure, ve které chcete zapnout protokolování | 
   ||||  

   Příklad:

   ![Nastavení diagnostiky pro váš účet integrace](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Zadejte název vaší nové nastavení diagnostiky a vyberte váš pracovní prostor Log Analytics a data, která chcete se přihlásit.

   1. Vyberte **odesílat do Log Analytics**. 

   1. V části **Log Analytics**vyberte **konfigurovat**. 

   1. V části **pracovních prostorů OMS**, vyberte pracovní prostor Log Analytics, kterou chcete použít pro přihlášení. 

      > [!NOTE]
      > Pracovní prostory OMS se nahrazují pracovních prostorů Log Analytics. 

   1. V části **protokolu**, vyberte **IntegrationAccountTrackingEvents** kategorie a zvolte **Uložit**.

   Příklad: 

   ![Nastavení Log Analytics, takže budete posílat diagnostická data do protokolu](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Nyní [nastavit sledování zpráv B2B v Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-logging-through-azure-monitor"></a>Zapnutí protokolování v nástroji Azure Monitor

1. V [webu Azure portal](https://portal.azure.com), v hlavní nabídce Azure zvolte **monitorování**. V části **nastavení**vyberte **nastavení diagnostiky**. 

   ![Vyberte "Monitorování" > "Nastavení diagnostiky" > účtu pro integraci](media/logic-apps-monitor-b2b-message/monitor-diagnostics-settings.png)

1. Nyní vyhledejte a vyberte svůj účet integrace. V seznamech filtr vyberte hodnoty, které se vztahují k vašemu účtu integrace.
Jakmile budete hotovi, zvolte **přidejte nastavení diagnostiky**.

   | Vlastnost | Hodnota | Popis | 
   |----------|-------|-------------|
   | **Předplatné** | <*název_předplatného_Azure*> | Předplatné Azure, přidružené k účtu pro integraci | 
   | **Skupina prostředků** | <*Azure-resource-group-name*> | Skupina prostředků Azure pro váš účet integrace | 
   | **Typ prostředku** | **Účty pro integraci** | Typ pro prostředky Azure, ve které chcete zapnout protokolování | 
   | **Prostředek** | <*název účtu integrace*> | Název pro váš prostředek Azure, ve které chcete zapnout protokolování | 
   ||||  

   Příklad:

   ![Nastavení diagnostiky pro váš účet integrace](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Zadejte název vaší nové nastavení diagnostiky a vyberte váš pracovní prostor Log Analytics a data, která chcete se přihlásit.

   1. Vyberte **odesílat do Log Analytics**. 

   1. V části **Log Analytics**vyberte **konfigurovat**. 

   1. V části **pracovních prostorů OMS**, vyberte pracovní prostor Log Analytics, kterou chcete použít pro přihlášení. 

      > [!NOTE]
      > Pracovní prostory OMS se nahrazují pracovních prostorů Log Analytics. 

   1. V části **protokolu**, vyberte **IntegrationAccountTrackingEvents** kategorie a zvolte **Uložit**.

   Příklad: 

   ![Nastavení Log Analytics, takže budete posílat diagnostická data do protokolu](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Nyní [nastavit sledování zpráv B2B v Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="use-diagnostic-data-with-other-services"></a>Použití diagnostických dat s jinými službami

Spolu s Azure Log Analytics můžete rozšířit použití diagnostických dat vaší aplikace logiky s ostatními službami Azure, například: 

* [Archiv, který protokoly diagnostiky Azure ve službě Azure Storage](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Stream protokoly diagnostiky Azure do služby Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

Můžete získat v reálném čase sledování pomocí telemetrie a analýz z jiných služeb, jako jsou [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) a [Power BI](../azure-monitor/platform/powerbi.md). Příklad:

* [Stream dat ze služby Event Hubs do služby Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analyzujte data streamovaná službou Stream Analytics a vytvořit řídicí panel analýzy v reálném čase v Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Na základě možností chcete sadu, ujistěte se, že jste první [vytvoření účtu služby Azure storage](../storage/common/storage-create-storage-account.md) nebo [vytvoření centra událostí Azure](../event-hubs/event-hubs-create.md). Vyberte cíle, ve které chcete posílat diagnostická data.
Období uchovávání platí jenom v případě, že budete chtít používat účet úložiště.

![Odesílání dat do služby Azure storage účet nebo event hub](./media/logic-apps-monitor-b2b-message/diagnostics-storage-event-hub-log-analytics.png)

## <a name="supported-tracking-schemas"></a>Podporované schémata sledování

Azure podporuje tyto typy schémat, které mají pevná schémata kromě vlastní typ sledování.

* [Schéma sledování AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schéma sledování X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Vlastní schémata sledování](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Další postup

* [Sledování zpráv B2B v Azure Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "zprávy sledování B2B ve službě Azure Log Analytics")
* [Další informace o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "přečtěte si víc o Enterprise Integration Pack")

