---
title: Řešení chyb při instalaci knihovny
description: Tento kurz poskytuje přehled o řešení chyb při instalaci knihovny.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: 60ea97ea2df271f867febec3fa0f0826a18dbbbf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417173"
---
# <a name="troubleshoot-library-installation-errors"></a>Řešení chyb při instalaci knihovny 
Chcete-li zpřístupnit aplikaci třetí straně nebo místně sestavený kód, můžete nainstalovat knihovnu na jeden z Apache Spark fondů bez serveru. Balíčky uvedené v souboru requirements.txt se stáhnou z PyPi v době spuštění fondu. Tento soubor požadavků se používá při každém vytvoření instance Spark z tohoto fondu Spark. Jakmile je knihovna nainstalována pro fond Spark, je k dispozici pro všechny relace používající stejný fond. 

V některých případech se může stát, že se knihovna, kterou se pokoušíte nainstalovat, nezobrazuje ve vašem fondu Apache Spark. K tomuto případu dochází často v případě chyby v zadaném requirements.txt nebo v zadaných knihovnách. Pokud v procesu instalace knihovny dojde k chybě, fond Apache Spark se vrátí zpět na knihovny určené v modulu runtime Base synapse.

Cílem tohoto dokumentu je poskytnout běžné problémy a pomůžou vám ladit chyby při instalaci knihovny.

## <a name="force-update-your-apache-spark-pool"></a>Vynutit aktualizaci fondu Apache Spark
Když aktualizujete knihovny ve fondu Apache Spark, tyto změny se vybírají po restartování fondu. Pokud máte aktivní úlohy, budou tyto úlohy nadále běžet v původní verzi fondu Spark.

Můžete vynutit, aby se změny projevily, a to tak, že vyberete možnost pro **vynucení nových nastavení**. Toto nastavení ukončí všechny aktuální relace pro vybraný fond Spark. Po ukončení relace budete muset počkat na restartování fondu. 

![Přidat knihovny Pythonu](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Přidat knihovny Pythonu")

## <a name="validate-your-permissions"></a>Ověření oprávnění
Pokud chcete nainstalovat a aktualizovat knihovny, musíte mít oprávnění správce **dat objektu BLOB úložiště** nebo **vlastníka dat objektu BLOB úložiště** v primárním účtu úložiště Azure Data Lake Storage Gen2, který je propojený s pracovním prostorem Azure synapse Analytics.

Chcete-li ověřit, zda máte tato oprávnění, můžete spustit následující kód:

```python
from pyspark.sql.types import StructType,StructField, StringType, IntegerType
data2 = [("James","Smith","Joe","4355","M",3000),
    ("Michael","Rose","Edward","40288","F",4000)
  ]

schema = StructType([ \
    StructField("firstname",StringType(),True), \
    StructField("middlename",StringType(),True), \
    StructField("lastname",StringType(),True), \
    StructField("id", StringType(), True), \
    StructField("gender", StringType(), True), \
    StructField("salary", IntegerType(), True) \
  ])
 
df = spark.createDataFrame(data=data2,schema=schema)

df.write.csv("abfss://<<ENTER NAME OF FILE SYSTEM>>@<<ENTER NAME OF PRIMARY STORAGE ACCOUNT>>.dfs.core.windows.net/validate_permissions.csv")

```
Pokud se zobrazí chybová zpráva, pravděpodobně chybí požadovaná oprávnění. Další informace o tom, jak získat požadovaná oprávnění, najdete v tomto dokumentu: [přiřazení oprávnění Přispěvatel dat objektu BLOB úložiště nebo oprávnění vlastníka dat objektu BLOB úložiště](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-an-azure-built-in-role).

Kromě toho, pokud používáte kanál, musí mít MSI v pracovním prostoru také oprávnění přispěvatele dat objektů BLOB úložiště nebo přispěvatele dat objektu BLOB úložiště. Informace o tom, jak udělit vašemu pracovnímu prostoru oprávnění k tomuto oprávnění, najdete v tématu: [udělení oprávnění pro identitu spravovanou pracovním prostorem](../security/how-to-grant-workspace-managed-identity-permissions.md).

## <a name="check-the-requirements-file"></a>Ověření souboru požadavků
K upgradu virtuálního prostředí se dá použít soubor ***requirements.txt*** (výstup z příkazu k zablokování PIP). Tento soubor následuje po formátu popsaném v referenční dokumentaci k [zablokování PIP](https://pip.pypa.io/en/stable/reference/pip_freeze/) .

Je důležité si uvědomit následující omezení:
   -  Název balíčku PyPI musí být uveden společně s přesnou verzí. 
   -  Obsah souboru požadavků nesmí obsahovat nadbytečné prázdné řádky ani znaky. 
   -  [Modul runtime synapse](apache-spark-version-support.md) zahrnuje sadu knihoven, které jsou předinstalované na každý Apache Spark fond bez serveru. Balíčky, které jsou předinstalované do základního modulu runtime, se nedají downgradovat. Balíčky je možné přidat nebo upgradovat.
   -  Změna verze PySpark, Python, Scala/Java, .NET nebo Spark se nepodporuje.

Následující fragment kódu ukazuje požadovaný formát souboru požadavků.

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

## <a name="validate-wheel-files"></a>Ověřit soubory kol
Fondy Apache Spark serverů bez serveru jsou založené na distribuci systému Linux. Když stahujete a instalujete soubory kolečka přímo z PyPI, nezapomeňte vybrat verzi založenou na systému Linux a spustit ve stejné verzi Pythonu jako fond Spark.

>[!IMPORTANT]
>Mezi relacemi lze přidat nebo upravit vlastní balíčky. Budete ale muset počkat na restartování fondu a relace, aby se zobrazil aktualizovaný balíček.

## <a name="check-for-dependency-conflicts"></a>Vyhledat konflikty závislostí
 Obecně platí, že řešení závislosti mezi Pythony může být obtížné spravovat. Pro místní ladění konfliktů závislostí můžete vytvořit vlastní virtuální prostředí založené na synapse runtime a ověřit změny.

Opětovné vytvoření prostředí a ověření aktualizací:
 1. [Stáhněte](https://github.com/Azure-Samples/Synapse/blob/main/Spark/Python/base_environment.yml) šablonu pro místně znovu vytvořte modul runtime synapse. Mezi šablonou a skutečným prostředím synapse může dojít ke mírnému rozdílu.
   
 2. Vytvořte virtuální prostředí podle [následujících pokynů](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html). Toto prostředí umožňuje vytvořit izolovanou instalaci Pythonu se zadaným seznamem knihoven. 
    
    ```
    conda myenv create -f environment.yml
    conda activate myenv
    ```
   
 3. Použijte ``pip install -r <provide your req.txt file>`` k aktualizaci virtuálního prostředí se zadanými balíčky. Pokud při instalaci dojde k chybě, může dojít ke konfliktu mezi tím, co je předem nainstalované v synapse základního modulu runtime a co je zadáno v zadaném souboru požadavků. Aby bylo možné získat aktualizované knihovny na Apache Spark fond bez serveru, je nutné vyřešit tyto konflikty závislostí.

## <a name="next-steps"></a>Další kroky
- Zobrazení výchozích knihoven: [podpora Apache Spark verzí](apache-spark-version-support.md)

