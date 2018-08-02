---
title: Kurz správy zařízení v řešení vzdáleného monitorování založeném na Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak spravovat zařízení připojená k akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/19/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: b1e12ad837def690e55fc501c67d8b2ed5e3cd4d
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284607"
---
# <a name="tutorial-configure-and-manage-devices-connected-to-your-monitoring-solution"></a>Kurz: Konfigurace a správa zařízení připojených k řešení monitorování

V tomto kurzu použijete akcelerátor řešení vzdáleného monitorování ke konfiguraci a správě připojených zařízení IoT. Do akcelerátoru řešení přidáte nové zařízení, nakonfigurujete ho a aktualizujete jeho firmware.

Společnost Contoso si objednala nové stroje pro rozšíření jednoho ze svých závodů. Při čekání na doručení nových strojů budete chtít spustit simulaci a otestovat chování vašeho řešení. Simulaci spustíte tak, že do akcelerátoru řešení vzdáleného monitorování přidáte nové simulované zařízení motoru a otestujete, jestli toto simulované zařízení správně reaguje na akce a aktualizace konfigurace.

Akcelerátor řešení vzdáleného monitorování využívá funkce služby IoT Hub, jako jsou [úlohy](../iot-hub/iot-hub-devguide-jobs.md) a [přímé metody](../iot-hub/iot-hub-devguide-direct-methods.md), a poskytuje tak rozšiřitelný způsob konfigurace a správy zařízení. Přestože se v tomto kurzu používají simulovaná zařízení, vývojář zařízení může implementovat přímé metody do [fyzického zařízení připojeného k akcelerátoru řešení vzdáleného monitorování](iot-accelerators-connecting-devices.md).

V tomto kurzu se naučíte:

>[!div class="checklist"]
> * Zřízení simulovaného zařízení
> * Test simulovaného zařízení
> * Aktualizace firmwaru zařízení
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

## <a name="act-on-a-device"></a>Práce na zařízení

Pokud chcete otestovat, že simulované zařízení motoru správně reaguje na akce inicializované z řídicího panelu, spusťte metodu **FirmwareUpdate**. Pokud chcete pracovat na zařízení spuštěním metody, vyberte zařízení v seznamu zařízení a pak klikněte na **Úlohy**. Pokud chcete pracovat na více zařízeních, můžete vybrat více než jedno zařízení. Na panelu **Úlohy** vyberte **Spustit metodu**. Model zařízení **Engine** (Motor) určuje tři metody: **FirmwareUpdate**, **FillTank** a **EmptyTank**:

[![Metody motoru](./media/iot-accelerators-remote-monitoring-manage/devicesmethods-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmethods-expanded.png#lightbox)

Zvolte **FirmwareUpdate**, nastavte název úlohy na **UpdateEngineFirmware**, verzi firmwaru na **2.0.0**, identifikátor URI firmwaru na **http://contoso.com/engine.bin** a pak klikněte na **Použít**:

[![Plánování metody aktualizace firmwaru](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatejob-inline.png)](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatejob-expanded.png#lightbox)

Pokud chcete sledovat stav úlohy, klikněte na **Zobrazit stav úlohy**:

[![Monitorování naplánované úlohy aktualizace firmwaru](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatestatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatestatus-expanded.png#lightbox)

Po dokončení úlohy se vraťte na stránku **Zařízení**. Zobrazí se nová verze firmwaru pro zařízení motoru.

Pokud na stránce **Zařízení** vyberete několik zařízení různých typů, přesto můžete vytvořit úlohu, která na těchto několika zařízeních spustí metodu. Na panelu **Úlohy** se zobrazí pouze metody společné pro všechna vybraná zařízení.

## <a name="reconfigure-a-device"></a>Změna konfigurace zařízení

Pokud chcete otestovat možnost aktualizace vlastností konfigurace zařízení, vyberte zařízení v seznamu zařízení na stránce **Zařízení**. Pak klikněte na **Úlohy** a zvolte **Změnit konfiguraci**. Na panelu úloh se zobrazí hodnoty vlastností pro vybrané zařízení, které můžete aktualizovat:

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

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak konfigurovat a spravovat zařízení připojená k akcelerátoru řešení vzdáleného monitorování. Informace o tom, jak pomocí akcelerátoru řešení identifikovat a opravit problémy s připojenými zařízeními, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Použití upozornění zařízení k identifikaci a opravě problémů se zařízeními připojenými k řešení monitorování](iot-accelerators-remote-monitoring-maintain.md)
