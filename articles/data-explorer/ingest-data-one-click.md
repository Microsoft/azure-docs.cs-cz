---
title: Ingestování dat do Azure Data Exploreru pomocí jedním kliknutím
description: Přehled ingestování (načítání) dat do Azure Data Explorer jednoduše pomocí ingestování jedním kliknutím.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 5bde63427ce76f14832551864bbf2c3d8e015fd6
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521587"
---
# <a name="what-is-one-click-ingestion"></a>Co je požití jedním kliknutím? 

Požití jedním kliknutím umožňuje rychlé ingestování dat a automatické navrhování tabulek a struktur mapování na základě zdroje dat v Průzkumníku dat Azure. 

Pomocí webového uživatelského rozhraní Průzkumníka dat Azure můžete ingestovat data z úložiště (soubor objektů blob), místního souboru nebo kontejneru (až 10 000 objektů BLOB). Můžete také definovat mřížku událostí v kontejneru pro nepřetržité ingestování. Data lze ingestovat do existující nebo nové tabulky v JSON, CSV a [dalších formátech](#file-formats). Jedno kliknutí na ingestování můžete navrhnout strukturu pro nové tabulky a mapování tabulky, na základě zdroje dat a poskytují intuitivní platformu pro úpravu struktury tabulky existující tabulky a tabulky mapování. Jedno kliknutí požití bude stelit data do tabulky během několika minut.

Přijetí jedním kliknutím je užitečné zejména při prvním ingestování dat nebo při tom, že schéma dat není pro vás známé.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Vytvořte cluster a databázi Průzkumníka dat Azure](create-cluster-database-portal.md).
* Přihlaste se k [webovému uživatelskému rozhraní Průzkumníka dat Azure](https://dataexplorer.azure.com/) a [přidejte připojení ke svému clusteru](/azure/data-explorer/web-query-data#add-clusters).

## <a name="file-formats"></a>Formáty souborů

Požití jedním kliknutím podporuje ingestování nové tabulky ze zdrojových dat v některém z následujících formátů:
* JSON
* CSV
* Tsv
* SCSV
* SOHSV
* TSVE
* PSV

## <a name="one-click-ingestion-wizard"></a>Průvodce přijím jedním kliknutím

Průvodce požitím jedním kliknutím vás provede procesem požití jedním kliknutím. 

> [!Note]
> Tato část popisuje průvodce obecně. Vybrané možnosti závisí na tom, zda ingestujete do nové nebo existující tabulky. Další informace naleznete v tématu:
    > * Požití do [nové tabulky](one-click-ingestion-new-table.md)
    > * Ingestování do [existující tabulky](one-click-ingestion-existing-table.md) 
    
1. Chcete-li získat přístup k průvodci, klikněte pravým tlačítkem myši na *databázi* nebo řádek *tabulky* v levé nabídce webového uživatelského rozhraní průzkumníka dat Azure a vyberte **položku Ingestovat nová data (náhled).**

    ![Výběr ingestování jedním kliknutím v uživatelském uživatelském počítači webu](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   

1. Průvodce vás provede následujícími možnostmi:
       * Ingestování do [existující tabulky](one-click-ingestion-existing-table.md)
       * Požití do [nové tabulky](one-click-ingestion-new-table.md)
       * Ingestování dat z: * Úložiště objektů blob * Místní soubor * Kontejner
       * Zadejte velikost vzorku od 1 do 10 000 řádků (pouze z kontejneru).
       
1. Po úspěšném výběru zdroje dat se zobrazí náhled dat. 
    Pokud ingestujete data z kontejneru, můžete je filtrovat tak, aby byly ingestovány pouze soubory s určitými předčíslími nebo příponami souborů. Můžete například chtít ingestovat pouze soubory s názvy souborů začínajícími slovem *Evropa*nebo pouze soubory s příponou *JSon*. 

1. Klepněte na **položku Upravit schéma**. Pokud ingestujete data do určité tabulky, můžete namapovat zdrojové sloupce na cílové sloupce a rozhodnout, zda chcete zahrnout názvy sloupců.

1. Spusťte proces požití dat.

> [!Note]
> Pokud je váš zdroj dat kontejner, všimněte si, že zásady agregace agregace agregace dat (dávkování) aplikace Azure Data Explorer jsou navržené k optimalizaci procesu ingestování. Ve výchozím nastavení je zásada nakonfigurována na 5 minut nebo 500 MB dat, takže může dojít k latenci. Viz [zásady dávkování](/azure/kusto/concepts/batchingpolicy) pro možnosti agregace. Při příjmu dat z jiných zdrojů, požití se projeví okamžitě.

## <a name="next-steps"></a>Další kroky

* Rozhodněte se, zda budete k ingestování dat do [existující nebo](one-click-ingestion-existing-table.md) [nové tabulky](one-click-ingestion-new-table.md) používat ingestování jedním kliknutím
* [Dotazovat se na data ve webovém uživatelském rozhraní Průzkumníka dat Azure](/azure/data-explorer/web-query-data)
* [Psaní dotazů pro Azure Data Explorer pomocí kusto dotazovacího jazyka](/azure/data-explorer/write-queries)