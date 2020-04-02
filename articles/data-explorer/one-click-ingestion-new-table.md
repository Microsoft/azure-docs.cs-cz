---
title: Použití ingestování jedním kliknutím k ingestování dat do nové tabulky Průzkumníka dat Azure
description: Ingestování (načítání) dat do nové tabulky Průzkumníka dat Azure jednoduše pomocí ingestování jedním kliknutím.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 1e337a46d1d8ddda1b07bde6f12d4c1f7feda42f
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549222"
---
# <a name="use-one-click-ingestion-to-ingest-data-to-a-new-table-in-azure-data-explorer"></a>Použití ingestování jedním kliknutím k ingestování dat do nové tabulky v Azure Data Exploreru

Požití jedním kliknutím umožňuje rychlé ingestování dat v JSON, CSV a dalších formátech do tabulky. Pomocí webového uživatelského rozhraní Průzkumníka dat Azure můžete ingestovat data z úložiště, z místního souboru nebo z kontejneru. 

Tento dokument popisuje použití intuitivního průvodce jedním kliknutím k ingestování dat CSV z kontejneru do nové tabulky. Potom můžete upravit tabulku a spouštět dotazy pomocí webového uživatelského rozhraní Průzkumníka dat Azure. Můžete také nastavit nepřetržité ingestování automaticky ingestovat data do tabulky při aktualizaci zdrojových dat.

Přijetí jedním kliknutím je užitečné zejména při prvním ingestování dat nebo při tom, že schéma dat není pro vás známé. 

Přehled požití jedním kliknutím a seznam požadavků naleznete v tématu [Ingestion jedním kliknutím](ingest-data-one-click.md).
Informace o ingestování dat do existující tabulky v Průzkumníku dat Azure najdete v [tématu ingestování jedním kliknutím do existující tabulky.](one-click-ingestion-existing-table.md)

## <a name="ingest-new-data"></a>Ingestování nových dat

1. V levé nabídce webového uživatelského prostředí klepněte pravým tlačítkem myši na *databázi* a vyberte **příkaz Ingestovat nová data (náhled).**

    ![Výběr ingestování jedním kliknutím v uživatelském uživatelském počítači webu](media/one-click-ingestion-new-table/one-click-ingestion-in-web-ui.png)   
 
1. V okně **Ingestovat nová data (náhled)** je automaticky vybrána karta **Zdroj.** 

1. Vyberte **Vytvořit novou tabulku** a zadejte název nové tabulky. Můžete použít alfanumerické, spojovníky a podtržítka. Speciální znaky nejsou podporovány.

![Vytvoření nové tabulky](media/one-click-ingestion-new-table/create-new-table.png) 

[!INCLUDE [data-explorer-one-click-ingestion-types](../../includes/data-explorer-one-click-ingestion-types.md)]

Vyberte **Upravit schéma,** chcete-li zobrazit a upravit konfiguraci sloupce tabulky. Systém vybere jeden z objektů BLOB náhodně a schéma bude generováno na základě tohoto objektu blob. Při pohledu na název zdroje služba automaticky identifikuje, zda je komprimována nebo ne.

## <a name="edit-the-schema"></a>Úprava schématu

1. Na kartě **Schéma:**

    1. Vyberte **formát dat**:

        [!INCLUDE [data-explorer-one-click-ingestion-edit-schema](../../includes/data-explorer-one-click-ingestion-edit-schema.md)]

    1. Pokud vyberete **JSON**, musíte také vybrat **úrovně JSON**, od 1 do 10. Úrovně ovlivňují zobrazení dat sloupce tabulky. 

    ![Vybrat úrovně JSON](media/one-click-ingestion-new-table/json-levels.png)

    * Pokud vyberete jiný formát než JSON, můžete zaškrtnout políčko **Zahrnout názvy sloupců** a ignorovat řádek záhlaví souboru.

        ![Vybrat Zahrnout názvy sloupců](media/one-click-ingestion-new-table/non-json-format.png)
        
1. Do pole **Název mapování** zadejte název mapování. Můžete použít alfanumerické znaky a podtržítka. Mezery, speciální znaky a spojovníky nejsou podporovány.
    
    ![Název mapování tabulky](media/one-click-ingestion-new-table/table-mapping.png)

## <a name="copy-and-paste-queries"></a>Kopírování a vkládání dotazů

1. Nad podoknem **Editor** vyberte tlačítko **v** pro otevření editoru. V editoru můžete zobrazit a zkopírovat automatické dotazy generované z vašich vstupů. 
1. V tabulce: 
    * Poklikejte na nový název sloupce, který chcete upravit.
    * Vyberte nová záhlaví sloupců a proveďte některou z následujících akcí:
    
|Akce         |Popis                                  |
|-----------------|-------------------------------------------|
|Změna datového typu |Změna datového typu z typu automaticky vybraného službou na jeden z dalších [podporovaných datových typů](#edit-the-schema)|
|Přejmenovat sloupec    |Změna názvu sloupce |
|Nový sloupec       |Přidání nového sloupce|
|Odstranit sloupec    |Odstranění vybraného sloupce|
|Seřadit vzestupně   |Seřazení tabulky podle vybraného sloupce ve vzestupném pořadí (pouze existující sloupce)|
|Seřadit sestupně  |Seřazení tabulky podle vybraného sloupce v sestupném pořadí (pouze existující sloupce) |

> [!Note]
> Pro tabulkové formáty každý sloupec lze ingestovat do jednoho sloupce v Průzkumníku dat Azure.
> Můžete vytvořit nové sloupce z různých úrovní JSON.

[![](media/one-click-ingestion-new-table/edit-view.png "One-click ingestion edit view")](media/one-click-ingestion-new-table/edit-view.png#lightbox) 

## <a name="start-ingestion"></a>Zahájení požití

Vyberte **Zahájit požití,** chcete-li vytvořit tabulku a mapování a zahájit přijímdat data.
![Zahájení požití](media/one-click-ingestion-new-table/start-ingestion.png)

## <a name="data-ingestion-completed"></a>Požití dat dokončeno

V okně **dokončení ingestování dat** budou všechny tři kroky označeny zelenými značkami zaškrtnutí, pokud je požití dat úspěšně dokončeno.
 
![Požití dat jedním kliknutím dokončeno](media/one-click-ingestion-new-table/one-click-data-ingestion-complete.png)

[!INCLUDE [data-explorer-one-click-ingestion-query-data](../../includes/data-explorer-one-click-ingestion-query-data.md)]

**Nepřetržité požití**

Nepřetržité ingestování umožňuje vytvořit mřížku událostí, která naslouchá zdrojovému kontejneru. Každý nový objekt blob, který splňuje kritéria předdefinovaných parametrů (předpona, přípona a tak dále), bude automaticky ingestován do cílové tabulky.

> [!Note]
> Nepřetržité požití je relevantní pouze při požití z kontejneru.

1. Kliknutím **na Nepřetržité ingestování** otevřete portál Azure. Stránka datového připojení se otevře s otevřeným datovým konektorem mřížky událostí a již zadanými zdrojovými a cílovými parametry (zdrojový kontejner, tabulky a mapování).

1. Vyberte **Vytvořit,** chcete-li vytvořit datové připojení, které bude naslouchat všem změnám v tomto kontejneru kontejneru. 

## <a name="next-steps"></a>Další kroky

* [Dotazovat se na data ve webovém uživatelském rozhraní Průzkumníka dat Azure](/azure/data-explorer/web-query-data)
* [Psaní dotazů pro Azure Data Explorer pomocí kusto dotazovacího jazyka](/azure/data-explorer/write-queries)
