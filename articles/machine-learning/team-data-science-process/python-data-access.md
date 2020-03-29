---
title: Přístup k datovým souborům s klientskou knihovnou Pythonu – proces vědecké ho procesu týmových dat
description: Nainstalujte a použijte klientskou knihovnu Pythonu k bezpečnému přístupu a správě dat Azure Machine Learning z místního prostředí Pythonu.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720975"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Přístup k datovým sadám pomocí Pythonu a klientské knihovny služby Azure Machine Learning pro Python
Náhled klientské knihovny Microsoft Azure Machine Learning Python umožňuje zabezpečený přístup k datovým sestavám Azure Machine Learning z místního prostředí Pythonu a umožňuje vytváření a správu datových sad v pracovním prostoru.

Toto téma obsahuje pokyny, jak:

* instalace klientské knihovny Machine Learning Python
* přístup k datovým souborům a nahrávání, včetně pokynů, jak získat autorizaci pro přístup k datovým souborům Azure Machine Learning z místního prostředí Pythonu
* přístup k mezilehlým datovým souborům z experimentů
* Použití klientské knihovny Pythonu k vytvoření výčtu datových sad, přístupu k metadatům, čtení obsahu datové sady, vytvoření nových datových sad a aktualizaci existujících datových sad

## <a name="prerequisites"></a><a name="prerequisites"></a>Požadavky
Klientská knihovna Pythonu byla testována v následujících prostředích:

* Windows, Mac a Linux
* Python 2.7, 3.3 a 3.4

Má závislost na následujících balíčcích:

* Požadavky
* python-dateutil
* Pandy

Doporučujeme používat distribuci Pythonu, jako je [Anaconda](http://continuum.io/downloads#all) nebo [Canopy](https://store.enthought.com/downloads/), které jsou dodávány s Pythonem, IPythonem a třemi výše uvedenými balíčky. I když IPython není nezbytně nutné, je skvělé prostředí pro manipulaci a vizualizaci dat interaktivně.

### <a name="how-to-install-the-azure-machine-learning-python-client-library"></a><a name="installation"></a>Jak nainstalovat klientskou knihovnu Azure Machine Learning Python
Nainstalujte klientskou knihovnu Azure Machine Learning Python k dokončení úkolů popsaných v tomto tématu. Tato knihovna je k dispozici v [indexu balíčků Pythonu](https://pypi.python.org/pypi/azureml). Chcete-li jej nainstalovat do prostředí Pythonu, spusťte následující příkaz z místního prostředí Pythonu:

    pip install azureml

Případně si můžete stáhnout a nainstalovat ze zdrojů na [GitHubu](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Pokud máte na vašem počítači nainstalovaný git, můžete použít pip k instalaci přímo z úložiště git:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="use-code-snippets-to-access-datasets"></a><a name="datasetAccess"></a>Použití fragmentů kódu pro přístup k datovým souborům
Klientská knihovna Pythonu poskytuje programový přístup k existujícím datovým souborům z experimentů, které byly spuštěny.

Z webového rozhraní Azure Machine Learning Studio (klasické) můžete generovat fragmenty kódu, které obsahují všechny potřebné informace ke stažení a dekonalizaci datových sad jako objekty pandas DataFrame v místním počítači.

### <a name="security-for-data-access"></a><a name="security"></a>Zabezpečení přístupu k datům
Fragmenty kódu poskytované Azure Machine Learning Studio (klasické) pro použití s klientskou knihovnou Pythonu obsahuje ID pracovního prostoru a autorizační token. Ty poskytují úplný přístup k vašemu pracovnímu prostoru a musí být chráněny, jako heslo.

Z bezpečnostních důvodů je funkce fragmentu kódu k dispozici pouze uživatelům, kteří mají svou roli nastavenou jako **vlastník** pro pracovní prostor. Vaše role se zobrazí v Azure Machine Learning Studio (klasické) na stránce **USERS** v části **Nastavení**.

![Zabezpečení][security]

Pokud vaše role není nastavena jako **vlastník**, můžete buď požádat o opětovné pozvání jako vlastníka, nebo požádat vlastníka pracovního prostoru, aby vám poskytl fragment kódu.

Chcete-li získat autorizační token, můžete zvolit jednu z těchto možností:

* Požádejte o žeton od majitele. Vlastníci mají přístup ke svým autorizačním tokenům ze stránky Nastavení svého pracovního prostoru v Azure Machine Learning Studio (klasické). V levém podokně vyberte **Nastavení** a kliknutím na **TOKENY AUTORIZACE** zobrazíte primární a sekundární tokeny. Přestože primární nebo sekundární autorizační tokeny lze použít ve fragmentu kódu, doporučuje se, aby vlastníci sdíleli pouze sekundární autorizační tokeny.

   ![Autorizační tokeny](./media/python-data-access/ml-python-access-settings-tokens.png)

* Požádejte o povýšení na roli vlastníka: aktuální vlastník pracovního prostoru vás musí nejprve odebrat z pracovního prostoru a pak vás k němu znovu pozvat jako vlastníka.

Jakmile vývojáři získali ID pracovního prostoru a token autorizace, mají přístup k pracovnímu prostoru pomocí fragmentu kódu bez ohledu na jejich roli.

Autorizační tokeny jsou spravovány na stránce **TOKENY AUTORIZACE** v části **NASTAVENÍ**. Můžete je znovu vygenerovat, ale tento postup odvolá přístup k předchozímu tokenu.

### <a name="access-datasets-from-a-local-python-application"></a><a name="accessingDatasets"></a>Přístup k datovým souborům z místní aplikace Pythonu
1. V Machine Learning Studio (klasické) klikněte na **datové sady** na navigačním panelu vlevo.
2. Vyberte datovou sadu, ke které chcete získat přístup. Můžete vybrat libovolnou datovou sadu ze seznamu **MOJE DATOVÉ SADY** nebo ze seznamu **VZORKY.**
3. Na dolním panelu nástrojů klepněte na **generovat přístupový kód k datům**. Pokud jsou data ve formátu nekompatibilním s klientskou knihovnou Pythonu, je toto tlačítko zakázáno.
   
    ![Datové sady][datasets]
4. Vyberte fragment kódu z okna, které se zobrazí, a zkopírujte ho do schránky.
   
    ![Tlačítko Generovat přístupový kód k datům][dataset-access-code]
5. Vložte kód do poznámkového bloku místní aplikace Pythonu.
   
    ![Vložení kódu do poznámkového bloku][ipython-dataset]

## <a name="access-intermediate-datasets-from-machine-learning-experiments"></a><a name="accessingIntermediateDatasets"></a>Přístup k průběžným datovým souborům z experimentů machine learningu
Po spuštění experimentu v Machine Learning Studio (klasické), je možné získat přístup k zprostředkující datové sady z výstupních uzlů modulů. Zprostředkující datové sady jsou data, která byla vytvořena a použita pro zprostředkující kroky při spuštění nástroje modelu.

Zprostředkující datové sady lze přistupovat tak dlouho, dokud je formát dat kompatibilní s klientskou knihovnou Pythonu.

Následující formáty jsou podporovány (konstanty pro `azureml.DataTypeIds` tyto formáty jsou ve třídě):

* PlainText
* Obecný cscsv
* Obecný TSV
* Obecný csvnoheader
* Obecný název obecné hostova

Formát můžete určit najetím na výstupní uzel modulu. Zobrazí se spolu s názvem uzlu v popisku.

Některé moduly, například modul [Split,][split] výstup do `Dataset`formátu s názvem , který není podporován klientskou knihovnou Pythonu.

![Formát datové sady][dataset-format]

Chcete-li získat výstup do podporovaného formátu, je třeba použít modul převodu, například [Převést na soubor CSV][convert-to-csv].

![Obecný formát CSV][csv-format]

Následující kroky ukazují příklad, který vytvoří experiment, spustí jej a přistupuje k zprostředkující datové sadě.

1. Vytvořte nový experiment.
2. Vložte modul **dat binární klasifikace binární klasifikace příjmů ze sčítání dospělých.**
3. Vložte modul [Split][split] a připojte jeho vstup k výstupu modulu datové sady.
4. Vložte [modul Převést na CSV][convert-to-csv] a připojte jeho vstup k jednomu z výstupů [split][split] modulu.
5. Uložte experiment, spusťte jej a počkejte na dokončení úlohy.
6. Klepněte na výstupní uzel v modulu [Převést na CSV.][convert-to-csv]
7. Po zobrazení kontextové nabídky vyberte **generovat přístupový kód k datům**.
   
    ![Místní nabídka][experiment]
8. Vyberte fragment kódu a zkopírujte ho do schránky z okna, které se zobrazí.
   
    ![Generovat přístupový kód z kontextové nabídky][intermediate-dataset-access-code]
9. Vložte kód do poznámkového bloku.
   
    ![Vložení kódu do poznámkového bloku][ipython-intermediate-dataset]
10. Data můžete vizualizovat pomocí matplotlib. Zobrazí se v histogramu pro sloupci stáří:
    
    ![Histogram][ipython-histogram]

## <a name="use-the-machine-learning-python-client-library-to-access-read-create-and-manage-datasets"></a><a name="clientApis"></a>Použití klientské knihovny Machine Learning Python pro přístup, čtení, vytváření a správu datových sad
### <a name="workspace"></a>Pracovní prostor
Pracovní prostor je vstupním bodem pro klientskou knihovnu Pythonu. Poskytněte `Workspace` třídě ID pracovního prostoru a token autorizace pro vytvoření instance:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Výčet datových sad
Vytvoření výčtu všech datových sad v daném pracovním prostoru:

    for ds in ws.datasets:
        print(ds.name)

Vytvoření výčtu pouze datových sad vytvořených uživatelem:

    for ds in ws.user_datasets:
        print(ds.name)

Výčet pouze ukázkové datové sady:

    for ds in ws.example_datasets:
        print(ds.name)

K datové sadě můžete přistupovat podle názvu (který rozlišuje malá a velká písmena):

    ds = ws.datasets['my dataset name']

Nebo k němu můžete přistupovat podle indexu:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metadata
Datové sady mají kromě obsahu také metadata. (Zprostředkující datové sady jsou výjimkou z tohoto pravidla a nemají žádná metadata.)

Některé hodnoty metadat jsou přiřazeny uživatelem v době vytvoření:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Jiné jsou hodnoty přiřazené Azure ML:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Další `SourceDataset` informace o dostupných metadatech najdete v kurzu.

### <a name="read-contents"></a>Čtení obsahu
Fragmenty kódu poskytované Machine Learning Studio (klasické) automaticky stáhnout a rekonstruovat datovou sadu pandas DataFrame objektu. To se provádí `to_dataframe` metodou:

    frame = ds.to_dataframe()

Pokud dáváte přednost stažení nezpracovaných dat a proveďte rekonstrukci sami, je to možnost. V současné době je to jediná možnost pro formáty, jako je například "ARFF", které klientská knihovna Pythonu nemůže rekonstruovat.

Chcete-li číst obsah jako text:

    text_data = ds.read_as_text()

Chcete-li číst obsah jako binární:

    binary_data = ds.read_as_binary()

Můžete také jen otevřít datový proud k obsahu:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Vytvoření nové datové sady
Klientská knihovna Pythonu umožňuje nahrávat datové sady z programu Pythonu. Tyto datové sady jsou pak k dispozici pro použití ve vašem pracovním prostoru.

Pokud máte data v pandas DataFrame, použijte následující kód:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Pokud jsou data již serializována, můžete použít:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Klientská knihovna Pythonu je schopna serializovat pandas DataFrame do následujících `azureml.DataTypeIds` formátů (konstanty pro tyto jsou ve třídě):

* PlainText
* Obecný cscsv
* Obecný TSV
* Obecný csvnoheader
* Obecný název obecné hostova

### <a name="update-an-existing-dataset"></a>Aktualizace existující datové sady
Pokud se pokusíte nahrát novou datovou sadu s názvem, který odpovídá existující datové sadě, měla by se zobrazí chyba konfliktu.

Chcete-li aktualizovat existující datovou sadu, musíte nejprve získat odkaz na existující datovou sadu:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Pak `update_from_dataframe` použijte serializovat a nahradit obsah datové sady v Azure:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Pokud chcete serializovat data do jiného formátu, zadejte `data_type_id` hodnotu volitelného parametru.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Volitelně můžete nastavit nový popis zadáním hodnoty `description` parametru.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Volitelně můžete nastavit nový název zadáním hodnoty `name` parametru. Od této chvíle budete datovou sadu načítat pouze pomocí nového názvu. Následující kód aktualizuje data, název a popis.

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

`data_type_id`Parametry `name` `description` , a jsou volitelné a výchozí na jejich předchozí hodnotu. Parametr `dataframe` je vždy vyžadován.

Pokud jsou data již serializována, použijte `update_from_raw_data` místo . `update_from_dataframe` Pokud jste právě `raw_data` projít `dataframe`místo , funguje to podobným způsobem.

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

