---
title: Generování map
description: Tento článek popisuje, jak vygenerovat mapy v Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: d0176fd337abd37f97a6dbe5cf7c68ccf94114d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102173860"
---
# <a name="generate-maps"></a>Generování map

Pomocí Azure FarmBeats můžete vygenerovat následující mapy pomocí satelitních snímků a datových vstupů ze senzorů. Pomocí map se můžete podívat na zeměpisnou polohu vaší farmy a určit vhodné umístění pro vaše zařízení.

  - **Mapa umístění senzoru**: poskytuje doporučení, kolik senzorů se má použít a kam je umístit na farmu.
  - **Mapa satelitních** indexů: zobrazuje index vegetativní a vodní index pro farmu.
  - **Heatmapu vlhkosti půdy**: zobrazuje distribuci vlhkosti půdy nezavádějícími satelitní data a data senzorů.

## <a name="sensor-placement-map"></a>Mapa umístění senzoru

Mapa umístění snímače FarmBeats vám pomůže se umísťováním senzorů vlhkosti v půdě. Výstup mapy se skládá ze seznamu souřadnic pro nasazení senzoru. Vstupy z těchto senzorů se používají společně se satelitními a heatmapuou vlhkosti půdy.

Tato mapa je odvozena segmentací zápoje, jak se v průběhu roku zobrazilo více kalendářních dat. I holé a budovy jsou součástí zápojeu. Můžete odebrat senzory, které na tomto místě nepotřebujete. Tato mapa je určena pro doprovodné materiály a na základě vlastních znalostí můžete změnit polohu a čísla mírně. Přidáním senzorů nedojde k navýšení vlhkosti v půdě heatmapu, ale existuje možnost zhoršení přesnosti heatmapu, pokud je číslo snímače omezené.

## <a name="before-you-begin"></a>Než začnete

Před pokusem o vygenerování mapy umístění senzoru splňovat následující požadavky:

- Velikost farmy musí být víc než jedna Acre.
- Počet scén bez cloudu pro vybraný rozsah dat musí být větší než 6.
- Minimálně šest scén bez cloudového škálování musí mít normalizovaný rozdílový index (NDVI), který je větší nebo roven 0,3.

    > [!NOTE]
    > Sentinel povoluje pouze dvě souběžná vlákna na uživatele. V důsledku toho se úlohy dostanou do fronty a jejich dokončení může trvat déle.

### <a name="dependencies-on-sentinel"></a>Závislosti na Sentinel

Následující závislosti se týkají Sentinel:

- Závisí na výkonu Sentinel pro stahování satelitních imagí. Zkontroluje stav výkonu Sentinel a [aktivity](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)údržby.
- Sentinel povoluje pouze dvě souběžná [vlákna stahování](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) na uživatele.
- Generování mapy přesnosti je ovlivněno [rozsahem Sentinel a frekvencí opakování]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage).

## <a name="create-a-sensor-placement-map"></a>Vytvořit mapu umístění senzorů
Tato část podrobně popisuje postupy pro vytváření map umístění senzorů.

> [!NOTE]
> Mapu umístění senzoru můžete iniciovat na stránce **mapy** nebo z rozevírací nabídky **Generovat mapy přesnosti** na stránce s **podrobnostmi o farmě** .

Postupujte takto:

1. Na domovské stránce přejděte na **mapy** z levé navigační nabídky.
2. Vyberte **vytvořit mapy** a v rozevírací nabídce vyberte **umístění snímače** .

    ![Vybrat umístění senzoru](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. Po výběru **umístění senzoru** se zobrazí okno **umístění senzoru** .

    ![Okno umístění senzorů](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. Vyberte farmu z rozevírací nabídky **farma** .
   Chcete-li vyhledat a vybrat farmu, můžete buď přejít v rozevíracím seznamu, nebo zadat název farmy do textového pole.
5. Pokud chcete vygenerovat mapu pro poslední rok, vyberte **Doporučené**.
6. Chcete-li vygenerovat mapu pro vlastní rozsah dat, vyberte možnost **Vybrat rozsah kalendářních dat**. Zadejte počáteční a koncové datum, pro které chcete vygenerovat mapu umístění senzoru.
7. Vyberte **Generovat mapy**.
 Zobrazí se potvrzovací zpráva s podrobnostmi o úloze.

  Informace o stavu úlohy najdete v části **zobrazení úloh**. Pokud se stav úlohy zobrazí jako *neúspěšné*, zobrazí se v popisku *neúspěšného* stavu podrobná chybová zpráva. V tomto případě opakujte předchozí kroky a akci opakujte.

  Pokud se problém nevyřeší, přečtěte si část [Poradce při potížích](troubleshoot-azure-farmbeats.md) nebo se obraťte na [Fórum Azure FarmBeats, kde najdete podporu](https://aka.ms/FarmBeatsMSDN) k příslušným protokolům.

### <a name="view-and-download-a-sensor-placement-map"></a>Zobrazení a stažení mapy umístění senzoru

Postupujte takto:

1. Na domovské stránce přejděte na **mapy** z levé navigační nabídky.

    ![Výběr map z levé navigační nabídky](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. V levém navigačním panelu okna vyberte **Filtr** .
  V okně **filtru** se zobrazí kritéria hledání.

    ![Okno filtru](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Z rozevíracích nabídek vyberte hodnoty **typ**, **Datum** a **název** . Pak vyberte **použít** a vyhledejte mapu, kterou chcete zobrazit.
  Datum, kdy byla úloha vytvořena, se zobrazí ve formátu type_farmname_YYYY-MM-DD.
4. Procházejte seznamem map, které jsou k dispozici, pomocí navigačních panelů na konci stránky.
5. Vyberte mapu, kterou chcete zobrazit. V automaticky otevíraném okně se zobrazí náhled pro vybranou mapu.
6. Vyberte **Stáhnout** a Stáhněte soubor. JSON pro souřadnice snímače.

    ![Náhled mapy umístění senzorů](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>Mapa satelitních indexů

Následující části popisují postupy spojené s vytvářením a zobrazením mapy satelitních indexů.

> [!NOTE]
> Můžete zahájit mapování satelitních indexů ze stránky **Maps** nebo z rozevírací nabídky **Generovat mapy přesnosti** na stránce s **podrobnostmi o farmě** .

FarmBeats poskytuje možnost generovat NDVI, Enhanced vegetativní index (EVI) a normalizované mapy vodního rozdílového indexu (NDWI) pro farmy. Tyto indexy vám pomůžou určit, jak se ořízne v současné době, kdy se v minulosti zvětšila i reprezentativní úrovně vody.


> [!NOTE]
> Pro dny, pro které se vygenerovala mapa, se vyžaduje bitová kopie Sentinel.


## <a name="create-a-satellite-indices-map"></a>Vytvoření mapy satelitních indexů

Postupujte takto:

1. Na domovské stránce přejděte na **mapy** z levé navigační nabídky.
2. Vyberte **vytvořit mapy** a v rozevírací nabídce vyberte **satelitové indexy** .

    ![Výběr satelitních indexů z rozevírací nabídky](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. Po výběru **satelitních indexů** se zobrazí okno **satelitní indexy** .

    ![Okno satelitních indexů](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. Vyberte farmu z rozevírací nabídky.
   Pokud chcete hledat a vybrat vaši farmu, můžete buď přejít v rozevíracím seznamu, nebo zadat název farmy.   
5. Pokud chcete vygenerovat mapu pro poslední týden, vyberte **Tento týden**.
6. Chcete-li vygenerovat mapu pro vlastní rozsah dat, vyberte možnost **Vybrat rozsah kalendářních dat**. Zadejte počáteční a koncové datum, pro které chcete generovat mapu satelitních indexů.
7. Vyberte **Generovat mapy**.
    Zobrazí se potvrzovací zpráva s podrobnostmi o úloze.

    ![Zpráva o potvrzení mapování satelitních indexů](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    Informace o stavu úlohy najdete v části **zobrazení úloh**. Pokud se stav úlohy zobrazí jako *neúspěšné*, zobrazí se v popisku *neúspěšného* stavu podrobná chybová zpráva. V tomto případě opakujte předchozí kroky a akci opakujte.

    Pokud se problém nevyřeší, přečtěte si část [Poradce při potížích](troubleshoot-azure-farmbeats.md) nebo se obraťte na [Fórum Azure FarmBeats, kde najdete podporu](https://aka.ms/FarmBeatsMSDN) k příslušným protokolům.

### <a name="view-and-download-a-map"></a>Zobrazení a stažení mapy

Postupujte takto:

1. Na domovské stránce přejděte na **mapy** z levé navigační nabídky.

    ![Vybrat mapy](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. V levém navigačním panelu okna vyberte **Filtr** . V okně **filtru** se zobrazí kritéria hledání.

    ![Okno filtru zobrazuje kritéria hledání.](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Z rozevíracích nabídek vyberte hodnoty **typ**, **Datum** a **název** . Pak vyberte **použít** a vyhledejte mapu, kterou chcete zobrazit.
  Datum, kdy byla úloha vytvořena, se zobrazí ve formátu type_farmname_YYYY-MM-DD.

4. Procházejte seznamem map, které jsou k dispozici, pomocí navigačních panelů na konci stránky.
5. Pro každou kombinaci názvu a **data** **farmy** jsou k dispozici následující tři mapy:
    - NDVI
    - EVI
    - NDWI
6. Vyberte mapu, kterou chcete zobrazit. V automaticky otevíraném okně se zobrazí náhled pro vybranou mapu.
7. V rozevírací nabídce vyberte **Stáhnout** a vyberte formát pro stažení. Mapa se stáhne a uloží do místní složky ve vašem počítači.

    ![Náhled vybraných satelitních indexů](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="soil-moisture-heatmap"></a>Heatmapu vlhkosti půdy

Vlhkost v půdě je voda, která je držena v prostorech mezi částicemi v půdě.Heatmapu vlhkosti půdy vám pomůže pochopit data o vlhkosti půdy v libovolné hloubce ve vaší farmě s vysokým rozlišením. Pro vygenerování přesného a použitelného heatmapu vlhkosti v půdě je nutné použít jednotné nasazení snímačů. Všechny senzory musí být od stejného poskytovatele. Různí poskytovatelé mají rozdíly ve způsobu měření vlhkosti půdy spolu s rozdíly v kalibraci. Heatmapu se generuje pro konkrétní hloubku pomocí senzorů nasazených v této hloubce.

### <a name="before-you-begin"></a>Než začnete

Před pokusem o vygenerování heatmapu vlhkosti v půdě splnění následujících požadavků:

- Musí se nasadit aspoň tři senzory vlhkosti v půdě. Nepokoušejte se vytvořit heatmapu vlhkosti v půdě před nasazením senzorů a přidruženými k farmě.
- Generování heatmapu vlhkosti v půdě má vliv na pokrytí cest, cloudové pokrytí a cloudové stíny. Alespoň jedna pracovní scény bez cloudu musí být k dispozici po dobu posledních 120 dnů od dne, po který se požaduje heatmapu vlhkosti půdy.
- Aspoň polovinu senzorů nasazených ve farmě musí být online a streamování dat do DataHub.
- Heatmapu se musí vygenerovat pomocí měření senzorů od stejného poskytovatele.


## <a name="create-a-soil-moisture-heatmap"></a>Vytvoření heatmapu vlhkosti půdy

Postupujte takto:

1. Na domovské stránce přejděte na **mapy** z levé navigační nabídky a zobrazte stránku **mapy** .
2. Vyberte **vytvořit mapy** a z rozevírací nabídky vyberte **vlhkost v půdě** .

    ![Výběr vlhkosti půdy z rozevírací nabídky](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. Po výběru **vlhkosti půdy** se zobrazí okno **vlhkosti půdy** .

    ![Okno vlhkosti půdy](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. Vyberte farmu z rozevírací nabídky **farma** .
   Chcete-li vyhledat a vybrat farmu, můžete buď přejít z rozevíracího seznamu, nebo zadat název farmy v rozevírací nabídce **Vybrat farmu** .
5. V rozevírací nabídce **Vybrat měření snímače vlhkosti** vyberte míru vlhkosti půdního senzoru (hloubku), pro kterou chcete vytvořit mapu.
Pokud chcete najít míru senzoru, přečtěte si **snímače** a vyberte jakýkoliv senzor vlhkosti půdy. Pak v části **vlastnosti senzoru** použijte hodnotu v poli **Název míry**.
6. Pokud chcete vygenerovat mapu pro **dnešek** nebo **Tento týden**, vyberte jednu z možností.
7. Chcete-li vygenerovat mapu pro vlastní rozsah dat, vyberte možnost **Vybrat rozsah kalendářních dat**. Zadejte počáteční a koncové datum, pro které chcete vygenerovat heatmapu vlhkosti půdy.
8. Vyberte **Generovat mapy**.
 Zobrazí se potvrzovací zpráva s podrobnostmi o úloze.

   ![Potvrzovací zpráva mapy vlhkosti půdy](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    Informace o stavu úlohy najdete v části **zobrazení úloh**. Pokud se stav úlohy zobrazí jako *neúspěšné*, zobrazí se v popisku *neúspěšného* stavu podrobná chybová zpráva. V tomto případě opakujte předchozí kroky a akci opakujte.

    Pokud se problém nevyřeší, přečtěte si část [Poradce při potížích](troubleshoot-azure-farmbeats.md) nebo se obraťte na [Fórum Azure FarmBeats, kde najdete podporu](https://aka.ms/FarmBeatsMSDN) k příslušným protokolům.

### <a name="view-and-download-a-map"></a>Zobrazení a stažení mapy

Postupujte takto:

1. Na domovské stránce přejděte na **mapy** z levé navigační nabídky.

    ![Přejít na mapy](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. V levém navigačním panelu okna vyberte **Filtr** . Zobrazí se okno **filtru** , ze kterého můžete hledat mapy.

    ![Výběr filtru z levé navigační části](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  Z rozevíracích nabídek vyberte hodnoty **typ**, **Datum** a **název** . Pak vyberte **použít** a vyhledejte mapu, kterou chcete zobrazit. Datum, kdy byla úloha vytvořena, se zobrazí ve formátu type_farmname_YYYY-MM-DD.
4. Vyberte ikonu **řazení** vedle záhlaví tabulky, která se mají seřadit podle **farmy**, **data**, **Vytvoření**, **ID úlohy** a **typu úlohy**.
5. Procházejte seznamem map dostupných pomocí navigačních tlačítek na konci stránky.
6. Vyberte mapu, kterou chcete zobrazit. V automaticky otevíraném okně se zobrazí náhled pro vybranou mapu.
7. V rozevírací nabídce vyberte **Stáhnout** a vyberte formát pro stažení. Mapa se stáhne a uloží do zadané složky.

    ![Heatmapua vlhkosti v půdě – Preview](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
