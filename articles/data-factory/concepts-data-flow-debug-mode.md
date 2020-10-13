---
title: Mapování režimu ladění toku dat
description: Spustit interaktivní ladicí relaci při sestavování toků dat
ms.author: makromer
author: kromerm
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/11/2020
ms.openlocfilehash: 41153c488825e87583284b23a287353f63ff8db8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90085089"
---
# <a name="mapping-data-flow-debug-mode"></a>Mapování režimu ladění toku dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Přehled

Azure Data Factory režim ladění toku dat umožňuje interaktivně sledovat transformaci datových tvarů při sestavování a ladění toků dat. Ladicí relaci lze použít jak v relacích návrhu toku dat, tak i při provádění ladění toků při ladění kanálu. Pokud chcete zapnout režim ladění, použijte v horní části návrhové plochy tlačítko "ladění toku dat".

![Posuvník ladění](media/data-flow/debugbutton.png "Posuvník ladění")

Jakmile posuvník zapnete, zobrazí se výzva, abyste vybrali konfiguraci prostředí Integration runtime, kterou chcete použít. Pokud je zvolená možnost AutoResolveIntegrationRuntime, provedou se cluster s osmi jádry obecného COMPUTE s 60 minutou až do živého času. Další informace o modulu runtime integrace toku dat najdete v tématu [výkon toku dat](concepts-data-flow-performance.md#ir).

![Ladění INFRAČERVENého výběru](media/data-flow/debugbutton2.png "Ladění INFRAČERVENého výběru")

Když je režim ladění zapnutý, budete interaktivně vytvářet tok dat s aktivním clusterem Spark. Po vypnutí ladění v Azure Data Factory dojde k ukončení relace. Měli byste si uvědomit, že hodinové poplatky vzniklé Azure Databricks v době, kdy máte zapnutou relaci ladění.

Ve většině případů je vhodné sestavovat toky dat v režimu ladění, abyste před publikováním práce v Azure Data Factory mohli ověřit svou obchodní logiku a zobrazit transformace dat. K otestování toku dat v kanálu použijte tlačítko ladit na panelu kanálů.

![Zobrazit relace ladění toku dat](media/iterative-development-debugging/view-dataflow-debug-sessions.png)

> [!NOTE]
> Každá relace ladění, kterou uživatel spustí z uživatelského rozhraní prohlížeče ADF, je nová relace se svým vlastním clusterem Spark. Pomocí zobrazení monitorování pro relace ladění výše můžete zobrazit a spravovat relace ladění na objekt pro vytváření.

## <a name="cluster-status"></a>Stav clusteru

Indikátor stavu clusteru v horní části návrhové plochy se po přípravě clusteru na ladění změní na zelený. Pokud je váš cluster již zateplný, bude zelený indikátor zobrazen téměř okamžitě. Pokud váš cluster ještě nebyl spuštěný, když jste zadali režim ladění, budete muset počkat 5-7 minut, než se cluster vytočí. Indikátor bude dokončen až do jeho připravenosti.

Až skončíte s laděním, vypněte přepínač ladění, aby se cluster Azure Databricks mohl ukončit a že se už nebudete účtovat za aktivitu ladění.

## <a name="debug-settings"></a>Nastavení ladění

Jakmile zapnete režim ladění, můžete upravit způsob, jakým datový tok data zobrazí. Nastavení ladění můžete upravit kliknutím na nastavení ladění na panelu nástrojů plátna toku dat. Tady můžete vybrat omezení počtu řádků nebo zdroj souborů, které se mají použít pro každou transformaci vašich zdrojů. Omezení řádků v tomto nastavení platí pouze pro aktuální relaci ladění. Můžete také vybrat pracovní propojenou službu, která se má použít pro zdroj Azure synapse Analytics. 

![Nastavení ladění](media/data-flow/debug-settings.png "Nastavení ladění")

Pokud máte v toku dat nebo kterékoli z jeho odkazovaných datových sad parametry, můžete určit, které hodnoty se mají použít během ladění, a to tak, že vyberete kartu **parametry** .

![Parametry nastavení ladění](media/data-flow/debug-settings2.png "Parametry nastavení ladění")

Výchozím nastavením IR použitým pro režim ladění v datových proudech ADF je malý jeden pracovní uzel, který má jeden uzel s jedním ovladačem se čtyřmi jádry. Při testování logiky toku dat to funguje dobře s menšími ukázkami dat. Pokud rozbalíte omezení v nastavení ladění během období Preview nebo během ladění kanálu nastavíte větší počet vzorků řádků ve zdroji, budete možná chtít zvážit nastavení většího výpočetního prostředí v novém Azure Integration Runtime. Pak můžete relaci ladění restartovat pomocí rozsáhlejšího výpočetního prostředí.

## <a name="data-preview"></a>Náhled dat

S laděním na se na spodním panelu na kartě náhled dat zobrazí světlo. Bez režimu ladění zapnutý tok dat zobrazí na kartě zkontrolovat pouze aktuální metadata v a z každé transformace. Náhled dat bude dotazovat jenom na počet řádků, které jste v nastavení ladění nastavili jako limit. Kliknutím na **aktualizovat** načtete náhled dat.

![Náhled dat](media/data-flow/datapreview.png "Náhled dat")

> [!NOTE]
> Zdroje souborů omezují pouze řádky, které vidíte, nikoli řádky, které jsou čteny. U velmi rozsáhlých datových sad se doporučuje, abyste vybrali malou část tohoto souboru a použili ho pro vaše testování. Můžete vybrat dočasný soubor v nastavení ladění pro každý zdroj, který je typem datové sady souboru.

Při spuštění v režimu ladění v toku dat nebudou data zapsána do transformace jímky. Ladicí relace má sloužit jako testovací kabel pro vaše transformace. Během ladění nejsou požadovány jímky a jsou ignorovány v toku dat. Pokud chcete testovat zápisy dat do jímky, spusťte tok dat z kanálu Azure Data Factory a použijte ladění z kanálu.

Data ve verzi Preview jsou snímky transformovaných dat s využitím omezení řádků a vzorkování dat z datových snímků v paměti Spark. Proto nejsou ovladače jímky v tomto scénáři použity ani testovány.

### <a name="testing-join-conditions"></a>Testování podmínek připojení

Pokud se test jednotky připojí, existují nebo transformace vyhledávání, ujistěte se, že používáte pro svůj test malou sadu známých dat. Pomocí možnosti nastavení ladění výše můžete nastavit dočasný soubor, který se má použít pro vaše testování. To je potřeba proto, že při omezování nebo vzorkování řádků z velké datové sady nemůžete předpovědět, které řádky a které klíče se budou do toku číst pro účely testování. Výsledek je Nedeterministický, což znamená, že podmínky připojení můžou selhat.

### <a name="quick-actions"></a>Rychlé akce

Jakmile se zobrazí náhled dat, můžete vygenerovat rychlou transformaci pro přetypovat, odebrání nebo provedení úprav sloupce. Klikněte na záhlaví sloupce a pak na panelu nástrojů Náhled dat vyberte jednu z možností.

![Rychlé akce](media/data-flow/quick-actions1.png "Rychlé akce")

Jakmile vyberete úpravu, náhled dat se okamžitě aktualizuje. Kliknutím na **Potvrdit** v pravém horním rohu Vygenerujte novou transformaci.

![Rychlé akce](media/data-flow/quick-actions2.png "Rychlé akce")

**Přetypovat** a **Modify** vygenerují odvozenou transformaci sloupce a **Odebrání** vytvoří transformaci SELECT.

![Rychlé akce](media/data-flow/quick-actions3.png "Rychlé akce")

> [!NOTE]
> Pokud upravíte tok dat, budete muset před přidáním rychlé transformace znovu načíst náhled dat.

### <a name="data-profiling"></a>Profilace dat

Výběr sloupce na kartě náhledu dat a kliknutí na **Statistika** na panelu nástrojů Náhled dat zobrazí graf na pravém okraji datové mřížky s podrobnými údaji o jednotlivých polích. Azure Data Factory se určí na základě vzorkování dat, který typ grafu se má zobrazit. Pole s vysokou mohutnou hodnotou budou mít jako výchozí hodnotu NULL nebo ne grafy, zatímco kategorií a číselná data s nízkou mohutnost budou zobrazovat pruhové grafy zobrazující frekvenci hodnot dat. Zobrazí se také hodnota Max/len pro pole řetězců, minimální/maximální hodnoty v číselném poli, standardní dev, percentily, počty a průměr.

![Statistiky sloupců](media/data-flow/stats.png "Statistiky sloupců")

## <a name="next-steps"></a>Další kroky

* Až dokončíte sestavování a ladění toku dat, [Spusťte ho z kanálu.](control-flow-execute-data-flow-activity.md)
* Při testování kanálu pomocí toku dat použijte [možnost spuštění ladění kanálu.](iterative-development-debugging.md)
