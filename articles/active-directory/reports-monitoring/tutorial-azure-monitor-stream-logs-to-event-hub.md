---
title: Kurz – Stream Azure Active Directory protokoly do centra událostí Azure | Microsoft Docs
description: Přečtěte si, jak nastavit Azure Diagnostics pro odesílání Azure Active Directory protokolů do centra událostí.
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93dd7b13ca9e1a3f078994e76610c45447cfa41c
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264090"
---
# <a name="tutorial-stream-azure-active-directory-logs-to-an-azure-event-hub"></a>Kurz: streamování Azure Active Directorych protokolů do centra událostí Azure

V tomto kurzu se naučíte nastavit Azure Monitor nastavení diagnostiky pro streamování protokolů Azure Active Directory (Azure AD) do centra událostí Azure. Pomocí tohoto mechanismu můžete integrovat své protokoly s nástroji SIEM (Security Information and Event Management) třetích stran, jako je například Splunk a QRadar.

## <a name="prerequisites"></a>Požadavky 

Chcete-li použít tuto funkci, budete potřebovat:

* Předplatné Azure. Pokud nemáte předplatné Azure, můžete si [zaregistrovat bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
* Tenant služby Azure AD.
* Uživatel, který je *globálním správcem* nebo *správcem zabezpečení* pro tenanta Azure AD.
* Obor názvů Event Hubs a centrum událostí ve vašem předplatném Azure. Naučte se [vytvořit centrum událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="stream-logs-to-an-event-hub"></a>Streamování protokolů do centra událostí

1. Přihlaste se k [Azure Portal](https://portal.azure.com). 

2. Vyberte **Azure Active Directory** > **monitorování**–**protokoly auditu** > . 

3. Vyberte **Nastavení exportu**.  
    
4. V podokně **nastavení diagnostiky** proveďte jednu z následujících akcí:
    * Pokud chcete změnit stávající nastavení, vyberte **Upravit nastavení**.
    * Chcete-li přidat nová nastavení, vyberte možnost **Přidat nastavení diagnostiky**.  
      Můžete mít až tři nastavení.

      ![Nastavení exportu](./media/quickstart-azure-monitor-stream-logs-to-event-hub/ExportSettings.png)

5. Zaškrtněte políčko **datový proud do centra událostí** a pak vyberte **centrum událostí/konfigurovat**.

6. Vyberte předplatné Azure a obor názvů Event Hubs, do kterého chcete protokoly směrovat.  
    Obor názvů Subscription a Event Hubs musí být přidružený ke klientovi Azure AD, ze kterého se protokoluje Stream. Můžete také zadat centrum událostí v oboru názvů Event Hubs, do kterého mají být protokoly odesílány. Pokud není zadané žádné centrum událostí, vytvoří se v oboru názvů centrum událostí s výchozím názvem **Insights-logs-audit**.

7. Výběrem **OK** ukončete konfiguraci centra událostí.

8. Proveďte jednu nebo obě následující akce:
    * Pokud chcete do účtu úložiště Odeslat protokoly auditu, zaškrtněte políčko **AuditLogs** . 
    * Pokud chcete odesílat protokoly přihlášení do účtu úložiště, zaškrtněte políčko **SignInLogs** .

9. Kliknutím na **Uložit** nastavení uložte.

    ![Nastavení diagnostiky](./media/quickstart-azure-monitor-stream-logs-to-event-hub/DiagnosticSettings.png)

10. Po asi 15 minutách ověřte, že se události zobrazují v centru událostí. Provedete to tak, že přejdete do centra událostí z portálu a ověříte, že počet **příchozích zpráv** je větší než nula. 

    ![Protokoly auditu](./media/quickstart-azure-monitor-stream-logs-to-event-hub/InsightsLogsAudit.png)

## <a name="access-data-from-your-event-hub"></a>Přístup k datům z centra událostí

Po zobrazení dat v centru událostí můžete data přistupovat a číst dvěma způsoby:

* **Nakonfigurujte podporovaný nástroj Siem**. Aby bylo možné číst data z centra událostí, většina nástrojů vyžaduje připojovací řetězec centra událostí a určitá oprávnění k vašemu předplatnému Azure. Nástroje třetích stran s integrací Azure Monitor zahrnují, ale nejsou omezené na:
    
    * **ArcSight**: Další informace o integraci protokolů služby Azure AD s Splunk najdete v tématu [integrování protokolů Azure Active Directory s ArcSight pomocí Azure monitor](howto-integrate-activity-logs-with-arcsight.md).
    
    * **Splunk**: Další informace o integraci protokolů služby Azure AD s Splunk najdete v tématu [integrování protokolů služby Azure AD s Splunk pomocí Azure monitor](tutorial-integrate-activity-logs-with-splunk.md).
    
    * **IBM QRadar**: protokol DSM a centrum událostí Azure je možné stáhnout na webu [IBM support](https://www.ibm.com/support). Další informace o integraci s Azure najdete na webu [IBM QRadar Security Intelligence Platform 7.3.0](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0) Web.
    
    * **Sumo Logic**: Pokud chcete nastavit logiku sumo pro využívání dat z centra událostí, přečtěte si téma [instalace aplikace Azure AD a zobrazení řídicích panelů](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards). 

* **Nastavení vlastních nástrojů**. Pokud vaše aktuální SIEM ještě není podporována v diagnostice Azure Monitor, můžete nastavit vlastní nástroje pomocí rozhraní Event Hubs API. Další informace najdete v tématu [Začínáme přijímat zprávy z centra událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph).


## <a name="next-steps"></a>Další kroky

* [Integrace protokolů Azure Active Directory s využitím ArcSight pomocí Azure Monitor](howto-integrate-activity-logs-with-arcsight.md)
* [Integrace protokolů služby Azure AD s Splunk pomocí Azure Monitor](tutorial-integrate-activity-logs-with-splunk.md)
* [Integrace protokolů služby Azure AD s SumoLogic pomocí Azure Monitor](howto-integrate-activity-logs-with-sumologic.md)
* [Interpretace schématu protokolů auditu v Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpretace schémat přihlašovacích protokolů v Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
