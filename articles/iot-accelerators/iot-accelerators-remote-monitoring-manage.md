---
title: Konfigurace zařízení v vzdáleného monitorování řešení kurz – Azure | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak nakonfigurovat zařízení připojená k akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/15/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 5505ceaa88f1ad792df57f9728f5e4809404942c
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53599497"
---
# <a name="tutorial-configure-devices-connected-to-your-monitoring-solution"></a>Kurz: Konfigurace zařízení připojených k řešení monitorování

V tomto kurzu použijete akcelerátor řešení vzdáleného monitorování ke konfiguraci a správě připojených zařízení IoT. Přidat nové zařízení k akcelerátoru řešení a konfigurace zařízení.

Společnost Contoso si objednala nové stroje pro rozšíření jednoho ze svých závodů. Při čekání na doručení nových strojů budete chtít spustit simulaci a otestovat chování vašeho řešení. Při spuštění simulace, přidat nové zařízení simulovaného modul akcelerátor řešení vzdálené monitorování a test, který toto simulované zařízení správně reaguje na aktualizace konfigurace. Přestože se v tomto kurzu používají simulovaná zařízení, vývojář zařízení může implementovat přímé metody do [fyzického zařízení připojeného k akcelerátoru řešení vzdáleného monitorování](iot-accelerators-connecting-devices.md).

V tomto kurzu se naučíte:

>[!div class="checklist"]
> * Zřízení simulovaného zařízení
> * Test simulovaného zařízení
> * Změna konfigurace zařízení
> * Uspořádání zařízení

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="add-a-simulated-device"></a>Přidání simulovaného zařízení

V řešení přejděte na stránku **Zařízení** a pak klikněte na **+ Nové zařízení**:

[![Zřízení simulovaného zařízení](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-expanded.png#lightbox)

Na panelu **Nové zařízení** zvolte **Simulované**, počet zařízení, která se mají zřídit, ponechte na hodnotě **1**, zvolte model zařízení **Vadný motor** a pak zvolením možnosti **Použít** vytvořte simulované zařízení:

[![Zřízení simulovaného zařízení motoru](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-expanded.png#lightbox)

## <a name="test-the-simulated-device"></a>Test simulovaného zařízení

Pokud chcete otestovat, že simulované zařízení motoru odesílá telemetrická data a hlásí hodnoty vlastností, vyberte ho v seznamu zařízení na stránce **Zařízení**. Na panelu **Podrobnosti o zařízení** se zobrazí aktuální informace o motoru:

[![Zobrazení nového simulovaného zařízení motoru](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-expanded.png#lightbox)

V části **Podrobnosti o zařízení** ověřte, že vaše nové zařízení odesílá telemetrická data. Pokud chcete zobrazit datový proud telemetrických dat o vibracích z vašeho zařízení, klikněte na **Vibrace**:

[![Výběr zobrazení datového proudu telemetrie](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-expanded.png#lightbox)

Na panelu **Podrobnosti o zařízení** se zobrazí další informace o zařízení, jako například hodnoty značek, podporované metody a vlastnosti, které zařízení hlásí.

Pokud chcete zobrazit podrobnou diagnostiku posuňte se na panelu **Podrobnosti o zařízení** dolů do části **Diagnostika**.

## <a name="reconfigure-a-device"></a>Změna konfigurace zařízení

Pokud chcete otestovat možnost aktualizace vlastností konfigurace zařízení, vyberte zařízení v seznamu zařízení na stránce **Zařízení**. Pak klikněte na tlačítko **úlohy**a klikněte na tlačítko **vlastnosti**. Na panelu úloh se zobrazí hodnoty vlastností pro vybrané zařízení, které můžete aktualizovat:

[![Změna konfigurace zařízení](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-expanded.png#lightbox)

Pokud chcete aktualizovat umístění motoru, nastavte název úlohy na **UpdateEngineLocation**, zeměpisnou délku na **-122.15**, umístění na **Factory 2**, zeměpisnou šířku na **47.62** a klikněte na **Použít**:

[![Aktualizace hodnoty vlastnosti zařízení](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-expanded.png#lightbox)

Pokud chcete sledovat stav úlohy, klikněte na **Zobrazit stav úlohy**:

[![Aktualizace hodnoty vlastnosti zařízení](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-expanded.png#lightbox)

Po dokončení úlohy přejděte na stránku **Řídicí panel**. Zařízení motoru se na mapě zobrazí ve svém novém umístění:

[![Zobrazení umístění motoru](./media/iot-accelerators-remote-monitoring-manage/enginelocation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/enginelocation-expanded.png#lightbox)

## <a name="organize-your-devices"></a>Uspořádání zařízení

Pokud si jako operátor chcete usnadnit uspořádání a správu zařízení, měli byste je označit názvem týmu. Contoso má dva různé týmy starající se o aktivity služeb u zákazníků:

* Tým pro chytrá vozidla spravuje nákladní vozy a prototypy zařízení.
* Tým pro chytré budovy spravuje chladiče, výtahy a motory.

Pokud chcete zobrazit všechna vaše zařízení, přejděte na stránku **Zařízení** a zvolte filtr **Všechna zařízení**:

[![Zobrazení všech zařízení](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-expanded.png#lightbox)

### <a name="add-tags"></a>Přidání značek

Vyberte všechny **Nákladní vozy** a **Prototypy** zařízení. Pak klikněte na **Úlohy**.

Na panelu **Úlohy** vyberte **Značka**, nastavte název úlohy na **AddConnectedVehicleTag** a pak přidejte textovou značku **FieldService** s hodnotou **ConnectedVehicle**. Pak klikněte na **Použít**:

[![Přidání značky k nákladním vozům a prototypům zařízení](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-expanded.png#lightbox)

Na stránce zařízení vyberte všechna zařízení **chladiče**, **výtahu** a **motoru**. Pak klikněte na **Úlohy**.

Na panelu **Úlohy** vyberte **Značka**, nastavte název úlohy na **AddSmartBuildingTag** a pak přidejte textovou značku **FieldService** s hodnotou **SmartBuilding**. Pak klikněte na **Použít**:

[![Přidání značky k zařízením chladiče, výtahu a motoru](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-expanded.png#lightbox)

### <a name="create-filters"></a>Vytváření filtrů

Hodnoty značek teď můžete využít k vytváření filtrů. Na stránce **Zařízení** klikněte na **Spravovat skupiny zařízení**:

[![Správa skupin zařízení](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-expanded.png#lightbox)

Vytvořte filtr textu, v jehož podmínce se používá značka s názvem **FieldService** a hodnotou **SmartBuilding**. Uložte filtr jako **Chytrá budova**:

[![Vytvoření filtru chytrých budov](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-expanded.png#lightbox)

Vytvořte filtr textu, v jehož podmínce se používá značka s názvem **FieldService** a hodnotou **ConnectedVehicle**. Uložte filtr jako **Připojené vozidlo**.

[![Vytvoření filtru připojených vozidel](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-expanded.png#lightbox)

Operátor společnosti Contoso teď může zadávat dotazy na zařízení na základě operačního týmu:

[![Vytvoření filtru připojených vozidel](./media/iot-accelerators-remote-monitoring-manage/filterinaction-inline.png)](./media/iot-accelerators-remote-monitoring-manage/filterinaction-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli, jak konfigurovat a spravovat zařízení připojená k akcelerátoru řešení vzdáleného monitorování. Pokud se chcete naučit, jak používat akcelerátor řešení k provádění analýz původních příčin neočekávaného upozornění, pokračujte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Analýza původní příčiny pro upozornění](iot-accelerators-remote-monitoring-root-cause-analysis.md)
