---
title: Monitorování zpráv B2B a nastavení protokolování – Azure Logic Apps | Dokumentace Microsoftu
description: Sledování AS2, X 12 a EDIFACT zprávy. Nastavení protokolování diagnostiky pro účtu pro integraci v Azure Logic Apps.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.date: 07/21/2017
ms.openlocfilehash: 63aa455851633d1e49fd1b26861aaac8a670ef15
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49404780"
---
# <a name="monitor-b2b-messages-and-set-up-logging-for-integration-accounts-in-azure-logic-apps"></a>Monitorování zpráv B2B a nastavení protokolování pro účty pro integraci v Azure Logic Apps

Po nastavení komunikace B2B mezi dvěma obchodních procesů nebo aplikací prostřednictvím účtu pro integraci, tyto entity můžou vyměňovat zprávy mezi sebou. K potvrzení této komunikace funguje podle očekávání, můžete nastavit pro AS2, X12, sledování a zprávy EDIFACT, spolu s protokolování diagnostiky pro váš účet pro integraci prostřednictvím [Azure Log Analytics](../log-analytics/log-analytics-overview.md) služby. Tato služba monitoruje cloudové a místní prostředí, což pomáhá zachovat jejich dostupnost a výkon a taky shromažďuje podrobnosti modulu CLR a události pro rozsáhlejší ladění. Můžete také [pomocí diagnostických dat s jinými službami](#extend-diagnostic-data), jako je Azure Storage a Azure Event Hubs.

## <a name="requirements"></a>Požadavky

* Aplikace logiky, která je nastavená pomocí diagnostického protokolování. Přečtěte si [jak nastavit protokolování pro tuto aplikaci logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

  > [!NOTE]
  > Po splnění tohoto požadavku, měli byste mít pracovní prostor v Log Analytics. Při nastavování protokolování pro váš účet pro integraci, měli byste použít stejný pracovní prostor Log Analytics. Pokud nemáte pracovní prostor Log Analytics, přečtěte si [jak vytvořit pracovní prostor Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md).

* Integrace účtu, který je propojený s vaší aplikací logiky. Přečtěte si [postup vytvoření účtu pro integraci s odkazem na aplikaci logiky](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging-for-your-integration-account"></a>Zapnout diagnostiku protokolování pro váš účet integrace

Můžete zapnout protokolování buď přímo z účtu pro integraci nebo [prostřednictvím služby Azure Monitor](#azure-monitor-service). Platforma Azure Monitor poskytuje základní monitorování s využitím dat na úrovni infrastruktury. Další informace o [Azure Monitor](../azure-monitor/overview.md).

### <a name="turn-on-diagnostics-logging-directly-from-your-integration-account"></a>Zapnout diagnostiku protokolování přímo ze svého účtu integrace

1. V [webu Azure portal](https://portal.azure.com)vyhledejte a vyberte svůj účet integrace. V části **monitorování**, zvolte **diagnostické protokoly** jak je znázorněno zde:

   ![Vyhledejte a vyberte váš účet pro integraci, zvolte "Diagnostické protokoly"](media/logic-apps-monitor-b2b-message/integration-account-diagnostics.png)

2. Po vybrání účtu pro integraci následující hodnoty jsou automaticky vybrané. Pokud tyto hodnoty jsou správné, zvolte **zapnout diagnostiku**. V opačném případě vyberte hodnoty, které chcete, aby:

   1. V části **předplatné**, vyberte předplatné Azure, který používáte k vašemu účtu integrace.
   2. V části **skupiny prostředků**, vyberte skupinu prostředků, který používáte k vašemu účtu integrace.
   3. V části **typ prostředku**vyberte **účty pro integraci**. 
   4. V části **prostředků**, vyberte svůj účet integrace. 
   5. Zvolte **zapnout diagnostiku**.

   ![Nastavení diagnostiky pro váš účet integrace](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. V části **nastavení diagnostiky**a potom **stav**, zvolte **na**.

   ![Zapnout diagnostiku Azure](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Teď vyberte pracovní prostor Log Analytics a data se mají použít pro protokolování, jak je znázorněno:

   1. Vyberte **odesílat do Log Analytics**. 
   2. V části **Log Analytics**, zvolte **konfigurovat**. 
   3. V části **pracovních prostorů OMS**, vyberte pracovní prostor Log Analytics pro účely protokolování. 
   > [!NOTE]
   > Pracovní prostory OMS jsou dnes označovány jako pracovní prostory Log Analytics. 
   4. V části **protokolu**, vyberte **IntegrationAccountTrackingEvents** kategorie.
   5. Zvolte **Uložit**.

   ![Nastavení Log Analytics, takže budete posílat diagnostická data do protokolu](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. Nyní [nastavit sledování zpráv B2B v Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-diagnostics-logging-through-azure-monitor"></a>Zapnutí protokolování diagnostiky v nástroji Azure Monitor

1. V [webu Azure portal](https://portal.azure.com), v hlavní nabídce Azure zvolte **monitorování**, **diagnostické protokoly**. Pak vyberte svůj účet integrace, jak je znázorněno zde:

   ![Tlačítko "Sledovat", "Diagnostické protokoly", vyberte svůj účet integrace](media/logic-apps-monitor-b2b-message/monitor-service-diagnostics-logs.png)

2. Po vybrání účtu pro integraci následující hodnoty jsou automaticky vybrané. Pokud tyto hodnoty jsou správné, zvolte **zapnout diagnostiku**. V opačném případě vyberte hodnoty, které chcete, aby:

   1. V části **předplatné**, vyberte předplatné Azure, který používáte k vašemu účtu integrace.
   2. V části **skupiny prostředků**, vyberte skupinu prostředků, který používáte k vašemu účtu integrace.
   3. V části **typ prostředku**vyberte **účty pro integraci**.
   4. V části **prostředků**, vyberte svůj účet integrace.
   5. Zvolte **zapnout diagnostiku**.

   ![Nastavení diagnostiky pro váš účet integrace](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. V části **nastavení diagnostiky**, zvolte **na**.

   ![Zapnout diagnostiku Azure](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Teď vyberte Log Analytics pracovní prostor a událostí kategorie pro protokolování, jak je znázorněno:

   1. Vyberte **odesílat do Log Analytics**. 
   2. V části **Log Analytics**, zvolte **konfigurovat**. 
   3. V části **pracovních prostorů OMS**, vyberte pracovní prostor Log Analytics pro účely protokolování.
   > [!NOTE]
   > Pracovní prostory OMS jsou dnes označovány jako pracovní prostory Log Analytics.
   4. V části **protokolu**, vyberte **IntegrationAccountTrackingEvents** kategorie.
   5. Jakmile budete hotoví, vyberte **Uložit**.

   ![Nastavení Log Analytics, takže budete posílat diagnostická data do protokolu](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. Nyní [nastavit sledování zpráv B2B v Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Rozšíření jak a kde použít diagnostických dat s jinými službami

Spolu s Azure Log Analytics můžete rozšířit použití diagnostických dat vaší aplikace logiky s ostatními službami Azure, například: 

* [Archiv, který protokoly diagnostiky Azure ve službě Azure Storage](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Stream protokoly diagnostiky Azure do služby Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

Můžete získat v reálném čase sledování pomocí telemetrie a analýz z jiných služeb, jako jsou [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) a [Power BI](../log-analytics/log-analytics-powerbi.md). Příklad:

* [Stream dat ze služby Event Hubs do služby Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analyzujte data streamovaná službou Stream Analytics a vytvořit řídicí panel analýzy v reálném čase v Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Na základě možností, které chcete nastavit, ujistěte se, že jste první [vytvoření účtu služby Azure storage](../storage/common/storage-create-storage-account.md) nebo [vytvoření centra událostí Azure](../event-hubs/event-hubs-create.md). Vyberte požadované možnosti, pro které chcete posílat diagnostická data:

![Odesílání dat do služby Azure storage účet nebo event hub](./media/logic-apps-monitor-b2b-message/storage-account-event-hubs.png)

> [!NOTE]
> Období uchovávání platí jenom v případě, že budete chtít používat účet úložiště.

## <a name="supported-tracking-schemas"></a>Podporované schémata sledování

Azure podporuje tyto typy schémat, které mají pevná schémata kromě vlastní typ sledování.

* [Schéma sledování AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schéma sledování X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Vlastní schémata sledování](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Další postup

* [Sledování zpráv B2B v Azure Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "zprávy sledování B2B ve službě Azure Log Analytics")
* [Další informace o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "přečtěte si víc o Enterprise Integration Pack")

