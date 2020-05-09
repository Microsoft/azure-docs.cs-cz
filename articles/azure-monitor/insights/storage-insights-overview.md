---
title: Monitorování Azure Storage služeb pomocí Azure Monitor pro úložiště (Preview) | Microsoft Docs
description: Tento článek popisuje Azure Monitor funkce úložiště, která správcům úložiště poskytuje rychlý přehled o problémech s výkonem a využitím jejich Azure Storagech účtů.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/15/2019
ms.openlocfilehash: 497ea5a27a56ed5f8192b694fac1c52c8bf57ca6
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691117"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage-preview"></a>Monitorování služby úložiště pomocí Azure Monitor pro úložiště (Preview)

Azure Monitor pro Storage (Preview) poskytuje ucelený přehled o vašich Azure Storagech účtech tím, že přináší jednotný přehled o výkonu, kapacitě a dostupnosti služby Azure Storage Services. Kapacitu úložiště a výkon můžete sledovat dvěma způsoby, zobrazit přímo z účtu úložiště nebo zobrazit z Azure Monitor k zobrazení napříč skupinami účtů úložiště. 

Tento článek vám pomůže pochopit Azure Monitor prostředí pro úložiště (verze Preview), která vám pomůžou odvoditelné znalosti o stavu a výkonu účtů úložiště ve velkém měřítku s možností soustředit se na aktivní hotspoty a diagnostikovat latenci, omezení a problémy s dostupností.

## <a name="introduction-to-azure-monitor-for-storage-preview"></a>Úvod do Azure Monitor pro úložiště (Preview)

Předtím, než se začnete do prostředí, byste měli pochopit, jak prezentuje a vizualizují informace. Bez ohledu na to, jestli vyberete funkci úložiště přímo z účtu úložiště nebo z Azure Monitor, nabízí Azure Monitor úložiště konzistentní prostředí. 

Kombinace it přináší:

* **V perspektivě stupnice** se zobrazuje zobrazení snímku jejich dostupnosti na základě stavu služby úložiště nebo operace rozhraní API. využití ukazuje celkový počet požadavků, které služba Storage přijme, a latence ukazující průměrnou dobu, kterou služba úložiště nebo typ operace rozhraní API přijímá při zpracování požadavků. Kapacitu můžete zobrazit také podle objektů blob, File, Table a Queue.

* Procházejte **analýzu** konkrétního účtu úložiště, která vám pomůžou diagnostikovat problémy nebo provádět podrobnou analýzu podle kategorie dostupnosti, výkonu, selhání a kapacity. Výběrem jedné z těchto možností získáte Podrobný pohled na metriky.  

* **Přizpůsobitelné** , kde můžete měnit metriky, které chcete zobrazit, upravit nebo nastavit prahové hodnoty, které odpovídají vašim limitům, a uložit jako vlastní sešit. Grafy v sešitě lze připnout na řídicí panel Azure.  

Tato funkce nevyžaduje, abyste povolili ani nenakonfigurovali cokoli. ve výchozím nastavení se shromažďují metriky úložiště z vašich účtů úložiště. Pokud neznáte metriky dostupné na Azure Storage, podívejte se na popis a definici v části Azure Storage metriky tím, že zkontrolujete [metriky služby Azure Storage](../../storage/common/storage-metrics-in-azure-monitor.md).

>[!NOTE]
>Pro přístup k této funkci se neplatí žádné poplatky a účtují se vám jenom Azure Monitor základní funkce, které nakonfigurujete nebo povolíte, jak je popsáno na stránce [Azure monitor s podrobnostmi o cenách](https://azure.microsoft.com/pricing/details/monitor/) .

>[!NOTE]
>Azure Monitor pro úložiště nepodporuje účty pro [obecné účely v1](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts).
>

## <a name="view-from-azure-monitor"></a>Zobrazit z Azure Monitor

Z Azure Monitor můžete zobrazit podrobnosti o transakcích, latencích a kapacitě z více účtů úložiště ve vašem předplatném a zajistit, aby bylo možné identifikovat výkon, problémy s kapacitou a selhání.

Pokud chcete zobrazit využití a dostupnost účtů úložiště ve všech předplatných, proveďte následující kroky.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. V Azure Portal v levém podokně vyberte **monitor** a v části **přehledy** vyberte **účty úložiště (Preview)**.

    ![Zobrazení více účtů úložiště](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>Sešit s přehledem

V sešitě s **přehledem** pro vybrané předplatné tabulka zobrazuje interaktivní metriky úložiště a stav dostupnosti služby pro až 10 účtů úložiště seskupených v rámci předplatného. Výsledky můžete filtrovat podle vybraných možností z následujících rozevíracích seznamů:

* V seznamu **předplatných** jsou uvedena pouze předplatná, která mají účty úložiště.  

* **Účty úložiště** – ve výchozím nastavení jsou předem vybrané 10 účtů úložiště. Pokud v voliči oboru vyberete všechny nebo víc účtů úložiště, vrátí se až 200 účtů úložiště. Pokud jste například v rámci tří předplatných vybrali celkem 573 účtů úložiště, zobrazí se pouze 200 účty. 

* **Časový rozsah** – ve výchozím nastavení zobrazí poslední 4 hodiny informací na základě odpovídajících výběrů.

Dlaždice počítadla v rozevíracích seznamech shrnuje celkový počet účtů úložiště v předplatném a odráží, kolik z nich bylo vybráno. Pro sloupce v sešitu jsou podmíněné barevné kódování nebo Heat mapy, které vykazují metriky transakcí nebo chyby. Hluboká barva má nejvyšší hodnotu a světlejší barva je založena na nejnižších hodnotách. Pro sloupce založené na chybách je hodnota červená a pro sloupce založené na metrikách je hodnota modrá.

Vyberte hodnotu v poli **dostupnost**sloupců, **latence e2e**, **latence serveru**a typ a **chyby transakce** , které vás přesměrují na sestavu přizpůsobenou konkrétnímu typu metriky úložiště, který odpovídá sloupci vybranému pro tento účet úložiště. Další informace o sešitech pro jednotlivé kategorie najdete v části [podrobné sešity úložiště](#detailed-storage-workbooks) níže. 

>[!NOTE]
>Podrobnosti o tom, které chyby lze v sestavě zobrazit, naleznete v tématu [schéma typu odpovědi](../../storage/common/monitor-storage-reference.md#metrics-dimensions) a hledání typů odpovědí, jako je například **ServerOtherError**, **ClientOtherError**, **ClientThrottlingError**. V závislosti na vybraných účtech úložiště platí, že pokud je nahlášeno více než tři typy chyb, jsou všechny ostatní chyby zastoupeny v kategorii **jiné**.

Výchozí prahová hodnota **dostupnosti** je:

* Upozornění – 99%
* Kritické – 90%

Pokud chcete nastavit prahovou hodnotu dostupnosti založenou na výsledcích vašich pozorování nebo požadavků, přečtěte si část [Úprava prahové hodnoty dostupnosti](#modify-the-availability-threshold). 

### <a name="capacity-workbook"></a>Pracovní sešit kapacity

V horní části stránky vyberte **kapacitu** a otevře se sešit **kapacity** . Zobrazuje celkovou velikost úložiště využitého v účtu a kapacity využité jednotlivými datovými službami v účtu, které vám pomůžou identifikovat a v rámci využitého úložiště.

![Pracovní sešit s více účty úložiště](./media/storage-insights-overview/storage-account-capacity-02.png) 

Pro sloupce v sešitu je podmíněné barevné kódování nebo Heat mapy, které vykazují metriky kapacity modrou hodnotou. Hluboká barva má nejvyšší hodnotu a světlejší barva je založena na nejnižších hodnotách.

Když vyberete nějakou hodnotu v jednom ze sloupců v sešitu, přejdete k podrobnostem o sešitu **kapacity** účtu úložiště. Další podrobnosti o sestavě přechodu k podrobnostem jsou popsány v části [podrobné sešity úložiště](#detailed-storage-workbooks) níže. 

## <a name="view-from-a-storage-account"></a>Zobrazení z účtu úložiště

Přístup k Azure Monitor pro virtuální počítače přímo z účtu úložiště:

1. V Azure Portal vyberte účty úložiště.

2. V seznamu vyberte účet úložiště. V části monitorování vyberte přehledy (Preview).

    ![Stránka s přehledem vybraného účtu úložiště](./media/storage-insights-overview/storage-account-direct-overview-01.png)

V sešitě s **přehledem** pro účet úložiště se zobrazuje několik metrik výkonu úložiště, které vám pomůžou rychle vyhodnotit:

* Stav služby úložiště, který okamžitě zjistí, jestli problém mimo váš ovládací prvek nemá vliv na službu úložiště v oblasti, do které je nasazený, který je uvedený ve sloupci **Shrnutí** .

* Interaktivní grafy výkonu zobrazující nejdůležitější podrobnosti týkající se kapacity úložiště, dostupnosti, transakcí a latence.  

* Metriky a stavové dlaždice zvýrazňují dostupnost služby, celkový počet transakcí do služby úložiště, latenci E2E a latenci serveru.

Po výběru některého z tlačítek pro **chyby**, **výkon**, **dostupnost**a **kapacita** se otevře příslušný sešit. 

![Stránka s přehledem vybraného účtu úložiště](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>Podrobné sešity úložiště

Bez ohledu na to, jestli jste vybrali hodnotu v poli **dostupnost**sloupců, **latenci e2e**, **latenci serveru**a **typ nebo chybu transakce** , nebo pokud pro chyby, **výkon**, **dostupnost**a **kapacitu** **ze sešitu s** **přehledem** z konkrétního účtu úložiště vyberete jedno z tlačítek, každý z nich dodá sadu interaktivních informací souvisejících s úložištěm **, které**jsou přizpůsobené této kategorii.  

* **Dostupnost** otevře sešit **dostupnosti** . Zobrazuje aktuální stav služby Azure Storage, což je tabulka s dostupným stavem jednotlivých objektů zařazených do kategorií podle datové služby definované v účtu úložiště s trendovou linií reprezentující vybraný časový rozsah a graf trend dostupnosti pro každou datovou službu v účtu.  

    ![Příklad sestavy dostupnosti](./media/storage-insights-overview/storage-account-availability-01.png)

* **E2e latence** a **latence serveru** otevírá sešit **výkonu** . Zahrnuje souhrnnou stavovou dlaždici, která ukazuje latenci E2E a latenci serveru, graf výkonu E2E a latenci serveru a ruší latenci úspěšných volání rozhraní API podle datové služby definované v účtu úložiště.

    ![Příklad sestavy výkonu](./media/storage-insights-overview/storage-account-performance-01.png)

* Výběrem některé z kategorií chyb uvedených v mřížce otevřete sešit **selhání** . Sestava zobrazuje dlaždice metrik všech dalších chyb na straně klienta s výjimkou popsaných a úspěšných požadavků, chyb při omezení klienta, grafu výkonu pro **typ odpovědi transakce typu** metrika specifická pro atribut ClientOtherError a dvou tabulek – **transakce podle názvu rozhraní API** a **transakcí podle typu odpovědi**.

   ![Příklad sestavy o selhání](./media/storage-insights-overview/storage-account-failures-01.png)

* **Kapacita** otevře pracovní **sešit.** Zobrazuje celkové množství úložiště, které se používá pro každý datový objekt úložiště v daném účtu v dlaždicích a v grafu, a kolik datových objektů se v účtu ukládá.  

    ![Vybraná stránka kapacity účtu úložiště](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>Připnout a exportovat

Libovolný oddíl metriky můžete připnout na řídicí panel Azure tak, že vyberete ikonu připínáčku v pravém horním rohu oddílu.

![Připnout oddíl metriky na příklad řídicího panelu](./media/storage-insights-overview/workbook-pin-example.png)

**Přehled** a sešity **kapacit** pro více předplatných a účet úložiště podporují Export výsledků ve formátu aplikace Excel výběrem ikony šipky dolů vpravo od ikony připínáčku.

![Příklad exportu výsledků mřížky sešitu](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage-preview"></a>Přizpůsobení Azure Monitor pro úložiště (Preview)

Tato část popisuje běžné scénáře úprav sešitu pro přizpůsobení v rámci podpory vašich potřeb analýzy dat:

* Určení rozsahu sešitu pro vždycky výběr konkrétního předplatného nebo účtů úložiště
* Změna metrik v mřížce
* Změna prahové hodnoty dostupnosti
* Změnit vykreslování barev

Vlastní nastavení se uloží do vlastního sešitu, aby se zabránilo přepsání výchozí konfigurace v našem publikovaném sešitu. Sešity se ukládají v rámci skupiny prostředků, a to buď v části **Mé sestavy** , která je pro vás nebo ve **sdílených sestavách** přístupná pro všechny s přístupem ke skupině prostředků. Po uložení vlastního sešitu musíte přejít do galerie sešitů a spustit ho.

![Spustit galerii sešitů z panelu příkazů](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>Zadání předplatného nebo účtu úložiště

Můžete nakonfigurovat předplatné pro více předplatných a účet úložiště **– Přehled** nebo **pracovní** sešity, které mají být při každém spuštění vymezeny na konkrétní předplatné nebo účty úložiště, a to provedením následujících kroků.

1. Na portálu vyberte **monitor** a pak v levém podokně vyberte **účty úložiště (Preview)** .

2. V sešitu **přehledu** na panelu příkazů vyberte **Upravit**.

3. V rozevíracím seznamu **odběry** vyberte jednu nebo více předplatných, pro které chcete výchozí nastavení. Nezapomeňte, že sešit podporuje výběr až 10 předplatných.  

4. V rozevíracím seznamu **účty úložiště** vyberte jednu nebo víc účtů, pro které chcete výchozí nastavení. Nezapomeňte, že sešit podporuje výběr až na celkem 200 účtů úložiště. 

5. Na panelu příkazů vyberte **Uložit jako** a uložte kopii sešitu s vlastními úpravami a potom kliknutím na **hotové úpravy** vraťte do režimu čtení.  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>Úprava metrik a barev v sešitu

Předem připravené sešity obsahují data metriky a máte možnost upravit nebo odebrat některou z vizualizací a přizpůsobit je konkrétním potřebám vašeho týmu.

V našem příkladu budeme pracovat se sešitem kapacity pro více předplatných a účtů úložiště, abyste mohli ukázat, jak:

* Odebrání metriky
* Změnit vykreslování barev

Stejné změny můžete provádět u všech předdefinovaných sešitů **selhání**, **výkonu**, **dostupnosti**a **kapacity** .

1. Na portálu vyberte **monitor** a pak v levém podokně vyberte **účty úložiště (Preview)** .

2. Vyberte možnost **kapacita** pro přepnutí do sešitu kapacity a z panelu příkazů na panelu příkazů vyberte možnost **Upravit** .

    ![Výběr úpravy pro úpravu sešitu](./media/storage-insights-overview/workbook-edit-workbook.png)

3. Vedle části metriky vyberte **Upravit**.

    ![Výběr možnosti upravit pro úpravu metriky sešitu kapacity](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. Chystáme se odebrat sloupec **Timeline využité kapacity účtu** , takže vyberte **Nastavení sloupce** v mřížce metriky.

    ![Upravit nastavení sloupce](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. V podokně **Upravit nastavení sloupce** vyberte v části **sloupce** část **Microsoft. Storage/Storageaccounts-Capacity-UsedCapacity Timeline $ | Účet použil časovou osu kapacity $** a v rozevíracím **seznamu vyberte** **skrytý**.

6. Kliknutím na **Uložit a zavřít** potvrďte změnu.

Teď změníme barevný motiv pro metriky kapacity v sestavě tak, aby místo modrého používal zelenou barvu.

1. Vyberte **Nastavení sloupce** v mřížce metriky.

2. V podokně **Upravit nastavení sloupců** vyberte v části **sloupce** část **Microsoft. Storage/storageaccounts-Capacity-UsedCapacity $ | Microsoft. Storage/storageaccounts/blobservices-Capacity-BlobCapacity $ | Microsoft. Storage/Storageaccounts/služby úložiště-Capacity-$ | Microsoft. Storage/storageaccounts/queueservices-Capacity-QueueCapacity $ | Microsoft. Storage/storageaccounts/tableservices-Capacity-TableCapacity $**. V **paletě barev**v rozevíracím seznamu vyberte **zelený**.

3. Kliknutím na **Uložit a zavřít** potvrďte změnu.

4. Na panelu příkazů vyberte **Uložit jako** a uložte kopii sešitu s vlastními úpravami a potom kliknutím na **hotové úpravy** vraťte do režimu čtení.  

### <a name="modify-the-availability-threshold"></a>Úprava prahové hodnoty dostupnosti

V tomto příkladu pracujeme se sešitem kapacity účtu úložiště a demonstrují, jak změnit prahovou hodnotu dostupnosti. Ve výchozím nastavení je procentuální hodnota dostupnosti sestavy dlaždice a mřížka nakonfigurovaná s minimální prahovou hodnotou 90 a maximální prahovou hodnotou 99. Chystáme se změnit minimální prahovou hodnotu **dostupnosti%** v mřížce **dostupnost podle názvu rozhraní API** na 85%, což znamená, že se stav změní na kritický, pokud je prahová hodnota menší než 85%. 

1. Na portálu vyberte **účty úložiště** a v seznamu vyberte účet úložiště.

2. V levém podokně vyberte **přehledy (Preview)** .

3. V sešitu vyberte možnost **dostupnost** , kterou chcete přepnout do sešitu dostupnosti, a pak na panelu příkazů vyberte **Upravit** . 

4. Posuňte se dolů k dolní části stránky a na levé straně vedle mřížky **dostupnost podle rozhraní API** , vyberte **Upravit**.

    ![Nastavení pro úpravu dostupnosti podle názvu rozhraní API v mřížce](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. Vyberte **Nastavení sloupce** a potom v podokně **Upravit nastavení sloupce** v části **sloupce** vyberte **dostupnost (%) (Prahové hodnoty + formátované)**.

6. Změňte hodnotu pro **kritický** stav z **90** na **85** a pak klikněte na **Uložit a zavřít**.

    ![Úprava prahové hodnoty dostupnosti pro kritický stav](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. Na panelu příkazů vyberte **Uložit jako** a uložte kopii sešitu s vlastními úpravami a potom kliknutím na **hotové úpravy** vraťte do režimu čtení.

## <a name="troubleshooting"></a>Odstraňování potíží

Tato část vám pomůže s diagnostikou a řešením potíží s některými běžnými problémy, se kterými se můžete setkat při používání Azure Monitor pro úložiště (Preview). K vyhledání informací týkajících se konkrétního problému použijte níže uvedený seznam.

### <a name="resolving-performance-capacity-or-availability-issues"></a>Řešení problémů s výkonem, kapacitou nebo dostupností

Chcete-li pomoct řešit problémy související s úložištěm, které identifikujete pomocí Azure Monitor pro úložiště (Preview), přečtěte si téma [pokyny k řešení potíží](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)s Azure Storage.  

### <a name="why-can-i-only-see-200-storage-accounts"></a>Proč mohu zobrazit jenom 200 účtů úložiště?

Počet vybraných účtů úložiště má limit 200, bez ohledu na počet vybraných předplatných.

### <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>Co se stane po kliknutí na nedávno připnuté dlaždici na řídicím panelu?

* Pokud kliknete kamkoli na dlaždici, přejdete na kartu, ze které se dlaždice připnula. Pokud například připnete graf na kartu Přehled účtu úložiště, pak když na řídicím panelu kliknete na tuto dlaždici, otevře se výchozí zobrazení. když ale připnete graf z vlastního uloženého kopírování, otevře se vaše uložené zobrazení kopie.
* Ikona filtru v levém horním rohu nadpisu otevře kartu konfigurovat nastavení dlaždic.
* Ikona elipsy v pravém horním rohu vám poskytne možnosti pro přizpůsobení dat nadpisu, přizpůsobení, aktualizace a odebrání z řídicího panelu.

### <a name="what-happens-when-i-save-a-workbook"></a>Co se stane, když sešit ukládám?

* Když sešit uložíte, můžete vytvořit novou kopii sešitu s úpravami a změnit název. Při uložení se sešit nepřepisuje, aktuální sešit bude vždycky výchozí zobrazení.
* **Neuložený** sešit je pouze výchozím zobrazením.


### <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>Proč na portálu nevidím všechna moje předplatná?

Portál zobrazí data pouze pro vybraná předplatná při spuštění portálu. Pokud chcete změnit, které odběry se mají vybrat, přejděte vpravo nahoře a klikněte na Poznámkový blok s ikonou filtru. Tím se zobrazí karta adresář a předplatná.

![Adresář a předplatné](./media/storage-insights-overview/fqa3.png)

### <a name="how-to-change-the-coloring-and-threshold-for-availability"></a>Jak změnit barvy a prahovou hodnotu dostupnosti?

Podrobné pokyny, jak změnit barvy a prahové hodnoty dostupnosti, najdete v části [Úprava prahové hodnoty dostupnosti](storage-insights-overview.md#modify-the-availability-threshold) .

### <a name="how-to-analyze-and-troubleshoot-the-data-shown-in-azure-monitor-for-storage"></a>Jak analyzovat a řešit potíže s daty zobrazenými v Azure Monitor pro úložiště?

 Podrobnosti o tom, jak analyzovat a řešit problémy Azure Storage dat zobrazených v Azure Monitor pro úložiště, najdete v článku [monitorování, diagnostika a řešení potíží s Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-monitoring-diagnosing-troubleshooting) .

### <a name="why-dont-i-see-all-the-types-of-errors-in-metrics"></a>Proč se mi nezobrazují všechny typy chyb v metrikách?

V současné době se zobrazují až tři různé typy chyb a zbytek chyb je seskupený do jednoho intervalu. Je ovládána pomocí splitByLimit a lze ji upravit. Pro změnu této vlastnosti:

1. Klikněte na Upravit sešit.
2. Přejděte na metriky, klikněte na Upravit a pak vyberte **transakce, součet** nebo jakékoli metriky, které chcete upravit.

    ![Přejděte na metriky a klikněte na Upravit a potom na transakce, součty.](./media/storage-insights-overview/fqa7.png)

1. Pak změňte počet rozdělení.

    ![Vyberte parametry metriky.](./media/storage-insights-overview/fqa7-2.png)

Pokud chcete zobrazit n různých typů chyb, než zadejte splitByLimit jako n + 1, 1 extra pro zbytek chyb.

###  <a name="i-saved-my-workbook-while-on-some-storage-account-why-cant-i-find-it-now"></a>Sešit byl uložen v průběhu nějakého účtu úložiště. Proč ho teď nejde najít?

Každý sešit je uložený v účtu úložiště, ve kterém jste ho uložili. Zkuste najít konkrétní účet úložiště, do kterého uživatel sešit uložil. Jinak neexistuje žádný způsob, jak najít konkrétní sešit bez znalosti prostředku (účet úložiště).

### <a name="what-is-time-range"></a>Co je časový rozsah?

Časový rozsah zobrazuje data z určitého časového rámce. Pokud je časový rozsah například 24 hodin, pak se zobrazuje data za posledních 24 hodin.

### <a name="what-is-time-granularity-time-grain"></a>Jaká je časová členitost (časová zrnitost)?

Časová členitost je časový rozdíl mezi dvěma datovými body. Například pokud je časový interval nastaven na 1 sekundu, znamená to, že jsou metriky shromažďovány každou sekundu.

### <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>Jaká je časová prodleva, když připnete do řídicího panelu jakékoli části sešitů?

Výchozí časová členitost je nastavená na automatické, aktuálně se v tuto chvíli nedá změnit.

### <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>Návody změnit rozsah TimeSpan/času v kroku sešitu na řídicím panelu?

Ve výchozím nastavení je rozsah TimeSpan/Time na dlaždici řídicího panelu nastavený na 24 hodin. Pokud chcete toto kliknutí změnit na tři tečky vpravo nahoře, vyberte **přizpůsobit data dlaždice**, zaškrtněte políčko Přepsat nastavení času řídicího panelu na úrovni nadpisu a potom vyberte časové rozpětí pomocí rozevírací nabídky.  

![Vyberte tři tečky v pravém horním rohu dlaždice a zvolte možnost přizpůsobit tato data.](./media/storage-insights-overview/fqa-data-settings.png)

![V části konfigurovat nastavení dlaždice vyberte v rozevíracím seznamu časový rozsah TimeSpan/Time.](./media/storage-insights-overview/fqa-timespan.png)

### <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>Návody změnit název sešitu nebo krok sešitu připnuté na řídicí panel?

Název kroku sešitu nebo sešitu, který je připnuté na řídicí panel, si zachová stejný název, který měl v sešitu. Chcete-li změnit název, je nutné uložit vlastní kopii sešitu. Pak budete moci sešit pojmenovat před tím, než kliknete na tlačítko Uložit.

![Vyberte Uložit v horní části, pokud chcete uložit kopii sešitu a změnit jeho název.](./media/storage-insights-overview/fqa-change-workbook-name.png)

Pokud chcete změnit název kroku v uloženém sešitu, vyberte Upravit v kroku a pak vyberte ozubené kolečko v nastavení.

![Výběrem možnosti upravit v dolní části kroku sešitu otevřete nastavení](./media/storage-insights-overview/fqa-edit.png)
![v části nastavení vyberte ozubené kolečko, abyste mohli změnit název kroku.](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>Další kroky

* Nakonfigurujte [Upozornění metrik](../platform/alerts-metric.md) a [oznámení o stavu služby](../../service-health/alerts-activity-log-service-notifications.md) , abyste nastavili automatizované upozorňování na pomoc při zjišťování problémů.

* Seznamte se s scénáři, které jsou navržené tak, aby podporovaly, vytváření nových a přizpůsobení existujících sestav a další informace najdete v tématu [vytváření interaktivních sestav pomocí Azure Monitorch sešitů](../app/usage-workbooks.md).

* Podrobný návod k používání Analýza úložiště a dalších nástrojů k identifikaci, diagnostice a odstraňování potíží souvisejících s Azure Storage najdete v tématu [monitorování, diagnostika a řešení potíží s Microsoft Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md).
