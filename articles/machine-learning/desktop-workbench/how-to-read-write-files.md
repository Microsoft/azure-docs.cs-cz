---
title: Čtení a zapisování velkých datových souborů | Dokumentace Microsoftu
description: Čtení a zápis velkých souborů v experimenty Azure Machine Learning.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/10/2017
ms.openlocfilehash: 5a772f8792c02139e45977e207b5be4bebc63a9c
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37908235"
---
# <a name="persisting-changes-and-working-with-large-files"></a>Uložením změn prostředků a práci s velkými soubory
Pomocí služby experimentování ve službě Azure Machine Learning můžete nakonfigurovat různé cíle spuštění. Některé cíle jsou místní, jako je například v místním počítači nebo kontejneru Docker v místním počítači. Ostatní jsou vzdálené, jako je například kontejneru Docker ve vzdáleném počítači nebo clusteru služby HDInsight. Další informace najdete v tématu [přehled Azure Machine Learning vyzkoušet spuštění služby](experimentation-service-configuration.md). 

Předtím, než můžete spustit na cíli, je třeba zkopírovat složku projektu do cílového výpočetního prostředí. Je potřeba udělat, tak i s místní spuštění, který používá místní dočasnou složku pro tento účel. 

## <a name="execution-isolation-portability-and-reproducibility"></a>Spuštění izolace, přenositelnost a reprodukovatelnosti problémových
Účelem tohoto návrhu je pro zajištění izolace, reprodukovatelnost a přenositelnost spuštění. Pokud stejný skript spustíte dvakrát, na stejném nebo jiném cílové výpočetní prostředí, se zobrazí stejné výsledky. Změny provedené při prvním spuštění by neměl mít vliv na ve spuštění druhého. S tímto návrhem lze považovat cílových výpočetních prostředí jako bezstavové výpočetní prostředky, mají spřažení s úlohami, které jsou spouštěny po jejich dokončení.

## <a name="challenges"></a>Výzvy
Přestože tento návrh přináší výhody přenositelnosti a opakovatelnost, přináší také některé jedinečné výzvy.

### <a name="persisting-state-changes"></a>Zachování změn stavu
Pokud váš skript změní stav výpočetním kontextu, nejsou trvalé změny pro další spuštění a jejich nejsou šířeny zpět do klientského počítače automaticky. 

Přesněji řečeno pokud váš skript vytvoří podsložky nebo zapisuje do souboru, složky nebo souboru nebudou k dispozici v adresáři projektu po spuštění. Soubory se ukládají do dočasné složky v cílovém prostředí compute. Můžete je použít pro účely ladění, ale nelze spoléhat na jejich trvalé existence.

### <a name="working-with-large-files-in-the-project-folder"></a>Práce s velkými soubory ve složce projektu

Pokud složky vašeho projektu obsahuje velké soubory, vznikne vám latence při složce je zkopírován do cílové výpočetní prostředí na začátku provádění. I v případě provedení dochází místně, je stále zbytečné diskové přenosy, aby se zabránilo. Z tohoto důvodu sledujeme aktuálně projektu maximální velikost na 25 MB.

## <a name="option-1-use-the-outputs-folder"></a>Možnost 1: Použití *výstupy* složky
Tato možnost je vhodnější, pokud platí následující podmínky:
* Váš skript vytvoří soubory.
* Očekáváte, že soubory, které chcete změnit s každou experimentu.
* Chcete zachovat historii těchto souborů. 

Běžné případy použití jsou:
* Trénování modelu
* Vytvoření datové sady
* Vykreslení grafu jako soubor obrázku jako součást provádění trénování modelu 

Kromě toho chcete porovnat výstupy během spuštění, vyberte výstupního souboru (například model), který byl vytvořen předchozí spustit a použít jej pro následující úlohy (například vyhodnocování).

Lze zapisovat soubory do složky s názvem *výstupy* , která je relativní vzhledem k kořenový adresář. Složka obdrží zvláštní zacházení pomocí služby experimentování ve službě. Nic váš skript vytvoří ve složce během provádění, jako je například soubor modelu, datový soubor nebo soubor možná vykreslená bitové kopie (souhrnně označované jako _artefakty_), je zkopírován do účtu služby Azure Blob storage, který je spojen s vaší Po dokončení spuštění se experimentální účet. Soubory se stanou součástí záznamu historie spuštění.

Zde je příklad krátkého kódu pro ukládání modelu *výstupy* složky:
```python
import os
import pickle

# m is a scikit-learn model. 
# we serialize it into a mode.plk file under the ./outputs folder.
with open(os.path.join('.', 'outputs', 'model.pkl'), 'wb') as f:    
    pickle.dump(m, f)
```
Si můžete stáhnout tak, že přejdete do všech artefaktů **výstupní soubory** část na stránku podrobné konkrétní spustit v Azure Machine Learning Workbench. Jednoduše vyberte spustit a pak vyberte **Stáhnout** tlačítko. Alternativně můžete zadat `az ml asset download` příkazu v okně rozhraní příkazového řádku (CLI).

Kompletní příklad, najdete v článku `iris_sklearn.py` Pythonového skriptu v _klasifikace Iris_ ukázkového projektu.

## <a name="option-2-use-the-shared-folder"></a>Možnost 2: Použití sdílené složky
Pokud není nutné udržovat historii každé spuštění souborů, použití sdílené složky, který je přístupný přes nezávislé spuštění může být skvělou možností pro následující scénáře: 
- Skript je potřeba načítat data z místních souborů, například soubory CSV nebo adresář souborů text či image jako školení nebo testovací data. 
- Váš skript zpracovává nezpracovaných dat a zapíše mezilehlé výsledky, jako je například natrénuje trénovacích dat ze souborů text či image, které se používají v následných školení spustit. 
- Váš skript průměrů modelu a následné hodnoticího skriptu musí vyzvednutí modelu a použít pro hodnocení. 

Je důležité si uvědomit, že sdílené složka se nachází místně na vaše cílové výpočetní prostředí zvolené. Proto tato možnost funguje pouze v případě, že je všechno, co váš skript se spustí, které odkazují na tuto sdílenou složku, které jsou spouštěny ve stejném cílové výpočetní prostředí a cílové výpočetní prostředí není recyklován mezi spuštění.

S využitím funkce sdílené složky, může číst nebo zapisovat do složky, která je identifikovaná proměnnou prostředí `AZUREML_NATIVE_SHARE_DIRECTORY`. 

### <a name="example"></a>Příklad:
Tady je ukázkový kód Pythonu pro použití této sdílené složky pro čtení a zápis do textového souboru:
```python
import os

# write to the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', "w") as f1:
    f1.write(“Hello World”)

# read from the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', "r") as f2:
    text = f2.read()
```

Podrobnější příklad naleznete v tématu *iris_sklearn_shared_folder.py* soubor _klasifikace Iris_ ukázkového projektu.

Tuto funkci mohli používat, je nutné nastavit *.compute* některé jednoduché konfigurace, které představují kontextu cílové spuštění v souboru *aml_config* složky. Skutečné cesty ke složce se může lišit v závislosti na cílové výpočetní prostředí, které zvolíte a hodnota, kterou nakonfigurujete.

### <a name="configure-local-compute-context"></a>Konfigurovat místní výpočetní kontext

Pokud chcete povolit tuto funkci v místním výpočetním kontextu, jednoduše přidejte do vašeho *.compute* souboru následující řádek, který představuje _místní_ prostředí (obvykle s názvem *local.compute*).
```
# local.runconfig
...
nativeSharedDirectory: ~/.azureml/share
...
```

~/.Azureml/share cesta je výchozí cesta základní složky. Můžete ho změnit na místní absolutní cestu, který je přístupný pro skript spustit. Název experimentálního účtu, název pracovního prostoru a název projektu jsou automaticky připojeným k názvu základního adresáře, který bude úplná cesta sdíleného adresáře. Například soubory můžete zapsána do (a získaných) následující cestu, pokud používáte předchozí výchozí hodnota:

```
# on Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# on macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

### <a name="configure-the-docker-compute-context-local-or-remote"></a>Konfigurace Dockeru výpočetním kontextu (místní nebo vzdálené)
Pokud chcete povolit tuto funkci na výpočetním kontextu Dockeru, musíte přidat následující dva řádky do místního nebo vzdáleného Docker *.compute* souboru.

```
# docker.compute
...
sharedVolumes: true
nativeSharedDirectory: ~/.azureml/share
...
```
>[!IMPORTANT]
>**SharedVolumes** musí být vlastnost nastavena na *true* při použití `AZUREML_NATIVE_SHARE_DIRECTORY` proměnné prostředí pro přístup ke sdílené složce. V opačném případě se nezdaří spuštění.

Kód v kontejneru Dockeru běží vždy uvidí tuto sdílenou složku jako */azureml-share /*. Cesta ke složce, jak je vidět v kontejneru Dockeru, není konfigurovatelné. Nepoužívejte název této složky ve vašem kódu. Místo toho použijte název proměnné prostředí vždy `AZUREML_NATIVE_SHARE_DIRECTORY` odkazovat na tuto složku. Je mapován na místní složky v hostitele Docker machine nebo výpočetního kontextu. Základní adresář tuto místní složku je konfigurovatelná hodnota `nativeSharedDirectory` nastavení *.compute* souboru. Místní cesta sdílenou složku na hostitelském počítači, pokud nechcete použít výchozí hodnotu, je následující:
```
# Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/

# Ubuntu Linux
/home/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

>[!NOTE]
>Cestu sdílené složce na místním disku, je stejný, ať už jde o místní výpočetní kontext nebo místní výpočetní kontext Dockeru. To znamená, že můžou i sdílet soubory mezi místním spuštění a místní spuštění Docker.

Můžete umístit vstupních dat přímo v uvedených složkách a očekávají, že vaše místní nebo spuštění Dockeru na počítači můžete ji vybrat. Můžete také zápisu do této složky souborů z místního počítače nebo spuštění Docker a očekávají, že v této složce získat trvalé soubory přežití životní cyklus spuštění.

Další informace najdete v tématu [Azure Machine Learning Workbench spouštění konfiguračních souborů](experimentation-service-configuration-reference.md).

>[!NOTE]
>`AZUREML_NATIVE_SHARE_DIRECTORY` Proměnná prostředí není podporován ve výpočetním kontextu HDInsight. Je však snadné k dosažení stejného výsledku explicitně pomocí absolutní cestu úložiště objektů Blob v Azure pro čtení a zápis do úložiště objektů blob v připojené.

## <a name="option-3-use-external-durable-storage"></a>Možnost 3: Použití externí odolné úložiště

Externí odolné úložiště můžete použít k uchování stavu během provádění. Tato možnost je užitečná v následujících scénářích:
- Vstupní data již uložena do odolného úložiště, který je přístupný z cílového výpočetního prostředí.
- Soubory nemusí být součástí záznamy historie spuštění.
- Soubory musí být sdílí spuštění v různých výpočetních prostředích.
- Soubory musí být možné nezbytné k překonání výpočetní kontext.

Jedním z těchto přístupů je použít úložiště objektů Blob v Azure z vašeho kódu Python nebo PySpark. Tady je krátké příklad:

```python
from azure.storage.blob import BlockBlobService
from azure.storage.blob.models import PublicAccess
import glob
import os

ACCOUNT_NAME = "<your blob storage account name>"
ACCOUNT_KEY = "<account key>"
CONTAINER_NAME = "<container name>"

blob_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

## Create a new container if necessary, or use an existing one
blob_service.create_container(CONTAINER_NAME, fail_on_exist=False, public_access=PublicAccess.Container)

# df is a pandas DataFrame
df.to_csv('mydata.csv', sep='\t', index=False)

# Export the mydata.csv file to Blob storage.
for name in glob.iglob('mydata.csv'):
    blob_service.create_blob_from_path(CONTAINER_NAME, 'single_file.csv', name)
```

Zde je příklad krátká pro připojení k prostředí runtime HDI Spark jakékoli libovolné úložiště objektů Blob v Azure:
```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)
 
attach_storage_container(spark, "<storage account name>", "<storage key>”)
```

## <a name="conclusion"></a>Závěr
Azure Machine Learning spouští skripty zkopírováním celé složky projektu do cílového výpočetního kontextu, věnujte zvláštní pozornost s velké vstupní, výstupní a zprostředkující soubory. Pro transakce velké soubory, můžete použít speciální výstupy složku sdílenou složku, která je přístupná prostřednictvím `AZUREML_NATIVE_SHARE_DIRECTORY` proměnné prostředí, nebo externí odolné úložiště. 

## <a name="next-steps"></a>Další postup
- Zkontrolujte [Azure Machine Learning Workbench spouštění konfiguračních souborů](experimentation-service-configuration-reference.md) článku.
- V tématu Jak [klasifikace Iris](tutorial-classifying-iris-part-1.md) kurz projekt používá k uchování trénovaného modelu složce výstupy.
