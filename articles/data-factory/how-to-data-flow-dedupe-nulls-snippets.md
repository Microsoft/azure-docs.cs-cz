---
title: Odstranění duplicit řádků a vyhledání hodnot null pomocí fragmentů toku dat
description: Naučte se snadno odsazovat řádky a vyhledávat hodnoty null pomocí fragmentů kódu v datových tocích.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: makromer
ms.openlocfilehash: 841484a647d2737d621c75ebe63f65f2de829a26
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91666496"
---
# <a name="dedupe-rows-and-find-nulls-using-data-flow-snippets"></a>Odstranění duplicit řádků a vyhledání hodnot null pomocí fragmentů toku dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pomocí fragmentů kódu v mapování toků dat můžete velmi snadno provádět běžné úkoly, jako je odstranění duplicitních dat a filtrování hodnot null. Tato příručka vysvětluje, jak tyto funkce přidat do kanálů velmi snadno pomocí fragmentů skriptů toku dat.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>Vytvoření kanálu

1. Vyberte **+ Nový kanál** a vytvořte nový kanál.

2. Přidejte aktivitu toku dat.

3. Přidejte zdrojovou transformaci a připojte ji k jedné z vašich datových sad.

    ![Zdrojový fragment 2](media/data-flow/snippet-adf-2.png)

4. Fragmenty souborů deduplicity a kontroly hodnoty NULL používají obecné vzory, které využívají posun schématu toku dat, takže budou fungovat s jakýmkoli schématem z vaší datové sady nebo s datovými sadami, které nemají žádné předdefinované schéma.

5. [Přejít na stránku dokumentace ke skriptu toku dat a zkopírovat fragment kódu pro jedinečné řádky.](https://docs.microsoft.com/azure/data-factory/data-flow-script#distinct-row-using-all-columns)

6. V uživatelském rozhraní návrháře toku dat klikněte na tlačítko skriptu v pravém horním rohu a otevřete editor skriptů za grafem toku dat.

    ![Zdrojový fragment kódu 3](media/data-flow/snippet-adf-3.png)

7. Po definování ```source1``` ve skriptu stiskněte ENTER a vložte ho do fragmentu kódu.

8. Tento vložený fragment kódu připojíte k předchozí transformaci zdroje, kterou jste vytvořili v grafu, zadáním "source1" před vloženého kódu.

9. Alternativně můžete novou transformaci propojit v Návrháři tak, že vyberete příchozí datový proud z nového uzlu transformace v grafu.

    ![Zdrojový fragment kódu 4](media/data-flow/snippet-adf-4.png)

10. Nyní váš tok dat odebere ze zdroje duplicitní řádky pomocí agregované transformace, která seskupí všechny řádky pomocí obecné hodnoty hash napříč všemi hodnotami sloupce.
    
11. Dále přidáte fragment kódu pro rozdělení dat do datového proudu, který obsahuje řádky s hodnotami NULL a datový proud, který neobsahuje žádné hodnoty NULL.

12. [Vraťte se do knihovny fragmentů kódu a tentokrát zkopírujte kód pro kontroly hodnoty NULL.](https://docs.microsoft.com/azure/data-factory/data-flow-script#check-for-nulls-in-all-columns)

13. V Návrháři toku dat znovu klikněte na skript a vložte tento nový kód transformace do dolní části a připojte ho k předchozí transformaci zadáním názvu této transformace před vloženým fragmentem.

14. Graf toku dat by teď měl vypadat nějak takto:

    ![Fragment zdroje 1](media/data-flow/snippet-adf-1.png)

  Nyní máte funkční tok dat s obecnými deduping a kontrolami hodnotou NULL tím, že z knihovny skriptu toku dat převezmete existující fragmenty kódu a přidáte je do existujícího návrhu.

## <a name="next-steps"></a>Další kroky

* Sestavte zbývající část logiky toku dat pomocí [transformací](concepts-data-flow-overview.md)mapování toků dat.
