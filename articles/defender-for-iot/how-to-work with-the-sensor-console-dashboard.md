---
title: Práce s řídicím panelem konzoly snímače
description: Řídicí panel vám umožní rychle zobrazit stav zabezpečení vaší sítě. Poskytuje přehled o hrozbách pro celý systém na časové ose spolu s informacemi o souvisejících zařízeních.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/03/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: eb37434213dd756ba5d7137b93a1cd37da5bb9ae
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523630"
---
# <a name="the-dashboard"></a>Řídicí panel

Řídicí panel vám umožní rychle zobrazit stav zabezpečení vaší sítě. Poskytuje přehled o hrozbách pro celý systém na časové ose spolu s informacemi o souvisejících zařízeních, včetně těchto:

- Výstrahy na různých úrovních závažnosti:

- Kritické

- Hlavní

- Vedlejší

- Upozornění

- Dva indikátory uprostřed stránky zobrazují pakety za sekundu (PPS) a nepotvrzené výstrahy (UA). **PPS** je počet paketů potvrzených systémem za sekundu. **UA** je počet výstrah, které ještě nebyly potvrzeny.

- Seznam nepotvrzených výstrah s jejich popisem.

- Časová osa s popisem výstrahy

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/dashboard-alert-screen-v2.png" alt-text="Řídicí panel":::

## <a name="viewing-the-latest-alerts"></a>Zobrazují se nejnovější výstrahy.

Měřidlo nepotvrzených výstrah (UA) ve středu stránky indikuje počet takových výstrah. Chcete-li zobrazit seznam výstrah, v dolní části stránky řídicího panelu vyberte **Další výstrahy** nebo v nabídce na straně vyberte **výstrahy** .

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unhandled-alerts-list.png" alt-text="Nepotvrzené výstrahy":::

## <a name="status-boxes"></a>Stavová pole

Každé pole stav je popsané v této části.

| Stavové pole a měřidla | Popis |
| -------------- | -------------- |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/critical-alert-status-box-v2.png" alt-text="Kritické výstrahy"::: | **Kritické výstrahy** – pole v horní polovině stránky indikuje počet kritických výstrah. Toto políčko zaškrtněte, pokud chcete zobrazit popisy výstrah na časové ose a v seznamu pod měřidly.                              |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/major-alert-status-box-v2.png" alt-text="Hlavní upozornění"::: | **Hlavní upozornění** – pole v pravém horním rohu stránky indikuje počet hlavních výstrah. Toto políčko zaškrtněte, pokud chcete zobrazit popisy výstrah na časové ose a v seznamu pod měřidly.                                     |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/minor-alert-status-box-v2.png" alt-text="Vedlejší výstrahy"::: | **Vedlejší výstrahy** – pole v levém dolním rohu stránky indikuje počet vedlejších výstrah. Toto políčko zaškrtněte, pokud chcete zobrazit popisy výstrah na časové ose a v seznamu pod měřidly.                                   |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/warnings-alert-status-box-v2.png" alt-text="Varovné výstrahy"::: | **Upozornění upozorňující** na pole v dolní polovině stránky indikuje počet upozornění. Toto políčko zaškrtněte, pokud chcete zobrazit popisy výstrah na časové ose a v seznamu pod měřidly.                             |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/all-alert-status-box-v2.png" alt-text="All Alerts"::: | **Všechny výstrahy** – pole v pravém dolním rohu stránky indikuje celkový počet výstrah kritických, hlavních, vedlejších a varovných. Toto políčko zaškrtněte, pokud chcete zobrazit popisy výstrah na časové ose a v seznamu pod měřidly. |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/packets-per-second-gauge-v2.png" alt-text="Pakety za sekundu"::: | **Pakety za sekundu (PPS)** – metrika PPS je ukazatel výkonu sítě. |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unacknowledged-events-gauge-v2.png" alt-text="Nepotvrzené události (UA)"::: | **Nepotvrzené události** – Tato metrika indikuje počet nepotvrzených událostí.

## <a name="using-the-timeline"></a>Používání časové osy

Výstrahy se zobrazují podél svislé časové osy, která obsahuje informace o datu a čase.

Grafickou osu zobrazuje:

- Kritické výstrahy

- Hlavní upozornění

- Vedlejší výstrahy

- Varovné výstrahy

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/timeline-of-events.png" alt-text="Graf časové osy":::

## <a name="viewing-alerts"></a>Zobrazení výstrah

Výběrem šipky dolů **v** dolní části okna výstrahy zobrazíte informace o položkách výstrahy a zařízeních.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/extended-alert-screen.png" alt-text="Informace o položkách a zařízeních výstrah":::

- Vyberte zařízení, na které se má zobrazit mapa fyzického režimu. Zařízení v předmětech jsou zvýrazněna.

- Kliknutím kamkoli do pole výstraha zobrazíte další podrobnosti týkající se výstrahy. Automaticky otevírané okno se zobrazí podobně jako na následujícím obrázku.

- Vyberte :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/excel-icon.png" alt-text="Excel"::: a EXPORTUJTE soubor CSV o výstraze.

- Jenom správci a analytiky zabezpečení – vyberte :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/approve-all-icon.png" alt-text="potvrdit vše"::: a **potvrďte všechny** přidružené výstrahy.

- Vyberte :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pdf-icon.png" alt-text="PDF":::a stáhněte zprávu o výstraze jako soubor PDF.

- Vyberte :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pin-icon.png" alt-text="připnout":::, aby se výstraha mohla připnout nebo odepnout. Výběrem možnosti Připnout ho přidáte do okna s **připojenými výstrahami** na obrazovce **výstrahy** .

- Vyberte :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/download-icon.png" alt-text="Stáhnout"::: a prozkoumejte upozornění stažením souvisejícího souboru PCAP, který obsahuje analýzu síťového protokolu.

- Vyberte :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/cloud-download-icon.png" alt-text="Cloud"::: pro stažení souvisejícího filtrovaného souboru PCAP, který obsahuje jenom pakety související s výstrahou, a tím zmenšete velikost výstupního souboru a umožníte lépe zaměřené analýzy. Můžete ji zobrazit pomocí programu [Wireshark](https://www.wireshark.org/).

- Vyberte možnost :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/navigate-icon.png" alt-text="Navigace"::: a přejděte k časové ose události v době požadované výstrahy. To vám umožní vyhodnotit další události, ke kterým může dojít kolem konkrétní výstrahy.

- Správci a analytici zabezpečení – mění stav výstrahy z nepotvrzeno na potvrzeno. Vyberte vědět ke schválení zjištěné aktivity.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unauthorized-internet-connectivity-detection-v3.png" alt-text="Bylo zjištěno neoprávněné připojení k Internetu.":::

## <a name="next-steps"></a>Další kroky

[Práce s výstrahami na senzoru](how-to-work-with-alerts-on-your-sensor.md)
