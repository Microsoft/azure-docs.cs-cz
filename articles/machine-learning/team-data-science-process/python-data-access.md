---
title: Přístup k datovým sadám pomocí Pythonu klientské knihovny - vědecké zpracování týmových dat
description: Instalace a použití klientské knihovny pro Python k přístupu a bezpečně spravovat Azure Machine Learning data z místního prostředí Pythonu.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 93ec5e740ac6acf9420a9d980092ed772ac1618e
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720975"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Přístup k datovým sadám pomocí Pythonu a klientské knihovny služby Azure Machine Learning pro Python
Klientská knihovna Python pro Microsoft Azure Machine Learning ve verzi preview můžete povolit zabezpečený přístup k vaší datové sady Azure Machine Learning z místního prostředí Pythonu a umožňuje vytváření a Správa datových sad v pracovním prostoru.

Toto téma obsahuje pokyny o tom, jak:

* Nainstalujte klientské knihovny pro Python pro Machine Learning
* přístup a nahrajte datové sady, včetně informací o tom, jak získat autorizaci pro přístup k datové sady Azure Machine Learning z vašeho místního prostředí Pythonu
* přístup k zprostředkující datovým sadám z experimentů
* použití klientské knihovny Pythonu k zobrazení výčtu datových sad, přístupu k metadatům, čtení obsahu datové sady, vytváření nových datových sad a aktualizaci existujících datových sad

## <a name="prerequisites"></a>Požadavky
Klientská knihovna Python pro prošel testováním v následujících prostředích:

* Windows, Mac a Linux
* Python 2.7, 3.3 a 3.4

Je závislý na následujících balíčků:

* požadavků
* Python dateutil
* pandas

Doporučujeme použít distribuci Pythonu, jako je [Anaconda](http://continuum.io/downloads#all) nebo [zápoje](https://store.enthought.com/downloads/), která se dodává s Pythonem, IPython a třemi balíčky uvedenými výše. I když IPython není bezpodmínečně nutné, je skvělé prostředí pro zpracování a interaktivní vizualizace dat.

### <a name="installation"></a>Postup instalace klientské knihovny Azure Machine Learning Pythonu
Nainstalujte klientskou knihovnu Azure Machine Learning Python pro dokončení úkolů popsaných v tomto tématu. Tato knihovna je k dispozici z [indexu balíčku Pythonu](https://pypi.python.org/pypi/azureml). Ho Pokud chcete nainstalovat ve vašem prostředí Pythonu, spusťte následující příkaz z vašeho místního prostředí Pythonu:

    pip install azureml

Případně si můžete stáhnout a nainstalovat ze zdrojů na [GitHubu](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Pokud máte na svém počítači nainstalovaný git, můžete nainstalovat přímo z úložiště git pip:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="datasetAccess"></a>Použití fragmentů kódu pro přístup k datovým sadám
Klientská knihovna Python poskytuje programový přístup k stávajících objektech datových sad z experimenty, které byly spuštěny.

Z webového rozhraní Azure Machine Learning Studio (Classic) můžete vygenerovat fragmenty kódu, které zahrnují všechny nezbytné informace ke stažení a deserializaci datových sad jako objekty dataframe PANDAS na místním počítači.

### <a name="security"></a>Zabezpečení přístupu k datům
Fragmenty kódu, které poskytuje Azure Machine Learning Studio (Classic) pro použití s knihovnou klienta Python, zahrnují ID pracovního prostoru a autorizační token. Tyto poskytují úplný přístup k vašemu pracovnímu prostoru a musí být chráněn, jako je heslo.

Z bezpečnostních důvodů je funkce fragmentu kódu dostupná jenom uživatelům, kteří mají roli nastavenou jako **vlastník** pracovního prostoru. Vaše role se zobrazí v Azure Machine Learning Studio (Classic) na stránce **Uživatelé** v části **Nastavení**.

![Zabezpečení][security]

Pokud vaše role není nastavená jako **vlastník**, můžete buď požádat o pozvání jako vlastník, nebo požádat vlastníka pracovního prostoru, aby vám poskytl fragment kódu.

K získání autorizačního tokenu si můžete vybrat jednu z těchto možností:

* Požádejte o token od vlastníka. Vlastníci mají přístup ke svým autorizačním tokenům ze stránky nastavení svého pracovního prostoru v Azure Machine Learning Studio (Classic). V levém podokně vyberte **Nastavení** a pro zobrazení primárních a sekundárních tokenů klikněte na **autorizační tokeny** . Přestože primární nebo sekundární autorizačních tokenech je možné ve fragmentu kódu, se doporučuje vlastníky sdílet jenom sekundární autorizačních tokenech.

   ![Autorizačních tokenech](./media/python-data-access/ml-python-access-settings-tokens.png)

* Požádat o zvýšení úrovně role vlastníka: aktuální vlastník pracovního prostoru musí nejdřív odebrat z pracovního prostoru a potom ho znovu pozvat jako vlastníka.

Po získání ID pracovního prostoru a autorizačního tokenu můžou vývojáři získat přístup k pracovnímu prostoru pomocí fragmentu kódu bez ohledu na jejich roli.

Autorizační tokeny se spravují na stránce **AUTORIZAČNÍ tokeny** v **Nastavení**. Můžete je obnovit, ale tento postup odebere přístup k předchozí token.

### <a name="accessingDatasets"></a>Přístup k datovým sadám z místní aplikace Pythonu
1. V Machine Learning Studio (Classic) klikněte na **datové sady** na navigačním panelu vlevo.
2. Vyberte datovou sadu, kterou chcete získat přístup. Můžete vybrat libovolnou datovou sadu ze seznamu **Moje datové sady** nebo ze seznamu **ukázek** .
3. V dolním panelu nástrojů klikněte na možnost **generovat kód pro přístup k datům**. Pokud jsou data ve formátu, který je kompatibilní s klientské knihovny pro Python, toto tlačítko je zakázané.
   
    ![Datové sady][datasets]
4. Vyberte fragment kódu z okna, který se zobrazí a zkopírujte do schránky.
   
    ![Data přístupový kód tlačítko Generovat][dataset-access-code]
5. Vložte kód do poznámkového bloku vaše místní aplikace v Pythonu.
   
    ![Kód vložte do poznámkového bloku][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>Přístup k mezilehlé datové sadě z Machine Learning experimentů
Po spuštění experimentu v Machine Learning Studio (Classic) je možné získat přístup k mezilehlé datové sadě z výstupních uzlů modulů. Zprostředkující datové sady jsou data, která byla vytvořena a používá se pro zprostředkující kroky při byl spuštěn nástroj modelu.

Zprostředkující datové sady je možný za předpokladu, formát dat je kompatibilní s klientské knihovny pro Python.

Podporovány jsou následující formáty (konstanty pro tyto formáty jsou ve třídě `azureml.DataTypeIds`):

* Ve formátu prostého textu
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

Podržením ukazatele nad uzlem výstup modulu můžete určit formát. Zobrazí se spolu s název uzlu v popisku.

Některé moduly, například [rozdělený][split] modul, výstup do formátu s názvem `Dataset`, který není podporován knihovnou klienta Python.

![Formát datové sady][dataset-format]

Chcete-li získat výstup do podporovaného formátu, je nutné použít převodový modul, například [převést na sdílený svazek clusteru][convert-to-csv].

![Formát GenericCSV][csv-format]

Následující postup ukazuje příklad, který vytvoří experiment, spustí jej a přistupuje k zprostředkující datové sady.

1. Vytvoření nového experimentu.
2. Vloží modul **datové sady binární klasifikace pro příjem z dospělého** .
3. Vložte [rozdělený][split] modul a připojte jeho vstup k výstupu modulu DataSet.
4. Vložte modul [převést do sdíleného svazku clusteru][convert-to-csv] a připojte jeho vstup k jednomu z výstupů [rozděleného][split] modulu.
5. Uložte experiment, spusťte ho a počkejte, než se úloha dokončí.
6. Klikněte na uzel výstup v modulu [Převod do sdíleného svazku clusteru][convert-to-csv] .
7. Jakmile se zobrazí místní nabídka, vyberte možnost **generovat kód pro přístup k datům**.
   
    ![Kontextová nabídka][experiment]
8. Vyberte fragment kódu a zkopírujte do schránky z okna, která se zobrazí.
   
    ![Generovat kód pro přístup k místní nabídce][intermediate-dataset-access-code]
9. Vložte kód v poznámkovém bloku.
   
    ![Kód vložte do poznámkového bloku][ipython-intermediate-dataset]
10. Můžete vizualizovat data pomocí matplotlib. Zobrazí se v histogramu, věk sloupce:
    
    ![Histogram][ipython-histogram]

## <a name="clientApis"></a>Použití klientské knihovny Machine Learning Python pro přístup k datovým sadám, jejich čtení, vytváření a správě
### <a name="workspace"></a>Pracovní prostor
Pracovní prostor je vstupním bodem pro klientské knihovny pro Python. Zadejte třídu `Workspace` s ID pracovního prostoru a autorizačním tokenem pro vytvoření instance:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Vytvářet výčty sad dat
Vytvořit výčet všech datových sad v daném pracovním prostoru:

    for ds in ws.datasets:
        print(ds.name)

Chcete získat výčet jenom uživatel vytvořil datové sady:

    for ds in ws.user_datasets:
        print(ds.name)

Chcete získat výčet jenom příklad datové sady:

    for ds in ws.example_datasets:
        print(ds.name)

Datová sada je přístupná po názvu (což je velká a malá písmena):

    ds = ws.datasets['my dataset name']

Nebo můžete k němu přístup podle indexu:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metadata
Datové sady mají metadat, kromě obsah. (Zprostředkující datové sady jsou výjimkou z tohoto pravidla a nemají žádná metadata.)

Některé hodnoty metadat jsou přiřazeny uživatelem v okamžiku vytvoření:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Ostatní hodnoty přiřazené službou Azure ML jsou:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Další informace o dostupných metadatech najdete v `SourceDataset` třídy.

### <a name="read-contents"></a>Přečtěte si obsah
Fragmenty kódu, které poskytuje Machine Learning Studio (Classic), automaticky stáhnou a deserializovat datovou sadu do objektu PANDAS dataframe. To se provádí pomocí metody `to_dataframe`:

    frame = ds.to_dataframe()

Pokud chcete stáhnout nezpracovaná data a provádět sami deserializace, který je možnost. V tuto chvíli Toto je jediná možnost pro formáty, jako je například "ARFF", které nelze deserializovat klientské knihovny pro Python.

Pro daný obsah přečíst jako text:

    text_data = ds.read_as_text()

K načtení obsahu jako binární soubor:

    binary_data = ds.read_as_binary()

Můžete také pouze otevřít datový proud obsahu:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Vytvoření nové datové sady
Klientská knihovna Python vám umožní nahrát datové sady z programu Pythonu. Tyto datové sady jsou pak k dispozici pro použití ve vašem pracovním prostoru.

Pokud máte data v pandas DataFrame, použijte následující kód:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Pokud vaše data již serializován, můžete použít:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Knihovna klienta Pythonu je schopná serializovat PANDAS dataframe do následujících formátů (konstanty pro tyto jsou ve třídě `azureml.DataTypeIds`):

* Ve formátu prostého textu
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Aktualizovat existující datovou sadu
Pokud se pokusíte odeslat novou datovou sadu s názvem, který odpovídá existující datovou sadu, měli byste obdržet chybu konflikt.

Pokud chcete aktualizovat existující datovou sadu, musíte nejprve získat odkaz na existující datovou sadu:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Pak použijte `update_from_dataframe` k serializaci a nahrazení obsahu datové sady v Azure:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Pokud chcete data serializovat do jiného formátu, zadejte hodnotu volitelného parametru `data_type_id`.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Volitelně můžete nastavit nový popis zadáním hodnoty pro parametr `description`.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Volitelně můžete nastavit nový název zadáním hodnoty pro parametr `name`. Od této chvíle budete načte datovou sadu pomocí nového názvu. Následující kód aktualizuje data, název a popis.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

Parametry `data_type_id`, `name` a `description` jsou volitelné a výchozí jejich předchozí hodnota. Parametr `dataframe` je vždy povinný.

Pokud jsou vaše data už serializovaná, použijte místo `update_from_dataframe``update_from_raw_data`. Pokud místo `dataframe`předáte pouze `raw_data`, funguje podobným způsobem.

<!-- Images -->
[security]:./media/python-data-access/security.png
[dataset-format]:./media/python-data-access/dataset-format.png
[csv-format]:./media/python-data-access/csv-format.png
[datasets]:./media/python-data-access/datasets.png
[dataset-access-code]:./media/python-data-access/dataset-access-code.png
[ipython-dataset]:./media/python-data-access/ipython-dataset.png
[experiment]:./media/python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

