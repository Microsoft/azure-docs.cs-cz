---
title: Monitorování služeb Azure Storage pomocí Azure Monitor for Storage (preview)| Dokumenty společnosti Microsoft
description: Tento článek popisuje funkci Azure Monitor for Storage, která správcům úložiště poskytuje rychlé pochopení problémů s výkonem a využitím jejich účtů Azure Storage.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/15/2019
ms.openlocfilehash: f23be7e764ad180a23c76abb7f9bb2218fd61e4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662515"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage-preview"></a>Sledování služby úložiště pomocí Azure Monitor for Storage (preview)

Azure Monitor for Storage (preview) poskytuje komplexní monitorování vašich účtů Azure Storage tím, že poskytuje jednotné zobrazení výkonu, kapacity a dostupnosti služeb Azure Storage. Kapacitu úložiště a výkon můžete sledovat dvěma způsoby, zobrazit přímo z účtu úložiště nebo zobrazení z Azure Monitoru a zobrazit napříč skupinami účtů úložiště. 

Tento článek vám pomůže pochopit prostředí, které Azure Monitor for Storage (preview) poskytuje k odvození použitelných znalostí o stavu a výkonu účtů úložiště ve velkém měřítku, se schopností zaměřit se na hotspoty a diagnostikovat latenci, omezení, a problémy s dostupností.

## <a name="introduction-to-azure-monitor-for-storage-preview"></a>Úvod do Azure Monitor u úložiště (preview)

Než se ponoříte do zážitku, měli byste pochopit, jak prezentuje a vizualizuje informace. Ať už vyberete funkci úložiště přímo z účtu úložiště nebo z Azure Monitoru, Azure Monitor for Storage představuje konzistentní prostředí. 

V kombinaci dodává:

* **V perspektivě škálování** zobrazující snímek zobrazení jejich dostupnosti na základě stavu služby úložiště nebo operace rozhraní API, využití zobrazující celkový počet požadavků, které služba úložiště obdrží a latence zobrazující průměrnou dobu služby úložiště nebo typ operace rozhraní API přebírá ke zpracování požadavků. Kapacitu můžete také zobrazit podle objektu blob, souboru, tabulky a fronty.

* **Přejděte k podrobnostem analýzu** konkrétního účtu úložiště, která vám pomůže diagnostikovat problémy nebo provést podrobnou analýzu podle kategorie – dostupnost, výkon, selhání a kapacitu. Výběr některé z těchto možností poskytuje podrobný pohled na metriky.  

* **Přizpůsobitelné** místo, kde můžete změnit metriky, které chcete zobrazit, upravit nebo nastavit prahové hodnoty, které odpovídají vašim limitům, a uložit je jako vlastní sešit. Grafy v sešitu se můžou připnout na řídicí panel Azure.  

Tato funkce nevyžaduje, abyste nic povolit nebo konfigurovat, metriky úložiště z účtů úložiště jsou shromažďovány ve výchozím nastavení. Pokud neznáte metriky dostupné ve Službě Azure, podívejte se na popis a definici metriky azure storage kontrolou [metriky úložiště Azure](../../storage/common/storage-metrics-in-azure-monitor.md).

>[!NOTE]
>Přístup k této funkci se neúčtuje a bude se vám účtovat jenom základní funkce Azure Monitoru, které nakonfigurujete nebo povolíte, jak je popsáno na stránce [s podrobnostmi o cenách Azure Monitoru.](https://azure.microsoft.com/pricing/details/monitor/)

>[!NOTE]
>Azure Monitor for Storage nepodporuje [účty pro obecné účely v1](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts).
>

## <a name="view-from-azure-monitor"></a>Zobrazení z Azure Monitoru

Z Azure Monitoru můžete zobrazit podrobnosti o transakcích, latenci a kapacitě z více účtů úložiště ve vašem předplatném a pomoci identifikovat výkon, problémy s kapacitou a selhání.

Chcete-li zobrazit využití a dostupnost účtů úložiště ve všech předplatných, proveďte následující kroky.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

2. V **Monitor** levém podokně na webu Azure Portal a v části **Přehledy** vyberte **Účty úložiště (preview).**

    ![Zobrazení více účtů úložiště](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>Přehledsešitu

V sešitu **Přehled** pro vybrané předplatné tabulka zobrazuje metriky interaktivního úložiště a stav dostupnosti služeb až pro 10 účtů úložiště seskupených v rámci předplatného. Výsledky můžete filtrovat na základě vybraných možností z následujících rozevíracích seznamů:

* **Předplatná** – jsou uvedeny pouze předplatná, která mají účty úložiště.  

* **Účty úložiště** – ve výchozím nastavení je předem vybráno 10 účtů úložiště. Pokud vyberete všechny nebo více účtů úložiště ve voliči oboru, bude vráceno až 200 účtů úložiště. Pokud jste například měli celkem 573 účtů úložiště ve třech vybraných předplatných, zobrazí se pouze 200 účtů. 

* **Časový rozsah** - ve výchozím nastavení zobrazuje poslední 4 hodiny informací na základě odpovídajících provedených výběrů.

Dlaždice čítače pod rozevírací seznamy rozbalí celkový počet účtů úložiště v předplatném a odráží, kolik z celkového počtu je vybráno. V sešitu, který hlásí metriky transakcí nebo chyby, existuje podmíněné barevné kódování nebo heatmapy pro sloupce. Nejhlubší barva má nejvyšší hodnotu a světlejší barva je založena na nejnižších hodnotách. U sloupců založených na chybách je hodnota červeně a pro sloupce založené na metrikách je hodnota modře.

Vyberte hodnotu ve sloupcích **Dostupnost**, **Latence E2E**, **Latence serveru**a typ **chyby transakce/Chyby** vás přesměrují na sestavu přizpůsobenou konkrétnímu typu metrik úložiště, které odpovídají sloupci vybranému pro daný účet úložiště. Další informace o sešitech pro jednotlivé kategorie najdete v části [Podrobné sešity úložiště](#detailed-storage-workbooks) níže. 

>[!NOTE]
>Podrobnosti o tom, které chyby lze zobrazit v sestavě, naleznete v [tématu](../../storage/common/storage-metrics-in-azure-monitor.md#metrics-dimensions) schéma typu odpovědi a vyhledejte typy odpovědí, jako je **Například ServerOtherError**, **ClientOtherError**, **ClientThrottlingError**. V závislosti na vybraných účtech úložiště, pokud jsou hlášeny více než tři typy chyb, všechny ostatní chyby jsou reprezentovány v kategorii **Other**.

Výchozí prahová hodnota **dostupnosti** je:

* Upozornění - 99%
* Kritické - 90%

Chcete-li nastavit prahovou hodnotu dostupnosti na základě výsledků pozorování nebo požadavků, [zkontrolujte prahovou hodnotu dostupnosti](#modify-the-availability-threshold). 

### <a name="capacity-workbook"></a>Sešit kapacity

V horní části stránky vyberte **Kapacita** a otevře se se šití **Kapacity.** Zobrazuje množství celkového úložiště použitého v účtu a kapacitu používanou každou datovou službou v účtu k identifikaci nad a pod využitým úložištěm.

![Více účtů úložiště Sešit kapacity](./media/storage-insights-overview/storage-account-capacity-02.png) 

V sešitu je podmíněné barevné kódování nebo heatmapy pro sloupce, které vykazují metriky kapacity s modrou hodnotou. Nejhlubší barva má nejvyšší hodnotu a světlejší barva je založena na nejnižších hodnotách.

Když vyberete hodnotu pod libovolným sloupcem v sešitu, přejdete k sešitu **Kapacita** pro účet úložiště. Další podrobnosti o sestavě podrobností jsou popsány v části [Podrobné sešity úložiště](#detailed-storage-workbooks) níže. 

## <a name="view-from-a-storage-account"></a>Zobrazení z účtu úložiště

Přístup k Azure Monitoru pro virtuální počítače přímo z účtu úložiště:

1. Na webu Azure Portal vyberte Účty úložiště.

2. V seznamu zvolte účet úložiště. V části Monitorování zvolte Přehledy (náhled).

    ![Stránka Přehled vybraného účtu úložiště](./media/storage-insights-overview/storage-account-direct-overview-01.png)

V sešitu **Přehled** pro účet úložiště se zobrazuje několik metrik výkonu úložiště, které vám pomohou rychle posoudit:

* Stav služby Úložiště okamžitě zjistit, pokud problém mimo váš ovládací prvek ovlivňuje službu úložiště v oblasti, do které je nasazena, což je uvedeno ve sloupci **Souhrn.**

* Interaktivní grafy výkonu zobrazující nejdůležitější podrobnosti týkající se kapacity úložiště, dostupnosti, transakcí a latence.  

* Metriky a stav dlaždice zvýraznění dostupnostslužby, celkový počet transakcí do služby úložiště, E2E latence a latence serveru.

Výběrem některého z tlačítek pro **selhání**, **výkon**, **dostupnost**a **kapacita** otevře příslušný sešit. 

![Stránka Přehled vybraného účtu úložiště](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>Podrobné sešity úložiště

Ať už jste vybrali hodnotu ve sloupcích **Dostupnost**, **Latence E2E**, **Latence serveru**a **typ chyby transakce/Chyby z** přehledového přehledu více účtů úložiště, nebo vyberete jedno z tlačítek pro **selhání**, **Výkon**, **Dostupnost**a **Kapacita** ze sešitu **Přehled** z konkrétního účtu úložiště, každý z nich zobrazí sadu interaktivních informací souvisejících s úložištěm přizpůsobenou této kategorii. **Overview**  

* **Dostupnost** otevře sešit **dostupnost.** Zobrazuje aktuální stav služby Azure Storage, tabulku s dostupným stavem každého objektu zařazeného do kategorií podle datové služby definované v účtu úložiště s trendovou čárou představující vybraný časový rozsah a graf trendů dostupnosti pro každou datovou službu v účtu.  

    ![Příklad sestavy dostupnosti](./media/storage-insights-overview/storage-account-availability-01.png)

* **Latence E2E** a **latence serveru** otevře sešit **výkonu.** Obsahuje dlaždici stavu souhrnu zobrazující latenci E2E a latenci serveru, graf výkonu latence E2E versus server a tabulku, která rozluštila latenci úspěšných volání podle rozhraní API kategorizovaných datovou službou definovanou v účtu úložiště.

    ![Příklad zprávy o výkonnosti](./media/storage-insights-overview/storage-account-performance-01.png)

* Při výběru libovolné kategorie chyb uvedených v mřížce otevřete sešit **Selhání.** Sestava zobrazuje metriky dlaždic všech ostatních chyb na straně klienta s výjimkou popsaných a úspěšných požadavků, chyb omezení klienta, grafu výkonu pro metriku **dimenze typ odpovědi** transakce specifické pro atribut ClientOtherError a dvou tabulek – Transakce podle názvu rozhraní **API** a transakce podle **typu odpovědi**.

   ![Příklad zprávy o selhání](./media/storage-insights-overview/storage-account-failures-01.png)

* **Kapacita** otevře **sešit Kapacita.** Zobrazuje celkové množství úložiště použitého pro každý datový objekt úložiště v účtu v dlaždicích a grafu a kolik datových objektů je v účtu uloženo.  

    ![Stránka Kapacita vybraného účtu úložiště](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>Připnutí a export

Na ovládací panel Azure můžete připnout kteroukoli z částí metriky tak, že vyberete ikonu připínáčku v pravém horním rohu oddílu.

![Příklad připnutí metrické sekce k řídicímu panelu](./media/storage-insights-overview/workbook-pin-example.png)

**Přehled** účtu s více odběry a úložiště nebo **sešity kapacity** podporují export výsledků ve formátu Excel výběrem ikony šipky dolů vpravo od ikony připínáčku.

![Příklad exportu výsledků mřížky sešitu](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage-preview"></a>Přizpůsobení Azure Monitoru pro úložiště (preview)

Tato část upozorňuje na běžné scénáře úprav sešitu, které se přizpůsobí potřebám analýzy dat:

* Rozsah sešitu vždy vybrat konkrétní předplatné nebo úložiště účty
* Změna metrik v mřížce
* Změna prahové hodnoty dostupnosti
* Změna vykreslení barev

Vlastní nastavení se uloží do vlastního sešitu, aby se zabránilo přepsání výchozí konfigurace v publikovaném sešitu. Sešity se ukládají do skupiny prostředků, a to buď v části **Moje sestavy,** která je pro vás soukromá, nebo v části **Sdílené sestavy,** která je přístupná všem uživatelům s přístupem ke skupině prostředků. Po uložení vlastního sešitu je třeba přejít do galerie sešitů a spustit jej.

![Spuštění galerie sešitů z panelu příkazů](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>Určení účtu předplatného nebo úložiště

Můžete nakonfigurovat **víceodběra** úložiště účet přehled nebo **kapacita** sešity na obor pro konkrétní předplatné nebo účty úložiště při každém spuštění, provést následující kroky.

1. Z portálu vyberte **Monitor** a v levém podokně vyberte **Účty úložiště (náhled).**

2. V sešitu **Přehled** vyberte na panelu příkazů vyberte **Upravit**.

3. V rozevíracím seznamu **Předplatná** vyberte jedno nebo více předplatných, na které chcete, aby bylo výchozí. Nezapomeňte, že sešit podporuje výběr až 10 předplatných.  

4. V rozevíracím seznamu **Účty úložiště** vyberte jeden nebo více účtů, na které chcete, aby byly výchozí. Nezapomeňte, že sešit podporuje výběr až 200 účtů úložiště. 

5. Chcete-li uložit kopii sešitu s vlastním nastavením, vyberte **Uložit jako** z panelu příkazů a kliknutím na **Hotovo úpravy** se vrátíte do režimu čtení.  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>Úprava metrik a barev v sešitu

Předem sestavené sešity obsahují metrická data a máte možnost upravit nebo odebrat některou z vizualizací a přizpůsobit se specifickým potřebám vašeho týmu.

V našem příkladu pracujeme se sešitem kapacity účtů s více předplatnými a úložištěm, abychom ukázali, jak:

* Odebrání metriky
* Změna vykreslení barev

Stejné změny můžete provést u všech předem **sestavených sešitů selhání**, **výkonu**, **dostupnosti**a **kapacity.**

1. Z portálu vyberte **Monitor** a v levém podokně vyberte **Účty úložiště (náhled).**

2. Vyberte **Kapacita** pro přepnutí do sešitu kapacity a na panelu příkazů vyberte **Upravit** z panelu příkazů.

    ![Výběrem úprav, chcete-li upravit sešit.](./media/storage-insights-overview/workbook-edit-workbook.png)

3. Vedle oddílu metrik vyberte **Upravit**.

    ![Vyberte Upravit, chcete-li upravit metriky sešitu kapacity.](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. Odstraníme sloupec **časové osy využité kapacity účtu,** takže v yberte **Nastavení sloupců** v tabulce metrik.

    ![Upravit nastavení sloupců](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. V podokně **Upravit nastavení sloupce** vyberte v části **Sloupce** **microsoft.storage/storageAccounts-Capacity-UsedCapacity Timeline$| Účet použil kapacitu Timeline$** a v rozevíracím seznamu **Column renderer** vyberte **Skrytý**.

6. Chcete-li potvrdit změnu, vyberte **uložit a zavřít.**

Nyní změníme barevný motiv metriky kapacity v sestavě tak, aby se používala zelená místo modrá.

1. V mřížce metrik vyberte **Nastavení sloupců.**

2. V podokně **Upravit nastavení sloupců** vyberte v části **Sloupce** **microsoft.storage/storageaccounts-Capacity-UsedCapacity$|microsoft.storage/storageaccounts/blobservices-BlobCapacity$|microsoft.storage/storageaccounts/fileservices-Capacity-FileCapacity$|microsoft.storage/storageaccounts/queueservices-Capacity-QueueCapacity$|microsoft.storage/storageaccounts/tableservices-Capacity-TableCapacity$.** V rozevíracím seznamu **Paleta barev**vyberte **Zelená**.

3. Chcete-li potvrdit změnu, vyberte **uložit a zavřít.**

4. Chcete-li uložit kopii sešitu s vlastním nastavením, vyberte **Uložit jako** z panelu příkazů a kliknutím na **Hotovo úpravy** se vrátíte do režimu čtení.  

### <a name="modify-the-availability-threshold"></a>Změna prahové hodnoty dostupnosti

V tomto příkladu pracujeme se sešitem kapacity účtu úložiště a ukazujeme, jak upravit prahovou hodnotu dostupnosti. Ve výchozím nastavení jsou procentuální dostupnost dlaždic a gridových sestav konfigurovány s minimální prahovou hodnotou 90 a maximální prahovou hodnotou 99. Změníme minimální prahovou hodnotu **dostupnosti %** v **mřížce názvů rozhraní API** na 85 %, což znamená, že stav se změní na kritický, pokud je prahová hodnota menší než 85 procent. 

1. Z portálu vyberte **Účty úložiště** a pak ze seznamu vyberte účet úložiště.

2. V levém podokně vyberte **Přehledy (náhled).**

3. V sešitu vyberte **Dostupnost,** chcete-li přepnout do sešitu dostupnosti, a pak na panelu příkazů vyberte **Upravit.** 

4. Posuňte se dolů do dolní části stránky a na levé straně vedle **mřížky Dostupnost podle rozhraní API** vyberte **Upravit**.

    ![Upravit dostupnost podle nastavení mřížky názvu rozhraní API](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. Vyberte **Nastavení sloupce** a pak v podokně **Upravit nastavení sloupců** v části **Sloupce** vyberte **Dostupnost (%) (Prahové hodnoty + formátováno).**

6. Změňte hodnotu **kritického** stavu z **90** na **85** a klepněte na tlačítko **Uložit a zavřít**.

    ![Změna prahové hodnoty dostupnosti pro kritický stav](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. Chcete-li uložit kopii sešitu s vlastním nastavením, vyberte **Uložit jako** z panelu příkazů a kliknutím na **Hotovo úpravy** se vrátíte do režimu čtení.

## <a name="troubleshooting"></a>Řešení potíží

Tato část vám pomůže s diagnostikou a řešením některých běžných problémů, se kterými se můžete setkat při používání Azure Monitor for Storage (preview). Pomocí následujícího seznamu vyhledejte informace týkající se konkrétního problému.

### <a name="resolving-performance-capacity-or-availability-issues"></a>Řešení problémů s výkonem, kapacitou nebo dostupností

Informace o řešení problémů souvisejících s úložištěm, které identifikujete s Azure Monitor for Storage (preview), najdete v [tématu pokyny pro řešení potíží s úložištěm](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)Azure .  

### <a name="why-can-i-only-see-200-storage-accounts"></a>Proč se mi zobrazí jenom 200 účtů úložiště?

Počet vybraných účtů úložiště má limit 200, bez ohledu na počet odběrů, které jsou vybrány.

### <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>Co se stane, když kliknu na nedávno připnutou dlaždici na řídicím panelu?

* Pokud kliknete na libovolné místo na dlaždici, dostanete se na kartu, ze které byla dlaždice připnutá. Pokud například připnete graf na kartu "Přehled účtu úložiště", pak po kliknutí na tuto dlaždici na řídicím panelu se otevře toto výchozí zobrazení, ale pokud připnete graf z vlastní uložené kopie, otevře zobrazení uložené kopie.
* Ikona filtru v levém horním rohu názvu otevře kartu Konfigurovat nastavení dlaždic.
* Ikona elipsy v pravém horním rohu vám poskytne možnosti "Přizpůsobit data o titulu", "přizpůsobit", "aktualizovat" a "odstranit z palubní desky".

### <a name="what-happens-when-i-save-a-workbook"></a>Co se stane, když uložím sešit?

* Když uložíte sešit, umožní vám vytvořit novou kopii sešitu s úpravami a změnit název. Uložení nepřepíše sešit, aktuální sešit bude vždy výchozím zobrazením.
* **Neuložený** sešit je pouze výchozí zobrazení.


### <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>Proč se na portálu nezobrazují všechna svá předplatná?

Portál zobrazí data pouze pro vybraná předplatná při spuštění portálu. Chcete-li změnit, jaká předplatná jsou vybrána, přejděte doprava nahoře a klikněte na poznámkový blok s ikonou filtru. Zobrazí se karta Directory + subscriptions .

![Adresář a předplatné](./media/storage-insights-overview/fqa3.png)

### <a name="how-to-change-the-coloring-and-threshold-for-availability"></a>Jak změnit zbarvení a práh pro dostupnost?

Podrobné kroky, jak změnit vybarvení a prahové hodnoty pro dostupnost, naleznete v části [Upravit prahovou hodnotu dostupnosti.](storage-insights-overview.md#modify-the-availability-threshold)

### <a name="how-to-analyze-and-troubleshoot-the-data-shown-in-azure-monitor-for-storage"></a>Jak analyzovat a řešit problémy s daty zobrazená mi v Azure Monitor u úložiště?

 Podrobnosti o tom, jak analyzovat a řešit problémy s daty Azure Storage zobrazená v Azure Monitoru pro úložiště, najdete v článku [Sledování, diagnostika a řešení potíží](https://docs.microsoft.com/azure/storage/common/storage-monitoring-diagnosing-troubleshooting) s Microsoft Azure Storage.

### <a name="why-dont-i-see-all-the-types-of-errors-in-metrics"></a>Proč nevidím všechny typy chyb v metrikách?

V současné době jsou zobrazeny až tři různé typy chyb a zbytek chyb jsou seskupeny do jednoho bloku. Je řízen pomocí splitByLimit a lze jej upravit. Pro změnu této vlastnosti:

1. Klikněte na upravit sešit.
2. Přejděte na metriky, klikněte na upravit a pak vyberte **Transakce, Součet** nebo jakékoliv metriky, které chcete upravit.

    ![Přejít na metriky a klikněte na upravit pak na "Transakce, součty"](./media/storage-insights-overview/fqa7.png)

1. Potom změňte počet rozdělení.

    ![Vybrat parametry metriky"](./media/storage-insights-overview/fqa7-2.png)

Pokud chcete zobrazit n různé typy chyb, než zadejte splitByLimit jako n +1, 1 navíc pro zbytek chyb.

###  <a name="i-saved-my-workbook-while-on-some-storage-account-why-cant-i-find-it-now"></a>Sešit jsem uložil v některém účtu úložiště. Proč ho nemůžu najít teď?

Každý sešit se uloží do účtu úložiště, do kterého jste ho uložili. Pokuste se najít konkrétní účet úložiště, ve kterém uživatel uložil sešit. V opačném případě neexistuje žádný způsob, jak najít konkrétní sešit bez znalosti prostředku (účet úložiště).

### <a name="what-is-time-range"></a>Co je časový rozsah?

Časový rozsah zobrazuje data z určitého časového rámce. Pokud je například časový rozsah 24 hodin, zobrazuje data za posledních 24 hodin.

### <a name="what-is-time-granularity-time-grain"></a>Co je čas rozlišovací schopnost (časové zrno)?

Rozlišovací schopnost času je časový rozdíl mezi dvěma datovými body. Například pokud je čas zrnitosti nastavena na 1 sekundu, což znamená, že metriky jsou shromažďovány každou sekundu.

### <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>Jaká je časová rozlišovací schopnost, jakmile připneme libovolnou část sešitů na řídicí panel?

Výchozí rozlišovací schopnost je nastavena na automatickou, v současné době ji nelze změnit.

### <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>Jak změním časový nebo časový rozsah kroku sešitu na řídicím panelu?

Ve výchozím nastavení je časový rozsah na dlaždici řídicího panelu nastaven na 24 hodin, chcete-li změnit toto kliknutí na tři tečky v pravém horním rohu, vyberte **možnost Přizpůsobit data dlaždice**, zaškrtněte políčko "přepsat nastavení času na úrovni nadpisu" a pak vybrat časový klíč pomocí rozbalovací nabídky.  

![Vyberte tři tečky v pravém rohu dlaždice a zvolte Přizpůsobit tato data](./media/storage-insights-overview/fqa-data-settings.png)

![V části Konfigurace nastavení dlaždic vyberte rozevírací nabídku časový montovna, chcete-li změnit časový rozsah/časový rozsah](./media/storage-insights-overview/fqa-timespan.png)

### <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>Jak změním název sešitu nebo krok sešitu připnutý na řídicí panel?

Název sešitu nebo kroku sešitu, který je připnutý na řídicí panel, si zachová stejný název, který měl v sešitu. Chcete-li změnit název, je nutné uložit vlastní kopii sešitu. Pak budete moci pojmenovat sešit před stisknutím tlačítka uložit.

![Výběrem možnosti Uložit v horní části uložíte kopii sešitu a změníte jeho název.](./media/storage-insights-overview/fqa-change-workbook-name.png)

Chcete-li změnit název kroku v uloženém sešitu, vyberte pod krokem upravit a vyberte ozubené kolo v dolní části nastavení.

![Výběrem možnosti Upravit v dolní části kroku](./media/storage-insights-overview/fqa-edit.png)
![sešitu otevřete nastavení V nastavení vyberte ozubené kolo v dolní části, abyste mohli změnit název kroku.](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>Další kroky

* Nakonfigurujte [upozornění na metriky](../platform/alerts-metric.md) a [oznámení o stavu služeb](../../service-health/alerts-activity-log-service-notifications.md) a nastavte automatické upozorňování, které pomůže při zjišťování problémů.

* Seznamte se scénáři, které jsou navržené tak, aby podporovaly, jak vytvářet nové a přizpůsobit existující sestavy a další informace v recenzi [Na vytvoření interaktivních sestav pomocí sešitů Azure Monitoru](../app/usage-workbooks.md).

* Podrobný průvodce používáním služby Storage Analytics a dalších nástrojů k identifikaci, diagnostice a řešení problémů souvisejících s úložištěm Azure najdete v tématu [Sledování, diagnostika a řešení potíží s úložištěm Microsoft Azure](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md).
