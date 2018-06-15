---
title: Vyhledávání nestrukturovaných dat v cloudu Azure storage
description: Hledání nestrukturovaných dat s využitím Azure search.
author: roygara
manager: timlt
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: rogara
ms.custom: mvc
ms.openlocfilehash: 930b735eb03aea6ce701b694ca527049b4c3f24d
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2017
ms.locfileid: "23930201"
---
# <a name="search-unstructured-data-in-cloud-storage"></a>Prohledávání nestrukturovaných dat v cloudovém úložišti

V tomto kurzu zjistíte, jak k vyhledání nestrukturovaných dat s využitím [Azure Search](../../search/search-what-is-azure-search.md) pomocí dat uložených v Azure BLOB. Nestrukturovaných dat jsou data, která není předem definovaná způsobem uspořádány nebo nemá datový model. Příkladem může být soubor s příponou .txt.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření skupiny prostředků
> * vytvořit účet úložiště
> * Vytvoření kontejneru
> * Nahrání dat do vašeho kontejneru
> * Vytvoření služby vyhledávání prostřednictvím portálu
> * Použijte funkci hledání služby k vyhledání vašeho kontejneru

## <a name="download-the-sample"></a>Stažení ukázky

Ukázka datové sady připravený pro vás. **Stáhněte si [klinické trials.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip)**  a rozbalte ho do vlastní složky.

Ukázka se skládá z textových souborů získali z [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). Můžete je použít jako příklad textové soubory hledání s použitím Azure.

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Přihlaste se k portálu [Azure Portal](http://portal.azure.com).

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Účet úložiště poskytuje jedinečný umístění pro ukládání a přístup k vaší datové objekty Azure Storage.

V současné době existují dva typy účtů úložiště **Blob** a **pro obecné účely**. V tomto kurzu vytvoříte **pro obecné účely** účet úložiště.

Pokud nejste obeznámeni s procesem vytvoření účtu úložiště pro obecné účely, chcete-li vytvořit:

1. V nabídce vlevo vyberte **účty úložiště**, pak vyberte **přidat**.

2. Zadejte jedinečný název pro účet úložiště. 

3. Vyberte **Resource Manager** pro vaše **modelu nasazení** a vyberte **obecné účely** z **účet druh** rozevíracího seznamu.

4. Vyberte **místně redundantní úložiště (LRS)** z **replikace** rozevíracího seznamu.

5. V části **skupiny prostředků**, vyberte **vytvořit nový** a zadejte jedinečný název.

6. Vyberte odpovídající předplatné.

7. Vyberte umístění a vyberte **vytvořit.**

  ![Nestrukturovaných vyhledávání](media/storage-unstructured-search/storagepanes2.png)

## <a name="create-a-container"></a>Vytvoření kontejneru

Kontejnery jsou podobné do složek a se používají k ukládání objektů BLOB.

V tomto kurzu použijete k uložení textových souborů získali z clinicaltrials.gov jediný kontejner.

1. Přejděte na svůj účet úložiště na portálu Azure.

2. Vyberte **procházet objekty BLOB** pod **služby objektů Blob.**

3. Přidáte nový kontejner.

4. Název kontejneru "data" a vyberte **kontejneru** pro úroveň veřejný přístup.

5. Vyberte **OK** vytvoření kontejneru. 

  ![Nestrukturovaných vyhledávání](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>Nahrání dat příklad

Teď, když máte kontejner, můžete nahrát ukázková data do ní.

1. Vyberte vašeho kontejneru a vyberte **nahrát**.

2. Vyberte složku blue ikonu vedle pole souborů a přejděte do místní složky, které jste extrahovali ukázková data na obrázku.

3. Vyberte všechny extrahované soubory a vyberte **otevřít**

4. Vyberte **nahrát** zahájíte proces odesílání.

  ![Nestrukturovaných vyhledávání](media/storage-unstructured-search/upload.png)

Proces odesílání může chvíli trvat.

Až se dokončí, přejděte zpět do vaší kontejner dat potvrďte textové soubory.

  ![Nestrukturovaných vyhledávání](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>Vytvořte službu vyhledávání

Služba Azure Search je řešení vyhledávání jako služby cloud, které poskytuje vývojářům rozhraní API a nástroje pro přidání bohaté vyhledáváním přes data v aplikacích pro webové, mobilní a enterprise.

Pokud nejste obeznámeni s procesem vytvoření vyhledávací službu, chcete-li vytvořit:

1. Přejděte na svůj účet úložiště na portálu Azure.

2. Posuňte se dolů a klikněte na tlačítko **přidat Azure Search** pod **služby objektů BLOB**.

3. V části **importovat Data**, vyberte **vyberte služby**.

4. Vyberte **kliknutím sem vytvoříte novou službu vyhledávání**.

5. Uvnitř **nové služby vyhledávání** zadejte jedinečný název pro vaši službu vyhledávání v **URL** pole.

6. V části **skupiny prostředků**, vyberte **použít existující** a zvolte skupinu prostředků, který jste vytvořili dříve.

7. Pro **cenová úroveň**, vyberte **volné** vrstvy a klikněte na tlačítko **vyberte**.

8. Vyberte **vytvořit** vytvořit službu vyhledávání.

  ![Nestrukturovaných vyhledávání](media/storage-unstructured-search/createsearch2.png)

## <a name="connect-your-search-service-to-your-container"></a>Připojit k vaší kontejneru služby search

Teď, když máte vyhledávací službu, můžete se připojit do úložiště objektů blob. Tato část vás provede procesem výběr zdroje dat, vytváření indexu a vytvoření indexer.

1. Přejděte na svůj účet úložiště.

2. Vyberte **přidat vyhledávání systému Azure** pod **služby objektů BLOB.**

3. Vyberte **službu vyhledávání** uvnitř **importovat Data** a pak klikněte na službu vyhledávání, který jste vytvořili v předchozí části. Otevře **nový zdroj dat**.

### <a name="new-data-source"></a>Nový zdroj dat

  Zdroj dat určuje, která data do indexu a jak získat přístup k datům. Zdroj dat lze vícekrát ve stejnou službu vyhledávání.

1. Zadejte název pro zdroj dat. V části **Data extrahovat**, vyberte **obsahu a metadat**. Zdroj dat určuje, jaké části objektu blob jsou uloženy.
    
    a. Vlastní budoucí způsoby, můžete také vybrat **obsahují pouze metadata úložiště**. Výběr by provést, pokud chcete omezit data, která je indexované vlastnosti standardní objektů blob nebo uživatelem definované vlastnosti.
    
    b. Můžete také **všechna metadata** k získání obou vlastností standardní objektů blob a *všechny* konkrétních metadat typu obsahu. 

2. Vzhledem k tomu, že používáte objekty BLOB jsou textové soubory, nastavení **analýza režimu** k **Text**.
    
    a. Vlastní budoucí způsoby, budete pravděpodobně chtít vyberte [další analýza režimy](../../search/search-howto-indexing-azure-blob-storage.md) v závislosti na obsahu objektů BLOB.

  ![Nestrukturovaných vyhledávání](media/storage-unstructured-search/datasources.png)

3. Vyberte **kontejner úložiště** k zobrazení seznamu účtů úložiště k dispozici.

4. Vyberte účet úložiště a pak vyberte kontejner, který jste předtím vytvořili.

5. Klikněte na tlačítko **vyberte** se vraťte do **nový zdroj dat** a vyberte **OK** pokračujte.

  ![Nestrukturovaných vyhledávání](media/storage-unstructured-search/datacontainer.png)

### <a name="configure-the-index"></a>Konfigurace indexu

  Index je kolekce polí ze zdroje dat, které lze vyhledat. Index je, jak vaši službu vyhledávání zná, jakým způsobem mají být vyhledávány vaše data.

1. V **importovat data** vyberte **upravit cílový index**.
 
2. Zadejte název indexu v **název indexu** pole.

3. Vyberte **Retrievable** políčko atributu pod **metadata_storage_name**.

  ![Nestrukturovaných vyhledávání](media/storage-unstructured-search/valuestoselect.png)

4. Klikněte na tlačítko **OK**, která se vyvolá **vytvořením indexeru**.

Parametry indexu a atributů poskytnout tyto parametry jsou důležité. Zadejte parametry *co* data k uložení, zadejte atributy *jak* ukládat data.

**Název pole** sloupec obsahuje parametry. Následující tabulka obsahuje seznam dostupné atributy a jejich popisy.

### <a name="field-attributes"></a>Atributy polí
| Atribut | Popis |
| --- | --- |
| *Klíč* |Řetězec, který obsahuje jedinečné ID jednotlivých dokumentů, které používá pro vyhledávání dokumentů. Každý index musí mít jeden klíč. Jenom jedno pole může být klíč a jeho typ musí být nastavený na Edm.String. |
| *Retrievable* |Určuje, jestli může být pole vrácené ve výsledku hledání. |
| *Filterable* |Umožňuje použít pole ve filtrovacích dotazech. |
| *Sortable* |Umožňuje dotazu seřadit výsledky hledání podle tohoto pole. |
| *Facetable* |Umožňuje použití pole pro uživatelské řízené samotným filtrování lze použít ve struktuře Fasetové navigace. Jako fasety obvykle nejlépe fungují pole, která obsahují opakované hodnoty použitelné k seskupení více dokumentů (například více dokumentů, které spadají pod jednu značku nebo kategorii služeb). |
| *Searchable* |Označí pole jako fulltextově prohledávatelné. |


### <a name="create-an-indexer"></a>Vytvořením indexeru.
    
  Indexer zdroje dat se připojí pomocí indexu vyhledávání a poskytuje plánu přeindexování vaše data.

1. Zadejte název do pole **název** pole a vyberte **OK**.

  ![Nestrukturovaných vyhledávání](media/storage-unstructured-search/exindexer.png)

2. Budete přesměrováni zpět na **importovat Data**, vyberte **OK** dokončete proces připojení.

Nyní jste úspěšně připojeno objektu blob služby k vyhledávací službě. Jak dlouho trvá několik minut na portálu zobrazit, že se naplní index. Služba vyhledávání začne indexování okamžitě, abyste mohli začít hned vyhledávání.

## <a name="search-your-text-files"></a>Hledání textových souborů

Chcete-li hledat soubory, otevřete Průzkumník služby search uvnitř index nově vytvořený vyhledávací službě.

Následující kroky ukazují, kde najít Průzkumník služby search a nabízí několik ukázkových dotazů:

1. Přejděte ke všem prostředkům a vyhledejte svoji službu nově vytvořený vyhledávání.

  ![Nestrukturovaných vyhledávání](media/storage-unstructured-search/exampleurl.png)

3. Vyberte indexu tak, aby ho otevřít. 

  ![Nestrukturovaných vyhledávání](media/storage-unstructured-search/overview.png)

4. Vyberte **Průzkumník služby Search** otevřete Průzkumník služby search, kde můžete provést za provozu dotazy na data.

  ![Nestrukturovaných vyhledávání](media/storage-unstructured-search/indexespane.png)

5. Vyberte **vyhledávání** při pole řetězce dotazu je prázdný. Prázdný dotaz vrátí *všechny* data z objektů BLOB.

  ![Nestrukturovaných vyhledávání](media/storage-unstructured-search/emptySearch.png)

### <a name="full-text-search"></a>Fulltextové vyhledávání 

Zadejte "Myopia" **řetězec dotazu** pole a vyberte **vyhledávání**. Zahajování hledání obsah souborů a vrácení jen některé z nich, obsahující slovo "Myopia."

  ![Nestrukturovaných vyhledávání](media/storage-unstructured-search/secondMyopia.png) 

### <a name="system-properties-search"></a>Hledání vlastnosti systému

Můžete také vytvořit dotazy, které hledat podle vlastností systému pomocí `$select` parametr. Zadejte `$select=metadata_storage_name` do řetězce dotazu a stiskněte klávesu zadejte, vrácení pouze tohoto konkrétního pole.
    
Řetězec dotazu je přímou úpravou adresu URL, takže nejsou povoleny mezery. Vyhledat několik polí, používejte čárku, například:`$select=metadata_storage_name,metadata_storage_path`
    
`$select` Parametr lze použít pouze s pole, které jsou označené dá načíst při definování indexu.

  ![Nestrukturovaných vyhledávání](media/storage-unstructured-search/metadatasearchunstructured.png) 

Nyní jste dokončili tento kurz a mají prohledávatelné sadu Nestrukturovaná data.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili o vyhledávání nestrukturovaných dat pomocí Azure Search, jako například:

> [!div class="checklist"]
> * Vytvoření skupiny prostředků
> * vytvořit účet úložiště
> * Vytvoření kontejneru
> * Nahrání dat do vašeho kontejneru
> * Vytvořte službu vyhledávání
> * Pomocí služby vyhledávání k vyhledání vašeho kontejneru

Další informace o indexování dokumentů s Azure Search na tento odkaz.

> [!div class="nextstepaction"]
> [Indexování dokumentů v úložišti objektů Blob v Azure s Azure Search](../../search/search-howto-indexing-azure-blob-storage.md)