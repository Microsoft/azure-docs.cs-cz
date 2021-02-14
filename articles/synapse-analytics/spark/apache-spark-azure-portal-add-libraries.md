---
title: Spravovat knihovny pro Apache Spark
description: Naučte se přidávat a spravovat knihovny používané Apache Spark ve službě Azure synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 0458fb8b140166b7bdf0fc0df41dbb207fdce3c9
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518517"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Správa knihoven pro Apache Spark ve službě Azure synapse Analytics

Knihovny poskytují opakovaně použitelný kód, který můžete chtít zahrnout do svých programů nebo projektů. Chcete-li zpřístupnit aplikaci třetí straně nebo místně sestavený kód, můžete nainstalovat knihovnu na jeden z Apache Spark fondů bez serveru. Jakmile je knihovna nainstalována pro fond Spark, je k dispozici pro všechny relace používající stejný fond. 

## <a name="before-you-begin"></a>Než začnete
- Pokud chcete nainstalovat a aktualizovat knihovny, musíte mít oprávnění správce **dat objektu BLOB úložiště** nebo **vlastníka dat objektů BLOB úložiště** v primárním účtu úložiště Gen2, který je propojený s pracovním prostorem Azure synapse Analytics.
  
## <a name="default-installation"></a>Výchozí instalace
Apache Spark ve službě Azure synapse Analytics má úplnou Anacondas instalaci a další knihovny. Seznam úplných knihoven najdete na stránce [podpora Apache Spark verzí](apache-spark-version-support.md). 

Po spuštění instance Spark budou automaticky zahrnuty tyto knihovny. Další Python a vlastní balíčky lze přidat na úrovni fondu Spark.


## <a name="manage-python-packages"></a>Spravovat balíčky Pythonu
Jakmile identifikujete knihovny, které chcete použít pro aplikaci Spark, můžete je nainstalovat do fondu Spark. 

 K upgradu virtuálního prostředí se dá použít soubor *requirements.txt* (výstup z `pip freeze` příkazu). Balíčky uvedené v tomto souboru pro instalaci nebo upgrade se stáhnou z PyPI v době spuštění fondu. Tento soubor požadavků se používá při každém vytvoření instance Spark z tohoto fondu Spark.

> [!IMPORTANT]
> - Pokud je balíček, který instalujete, velký nebo trvá jeho instalaci dlouhou dobu, bude to mít vliv na počáteční čas instance Spark.
> - Balíčky, které při instalaci vyžadují podporu kompilátoru, jako je GCC, se nepodporují.
> - Balíčky nemůžou být downgradované, jenom přidané nebo upgradované.
> - Změna verze PySpark, Python, Scala/Java, .NET nebo Spark se nepodporuje.
> - Instalace balíčků z PyPI není v pracovních prostorech povolených pro DEP podporována.


### <a name="requirements-format"></a>Formát požadavků

Následující fragment kódu ukazuje formát souboru požadavků. Název balíčku PyPi je uveden společně s přesnou verzí. Tento soubor se řídí formátem popsaným v referenční dokumentaci k [zablokování PIP](https://pip.pypa.io/en/stable/reference/pip_freeze/) . Tento příklad připnete konkrétní verzi. 

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

### <a name="install-python-packages"></a>Instalovat balíčky Pythonu
Při vývoji aplikace Spark můžete zjistit, že je potřeba aktualizovat existující nebo nainstalovat nové knihovny. Knihovny lze aktualizovat během nebo po vytvoření fondu.

> [!IMPORTANT]
> Pokud chcete nainstalovat knihovny, musíte mít oprávnění správce dat objektu BLOB úložiště nebo vlastníka dat objektů BLOB úložiště na primárním účtu úložiště Gen2 propojeném s pracovním prostorem synapse.

#### <a name="install-packages-during-pool-creation"></a>Instalovat balíčky během vytváření fondu
Instalace knihoven do fondu Spark během vytváření fondu:
   
1. Přejděte do pracovního prostoru Azure synapse Analytics z Azure Portal.
   
2. Vyberte **vytvořit fond Apache Spark** a pak vyberte kartu **Další nastavení** . 
   
3. Nahrajte konfigurační soubor prostředí pomocí voliče souborů v části **Packages (balíčky** ) na stránce. 
   
    ![Přidání knihoven Pythonu během vytváření fondu](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-add-library-python.png "Přidat knihovny Pythonu")
 

#### <a name="install-packages-from-the-synapse-workspace"></a>Instalace balíčků z pracovního prostoru synapse
Aktualizace nebo přidání dalších knihoven do fondu Spark z portálu Azure synapse Analytics:

1.  Přejděte do pracovního prostoru Azure synapse Analytics z Azure Portal.
   
2.  Spusťte pracovní prostor Azure synapse Analytics z Azure Portal.

3.  V hlavním navigačním panelu vyberte **Spravovat** a pak vyberte **fondy Apache Spark**.
   
4. Vyberte jeden fond Spark a nahrajte konfigurační soubor prostředí pomocí voliče souborů v části  **Packages (balíčky** ) stránky.

    ![Přidání knihoven Pythonu v synapse](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-update.png)
   
#### <a name="install-packages-from-the-azure-portal"></a>Instalovat balíčky z Azure Portal
Postup instalace knihovny do fondu Spark přímo z Azure Portal:
   
 1. Přejděte do pracovního prostoru Azure synapse Analytics z Azure Portal.
   
 2. V části **prostředky synapse** vyberte kartu **fondy Apache Spark** a v seznamu vyberte fond Spark.
   
 3. V části **Nastavení** ve fondu Spark vyberte **balíčky** . 

 4. Nahrajte konfigurační soubor prostředí pomocí voliče souborů.

    ![Snímek obrazovky, který zvýrazní tlačítko pro konfiguraci souboru pro nahrání prostředí.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Přidat knihovny Pythonu")

### <a name="verify-installed-libraries"></a>Ověřit nainstalované knihovny

Pokud chcete ověřit, jestli jsou nainstalované správné verze správných knihoven, spusťte následující kód.

```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
### <a name="update-python-packages"></a>Aktualizovat balíčky Pythonu
Balíčky je možné přidávat nebo upravovat kdykoli mezi relacemi. Nový konfigurační soubor balíčku přepíše existující balíčky a verze.  

Aktualizace nebo odinstalace knihovny:
1. Přejděte do pracovního prostoru Azure synapse Analytics. 

2. Pomocí Azure Portal nebo pracovního prostoru Azure synapse vyberte **fond Apache Spark** , který chcete aktualizovat.

3. Přejděte do části **Packages (balíčky** ) a nahrajte nový soubor konfigurace prostředí.
   
4. Po uložení změn bude nutné ukončit aktivní relace a nechat fond restartovat. Volitelně můžete aktivní relace vynutit tak, že zaškrtnutím políčka **vynutíte nová nastavení**.

    ![Přidat knihovny Pythonu](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Přidat knihovny Pythonu")
   

> [!IMPORTANT]
> Když vyberete možnost pro **vynucení nových nastavení**, ukončí se všechny aktuální relace pro vybraný fond Spark. Po ukončení relace budete muset počkat na restartování fondu. 
>
> Pokud je toto nastavení nezaškrtnuté, budete muset počkat, až aktuální relace Spark ukončí nebo zastaví ručně. Po ukončení relace budete muset povolit restart fondu. 


## <a name="manage-a-python-wheel"></a>Správa kolečka v Pythonu

### <a name="install-a-custom-wheel-file"></a>Nainstalovat vlastní soubor kolečka
Vlastní balíčky kol lze nainstalovat do fondu Apache Spark tím, že všechny soubory kol nahrajete do účtu Azure Data Lake Storage (Gen2), který je propojený s pracovním prostorem synapse. 

Soubory by měly být nahrány do následující cesty ve výchozím kontejneru účtu úložiště: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

Je možné, že budete muset přidat ```python``` složku do ```libraries``` složky, pokud ještě neexistuje.

>[!IMPORTANT]
>Vlastní balíčky je možné přidat nebo změnit mezi relacemi. Budete ale muset počkat na restartování fondu a relace, aby se zobrazil aktualizovaný balíček.

## <a name="next-steps"></a>Další kroky
- Zobrazení výchozích knihoven: [podpora Apache Spark verzí](apache-spark-version-support.md)
