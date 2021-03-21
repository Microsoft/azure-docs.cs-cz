---
title: Informace o zařízeních zjištěných všemi podnikovými senzory
description: Pomocí inventáře zařízení v místní konzole pro správu získáte ucelený přehled o zařízeních od připojených senzorů. Tyto informace můžete spravovat pomocí nástrojů pro import, export a filtrování.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 9da5c8c89ee124e527584164b21b096ac815e5ca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100524021"
---
# <a name="investigate-all-enterprise-sensor-detections-in-the-device-inventory"></a>Prozkoumat všechna zjišťování podnikového senzoru v inventáři zařízení

Informace o zařízení z připojených senzorů můžete zobrazit pomocí *inventáře zařízení* v místní konzole pro správu. Tato funkce poskytuje ucelený přehled o všech síťových informacích. Tyto informace můžete spravovat pomocí nástrojů pro import, export a filtrování. Zobrazí se také stavové informace o verzích připojeného snímače.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-data-table.png" alt-text="Snímek obrazovky s tabulkou dat inventáře zařízení":::

Následující tabulka popisuje sloupce tabulky v inventáři zařízení.

| Parametr | Popis |
|--|--|
| **Nepotvrzené výstrahy** | Počet neošetřených výstrah přidružených k tomuto zařízení. |
| **Organizační jednotka** | Organizační jednotka, která obsahuje toto zařízení. |
| **Oblast** | Oblast, která obsahuje toto zařízení. |
| **Web** | Lokalita, která obsahuje toto zařízení. |
| **Zóna** | Zóna, která obsahuje toto zařízení. |
| **Náplně** | Azure Defender pro IoT snímač, který chrání toto zařízení. |
| **Název** | Název tohoto zařízení jako Defender pro IoT, který ho zjistil |
| **Typ** | Typ zařízení, například PLC nebo HMI. |
| **Dodavatel** | Název dodavatele zařízení, jak je definován v adrese MAC. |
| **Operační systém** | Operační systém zařízení. |
| **Firmware** | Firmware zařízení. |
| **IP adresa** | IP adresa zařízení. |
| **REŽIM** | SÍŤ VLAN zařízení. |
| **Adresa MAC** | Adresa MAC zařízení. |
| **Protokoly** | Protokoly, které zařízení používá. |
| **Nepotvrzené výstrahy** | Počet neošetřených výstrah přidružených k tomuto zařízení. |
| **Je autorizován** | Stav autorizace zařízení:<br />- **True**: zařízení je autorizované.<br />- **False**: zařízení není autorizované. |
| **Se označuje jako skener** | Určuje, zda toto zařízení provádí v síti aktivity typu prohledávání. |
| **Je programovací zařízení** | Bez ohledu na to, jestli se jedná o programové zařízení:<br />- **True**: zařízení provádí programovací činnosti pro PLCs, RTUs a řadiče, které jsou relevantní pro technické stanice.<br />- **False**: zařízení není programovací zařízení. |
| **Skupiny** | Skupiny, ve kterých se toto zařízení účastní |
| **Poslední aktivita** | Poslední aktivita, kterou zařízení provedlo. |
| **Zjistil** | Když se toto zařízení poprvé zobrazilo v síti. |

## <a name="integrate-data-into-the-enterprise-device-inventory"></a>Integrace dat do inventáře podnikových zařízení

Funkce integrace dat umožňují vylepšit data v inventáři zařízení s informacemi z dalších podnikových prostředků. Mezi tyto zdroje patří CMDBs, DNS, brány firewall a webová rozhraní API.

Tyto informace můžete použít k tomu, abyste se dozvěděli. Například:

- Data o nákupu zařízení a data ukončení záruky

- Uživatelé odpovědní za jednotlivá zařízení

- Otevřené lístky pro zařízení

- Poslední datum upgradu firmwaru

- Zařízení s povoleným přístupem k Internetu

- Zařízení spouštějící aktivní antivirové aplikace

- Uživatelé přihlášení k zařízením

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-screen-with-items-highlighted-v2.png" alt-text="Tabulka dat na obrazovce inventáře zařízení":::

Data můžete integrovat podle těchto údajů:

- Ruční přidání

- Spouštění přizpůsobených skriptů, které Defender pro IoT nabízí

:::image type="content" source="media/how-to-work-with-asset-inventory-information/enterprise-data-integrator-graph.png" alt-text="Diagram integrátoru podnikových dat":::

Pokud chcete nastavit systém pro příjem dotazů webového rozhraní API, můžete pracovat s Defenderem pro technickou podporu IoT.

Ruční přidání dat:

1. V postranní nabídce vyberte **inventář zařízení** a pak vyberte :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-settings-v2.png" alt-text="Upravte nastavení inventáře zařízení.":::

2. V dialogovém okně **Nastavení inventáře zařízení** vyberte **Přidat vlastní sloupec**.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="Přidejte do svého inventáře vlastní sloupec.":::

3. V dialogovém okně **Přidat vlastní sloupec** přidejte nový název sloupce (až 250 znaků UTF), vyberte **ručně** a vyberte **Uložit**. Nová položka se zobrazí v dialogovém okně **Nastavení inventáře zařízení** .

4. V pravém horním rohu okna **inventáře zařízení** vyberte :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: a vyberte **exportovat všechny inventáře zařízení**. Soubor CSV se vygeneruje.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/sample-exported-csv-file.png" alt-text="Exportovaný soubor CSV.":::

5. Ručně přidejte informace do nového sloupce a soubor uložte.

6. V pravém horním rohu okna **inventáře zařízení** vyberte :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: , vyberte **importovat ruční vstupní sloupce** a přejděte k souboru CSV. Nová data se zobrazí v tabulce **inventáře zařízení** .

Integrace dat z jiných podnikových entit:

1. V pravém horním rohu okna **inventáře zařízení** vyberte :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: a vyberte **exportovat všechny inventáře zařízení**.

2. V dialogovém okně **Nastavení inventáře zařízení** vyberte **Přidat vlastní sloupec**.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="Přidejte do svého inventáře vlastní sloupec.":::

3. V dialogovém okně **Přidat vlastní sloupec** přidejte nový název sloupce (až 250 znaků UTF) a pak vyberte **automaticky**. Zobrazí se možnosti **Nahrát skript** a **testovací skript** .

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column-automatic.png" alt-text="Automaticky přidat vlastní sloupce.":::

4. Nahrajte a otestujte skript, který jste dostali z [Podpora Microsoftu](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="retrieve-information-from-the-device-inventory"></a>Načtení informací z inventáře zařízení

Můžete získat rozsáhlou škálu informací o zařízeních zjištěných spravovanými senzory a integrovat tyto informace s partnerskými systémy. Můžete například načíst senzor, zónu, ID webu, IP adresu, adresu MAC, firmware, protokol a informace o dodavatelích. Filtrovat informace, které načtete na základě:

- Autorizovaných a neautorizovaných zařízení.

- Zařízení přidružená k určitým lokalitám

- Zařízení přidružená ke konkrétním zónám.

- Zařízení přidružená k určitým senzorům.

K načtení a integraci těchto informací můžete použít příkazy v programu Defender pro IoT API. Další informace najdete v tématu [Defender pro rozhraní API pro senzory rozhraní IoT a konzolu pro správu](references-work-with-defender-for-iot-apis.md).

## <a name="filter-the-device-inventory"></a>Filtrování inventáře zařízení

Můžete filtrovat inventář zařízení a zobrazit tak sloupce zájmu. Můžete například zobrazit informace o zařízení PLC.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-view-v2.png" alt-text="Snímek obrazovky inventáře zařízení.":::

Filtr je po opuštění okna vymazán.

Chcete-li použít stejný filtr několikrát, můžete uložit filtr nebo kombinaci filtrů, které potřebujete. Můžete otevřít levé podokno a zobrazit filtry, které jste uložili:

:::image type="content" source="media/how-to-work-with-asset-inventory-information/view-your-asset-inventories-v2.png" alt-text="Obrazovka inventáře zařízení.":::

Filtrování inventáře zařízení:

1. Ve sloupci, který chcete filtrovat, vyberte :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-a-column-icon.png" border="false"::: .

2. V dialogovém okně **Filtr** vyberte typ filtru:

   - **Equals**: přesná hodnota, podle které chcete sloupec filtrovat. Pokud například filtrujete sloupec Protocol v závislosti na **hodnotě Equals** a `value=ICMP` , bude ve sloupci uvedena pouze zařízení, která používají protokol ICMP.

   - **Obsahuje**: hodnota, která je obsažená mezi ostatními hodnotami ve sloupci. Pokud například filtrujete sloupec Protocol podle **obsahuje** a `value=ICMP` , ve sloupci se zobrazí zařízení, která používají protokol ICMP jako součást seznamu protokolů, které zařízení používá.

3. Chcete-li uspořádat informace o sloupci podle abecedního pořadí, vyberte :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: . Uspořádejte objednávku výběrem :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: šipek a.

4. Pokud chcete uložit nový filtr, definujte filtr a vyberte **Uložit jako**.

5. Chcete-li změnit definice filtru, změňte definice a vyberte **Uložit změny**.

## <a name="view-device-information-per-zone"></a>Zobrazit informace o zařízení na zónu

Můžete se seznámit s následujícími informacemi o zařízeních v zóně.

### <a name="view-a-device-map"></a>Zobrazit mapu zařízení

Zobrazení mapy zařízení snímače pro vybranou zónu:

- V okně **Správa lokality** vyberte možnost **Zobrazit mapu zóny** z panelu, který obsahuje název zóny.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="Výchozí oblast pro výchozí organizační jednotku.":::

Zobrazí se okno **Mapa zařízení** . Zobrazuje všechny síťové prvky související s vybranou zónou, včetně senzorů, zařízení, která jsou k nim připojená, a dalších informací.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="Snímek obrazovky s mapou zóny":::

Následující nástroje jsou k dispozici pro zobrazení zařízení a informací o zařízení z mapy. Podrobnosti o každé z těchto funkcí najdete v tématu *Příručka pro uživatele v programu Defender for IoT Platform*.

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

### <a name="view-alerts-associated-with-a-zone"></a>Zobrazení výstrah přidružených k zóně

Zobrazení výstrah přidružených ke konkrétní zóně:

- Vyberte ikonu výstrahy vytvořit okno **zóny** . 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="Výchozí zobrazení obchodní jednotky s příklady.":::

Další informace najdete v tématu [Přehled: práce s výstrahami](how-to-work-with-alerts-on-premises-management-console.md).

### <a name="view-the-device-inventory-of-a-zone"></a>Zobrazení inventáře zařízení v zóně

Zobrazení inventáře zařízení přidruženého ke konkrétní zóně:

- V okně **zóna** vyberte **Zobrazit inventář zařízení** .

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="Zobrazí se obrazovka inventáře zařízení.":::

Další informace najdete v tématu [prozkoumání všech detekcí podnikového senzoru v inventáři zařízení](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md).

### <a name="view-additional-zone-information"></a>Zobrazit další informace o zóně

K dispozici jsou tyto další informace o zóně:

- **Podrobnosti zóny**: zobrazení počtu zařízení, výstrah a senzorů přidružených k zóně.

- **Podrobnosti snímače**: zobrazí název, IP adresu a verzi každého snímače přiřazeného k zóně.

- **Stav připojení**: Pokud je senzor odpojený, připojte se ze senzoru. Viz [propojení senzorů s místní konzolou pro správu](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console). 

- **Průběh aktualizace**: Pokud se upgraduje připojený senzor, zobrazí se stav upgradu. Během upgradu neobdrží místní Konzola pro správu informace o zařízení ze senzoru.

## <a name="see-also"></a>Viz také

[Prošetření detekovaných senzorů v inventáři zařízení](how-to-investigate-sensor-detections-in-a-device-inventory.md)
