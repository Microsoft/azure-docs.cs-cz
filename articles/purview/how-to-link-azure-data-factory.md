---
title: Připojení k Azure Data Factory
description: Tento článek popisuje, jak připojit Azure Data Factory a Azure dosah a sledovat tak datovou řadu.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/22/2020
ms.openlocfilehash: 0e993cb1e53645f7081a20fc6a2785b8cfef1cce
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954174"
---
# <a name="how-to-connect-azure-data-factory-and-azure-purview"></a>Jak připojit Azure Data Factory a Azure dosah

Tento dokument popisuje kroky potřebné pro připojení účtu Azure Data Factory k účtu Azure dosah ke sledování datových linií. Dokument také získá informace o rozsahu pokrytí a podporovaných vzorcích pro čárové vzory.

## <a name="view-existing-data-factory-connections"></a>Zobrazit existující připojení Data Factory

Několik datových továrn Azure se může připojit k jednomu Data Catalog Azure dosah a získat informace o počtu řádků. Aktuální limit umožňuje připojit po dobu deseti Data Factory účtů z centra pro správu dosah. Pokud chcete zobrazit seznam Data Factory účtů připojených k vašemu Data Catalog dosah, udělejte toto:

1. V levém navigačním podokně vyberte **Centrum pro správu** .
2. V části **externí připojení** vyberte **Data Factory připojení**.
3. Zobrazí se seznam připojení Data Factory.

    :::image type="content" source="./media/how-to-link-azure-data-factory/data-factory-connection.png" alt-text="Snímek obrazovky znázorňující seznam připojení k datové továrně." lightbox="./media/how-to-link-azure-data-factory/data-factory-connection.png":::

4. Všimněte si různých hodnot pro **stav** připojení:

    - **Připojeno**: objekt pro vytváření dat je připojený ke katalogu dat.
    - **Odpojeno**: objekt pro vytváření dat má přístup ke katalogu, ale je připojený k jinému katalogu. V důsledku toho se datový řádek nebude automaticky hlásit katalogu.
    - **CannotAccess**: aktuální uživatel nemá přístup k objektu pro vytváření dat, takže stav připojení není známý.
 >[!Note]
 >Aby bylo možné zobrazit Data Factory připojení, je nutné přiřadit jednu z rolí dosah:
 >- Přispěvatel
 >- Vlastník
 >- Čtenář
 >- Správce uživatelských přístupů

## <a name="create-new-data-factory-connection"></a>Vytvořit nové připojení Data Factory

>[!Note]
>Chcete-li přidat nebo odebrat připojení Data Factory, je nutné přiřadit jednu z rolí dosah:
>- Vlastník
>- Správce uživatelských přístupů
>
> Kromě toho vyžaduje, aby uživatelé byli "vlastníkem" objektu pro vytváření dat "vlastník" nebo "Přispěvatel". 

Pomocí následujících kroků připojte existující účty Data Factory ke Data Catalog dosah.

1. V levém navigačním podokně vyberte **Centrum pro správu** .
2. V části **externí připojení** vyberte **Data Factory připojení**.
3. Na stránce **Data Factory připojení** vyberte **Nový**.

4. V seznamu vyberte svůj účet Data Factory a vyberte **OK**. Můžete také filtrovat podle názvu předplatného a omezit tak seznam.

    :::image type="content" source="./media/how-to-link-azure-data-factory/connect-data-factory.png" alt-text="Snímek obrazovky ukazující, jak se připojit Azure Data Factory." lightbox="./media/how-to-link-azure-data-factory/connect-data-factory.png":::

    Některé instance Data Factory mohou být zakázány, pokud je objekt pro vytváření dat již připojen k aktuálnímu účtu dosah, nebo objekt pro vytváření dat nemá spravovanou identitu.

    Pokud je některý z vybraných datových továrn již připojen k jinému účtu dosah, zobrazí se varovná zpráva. Když vyberete OK, připojení Data Factory k druhému účtu dosah se odpojí. Žádná další potvrzení nejsou vyžadována.


    :::image type="content" source="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png" alt-text="Snímek obrazovky s upozorněním na odpojení Azure Data Factory." lightbox="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png":::

>[!Note]
>Nyní podporujeme přidávání více než 10 datových továren najednou. Pokud chcete přidat více než 10 datových továren najednou, uložte lístek podpory.

### <a name="how-does-the-authentication-work"></a>Jak funguje ověřování?

Když uživatel dosah zaregistruje Data Factory ke kterému mají přístup, dojde k následujícím akcím v back-endu:

1. **Data Factory MSI** se přidá do role dosah RBAC: **dosah data kurátor**.

    :::image type="content" source="./media/how-to-link-azure-data-factory/adf-msi.png" alt-text="Snímek obrazovky zobrazující Azure Data Factory MSI" lightbox="./media/how-to-link-azure-data-factory/adf-msi.png":::
     
2. Kanál Data Factory je třeba znovu spustit, aby se metadata na řádku mohla vrátit do dosah.
3. Po spuštění bude Data Factory metadata vložena do dosah.

### <a name="remove-data-factory-connections"></a>Odebrat připojení Data Factory
Pokud chcete odebrat připojení k datové továrně, udělejte toto:

1. Na stránce **Data Factory připojení** vyberte tlačítko **Odebrat** vedle jednoho nebo více připojení ke službě Data Factory.
2. V místní nabídce vyberte **Potvrdit** a odstraňte vybraná připojení ke službě Data Factory.

    :::image type="content" source="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png" alt-text="Snímek obrazovky ukazující, jak vybrat datové továrny pro odebrání připojení" lightbox="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png":::

## <a name="configure-a-self-hosted-ir-to-collect-lineage-from-on-prem-sql"></a>Konfigurace prostředí IR v místním prostředí pro shromažďování řádků z Prem SQL

Pro místní databáze SQL je k dispozici Data Factory aktivita kopírování. Pokud provozujete místní prostředí Integration runtime pro přesun dat pomocí Azure Data Factory a chcete zachytit dosah ve službě Azure, ujistěte se, že verze je 4.8.7418.1 nebo novější. Další informace o modulu Integration runtime v místním prostředí najdete v tématu [Vytvoření a konfigurace prostředí Integration runtime](../data-factory/create-self-hosted-integration-runtime.md)v místním prostředí.

## <a name="supported-azure-data-factory-activities"></a>Podporované aktivity Azure Data Factory

Azure dosah zachycuje za běhu z následujících aktivit Azure Data Factory:

- Kopírování dat
- Data Flow
- Spustit balíček SSIS

> [!IMPORTANT]
> V případě, že zdroj nebo cíl používá nepodporovaný systém úložiště dat, služba Azure dosah zruší.

Integrace mezi Data Factory a dosah podporuje pouze podmnožinu datových systémů, které Data Factory podporuje, jak je popsáno v následujících částech.

### <a name="data-factory-copy-data-support"></a>Podpora Data Factory Kopírování dat

| Systém úložiště dat | Podporováno jako zdroj | Podporováno jako jímka |
| ------------------- | ------------------- | ----------------- |
| ADLS Gen1 | Yes | Ano (pouze bez binární kopie) |
| ADLS Gen2 | Yes | Yes |
| Azure Blob | Yes | Yes |
| Azure Cosmos DB (SQL API) | Yes | Yes |
| Azure Cosmos DB (rozhraní API Mongo) | Yes | Yes |
| Azure Cognitive Search | Yes | Yes |
| Průzkumník dat Azure | Yes | Yes |
| Databáze Azure Database for Marie DB \* | Yes | Yes |
| Azure Database for MYSQL \* | Yes | Yes |
| Azure Database for PostgreSQL \* | Yes | Yes |
| Azure File Storage | Yes | Ano |
| Azure Table Storage | Yes | Yes |
| Azure SQL Database \* | Yes | Yes |
| Azure SQL MI \* | Yes | Yes |
| Azure synapse Analytics (dřív SQL DW) \* | Yes | Yes |
| SQL Server on-Prem (vyžaduje se SHIR) \* | Yes | Yes |
| Amazon S3 | Yes | Yes |
| Teradata | Yes | Yes |
| SAP S4 Hana | Yes | Yes |
| SAP ECC | Yes | Yes |
| Hive | Yes | Yes |

> [!Note]
> Funkce line má určité nároky na výkon v Data Factory aktivity kopírování. Pro ty, kteří nastavili připojení k datové továrně v dosah, se může stát, že některé úlohy kopírování trvá déle. Většinou je dopad na zanedbatelné. Pokud úlohy kopírování trvá déle než obvykle, kontaktujte prosím podporu s časovým porovnáním.

### <a name="data-factory-data-flow-support"></a>Data Factory podpora toku dat

| Systém úložiště dat | Podporováno |
| ------------------- | ------------------- | ----------------- |
| ADLS Gen1 | Yes |
| ADLS Gen2 | Yes |
| Azure Blob | Yes |
| Azure SQL Database \* | Yes |
| Azure synapse Analytics (dřív SQL DW) \* | Yes |

### <a name="data-factory-execute-ssis-package-support"></a>Data Factory provádění podpory balíčků SSIS

| Systém úložiště dat | Podporováno |
| ------------------- | ------------------- | ----------------- |
| Azure Blob | Yes |
| ADLS Gen1 | Yes |
| ADLS Gen2 | Yes |
| Azure SQL Database \* | Yes |
| Azure SQL MI \*| Yes |
| Azure synapse Analytics (dřív SQL DW) \* | Yes |
| SQL Server Prem \* | Yes |
| Azure File Storage | Yes |

*\* V případě scénářů SQL (Azure a místních) nepodporuje Azure dosah uložené procedury nebo skripty pro kontrolu a zjišťování. U pořadu je omezené jenom na tabulky a zobrazení zdrojů.*

> [!Note]
> Platforma Azure Data Lake Storage Gen2 je teď obecně dostupná. Doporučujeme začít ji používat ještě dnes. Další informace najdete na [stránce produktu](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/).

## <a name="supported-lineage-patterns"></a>Podporované vzory pro čárové vzorky

Existuje několik vzorů, které dosah podporuje Azure. Vygenerovaná data na řádku vycházejí z typu zdroje a jímky používané v aktivitách Data Factory. I když Data Factory podporuje více než 80 zdrojů a jímka, Azure dosah podporuje pouze podmnožinu, jak je uvedeno v části [podporované Azure Data Factory aktivity](#supported-azure-data-factory-activities).

Pokud chcete nakonfigurovat Data Factory pro odesílání informací o čárových údajích, přečtěte si téma Začínáme s vydanými [řádky](catalog-lineage-user-guide.md#get-started-with-lineage).

Některé další způsoby, jak najít informace v zobrazení počtu řádků, zahrnují následující:

- Na kartě **se** zarážkami myší na obrazce zobrazíte další informace o assetu v popisku.
- Vyberte uzel nebo okraj pro zobrazení typu assetu, který patří, nebo přepněte prostředky.
- Sloupce datové sady se zobrazí na levé straně karty vydaných **řádků** . Další informace o čárových úrovních na úrovni sloupce naleznete v tématu [Column-Level line](catalog-lineage-user-guide.md#column-level-lineage).

### <a name="data-lineage-for-11-operations"></a>Datové řádky pro 1:1 operace

Nejběžnějším vzorem pro zachycení datových linií je přesouvání dat z jedné vstupní datové sady do jedné výstupní datové sady s procesem mezi.

Příkladem tohoto modelu může být následující:

- 1 zdroj/vstup: *Zákazník* (tabulka SQL)
- 1 jímka/výstup: *Customer1.csv* (objekt blob Azure)
- 1 proces: *CopyCustomerInfo1 \#Customer1.csv* (aktivita kopírování Data Factory)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage.png" alt-text="Snímek obrazovky zobrazující, že se pro jednu operaci kopírování Data Factory." lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage.png":::

### <a name="data-movement-with-11-lineage-and-wildcard-support"></a>Pohyb dat s 1:1em a podporou zástupných znaků

Dalším běžným scénářem pro zachycení řádku je použití zástupného znaku ke kopírování souborů z jedné vstupní datové sady do jedné výstupní datové sady. Zástupný znak umožňuje aktivitě kopírování vyhledat více souborů pro kopírování pomocí běžné části názvu souboru. Služba Azure dosah zachycuje u jednotlivých souborů zkopírovaných příslušnou aktivitu kopírování na úrovni souborů.

Příkladem tohoto modelu může být následující:

* Zdroj/vstup: *CustomerCall \* . csv* (adls Gen2 cesta)
* Jímka/výstup: *CustomerCall \* . csv* (soubor blob Azure)
* 1 proces: *CopyGen2ToBlob \#CustomerCall.csv* (aktivita kopírování Data Factory)  

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png" alt-text="Snímek obrazovky znázorňující, že jedna operace kopírování s podporou zástupných znaků je na jednom" lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png":::

### <a name="data-movement-with-n1-lineage"></a>Pohyb dat s použitím řádku n:1

Aktivity toku dat můžete použít k provádění operací s daty, jako je například sloučení, spojení a tak dále. K vytvoření cílové datové sady lze použít více než jednu zdrojovou datovou sadu. V tomto příkladu Azure dosah zachycuje na úrovni souborů pro jednotlivé vstupní soubory do tabulky SQL, která je součástí aktivity toku dat.

Příkladem tohoto modelu může být následující:

* 2 zdroje/vstupy: *Customer.csv*, *Sales. Parquet* (adls Gen2 cesta)
* 1 jímka/výstup: *firemní data* (tabulka Azure SQL)
* 1 proces: *DataFlowBlobsToSQL* (aktivita toku dat Data Factory)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png" alt-text="Snímek obrazovky ukazující, že n pro jednu operaci toku dat v jazyce D F" lightbox="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png":::

### <a name="lineage-for-resource-sets"></a>Řádky pro sady prostředků

Sada prostředků je logický objekt v katalogu, který představuje mnoho souborů oddílů v podkladovém úložišti. Další informace najdete v tématu [porozumění sadám prostředků](concept-resource-sets.md). Když Azure dosah zachycuje od Azure Data Factory, použijí pravidla pro normalizaci jednotlivých souborů oddílů a vytvoří jeden logický objekt.

V následujícím příkladu je sada prostředků Azure Data Lake Gen2 vytvořená z objektu blob Azure:

* 1 zdroj/vstup: *\_management.csvzaměstnanců* (Azure BLOB)
* 1 jímka/výstup: *\_management.csvzaměstnanců* (Azure Data Lake Gen 2)
* 1 proces: *CopyBlobToAdlsGen2 \_ RS* (aktivita kopírování Data Factory)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png" alt-text="Snímek obrazovky znázorňující vydanou řadu prostředků" lightbox="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png":::

## <a name="next-steps"></a>Další kroky

- [Uživatelská příručka k linii katalogu](catalog-lineage-user-guide.md)
- [Odkaz na službu Azure Data Share za účelem vyvedení](how-to-link-azure-data-share.md)
