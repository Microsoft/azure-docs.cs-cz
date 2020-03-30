---
title: Správa farem
description: Popisuje, jak spravovat farmy
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 09144c4c35ab911b60931849807123608f2c3cdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271717"
---
# <a name="manage-farms"></a>Správa farem

Farmy můžete spravovat v Azure FarmBeats. Tento článek obsahuje informace o tom, jak vytvářet farmy, instalovat zařízení, senzory a drony, které vám pomohou spravovat vaše farmy.

## <a name="create-farms"></a>Vytvořit farmy

Použijte k tomu následující postup:

1. Přihlaste se do akcelerátoru farmy, zobrazí se stránka **Farmy.**
    Na stránce **Farmy** se zobrazí seznam farem v případě, že již byly vytvořeny v předplatném.

    Zde je ukázkový obrázek:

    ![Projekt Farm Beats](./media/create-farms-in-azure-farmbeats/create-farm-main-page-1.png)


2. Vyberte **Vytvořit farmu** a zadejte **název**, **plodiny** a **adresu**.
3. V poli **Definovat hranice farmy**(povinné pole) vyberte buď Označit na **mapě,** nebo **vložit kód GeoJSON**.

Zde jsou dva způsoby, jak definovat hranice farmy:

1. **Označit na mapě**: Pomocí nástroje pro ovládání mapy nakreslete a označte hranici farmy. Chcete-li označit ![hranice,](./media/create-farms-in-azure-farmbeats/pencil-icon-1.png) Projekt Farm Beats a označit přesné hranice.

    ![Projekt Farm Beats](./media/create-farms-in-azure-farmbeats/create-farm-mark-on-map-1.png)

2. **Vložit geojson kód**: GeoJSON je formát pro kódování geografických datových struktur pomocí JavaScript object notace (JSON). Tato možnost zobrazí textové pole, do kterého lze zadat řetězec GeoJSON, který označuje hranice farmy. Kód GeoJSON můžete také vytvořit z GeoJSON.io.
Pomocí popisů můžete vyplnit informace.

    ![Projekt Farm Beats](./media/create-farms-in-azure-farmbeats/create-new-farm-1.png)

3.  Chcete-li vytvořit farmu, **vyberte Odeslat.** Na stránce Farmy je vytvořena a zobrazena nová **farma.**

## <a name="view-farm"></a>Zobrazit farmu

Na stránce se seznamem Farm a seznam vytvořených farem se zobrazí seznam vytvořených farem. Výběrem farmy zobrazíte seznam:

 - **Počet zařízení** – zobrazuje počet a stav zařízení nasazených v rámci farmy.
 - **Mapa** — mapa farmy se zařízeními nasazenými na farmě.
 - **Telemetrie** – zobrazuje telemetrii ze senzorů nasazených ve farmě.
 - **Nejnovější přesné mapy** – zobrazuje nejnovější mapu satelitních indexů (EVI, NDWI), heatmapu vlhkosti půdy a mapu umístění senzorů.

## <a name="edit-farm"></a>Upravit farmu

Na stránce **Farmy** se zobrazí seznam vytvořených farem.

1.  Vyberte farmu, kterou chcete zobrazit a upravit.
2.  Vyberte **Upravit farmu,** chcete-li upravit informace o farmě. V okně **Podrobnosti farmy** můžete upravit pole **Název**, **Oříznutí**, **Adresa**a definovat pole **Hranice farmy.**

    ![Projekt Farm Beats](./media/create-farms-in-azure-farmbeats/edit-farm-1.png)

3. Vyberte **Odeslat,** chcete-li upravit upravené podrobnosti.

## <a name="delete-farm"></a>Odstranit farmu

Na stránce **Farmy** se zobrazí seznam vytvořených farem. K odstranění farmy použijte následující kroky:

1.  Vyberte farmu ze seznamu, chcete-li odstranit podrobnosti o farmě.
2.  Chcete-li odstranit farmu, vyberte **Odstranit farmu.**

    ![Projekt Farm Beats](./media/create-farms-in-azure-farmbeats/delete-farm-1.png)

    > [!NOTE]
    > Když odstraníte farmu, zařízení a mapy přidružené k farmě se neodstraní. Žádné informace o farmě spojené se zařízením a mapami nebudou relevantní. Můžete pokračovat v zobrazení zařízení, telemetrie a mapy ze služby FarmBeats.


## <a name="next-steps"></a>Další kroky

Nyní, když jste vytvořili svou farmu, se dozvíte, jak [dostat data ze senzorů](get-sensor-data-from-sensor-partner.md) proudící do vaší farmy.
