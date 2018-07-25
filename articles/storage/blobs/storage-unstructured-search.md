---
title: Vyhledávání nestrukturovaných dat v cloudovém úložišti Azure
description: Vyhledávání nestrukturovaných dat pomocí služby Azure Search.
author: roygara
manager: twooley
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: rogarana
ms.custom: mvc
ms.openlocfilehash: e50ff3b3a53a13d1604fcb7872853d758259ff9f
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136532"
---
# <a name="search-unstructured-data-in-cloud-storage"></a>Prohledávání nestrukturovaných dat v cloudovém úložišti

V tomto kurzu se dozvíte, jak pomocí [Azure Search](../../search/search-what-is-azure-search.md) vyhledávat nestrukturovaná data na základě dat uložených v objektech blob v Azure. Nestrukturovaná data jsou data, která buď nejsou uspořádaná předdefinovaným způsobem, nebo nemají datový model. Příkladem může být soubor s příponou TXT.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření skupiny prostředků
> * vytvořit účet úložiště
> * Vytvoření kontejneru
> * Nahrání dat do kontejneru
> * Vytvoření vyhledávací služby na portálu
> * Prohledávání kontejneru pomocí vyhledávací služby

## <a name="download-the-sample"></a>Stažení ukázky

Připravili jsme pro vás ukázkovou datovou sadu. **Stáhněte soubor [clinical-trials.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip)** a rozbalte ho do samostatné složky.

Ukázka se skládá z textových souborů získaných na webu [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). Můžete je použít jako příklad textových souborů k prohledávání pomocí Azure.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](http://portal.azure.com).

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Účet úložiště poskytuje jedinečné umístění pro ukládání datových objektů Azure Storage a přístup k nim.

V současné době existují dva typy účtů úložiště: **Objekt blob** a **Pro obecné účely**. V tomto kurzu vytvoříte účet úložiště typu **Pro obecné účely**.

Pokud ještě nevíte, jak vytvořit účet úložiště pro obecné účely, postup je následující:

1. V nabídce vlevo vyberte **Účty úložiště** a potom **Přidat**.

2. Zadejte jedinečný název účtu úložiště. 

3. Jako **Model nasazení** zvolte **Resource Manager** a v rozevírací nabídce **Druh účtu** zvolte **Pro obecné účely**.

4. V rozevírací nabídce **Replikace** vyberte **Místně redundantní úložiště (LRS)**.

5. U položky **Skupina prostředků** zvolte **Vytvořit nový** a zadejte jedinečný název.

6. Vyberte odpovídající předplatné.

7. Vyberte umístění a zvolte **Vytvořit**.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/storagepanes2.png)

## <a name="create-a-container"></a>Vytvoření kontejneru

Kontejnery jsou podobné složkám a používají se k ukládání objektů blob.

V tomto kurzu uložíte textové soubory získané z webu clinicaltrials.gov do jednoho kontejneru.

1. Na webu Azure Portal přejděte na svůj účet úložiště.

2. U položky **Blob Service** zvolte **Procházet objekty blob**.

3. Přidejte nový kontejner.

4. Dejte tomuto kontejneru název „data“ vyberte **Kontejner** pro veřejnou úroveň přístupu.

5. Kliknutím na **OK** kontejner vytvoříte. 

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>Nahrání ukázkových dat

Vytvořili jste kontejner a teď do něj můžete nahrát ukázková data.

1. Vyberte kontejner a zvolte **Nahrát**.

2. Vyberte modrou ikonu složky vedle pole Soubory a přejděte do místní složky, do které jste rozbalili ukázková data.

3. Vyberte všechny extrahované soubory a potom zvolte **Otevřít**.

4. Zvolte **Nahrát**, aby se zahájil proces nahrávání.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/upload.png)

Proces nahrávání může chvíli trvat.

Po jeho dokončení přejděte zpátky do svého datového kontejneru a zkontrolujte, jestli se do něj nahrály textové soubory.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>Vytvoření vyhledávací služby

Azure Search je cloudové řešení pro vyhledávání v podobě služby, které poskytuje vývojářům rozhraní API a nástroje, aby mohli přidat výkonné vyhledávání dat ve webových, mobilních a firemních aplikacích.

Pokud ještě nevíte, jak vytvořit vyhledávací službu, postup je následující:

1. Na webu Azure Portal přejděte na svůj účet úložiště.

2. Posuňte se dolů a u položky **BLOB SERVICE** klikněte na **Přidat Azure Search**.

3. U položky **Importovat data** zvolte **Vyberte svoji službu**.

4. Zvolte **Pokud chcete vytvořit novou vyhledávací službu, klikněte sem**.

5. V části **Nová služba vyhledávání** zadejte do pole **Adresa URL** jedinečný název vyhledávací služby.

6. V části **Skupina prostředků** zvolte **Použít existující** a vyberte skupinu prostředků, kterou jste předtím vytvořili.

7. U položky **Cenová úroveň** zvolte úroveň **Free** a klikněte na **Vybrat**.

8. Vytvořte vyhledávací službu kliknutím na **Vytvořit**.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/createsearch2.png)

## <a name="connect-your-search-service-to-your-container"></a>Propojení služby Search s kontejnerem

Vytvořili jste vyhledávací službu a teď ji můžete připojit ke svému úložišti objektů blob. Tato část vás provede procesem výběru zdroje dat a vytvořením indexu a indexeru.

1. Přejděte na svůj účet úložiště.

2. U položky **BLOB SERVICE** klikněte na **Přidat Azure Search**.

3. U položky **Importovat data** zvolte **Služba vyhledávání** a potom klikněte na vyhledávací službu, kterou jste vytvořili v předchozí části. Otevře se okno **Nový zdroj dat**.

### <a name="new-data-source"></a>Nový zdroj dat

  Zdroj dat určuje, jaká data se mají indexovat a jak se k nim bude získávat přístup. Stejná vyhledávací služba může opakovaně využívat jeden zdroj dat.

1. Zadejte název zdroje dat. V části **Data, která se mají extrahovat** zvolte **Obsah a metadata**. Zdroj dat určuje, které části objektu blob se mají indexovat.
    
    a. Ve vlastních budoucích scénářích můžete taky zvolit **Jenom metadata úložiště**. Tuto položku vyberte v případě, že chcete omezit data k indexaci na standardní vlastnosti objektů blob nebo vlastnosti definované uživatelem.
    
    b. Můžete taky zvolit **Všechna metadata** a získat jak standardní vlastnosti objektu blob, tak *všechna* metadata specifická pro daný typ obsahu. 

2. Vzhledem k tomu, že objekty blob, které používáte, jsou textové soubory, u položky **Režim parsování** nastavte **Text**.
    
    a. Ve vlastních budoucí scénářích možná budete chtít vybrat [jiné režimy parsování](../../search/search-howto-indexing-azure-blob-storage.md) v závislosti na obsahu objektů blob.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/datasources.png)

3. Zvolte **Kontejner úložiště**. Zobrazí se seznam dostupných účtů úložiště.

4. Vyberte svůj účet úložiště a potom vyberte kontejner, který jste předtím vytvořili.

5. Kliknutím na **Vybrat** se vrátíte do okna **Nový zdroj dat**. Pokračujte kliknutím na **OK**.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/datacontainer.png)

### <a name="configure-the-index"></a>Konfigurace indexu

  Index je kolekce polí ze zdroje dat, která se dají prohledávat. Index umožňuje vyhledávací službě poznat, jakými způsoby se mají vaše data prohledávat.

1. V části **Importovat data** zvolte **Přizpůsobit cílový index**.
 
2. Do pole **Název indexu** zadejte název svého indexu.

3. Zaškrtněte políčko atributu **Zobrazitelné** v části **metadata_storage_name**.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/valuestoselect.png)

4. Klikněte na **OK**. Zobrazí se okno **Vytvořit indexer**.

Parametry indexu a atributy, které jim přiřadíte, mají velký význam. Parametry určují, *jaká* data se mají uložit, a atributy zase udávají, *jak* má ukládání dat probíhat.

Parametry jsou uvedené ve sloupci **NÁZEV POLE**. Následující tabulka obsahuje seznam dostupných atributů a jejich popisy.

### <a name="field-attributes"></a>Atributy polí
| Atribut | Popis |
| --- | --- |
| *Klíč* |Řetězec obsahující jedinečné ID jednotlivých dokumentů, které slouží k vyhledávání dokumentů. Každý index musí mít jeden klíč. Jenom jedno pole může být klíč a jeho typ musí být nastavený na Edm.String. |
| *Retrievable* |Určuje, jestli může být pole vrácené ve výsledku hledání. |
| *Filterable* |Umožňuje použít pole ve filtrovacích dotazech. |
| *Sortable* |Umožňuje dotazu seřadit výsledky hledání podle tohoto pole. |
| *Facetable* |Umožňuje použití pole ve struktuře fasetové navigace k filtrování, které je řízené samotným uživatelem. Jako fasety obvykle nejlépe fungují pole, která obsahují opakované hodnoty použitelné k seskupení více dokumentů (například více dokumentů, které spadají pod jednu značku nebo kategorii služeb). |
| *Searchable* |Označí pole jako fulltextově prohledávatelné. |


### <a name="create-an-indexer"></a>Vytvoření indexeru
    
  Indexer propojuje zdroj dat s indexem vyhledávání a poskytuje plán přeindexování dat.

1. Do pole **Název** zadejte název a klikněte na **OK**.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/exindexer.png)

2. Vrátíte se do okna **Importovat data**. Kliknutím na **OK** dokončíte proces připojování.

Právě jste úspěšně připojili svůj objekt blob k vyhledávací službě. Bude několik minut trvat, než se naplnění indexu daty projeví na portálu. Vyhledávací služba ale hned začne indexovat, takže můžete okamžitě vyhledávat.

## <a name="search-your-text-files"></a>Prohledávání textových souborů

Pokud chcete prohledávat soubory, otevřete v nově vytvořené vyhledávací službě Průzkumníka služby Hledání.

Následující postup vám napoví, kde Průzkumníka služby Hledání najít, a najdete v něm i pár ukázkových dotazů:

1. Přejděte na všechny prostředky a najděte nově vytvořenou vyhledávací službu.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/exampleurl.png)

3. Vyberte svůj index a otevřete ho. 

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/overview.png)

4. Vyberte a otevřete **Průzkumníka služby Hledání**, ve kterém můžete zadávat živé dotazy na data.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/indexespane.png)

5. Při prázdném poli řetězce dotazu zvolte **Hledat**. Prázdný dotaz vrátí *všechna* data z objektů blob.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/emptySearch.png)

### <a name="full-text-search"></a>Fulltextové vyhledávání 

Do pole **Řetězec dotazu** zadejte „Myopia“ a zvolte **Hledat**. Zahájí se prohledávání obsahu souborů a vrátí se podmnožina souborů, které obsahují slovo „Myopia“.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/secondMyopia.png) 

### <a name="system-properties-search"></a>Vyhledávání podle systémových vlastností

Pomocí parametru `$select` můžete taky vytvářet dotazy, které vyhledávají podle systémových vlastností. Do řetězce dotazu zadejte `$select=metadata_storage_name` a stiskněte Enter. Vrátí se jenom příslušné pole.
    
Řetězec dotazu přímo upravuje adresu URL, takže v něm nejsou povolené mezery. Pokud chcete vyhledat víc polí, použijte čárku, jak je vidět tady: `$select=metadata_storage_name,metadata_storage_path`
    
Parametr `$select` se dá použít jenom u polí, která při definování indexu označíte jako zobrazitelná.

  ![Prohledávání nestrukturovaných dat](media/storage-unstructured-search/metadatasearchunstructured.png) 

Právě jste dokončili tento kurz a máte prohledávatelnou sadu nestrukturovaných dat.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se seznámili s prohledáváním nestrukturovaných dat pomocí služby Azure Search a následujícími tématy:

> [!div class="checklist"]
> * Vytvoření skupiny prostředků
> * vytvořit účet úložiště
> * Vytvoření kontejneru
> * Nahrání dat do kontejneru
> * Vytvoření vyhledávací služby
> * Prohledávání kontejneru pomocí vyhledávací služby

Pokud se chcete dozvědět víc o indexování dokumentů v Azure Search, klikněte na následující odkaz.

> [!div class="nextstepaction"]
> [Indexování dokumentů pomocí služby Azure Blob Storage v Azure Search](../../search/search-howto-indexing-azure-blob-storage.md)