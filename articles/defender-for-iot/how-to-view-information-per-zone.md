---
title: Informace o zařízeních v určitých zónách
description: K získání komplexních informací o zobrazení na konkrétní zónu použijte místní konzolu pro správu.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 685d7b1df4389c356ee7b531d179919025d298d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104776169"
---
# <a name="view-information-per-zone"></a>Zobrazit informace na zónu


## <a name="view-a-device-map-for-a-zone"></a>Zobrazení mapy zařízení v zóně

Zobrazí mapu zařízení pro vybranou zónu na senzoru. V tomto zobrazení se zobrazují všechny síťové prvky související s vybranou zónou, včetně senzorů, zařízení, která jsou k nim připojená, a dalších informací.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="Snímek obrazovky s mapou zóny":::


- V okně **Správa lokality** vyberte možnost **Zobrazit mapu zóny** z panelu, který obsahuje název zóny.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="Výchozí oblast pro výchozí organizační jednotku.":::

Zobrazí se okno **Mapa zařízení** . Následující nástroje jsou k dispozici pro zobrazení zařízení a informací o zařízení z mapy. Podrobnosti o každé z těchto funkcí najdete v tématu *Příručka pro uživatele v programu Defender for IoT Platform*.

- **Zobrazení přiblížení mapy**: zjednodušené zobrazení, zobrazení připojení a podrobné zobrazení. Zobrazené zobrazení mapy se liší v závislosti na úrovni přiblížení mapy. Můžete přepínat mezi zobrazeními mapy úpravou úrovní přiblížení.

  :::image type="icon" source="media/how-to-work-with-asset-inventory-information/zoom-icon.png" border="false":::

- **Nástroje pro vyhledávání a rozložení mapy**: nástroje sloužící k zobrazení různých segmentů sítě, zařízení, skupin zařízení nebo vrstev.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/search-and-layout-tools.png" alt-text="Snímek obrazovky zobrazení nástrojů pro hledání a rozložení":::

- **Popisky a indikátory na zařízeních:** Například počet zařízení seskupených v podsíti v síti IT. V tomto příkladu je to 8.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/labels-and-indicators.png" alt-text="Snímek obrazovky s popisky a indikátory.":::

- **Zobrazit vlastnosti zařízení**: například senzor, který monitoruje zařízení a základní vlastnosti zařízení. Kliknutím pravým tlačítkem myši na zařízení zobrazíte vlastnosti zařízení.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-properties-v2.png" alt-text="Snímek obrazovky s zobrazením vlastností zařízení":::

- **Výstraha přidružená k zařízení:** Kliknutím pravým tlačítkem myši na zařízení zobrazíte související výstrahy.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/show-alerts.png" alt-text="Snímek obrazovky s zobrazením zobrazit výstrahy":::

## <a name="view-alerts-associated-with-a-zone"></a>Zobrazení výstrah přidružených k zóně

Zobrazení výstrah přidružených ke konkrétní zóně:

- Vyberte ikonu výstrahy vytvořit okno **zóny** . 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="Výchozí zobrazení obchodní jednotky s příklady.":::

Další informace najdete v tématu [Přehled: práce s výstrahami](how-to-work-with-alerts-on-premises-management-console.md).

### <a name="view-the-device-inventory-of-a-zone"></a>Zobrazení inventáře zařízení v zóně

Zobrazení inventáře zařízení přidruženého ke konkrétní zóně:

- V okně **zóna** vyberte **Zobrazit inventář zařízení** .

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="Zobrazí se obrazovka inventáře zařízení.":::

Další informace najdete v tématu [prozkoumání všech detekcí podnikového senzoru v inventáři zařízení](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md).

## <a name="view-additional-zone-information"></a>Zobrazit další informace o zóně

K dispozici jsou tyto další informace o zóně:

- **Podrobnosti zóny**: zobrazení počtu zařízení, výstrah a senzorů přidružených k zóně.

- **Podrobnosti snímače**: zobrazí název, IP adresu a verzi každého snímače přiřazeného k zóně.

- **Stav připojení**: Pokud je senzor odpojený, připojte se ze senzoru. Viz [propojení senzorů s místní konzolou pro správu](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console). 

- **Průběh aktualizace**: Pokud se upgraduje připojený senzor, zobrazí se stav upgradu. Během upgradu neobdrží místní Konzola pro správu informace o zařízení ze senzoru.

## <a name="next-steps"></a>Další kroky

[Získání přehledu o globálních, regionálních a místních hrozbách](how-to-gain-insight-into-global-regional-and-local-threats.md)
