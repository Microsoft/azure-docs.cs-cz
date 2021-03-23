---
title: Filtrování a správa výstrah ze stránky s výstrahami
description: Zobrazit výstrahy podle různých kategorií a pomocí funkcí pro vyhledávání pomůžou najít výstrahy, které vás zajímají.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 3/22/2021
ms.topic: how-to
ms.openlocfilehash: 391d1872124c7332bdaa6008a244490b47df4bf7
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781581"
---
# <a name="filter-and-manage-alerts-from-the-alerts-page"></a>Filtrování a správa výstrah ze stránky s výstrahami 

Tento článek popisuje, jak zobrazit výstrahy aktivované senzorem a jak je spravovat pomocí nástrojů výstrah.

Výstrahy můžete zobrazit na základě různých kategorií, jako jsou například výstrahy, které byly archivovány nebo připnuté. Můžete také vyhledat výstrahy týkající se zájmu, například výstrahy na základě IP adresy nebo adresy MAC.  

Výstrahy můžete také zobrazit na řídicím panelu senzoru.

Zobrazení výstrah:

- V postranní nabídce vyberte **výstrahy** . V okně výstrahy se zobrazují výstrahy, které senzor rozpoznal.

  :::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-screen.png" alt-text="Zobrazení obrazovky s výstrahami":::

## <a name="view-alerts-by-category"></a>Zobrazit výstrahy podle kategorie

Výstrahy můžete zobrazit podle různých kategorií v hlavním zobrazení **výstrahy** . Vyberte výstrahu, chcete-li zkontrolovat podrobnosti a spravovat událost.

| Seřadit podle typu | Popis |
|--|--|
| **Důležité výstrahy** | Výstrahy seřazené podle důležitosti. |
| **Připnuté výstrahy** | Upozorňuje, že uživatel připnul k dalšímu šetření. Připnuté výstrahy nejsou archivovány a jsou uloženy po dobu 14 dní v připnuté složce. |
| **Nedávné výstrahy** | Výstrahy seřazené podle času. |
| **Potvrzené výstrahy** | Výstrahy, které byly potvrzené a neošetřené nebo které byly ztlumené a Neztlumené. |
| **Archivované výstrahy** | Upozorňuje, že se systém automaticky archivoval. K nim má přístup jenom uživatel s oprávněním správce. |

## <a name="search-for-alerts-of-interest"></a>Vyhledat výstrahy týkající se zájmu

Hlavní zobrazení výstrahy nabízí různé funkce hledání, které vám pomůžou najít výstrahy, které vás zajímají.

:::image type="content" source="media/how-to-work-with-alerts-sensor/main-alerts-view.png" alt-text="Snímek obrazovky s výukou výstrah":::

### <a name="text-search"></a>Hledání textu

Pomocí možnosti bezplatné hledání můžete vyhledat výstrahy podle textu, číslic nebo znaků.

Pro vyhledávání:

- Do pole bezplatné hledání zadejte požadovaný text a stiskněte klávesu ENTER na své klávesnici.

Vymazání hledání:

- Odstraňte text v poli bezplatné hledání a stiskněte klávesu ENTER na své klávesnici.

### <a name="device-group-or-device-ip-address-search"></a>Hledání podle skupin zařízení nebo IP adres zařízení

Vyhledejte výstrahy, které odkazují na konkrétní IP adresy nebo skupiny zařízení. Skupiny zařízení se vytvářejí v mapě zařízení.

Použití rozšířených filtrů:

1. V hlavním zobrazení **výstrahy** vyberte **Rozšířené filtry** .

2. Vyberte skupinu zařízení nebo zařízení.

3. Vyberte **Potvrdit**.

4. Chcete-li vymazat hledání, vyberte možnost **Zrušit vše**.

### <a name="security-versus-operational-alert-search"></a>Vyhledávání v závislosti na zabezpečení a provozních výstrahách

Přepínání mezi zobrazením provozu a výstrah zabezpečení:

- Výstrahy **zabezpečení** reprezentují potenciální provoz malwaru, anomálie sítě, porušení zásad a porušení protokolu.

- **Provozní** výstrahy reprezentují anomálie sítě, porušení zásad a porušení protokolu.

Když není vybraná žádná z možností, zobrazí se všechny výstrahy.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-security.png" alt-text="Zabezpečení na obrazovce výstrahy.":::

## <a name="alert-page-options"></a>Možnosti stránky výstrahy

Zprávy upozorňující na zprávy obsahují tyto akce:

- Tuto možnost vyberte :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-an-alert-icon.png" border="false"::: , pokud chcete upozornění potvrdit.

- Tuto možnost vyberte :::image type="icon" source="media/how-to-work-with-alerts-sensor/unacknowledge-an-alert-icon.png" border="false"::: , pokud chcete výstrahu Nepotvrdit.

- Tuto možnost vyberte :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: , pokud chcete výstrahu připnout.

- Tuto možnost vyberte :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: , pokud chcete odebrat upozornění.

- Tuto možnost vyberte :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-all-alerts-icon.png" border="false"::: , pokud chcete potvrdit všechny výstrahy.

- Vyberte, :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-and-acknowledge-all-alerts.png" border="false"::: Chcete-li zjistit a potvrdit všechny výstrahy.

- Tuto možnost vyberte, pokud chcete :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-csv.png" border="false"::: exportovat informace o výstraze do souboru. csv. Pomocí možnosti **exportovat rozšířené výstrahy** můžete exportovat informace o výstrahách v samostatných řádcích pro každou výstrahu, která pokrývá více zařízení.

## <a name="alert-pop-up-window-options"></a>Možnosti překryvného okna výstrahy

- Vyberte :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-pdf.png" border="false"::: ikonu pro stažení zprávy o výstrahách jako soubor PDF.

- Vyberte :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-pcap.png" border="false"::: ikonu pro stažení souboru PCAP. Soubor je zobrazitelný pomocí nástroje Wireshark, což je nástroj pro uvolnění bezplatného síťového protokolu.

- Tuto možnost vyberte, pokud :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-filtered-pcap.png" border="false"::: chcete stáhnout filtrovaný soubor pcap, který obsahuje jenom příslušné pakety výstrah. tím se zmenší velikost výstupního souboru a umožní se vám lépe zaměřené analýzy. Soubor můžete zobrazit pomocí nástroje Wireshark.

- Vyberte :::image type="icon" source="media/how-to-work-with-alerts-sensor/show-alert-in-timeline.png" border="false"::: ikonu, která zobrazí výstrahu v časové ose události.

- Vyberte :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: ikonu pro připnutí výstrahy.

- Kliknutím na :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: ikonu tuto výstrahu ododepnout.

- Tuto možnost vyberte :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-icon.png" border="false"::: , pokud chcete provoz schválit (jenom analytiké a správci zabezpečení).

- Vyberte zařízení, které chcete zobrazit v mapě zařízení.

## <a name="next-steps"></a>Další kroky

[Správa události upozornění](how-to-manage-the-alert-event.md)

[Urychlení pracovních postupů výstrah](how-to-accelerate-alert-incident-response.md)
