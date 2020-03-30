---
title: 'Spustit skript Pythonu: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak spustit modul Script Pythonu v Azure Machine Learning ke spuštění kódu Pythonu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: 0f86d1ad03062797764af6a0d49beacaa3458a8f
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365555"
---
# <a name="execute-python-script-module"></a>Spuštění modulu Skript Pythonu

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží ke spuštění kódu Pythonu. Další informace o architektuře a principech návrhu Pythonu naleznete [v následujícím článku](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts).

V Pythonu můžete provádět úkoly, které momentálně nejsou podporovány existujícími moduly, například:

+ Vizualizace dat pomocí`matplotlib`
+ Použití knihoven Pythonu k vytvoření výčtu datových sad a modelů v pracovním prostoru
+ Čtení, načítání a manipulace s daty ze zdrojů, které nejsou podporovány modulem [Import dat](./import-data.md)
+ Spusťte svůj vlastní kód pro hluboké učení 


Azure Machine Learning používá distribuci Anaconda pythonu, která zahrnuje mnoho běžných nástrojů pro zpracování dat. Budeme aktualizovat verzi Anaconda automaticky. Aktuální verze je:
 -  Distribuce Anaconda 4.5+ pro Python 3.6 

Předinstalované balíčky jsou:
-    adal==1,2,2
-    applicationinsights==0.11.9
-    attrs==19.3.0
-    azurová-společná==1.1.25
-    azurové jádro==1.3.0
-    azurový grafrbac==0,61,1
-    azurová identita==1.3.0
-    azure-mgmt-authorization==0.60.0
-    azure-mgmt-containerregistry==2.8.0
-    azure-mgmt-keyvault==2.2.0
-    azure-mgmt-resource==8.0.1
-    azure-mgmt-storage==8.0.0
-    azure-storage-blob==1.5.0
-    azurové úložiště-společné==1.4.2
-    azureml-core==1.1.5.5
-    azureml-dataprep-nativní==14.1.0
-    azureml-dataprep==1.3.5
-    azureml-defaults==1.1.5.1
-    azureml-designer-classic-modules==0.0.118
-    azureml-designer-core==0.0.31
-    azureml-designer-interní==0.0.18
-    azureml-model-management-sdk==1.0.1b6.post1
-    azureml-pipeline-core==1.1.5
-    azureml-telemetrie==1.1.5.3
-    backports.tempfile==1.0
-    backports.weakref==1.0.post1
-    boto3==1,12,29
-    botocore==1,15,29
-    cachetools==4.0.0
-    certifi==2019.11.28
-    cffi==1,12,3
-    chardet==3.0.4
-    cvaknutí==7.1.1
-    cloudpickle==1.3.0
-    configparser==3.7.4
-    contextlib2==0.6.0.post1
-    kryptografie==2,8
-    cyklor==0,10,0
-    kopr==0,3,1,1
-    distro==1.4.0
-    docker==4.2.0
-    docutils==0,15,2
-    dotnetcore2==2.1.13
-    baňka==1,0,3
-    pojistka==3,0,1
-    gensim==3.8.1
-    google-api-core==1.16.0
-    google-auth==1.12.0
-    google-cloud-core==1.3.0
-    google-cloud-storage==1.26.0
-    google-resumable-media==0.5.0
-    googleapis-common-protos==1.51.0
-    gunicorn==19.9.0
-    idna==2,9
-    imbalanced-learn==0,4,3
-    isodate==0,6,0
-    itsdangerous==1.1.0
-    jeepney==0,4,3
-    jinja2==2,11,1
-    jmespath==0,9,5
-    joblib==0,14,0
-    json-logování-py==0,2
-    jsonpickle==1,3
-    jsonschema==3.0.1
-    kiwisolver==1.1.0
-    liac-arff==2.4.0
-    lightgbm==2.2.3
-    markupsafe==1.1.1
-    matplotlib==3.1.3
-    více itertools==6.0.0
-    mSal-rozšíření==0,1,3
-    msal==1,1,0
-    msrest==0,6,11
-    msrestazure==0,6,3
-    ndg-httpsclient==0.5.1
-    nimbusml==1.6.1
-    numpy==1,18,2
-    oauthlib==3.1.0
-    pandy==0,25,3
-    pathspec==0,7,0
-    pip==20.0.2
-    portalocker==1.6.0
-    protobuf==3,11,3
-    pyarrow==0,16,0
-    pyasn1-moduly==0.2.8
-    pyasn1==0,4,8
-    pycparser==2,20
-    pycryptodomex==3.7.3
-    pyjwt==1,7,1
-    pyopenssl==19.1.0
-    pyparsing==2.4.6
-    pyrsistent==0,16,0
-    python-dateutil==2.8.1
-    pytz==2019.3
-    požadavky-oauthlib==1.3.0
-    požadavky==2.23.0
-    rsa==4,0
-    ruamel.yaml==0,15,89
-    s3transfer==0,3,3
-    scikit-learn==0,22,2
-    scipy==1,4,1
-    tajné skladování==3.1.2
-    instalační nástroje==46.1.1.post20200323
-    šest==1,14,0
-    smart-open==1.10.0
-    urllib3==1,25,8
-    websocket-client==0.57.0
-    werkzeug==0,16,1
-    kolo==0,34,2

 Chcete-li nainstalovat další balíčky, které nejsou v předinstalovaném seznamu, například *scikit-misc*, přidejte do skriptu následující kód: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="upload-files"></a>Nahrání souborů
**Spouštění pythonového skriptu** podporuje nahrávání souborů pomocí [sady Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#upload-file-name--path-or-stream-).

Následující příklad ukazuje, jak nahrát soubor obrázku v modulu **Spustit skript Pythonu:**

```Python

# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')

    from matplotlib import pyplot as plt
    plt.plot([1, 2, 3, 4])
    plt.ylabel('some numbers')
    img_file = "line.png"
    plt.savefig(img_file)

    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    run.upload_file(f"graphics/{img_file}", img_file)

    # Return value must be of a sequence of pandas.DataFrame
    # E.g.
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

Po dokončení spuštění potrubí můžete zobrazit náhled obrazu v pravém panelu modulu

> [!div class="mx-imgBorder"]
> ![Nahraný obrázek](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>Jak nakonfigurovat spuštění skriptu Pythonu

Modul **Execute Python Script** obsahuje ukázkový kód Pythonu, který můžete použít jako výchozí bod. Chcete-li nakonfigurovat modul **Spouštět skript Pythonu,** zadejte sadu vstupů a kód Pythonu, které se mají spustit v textovém poli **skriptu Pythonu.**

1. Přidejte do kanálu modul **Spouštět skript Pythonu.**

2. Přidejte a připojte **dataset1** všechny datové sady z návrháře, který chcete použít pro vstup. Odkazujte na tuto datovou sadu ve skriptu Pythonu jako **Na dataframe1**.

    Použití datové sady je volitelné, pokud chcete generovat data pomocí Pythonu, nebo použít kód Pythonu k importu dat přímo do modulu.

    Tento modul podporuje přidání druhé datové sady na **Dataset2**. Odkazujte na druhou datovou sadu ve skriptu Pythonu jako Na DataFrame2.

    Datové sady uložené v Azure Machine Learning se automaticky převedou na **pandas** data.frames při načtení s tímto modulem.

    ![Spustit vstupní mapu Pythonu](media/module/python-module.png)

4. Chcete-li zahrnout nové balíčky pythonu nebo kód, přidejte soubor zip obsahující tyto vlastní prostředky do **sady Script**. Vstup do **sady Skript** musí být zip soubor nahraný do pracovního prostoru jako datový soubor typu soubor. Datovou sadu můžete nahrát na stránce datové **sady** a můžete přetáhnout modul datové sady ze seznamu **Moje datové sady** ve stromu levého modulu na stránce vytváření návrháře. 

    Jakýkoli soubor obsažený v nahraném archivu zip lze použít při provádění kanálu. Pokud archiv obsahuje adresářovou strukturu, struktura je zachována, ale je nutné předřadit adresář s názvem **src** na cestu.

5. Do textového pole **skriptu Pythonu** zadejte nebo vložte platný skript Pythonu.

    Textové pole **skriptu Pythonu** je předem vyplněno některými pokyny v komentářích a ukázkovým kódem pro přístup k datům a výstup. Tento kód je nutné upravit nebo nahradit. Ujistěte se, že postupujte podle pythonských konvencí o odsazení a caseingu.

    + Skript musí obsahovat `azureml_main` funkci pojmenovanou jako vstupní bod pro tento modul.
    + Funkce vstupního bodu může obsahovat až `Param<dataframe1>` dva vstupní argumenty: a`Param<dataframe2>`
    + Zip soubory připojené k třetímu vstupnímu portu jsou `.\Script Bundle`rozbaleny a uloženy `sys.path`v adresáři , který je také přidán do Pythonu . 

    Pokud tedy soubor ZIP `mymodule.py`obsahuje , `import mymodule`importujte jej pomocí aplikace .

    + Dvě datové sady mohou být vráceny návrháři, `pandas.DataFrame`což musí být posloupnost typu . Můžete vytvořit další výstupy v kódu Pythonu a zapsat je přímo do úložiště Azure.

6. Odešlete kanál nebo vyberte modul a kliknutím na **Spustit vybraný** spustit pouze skript Pythonu.

    Všechna data a kód se načtou do virtuálního počítače a spustí se pomocí zadaného prostředí Pythonu.

## <a name="results"></a>Výsledky

Výsledky všech výpočtů prováděných vloženým kódem Pythonu musí být poskytnuty jako pandy. DataFrame, který se automaticky převede do formátu datové sady Azure Machine Learning, takže můžete použít výsledky s jinými moduly v kanálu.

Modul vrátí dvě datové sady:  
  
+ **Dataset výsledků 1**, definovaný prvním vráceným datovým rámcem pand ve skriptu Pythonu

+ **Datová sada výsledků 2**, definovaná druhým vráceným datovým rámcem pandas ve skriptu Pythonu


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 