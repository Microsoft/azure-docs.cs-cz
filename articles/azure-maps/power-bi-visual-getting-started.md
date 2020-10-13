---
title: Začínáme s Azure Maps Power BI vizuál | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak používat vizuál Microsoft Azure Maps pro Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: aecda5ed9b87ba2345a52923a574919d6a08594f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362667"
---
# <a name="getting-started-with-the-azure-maps-power-bi-visual"></a>Začínáme s Azure Maps Power BI vizuál

<Token>**platí pro:** ![ Zelená značka zaškrtnutí. ](media/power-bi-visual/yes.png) Služba Power BI pro ***uživatele*** ![ zelený symbol zaškrtnutí. ](media/power-bi-visual/yes.png) Služba Power BI pro návrháře & vývojářům ![ zelený symbol zaškrtnutí. ](media/power-bi-visual/yes.png) Power BI Desktop ![ X značí ne. ](media/power-bi-visual/no.png) Vyžaduje licenci pro nebo Premium. </Token>

Tento článek ukazuje, jak použít vizuál Microsoft Azure Maps pro Power BI.

> [!NOTE]
> Tento vizuál lze vytvořit a zobrazit v Power BI Desktop i služba Power BI. Kroky a obrázky v tomto článku se týkají Power BI Desktopu.

Azure Maps vizuál pro Power BI poskytuje bohatou sadu vizualizací dat pro prostorová data nad mapou. Odhaduje se, že více než 80% obchodních dat má kontext umístění. Azure Maps vizuál se dá použít k získání přehledu o tom, jak se tento kontext umístění týká a ovlivňuje vaše obchodní data.

![Power BI plochu s Azure Maps vizuálním zobrazením obchodních dat](media/power-bi-visual/azure-maps-visual-hero.png)

## <a name="what-is-sent-to-azure"></a>Co se pošle do Azure?

Azure Maps vizuál se připojuje ke cloudové službě hostované v Azure a načítá data o poloze, jako jsou obrázky mapy a souřadnice, které se používají k vytvoření vizualizace mapy. 

-   Podrobnosti o oblasti, na které je mapa soustředěna, jsou odesílány do Azure za účelem načtení imagí potřebných k vykreslení plátna mapy (označovaného také jako dlaždice mapy). 
-   Data v kontejnerech umístění, zeměpisná šířka a zeměpisná délka se můžou odeslat do Azure a načíst souřadnice map (proces nazvaný geografické kódování). 
-   Pokud je povolená možnost telemetrie v Power BI, můžou se shromažďovat data telemetrie ve stavu vizuálu (například zprávy o selhání).

Kromě výše popsaných scénářů se na Azure Maps servery neodesílají žádná další data překrývající se na mapě. Veškerá vykreslování dat probíhá místně v rámci klienta.

Vy nebo váš správce budete možná muset aktualizovat bránu firewall tak, aby povolovala přístup k Azure Maps platformě, která používá následující adresu URL.

> `https://atlas.microsoft.com`

Další informace o ochraně osobních údajů a podmínek použití týkající se Azure Mapsho vizuálu najdete [Microsoft Azure právní informace](https://azure.microsoft.com/support/legal/).

## <a name="preview-behavior-and-requirements"></a>Chování a požadavky ve verzi Preview

Pro tuto verzi Preview **Azure Maps**existuje několik důležitých informací a požadavků:

-   **Azure Maps** vizuál je ve verzi Preview a musí být povolený v Power BI Desktop. Pokud chcete povolit **Azure Maps**, vyberte možnosti **souboru** &gt; **a** &gt; **Možnosti** nastavení &gt; ve **verzi Preview**a pak zaškrtněte políčko **Azure Maps vizuální** . Pokud Azure Maps vizuál není po tomto postupu k dispozici, je možné, že je nutné povolit přepínač správce tenanta na portálu pro správu.
-   Datová sada musí obsahovat pole, která obsahují informace o **zeměpisné šířce** a **délce** . Geografické kódování polí umístění bude přidáno v budoucí aktualizaci.
-   Předdefinovaný ovládací prvek legendy pro Power BI se v této verzi Preview aktuálně nezobrazuje. Bude přidáno v budoucí aktualizaci.

## <a name="use-the-azure-maps-visual"></a>Použití Azure Mapsho vizuálu

Jakmile je **Azure Maps** vizuál povolený, vyberte v podokně **vizualizace** ikonu **Azure Maps** .

![Tlačítko vizuálu Azure Maps v podokně vizualizace](media/power-bi-visual/azure-maps-in-visualizations-pane.png)

Power BI vytvoří prázdné Azure Maps plátno pro vizuální návrh. Ve verzi Preview se zobrazí další právní omezení.

![Power BI plochu s Azure Mapsm vizuálem načteným v počátečním stavu](media/power-bi-visual/visual-initial-load.png)

Pro načtení Azure Maps vizuálu proveďte následující kroky:

1.  V podokně **pole** přetáhněte datová pole, která obsahují informace o zeměpisné šířce a délce, do kontejnerů **Zeměpisná šířka** **a délka.** Jedná se o minimální data potřebná pro načtení Azure Mapsho vizuálu.
    
    > [!div class="mx-imgBorder"]
    > ![Azure Maps vizuální zobrazení bodů na mapě po zadaných polích Zeměpisná šířka a zeměpisná délka](media/power-bi-visual/bubble-layer.png)

2.  Chcete-li data vybarvit na základě kategorizace, přetáhněte pole kategorií do kontejneru **legend** podokna **pole** . V tomto příkladu používáme sloupec **AdminDistrict** (označovaný také jako stát nebo provincie).  
    
    > [!div class="mx-imgBorder"]
    > ![Azure Maps vizuální zobrazení bodů jako barevných bublin na mapě po poskytnutém poli legendy](media/power-bi-visual/bubble-layer-with-legend-color.png)

    > [!NOTE]
    > Předdefinovaný ovládací prvek legendy pro Power BI se v této verzi Preview aktuálně nezobrazuje. Bude přidáno v budoucí aktualizaci.

3.  Chcete-li data škálovat relativně, přetáhněte míru do **velikosti** kontejneru v podokně **pole** . V tomto příkladu používáme sloupec **Sales** .  
    
    > [!div class="mx-imgBorder"]
    > ![Azure Maps vizuál zobrazuje body jako barevné a škálované bubliny na mapě po zadaném poli velikosti.](media/power-bi-visual/bubble-layer-with-legend-color-and-size.png)

4.  Pomocí možností v podokně **Formát** můžete přizpůsobit způsob vykreslování dat. Následující obrázek je stejná jako mapa uvedená výše, ale možnost průhlednosti výplně vrstev bublin je nastavená na 50% a možnost osnovy s vysokým kontrastem je zapnutá.  
    
    > [!div class="mx-imgBorder"]
    > ![Azure Maps vizuální zobrazení bodů jako bublin na mapě s vlastním stylem](media/power-bi-visual/bubble-layer-styled.png)

## <a name="fields-pane-buckets"></a>Bloky podokna polí

V podokně **pole** Azure Mapsového vizuálu jsou k dispozici následující datové bloky.

| Pole     | Description  |
|-----------|--------------|
| Zeměpisná šířka  | Pole, které slouží k zadání hodnoty zeměpisné šířky datových bodů. Hodnoty zeměpisné šířky by měly být v rozmezí od-90 do 90 ve formátu desetinného stupně.  |
| Zeměpisná délka | Pole, které slouží k zadání zeměpisné hodnoty datových bodů. Hodnoty Zeměpisná délka by měly být v rozmezí od-180 do 180 ve formátu desetinného stupně.  |
| Legenda    | Pole, které slouží ke kategorizaci dat a přiřazení jedinečné barvy pro datové body v každé kategorii. Po vyplnění tohoto intervalu se v podokně **Formát** zobrazí část **barvy dat** , která umožňuje úpravy barev. |
| Velikost      | Míra použitá pro relativní velikost datových bodů na mapě.   |
| Popisy  | Další datová pole, která jsou zobrazena v popisech, když jsou tvary najetí myší. |

## <a name="map-settings"></a>Nastavení mapy

Část **nastavení mapy** v podokně formát poskytuje možnosti pro přizpůsobení způsobu zobrazení mapy a reakce na aktualizace.

| Nastavení             | Popis  |
|---------------------|--------------|
| Automatické přiblížení           | Automaticky zvětší mapu na data načtená pomocí podokna **pole** vizuálu. V průběhu změny dat bude mapa odpovídajícím způsobem aktualizovat svou polohu. Když je posuvník ve **vypnuté** pozici, zobrazí se další nastavení zobrazení mapy pro výchozí zobrazení mapy. |
| Obtékání po celém světě          | Umožňuje uživateli, aby se mapa vodorovně nekonečně. |
| Výběr stylu        | Přidá na mapu tlačítko, které umožňuje čtenářům sestavy změnit styl mapy. |
| Navigační ovládací prvky | Přidá tlačítka na mapu jako další metodu, která čtenářům sestav umožní přiblížit, otočit a změnit výšku mapy. Další informace najdete v tomto dokumentu o [přechodu na mapu](map-accessibility.md#navigating-the-map) , kde najdete podrobnosti o všech různých způsobech, jak můžou uživatelé procházet mapu. |
| Styl mapy           | Styl mapy Další informace najdete v tomto dokumentu, kde najdete další informace o [podporovaných stylech mapy](supported-map-styles.md). |

### <a name="map-view-settings"></a>Nastavení zobrazení mapy

Pokud je posuvník **automatického přiblížení** v nepoužívaném **umístění,** zobrazí se následující nastavení a umožní uživateli zadat výchozí informace o zobrazení mapy.

| Nastavení          | Popis   |
|------------------|---------------|
| Zoom             | Výchozí úroveň přiblížení mapy. Může být číslo v rozsahu od 0 do 22. |
| Střední Zeměpisná šířka  | Výchozí Zeměpisná šířka uprostřed mapy. |
| Střední délka | Výchozí Zeměpisná délka uprostřed mapy. |
| Adresář          | Výchozí orientace mapy ve stupních, kde 0 je sever, 90 je východ, 180 je jih a 270 je západní. Může to být jakékoli číslo mezi 0 a 360. |
| Teče            | Výchozí sklon mapy ve stupních od 0 do 60, kde 0 vypadá na mapě na rovném místě. |

## <a name="considerations-and-limitations"></a>Požadavky a omezení

Azure Maps vizuál je k dispozici v následujících službách a aplikacích:

| Služba nebo aplikace                              | Dostupnost |
|------------------------------------------|--------------|
| Power BI Desktop                         | Ano          |
| Služba Power BI (app.powerbi.com)       | Ano          |
| Mobilní aplikace Power BI             | Ano          |
| Publikování na web z Power BI                  | No           |
| Power BI Embedded                        | No           |
| Vkládání ve službě Power BI (PowerBI.com) | Yes          |

Podpora dalších Power BI služeb nebo aplikací se přidá v budoucích aktualizacích.

**Kde je Azure Maps k dispozici?**

V současné době je Azure Maps aktuálně k dispozici ve všech zemích a oblastech s výjimkou následujících:

- Čína
- Jižní Korea

Podrobnosti o pokrytí pro různé Azure Maps služby, které tento vizuál vysílaly, najdete v dokumentu s [informacemi o zeměpisném pokrytí](geographic-coverage.md) .

**Které webové prohlížeče podporuje Azure Maps vizuál?**

V této dokumentaci najdete informace o [podporovaných prohlížečích Azure Maps Web SDK](supported-browsers.md).

**Kolik datových bodů je možné vizualizovat?**

Tento vizuál podporuje až 30 000 datových bodů.

**Můžou se v tomto vizuálu používat adresy nebo jiné řetězce umístění?**

Úvodní náhled tohoto vizuálu podporuje jenom hodnoty zeměpisné šířky a délky v desítkových stupních. V budoucí aktualizaci se přidá podpora adres a dalších řetězců umístění.

## <a name="next-steps"></a>Další kroky

Další informace o Azure Maps Power BI vizuálu:

> [!div class="nextstepaction"]
> [Principy vrstev v Azure Maps Power BI vizuálu](power-bi-visual-understanding-layers.md)

> [!div class="nextstepaction"]
> [Správa Azure Mapsového vizuálu v rámci vaší organizace](power-bi-visual-manage-access.md)

Přizpůsobení vizuálu:

> [!div class="nextstepaction"]
> [Tipy a triky pro formátování barev v Power BI](https://docs.microsoft.com/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [Přizpůsobení názvů, pozadí a legend vizualizací](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)
