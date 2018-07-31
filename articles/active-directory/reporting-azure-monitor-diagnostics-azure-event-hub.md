---
title: Kurz – Streamování protokolů Azure Active Directory do centra událostí Azure (Preview) | Microsoft Docs
description: Zjistěte, jak nastavit službu Azure Diagnostics, aby předávala protokoly Azure Active Directory do centra událostí (Preview)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: c4f5bf1b49d7f59b2bb938a7ddc53b96c1d354ef
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240172"
---
# <a name="tutorial-stream-azure-active-directory-logs-to-an-azure-event-hub-preview"></a>Kurz: Streamování protokolů Azure Active Directory do centra událostí Azure (Preview)

V tomto kurzu se dozvíte, jak nastavit diagnostiku v Azure Monitoru na streamování protokolů Azure Active Directory do centra událostí Azure. Pomocí tohoto mechanismu můžete integrovat své protokoly s nástroji SIEM třetích stran, jako jsou Splunk a QRadar.

## <a name="prerequisites"></a>Požadavky 

Budete potřebovat:

* Předplatné Azure. Pokud předplatné Azure nemáte, můžete si [zaregistrovat bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
* Tenanta Azure Active Directory.
* Uživatele, který je globálním správcem nebo správcem zabezpečení pro tohoto tenanta.
* Obor názvů služby Event Hubs a centrum událostí ve vašem předplatném Azure. Informace o tom, jak ho vytvořit, získáte [tady](https://docs.microsoft.com/azure/event-hubs/event-hubs-create.md).

## <a name="archive-logs-to-event-hub"></a>Archivace protokolů do centra událostí

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
2. Klikněte na **Azure Active Directory** -> **Aktivita** -> **Protokoly auditu**. 
3. Kliknutím na **Exportovat nastavení** otevřete okno Nastavení diagnostiky. Pokud chcete změnit stávající nastavení, klikněte na **Upravit nastavení**, a pokud chcete přidat nové nastavení, klikněte na **Přidat nastavení diagnostiky**. Můžete mít až tři nastavení. 
    ![Exportovat nastavení](./media/reporting-azure-monitor-diagnostics-azure-event-hub/ExportSettings.png "Exportovat nastavení")

4. Zaškrtněte políčko **Streamovat do centra událostí** a klikněte na **Centrum událostí / Konfigurovat**.
5. Vyberte předplatné Azure a obor názvů služby Event Hubs, kam chcete protokoly směrovat. Předplatné i obor názvů služby Event Hubs musí být přidružené k tenantovi Active Directory, ze kterého se protokoly streamují. Můžete také určit, do kterého centra událostí v rámci oboru názvů služby Event Hubs se mají protokoly odesílat. Pokud nezadáte žádné centrum událostí, vytvoří se centrum událostí v oboru názvů s výchozím názvem **insights-logs-audit**.
6. Kliknutím na tlačítko **OK** ukončíte konfiguraci centra událostí.
7. Zaškrtnutím políčka **Protokoly auditu** určíte, že se mají do účtu úložiště odesílat protokoly auditu. 
8. Zaškrtnutím políčka **Protokoly přihlášení** určíte, že se mají do účtu úložiště odesílat protokoly přihlášení.
9. Uložte nastavení kliknutím na **Uložit**.
    ![Nastavení diagnostiky](./media/reporting-azure-monitor-diagnostics-azure-event-hub/DiagnosticSettings.png "Nastavení diagnostiky")

10. Asi po 15 minutách zkontrolujte, jestli se v centru událostí objevují události. To provedete tak, že z portálu přejdete do centra událostí a zkontrolujete, jestli je počet **příchozích zpráv** vyšší než nula. 
    ![Protokoly auditu](./media/reporting-azure-monitor-diagnostics-azure-event-hub/InsightsLogsAudit.png "Protokoly auditu")


## <a name="access-data-from-event-hubs"></a>Přístup k datům ze služby Event Hubs

Jakmile se data zobrazí v centru událostí, můžete k nim získat přístup dvěma způsoby.

* **Nakonfigurujte podporovaný nástroj SIEM na čtení dat:** Většina nástrojů vyžaduje ke čtení dat z centra událostí připojovací řetězec centra událostí a určitá oprávnění k vašemu předplatnému Azure. Tady je seznam několika nástrojů s integrací Azure Monitoru:
    1. **Splunk:** Další informace o tom, jak integrovat protokoly Azure AD do nástroje Splunk, najdete v článku [Integrace protokolů Azure Active Directory do nástroje Splunk pomocí Azure Monitoru](reporting-azure-monitor-diagnostics-splunk-integration.md).
    
    2. **IBM QRadar:** Microsoft Azure DSM a Microsoft Azure Event Hub Protocol můžete stáhnout z [webu podpory IBM](http://www.ibm.com/support). [Další informace o integraci s Azure získáte tady](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
    
    3. **SumoLogic:** Podle [těchto pokynů](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub) můžete nastavit nástroj SumoLogic na využívání dat z centra událostí. 

* **Nastavení vlastních nástrojů ke čtení dat:** Pokud diagnostika Azure Monitoru ještě nepodporuje váš stávající nástroj SIEM, můžete pomocí rozhraní API center událostí nastavit vlastní nástroje. Další informace najdete v článku [Rozhraní API center událostí](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph).


## <a name="next-steps"></a>Další kroky

* [Integrace protokolů Azure Active Directory do nástroje Splunk pomocí diagnostiky Azure Monitoru](reporting-azure-monitor-diagnostics-splunk-integration.md)
* [Interpretace schématu protokolů auditu v diagnostice Azure Monitoru](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Interpretace schématu protokolů přihlášení v diagnostice Azure Monitoru](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)