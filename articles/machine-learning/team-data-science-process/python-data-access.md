---
title: Přístup k datové sady s knihovnou klienta Machine Learning Python | Microsoft Docs
description: Nainstalovat a používat klientské knihovny Python přístup k datům a spravovat Azure Machine Learning bezpečně z místního prostředí Python.
services: machine-learning
documentationcenter: python
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 9ab42272-c30c-4b7e-8e66-d64eafef22d0
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: f97fbb76ddf48fb3c7ec79b6b2ed8cee3e0ceabb
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Přístup k datovým sadám pomocí Pythonu a klientské knihovny služby Azure Machine Learning pro Python
Ve verzi preview Klientská knihovna pro Microsoft Azure Machine Learning Python můžete povolit zabezpečený přístup k vaší Azure Machine Learning datové sady z místního prostředí Python a umožňuje vytváření a správu datových sad v pracovním prostoru.

Toto téma obsahuje pokyny o tom, jak:

* nainstaluje klientské knihovny pro Machine Learning Python 
* přístup a nahrání datové sady, včetně pokyny, jak získat oprávnění pro přístup k Azure Machine Learning datové sady z místního prostředí Python
* přístup k zprostředkující datové sady z experimentů
* Použijte klientskou knihovnu Python výčet datové sady, získat přístup k metadatům, přečíst obsah datové sady, vytvořit nové datové sady a aktualizovat existující datové sady

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="prerequisites"></a>Požadavky
Klientská knihovna Python byl testován v následujících prostředích:

* Windows, Mac a Linux
* Python 2.7, 3.3 a 3.4

Má závislost na následujících balíčků:

* žádostí
* Python dateutil
* pandas

Doporučujeme použít distribuci jazyka Python, jako [Anaconda](http://continuum.io/downloads#all) nebo [zápoje](https://store.enthought.com/downloads/), které jsou součástí Python, IPython a nainstalované tři balíčky uvedené výše. I když IPython není nezbytně nutné, je skvělé prostředí pro manipulace a vizualizace dat interaktivně.

### <a name="installation"></a>Postup instalace knihovnu klienta služby Azure Machine Learning Python
Knihovnu klienta služby Azure Machine Learning Python musí být nainstalován také dokončit úlohy popsané v tomto tématu. Je k dispozici z [indexu balíčků Pythonu](https://pypi.python.org/pypi/azureml). K její instalaci ve vašem prostředí Python, spusťte následující příkaz z místního prostředí Python:

    pip install azureml

Alternativně můžete stáhnout a nainstalovat ze zdroje na [githubu](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Pokud máte nainstalovaný na počítači git, můžete instalovat přímo z úložiště git pip:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="datasetAccess"></a>Fragmenty kódu Studio použít pro přístup k datové sady
Klientská knihovna Python umožňuje programový přístup k vaší stávající datové sady z experimenty, které byly spuštěny.

Z webové rozhraní Studio můžete vygenerovat fragmenty kódu, které zahrnují všechny potřebné informace ke stažení a deserializovat datové sady jako objekty Pandas DataFrame na počítači pro umístění.

### <a name="security"></a>Zabezpečení pro přístup k datům
Fragmenty kódu poskytované Studio pro použití s knihovnou klienta Python zahrnují id pracovního prostoru a autorizační token. Tyto poskytnutí plného přístupu do pracovního prostoru a musí být chráněny, jako je heslo.

Z bezpečnostních důvodů funkce fragmentu kódu je k dispozici pouze uživatelé, kteří mají roli nastavit jako **vlastníka** pro pracovní prostor. Vaše role se zobrazí v Azure Machine Learning Studio na **uživatelé** v části **nastavení**.

![Zabezpečení][security]

Pokud vaše role není nastaven jako **vlastníka**, můžete buď žádost o nového pozvání jako vlastníka, nebo požádejte vlastníka pracovního prostoru, kde přinášejí fragmentu kódu.

Pokud chcete získat autorizačním tokenem, můžete provést jednu z těchto možností:

* Požádejte o token z vlastníka. Přístup k nim vlastníci jejich autorizace tokeny na stránce nastavení jejich pracovního prostoru v Studio. Vyberte **nastavení** levém podokně a klikněte na **AUTORIZACE TOKENY** zobrazíte primární a sekundární tokenů.  Přestože primární nebo sekundární autorizace tokeny můžete použít ve fragmentu kódu, se doporučuje vlastníky sdílet jenom tokeny sekundární ověřování.

![Tokeny ověřování](./media/python-data-access/ml-python-access-settings-tokens.png)

* Požádejte o povýšit na roli vlastníka.  Aktuální vlastník pracovního prostoru musí k tomu, odeberte nejprve můžete z pracovního prostoru a poté znovu vás pozval k němu jako vlastníka.

Po získání vývojáři id pracovního prostoru a autorizace tokenu, budou mít přístup k pracovním prostoru pomocí fragmentu kódu bez ohledu na jejich role.

Tokeny ověřování jsou spravovány v **AUTORIZACE TOKENY** v části **nastavení**. Můžete je obnovit, ale tento postup odebere přístup k předchozím tokenem.

### <a name="accessingDatasets"></a>Datové sady přístup z místních aplikací Python
1. V nástroji Machine Learning Studio, klikněte na tlačítko **datové sady** na navigačním panelu na levé straně.
2. Vyberte datovou sadu, kterou jste chtěli přístup. Můžete vybrat libovolný datových sad z **Moje datové sady** seznamu nebo z **UKÁZKY** seznamu.
3. V dolním panelu nástrojů klikněte na **generování dat přístupového kódu**. Pokud jsou data ve formátu, který je kompatibilní s knihovnou klienta Python, toto tlačítko k dispozici.
   
    ![Datové sady][datasets]
4. Vyberte fragmentu kódu zobrazeném okně, které se zobrazí a zkopírujte jej do schránky.
   
    ![Přístupový kód][dataset-access-code]
5. Kód vložte do poznámkového bloku místní aplikace Python.
   
    ![Poznámkový blok][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>Zprostředkující datové sady přístup z experimentů Machine Learning
Po spuštění experimentu v nástroji Machine Learning Studio, je možné k zprostředkující datové sady z výstupních uzlů modulů. Zprostředkující datové sady jsou data, která byla vytvořena a používá se pro zprostředkující kroky při byl spuštěn nástroj modelu.

Zprostředkující datové sady je přístupný, dokud formát dat je kompatibilní s knihovnou klienta Python.

Jsou podporovány následující formáty (jsou konstanty pro tyto `azureml.DataTypeIds` třída):

* Ve formátu prostého textu
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

Můžete určit formát podržením ukazatele nad uzlem výstup modulu. Zobrazí se společně s název uzlu, v popisu tlačítka.

Některé moduly, například [rozdělení] [ split] modulu, výstup do formátu s názvem `Dataset`, který nepodporuje Python klientské knihovny.

![Formát datové sady][dataset-format]

Budete muset použít převod modulu, například [převést na sdílený svazek clusteru][convert-to-csv], chcete-li získat výstup do podporovaného formátu.

![Formát GenericCSV][csv-format]

Následující postup ukazuje příklad, který vytvoří experimentu, ji spustí a přistupuje k zprostředkující datovou sadu.

1. Vytvoření nového experimentu.
2. Vložit **datovou sadu pro dospělé úplné zjišťování příjem binární klasifikace** modulu.
3. Vložení [rozdělení] [ split] modul a připojte se k výstupu modulu datová sada vstupem.
4. Vložení [převést na sdílený svazek clusteru] [ convert-to-csv] modulu a připojte se vstupem do jednoho z [rozdělení] [ split] výstupy modulu.
5. Uložte experiment, spouštět a počkejte na dokončení spuštění.
6. Klikněte na uzel výstup na [převést na sdílený svazek clusteru] [ convert-to-csv] modulu.
7. Po zobrazení v místní nabídce vyberte **generování dat přístupového kódu**.
   
    ![Kontextové nabídky][experiment]
8. Vyberte fragmentu kódu a zkopírujte jej do schránky z okna, který se zobrazí.
   
    ![Přístupový kód][intermediate-dataset-access-code]
9. Vložte kód v poznámkovém bloku.
   
    ![Poznámkový blok][ipython-intermediate-dataset]
10. Můžete vizualizovat data pomocí matplotlib. Zobrazí se v histogram stáří sloupce:
    
    ![Histogram][ipython-histogram]

## <a name="clientApis"></a>Použijte klientskou knihovnu Machine Learning Python přístup, číst, vytvářet a spravovat datové sady
### <a name="workspace"></a>Pracovní prostor
Pracovní prostor je vstupní bod pro klientská knihovna Python. Zadejte `Workspace` s id pracovního prostoru a autorizační token pro vytvoření instance:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Zobrazení výčtu datových sad
Výčet všech datových sad v daném prostoru:

    for ds in ws.datasets:
        print(ds.name)

Výčet právě vytvořené uživatelem datové sady:

    for ds in ws.user_datasets:
        print(ds.name)

Chcete získat výčet jenom datové sady příklad:

    for ds in ws.example_datasets:
        print(ds.name)

Datové sady se můžete dostat pomocí názvu (což je malá a velká písmena):

    ds = ws.datasets['my dataset name']

Nebo můžete k němu přístup podle indexu:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metadata
Datové sady mít metadata, kromě obsahu. (Zprostředkující datové sady představují výjimku toto pravidlo a nemají žádné metadata.)

Některé hodnoty metadata jsou přiřazeny uživatelem v okamžiku vytvoření:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Jiné jsou hodnoty přiřadila Azure ML:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Najdete v článku `SourceDataset` třídy pro další informace o dostupných metadata.

### <a name="read-contents"></a>Načíst obsah
Fragmenty kódu poskytované Machine Learning Studio automaticky stáhnout a deserializaci na objekt Pandas DataFrame datovou sadu. To lze provést pomocí `to_dataframe` metoda:

    frame = ds.to_dataframe()

Pokud dáváte přednost stáhnout nezpracovaná data a provádět deserializace sami, který je možnost. V tuto chvíli Toto je jediná možnost pro formáty například 'ARFF', které nelze deserializovat Klientská knihovna Python.

Číst obsah jako text:

    text_data = ds.read_as_text()

Číst obsah jako binární:

    binary_data = ds.read_as_binary()

Můžete také stačí otevřít datový proud do obsahu:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Vytvořit novou sadu dat
Klientská knihovna Python vám umožní nahrát datové sady z programu Python. Tyto datové sady jsou pak k dispozici pro použití v pracovním prostoru.

Pokud máte v Pandas DataFrame vaše data, použijte následující kód:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Pokud už je serializováno vaše data, můžete použít:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Klientská knihovna Python je schopen serializovat Pandas DataFrame do následujících formátů (konstanty pro tyto jsou v `azureml.DataTypeIds` třída):

* Ve formátu prostého textu
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Aktualizovat existující datovou sadu
Pokud se pokusíte odeslat novou datovou sadu s názvem, který odpovídá existující datovou sadu, měli byste obdržet chybu konfliktu.

Pokud chcete aktualizovat existující datovou sadu, musíte nejprve získat odkaz na existující datovou sadu:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Potom pomocí `update_from_dataframe` k serializaci a nahraďte jeho obsah datové sady v Azure:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Pokud chcete k serializaci dat do jiného formátu, zadejte hodnotu pro volitelné `data_type_id` parametr.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Volitelně můžete nastavit nový popis zadáním hodnoty pro `description` parametr.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Volitelně můžete nastavit nový název tak, že zadáte hodnotu `name` parametr. Od této chvíle budete načíst datové sady pomocí nového názvu. Následující kód aktualizuje data, název a popis.

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

`data_type_id`, `name` a `description` parametry jsou volitelné a výchozí jejich předchozí hodnotu. `dataframe` Vždy parametr je povinný.

Pokud už je serializováno vaše data, použijte `update_from_raw_data` místo `update_from_dataframe`. Pokud předáte právě v `raw_data` místo `dataframe`, funguje podobným způsobem.

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

