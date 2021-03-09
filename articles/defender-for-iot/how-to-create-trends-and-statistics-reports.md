---
title: Generování trendů a statistických sestav
description: Získejte přehled o aktivitě sítě, statistice a trendech pomocí programu Defender pro trendy a statistiky IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 2/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 7cb3848f3711b6bf10c316ba4f5321286e2260fb
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "101706383"
---
# <a name="sensor-trends-and-statistics-reports"></a>Trendy senzorů a statistiky sestav

Můžete vytvořit grafy widgetů a výsečové grafy a získat tak přehled o trendech a statistice sítě. Widgety se dají seskupovat v části uživatelsky definované řídicí panely.

> [!NOTE]
> Správci a analytici zabezpečení můžou vytvářet trendy a statistiky sestav.

Řídicí panel obsahuje widgety, které graficky popisují následující typy informací:

- Provoz podle portu
- Hlavní provoz podle portu
- Šířka pásma kanálu
- Celková šířka pásma
- Aktivní připojení TCP
- Horní šířka pásma podle sítě VLAN
- Signalizac
  - Nová zařízení
  - Zaneprázdněná zařízení
  - Zařízení podle dodavatele
  - Zařízení podle operačního systému
  - Počet zařízení na síť VLAN
  - Odpojená zařízení
- Připojení se zamítá po hodinách.
- Výstrahy pro incidenty podle typu
- Přístup k databázové tabulce
- Inoddílování protokolů – widgety
- DELTAV
  - Distribuce operací DeltaV Roc
  - Události DeltaV Roc podle názvu
  - DeltaV události podle času
- AMS
  - Provoz AMS podle portu serveru
  - Provoz AMS podle příkazu
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
- REŽIM
  - Počet zařízení na síť VLAN
  - Horní šířka pásma podle sítě VLAN
- 60870-5-104
  - IEC – 60870 přenosů podle ASDU
- BACNET
  - Služby BACnet
- DNP3
  - DNP3 provoz podle funkce
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

Ve výchozím nastavení se výsledky pro detekce za posledních sedm dní zobrazují. Pomocí nástrojů filtru můžete tento rozsah změnit. Například bezplatné hledání textu.

## <a name="create-a-dashboard"></a>Vytvoření řídicího panelu

Vytvořte nový řídicí panel výběrem rozevírací nabídky **řídicí panel** . Řídicí panel můžete vytvořit a přidat tolik widgetů.

Přizpůsobené řídicí panely můžete vytvořit pomocí následujících možností:

- Přidání widgetu na řídicí panel

- Odstranění widgetu z řídicího panelu

- Úprava filtru widgetu

- Změna velikosti widgetu

- Změna umístění widgetu

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/pin-a-dashboard.png" alt-text="Změna umístění widgetu":::

Vytvoření přizpůsobeného řídicího panelu:

1. Na levém panelu vyberte **trendy a statistiky** .

1. Vyberte rozevírací nabídku **Vybrat řídicí panel** a klikněte na tlačítko **vytvořit řídicí panel** .

1. Zadejte smysluplný název nového řídicího panelu a vyberte **vytvořit**.

1. V levém horním rohu stránky vyberte **Přidat widget** .

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="Vyberte widget z obchodu widgetů.":::

1. **Zabezpečení** a **provozní** pomůcky jsou k dispozici v pravém horním rohu okna. Vyberte si z různých kategorií a protokoly. U každého widgetu se zobrazí stručný popis s miniaturní grafikou. Pomocí posuvníku můžete zobrazit všechny dostupné pomůcky.

1. Vyberte pomůcku pomocí tlačítka **přidat kliknutím** . Pomůcka se okamžitě zobrazí na řídicím panelu.

Postup odstranění řídicího panelu:

1. Z rozevírací nabídky vyberte název řídicího panelu.

1. Vyberte ikonu **Odstranit** a pak vyberte **OK**.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/garbage-icon.png" alt-text="Vyberte ikonu Odstranit pro odstranění řídicího panelu.":::

Postup úpravy názvu řídicího panelu:

1. Z rozevírací nabídky vyberte název řídicího panelu.

1. Vyberte ikonu **Upravit** .
  
  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/edit-name.png" alt-text="Výběrem ikony upravit upravte název řídicího panelu.":::

1. Zadejte nový název řídicího panelu a vyberte **Uložit**.
 
Nastavení výchozího řídicího panelu:

1. Z rozevírací nabídky vyberte název řídicího panelu.

1. Výběrem ikony **hvězdičky** vyberte řídicí panel, který chcete nastavit jako výchozí řídicí panel.

   :::image type="content" source="media/how-to-create-trends-and-statistics-reports/default-dashboard.png" alt-text="Vyberte ikonu hvězdičky a vyberte výchozí řídicí panel."::: 

Úprava dat filtrování v widgetu:

1. Vyberte ikonu **filtru** .

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/filter-widget.png" alt-text="Vyberte ikonu filtru pro nastavení parametrů widgetu.":::

1. Upravte požadované parametry.

1. Vyberte **OK**.

Postup odstranění widgetu:

- Vyberte :::image type="icon" source="media/how-to-create-trends-and-statistics-reports/x-icon.png" border="false"::: ikonu.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/delete-widget.png" alt-text="Vyberte X, aby se pomůcka odstranila.":::

## <a name="creating-widgets"></a>Vytváření widgetů 

Úložiště widgetů umožňuje vybírat widgety podle kategorie a protokolu. Můžete zobrazit **zabezpečení** nebo **provozní** widgety, které jsou dostupné, a to tak, že je vyberete.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="Vyberte widget z obchodu widgetů.":::

Každá pomůcka obsahuje konkrétní informace o provozu systému, statistikách sítě, Statistika protokolu, zařízení a informace o výstrahách. Zpráva se zobrazí, když pro widget nejsou k dispozici žádná data.

Můžete odebrat část z výsečového grafu ve výsečovém grafu, abyste viděli relativní význam zbylých řezů zřetelněji. Vyberte název řezu v legendě v dolní části obrazovky.

Následující části obsahují příklady případů použití pro několik widgetů.

### <a name="busy-devices-widget"></a>Pomůcka zaneprázdněných zařízení

V této pomůckě je uveden seznam pěti nejoblíbenějších zařízení nejvytíženější. V režimu **úprav** můžete filtrovat podle známých protokolů.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/busy-device.png" alt-text="Zobrazení widgetu zaneprázdněného zařízení.":::

### <a name="total-bandwidth-widget"></a>Widget celkové šířky pásma

Tato pomůcka sleduje šířku pásma v MB/s (megabitů za sekundu). Šířka pásma je uvedena na ose y s datem zobrazeným na ose x. Režim **úprav** umožňuje filtrovat výsledky podle klienta, serveru, portu serveru nebo podsítě. Popisek se zobrazí, když najedete myší na ukazatel myši v grafu.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/total-bandwidth.png" alt-text="Zobrazení celkové pomůcky šířky pásma.":::

### <a name="channels-bandwidth-widget"></a>Pomůcka šířky pásma kanálů

V této pomůckě se zobrazí pět kanálů provozu v horní části. Můžete filtrovat podle adresy a nastavit počet zobrazených výsledků. Výběrem šipky dolů zobrazíte další kanály.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/channels-bandwidth.png" alt-text="Zobrazení widgetu šířky pásma kanálů":::

### <a name="traffic-by-port-widget"></a>Provoz podle widgetu portu

Tato pomůcka zobrazuje provoz podle portu, který je označený pomocí výsečového grafu s každým portem určeným jinou barvou. Objem přenosů v každém portu je úměrný velikosti jeho části.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/traffic-by-port.png" alt-text="Zobrazení provozu podle widgetu portu.":::

### <a name="new-devices-widget"></a>Pomůcka nových zařízení

Tato pomůcka zobrazí pruhový graf nová zařízení, která indikuje, kolik nových zařízení bylo zjištěno k určitému datu.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/new-devices.png" alt-text="Zobrazení widgetu nových zařízení.":::

### <a name="protocol-dissection-widgets"></a>Inoddílování protokolů – widgety

V tomto widgetu se zobrazí výsečový graf, který vám poskytne přehled o provozu na protokol, který je oddělený kódy funkcí a službami. Velikost každého řezu výsečového grafu je úměrná objemu přenosů vzhledem k ostatním řezům.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/protocol-dissection.png" alt-text="Zobrazení widgetu protokolu.":::

### <a name="active-tcp-connections-widget"></a>Widget aktivních připojení TCP

Tento widget zobrazuje graf, který zobrazuje počet aktivních připojení TCP v systému.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/active-tcp.png" alt-text="Zobrazení widgetu aktivních připojení TCP.":::

### <a name="incident-by-type-widget"></a>Incident podle typu widget

Tento widget zobrazuje výsečový graf, který zobrazuje počet incidentů podle typu. Toto je počet výstrah vygenerovaných jednotlivými moduly v rámci předdefinovaného časového období.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/incident-by-type.png" alt-text="Zobrazení incidentu podle typu widget.":::

## <a name="devices-by-vendor-widget"></a>Pomůcka zařízení podle dodavatele

Tento widget zobrazuje výsečový graf, který zobrazuje počet zařízení podle dodavatele. Počet zařízení pro určitého dodavatele je úměrný velikosti části dodavatele daného zařízení vzhledem k ostatním dodavatelům zařízení.

## <a name="number-of-devices-per-vlan-widget"></a>Počet zařízení na widget VLAN

Tento widget zobrazuje výsečový graf, který zobrazuje počet zjištěných zařízení na jednu síť VLAN. Velikost každého řezu výsečového grafu je úměrná počtu zjištěných zařízení relativních k ostatním řezům.

Každá síť VLAN se zobrazí se značkou VLAN přiřazenou snímačem nebo názvem, který jste přidali ručně.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/number-of-devices.png" alt-text="Zobrazení widgetu počtu zařízení.":::

### <a name="top-bandwidth-by-vlan-widget"></a>Nejlepší šířka pásma podle widgetu VLAN

Tento widget zobrazuje spotřebu šířky pásma podle sítě VLAN. Ve výchozím nastavení Widget zobrazuje pět sítí VLAN s nejvyšším využitím šířky pásma.

Data můžete filtrovat podle období prezentovaného v widgetu. Výběrem šipky dolů zobrazíte další výsledky.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/top-bandwidth.png" alt-text="Pohled na horní šířku pásma podle widgetu VLAN.":::

## <a name="system-report"></a>Sestava systému

Stažení systémové sestavy: 

1. V postranní nabídce vyberte **trendy & statistiku** .

1. V pravém horním rohu vyberte **Sestava systém** . Sestava se automaticky stáhne.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/system-report.png" alt-text="Kliknutím na tlačítko Systémová sestava Stáhněte kopii systémové sestavy.":::

Systémová sestava je soubor PDF obsahující všechna data v systému:

  - Zařízení

  - Výstrahy

  - Informace o zásadách sítě

## <a name="devices-in-a-system-report"></a>Zařízení v systémové sestavě 

V systémové sestavě se zobrazí seznam všech zařízení a jejich informace. Například typ, název a používané protokoly. Sestava systém také zobrazuje seznam zařízení na dodavatele.

## <a name="alerts-in-system-report"></a>Výstrahy v systémové sestavě 

Sestava systém zobrazuje seznam všech výstrah s informacemi, jako je datum a závažnost.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/alerts-report.png" alt-text="Zobrazení výstrah v sestavách systému.":::

## <a name="network-information-in-system-report"></a>Informace o síti v systémové sestavě 

Systémová sestava zobrazuje podrobnosti o směrném plánu sítě. Například DNP3 kód funkce a otevřené porty pro každé připojení.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/dnp3.png" alt-text="Zobrazení funkce DNP3 ze sestavy systému.":::

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/open-port.png" alt-text="Zobrazení sestavy otevřeného portu na připojení":::

## <a name="see-also"></a>Viz také

[Generování sestav](how-to-create-risk-assessment-reports.md) 
 posouzení rizik Dotazy dolování dat ze [senzorů](how-to-create-data-mining-queries.md) 
 [Vytváření sestav vektorů útoku](how-to-create-attack-vector-reports.md)
