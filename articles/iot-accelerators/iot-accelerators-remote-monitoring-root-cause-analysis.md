---
title: Analýza hlavní příčiny pro upozornění – Azure | Microsoft Docs
description: V tomto kurzu se naučíte provádět analýzu hlavní příčiny pro upozornění pomocí služby Azure Time Series Insights.
author: aditidugar
ms.author: adugar
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 70d29359d4a4bcf9f5badbbf0c553d7bed88a02b
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284555"
---
# <a name="tutorial-conduct-a-root-cause-analysis-on-an-alert"></a>Kurz: Analýza hlavní příčiny pro upozornění

V tomto kurzu se dozvíte, jak používat akcelerátor řešení vzdáleného monitorování k diagnostice hlavní příčiny upozornění. Uvidíte, že se upozornění aktivovalo na řídicím panelu řešení vzdáleného monitorování, a potom použijete průzkumníka služby Azure Time Series Insights a prozkoumáte hlavní příčinu.

Tento kurz používá dvě simulovaná zařízení nákladního vozu, která odesílají polohu, zeměpisnou výšku, rychlost a telemetrická data o teplotě nákladu. Tyto nákladní vozy spravuje organizace Contoso a jsou připojené k akcelerátoru řešení vzdáleného monitorování. Jako operátor organizace Contoso musíte pochopit, proč jeden z vašich nákladních vozů (delivery-truck-02) zaprotokoloval upozornění na nízkou teplotu.

V tomto kurzu se naučíte:

>[!div class="checklist"]
> * Filtrování zařízení na řídicím panelu
> * Zobrazení telemetrie v reálném čase
> * Prozkoumat data v průzkumníku služby Time Series Insights
> * Provést analýzu hlavní příčiny
> * Vytvořit nové pravidlo na základě vašich poznatků

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>Výběr zařízení k zobrazení

K výběru připojených zařízení, která se zobrazí na stránce **Řídicí panel**, použijte filtry. Pokud chcete zobrazit pouze zařízení **Nákladní vůz**, v rozevíracím seznamu filtru zvolte předdefinovaný filtr **Nákladní vozy**:

[![Vyfiltrování nákladních vozů na řídicím panelu](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-expanded.png#lightbox)

Když filtr použijete, jenom zařízení, které splňují podmínky filtru se zobrazí na mapě a na panelu telemetrie na **řídicí panel**. Uvidíte, že k akcelerátoru řešení jsou připojené dva nákladní vozy (včetně nákladního vozu **truck-02**).

## <a name="view-real-time-telemetry"></a>Zobrazení telemetrie v reálném čase

Akcelerátor řešení vykresluje telemetrická data v reálném čase do grafu na stránce **Řídicí panel**. Ve výchozím nastavení graf zobrazuje telemetrická data zeměpisné výšky, která jsou v průběhu času různá:

[![Graf telemetrických dat zeměpisné výšky nákladního vozu](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-expanded.png#lightbox)

Pokud chcete zobrazit telemetrická data o teplotě nákladních vozů, klikněte na **Temperature** (Teplota) na **panelu telemetrických dat**. Uvidíte, jak se teplota za posledních 15 minut u obou nákladních vozů měnila. V podokně upozornění můžete také vidět, že se pro vůz delivery-truck-02 aktivovalo upozornění na nízkou teplotu.

[![Řídicí panel vzdáleného monitorování s upozorněním na nízkou teplotu](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-expanded.png#lightbox)

## <a name="explore-the-data"></a>Zkoumání dat

Pokud chcete identifikovat příčinu varování týkajícího se nízké teploty, otevřete telemetrická data nákladního vozu v průzkumníku služby Time Series Insights. Na řídicím panelu klikněte na jakýkoli odkaz pro **prozkoumání ve službě Time Series Insights**:

[![Řídicí panel vzdáleného monitorování se zvýrazněnými odkazy služby TSI](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-expanded.png#lightbox)

Jakmile se průzkumník spustí, uvidíte seznam všech vašich zařízení:

[![Počáteční zobrazení průzkumníka TSI](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-expanded.png#lightbox)

Filtrovat zařízení tak, že zadáte **doručování truck** pole filtru, a vyberte **teploty** jako **míru** na levém panelu:

[![Teplota vozu v průzkumníku TSI](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-expanded.png#lightbox)

Zobrazí stejné zobrazení, které jste viděli v řídicím panelu vzdáleného monitorování. Kromě toho můžete nyní lupy blíže časový rámec, pro který byla výstraha v rámci:

[![Přiblížení v průzkumníku TSI](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-expanded.png#lightbox)

Můžete také přidat další streamy telemetrických dat přicházející z nákladních vozů. Klikněte na tlačítko **přidat** tlačítko v levém horním rohu. Zobrazí se nové podokno:

[![Průzkumník TSI s novým podoknem](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-expanded.png#lightbox)

V novém podokně změňte název nového popisku na **Devices** (Zařízení) tak, aby odpovídalo předchozímu názvu. Vyberte **výška** jako **míru** a **iothub připojení zařízení id** jako **rozdělit podle** přidat výšku telemetrická data do hodnotu zobrazení:

[![Průzkumník TSI s teplotou a výškou](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-expanded.png#lightbox)

## <a name="diagnose-the-alert"></a>Diagnostika upozornění

Při pohledu na datových proudů v aktuálním zobrazení uvidíte, že výška profily pro dva trucks se liší. Také uvidíte to, že k poklesu teploty ve voze **delivery-truck-02** dojde, když vůz dosáhne vysoké zeměpisné výšky. Tímto zjištěním jste překvapeni, protože vozy měly naplánovanou stejnou trasu.

Abyste si potvrdili domněnku, že každý z vozů jel jinou cestou, přidejte na boční panel další podokno pomocí tlačítka **Add** (Přidat). V novém podokně změňte název nového popisku na **Devices** (Zařízení) tak, aby odpovídalo předchozímu názvu. Pokud chcete do zobrazení přidat telemetrická data zeměpisné délky, vyberte **zeměpisnou délku** jako **míru** a **iothub-connection-device-id** jako hodnotu, **podle které se má provádět rozdělení**. Když se podíváte na rozdíly mezi streamy **zeměpisné délky**, uvidíte, že vozy jely různými cestami:

[![Průzkumník TSI s teplotou, zeměpisnou výškou a délkou](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-expanded.png#lightbox)

## <a name="create-a-new-rule"></a>Vytvoření nového pravidla

Zatímco trasy nákladní vozidlo jsou obvykle optimalizované předem, zjistíte, že vzory provozu, počasí a jiné nepředvídatelné události můžete způsobit prodlevy a ponechat rozhodnutí o směrování poslední minutu truck ovladače podle jejich nejlepší rozhodnutí. Ale vzhledem k tomu, že teplota vaše prostředky uvnitř vozidlo je velmi důležité, by měl vytvořit další pravidla ve vašem řešení vzdáleného monitorování. Toto pravidlo je zajistit, že se zobrazí upozornění, pokud průměrná výška za interval 1 minuty překročí 350 nohou:

[![Karta pravidel vzdáleného monitorování s nastavením pravidla pro zeměpisnou výšku](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-expanded.png#lightbox)

Pokud se chcete dozvědět, jak vytvářet a upravovat pravidla, podívejte se na předchozí kurz týkající se [zjišťování problémů se zařízením](iot-accelerators-remote-monitoring-automate.md).

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli, jak používat průzkumníka služby Time Series Insights s akcelerátorem řešení vzdáleného monitorování k diagnostice hlavní příčiny upozornění. Informace o tom, jak pomocí akcelerátoru řešení identifikovat a opravit problémy s připojenými zařízeními, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Použití upozornění zařízení k identifikaci a opravě problémů se zařízeními připojenými k řešení monitorování](iot-accelerators-remote-monitoring-maintain.md)
