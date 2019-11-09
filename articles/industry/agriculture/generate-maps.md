---
title: Generovat mapy
description: Popisuje, jak generovat mapy v FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 635431fb87e5f164f92ab4b7a1027ee96e9d801a
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891002"
---
# <a name="generate-maps"></a>Generovat mapy

Pomocí Azure FarmBeats můžete vygenerovat následující mapy pomocí satelitních snímků a datových vstupů ze senzorů. Mapy vám pomůžou při prohlížení geografického umístění farmy a určení vhodného umístění pro vaše zařízení.

  -  **Mapa umístění senzoru** – poskytuje doporučení, kolik senzorů se má použít a kam je umístit do farmy.
  - **Mapa satelitních indexů** – zobrazuje index vegetativní a vodní index pro farmu.
  - **Mapa vlhkosti půdy** – zobrazuje distribuci vlhkosti půdy nezavádějícími satelitní data a data ze senzorů.

## <a name="sensor-placement-maps"></a>Mapy umístění senzorů

Mapa umístění snímače FarmBeats vám pomůže se umísťováním senzorů vlhkosti v půdě. Výstup mapy se skládá ze seznamu souřadnic pro nasazení senzoru. Vstupy z těchto senzorů se používají společně se satelitními a Heatmapuou vlhkosti půdy.  

Tato mapa je odvozena segmentací zápoje, jak se seznámilo nad více kalendářními daty po celý rok, dokonce i bez ohledu na půdu a budovy jsou součástí zápoje. Můžete odebrat senzory, které nejsou na daném místě požadovány. Tato mapa je určena pro doprovodné materiály a v závislosti na vašem vlastním znalostech můžete polohu a čísla upravit, a to mírně na základě vašich vlastních znalostí (přidáním senzorů nebude možné navýšit výsledky tepelné mapy vlhkosti v půdě, ale pokud je číslo snímače omezené, může dojít ke zhoršení přesnosti.  

## <a name="before-you-begin"></a>Než začnete  

Před pokusem o vygenerování mapy umístění senzoru zajistěte následující:

- Velikost farmy musí být víc než jedna Acre.
- Počet scén bez cloudu pro vybraný rozsah dat musí být větší než 6.  
- Minimálně šest scén bez cloudového škálování musí mít NDVI > = 0,3.

    > [!NOTE]
    > Sentinel povoluje pouze dvě souběžná vlákna na uživatele. Výsledkem je, že úlohy se zařadí do fronty a jejich dokončení může trvat delší dobu.

### <a name="dependencies-on-sentinel"></a>Závislosti na Sentinel  

Níže jsou závislosti Sentinel:

- Závisí na výkonu Sentinel pro stahování satelitních imagí. Zkontroluje stav výkonu Sentinel a [aktivity](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)údržby.
- Sentinel povoluje pouze dvě souběžná [vlákna stahování](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) na uživatele.
- Generace mapování přesnosti bude ovlivněna [rozsahem Sentinel a četností opakování]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage).

## <a name="create-sensor-placement-map"></a>Vytvořit mapu umístění senzorů
Oddíl podrobně popisuje postupy pro vytváření map umístění senzorů.

> [!NOTE]
> Umístění senzoru můžete iniciovat na stránce **mapy** nebo z rozevírací nabídky **Generovat mapy přesnosti** na stránce s **podrobnostmi o farmě** .

Použijte k tomu následující postup:

1. Na domovské stránce přejděte na **mapy** z levé navigační nabídky.
2. V rozevírací nabídce vyberte **vytvořit mapy** a vyberte **umístění snímače** .

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. Vyberte **umístění senzoru**.
  Zobrazí se okno umístění senzoru.

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. Vyberte farmu z rozevírací nabídky **farma** .  
   Chcete-li vyhledat a vybrat farmu, můžete buď přejít v rozevíracím seznamu, nebo do textového pole zadat název farmy.
5. Pokud chcete vygenerovat mapu pro poslední rok, vyberte **doporučenou** možnost.
6. Chcete-li generovat mapu pro vlastní rozsah dat, vyberte **možnost vybrat rozsah dat**a zadejte počáteční a koncové datum, pro které chcete vygenerovat mapu umístění senzoru.
7. Vyberte **Generovat mapy**.
 Zobrazí se potvrzovací zpráva s podrobnostmi o úloze.

  Informace o stavu úlohy najdete v části **zobrazení úloh**. Pokud se stav úlohy zobrazí jako *neúspěšné*, zobrazí se v popisku *neúspěšného* stavu podrobná chybová zpráva. V takovém případě opakujte výše uvedené kroky a akci opakujte.

  Pokud se problém nevyřeší, přečtěte si část [Poradce při potížích](troubleshoot-project-farmbeats.md) nebo kontaktujte [Fórum Azure FarmBeats, kde najdete podporu pro](https://aka.ms/FarmBeatsMSDN) příslušné protokoly.

### <a name="view-and-download-sensor-placement-map"></a>Zobrazit a stáhnout mapu umístění senzorů

Použijte k tomu následující postup:

1. Na domovské stránce přejděte na **mapy** z levé navigační nabídky.

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. V levém navigačním panelu okna vyberte **Filtr** .
  V okně **filtru** se zobrazí kritéria hledání.

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. V rozevírací nabídce vyberte **typ**, **Datum** a **název** a potom vyberte **použít** a vyhledejte mapu, kterou chcete zobrazit.
  Datum, kdy byla úloha vytvořena, se zobrazí ve formátu type_farmname_YYYY-MM-DD.
4. Procházejte seznamem dostupných map pomocí navigačních panelů na konci stránky.
5. Vyberte mapu, kterou chcete zobrazit. V automaticky otevíraném okně se zobrazí náhled pro vybranou mapu.
6. Vyberte **Stáhnout**a Stáhněte soubor. JSON pro souřadnice snímače.

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>Mapa satelitních indexů

V následujících částech jsou vysvětleny postupy při vytváření a zobrazování mapy satelitních indexů.

> [!NOTE]
> Můžete zahájit mapování satelitních indexů ze stránky **Maps** nebo z rozevírací nabídky **Generovat mapy přesnosti** na stránce s **podrobnostmi o farmě** .

FarmBeats poskytuje možnost generovat normalizovaný rozdílový index vegetace (NDVI), Enhanced vegetativní index (EVI) a normalizovaný rozdílový index vody (NDWI) pro farmy. Tyto indexy vám pomůžou určit, jak se ořízne v současné době, kdy se v minulosti zvětšila i reprezentativní úrovně vody.


> [!NOTE]
> Pro dny, pro které se vygenerovala mapa, se vyžaduje bitová kopie Sentinel.


## <a name="create-satellite-indices-map"></a>Vytvořit mapu satelitních indexů

Použijte k tomu následující postup:

1. Na domovské stránce přejděte na **mapy** z levé navigační nabídky.
2. Vyberte **vytvořit mapy** a v rozevírací nabídce vyberte **satelitové indexy** .

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. Vyberte **satelitové indexy**.
  Zobrazí se okno satelitní indexy.

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. Vyberte farmu z rozevírací nabídky.  
   Pokud chcete hledat a vybrat vaši farmu, můžete buď přejít v rozevíracím seznamu, nebo zadat název farmy.   
5. Pokud chcete vygenerovat mapu pro poslední týden, vyberte možnost **Tento týden** .
6. Pokud chcete vygenerovat mapu pro vlastní rozsah dat, vyberte **Vybrat rozsah kalendářních dat**a zadejte počáteční a koncové datum, pro které chcete generovat mapu satelitních indexů.
7. Vyberte **Generovat mapy**.
    Zobrazí se potvrzovací zpráva s podrobnostmi o úloze.

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    Informace o stavu úlohy najdete v části **zobrazení úloh**. Pokud se stav úlohy zobrazí jako *neúspěšné*, zobrazí se v popisku *neúspěšného* stavu podrobná chybová zpráva. V takovém případě opakujte výše uvedené kroky a akci opakujte.

    Pokud se problém nevyřeší, přečtěte si část [Poradce při potížích](troubleshoot-project-farmbeats.md) nebo kontaktujte [Fórum Azure FarmBeats, kde najdete podporu pro](https://aka.ms/FarmBeatsMSDN) příslušné protokoly.

### <a name="view-and-download-map"></a>Zobrazit a stáhnout mapu

Použijte k tomu následující postup:

1. Na domovské stránce přejděte na **mapy** z levé navigační nabídky.

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. V levé navigační části okna vyberte **Filtr** . v okně **filtru** se zobrazí kritéria hledání.

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. V rozevírací nabídce vyberte **typ**, **Datum** a **název** a potom vyberte **použít** a vyhledejte mapu, kterou chcete zobrazit.
  Datum, kdy byla úloha vytvořena, se zobrazí ve formátu type_farmname_YYYY-MM-DD.

4. Procházejte seznamem dostupných map pomocí navigačních panelů na konci stránky.
5. Pro každou kombinaci názvu a **data** **farmy** jsou k dispozici následující tři mapy:
    - NDVI
    - EVI
    - NDWI
6. Vyberte mapu, kterou chcete zobrazit. V automaticky otevíraném okně se zobrazí náhled pro vybranou mapu.
7. Vyberte možnost **Stáhnout** rozevírací nabídku a vyberte formát stahování a mapa se stáhne a uloží do místní složky na vašem počítači.

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="get-soil-moisture-heatmap"></a>Získat heatmapu vlhkosti půdy

Vlhkost v půdě je voda, která je držena v prostorech mezi částicemi v půdě. Heatmapu vlhkosti půdy vám pomůže pochopit data o vlhkosti půdy v libovolné hloubce, ve velkém rozlišení v rámci vašich farem. Aby bylo možné vygenerovat přesnou a použitelnou heatmapu vlhkosti v půdě, je nutné použít jednotné nasazení snímačů, kde jsou všechny senzory od stejného poskytovatele. Různí poskytovatelé budou mít rozdíly ve způsobu měření vlhkosti půdy spolu s rozdíly v kalibraci. Heatmapu se generuje pro konkrétní hloubku pomocí senzorů nasazených v této hloubce.

### <a name="before-you-begin"></a>Než začnete  

Před pokusem o vygenerování heatmapu vlhkosti v půdě ověřte následující:

- Musí se nasadit aspoň tři senzory vlhkosti v půdě. Microsoft doporučuje, abyste se nepokusili vytvořit mapu vlhkosti půdy před nasazením senzorů a přidružením k farmě.  
- Generování heatmapu vlhkosti v půdě má vliv na pokrytí cest Sentinel, cloudové pokrytí a cloudové stíny. Alespoň jedna pracovní verze dostupnosti v cloudu musí být k dispozici po dobu posledních 120 dní od dne, pro který byla požadována mapa vlhkosti půdy.
- Aspoň polovinu senzorů nasazených ve farmě musí být online a musí mít streamování dat do datového centra.
- Heatmapu by mělo být vygenerováno pomocí měření senzoru ze stejného poskytovatele.


## <a name="create-soil-moisture-heatmap"></a>Vytvořit heatmapu vlhkosti v půdě

Použijte k tomu následující postup:

1. Na domovské stránce přejděte na **mapy** z levé navigační nabídky a zobrazte stránku mapy.
2. Vyberte **vytvořit mapy** a z rozevírací nabídky vyberte **vlhkost půdy** .

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. Vyberte **vlhkost půdy**.
    Zobrazí se okno vlhkosti půdy.

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. Vyberte farmu z rozevírací nabídky **farma** .  
   Chcete-li vyhledat a vybrat farmu, můžete buď přejít z rozevíracího seznamu, nebo zadat název farmy v rozevírací nabídce vybrat farmu.
5. V rozevírací nabídce **Vybrat měření snímače vlhkosti** vyberte míru vlhkosti půdního senzoru (hloubka), pro kterou chcete vytvořit mapu.
Pokud chcete najít míru senzoru, přečtěte **si snímače, vyberte**jakýkoliv senzor vlhkosti půdy. Potom v části **vlastnosti senzoru** použijte hodnotu proti v poli **Název míry** .
6. Pokud chcete vygenerovat pro **dnešek** nebo **Tento týden**, vyberte jednu z možností.
7. Pokud chcete vygenerovat pro vlastní rozsah dat, vyberte **Vybrat rozsah kalendářních dat**a zadejte počáteční a koncové datum, pro které chcete vygenerovat mapu vlhkosti půdy.
8. Vyberte **Generovat mapy**.
 Zobrazí se potvrzovací zpráva s podrobnostmi o úloze.

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    Informace o stavu úlohy najdete v části **zobrazení úloh**. Pokud se stav úlohy zobrazí jako *neúspěšné*, zobrazí se v popisku *neúspěšného* stavu podrobná chybová zpráva. V takovém případě opakujte výše uvedené kroky a akci opakujte.

    Pokud se problém nevyřeší, přečtěte si část [Poradce při potížích](troubleshoot-project-farmbeats.md) nebo kontaktujte [Fórum Azure FarmBeats, kde najdete podporu pro](https://aka.ms/FarmBeatsMSDN) příslušné protokoly.

### <a name="view-and-download-map"></a>Zobrazit a stáhnout mapu

Použijte k tomu následující postup:

1. Na domovské stránce přejděte na **mapy** z levé navigační nabídky.

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. V levé navigační části okna vyberte **Filtr** . zobrazí se okno **filtru** , ze kterého můžete hledat mapy.

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  V rozevírací nabídce vyberte **typ**, **Datum** a **název** a potom vyberte **použít** a vyhledejte mapu, kterou chcete zobrazit. Datum, kdy byla úloha vytvořena, se zobrazí ve formátu type_farmname_YYYY-MM-DD.
4. Vyberte ikonu **řazení** vedle záhlaví tabulky, která se mají seřadit podle farmy, data, vytvoření, ID úlohy a typu úlohy.
5. Procházejte seznamem dostupných map pomocí navigačních tlačítek na konci stránky.
6. Vyberte mapu, kterou chcete zobrazit. V automaticky otevíraném okně se zobrazí náhled pro vybranou mapu.
7. Vyberte možnost **Stáhnout** rozevírací nabídku a vyberte formát stahování a mapa se stáhne a uloží do zadané složky.

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
