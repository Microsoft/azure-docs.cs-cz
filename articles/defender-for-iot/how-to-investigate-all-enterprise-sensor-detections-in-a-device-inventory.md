---
title: Informace o zařízeních zjištěných všemi podnikovými senzory
description: Pomocí inventáře zařízení v místní konzole pro správu získáte ucelený přehled o zařízeních od připojených senzorů. Tyto informace můžete spravovat pomocí nástrojů pro import, export a filtrování.
ms.date: 12/02/2020
ms.topic: how-to
ms.openlocfilehash: 0ae59123b59cfb54cba2a2ee9bdeefb411c8793b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782176"
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

## <a name="next-steps"></a>Další kroky

[Prošetření detekovaných senzorů v inventáři zařízení](how-to-investigate-sensor-detections-in-a-device-inventory.md)
