---
title: Azure Event Grid – povolení diagnostických protokolů pro téma
description: Tento článek obsahuje podrobné pokyny, jak povolit diagnostické protokoly pro téma mřížky událostí Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: a32d26629bb9efed4a4f1f49eee2f0534e1873a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960500"
---
#  <a name="diagnostic-logs-for-an-azure-event-grid-topic"></a>Diagnostické protokoly pro téma mřížky událostí Azure
Nastavení diagnostiky umožňují uživatelům služby Event Grid zachytit a zobrazit protokoly o selhání publikování a doručení na jednom z následujících míst: účet úložiště Azure, centrum událostí nebo pracovní prostor Analýzy protokolů. Tento článek obsahuje podrobné pokyny k povolení diagnostických protokolů pro téma mřížky událostí.

## <a name="prerequisites"></a>Požadavky

- Téma zřízené mřížky událostí
- Zřízené místo určení pro zachycení diagnostických protokolů. To může jeden z následujících cílů:
    - Účet služby Azure Storage
    - Centrum událostí
    - Pracovní prostor služby Log Analytics


## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>Postup povolení diagnostických protokolů pro téma

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Přejděte na téma mřížky událostí, pro které chcete povolit nastavení diagnostického protokolu. 
3. V levé nabídce vyberte **Nastavení diagnostiky** v části **Monitorování.**
4. Na stránce **Nastavení diagnostiky** vyberte **Přidat nové diagnostické nastavení**. 
    
    ![Tlačítko nastavení diagnostiky](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Zadejte **název** nastavení diagnostiky. 

    ![Diagnostická nastavení - název](./media/enable-diagnostic-logs-topic/diagnostic-settings-name.png)     
6. Povolte jeden nebo více cílů sběru protokolů a pak je nakonfigurujte výběrem dříve vytvořeného prostředku sběru. 
    - Pokud vyberete **Archivovat do účtu úložiště**, vyberte **účet úložiště – Konfigurovat**a pak vyberte účet úložiště ve svém předplatném Azure. 

        ![Archivace do účtu úložiště Azure](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Pokud vyberete **Stream ovat do centra událostí**, vyberte Centrum událostí – **Konfigurovat**a pak vyberte obor názvů Event Hubs, centrum událostí a zásady přístupu. 
        ![Streamování do centra událostí](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Pokud vyberete **Odeslat do analýzy protokolů**, vyberte pracovní prostor Log Analytics.
        ![Odeslání do Log Analytics](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
7. V části **Protokol** vyberte možnosti **DeliveryFailures** a **PublishFailures.** 
    ![Vyberte chyby](./media/enable-diagnostic-logs-topic/log-failures.png)
8. Vyberte **Uložit**. Chcete-li stránku zavřít, vyberte **x** v pravém rohu. 
9. Nyní zpět na stránce **Nastavení diagnostiky** potvrďte, že se v tabulce **Nastavení diagnostiky** zobrazí nová položka. 
    ![Diagnostické nastavení v seznamu](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     Můžete také povolit shromažďování všech metrik pro toto téma. 

## <a name="next-steps"></a>Další kroky
Pokud potřebujete další pomoc, zveřejněte svůj problém ve [fóru Přetečení zásobníku](https://stackoverflow.com/questions/tagged/azure-eventgrid) nebo otevřete [lístek podpory](https://azure.microsoft.com/support/options/). 
