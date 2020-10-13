---
title: Konfigurace zařízení v řešení vzdáleného monitorování – Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak nakonfigurovat zařízení připojená k akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 432386809596fb2ef040a05d1fe0d12294a1abef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91534518"
---
# <a name="tutorial-configure-devices-connected-to-your-monitoring-solution"></a>Kurz: konfigurace zařízení připojených k řešení monitorování

V tomto kurzu použijete akcelerátor řešení pro vzdálené monitorování ke konfiguraci a správě připojených zařízení IoT. Přidáte nové zařízení do akcelerátoru řešení a nakonfigurujete zařízení.

Společnost Contoso si objednala nové stroje pro rozšíření jednoho ze svých závodů. Při čekání na doručení nových strojů budete chtít spustit simulaci a otestovat chování vašeho řešení. Chcete-li spustit simulaci, přidejte nové simulované zařízení modulu do akcelerátoru řešení vzdáleného monitorování a otestujte, že toto simulované zařízení reaguje správně na aktualizace konfigurace. I když tento kurz používá simulovaná zařízení, vývojář zařízení může implementovat přímé metody do [reálného zařízení připojeného k akcelerátoru řešení vzdáleného monitorování](iot-accelerators-connecting-devices.md).

V tomto kurzu jste:

>[!div class="checklist"]
> * Zřízení simulovaného zařízení
> * Test simulovaného zařízení
> * Změna konfigurace zařízení
> * Uspořádání zařízení

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="add-a-simulated-device"></a>Přidání simulovaného zařízení

V řešení přejděte na stránku **Device Explorer** a pak klikněte na **+ nové zařízení**:

[![Zřízení simulovaného zařízení](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-expanded.png#lightbox)

Na panelu **Nové zařízení** zvolte **Simulované**, počet zařízení, která se mají zřídit, ponechte na hodnotě **1**, zvolte model zařízení **Vadný motor** a pak zvolením možnosti **Použít** vytvořte simulované zařízení:

[![Zřízení simulovaného zařízení modulu](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-expanded.png#lightbox)

## <a name="test-the-simulated-device"></a>Test simulovaného zařízení

Chcete-li otestovat simulované zařízení modulu, odesílá hodnoty vlastností telemetrie a vytváření sestav, vyberte je v seznamu zařízení na stránce **Device Explorer** . Na panelu **Podrobnosti o zařízení** se zobrazí aktuální informace o motoru:

[![Zobrazení nového zařízení simulovaného motoru](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-expanded.png#lightbox)

V části **Podrobnosti o zařízení** ověřte, že vaše nové zařízení odesílá telemetrická data. Pokud chcete zobrazit datový proud telemetrických dat o vibracích z vašeho zařízení, klikněte na **Vibrace**:

[![Vyberte datový proud telemetrie, který chcete zobrazit.](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-expanded.png#lightbox)

Na panelu **Podrobnosti o zařízení** se zobrazí další informace o zařízení, jako například hodnoty značek, podporované metody a vlastnosti, které zařízení hlásí.

Pokud chcete zobrazit podrobnou diagnostiku posuňte se na panelu **Podrobnosti o zařízení** dolů do části **Diagnostika**.

## <a name="reconfigure-a-device"></a>Změna konfigurace zařízení

Chcete-li otestovat, zda lze aktualizovat vlastnosti konfigurace modulu, vyberte ji v seznamu zařízení na stránce **Device Explorer** . Pak klikněte na **úlohy**a pak zvolte **vlastnosti**. Na panelu úloh se zobrazí hodnoty vlastností pro vybrané zařízení, které můžete aktualizovat:

[![Změna konfigurace zařízení](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-expanded.png#lightbox)

Pokud chcete aktualizovat umístění motoru, nastavte název úlohy na **UpdateEngineLocation**, zeměpisnou délku na **-122.15**, umístění na **Factory 2**, zeměpisnou šířku na **47.62** a klikněte na **Použít**:

[![Snímek obrazovky zobrazující stránku "Device Explorer" s zvýrazněným oknem "Jobs".](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-expanded.png#lightbox)

Pokud chcete sledovat stav úlohy, klikněte na **Zobrazit stav úlohy**:

[![Aktualizace hodnoty vlastnosti zařízení](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-expanded.png#lightbox)

Po dokončení úlohy přejděte na stránku **Řídicí panel**. Zařízení motoru se na mapě zobrazí ve svém novém umístění:

[![Zobrazit umístění modulu](./media/iot-accelerators-remote-monitoring-manage/enginelocation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/enginelocation-expanded.png#lightbox)

## <a name="organize-your-devices"></a>Uspořádání zařízení

Pokud si jako operátor chcete usnadnit uspořádání a správu zařízení, měli byste je označit názvem týmu. Contoso má dva různé týmy starající se o aktivity služeb u zákazníků:

* Tým pro chytrá vozidla spravuje nákladní vozy a prototypy zařízení.
* Tým pro chytré budovy spravuje chladiče, výtahy a motory.

Pokud chcete zobrazit všechna vaše zařízení, přejděte na stránku **Device Explorer** a vyberte filtr **všechna zařízení** :

[![Zobrazit všechna zařízení](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-expanded.png#lightbox)

### <a name="add-tags"></a>Přidání značek

Vyberte všechny **Nákladní vozy** a **Prototypy** zařízení. Pak klikněte na **úlohy**.

Na panelu **Úlohy** vyberte **Značka**, nastavte název úlohy na **AddConnectedVehicleTag** a pak přidejte textovou značku **FieldService** s hodnotou **ConnectedVehicle**. Pak klikněte na **Použít**:

[![Přidat značku k prototypům a nákladním zařízením](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-expanded.png#lightbox)

Na stránce zařízení vyberte všechna zařízení **chladiče**, **výtahu** a **motoru**. Pak klikněte na **úlohy**.

Na panelu **Úlohy** vyberte **Značka**, nastavte název úlohy na **AddSmartBuildingTag** a pak přidejte textovou značku **FieldService** s hodnotou **SmartBuilding**. Pak klikněte na **Použít**:

[![Přidat značku do zařízení pro chlazení, výtah a modul](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-expanded.png#lightbox)

### <a name="create-filters"></a>Vytváření filtrů

Hodnoty značek teď můžete využít k vytváření filtrů. Na stránce **Device Explorer** klikněte na možnost **Spravovat skupiny zařízení**:

[![Správa skupin zařízení](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-expanded.png#lightbox)

Vytvořte filtr textu, v jehož podmínce se používá značka s názvem **FieldService** a hodnotou **SmartBuilding**. Uložte filtr jako **Chytrá budova**:

[![Vytvoření filtru inteligentního sestavování](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-expanded.png#lightbox)

Vytvořte filtr textu, v jehož podmínce se používá značka s názvem **FieldService** a hodnotou **ConnectedVehicle**. Uložte filtr jako **Připojené vozidlo**.

[![Snímek obrazovky zobrazující stránku "Device Explorer" s zvýrazněným oknem "Spravovat skupiny zařízení".](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-expanded.png#lightbox)

Operátor společnosti Contoso teď může zadávat dotazy na zařízení na základě operačního týmu:

[![Vytvořit filtr připojených vozidel](./media/iot-accelerators-remote-monitoring-manage/filterinaction-inline.png)](./media/iot-accelerators-remote-monitoring-manage/filterinaction-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak konfigurovat a spravovat zařízení připojená k akcelerátoru řešení pro vzdálené monitorování. Pokud se chcete naučit, jak používat akcelerátor řešení k provádění analýz původních příčin neočekávaného upozornění, pokračujte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Analýza původní příčiny pro upozornění](iot-accelerators-remote-monitoring-root-cause-analysis.md)
