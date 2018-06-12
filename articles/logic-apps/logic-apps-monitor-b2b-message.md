---
title: Monitorování transakcí B2B a nastavení protokolování - Azure Logic Apps | Microsoft Docs
description: Monitorování pro AS2, X 12 a EDIFACT zprávy, spusťte protokolování diagnostiky pro váš účet integrace
author: padmavc
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 399c7b91949a854f3a152e9a3788d9163c565934
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299313"
---
# <a name="monitor-and-set-up-diagnostics-logging-for-b2b-communication-in-integration-accounts"></a>Sledování a protokolování diagnostiky pro komunikace B2B ve účty pro integraci

Po nastavení komunikace B2B mezi dvěma systémem obchodních procesů nebo aplikací prostřednictvím účtu integrace tyto entity můžou vyměňovat zprávy mezi sebou. Potvrďte tuto komunikaci funguje podle očekávání, můžete nastavit pro AS2, X12, monitorování a EDIFACT zprávy, spolu s protokolování diagnostiky pro váš účet integrace prostřednictvím [Azure Log Analytics](../log-analytics/log-analytics-overview.md) služby. Tato služba monitoruje své cloudové a místní prostředí, což pomáhá udržovat jejich dostupnost a výkon a taky shromažďuje podrobnosti runtime a událostí pro širší ladění. Můžete také [pomocí diagnostických dat s jinými službami](#extend-diagnostic-data), jako jsou služby Azure Storage a Azure Event Hubs.

## <a name="requirements"></a>Požadavky

* Aplikace logiky, který je nastavený s protokolování diagnostiky. Další informace [postup nastavení protokolování pro tuto aplikaci logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

  > [!NOTE]
  > Po splnění tohoto požadavku, měli byste mít pracovního prostoru v analýzy protokolů. Při nastavení protokolování pro váš účet integrace, měli byste použít stejné pracovní prostor analýzy protokolů. Pokud nemáte pracovní prostor analýzy protokolů, přečtěte si [jak vytvořit pracovní prostor analýzy protokolů](../log-analytics/log-analytics-quick-create-workspace.md).

* Integrace účet, který je propojený s svou aplikaci logiky. Další informace [postup vytvoření účtu integrace se zobrazí odkaz na svou aplikaci logiky](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging-for-your-integration-account"></a>Zapněte diagnostiku protokolování pro váš účet integrace

Můžete zapnout protokolování buď přímo z vašeho účtu integrace nebo [pomocí služby Azure monitorování](#azure-monitor-service). Monitorování Azure poskytuje základní monitorování s daty úrovni infrastruktury. Další informace o [Azure monitorování](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md).

### <a name="turn-on-diagnostics-logging-directly-from-your-integration-account"></a>Zapněte diagnostiku protokolování přímo z vašeho účtu integrace

1. V [portál Azure](https://portal.azure.com), najděte a vyberte svůj účet integrace. V části **monitorování**, zvolte **protokolů diagnostiky** jak je vidět tady:

   ![Najít a vyberte svůj účet integrace, zvolte "Diagnostické protokoly"](media/logic-apps-monitor-b2b-message/integration-account-diagnostics.png)

2. Po výběru účtu integrace, automaticky se vyberou následující hodnoty. Pokud tyto hodnoty jsou správné, zvolte **zapněte diagnostiku**. Jinak vyberte možnost hodnoty, které chcete:

   1. V části **předplatné**, vyberte předplatné Azure, který používáte s vaším účtem integrace.
   2. V části **skupiny prostředků**, vyberte skupinu prostředků, který používáte s vaším účtem integrace.
   3. V části **typ prostředku**, vyberte **účty pro integraci**. 
   4. V části **prostředků**, vyberte svůj účet integrace. 
   5. Zvolte **zapněte diagnostiku**.

   ![Nastavení diagnostiky pro váš účet integrace](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. V části **nastavení diagnostiky**a potom **stav**, zvolte **na**.

   ![Zapněte diagnostiku Azure](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Nyní vyberte pracovní prostor analýzy protokolů a data se mají použít pro protokolování, jak je znázorněno:

   1. Vyberte **odeslat k analýze protokolů**. 
   2. V části **analýzy protokolů**, zvolte **konfigurace**. 
   3. V části **pracovních prostorů OMS**, vyberte pracovní prostor analýzy protokolů pro protokolování.
   4. V části **protokolu**, vyberte **IntegrationAccountTrackingEvents** kategorie.
   5. Zvolte **Uložit**.

   ![Nastavení analýzy protokolů tak může odesílat data diagnostiky protokolu](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. Nyní [nastavení sledování zpráv B2B v analýzy protokolů](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-diagnostics-logging-through-azure-monitor"></a>Zapnutí protokolování diagnostiky prostřednictvím Azure monitorování

1. V [portál Azure](https://portal.azure.com), v hlavní nabídce Azure, zvolte **monitorování**, **protokolů diagnostiky**. Potom vyberte svůj účet integrace, jak je vidět tady:

   ![Zvolte "Sledovat", "Diagnostické protokoly", vyberte svůj účet integrace](media/logic-apps-monitor-b2b-message/monitor-service-diagnostics-logs.png)

2. Po výběru účtu integrace, automaticky se vyberou následující hodnoty. Pokud tyto hodnoty jsou správné, zvolte **zapněte diagnostiku**. Jinak vyberte možnost hodnoty, které chcete:

   1. V části **předplatné**, vyberte předplatné Azure, který používáte s vaším účtem integrace.
   2. V části **skupiny prostředků**, vyberte skupinu prostředků, který používáte s vaším účtem integrace.
   3. V části **typ prostředku**, vyberte **účty pro integraci**.
   4. V části **prostředků**, vyberte svůj účet integrace.
   5. Zvolte **zapněte diagnostiku**.

   ![Nastavení diagnostiky pro váš účet integrace](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. V části **nastavení diagnostiky**, zvolte **na**.

   ![Zapněte diagnostiku Azure](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Nyní vyberte kategorii pracovní prostor a událostí analýzy protokolů pro protokolování, jak je znázorněno:

   1. Vyberte **odeslat k analýze protokolů**. 
   2. V části **analýzy protokolů**, zvolte **konfigurace**. 
   3. V části **pracovních prostorů OMS**, vyberte pracovní prostor analýzy protokolů pro protokolování.
   4. V části **protokolu**, vyberte **IntegrationAccountTrackingEvents** kategorie.
   5. Jakmile budete hotoví, vyberte **Uložit**.

   ![Nastavení analýzy protokolů tak může odesílat data diagnostiky protokolu](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. Nyní [nastavení sledování zpráv B2B v analýzy protokolů](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Rozšíření jak a kde používáte diagnostických dat s jinými službami

Společně s Azure Log Analytics můžete rozšířit použití aplikace logiky diagnostických dat s jinými službami Azure, například: 

* [Archiv, který Azure Diagnostics protokolů v úložišti Azure](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Datový proud protokolů Azure Diagnostics do služby Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

Můžete pak monitorování pomocí telemetrie a analýzy z jiných služeb, jako například get v reálném čase [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) a [Power BI](../log-analytics/log-analytics-powerbi.md). Příklad:

* [Datový proud dat ze služby Event Hubs do služby Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analýza dat pomocí služby Stream Analytics a vytvořit řídicí panel analýzu v reálném čase v Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

V závislosti na možnosti, které chcete nastavit, ujistěte se, že jste první [vytvořit účet úložiště Azure](../storage/common/storage-create-storage-account.md) nebo [vytváření centra událostí Azure](../event-hubs/event-hubs-create.md). Vyberte požadované možnosti pro které chcete poslat diagnostická data:

![Odesílání dat do úložiště Azure účet nebo události rozbočovače](./media/logic-apps-monitor-b2b-message/storage-account-event-hubs.png)

> [!NOTE]
> Období uchovávání se projeví pouze v případě, že ji rozhodnete použít účet úložiště.

## <a name="supported-tracking-schemas"></a>Podporované sledování schémata

Azure podporuje tyto typy schémat, které stanoveny schémata kromě vlastní typ sledování.

* [Schéma sledování AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schéma sledování X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Vlastní schémata sledování](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Další postup

* [Sledování zpráv B2B ve analýzy protokolů](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "zpráv B2B sledování v OMS")
* [Další informace o integračního balíčku Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md "Další informace o Enterprise integračního balíčku")

