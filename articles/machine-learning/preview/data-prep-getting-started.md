---
title: "Začínáme s Data přípravy pro Azure Machine Learning | Microsoft Docs"
description: "Toto je příručka Začínáme pro přípravný datová část AML workbench"
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: b0fbb0af433cfad6693b022d7a00373dc39533aa
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="getting-started-with-data-preparation"></a>Začínáme s data přípravy

Vítá vás na Data přípravy Příručka Začínáme. 

Příprava dat poskytuje sadu nástrojů pro efektivní zkoumat, pochopení a odstraňování potíží v datech. Umožňuje využívat data v mnoha formulářích a transformaci dat do vyčištěny data, která je vhodnější pro příjem dat využití.

Příprava dat je nainstalován jako součást prostředí Azure Machine Learning Workbench.  Příprava dat pomocí místně nebo nasadit na cílový cluster a cloud jako prostředí runtime nebo spuštění.

Modul runtime pro dobu návrhu používá Python pro rozšiřitelnost a závisí na různých knihoven Python, jako je například Pandas. S dalšími součástmi nástroje Azure ML Workbench je potřeba nainstalovat Python, je nainstalována za vás. Ale pokud je potřeba nainstalovat další knihovny tyto knihovny musí být nainstalována v adresáři Azure ML Workbench Python není obvyklé Python adresáře. Můžete najít další podrobnosti o tom, jak nainstalovat balíčky [zde](data-prep-python-extensibility-overview.md).

Projekt je vyžadována před Data Prep, můžete použít po vytvoření tohoto projektu, že můžete připravit data. 

Přejděte do části Data projektu výběrem ikony Data ![ikona zdroje dat](media/data-prep-getting-started/data-source-icon.png) na levé straně obrazovky.  Klikněte na tlačítko "+" znovu do **přidání zdroje dat**. Průvodce zdrojem dat by měla spustit a přidá **zdroj dat** souboru (.dsource) do projektu po dokončení průvodce. Ve výchozím nastavení je zobrazení dat mřížky. Výše mřížky je také možné vybrat zobrazení metriky. V zobrazení metriky jsou uvedeny souhrnné statistiky.  Po zkontrolování souhrnné statistiky, klikněte na **Příprava** v horní části obrazovky. [Další informace o Průvodci zdroje dat](data-source-wizard.md) 

## <a name="building-blocks-of-data-preparation"></a>Stavební bloky dat přípravy ##
### <a name="the-package"></a>Balíček ###

Balíček je primární kontejner pro vaši práci. Balíček je artefaktu, který je uložený na a načten z disku. Při práci uvnitř klienta balíčku je neustále automaticky uloženo na pozadí. 

Výstup/výsledky balíčku může být zkoumána v Pythonu nebo prostřednictvím poznámkového bloku Jupyter.

Balíček lze provést napříč více moduly runtime včetně místních Python, Spark (včetně v Docker) a HDInsight.

Balíček obsahuje jeden nebo více toky dat, které jsou kroky a transformací použitá pro data.

Balíček jako zdroj dat (označované jako toku dat referenční) použít jiný balíček.

### <a name="the-dataflow"></a>Toku dat ###
Toku dat má zdroj a volitelné transformace, které jsou uspořádány prostřednictvím řady kroků a volitelné cíle. Kliknutím na krok znovu zpracuje všechny zdroje a transformace před a včetně vybraný krok.  Transformovaná data prostřednictvím tohoto kroku se zobrazí v mřížce. Kroky můžete přidat, přesunout a odstranit v rámci toku dat prostřednictvím seznamu krok.

Krok seznamu na pravé straně klienta, můžete otevřít a ukončeno zajistit více místa.

Více toky dat může existovat v uživatelském rozhraní najednou, každý toku dat je reprezentován jako na kartě v uživatelském rozhraní.

### <a name="the-source"></a>Zdroj
Zdroj je, kde data pocházejí z, a formát je v. Příprava balíčku Data vždy zdroje data z jiného Flow(Data Source) Data. Tento odkaz, který obsahuje informace o toku dat je. Každý zdroj obsahuje i prostředí pro jiné uživatele tak, aby ji nakonfigurovat. Zdroj vytvoří "obdélníkový" / tabulkové zobrazení data. Pokud zdrojová data původně "nepravidelné právo", pak normalizována strukturu jako "obdélníkový". [Příloha 2 poskytuje aktuální seznam podporovaných zdrojů](data-prep-appendix2-supported-data-sources.md).

### <a name="the-transform"></a>Pro transformaci ###
Transformace využívat data v daném formátu, provádět některé operace na data (například změna datového typu) a potom vytvoří data v novém formátu. Každý transformace má svou vlastní uživatelské rozhraní a behavior(s). Řetězení několik transformací společně pomocí kroků toku dat je základní funkce přípravy Data. [Příloha 3 poskytuje aktuální seznam podporovaných transformací](data-prep-appendix3-supported-transforms.md).

### <a name="the-inspector"></a>Kontrolor ###

Inspektoři se vizualizace dat a jsou k dispozici pro zvýšení povědomí o data.  Pochopení data a data quality vydává pomůže vám při rozhodování by měla být provedena akce, které (transformací). Některé inspektoři podporovat akce, které generují transformací. Počet hodnot Inspector například umožňuje vyberte hodnotu a pak použít filtr tuto hodnotu zahrnout nebo vyloučit tuto hodnotu. Inspektoři zadat také kontextu transformací. Například vyberte jeden nebo více sloupců změní možné transformace, které mohou být použity.

Sloupec může mít několik inspektoři v libovolném bodě v čase (například statistiky sloupce a Histogram). Napříč více sloupců může být také instancí Inspector. Například může mít všechny číselné sloupce histogramy ve stejnou dobu.

Inspektoři se zobrazí v profilace a v dolní části obrazovky.  Maximalizujte inspektoři neuvidíte větší v rámci oblast hlavního obsahu. Datová mřížka si můžete představit jako výchozí inspector. Všechny Inspector lze rozšířit do oblast hlavního obsahu. Na profilace dobře minimalizovat inspektoři v rámci oblast hlavního obsahu. Kliknutím na ikonu tužky během kontrolor přizpůsobte Inspector. Inspektoři změnit pořadí v rámci dobře pomocí přetažení.

Některé inspektoři podporovat režim "Bylo". Tento režim zobrazuje hodnotu nebo stavu před poslední transformace byla použita. Původní hodnoty se zobrazí šedě s aktuální hodnota v popředí a zobrazuje dopad transformace. [Příloha 4 poskytuje aktuální seznam podporovaných inspektoři](data-prep-appendix4-supported-inspectors.md).

### <a name="the-destination"></a>Cíl
 Cíl je, kde jste zápisu a exportu dat po jeho připravíte v toku dat. Daný toku dat může mít více cílů. Jednotlivé cíle obsahuje i prostředí pro jiné uživatele tak, aby ji nakonfigurovat. Cíl spotřebovává data ve formátu "obdélníkový" / tabulkové a zapíše ho do vhodného umístění v daném formátu. [Příloha 5 poskytuje aktuální seznam podporovaných cíle](data-prep-appendix5-supported-destinations.md).

### <a name="using-data-preparation"></a>Příprava dat pomocí ###
Příprava dat předpokládá základní pěti krocích metodika nebo přístup k přípravě data.

#### <a name="step-1-ingestion"></a>Krok 1: přijímání ####
Umožňuje importovat data pro přípravu dat pomocí **přidat zdroj dat** možnost v zobrazení projektu.  Prostřednictvím Průvodce zdrojem dat. všechny počáteční přijímání dat zpracovává.

#### <a name="step-2-understandprofile-the-data"></a>Krok 2: Pochopit, nebo profil data ####

Nejprve se podívejte na panelu kvality dat v horní části každý sloupec. Zelená představuje řádky, které mají hodnoty. Šedá představuje řádky s chybějící atd hodnotu null, hodnotu. Červená značí chybové hodnoty. Najeďte myší na panelu získat popisku tlačítka s přesnou počty řádků v každé tři intervalů. Používá pruh dat kvality hodnota na logaritmické stupnici proto vždy zkontrolujte skutečné čísla, která se podívat, hrubý objem dat chybí.

![sloupce](media/data-prep-getting-started/columns.png)

Abyste lépe pochopili vlastností dat v dalším kroku použijte kombinaci další kontroly a mřížky.  Spusťte formulování hypotézy o přípravě dat vyžaduje pro další analýzu. Většina inspektoři fungovat na jeden sloupec nebo malý počet sloupců.  

Je pravděpodobné, že několik inspektoři do několika sloupců jsou potřebné k pochopení data. Můžete procházet různé inspektoři v profilace dobře. V rámci také můžete také můžete přesunout inspektoři head seznamu Chcete-li je zobrazit v oblasti okamžitě zobrazit.

![inspektoři](media/data-prep-getting-started/inspectors.PNG)

Různých kontrol jsou k dispozici pro sloupce kategorií proměnné Průběžné vs. V nabídce Inspector povolí nebo zakáže možnosti v závislosti na typu proměnné nebo sloupce, které máte.

Při práci s široké datové sady, které mají mnoho sloupců, se doporučuje protokol přístup pracovat s podmnožin. Tento postup zahrnuje zaměřené na malý počet sloupců (například 5-10), přípravy je a pak práce prostřednictvím zbývající sloupce. Nástroj inspector mřížky podporuje vertikální dělení sloupců a, pokud máte více než 300 sloupce je třeba "stránka" prostřednictvím je.
 

#### <a name="step-3-transform-the-data"></a>Krok 3: Transformace dat ####
Transformace změnit data a povolit spuštění dat pro podporu aktuální pracovní předpoklad. Transformace se zobrazí jako kroky v kroku seznamu na pravé straně. Je možné "čas cesta" prostřednictvím seznamu krok kliknutím na libovolný libovolný bod v seznamu krok.

Zelená ikona směrem doleva od daného kroku označuje, že byla spuštěna a odráží data provádění pro transformaci. Svislá čára nalevo od kroku označuje aktuální stav dat v inspektoři.

![kroky](media/data-prep-getting-started/steps.PNG)

Proveďte malé časté změny dat a k ověření (krok 4) po každé změně, jako předpoklad zpracovaní.

#### <a name="step-4-verify-the-impact-of-the-transformation"></a>Krok 4: Ověření dopad transformace. 
Rozhodněte, pokud předpoklad byla správná. Pokud je správná, vytvořte další předpoklad a zopakujte kroky 2 – 3 pro novým. Pokud je to nesprávná, pak vrátit zpět poslední zpracování a vyvíjet nové předpoklad a zopakujte kroky 2 – 3.

Primární způsob jak určit, pokud pro transformaci měl dopad na pravém je použití inspektoři. Použít existující. Inspektoři použití s nimi vliv povoleno nebo spuštění více inspektoři, chcete-li zobrazit data na danými body v čase.

![bylo inspector](media/data-prep-getting-started/halo1.PNG) ![bylo inspector](media/data-prep-getting-started/halo2.PNG)

Chcete-li vrátit zpět transformaci, přejděte seznamu kroky na pravé straně uživatelského rozhraní. (Seznam kroků panelu možná muset být odebrány zpět na. Otevřete ho kliknutím na dvojitou šipku odkazující levém). V panelu vyberte transformace, která byla spuštěna, které chcete vrátit zpět. Vyberte z rozevírací nabídky na pravé straně bloku uživatelského rozhraní. Vyberte buď **upravit** provést změny nebo **odstranit** odeberte ze seznamu kroků a toku dat pro transformaci.

#### <a name="step-5-output"></a>Krok 5: výstup 
Po dokončení přípravy vaše data, můžete psát toku dat pro výstup. Toku dat může mít mnoho výstupy. V nabídce transformací můžete vybrat, která výstup, že který má k zapsání jako datové sadě. Můžete také vybrat cíl výstupu. 

## <a name="list-of-appendices"></a>Seznam přílohy 
[Příloha 2 – podporované zdroje dat](data-prep-appendix2-supported-data-sources.md)  
[Transformuje příloha 3 – podporované](data-prep-appendix3-supported-transforms.md)  
[Příloha 4 - podporované kontroly](data-prep-appendix4-supported-inspectors.md)  
[Příloha 5 – podporované cíle](data-prep-appendix5-supported-destinations.md)  
[Příloha 6 – ukázky filtr výrazů v Pythonu](data-prep-appendix6-sample-filter-expressions-python.md)  
[Příloha 7 – ukázka transformace výrazy toku dat v Pythonu](data-prep-appendix7-sample-transform-data-flow-python.md)  
[Příloha 8 – Ukázka zdroje dat v Pythonu](data-prep-appendix8-sample-source-connections-python.md)  
[Příloha 9 – ukázka připojení cílů v Pythonu](data-prep-appendix9-sample-destination-connections-python.md)  
[Příloha 10 – ukázka sloupec transformuje v Pythonu](data-prep-appendix10-sample-custom-column-transforms-python.md)  

## <a name="see-also"></a>Viz také

[Pokročilé data přípravu kurzu](tutorial-bikeshare-dataprep.md)