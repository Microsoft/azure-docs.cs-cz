---
title: "Čtení a zápis velkých objemů dat souborů | Microsoft Docs"
description: "Čtení a zápis velkých souborů ve experimenty Azure Machine Learning."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/10/2017
ms.openlocfilehash: 099ff69b396c35730471d684b59115f03ccf67d9
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="persisting-changes-and-working-with-large-files"></a>Uložením změn a práce s velkými soubory
Ve službě Azure Machine Learning experimentování můžete nakonfigurovat různé provádění cílů. Některé cíle jsou místní, například v místním počítači nebo kontejner Docker v místním počítači. Ostatní jsou vzdálené, jako je například kontejner Docker na vzdáleném počítači nebo clusteru služby HDInsight. Další informace najdete v tématu [přehled Azure Machine Learning experimentovat služba provedení](experimentation-service-configuration.md). 

Předtím, než můžete spustit na cíl, musíte zkopírovat složce projektu výpočetní cíl. Je potřeba udělat, tak i s místní spuštění, který používá místní dočasnou složku pro tento účel. 

## <a name="execution-isolation-portability-and-reproducibility"></a>Provádění izolace, přenositelnost a reprodukovatelnost
Účelem tohoto návrhu je zajištění izolace, reprodukovatelnosti a přenositelnost spuštění. Pokud dvakrát spustit stejný skriptu na stejném nebo jiném výpočetní cíl, obdržíte stejné výsledky. Změny provedené při prvním spuštění by neměl určovat nastavení v druhé provádění. V tomto návrhu může výpočetní cíle považovat bezstavové výpočetních prostředků, každý s bez přidružení pro úlohy, které jsou spouštěny po ukončení práce.

## <a name="challenges"></a>Problémy
I když tento návrh poskytuje výhody přenositelnosti a opakovatelnosti, přináší také některé jedinečné výzvy.

### <a name="persisting-state-changes"></a>Zachování změny stavu
Pokud váš skript upravit stav kontext výpočetní, nejsou pro vaše další provádění trvalé změny a jejich nebudou rozšířeny zpět na klientský počítač automaticky. 

Přesněji řečeno pokud váš skript vytvoří podsložky nebo zapisuje do souboru, složky nebo souboru nebude existovat v adresáři projektu po spuštění. Soubory jsou uloženy v dočasnou složku v cílovém prostředí výpočty. Můžete je použít pro účely ladění, ale nelze spoléhat na jejich trvalé existence.

### <a name="working-with-large-files-in-the-project-folder"></a>Práce s velkými soubory ve složce projektu

Pokud složky projektu obsahuje velké soubory, když složce je zkopírován do cílové výpočetní prostředí na začátku provádění zpoplatněná latence. I v případě, že k provádění dojde místně, je stále nepotřebné disku provoz předejdete. Z tohoto důvodu jsme aktuálně cap projektu maximální velikost v 25 MB.

## <a name="option-1-use-the-outputs-folder"></a>Možnost 1: Použití *výstupy* složky
Tato možnost je vhodnější, pokud platí následující podmínky:
* Váš skript vytvoří soubory.
* Očekáváte soubory a změnit s každou experimentu.
* Chcete zachovat historii těchto souborů. 

Běžné případy použití jsou:
* Cvičení modelu
* Vytvoření datové sady
* Vykreslení graf jako soubor bitové kopie v rámci vašeho modelu školení provádění 

Kromě toho chcete porovnat výstupy napříč běží, vyberte výstupního souboru (například model), která byla vytvořena pomocí předchozí spustit a následně použít pro následné úlohy (například vyhodnocování).

Lze zapisovat soubory do složky s názvem *výstupy* , je relativní vzhledem ke kořenovému adresáři. Složka obdrží zvláštní zacházení službou experimenty. Nic váš skript vytvoří ve složce během provádění, například souboru modelu, datový soubor nebo soubor možná vykreslená bitové kopie (souhrnně známé jako _artefakty_), se zkopíruje do účtu Azure Blob storage, který je spojen s vaší Po dokončení spuštění experimentování účet. Soubory se stanou součástí vaší záznam historie spouštění.

Zde je stručný příklad kódu pro ukládání model v *výstupy* složky:
```python
import os
import pickle

# m is a scikit-learn model. 
# we serialize it into a mode.plk file under the ./outputs folder.
with open(os.path.join('.', 'outputs', 'model.pkl'), 'wb') as f:    
    pickle.dump(m, f)
```
Si můžete stáhnout všechny artefaktů procházením **výstupní soubory** část na stránku podrobností spuštění konkrétní spustit v Azure Machine Learning Workbench. Jednoduše vyberte spustit a pak vyberte **Stáhnout** tlačítko. Alternativně můžete zadat `az ml asset download` příkazu v okně rozhraní příkazového řádku (CLI).

Více kompletní příklad, najdete v článku `iris_sklearn.py` Python script v _klasifikace Iris_ ukázkový projekt.

## <a name="option-2-use-the-shared-folder"></a>Možnost 2: Použití sdílené složky
Pokud nepotřebujete zachovat historie souborů každé spuštění, může být sdílená složka, která je přístupná přes nezávislé spustí pomocí skvělou možnost pro následující scénáře: 
- Skript musí načtení dat z místních souborů, jako jsou soubory .csv nebo adresář souborů text či image, jako školení nebo testovací data. 
- Váš skript zpracovává nezpracovaná data a zapíše se mezilehlých výsledků, jako je například featurized Cvičná data z textu nebo bitové kopie souborů, které se používají v následných školení spustit. 
- Váš skript průměrů modelu a následné vyhodnocování skript musí vyzvednutí modelu a použít jej pro vyhodnocení. 

Je důležité si uvědomit, místně žije sdílenou složku na cílovém zvolené výpočetní. Proto tato možnost funguje jenom v případě, že všechny vaše skript se spustí odkazující na tuto sdílenou složku jsou spouštěny ve stejném výpočetní cíl a cíl výpočetní není recykluje mezi spustí.

Využitím funkce sdílené složky, může číst nebo zapisovat do speciální složky, která je identifikovaná proměnná prostředí `AZUREML_NATIVE_SHARE_DIRECTORY`. 

### <a name="example"></a>Příklad:
Tady je ukázkový kód Python pro čtení a zápis do textového souboru pomocí tuto sdílenou složku:
```python
import os

# write to the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'wb') as f:
    f.write(“Hello World”)

# read from the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'r') as f:
    text = file.read()
```

Více kompletní příklad najdete v tématu *iris_sklearn_shared_folder.py* v soubor _klasifikace Iris_ ukázkový projekt.

Před použitím této funkce budete muset nastavit *.compute* některé jednoduché konfigurace, které představují kontextu cílové spuštění v souboru *aml_config* složky. Skutečné cesty ke složce se může lišit v závislosti na cílových výpočetní zvolíte a hodnota, kterou konfigurujete.

### <a name="configure-local-compute-context"></a>Nakonfigurujte místní výpočetní kontextu

Chcete-li povolit tuto funkci v kontextu místního výpočetní, jednoduše přidat do vaší *.compute* souboru následující řádek, který představuje _místní_ prostředí (obvykle s názvem *local.compute*).
```
# local.runconfig
...
nativeSharedDirectory: ~/.azureml/share
...
```

Cesta ~/.azureml/share cesta je výchozí základní složky. Můžete ho změnit na jakékoli místní cestou absolutní, který je přístupný pro skript spustit. Název účtu experimentování, název pracovního prostoru a název projektu se automaticky připojí název základního adresáře, který se stane úplná cesta sdíleného adresáře. Například soubory lze zapisovat do (a načítat z) následující cestu, pokud používáte předchozí výchozí hodnota:

```
# on Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# on macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

### <a name="configure-the-docker-compute-context-local-or-remote"></a>Konfigurovat kontext Docker výpočetní (místní nebo vzdálené)
Chcete-li povolit tuto funkci v kontextu výpočetní Docker, musíte přidat následující dva řádky do vaší místní nebo vzdálené Docker *.compute* souboru.

```
# docker.compute
...
sharedVolumes: true
nativeSharedDirectory: ~/.azureml/share
...
```
>[!IMPORTANT]
>**SharedVolumes** musí být nastavena na *true* při použití `AZUREML_NATIVE_SHARE_DIRECTORY` proměnnou prostředí k přístupu ke sdílené složce. Provádění, jinak selže.

Kód spuštěný ve kontejner Docker vždy zobrazí tuto sdílenou složku jako */azureml-share /*. Cesta ke složce, jak je vidět ve kontejner Docker se nedá konfigurovat. Nepoužívejte název této složky ve vašem kódu. Místo toho použijte název proměnné prostředí vždy `AZUREML_NATIVE_SHARE_DIRECTORY` odkazovat na tuto složku. Je mapován na místní složku na hostiteli Docker počítač nebo výpočetní kontextu. Základní adresář této místní složky je nastavitelná hodnota `nativeSharedDirectory` nastavení v *.compute* souboru. Místní cesta sdílené složky na hostitelském počítači, pokud použijete výchozí hodnota je následující:
```
# Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/

# Ubuntu Linux
/home/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

>[!NOTE]
>Cesta sdílené složky na místním disku je stejný, ať už místní výpočetní kontext nebo lokální kontext výpočetní Docker. To znamená, že můžete sdílet soubory mezi místním spuštění a spuštění místní Docker.

Můžete umístit vstupní data přímo v uvedených složkách a očekávají, že místní nebo Docker spustí na počítači můžete vyzvedávat ho. Můžete také zapisovat soubory do této složky z vaší místní nebo Docker spustí a očekávají, že soubory získat uchovávané v této složce zbývajících provádění životního cyklu.

Další informace najdete v tématu [Azure Machine Learning Workbench provádění konfigurační soubory](experimentation-service-configuration-reference.md).

>[!NOTE]
>`AZUREML_NATIVE_SHARE_DIRECTORY` Proměnnou prostředí není podporován v kontextu výpočetní HDInsight. Je však snadno dosažení stejného výsledku explicitně pomocí absolutní cestu úložiště objektů Blob v Azure číst a zapisovat do úložiště objektů blob připojené.

## <a name="option-3-use-external-durable-storage"></a>Možnost 3: Použití externího odolná úložiště

Externí odolná úložiště můžete použít k uchování stavu během provádění. Tato možnost je užitečná v následujících scénářích:
- Vstupní data je již uložen v odolná úložiště, který je přístupný z cílového výpočetním prostředí.
- Soubory nemusí být součástí záznamy historie spouštění.
- Soubory musí být ve spuštěních sdíleny různých výpočetních prostředí.
- Soubory musí být schopen zůstanou platné i po jeho výpočetní kontextu.

Jedním z těchto způsobů je použití Azure Blob storage z Pythonu nebo PySpark kódu. Zde je stručný příklad:

```python
from azure.storage.blob import BlockBlobService
import glob
import os

ACCOUNT_NAME = "<your blob storage account name>"
ACCOUNT_KEY = "<account key>"
CONTAINER_NAME = "<container name>"

blob_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

## Create a new container if necessary, or use an existing one
my_service.create_container(CONTAINER_NAME, fail_on_exist=False, public_access=PublicAccess.Container)

# df is a pandas DataFrame
df.to_csv('mydata.csv', sep='\t', index=False)

# Export the mydata.csv file to Blob storage.
for name in glob.iglob('mydata.csv'):
    blob_service.create_blob_from_path(CONTAINER_NAME, 'single_file.csv', name)
```

Tady je příklad Krátký pro připojení k modulu runtime HDI Spark jakékoli libovolný úložiště objektů Blob v Azure:
```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)
 
attach_storage_container(spark, "<storage account name>", "<storage key>”)
```

## <a name="conclusion"></a>Závěr
Vzhledem k tomu, že Azure Machine Learning spustí skripty tak, že zkopírujete složku celý projekt k cíli výpočetní kontextu, věnujte zvláštní pozornost s velké vstupní, výstupní a zprostředkující soubory. Pro transakce velkých souborů, můžete použít složku speciální výstupy, sdílené složky, která je přístupná prostřednictvím `AZUREML_NATIVE_SHARE_DIRECTORY` proměnné prostředí, nebo externí odolné úložiště. 

## <a name="next-steps"></a>Další postup
- Zkontrolujte [Azure Machine Learning Workbench provádění konfigurační soubory](experimentation-service-configuration-reference.md) článku.
- V tématu Jak [klasifikace Iris](tutorial-classifying-iris-part-1.md) kurz projektu používá složce výstupy k zachování modulu trained model.
