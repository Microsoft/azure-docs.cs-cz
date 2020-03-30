---
title: Generování map
description: Tento článek popisuje, jak generovat mapy v Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 92228c691c323bc0b9621dfc7413d86c5c2669e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271769"
---
# <a name="generate-maps"></a>Generování map

Pomocí Azure FarmBeats můžete vygenerovat následující mapy pomocí satelitních snímků a vstupů dat senzorů. Mapy vám pomohou zjistit zeměpisnou polohu vaší farmy a určit vhodné umístění vašich zařízení.

  - **Mapa umístění senzoru**: Poskytuje doporučení, kolik senzorů použít a kam je umístit na farmu.
  - **Mapa satelitních indexů**: Zobrazuje index vegetace a index vody pro farmu.
  - **Půdní vlhkost heatmap**: Zobrazuje rozložení vlhkosti půdy fixací satelitních dat a dat ze senzorů.

## <a name="sensor-placement-map"></a>Mapa umístění senzoru

Mapa Farmy Senzor y Pro umístění vám pomůže s umístěním senzorů vlhkosti půdy. Výstup mapy se skládá ze seznamu souřadnic pro nasazení senzoru. Vstupy z těchto senzorů se používají spolu se satelitními snímky pro generování heatmapy Půdní vlhkosti.

Tato mapa je odvozena segmentací baldachýn, jak je vidět na více dat v průběhu roku. Dokonce i holá půda a budovy jsou součástí baldachýnu. Můžete odstranit senzory, které nejsou v místě vyžadovány. Tato mapa je pro navádění a můžete mírně změnit pozici a čísla na základě vašich vlastních znalostí. Přidání mandory neustoupí výsledky heatmapy vlhkosti půdy, ale existuje možnost zhoršení přesnosti heatmapy, pokud se sníží počet senzorů.

## <a name="before-you-begin"></a>Než začnete

Než se pokusíte vygenerovat mapu umístění senzoru, splédte na následující požadavky:

- Velikost farmy musí být větší než jeden akr.
- Počet scén Sentinelu bez cloudu musí být pro vybrané časové období větší než šest.
- Nejméně šest scén Sentinelu bez cloudu musí mít index vegetace normalizovaného rozdílu (NDVI) větší nebo roven 0,3.

    > [!NOTE]
    > Sentinel umožňuje pouze dvě souběžná vlákna na uživatele. V důsledku toho se úlohy dostanou do fronty a může trvat déle.

### <a name="dependencies-on-sentinel"></a>Závislosti na Sentinelu

Následující závislosti se vězí sentinelu:

- Při stahování satelitních snímků jsme závislí na výkonu sentinelu. Zkontrolujte stav výkonu sentinelu a [činnosti údržby](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome).
- Sentinel umožňuje pouze dvě souběžné [vlákna stahování](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) na uživatele.
- Generování přesné mapy je ovlivněno [pokrytím Sentinelu a frekvencí opětovného přístupu]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage).

## <a name="create-a-sensor-placement-map"></a>Vytvoření mapy umístění senzoru
V této části jsou podrobně uvedeny postupy pro vytváření map umístění senzorů.

> [!NOTE]
> Mapu umístění senzoru můžete spustit ze stránky **Mapy** nebo z rozevírací nabídky **Generovat přesné mapy** na stránce **Podrobnosti farmy.**

Postupujte následovně.

1. Na domovské stránce přejděte z levé navigační nabídky do **Mapy.**
2. V **rozevírací**nabídce vyberte Vytvořit mapy a v rozevírací nabídce vyberte Umístění **senzoru.**

    ![Vybrat umístění senzoru](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. Po výběru **možnosti Umístění senzoru**se zobrazí okno **Umístění senzoru.**

    ![Okno Umístění senzoru](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. V rozevírací nabídce **Farma** vyberte farmu.
   Chcete-li vyhledat a vybrat farmu, můžete se v rozevíracím seznamu posuzovat nebo zadat název farmy do textového pole.
5. Chcete-li vygenerovat mapu za poslední rok, vyberte **možnost Doporučeno**.
6. Chcete-li generovat mapu pro vlastní rozsah dat, vyberte možnost **Vybrat rozsah dat**. Zadejte počáteční a koncové datum, pro které chcete vygenerovat mapu Umístění senzoru.
7. Vyberte **Generovat mapy**.
 Zobrazí se potvrzovací zpráva s podrobnostmi o úloze.

  Informace o stavu úlohy naleznete v části **Zobrazit úlohy**. Pokud se stav úlohy zobrazuje *Nezdařilo*, zobrazí se v popisku stavu *Nezdařilo* se podrobná chybová zpráva. V takovém případě opakujte předchozí kroky a akci opakujte znovu.

  Pokud problém přetrvává, přečtěte si část [Poradce při potížích](troubleshoot-azure-farmbeats.md) nebo se obraťte na [fórum Azure FarmBeats, kde najdete podporu](https://aka.ms/FarmBeatsMSDN) s příslušnými protokoly.

### <a name="view-and-download-a-sensor-placement-map"></a>Zobrazení a stažení mapy umístění senzoru

Postupujte následovně.

1. Na domovské stránce přejděte z levé navigační nabídky do **Mapy.**

    ![V levé navigační nabídce vyberte Mapy.](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. V levém navigačním panelu okna vyberte **Filtr.**
  V okně **Filtr** se zobrazí kritéria vyhledávání.

    ![Okno filtru](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. V rozevíracích nabídkách vyberte Hodnoty **Typu**, **Datum**a **Název.** Pak vyberte **Použít,** chcete-li vyhledat mapu, kterou chcete zobrazit.
  Datum, kdy byla úloha vytvořena, je zobrazeno ve formátu type_farmname_YYYY-MM-DD.
4. Procházejte seznam map, které jsou k dispozici, pomocí navigačních panelů na konci stránky.
5. Vyberte mapu, kterou chcete zobrazit. Vyskakovací okno zobrazuje náhled vybrané mapy.
6. Vyberte **Stáhnout**a stáhněte soubor GeoJSON souřadnic senzorů.

    ![Náhled mapy umístění senzoru](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>Mapa satelitních indexů

V následujících částech jsou vysvětleny postupy spojené s vytvořením a zobrazením mapy satelitních indexů.

> [!NOTE]
> Mapu satelitních indexů můžete spustit ze stránky **Mapy** nebo z rozevírací nabídky **Generovat přesné mapy** na stránce **Podrobnosti farmy.**

FarmBeats vám poskytuje možnost vytvářet NDVI, Enhanced Vegetation Index (EVI) a normalizované indexy vody (NDWI) pro farmy. Tyto indexy pomáhají určit, jak plodina v současné době roste, nebo rostla v minulosti, a reprezentativní hladiny vody v zemi.


> [!NOTE]
> Pro dny, pro které je mapa generována, je vyžadován obrázek Sentinelu.


## <a name="create-a-satellite-indices-map"></a>Vytvoření mapy satelitních indexů

Postupujte následovně.

1. Na domovské stránce přejděte z levé navigační nabídky do **Mapy.**
2. V **rozevírací**nabídce vyberte Vytvořit mapy a v rozevírací nabídce vyberte Satelitní **indexy.**

    ![V rozevírací nabídce vyberte satelitní indexy.](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. Po výběru **satelitních indexů**se zobrazí okno **Satelitní indexy.**

    ![Okno Satelitní indexy](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. V rozevírací nabídce vyberte farmu.
   Chcete-li vyhledat a vybrat farmu, můžete se v rozevíracím seznamu posuzovat nebo zadat název farmy.   
5. Chcete-li vygenerovat mapu za poslední týden, vyberte **možnost Tento týden**.
6. Chcete-li generovat mapu pro vlastní rozsah dat, vyberte možnost **Vybrat rozsah dat**. Zadejte počáteční a koncové datum, pro které chcete vygenerovat mapu Satelitní indexy.
7. Vyberte **Generovat mapy**.
    Zobrazí se potvrzovací zpráva s podrobnostmi o úloze.

    ![Zpráva potvrzení mapy satelitních indexů](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    Informace o stavu úlohy naleznete v části **Zobrazit úlohy**. Pokud se stav úlohy zobrazuje *Nezdařilo*, zobrazí se v popisku stavu *Nezdařilo* se podrobná chybová zpráva. V takovém případě opakujte předchozí kroky a akci opakujte znovu.

    Pokud problém přetrvává, přečtěte si část [Poradce při potížích](troubleshoot-azure-farmbeats.md) nebo se obraťte na [fórum Azure FarmBeats, kde najdete podporu](https://aka.ms/FarmBeatsMSDN) s příslušnými protokoly.

### <a name="view-and-download-a-map"></a>Zobrazení a stažení mapy

Postupujte následovně.

1. Na domovské stránce přejděte z levé navigační nabídky do **Mapy.**

    ![Vybrat mapy](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. V levém navigačním panelu okna vyberte **Filtr.** V okně **Filtr** se zobrazí kritéria vyhledávání.

    ![Okno filtru zobrazuje kritéria vyhledávání](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. V rozevíracích nabídkách vyberte Hodnoty **Typu**, **Datum**a **Název.** Pak vyberte **Použít,** chcete-li vyhledat mapu, kterou chcete zobrazit.
  Datum, kdy byla úloha vytvořena, je zobrazeno ve formátu type_farmname_YYYY-MM-DD.

4. Procházejte seznam map, které jsou k dispozici, pomocí navigačních panelů na konci stránky.
5. Pro každou kombinaci **názvu farmy** a **data**jsou k dispozici tyto tři mapy:
    - NDVI
    - Evi
    - NDWI
6. Vyberte mapu, kterou chcete zobrazit. Vyskakovací okno zobrazuje náhled vybrané mapy.
7. V rozevírací nabídce vyberte stáhnout položku **Stáhnout** a vyberte formát stahování. Mapa je stažena a uložena v místní složce v počítači.

    ![Náhled mapy vybraných satelitních indexů](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="soil-moisture-heatmap"></a>Půdní vlhkost heatmap

Půdní vlhkost je voda, která se drží v prostoru mezi částicemi půdy.Heatmapa půdní vlhkosti vám pomůže porozumět datům vlhkosti půdy v jakékoli hloubce, ve vysokém rozlišení ve vaší farmě. Pro vytvoření přesné a použitelné heatmap půdní vlhkosti je nutné jednotné nasazení senzorů. Všechny senzory musí být od stejného poskytovatele. Různí poskytovatelé mají rozdíly ve způsobu měření vlhkosti půdy spolu s rozdíly v kalibraci. Heatmapa je generována pro určitou hloubku pomocí senzorů nasazených v této hloubce.

### <a name="before-you-begin"></a>Než začnete

Než se pokusíte vytvořit heatmapu Půdní vlhkost, seznamte se s následujícími požadavky:

- Musí být nasazeny nejméně tři senzory vlhkosti půdy. Nepokoušejte se vytvořit heatmapu půdní vlhkosti před nasazením senzorů a přidružených k farmě.
- Generování heatmapy půdní vlhkosti je ovlivněno pokrytím trasy Sentinelu, oblačností a stínem mraků. Za posledních 120 dní ode dne, pro který byla požadována heatmapa půdní vlhkosti, musí být k dispozici alespoň jedna scéna Sentinel u sentinelu bez cloudu.
- Alespoň polovina senzorů nasazených na farmě musí být online a musí mít datové vysílání do datahubu.
- Heatmap musí být generovánpomocí senzorových rozměrů od stejného poskytovatele.


## <a name="create-a-soil-moisture-heatmap"></a>Vytvořte heatmapu vlhkosti půdy

Postupujte následovně.

1. Na domovské stránce přejděte na **Mapy** z levé navigační nabídky a zobrazte stránku **Mapy.**
2. V **rozevírací**nabídce vyberte Vytvořit mapy a v rozevírací nabídce vyberte **Půdní vlhkost.**

    ![V rozevírací nabídce vyberte Půdní vlhkost.](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. Po výběru **možnosti Půdní vlhkost**se zobrazí okno **Půdní vlhkost.**

    ![Půdní vlhkost okno](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. V rozevírací nabídce **Farma** vyberte farmu.
   Chcete-li vyhledat a vybrat farmu, můžete buď přejít z rozevíracího seznamu, nebo zadat název farmy do rozevírací nabídky **Vybrat farmu.**
5. V rozevírací nabídce **Select Soil Moisture Sensor Measure** vyberte měření vlhkosti půdy (hloubku), pro kterou chcete mapu vygenerovat.
Chcete-li najít měření senzoru, přejděte na **senzory**a vyberte libovolný snímač vlhkosti půdy. Potom v části **Vlastnosti senzoru** použijte hodnotu v **části Název míry**.
6. Chcete-li vygenerovat mapu pro **dnešek** nebo **tento týden**, vyberte jednu z možností.
7. Chcete-li generovat mapu pro vlastní rozsah dat, vyberte možnost **Vybrat rozsah dat**. Zadejte počáteční a koncové datum, pro které chcete generovat heatmapu Půdní vlhkost.
8. Vyberte **Generovat mapy**.
 Zobrazí se potvrzovací zpráva s podrobnostmi o úloze.

   ![Zpráva o potvrzení mapy půdní vlhkosti](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    Informace o stavu úlohy naleznete v části **Zobrazit úlohy**. Pokud se stav úlohy zobrazuje *Nezdařilo*, zobrazí se v popisku stavu *Nezdařilo* se podrobná chybová zpráva. V takovém případě opakujte předchozí kroky a akci opakujte znovu.

    Pokud problém přetrvává, přečtěte si část [Poradce při potížích](troubleshoot-azure-farmbeats.md) nebo se obraťte na [fórum Azure FarmBeats, kde najdete podporu](https://aka.ms/FarmBeatsMSDN) s příslušnými protokoly.

### <a name="view-and-download-a-map"></a>Zobrazení a stažení mapy

Postupujte následovně.

1. Na domovské stránce přejděte z levé navigační nabídky do **Mapy.**

    ![Přejít na Mapy](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. V levém navigačním panelu okna vyberte **Filtr.** Okno **Filtr** se zobrazí, odkud můžete vyhledávat mapy.

    ![V levém navigačním panelu vyberte filtr.](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  V rozevíracích nabídkách vyberte Hodnoty **Typu**, **Datum**a **Název.** Pak vyberte **Použít,** chcete-li vyhledat mapu, kterou chcete zobrazit. Datum, kdy byla úloha vytvořena, je zobrazeno ve formátu type_farmname_YYYY-MM-DD.
4. Vyberte ikonu **Seřadit** vedle záhlaví tabulky, která chcete seřadit podle **farmy**, **data**, **vytvořeného na**, **ID úlohy**a **typu úlohy**.
5. Procházejte seznam map, které jsou k dispozici, pomocí navigačních tlačítek na konci stránky.
6. Vyberte mapu, kterou chcete zobrazit. Vyskakovací okno zobrazuje náhled vybrané mapy.
7. V rozevírací nabídce vyberte stáhnout položku **Stáhnout** a vyberte formát stahování. Mapa je stažena a uložena v zadané složce.

    ![Náhled heatmap půdní vlhkosti](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
