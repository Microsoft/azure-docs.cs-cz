---
title: Generování trendů a statistických sestav
description: Získejte přehled o aktivitě sítě, statistiku a trendech pomocí programu Defender pro trendy a statistiky IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/24/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 8bfbacc77843c7978bcb1d364bd93f5e09381144
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98811633"
---
# <a name="sensor-trends-and-statistics-reports"></a>Trendy senzorů a statistiky sestav

## <a name="about-sensor-trends-and-statistics-reports"></a>O trendech senzorů a statistických sestavách

Můžete vytvořit grafy widgetů a výsečové grafy a získat tak přehled o trendech a statistice sítě. Widgety se dají seskupovat v části uživatelsky definované řídicí panely.

> [!NOTE]
> Správci a analytici zabezpečení můžou vytvářet trendy a statistiky sestav.

Řídicí panel obsahuje widgety, které graficky popisují následující typy informací:

- Provoz podle portu
- Šířka pásma kanálu
- Celková šířka pásma
- Aktivní připojení TCP
- Signalizac
  - Nová zařízení
  - Zaneprázdněná zařízení
  - Zařízení podle dodavatele
  - Zařízení podle operačního systému
  - Odpojená zařízení
- Přerušení připojení po hodinách
- Výstrahy pro incidenty podle typu
- Přístup k databázové tabulce
- Inoddílování protokolů – widgety
- Síť Ethernet a IP adresa:
  - Přenos dat Ethernet a IP adres službou CIP
  - Přenos dat Ethernet a IP adres podle třídy CIP
  - Přenos přes síť Ethernet a IP adresy pomocí příkazu
- OPC
  - OPC hlavní operace správy
  - OPC hlavní vstupně-výstupní operace
- S7 pro Siemens:
  - S7 provoz podle řídicí funkce
  - S7 provoz podle podfunkce
- SRTP:
  - SRTP provoz podle kódu služby
  - SRTP chyby podle dne
- SuiteLink:
  - SuiteLink nahoře dotazovaných značek
  - SuiteLink – chování numerických značek
- IEC – 60870 přenosů podle ASDU
- DNP3 provoz podle funkce
- Provoz MMS podle služby
- Modbus provoz podle funkce
- Provoz OPC-UA podle služby

> [!NOTE]
>  Čas v widgetech se nastaví podle času snímače.

## <a name="create-reports"></a>Vytváření sestav

Zobrazení řídicích panelů a widgetů:

V postranní nabídce vyberte **trendy & statistiku** .

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Snímek obrazovky s šetřením":::

Ve výchozím nastavení se pro zjišťování za posledních 7 dnů zobrazí výsledky. Pomocí nástrojů filtru můžete tento rozsah změnit. Například bezplatné hledání textu.

## <a name="see-also"></a>Viz také

[Generování sestav](how-to-create-risk-assessment-reports.md) 
 posouzení rizik Dotazy dolování dat ze [senzorů](how-to-create-data-mining-queries.md) 
 [Vytváření sestav vektorů útoku](how-to-create-attack-vector-reports.md)