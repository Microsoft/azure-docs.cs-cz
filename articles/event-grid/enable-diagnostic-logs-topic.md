---
title: Azure Event Grid – povolení diagnostických protokolů pro téma
description: Tento článek poskytuje podrobné pokyny k povolení diagnostických protokolů pro téma Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: a32d26629bb9efed4a4f1f49eee2f0534e1873a9
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960500"
---
#  <a name="diagnostic-logs-for-an-azure-event-grid-topic"></a>Diagnostické protokoly pro téma Azure Event Grid
Nastavení diagnostiky umožňuje Event Grid uživatelům zachytit a zobrazit protokoly selhání publikování a doručení v jednom z následujících umístění: účet Azure Storage, centrum událostí nebo pracovní prostor Log Analytics. Tento článek poskytuje podrobné pokyny k povolení diagnostických protokolů pro téma Event gridu.

## <a name="prerequisites"></a>Požadavky

- Téma zřízené Event gridu
- Zřízené cíle pro zachytávání diagnostických protokolů. Může to být jeden z následujících cílů:
    - Účet služby Azure Storage
    - Centrum událostí
    - Pracovní prostor služby Log Analytics


## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>Postup povolení diagnostických protokolů pro téma

1. Přihlaste se k [Portálu Azure](https://portal.azure.com).
2. Přejděte do tématu Event gridu, pro které chcete povolit nastavení diagnostického protokolu. 
3. V nabídce vlevo vyberte **nastavení diagnostiky** v části **monitorování** .
4. Na stránce **nastavení diagnostiky** vyberte **Přidat nové nastavení diagnostiky**. 
    
    ![Tlačítko Přidat nastavení diagnostiky](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Zadejte **název** pro nastavení diagnostiky. 

    ![Nastavení diagnostiky – název](./media/enable-diagnostic-logs-topic/diagnostic-settings-name.png)     
6. Povolte jedno nebo více cílových umístění pro zaznamenávání protokolů a pak je nakonfigurujte výběrem předchozího vytvořeného prostředku zachycení. 
    - Pokud vyberete možnost **archivovat do účtu úložiště**, vyberte **účet úložiště – konfigurovat**a potom vyberte účet úložiště ve vašem předplatném Azure. 

        ![Archivace na účet služby Azure Storage](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Pokud vyberete **datový proud do centra událostí**, vyberte **centrum událostí – konfigurovat**a pak vyberte obor názvů Event Hubs, centrum událostí a zásady přístupu. 
        ![Stream do centra událostí](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Pokud vyberete možnost **Odeslat do Log Analytics**, vyberte pracovní prostor Log Analytics.
        ![odeslat do Log Analytics](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
7. V části **protokol** vyberte možnosti **DeliveryFailures** a **PublishFailures** . 
    ![vybrat selhání](./media/enable-diagnostic-logs-topic/log-failures.png)
8. Vyberte **Uložit**. V pravém horním rohu vyberte **X** , aby se stránka zavřela. 
9. Teď se vraťte na stránku **nastavení diagnostiky** a ověřte, že se v tabulce **nastavení diagnostiky** zobrazila nová položka. 
    nastavení diagnostiky ![v seznamu](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     Můžete také povolit shromažďování všech metrik pro téma. 

## <a name="next-steps"></a>Další kroky
Pokud potřebujete další pomoc, vystavte svůj problém ve [fóru Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) nebo otevřete [lístek podpory](https://azure.microsoft.com/support/options/). 
