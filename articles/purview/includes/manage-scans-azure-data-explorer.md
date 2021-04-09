---
author: amberz
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 1/20/2021
ms.author: amberz
ms.openlocfilehash: bf872feae9c3a7ca94e5252872adee2b653f5524
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896099"
---
## <a name="creating-and-running-a-scan"></a>Vytvoření a spuštění kontroly

> [!Note] 
> Níže uvedené kroky a snímky obrazovky ilustrují obecný proces správy kontrol napříč různými typy zdrojů dat. Vaše možnosti se mohou mírně lišit v závislosti na typech zdrojů dat, se kterými pracujete.

Pokud chcete vytvořit a spustit novou kontrolu, udělejte toto:

1. Přejděte ke **zdrojům** .

1. Vyberte zdroj dat, který jste zaregistrovali.

1. Vybrat **+ Nová kontrola**

1. Vyberte přihlašovací údaje pro připojení ke zdroji dat. 

   :::image type="content" source="media/manage-scans/set-up-scan-data-explorer.png" alt-text="Nastavit kontrolu":::

1. Můžete určit rozsah kontroly na konkrétní části zdroje dat, jako jsou složky, kolekce nebo schémata, a to tak, že zkontrolujete příslušné položky v seznamu.

   :::image type="content" source="media/manage-scans/scope-your-scan-data-explorer.png" alt-text="Určení rozsahu kontroly":::

1. Vybraná sada pravidel skenování pro kontrolu. Můžete si vybrat mezi systémovým výchozím nastavením, stávající vlastní nebo vytvořit nové.

   :::image type="content" source="media/manage-scans/scan-rule-set-data-explorer.png" alt-text="Sada pravidel skenování":::

1. Vyberte aktivační událost kontroly. Můžete nastavit plán nebo spustit kontrolu jednou.

   :::image type="content" source="media/manage-scans/trigger-scan-data-explorer.png" alt-text="signálu":::

1. Zkontrolujte kontrolu a vyberte **Uložit a spustit**.

## <a name="viewing-your-scans-and-scan-runs"></a>Zobrazení kontrol a spuštění kontroly

Pokud chcete zobrazit existující kontroly, udělejte toto:

1. Přejděte do centra pro správu. V části **zdroje a skenování** vyberte **zdroje dat** . 

2. Vyberte požadovaný zdroj dat. Zobrazí se seznam existujících kontrol pro daný zdroj dat.

3. Vyberte kontrolu, jejichž výsledky chcete zobrazit.

4. Na této stránce se zobrazí všechna předchozí kontrola spuštěná spolu se metrikami a stavy pro každé spuštění skenování. Zobrazí se také informace o tom, zda byla vaše kontrola naplánovaná nebo ruční, kolik prostředků bylo použito klasifikace, kolik prostředků bylo zjištěno, čas zahájení a ukončení kontroly a celková doba kontroly.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Správa kontrol – úpravy, odstranění nebo zrušení

Pokud chcete kontrolu spravovat nebo odstranit, postupujte takto:

1. Přejděte do centra pro správu. V části **zdroje a skenování** vyberte **zdroje dat** a potom vyberte požadovaný zdroj dat.

2. Vyberte kontrolu, kterou chcete spravovat. Kontrolu můžete upravit výběrem možnosti **Upravit**.

3. Kontrolu můžete odstranit výběrem možnosti **Odstranit**. 
