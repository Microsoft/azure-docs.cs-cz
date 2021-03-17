---
title: Azure Event Grid – povolení diagnostických protokolů pro témata nebo domény
description: Tento článek poskytuje podrobné pokyny k povolení diagnostických protokolů pro téma Azure Event Grid.
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: ff00c1438c49cbc9f9e67eba0cf0acef7991a5a4
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576447"
---
#  <a name="enable-diagnostic-logs-for-azure-event-grid-topics-or-domains"></a>Povolení diagnostických protokolů pro témata nebo domény služby Azure Event Grid
Tento článek poskytuje podrobné pokyny, jak povolit nastavení diagnostiky pro Event Grid témata nebo domény.  Tato nastavení umožňují zachytit a zobrazit protokoly **selhání publikování a doručení** . 

## <a name="prerequisites"></a>Předpoklady

- Téma zřízené Event gridu
- Zřízené cíle pro zachytávání diagnostických protokolů. Může to být jeden z následujících cílů ve stejném umístění jako téma Event Grid:
    - Účet služby Azure Storage
    - Centrum událostí
    - Pracovní prostor služby Log Analytics

## <a name="enable-diagnostic-logs-for-a-custom-topic"></a>Povolení diagnostických protokolů pro vlastní téma

> [!NOTE]
> Následující postup poskytuje podrobné pokyny pro povolení diagnostických protokolů pro téma. Postup povolení diagnostických protokolů pro doménu je velmi podobný. V kroku 2 Přejděte do **domény** Event grid v Azure Portal.  

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
2. Přejděte do tématu Event gridu, pro které chcete povolit nastavení diagnostického protokolu. 
    1. Na panelu hledání v horní části vyhledejte **Event Grid témata**. 
    
        ![Hledat vlastní témata](./media/enable-diagnostic-logs-topic/search-custom-topics.png)
    1. Vyberte **téma** ze seznamu, pro který chcete konfigurovat nastavení diagnostiky. 
1. V nabídce vlevo vyberte **nastavení diagnostiky** v části **monitorování** .
1. Na stránce **nastavení diagnostiky** vyberte **Přidat nové nastavení diagnostiky**. 
    
    ![Tlačítko Přidat nastavení diagnostiky](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Zadejte **název** pro nastavení diagnostiky. 
6. V části **protokol** vyberte možnosti **DeliveryFailures** a **PublishFailures** . 
    ![Vybrat selhání](./media/enable-diagnostic-logs-topic/log-failures.png)
7. Povolte jedno nebo více cílových umístění pro zaznamenávání protokolů a pak je nakonfigurujte výběrem předchozího vytvořeného prostředku zachycení. 
    - Pokud vyberete možnost **archivovat do účtu úložiště**, vyberte **účet úložiště – konfigurovat** a potom vyberte účet úložiště ve vašem předplatném Azure. 

        ![Snímek obrazovky zobrazující stránku nastavení diagnostiky, která má zaškrtnuté políčko Archivovat na účet úložiště Azure a vybraný účet úložiště.](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Pokud vyberete **datový proud do centra událostí**, vyberte **centrum událostí – konfigurovat** a pak vyberte obor názvů Event Hubs, centrum událostí a zásady přístupu. 
        ![Snímek obrazovky zobrazující, že stránka nastavení diagnostiky s datovým proudem do centra událostí je zaškrtnuté.](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Pokud vyberete možnost **Odeslat do Log Analytics**, vyberte pracovní prostor Log Analytics.
        ![Snímek obrazovky zobrazující stránku nastavení diagnostiky, která má zaškrtnuté políčko Odeslat do Log Analytics.](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
8. Vyberte **Uložit**. Pak v pravém horním rohu vyberte **X** , aby se stránka zavřela. 
9. Teď se vraťte na stránku **nastavení diagnostiky** a ověřte, že se v tabulce **nastavení diagnostiky** zobrazila nová položka. 
    ![Snímek obrazovky zobrazující stránku nastavení diagnostiky s novou položkou zvýrazněnou v tabulce nastavení diagnostiky](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     Můžete také povolit shromažďování všech metrik pro téma. 

## <a name="enable-diagnostic-logs-for-a-system-topic"></a>Povolení diagnostických protokolů pro systémové téma

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
2. Přejděte do tématu Event gridu, pro které chcete povolit nastavení diagnostického protokolu. 
    1. Na panelu hledání v horní části vyhledejte **Event Grid systémová témata**. 
    
        ![Vyhledat systémová témata](./media/enable-diagnostic-logs-topic/search-system-topics.png)
    1. Vyberte **téma systému** , pro které chcete konfigurovat nastavení diagnostiky. 
    
        ![Vybrat systémové téma](./media/enable-diagnostic-logs-topic/select-system-topic.png)
3. **V nabídce** vlevo vyberte **nastavení diagnostiky** a pak vyberte **Přidat nastavení diagnostiky**. 

    ![Přidat nastavení diagnostiky – tlačítko](./media/enable-diagnostic-logs-topic/system-topic-add-diagnostic-settings-button.png)
4. Zadejte **název** pro nastavení diagnostiky. 
7. V části **protokol** vyberte **DeliveryFailures** . 
    ![Vybrat selhání doručení](./media/enable-diagnostic-logs-topic/system-topic-select-delivery-failures.png)
6. Povolte jedno nebo více cílových umístění pro zaznamenávání protokolů a pak je nakonfigurujte výběrem předchozího vytvořeného prostředku zachycení. 
    - Pokud vyberete možnost **Odeslat do Log Analytics**, vyberte pracovní prostor Log Analytics.
        ![Odeslání do Log Analytics](./media/enable-diagnostic-logs-topic/system-topic-select-log-workspace.png) 
    - Pokud vyberete možnost **archivovat do účtu úložiště**, vyberte **účet úložiště – konfigurovat** a potom vyberte účet úložiště ve vašem předplatném Azure. 

        ![Archivace na účet služby Azure Storage](./media/enable-diagnostic-logs-topic/system-topic-select-storage-account.png)
    - Pokud vyberete **datový proud do centra událostí**, vyberte **centrum událostí – konfigurovat** a pak vyberte obor názvů Event Hubs, centrum událostí a zásady přístupu. 
        ![Streamování do centra událostí](./media/enable-diagnostic-logs-topic/system-topic-select-event-hub.png)
8. Vyberte **Uložit**. Pak v pravém horním rohu vyberte **X** , aby se stránka zavřela. 
9. Teď se vraťte na stránku **nastavení diagnostiky** a ověřte, že se v tabulce **nastavení diagnostiky** zobrazila nová položka. 
    ![Nastavení diagnostiky v seznamu](./media/enable-diagnostic-logs-topic/system-topic-diagnostic-settings-targets.png)

     Můžete také povolit shromažďování všech **metrik** pro systémové téma.

    ![Systémové téma – povolení všech metrik](./media/enable-diagnostic-logs-topic/system-topics-metrics.png)

## <a name="view-diagnostic-logs-in-azure-storage"></a>Zobrazit diagnostické protokoly v Azure Storage 

1. Jakmile povolíte účet úložiště jako cíl zachytávání, Event Grid začne generovat diagnostické protokoly. V účtu úložiště byste měli vidět nové kontejnery s názvem **Insights-logs-deliveryfailures** a **Insights-logs-publishfailures** . 

    ![Úložiště – kontejnery pro diagnostické protokoly](./media/enable-diagnostic-logs-topic/storage-containers.png)
2. Při procházení jednoho z kontejnerů skončíte s objektem BLOB ve formátu JSON. Soubor obsahuje položky protokolu pro buď selhání doručení, nebo selhání publikování. Navigační cesta představuje **ResourceID** tématu Event gridu a časové razítko (minuty), jako při vygenerování položek protokolu. Soubor BLOB/JSON, který je ke stažení ke stažení, je v konečném souladu se schématem popsaným v následující části. 

    [![Soubor JSON v úložišti ](./media/enable-diagnostic-logs-topic/select-json.png)](./media/enable-diagnostic-logs-topic/select-json.png)
3. Obsah v souboru JSON byste měli vidět podobně jako v následujícím příkladu: 

    ```json
    {
        "time": "2019-11-01T00:17:13.4389048Z",
        "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
        "eventSubscriptionName": "SAMPLEDESTINATION",
        "category": "DeliveryFailures",
        "operationName": "Deliver",
        "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
    }
    ```
## <a name="next-steps"></a>Další kroky
Schéma protokolu a další koncepční informace o diagnostických protokolech pro témata a domény najdete v tématu [diagnostické protokoly](diagnostic-logs.md).
