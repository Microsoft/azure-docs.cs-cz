---
title: 'Kurz: Prohledávání nestrukturovaných dat v úložišti objektů Blob v Azure'
description: 'Kurz: Prohledávání nestrukturovaných dat v úložišti objektů Blob pomocí služby Azure search.'
author: roygara
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 12/13/2018
ms.author: rogarana
ms.custom: mvc
ms.openlocfilehash: 42c67d73ee776488fbe932676f61cb7166c2984b
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53599828"
---
# <a name="tutorial-search-unstructured-data-in-cloud-storage"></a>Kurz: Prohledávání nestrukturovaných dat v cloudovém úložišti

V tomto kurzu se dozvíte, jak k prohledávání nestrukturovaných dat s využitím [Azure Search](../../search/search-what-is-azure-search.md), pomocí dat uložených v úložišti objektů Blob v Azure. Nestrukturovaná data jsou data, která buď nejsou uspořádaná předdefinovaným způsobem, nebo nemají datový model. Příkladem je soubor s příponou .txt.

Tento kurz vyžaduje, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření skupiny prostředků
> * vytvořit účet úložiště
> * Vytvoření kontejneru
> * Nahrání dat do kontejneru
> * Vytvoření vyhledávací služby na portálu
> * Připojení služby search na účet úložiště
> * Vytvoření zdroje dat
> * Konfigurace indexu
> * Vytvoření indexeru
> * Prohledávání kontejneru pomocí vyhledávací služby

## <a name="prerequisites"></a>Požadavky

Každý účet úložiště musí patřit do nějaké skupiny prostředků Azure. Skupina prostředků je logický kontejner pro seskupení služeb Azure. Když vytvoříte účet úložiště, máte možnost vytvořit novou skupinu prostředků nebo použijte existující skupinu prostředků. Tento kurz vytvoří novou skupinu prostředků.

Přihlaste se k webu [Azure Portal](http://portal.azure.com).

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

Ukázkové datové sadě připravili jsme pro vás tak, aby měli využít pro účely tohoto kurzu. Stáhněte si [klinické trials.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip) a rozbalte ho do samostatné složky.

Ukázka se skládá z textových souborů získaných na webu [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). V tomto kurzu je využívá jako příklad textové soubory, které budou vyhledány pomocí služby Azure Search.

## <a name="create-a-container"></a>Vytvoření kontejneru

Kontejnery jsou podobné složkám a používají se k ukládání objektů blob.

V tomto kurzu uložíte textové soubory získané z webu clinicaltrials.gov do jednoho kontejneru.

1. Přejděte do účtu úložiště na webu Azure Portal.

2. Vyberte **procházet objekty BLOB** pod **služby Blob Service**.

3. Přidejte nový kontejner.

4. Název kontejneru **data** a vyberte **kontejneru** pro úroveň veřejného přístupu.

5. Kliknutím na **OK** kontejner vytvoříte.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>Nahrání ukázkových dat

Vytvořili jste kontejner a teď do něj můžete nahrát ukázková data.

1. Vyberte kontejner a zvolte **Nahrát**.

2. Vyberte ikonu modré složky vedle **soubory** pole a přejděte do místní složky, které jste extrahovali ukázková data.

3. Vyberte všechny extrahované soubory a pak vyberte **otevřít**.

4. Zvolte **Nahrát**, aby se zahájil proces nahrávání.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/upload.png)

Proces nahrávání může chvíli trvat.

Poté, co dokončí, přejděte zpět do kontejneru dat potvrďte, že nahraný textové soubory.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>Vytvoření vyhledávací služby

Azure Search je řešení vyhledávání jako služby cloud, které poskytuje vývojářům rozhraní API a nástroje pro přidání vyhledávací funkce nad vašimi daty.

V tomto kurzu použijete k vyhledat textové soubory získané z clinicaltrials.gov vyhledávací službu.

1. Přejděte do účtu úložiště na webu Azure Portal.

2. Přejděte dolů a vyberte možnost **přidat Azure Search** pod **služby BLOB SERVICE**.

3. U položky **Importovat data** zvolte **Vyberte svoji službu**.

4. Zvolte **Pokud chcete vytvořit novou vyhledávací službu, klikněte sem**.

5. V části **Nová služba vyhledávání** zadejte do pole **Adresa URL** jedinečný název vyhledávací služby.

6. V části **skupiny prostředků**vyberte **použít existující** a zvolte skupinu prostředků, kterou jste vytvořili dříve.

7. Pro **cenová úroveň**, vyberte **Free** vrstvy a klikněte na tlačítko **vyberte**.

8. Vytvořte vyhledávací službu kliknutím na **Vytvořit**.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/createsearch2.png)

## <a name="connect-your-search-service-to-your-container"></a>Propojení služby Search s kontejnerem

Vytvořili jste vyhledávací službu a teď ji můžete připojit ke svému úložišti objektů blob. Tato část vás provede procesem výběru zdroje dat a vytvořením indexu a indexeru.

1. Přejděte do účtu úložiště.

2. Vyberte **přidat Azure Search** pod **služby BLOB SERVICE**.

3. Vyberte **vyhledávací služba** uvnitř **Import dat**a potom klikněte na službu search, kterou jste vytvořili v předchozí části. Tím se otevře **nový zdroj dat**.

### <a name="create-a-data-source"></a>Vytvoření zdroje dat

  Zdroj dat určuje, jaká data se mají indexovat a jak se k nim bude získávat přístup. Stejná vyhledávací služba může opakovaně využívat jeden zdroj dat.

1. Zadejte název zdroje dat. V části **Data, která se mají extrahovat** zvolte **Obsah a metadata**. Zdroj dat určuje, které části objektu blob se mají indexovat.

2. Nastavit, protože používáte objekty BLOB jsou textové soubory, **analýze režimu** k **Text**.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/datasources.png)

3. Zvolte **Kontejner úložiště**. Zobrazí se seznam dostupných účtů úložiště.

4. Vyberte svůj účet úložiště a potom vyberte kontejner, který jste vytvořili dříve.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/datacontainer.png)

5. Klikněte na tlačítko **vyberte** se vraťte do **nový zdroj dat**a vyberte **OK** pokračujte.

### <a name="configure-the-index"></a>Konfigurace indexu

  Index je kolekce polí ze zdroje dat, která se dají prohledávat. Nastavit a nakonfigurovat parametry v těchto polí tak, aby vaše vyhledávací služba ví, co způsoby data prohledat.

1. V **importovat data**vyberte **přizpůsobit cílový index**.

2. Do pole **Název indexu** zadejte název svého indexu.

3. Vyberte **Retrievable** atributu zaškrtávací políčko v oblasti **metadata_storage_name**.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/valuestoselect.png)

4. Vyberte **OK**, která se vyvolá **vytvořit Indexer**.

Parametry indexu a atributy, které jim přiřadíte, mají velký význam. Zadejte parametry *co* data k uložení a atributy určují *jak* na tato data ukládat.

Parametry jsou uvedené ve sloupci **NÁZEV POLE**. Následující tabulka obsahuje seznam dostupných atributů a jejich popisy.

#### <a name="field-attributes"></a>Atributy polí

| Atribut | Popis |
| --- | --- |
| *Klíč* |Řetězec obsahující jedinečné ID jednotlivých dokumentů, které slouží k vyhledávání dokumentů. Každý index musí mít jeden klíč. Jenom jedno pole může být klíč a jeho typ musí být nastavený na Edm.String. |
| *Retrievable* |Určuje, jestli může být pole vrácené ve výsledku hledání. |
| *Filterable* |Umožňuje použít pole ve filtrovacích dotazech. |
| *Sortable* |Umožňuje dotazu seřadit výsledky hledání podle tohoto pole. |
| *Facetable* |Umožňuje použití pole ve struktuře fasetové navigace k filtrování, které je řízené samotným uživatelem. Pole, která obsahují opakované hodnoty, které můžete použít k dokumentům skupiny společně (například více dokumentů, které spadají pod jednu značku nebo kategorii služeb) obvykle fungují nejlépe jako omezujících vlastností. |
| *Searchable* |Označí pole jako fulltextově prohledávatelné. |

### <a name="create-an-indexer"></a>Vytvoření indexeru

  Indexer propojuje zdroj dat s indexem vyhledávání a poskytuje plán přeindexování dat.

1. Do pole **Název** zadejte název a klikněte na **OK**.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/exindexer.png)

2. Budete přesměrováni zpět **Import dat**. Vyberte **OK** dokončete proces připojení.

Právě jste úspěšně připojili svůj objekt blob k vyhledávací službě. Bude několik minut trvat, než se naplnění indexu daty projeví na portálu. Vyhledávací služba ale hned začne indexovat, takže můžete okamžitě vyhledávat.

## <a name="search-your-text-files"></a>Prohledávání textových souborů

Pokud chcete prohledávat soubory, otevřete v nově vytvořené vyhledávací službě Průzkumníka služby Hledání.

Následující postup vám napoví, kde Průzkumníka služby Hledání najít, a najdete v něm i pár ukázkových dotazů:

1. Přejít na všechny prostředky a najděte si nově vytvořený vyhledávací službu.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/exampleurl.png)

2. Vyberte indexu tak, aby ho otevřete.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/overview.png)

3. Vyberte **Průzkumníka služby Search** otevřete Průzkumníka služby search, kde můžete provádět živých dotazů na data.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/indexespane.png)

4. Při prázdném poli řetězce dotazu zvolte **Hledat**. Prázdný dotaz vrátí *všechna* data z objektů blob.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/emptySearch.png)

### <a name="perform-a-full-text-search"></a>Fulltextové vyhledávání

Zadejte **Myopia** v **řetězec dotazu** pole a vyberte **hledání**. Tento krok spustí vyhledávání soubory obsahu a vrátí jen některé z nich, který obsahuje slovo "Myopia."

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/secondMyopia.png)

### <a name="perform-a-system-properties-search"></a>Hledání vlastnosti systému

Kromě fulltextové vyhledávání, můžete vytvořit dotazy, které hledat podle vlastností systému pomocí `$select` parametru.

Zadejte `$select=metadata_storage_name` do řetězce dotazu a stiskněte klávesu **Enter**. To způsobí, že pouze tento konkrétní pole se má vrátit.

Řetězec dotazu přímo upravuje adresu URL, takže v něm nejsou povolené mezery. Pokud chcete vyhledat víc polí, použijte čárku, jak je vidět tady: `$select=metadata_storage_name,metadata_storage_path`

Parametr `$select` se dá použít jenom u polí, která při definování indexu označíte jako zobrazitelná.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/metadatasearchunstructured.png)

Právě jste dokončili tento kurz a máte prohledávatelnou sadu nestrukturovaných dat.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejjednodušší způsob, jak odebrat prostředky, které jste vytvořili, je odstranit skupinu prostředků. Odebráním skupiny prostředků se odstraní také všechny prostředky, které tato skupina obsahuje. V následujícím příkladu se odebráním skupiny prostředků odebere účet úložiště a samotná skupiny prostředků.

1. Na webu Azure Portal přejděte na seznam skupin prostředků ve vašem předplatném.
2. Vyberte skupinu prostředků, kterou chcete odstranit.
3. Vyberte **odstranit skupinu prostředků** tlačítko a zadejte název skupiny prostředků v poli odstranění.
4. Vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

Další informace o indexování dokumentů s Azure Search na následujícím odkazu:

> [!div class="nextstepaction"]
> [Indexování dokumentů pomocí služby Azure Blob Storage v Azure Search](../../search/search-howto-indexing-azure-blob-storage.md)
