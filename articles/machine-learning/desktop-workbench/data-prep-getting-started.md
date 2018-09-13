---
title: Začínáme s přípravou dat pro Azure Machine Learning | Dokumentace Microsoftu
description: Toto je příručka Začínáme pro přípravu datové části aplikace workbench AML
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: ffff3d274e093e9ce4aa32dee7b033bd1ed288bc
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35643526"
---
# <a name="getting-started-with-data-preparation"></a>Začínáme s přípravou dat

Vítá vás na Data přípravy příručku Začínáme. 

Příprava dat poskytuje sadu nástrojů pro efektivní zkoumat, pochopení a odstraňování potíží v datech. Umožňuje využívat data v mnoha formách a transformovat tato data na očištěnou data, která je vhodnější pro příjem dat využití.

Přípravu dat je nainstalován jako součást možností Azure Machine Learning Workbench.  Používat přípravu dat místně nebo nasadit na cílový cluster a jako prostředí modulu runtime nebo spuštění v cloudu.

Modul runtime času návrhu používá Python pro rozšíření a závisí na různé knihovny jazyka Python, jako je například Pandas. Jak je s dalšími komponentami Azure ML Workbench není nutné k instalaci Pythonu, se nainstaluje za vás. Nicméně pokud je potřeba nainstalovat další knihovny se tyto knihovny musí být nainstalované v adresáři Azure ML Workbench Pythonu není obvyklé Python adresáři. Další podrobnosti o tom, jak nainstalovat balíčky najdete [tady](data-prep-python-extensibility-overview.md).

Projekt je povinný přípravy dat, abyste mohli používat po vytvoření projektu že můžete připravit data. 

Přejděte do části dat projektu výběrem ikony Data ![ikona zdroje dat](media/data-prep-getting-started/data-source-icon.png) na levé straně obrazovky.  Klikněte na tlačítko "+" znovu do **přidat zdroj dat**. V Průvodci zdroje dat by měl spustit a přidá **zdroj dat** (.dsource) soubor do projektu po dokončení průvodce. Ve výchozím nastavení je zobrazení dat mřížce. Nad mřížkou je také možné vybrat zobrazení metrik. V zobrazení metrik jsou uvedeny souhrnné statistiky.  Jakmile zkontrolujete souhrnné statistiky, klikněte na **připravit** v horní části obrazovky. [Další informace o Průvodci zdroje dat](data-source-wizard.md) 

## <a name="building-blocks-of-data-preparation"></a>Stavební bloky přípravu dat ##
### <a name="the-package"></a>Balíček ###

Balíček je primárním kontejnerem pro práci. Balíček je artefakt, který je uložený na a načíst z disku. Při práci uvnitř klienta balíček je neustále automaticky uložené na pozadí. 

Výstup a výsledky balíčku můžete prozkoumat v Pythonu, nebo prostřednictvím Poznámkový blok Jupyter.

Balíček je možné spouštět napříč různými moduly runtime, včetně místního Pythonu, Spark (i v Dockeru) a HDInsight.

Balíček obsahuje jeden nebo více toků, které jsou uvedené kroky a transformací použitá pro data.

Balíček může použít jiný balíček jako zdroj dat (označované jako odkaz na toku dat).

### <a name="the-dataflow"></a>Toku dat ###
Toku dat má zdroj a volitelné transformační soubory, které jsou seřazené prostřednictvím řady kroků a volitelné cíle. Kliknutím na krok znovu provede všechny zdroje a před transformací, včetně vybraný krok.  Transformovaná data prostřednictvím tohoto kroku se zobrazí v mřížce. Kroky můžete přidat, přesunout a odstranit v rámci toku dat prostřednictvím seznamu krok.

Krok seznamu na pravé straně klienta může otevřel a uzavřel poskytnout více místa na obrazovce.

V uživatelském rozhraní najednou může existovat více toků, každý tok dat je vyjádřena jako kartu v uživatelském rozhraní.

### <a name="the-source"></a>Zdroj
Pokud data pocházejí z, a formát je v je zdroj. Příprava balíčku dat vždy zdrojů data z jiného Flow(Data Source) Data. Tento odkaz, který obsahuje informace o toku dat je. Každý zdroj má různé uživatelské prostředí, aby mohla být nakonfigurované. Zdroj vytvoří "obdélníkový" / tabulkového zobrazení data. Pokud zdrojová data původně "ragged right", pak struktury je normalizovat "obdélníkový". [Příloha 2 poskytuje aktuální seznam podporovaných zdrojů](data-prep-appendix2-supported-data-sources.md).

### <a name="the-transform"></a>Transformace ###
Transformace zpracování dat v daném formátu, provádět některé operace s daty (jako je například změna datového typu) a pak vytvářejí data v novém formátu. Každá transformace má své vlastní uživatelské rozhraní a behavior(s). Zřetězení více transformací pomocí kroků v toku dat je základní přípravu dat. funkce. [Dodatek 3 poskytuje aktuální seznam podporovaných transformací](data-prep-appendix3-supported-transforms.md).

### <a name="the-inspector"></a>Inspektor ###

Kontroly jsou vizualizace dat a ke zlepšení objasnit význam dat k dispozici.  Principy data a data kvality vydá pomůže rozhodnout, jaké akce (transformace) je třeba provést. Některé kontroly podporovat akce, které generují transformací. Například inspektor počet hodnot vám umožní vybrat hodnotu a pak použít filtr tuto hodnotu zahrnout nebo vyloučit tuto hodnotu. Kontroly může také poskytnout kontext pro transformace. Například vyberte jeden nebo více sloupců změní možné transformace, které mohou být použity.

Sloupec může mít více kontroly v libovolném bodě v čase (například statistiky sloupce a Histogram). Do několika sloupců může být také instance je kontrola. Všechny číselné sloupce může mít například histogramy ve stejnou dobu.

Inspektoři joinkind profilace a v dolní části obrazovky.  Maximalizujte kontroly a zobrazte je větší v rámci oblast hlavního obsahu. Datová mřížka můžete představit jako výchozí inspektor. Libovolný nástroj Inspector se rozšířit do oblast hlavního obsahu. Kontroly v rámci oblast hlavního obsahu minimalizováno profilaci dobře. Přizpůsobení je kontrola po kliknutí na ikonu tužky v okně Inspektor. Inspektoři přesunout v pořadí v rámci kontejneru pomocí přetažení.

Některé kontroly podporovat režim "Halo". Tento režim zobrazuje hodnotu nebo stav před poslední transformace byla použita. Původní hodnota se zobrazí v šedé s aktuální hodnotou v popředí a ukazuje účinek transformace. [Dodatek 4 poskytuje aktuální seznam podporovaných kontroly](data-prep-appendix4-supported-inspectors.md).

### <a name="the-destination"></a>Cíl
 Cíl je ve kterém můžete zápisu/export dat do Jakmile ji připravíte v toku dat. Daný tok dat může mít více cílů. Jednotlivé cíle má různé uživatelské prostředí, aby mohla být nakonfigurované. Cíl spotřebovává data ve formátu "obdélníkový" / tabulkových a zapíše jej do umístění v daném formátu. [Dodatek 5 obsahuje aktuální seznam podporovaných cílů](data-prep-appendix5-supported-destinations.md).

### <a name="using-data-preparation"></a>Pomocí přípravu dat ###
Příprava dat předpokládá základní pěti krocích metodologie/přístup k přípravě dat.

#### <a name="step-1-ingestion"></a>Krok 1: příjem ####
Importovat data pro přípravu dat s použitím **přidat zdroj dat** možnost v rámci zobrazení projektu.  Všechny počáteční ingestování dat probíhá prostřednictvím Průvodce zdrojem dat.

#### <a name="step-2-understandprofile-the-data"></a>Krok 2: Pochopení/profil dat ####

Nejprve se podívejte na datový pruh kvality v horní části každého sloupce. Zelená představuje řádky, které se mají hodnoty. Šedé představuje řádky s chybějící atd má hodnotu null, hodnotu. Červená značí chybovými hodnotami. Najeďte myší na panelu zobrazíte popisku tlačítka s přesnou počty řádků ve všech tří bloků. Pruh kvality dat používá logaritmické měřítko proto nikdy zkontrolovat skutečné čísla zobrazíte přibližnou představu svazku chybějící data.

![Sloupce](media/data-prep-getting-started/columns.png)

Pak pomocí kombinace další kontroly a mřížky lépe pochopit vlastnosti data.  Začněte formulování hypotézy informace o přípravě dat, které jsou nutné pro další analýzy. Většina kontroly pracovat na jeden sloupec nebo malý počet sloupců.  

Je pravděpodobné, že několik kontroly napříč několika sloupců jsou potřeba rozumět datům. Můžete procházet různé kontroly v profilaci dobře. V rámci kontejneru můžete také přesunout kontroly na jednoho seznamu Chcete-li zobrazit v oblasti okamžitě zobrazit.

![Kontroly](media/data-prep-getting-started/inspectors.PNG)

Jiné kontroly jsou k dispozici pro průběžné vs zařazené do kategorií proměnné nebo sloupce. V nabídce inspektoru povolí nebo zakáže možnosti v závislosti na typu proměnné nebo sloupce, které máte.

Při práci s široké datové sady, které mají mnoho sloupců, je vhodné pragmatický postoj práce s podmnožin. Tento přístup zahrnuje zaměření na malý počet sloupců (například 5-10), jejich přípravy a pak funguje prostřednictvím zbývajících sloupců. Inspektor mřížky podporuje vertikální dělení ze sloupce, takže pokud máte více než 300 sloupce pak budete muset "page" Díky nim.
 

#### <a name="step-3-transform-the-data"></a>Krok 3: Transformace dat ####
Transformace změnit data a umožňují zpracování dat pro podporu aktuální pracovní hypotéza. Transformace se zobrazí jako kroky v seznamu krok na pravé straně. Je možné "doba trvání cesty" prostřednictvím seznamu krok po kliknutí na libovolné libovolného bodu v seznamu krok.

Zelená ikona vlevo od daného kroku označuje, že byla spuštěna a provádění transformací, která zobrazuje data. Svislá čára nalevo od kroku označuje aktuální stav dat v inspektor.

![kroky](media/data-prep-getting-started/steps.PNG)

Pokuste se uskutečnit malé časté změny dat a k ověření (krok 4) po každé změně jako hypotézu vyvíjí.

#### <a name="step-4-verify-the-impact-of-the-transformation"></a>Krok 4: Ověření dopadu transformace. 
Rozhodněte, pokud hypotéza byla správná. Pokud je správná, vyvíjet další hypotézy a opakujte kroky 2 až 3 pro nové. Pokud je nesprávná, pak vrátit zpět poslední transformace a vyvíjet nové hypotézy a opakujte kroky 2 až 3.

Primárním způsob, jak určit, pokud transformací, která se má správný dopad, je použít inspektor. Použít existující. Použití kontroly s Haló efekt povoleno nebo spuštění více kontroly, chcete-li zobrazit data na zadané body v čase.

![Halo inspektoru](media/data-prep-getting-started/halo1.PNG) ![Halo inspektoru](media/data-prep-getting-started/halo2.PNG)

Pokud chcete vrátit zpět transformaci, přejděte na pravé straně uživatelského rozhraní seznamu kroků. (Odebrání zpět na panelu seznamu kroků potřebovat. Pokud chcete soubor otevřít, klikněte na dvojitou šipku směřující vlevo). V panelu vyberte transformaci, která se spustil, které chcete vrátit zpět. Vyberte rozevírací seznam na pravé straně bloku uživatelského rozhraní. Vyberte buď **upravit** provést změny nebo **odstranit** odebrat ze seznamu kroků a toku dat transformace.

#### <a name="step-5-output"></a>Krok 5: výstup 
Po dokončení přípravy dat, můžete psát toku dat do výstupu. Toku dat může mít mnoho výstupy. Z nabídky transformací můžete vybrat, který výstup, že chcete k zapsání jako datové sady. Můžete také vybrat cílový ve výstupu. 

## <a name="list-of-appendices"></a>Seznam příloh 
[Příloha 2 – podporované zdroje dat](data-prep-appendix2-supported-data-sources.md)  
[Dodatek 3 – podporované transformace](data-prep-appendix3-supported-transforms.md)  
[Dodatek 4 – podporované kontroly](data-prep-appendix4-supported-inspectors.md)  
[Dodatek 5 - podporovaných cílů](data-prep-appendix5-supported-destinations.md)  
[Příloha 6 - vzorových výrazů filtru v Pythonu](data-prep-appendix6-sample-filter-expressions-python.md)  
[Příloha 7 – ukázka transformace toku dat výrazy v jazyce Python](data-prep-appendix7-sample-transform-data-flow-python.md)  
[Příloha 8 – ukázkové zdroje dat v Pythonu](data-prep-appendix8-sample-source-connections-python.md)  
[Příloha 9 – ukázka připojení cíle v Pythonu](data-prep-appendix9-sample-destination-connections-python.md)  
[Příloha 10 – ukázky sloupec transformuje v Pythonu](data-prep-appendix10-sample-custom-column-transforms-python.md)  

## <a name="see-also"></a>Viz také

[Kurz přípravy dat pokročilé](tutorial-bikeshare-dataprep.md)