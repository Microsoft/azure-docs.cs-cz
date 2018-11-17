---
title: Analýza hlavní příčiny pro upozornění – Azure | Microsoft Docs
description: V tomto kurzu se naučíte provádět analýzu hlavní příčiny pro upozornění pomocí služby Azure Time Series Insights.
author: aditidugar
ms.author: adugar
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 9d95734f8930d9a80e2d2872d95fdf891088dd21
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2018
ms.locfileid: "51824825"
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

Když použijete filtr, na mapě a panelu telemetrie na stránce **Řídicí panel** se zobrazí pouze zařízení, která splňují podmínky filtru. Uvidíte, že k akcelerátoru řešení jsou připojené dva nákladní vozy (včetně nákladního vozu **truck-02**).

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

Zařízení vyfiltrujte zadáním řetězce **delivery-truck** do pole filtru a výběrem **teploty** jako **míry** na levém panelu:

[![Teplota vozu v průzkumníku TSI](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-expanded.png#lightbox)

Uvidíte stejné zobrazení, které jste viděli na řídicím panelu vzdáleného monitorování. Teď si můžete přiblížit časový rámec, ve kterém se upozornění aktivovalo:

[![Přiblížení v průzkumníku TSI](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-expanded.png#lightbox)

Můžete také přidat další streamy telemetrických dat přicházející z nákladních vozů. Klikněte na tlačítko **Add** (Přidat) v levém horním rohu. Zobrazí se nové podokno:

[![Průzkumník TSI s novým podoknem](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-expanded.png#lightbox)

V novém podokně změňte název nového popisku na **Devices** (Zařízení) tak, aby odpovídalo předchozímu názvu. Pokud chcete do zobrazení přidat telemetrická data zeměpisné výšky, vyberte **zeměpisnou výšku** jako **míru** a **iothub-connection-device-id** jako hodnotu, **podle které se má provádět rozdělení**:

[![Průzkumník TSI s teplotou a výškou](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-expanded.png#lightbox)

## <a name="diagnose-the-alert"></a>Diagnostika upozornění

Když se na streamy podíváte v aktuálním zobrazení, můžete vidět, že jsou profily zeměpisné výšky pro tyto dva nákladní vozy velmi odlišné. Také uvidíte to, že k poklesu teploty ve voze **delivery-truck-02** dojde, když vůz dosáhne vysoké zeměpisné výšky. Tímto zjištěním jste překvapeni, protože vozy měly naplánovanou stejnou trasu.

Abyste si potvrdili domněnku, že každý z vozů jel jinou cestou, přidejte na boční panel další podokno pomocí tlačítka **Add** (Přidat). V novém podokně změňte název nového popisku na **Devices** (Zařízení) tak, aby odpovídalo předchozímu názvu. Pokud chcete do zobrazení přidat telemetrická data zeměpisné délky, vyberte **zeměpisnou délku** jako **míru** a **iothub-connection-device-id** jako hodnotu, **podle které se má provádět rozdělení**. Když se podíváte na rozdíly mezi streamy **zeměpisné délky**, uvidíte, že vozy jely různými cestami:

[![Průzkumník TSI s teplotou, zeměpisnou výškou a délkou](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-expanded.png#lightbox)

## <a name="create-a-new-rule"></a>Vytvoření nového pravidla

Zatímco trasy nákladních vozů se obvykle optimalizují předem, zjistíte, že vzory provozu, počasí a další nepředvídatelné události mohou způsobit zdržení a nechat rozhodování o trase na poslední chvíli na řidičích vozů, kteří se rozhodnou podle svého nejlepšího úsudku. Vzhledem k tomu, že teplota vašich prostředků uvnitř vozidla je velmi důležitá, měli byste ve svém řešení vzdáleného monitorování vytvořit další pravidlo, kterým zajistíte, že se v případě překročení průměrné zeměpisné výšky o 350 stop za 1 minutu zobrazí upozornění:

[![Karta pravidel vzdáleného monitorování s nastavením pravidla pro zeměpisnou výšku](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-expanded.png#lightbox)

Pokud se chcete dozvědět, jak vytvářet a upravovat pravidla, podívejte se na předchozí kurz týkající se [zjišťování problémů se zařízením](iot-accelerators-remote-monitoring-automate.md).

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli, jak používat průzkumníka služby Time Series Insights s akcelerátorem řešení vzdáleného monitorování k diagnostice hlavní příčiny upozornění. Informace o tom, jak pomocí akcelerátoru řešení identifikovat a opravit problémy s připojenými zařízeními, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Použití upozornění zařízení k identifikaci a opravě problémů se zařízeními připojenými k řešení monitorování](iot-accelerators-remote-monitoring-maintain.md)
