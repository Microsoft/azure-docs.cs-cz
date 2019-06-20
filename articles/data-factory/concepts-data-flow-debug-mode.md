---
title: Azure Data Factory mapování režim ladění toku dat
description: Spusťte relaci interaktivní ladění při vytváření datové toky
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: d86725718217caf7fd1d9dd6d5d67362e5de7270
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147371"
---
# <a name="mapping-data-flow-debug-mode"></a>Mapování režim ladění toku dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory mapování toku dat v režimu ladění lze zapnout pomocí tlačítka "Tok ladění" v horní části návrhové plochy. Při návrhu datové toky, zapnutí režimu ladění vám umožní interaktivně sledovat data tvarovat transformace při sestavení a ladění datové toky. V toku dat seminářů zaměřených na návrh i při spuštění ladění kanálů toků dat je možné relace ladění.

![Ladění tlačítko](media/data-flow/debugbutton.png "tlačítko ladit.")

## <a name="overview"></a>Přehled
Po zapnutí režimu ladění vytvoříte interaktivně váš tok dat s aktivním clusteru Spark. Relace se zavře, jakmile vypnete ladění ve službě Azure Data Factory. Měli byste vědět hodinové poplatky vzniklé během doby, kterou máte zapnuté relaci ladění v Azure Databricks.

Ve většině případů je vhodné vytvořit vaše toky dat v režimu ladění, mohli ověřit vaši obchodní logiku a zobrazit transformaci dat před publikováním svou práci ve službě Azure Data Factory. Testování toku dat v rámci kanálu pomocí tlačítka "Ladění" na panelu kanálu.

> [!NOTE]
> Světlý režim ladění je zelené na panelu nástrojů služby Data Factory, se vám účtovat sazbou toku dat ladění 8 jader/hod obecné výpočetní s 60 minut time-to-live 

> [!NOTE]
>Při spuštění v režimu ladění v toku dat, vaše data nebudou zapsány do jímky transformace. Ladicí relaci má sloužit jako testovací prostředí pro transformaci. Jímky nejsou vyžadovány během ladění a jsou ignorovány ve svém toku data. Pokud chcete otestovat, zápis dat v jímku, spuštění toku dat z kanálu služby Azure Data Factory a používejte spuštění ladění z kanálu.

## <a name="debug-settings"></a>Nastavení ladění
Ladit nastavení můžete upravit kliknutím na "Nastavení ladění" na plátně nástrojů se předávají Data. Omezení a/nebo zdrojový soubor pro použití u každého zdroje transformace Tady můžete vybrat. Omezení řádků v tomto nastavení platí pouze pro aktuální relaci ladění. Můžete také vybrat přípravná propojená služba má být použit pro SQL data Warehouse zdroje. 

![Nastavení ladění](media/data-flow/debug-settings.png "nastavení ladění")

## <a name="cluster-status"></a>Stav clusteru
Je indikátor stavu clusteru v horní části návrhové plochy, která se změní na zelenou při bude cluster připravený pro ladění. Pokud váš cluster již je teplo, se prakticky okamžitě zobrazí zelený ukazatel. Pokud váš cluster nebyla spuštěna při zadání režimu ladění, pak budete muset počkejte 5-7 minut pro cluster, abyste mohli spustit. Dokud je připraven bude aktivovat indikátoru.

Až skončíte s vaší ladění, vypněte ladění přepínač tak, aby váš cluster Azure Databricks můžete ukončit a je již nebude se účtovat aktivity ladění.

## <a name="data-preview"></a>Náhled dat
Ladicí program na kartě Náhled dat bude světla nahoru na dolním panelu. Bez režimu ladění na toku dat zobrazí pouze aktuální metadata do a z transformace na kartě zkontrolujte, jestli se. Náhled dat bude pouze dotaz na počet řádků, které jste nastavili jako svůj limit v nastavení ladění. Budete možná muset kliknout na "Načíst data" aktualizace dat ve verzi preview.

![Náhled dat](media/data-flow/datapreview.png "dat ve verzi preview")

## <a name="data-profiles"></a>Data profilů
Výběrem jednotlivých sloupců v kartě náhledu dat objeví grafu na vaší datové mřížce se podrobné statistikami o jednotlivých polích úplně vpravo. Azure Data Factory způsobí, že zjišťování na základě dat vzorkování, který typ grafu pro zobrazení. Zatímco zařazené do kategorií a číselná data, která má nízkou Kardinalita se zobrazí pruhové grafy znázorňující frekvenci hodnotu data, je výchozí nastavení vysokou kardinalitou pole hodnotu NULL nebo NOT NULL grafy. Uvidíte také maximální počet/délka délka pole řetězců, minimální/maximální hodnoty v číselné pole, standardní dev, percentily, počty a průměr. 

![Statistiky sloupce](media/data-flow/stats.png "statistiky sloupce")

## <a name="next-steps"></a>Další postup

Jakmile budete hotovi, sestavování a ladění váš tok dat [spustit ho v kanálu.](control-flow-execute-data-flow-activity.md)

Při testování svůj kanál s tokem dat, použití kanálu [provádění možnost Spustit ladění.](iterative-development-debugging.md)
