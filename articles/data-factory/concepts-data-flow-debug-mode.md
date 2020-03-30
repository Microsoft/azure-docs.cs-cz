---
title: Režim ladění toku dat
description: Spuštění interaktivní relace ladění při vytváření toků dat
ms.author: makromer
author: kromerm
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/06/2019
ms.openlocfilehash: 18848695327a374f12fbe5a34d03366b050d8b65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74928368"
---
# <a name="mapping-data-flow-debug-mode"></a>Režim ladění toku dat

## <a name="overview"></a>Přehled

Režim ladění toku dat azure data factory umožňuje interaktivně sledovat transformaci obrazce dat při vytváření a ladění toků dat. Ladicí relace lze použít jak v relacích návrhu toku dat, tak při ladění kanálu provádění toků dat. Chcete-li zapnout režim ladění, použijte tlačítko "Ladění toku dat" v horní části návrhové plochy.

![Jezdec ladění](media/data-flow/debugbutton.png "Jezdec ladění")

Po zapnutí posuvníku budete vyzváni k výběru konfigurace integračního běhu, kterou chcete použít. Pokud je vybrán a automaticky vyřešitIntegrationIntegrationRuntime, cluster s osmi jádry obecné výpočetní s 60 minut čas žít bude stočil nahoru. Další informace o běhu integrace toku dat naleznete v [tématu Výkon toku dat](concepts-data-flow-performance.md#increasing-compute-size-in-azure-integration-runtime).

![Výběr infračerveného přenosu ladění](media/data-flow/debugbutton2.png "Výběr infračerveného přenosu ladění")

Když je zapnutý režim ladění, budete interaktivně vytvářet tok dat pomocí aktivního clusteru Spark. Relace se zavře po vypnutí ladění v Azure Data Factory. Měli byste si být vědomi hodinových poplatků, které vznikly Azure Databricks během doby, kdy máte zapnutou relaci ladění.

Ve většině případů je vhodné vytvořit toky dat v režimu ladění, abyste před publikováním své práce v Azure Data Factory mohli ověřit obchodní logiku a zobrazit transformace dat. Pomocí tlačítka "Ladění" na panelu kanálu otestujte tok dat v kanálu.

## <a name="cluster-status"></a>Stav clusteru

Indikátor stavu clusteru v horní části návrhové plochy se změní na zelenou, když je cluster připraven k ladění. Pokud je váš cluster již teplý, zelený indikátor se objeví téměř okamžitě. Pokud váš cluster nebyl ještě spuštěn, když jste vstoupili do režimu ladění, pak budete muset počkat 5-7 minut pro clusteru se spustit. Indikátor se bude otáčet, dokud není připraven.

Až dokončíte ladění, vypněte vypínač ladění, aby se váš cluster Azure Databricks mohl ukončit a už se vám nebude účtovat ladicí aktivita.

## <a name="debug-settings"></a>Nastavení ladění

Nastavení ladění lze upravit kliknutím na tlačítko "Nastavení ladění" na panelu nástrojů plátna Toku dat. Zde můžete vybrat limit řádku nebo zdroj souboru, který chcete použít pro každou z vašich transformace Zdroje. Omezení řádků v tomto nastavení jsou pouze pro aktuální relaci ladění. Můžete také vybrat pracovní propojené služby, které mají být použity pro zdroj SQL DW. 

![Nastavení ladění](media/data-flow/debug-settings.png "Nastavení ladění")

Pokud máte parametry v toku dat nebo některé z jeho odkazovaných datových sad, můžete určit, jaké hodnoty se mají použít při ladění výběrem karty **Parametry.**

![Ladění parametrů nastavení](media/data-flow/debug-settings2.png "Ladění parametrů nastavení")

## <a name="data-preview"></a>Náhled dat

Při zapnutém ladění se na spodním panelu rozsvítí karta Náhled dat. Bez režimu ladění na tok dat se zobrazí pouze aktuální metadata v a z každé z vašich transformací na kartě Zkontrolovat. Náhled dat bude dotazovat pouze počet řádků, které jste nastavili jako limit v nastavení ladění. Kliknutím na **Aktualizovat** načtete náhled dat.

![Náhled dat](media/data-flow/datapreview.png "Náhled dat")

> [!NOTE]
> Zdroje souborů omezují pouze řádky, které vidíte, nikoli řádky, které se čtou. U velmi velkých datových sad se doporučuje vzít malou část tohoto souboru a použít jej pro testování. Dočasný soubor můžete vybrat v nastavení ladění pro každý zdroj, který je typem datové sady souboru.

Při spuštění v režimu ladění v toku dat, vaše data nebudou zapsány do transformace jímky. Relace ladění je určena sloužit jako testovací postroj pro vaše transformace. Propady nejsou vyžadovány během ladění a jsou ignorovány v toku dat. Pokud chcete otestovat zápis dat v jímce, spusťte tok dat z kanálu Azure Data Factory a použijte spuštění ladění z kanálu.

### <a name="testing-join-conditions"></a>Podmínky spojení testování

Při testování částí spojení, existuje nebo vyhledávání transformace, ujistěte se, že používáte malou sadu známých dat pro test. Výše uvedené možnostnastavení ladění můžete nastavit dočasný soubor pro testování. To je potřeba, protože při omezení nebo vzorkování řádků z velké datové sady nelze předpovědět, které řádky a které klíče budou přečteny do toku pro testování. Výsledkem je nedeterministický, což znamená, že podmínky spojení může selhat.

### <a name="quick-actions"></a>Rychlé akce

Jakmile se zobrazí náhled dat, můžete vygenerovat rychlou transformaci pro přetavění, odebrání nebo provedení úprav y sloupce. Klikněte na záhlaví sloupce a vyberte jednu z možností z panelu nástrojů náhledu dat.

![Rychlé akce](media/data-flow/quick-actions1.png "Rychlé akce")

Jakmile vyberete změnu, náhled dat se okamžitě aktualizuje. Kliknutím na **Potvrdit** v pravém horním rohu vygenerujete novou transformaci.

![Rychlé akce](media/data-flow/quick-actions2.png "Rychlé akce")

**Typecast** a **Modify** vygenerují transformaci odvozeného sloupce a **Odebrat** vygeneruje transformaci Select.

![Rychlé akce](media/data-flow/quick-actions3.png "Rychlé akce")

> [!NOTE]
> Pokud svůj tok dat upravíte, musíte před přidáním rychlé transformace znovu načíst náhled dat.

### <a name="data-profiling"></a>Profilování dat

Když vyberete sloupec na kartě Náhled dat a na panelu nástrojů náhledu dat kliknete na **Statistika,** zobrazí se graf v pravé části datové mřížky s podrobnými statistikami o jednotlivých polích. Azure Data Factory provede určení na základě vzorkování dat, který typ grafu se má zobrazit. Pole s vysokou mohutností budou ve výchozím nastavení nastavena na grafy NULL/NOT NULL, zatímco kategorická a číselná data s nízkou mohutností zobrazí pruhové grafy zobrazující frekvenci hodnot dat. Zobrazí se také maximální délka/délka počtu nk/len ů, min/max hodnoty v číselných polích, standardní vývoj, percentily, počty a průměr.

![Statistika sloupců](media/data-flow/stats.png "Statistika sloupců")

## <a name="next-steps"></a>Další kroky

* Po dokončení vytváření a ladění toku [dat, spusťte jej z kanálu.](control-flow-execute-data-flow-activity.md)
* Při testování kanálu s tokem dat použijte [možnost spuštění ladění kanálu.](iterative-development-debugging.md)
