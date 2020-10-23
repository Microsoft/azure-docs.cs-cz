---
title: Odstranění duplicit řádků a vyhledání hodnot null pomocí fragmentů toku dat
description: Přečtěte si, jak snadno odsazovat řádky a najít hodnoty null pomocí fragmentů kódu v datových tocích.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: makromer
ms.openlocfilehash: 1c630cdd66fa4f8e609524feb9c3f0bcad9711a0
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92458162"
---
# <a name="dedupe-rows-and-find-nulls-by-using-data-flow-snippets"></a>Odstranění duplicit řádků a vyhledání hodnot null pomocí fragmentů toku dat

[!INCLUDE [appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pomocí fragmentů kódu při mapování toků dat můžete snadno provádět běžné úlohy, jako jsou odstranění duplicitních dat a filtrování hodnot null. Tento článek vysvětluje, jak snadno přidat tyto funkce do kanálů pomocí fragmentů skriptů toku dat.
<br>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>Vytvoření kanálu

1. Vyberte **Nový kanál**.

1. Přidejte aktivitu toku dat.

1. Vyberte kartu **Nastavení zdroje** , přidejte transformaci zdroje a pak ji připojte k jedné z vašich datových sad.

    ![Snímek obrazovky s podoknem nastavení zdroje pro přidání zdrojového typu](media/data-flow/snippet-adf-2.png)

    Deduplicity a fragmenty kontroly hodnoty null používají obecné vzory, které využívají posun schématu toku dat. Fragmenty kódu fungují s jakýmkoli schématem z vaší datové sady nebo s datovými sadami, které nemají žádné předdefinované schéma.

1. V oddílu "DISTINCT řádky všech sloupců" [skriptu toku dat (DFS)](https://docs.microsoft.com/azure/data-factory/data-flow-script#distinct-row-using-all-columns)zkopírujte fragment kódu pro DistinctRows.

1. V uživatelském rozhraní návrháře toku dat vyberte tlačítko **skriptu** v pravém horním rohu a otevřete editor skriptů za grafem toku dat.

    ![Snímek obrazovky zdrojového fragmentu](media/data-flow/snippet-adf-3.png)

1. Ve skriptu po definici pro `source1` , stiskněte ENTER a vložte fragment kódu.

1. Proveďte jednu z následujících akcí:

   * Připojte tento vložený fragment kódu ke zdrojové transformaci, kterou jste vytvořili dříve v grafu, zadáním **source1** před vloženým kódem.

   * Alternativně můžete novou transformaci propojit v Návrháři tak, že vyberete příchozí datový proud z nového uzlu transformace v grafu.

     ![Snímek obrazovky s podoknem nastavení podmíněného rozdělení](media/data-flow/snippet-adf-4.png)

   Nyní datový tok odstraní ze zdroje duplicitní řádky pomocí agregované transformace, které seskupí všechny řádky pomocí obecné hodnoty hash napříč všemi hodnotami sloupce.
    
1. Přidejte fragment kódu pro rozdělení dat do jednoho datového proudu, který obsahuje řádky s hodnotami null a další datový proud bez hodnot null. Postupujte následovně:

   a. Vraťte se do [knihovny fragmentů kódu](https://docs.microsoft.com/azure/data-factory/data-flow-script#check-for-nulls-in-all-columns)a pak zkopírujte kód pro kontroly null.

   b. V Návrháři toku dat znovu vyberte **skript** a potom tento nový kód transformace vložte do dolní části. Tato akce připojí skript k předchozí transformaci tím, že místo vloženého fragmentu umístí název této transformace.

   Graf toku dat by teď měl vypadat nějak takto:

    ![Snímek obrazovky grafu toku dat](media/data-flow/snippet-adf-1.png)

  Nyní jste vytvořili pracovní tok dat s obecnými deduping a kontrolami hodnoty null tím, že z knihovny skriptu toku dat převezmete existující fragmenty kódu a přidáte je do existujícího návrhu.

## <a name="next-steps"></a>Další kroky

* Sestavte zbývající část logiky toku dat pomocí [transformací](concepts-data-flow-overview.md)mapování toků dat.
