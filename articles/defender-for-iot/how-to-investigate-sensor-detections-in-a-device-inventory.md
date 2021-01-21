---
title: Získání přehledu o zařízeních zjištěných konkrétním senzorem
description: Inventář zařízení zobrazuje rozsáhlou škálu atributů zařízení, které senzor detekuje.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 9c868685ad943c1ab9ab263a164111e46294c042
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625427"
---
# <a name="investigate-sensor-detections-in-a-device-inventory"></a>Prošetření detekovaných senzorů v inventáři zařízení

Inventář zařízení zobrazuje rozsáhlou škálu atributů zařízení, které senzor detekuje. Možnosti jsou k dispozici pro:

 - Tyto informace můžete snadno filtrovat.

 - Exportujte informace do souboru CSV.

 - Importujte podrobnosti registru Windows.

 - Vytvořte skupiny pro zobrazení v mapě zařízení.

## <a name="view-device-attributes-in-the-device-inventory"></a>Zobrazení atributů zařízení v inventáři zařízení

V tabulce inventáře zařízení se zobrazí následující atributy.

| Parametr | Popis |
|--|--|
| Název | Název zařízení jako senzor, který ho zjistil. |
| Typ | Typ zařízení. |
| Dodavatel | Název dodavatele zařízení, jak je definován v adrese MAC. |
| Operační systém | Operační systém zařízení. |
| Firmware | Firmware zařízení. |
| IP adresa | IP adresa zařízení. |
| REŽIM | SÍŤ VLAN zařízení. Podrobnosti o tom, jak senzoru zjišťovat sítě VLAN, najdete v tématu [definice názvů sítí VLAN](how-to-manage-the-on-premises-management-console.md#define-vlan-names). (postupy: definování-Management-Console-Network-Settings. MD # define-VLAN-Names). |
| Adresa MAC | Adresa MAC zařízení. |
| Protokoly | Protokoly, které zařízení používá. |
| Nepotvrzené výstrahy | Počet nepotvrzených výstrah přidružených k tomuto zařízení. |
| Je autorizován | Stav autorizace definovaný uživatelem:<br />- **True**: zařízení je autorizované.<br />- **False**: zařízení není autorizované. |
| Se označuje jako skener | Definováno jako skenovací zařízení uživatelem. |
| Je programovací zařízení | Definováno jako autorizované programovací zařízení uživatelem. <br />- **True**: zařízení provádí programovací činnosti pro PLCs, RTUs a řadiče, které jsou relevantní pro technické stanice. <br />- **False**: zařízení není programovací zařízení. |
| Skupiny | Skupiny, ve kterých se toto zařízení účastní. |
| Poslední aktivita | Poslední aktivita, kterou zařízení provedlo. |
| Zjištěno | Když se toto zařízení poprvé zobrazilo v síti. |

Postup zobrazení inventáře zařízení:

1. V levém podokně vyberte **zařízení**. Na pravé straně se otevře podokno **zařízení** .

2. V podokně **zařízení** vyberte :::image type="icon" source="media/how-to-work-with-asset-inventory-information/device-pane-icon.png" border="false"::: .

Chcete-li skrýt a zobrazit sloupce, upravte tabulku inventáře zařízení:

1. V nabídce vpravo nahoře v inventáři zařízení vyberte :::image type="icon" source="media/how-to-work-with-asset-inventory-information/settings-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-settings-screens-v2.png" alt-text="Obrazovka nastavení inventáře zařízení.":::

2. V okně **Nastavení inventáře zařízení** vyberte sloupce, které chcete zobrazit v tabulce inventáře zařízení.

3. Změňte umístění sloupců v tabulce pomocí šipek.

4. Vyberte **Uložit**. Okno **Nastavení inventáře zařízení** se zavře a nové nastavení se zobrazí v tabulce.

### <a name="create-temporary-device-inventory-filters"></a>Vytvořit dočasné filtry inventáře zařízení

Můžete nastavit filtr, který definuje informace zobrazené v tabulce. Můžete se třeba rozhodnout, že chcete zobrazit jenom informace o zařízení PLC.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/devices-learning-v2.png" alt-text="Učení zařízení.":::

Filtr se při opuštění inventáře neuloží.

### <a name="save-device-inventory-filters"></a>Uložení filtrů inventáře zařízení

Filtr nebo kombinaci filtrů, které potřebujete, můžete uložit a znovu je použít v inventáři zařízení. Vytvořte širší filtry na základě určitého typu zařízení nebo dalších úzkých filtrů na základě konkrétního typu a konkrétního protokolu.

Filtry, které uložíte, jsou také uloženy jako skupiny map zařízení. Tato funkce poskytuje další úroveň členitosti při zobrazení síťových zařízení na mapě.

Vytvoření filtrů:

1. Ve sloupci, který chcete filtrovat, vyberte :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-icon.png" border="false"::: .

2. V dialogovém okně **Filtr** vyberte typ filtru:

   - **Equals**: přesná hodnota, podle které chcete sloupec filtrovat. Pokud například filtrujete sloupec Protocol v závislosti na **hodnotě Equals** a `value=ICMP` , bude ve sloupci uvedena pouze zařízení, která používají protokol ICMP.

   - **Obsahuje**: hodnota, která je obsažená mezi ostatními hodnotami ve sloupci. Pokud například filtrujete sloupec Protocol podle **obsahuje** a `value=ICMP` , ve sloupci se zobrazí zařízení, která používají protokol ICMP jako součást seznamu protokolů, které zařízení používá.

3. Chcete-li uspořádat informace o sloupci podle abecedního pořadí, vyberte :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: . Uspořádejte objednávku výběrem :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: šipek a.

4. Pokud chcete uložit nový filtr, definujte filtr a vyberte **Uložit jako**.

5. Chcete-li změnit definice filtru, změňte definice a vyberte **Uložit změny**.

Zobrazení filtrů:

- Otevřete levé podokno a zobrazte filtry, které jste uložili:

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-from-left-pane-v2.png" alt-text="Zobrazit filtry v levém podokně.":::

### <a name="view-filtered-information-as-a-map-group"></a>Zobrazení filtrovaných informací jako skupiny mapování

Když přepnete do zobrazení mapy, filtrovaná zařízení se zvýrazní a filtrují. Skupina filtru, kterou jste uložili, se zobrazí v nabídce vedle skupiny **filtry inventáře zařízení** .

:::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-in-the-map-view-v2.png" alt-text="Zobrazit filtry v zobrazení mapy":::

## <a name="learn-windows-registry-details"></a>Podrobnosti o registru Windows

Kromě kurzů pro zařízení můžete zjistit zařízení, včetně pracovních stanic a serverů Microsoft Windows. Tato zařízení se také zobrazují v inventáři zařízení. Po zjištění zařízení můžete inventář zařízení rozšířit o podrobné informace o Windows, třeba:

- Nainstalovaná verze Windows

- Nainstalované aplikace

- Informace na úrovni opravy

- Otevřené porty

- Robustnější informace o verzích operačních systémů

Pro načtení těchto informací jsou k dispozici dvě možnosti:

- Aktivní cyklické dotazování pomocí naplánovaných kontrol služby WMI. 

- Místní průzkum distribucí a spuštěním skriptu na zařízení. Práce s místními skripty obchází rizika spuštění cyklického dotazování rozhraní WMI na koncový bod. Je to také užitečné pro regulované sítě s vodopády a jednosměrnými prvky.

Tento článek popisuje, jak místně prozjišťovat Registry Windows Endpoint pomocí skriptu. Tyto informace budou použity pro generování výstrah, oznámení, sestav dolování dat, posouzení rizik a sestavy vektorů útoku.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/data-mining-screen.png" alt-text="Snímek obrazovky dolování dat.":::

Můžete zjišťovat následující operační systémy Windows:

- Windows XP

- Windows 2000

- Windows NT

- Windows 7

- Windows 10

- Windows Server 2003/2008/2012/2016

### <a name="before-you-begin"></a>Než začnete

Chcete-li pracovat se skriptem, je nutné splnit následující požadavky:

- Ke spuštění skriptu na zařízení jsou nutná oprávnění správce.

- Senzor by již měl zařízení se systémem Windows naučeno. To znamená, že pokud zařízení již existuje, skript načte příslušné informace.

- Senzor sleduje síť, ke které je počítač s Windows připojen.

### <a name="acquire-the-script"></a>Získání skriptu

Pokud chcete získat skript, [obraťte se na zákaznickou podporu](mailto:support.microsoft.com).

### <a name="deploy-the-script"></a>Nasazení skriptu

Skript můžete nasadit jednou nebo naplánovat probíhající dotazy pomocí standardních metod a nástrojů pro automatizované nasazení.

### <a name="about-the-script"></a>O skriptu

- Skript je spuštěn jako nástroj, nikoli nainstalovaný program. Spuštění skriptu nemá vliv na koncový bod.

- Soubory, které skript vygeneruje, zůstane na místním disku, dokud je neodstraníte.

- Soubory, které skript vygeneruje, jsou umístěny vedle sebe. Oddělte je.

- Pokud skript znovu spustíte ve stejném umístění, tyto soubory se přepíší.

Spuštění skriptu:  

1. Zkopírujte skript na místní disk a rozbalte ho. Zobrazí se následující soubory:

    - start.bat

    - settings.jsna

    - data. bin

    - run.bat

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/files-in-file-explorer.png" alt-text="Zobrazení souborů v Průzkumníkovi souborů.":::

2. Spusťte `run.bat` soubor.

3. Po dokončení testu registru se zobrazí soubor sady CX-Snapshot s informacemi v registru.

4. Název souboru označuje systémový název a datum a čas snímku. Příkladem názvu souboru je `CX-snaphot_SystemName_Month_Year_Time` .

### <a name="import-device-details"></a>Importovat podrobnosti o zařízení

Informace zjištěné u každého koncového bodu by měly být naimportovány do snímače.

Soubory vygenerované z dotazů lze umístit do jedné složky, ke které můžete přistupovat ze senzorů. Pomocí standardních automatizovaných metod a nástrojů můžete přesunout soubory z každého koncového bodu Windows do umístění, kam je budete importovat do snímače.

Neaktualizujte názvy souborů.

Import:

1. V dialogovém okně **Importovat konfiguraci systému Windows** vyberte **importovat nastavení** .

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/import-windows-configuration-v2.png" alt-text="Importujte konfigurace systému Windows.":::

2. Vyberte **Přidat** a potom vyberte všechny soubory (CTRL + A).

3. Vyberte **Zavřít**. Informace registru zařízení se importují. Pokud dojde k potížím při nahrávání jednoho ze souborů, budete informováni o tom, který soubor se nepovedlo nahrát.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-new-file.png" alt-text="Nahrání přidaných souborů bylo úspěšné.":::

## <a name="export-device-inventory-information"></a>Exportovat informace o inventáři zařízení

Informace o inventáři zařízení můžete exportovat do excelového souboru. Importované informace přepisují aktuální informace.

Export souboru CSV:

- V nabídce vpravo nahoře v inventáři zařízení vyberte :::image type="icon" source="media/how-to-work-with-asset-inventory-information/csv-excel-export-icon.png" border="false"::: . Sestava sdíleného svazku clusteru je vygenerována a stažena.

## <a name="see-also"></a>Viz také

[Prošetření všech detekovaných podnikových senzorů v inventáři zařízení](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)

[Práce s zobrazeními map webu](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)
