---
title: Správa knihoven pro Apache Spark ve službě Azure synapse Analytics
description: Naučte se přidávat a spravovat knihovny používané Apache Spark ve službě Azure synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: euang
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: ad3231652056244fdfc814251e6caad025db77e5
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91649894"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Správa knihoven pro Apache Spark ve službě Azure synapse Analytics

Knihovny poskytují opakovaně použitelný kód, který můžete chtít zahrnout do svých programů nebo projektů. Pokud chcete vašim aplikacím zpřístupnit třetí stranu nebo místně sestavený kód, můžete knihovnu nainstalovat na jeden z vašich fondů Spark (Preview). Jakmile je knihovna nainstalována pro fond Spark, je k dispozici pro všechny relace používající stejný fond. 

## <a name="default-installation"></a>Výchozí instalace
Apache Spark ve službě Azure synapse Analytics má úplnou instalaci Anacondas a další knihovny. Seznam úplných knihoven najdete na stránce [podpora Apache Spark verzí](apache-spark-version-support.md). 

Po spuštění instance Spark budou automaticky zahrnuty tyto knihovny. Další Python a vlastní sestavené balíčky je možné přidat na úrovni fondu Spark (Preview).


## <a name="manage-python-packages"></a>Spravovat balíčky Pythonu
Jakmile identifikujete knihovny, které chcete použít pro aplikaci Spark, můžete je nainstalovat do fondu Spark (Preview). 

 K upgradu virtuálního prostředí se dá použít soubor *requirements.txt* (výstup z `pip freeze` příkazu). Balíčky uvedené v tomto souboru pro instalaci nebo upgrade se stáhnou z PyPi v době spuštění fondu. Tento soubor požadavků se používá při každém vytvoření instance Spark z tohoto fondu Spark.

> [!IMPORTANT]
> - Pokud je balíček, který instalujete, velký nebo trvá jeho instalaci dlouhou dobu, bude to mít vliv na počáteční čas instance Spark.
> - Balíčky, které vyžadují podporu kompilátoru v době instalace, jako je třeba RSZ, se nepodporují.
> - Balíčky nemůžou být downgradované, jenom přidané nebo upgradované.

### <a name="requirements-format"></a>Formát požadavků

Následující fragment kódu ukazuje formát souboru požadavků. Název balíčku PyPi je uveden společně s přesnou verzí. Tento soubor se řídí formátem popsaným v referenční dokumentaci k [zablokování PIP](https://pip.pypa.io/en/stable/reference/pip_freeze/) . Tento příklad připnete konkrétní verzi. 

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

### <a name="install-python-packages"></a>Instalovat balíčky Pythonu
Při vývoji aplikace Spark můžete zjistit, že je potřeba aktualizovat existující nebo nainstalovat nové knihovny. Knihovny lze aktualizovat během nebo po vytvoření fondu.

#### <a name="install-packages-during-pool-creation"></a>Instalovat balíčky během vytváření fondu
Instalace knihoven do fondu Spark (ve verzi Preview) během vytváření fondu:
   
1. Přejděte do pracovního prostoru Azure synapse Analytics z Azure Portal.
   
2. Vyberte **vytvořit fond Apache Spark** a pak vyberte kartu **Další nastavení** . 
   
3. Nahrajte konfigurační soubor prostředí pomocí voliče souborů v části **Packages (balíčky** ) na stránce. 
   
![Přidání knihoven Pythonu během vytváření fondu](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-add-library-python.png "Přidat knihovny Pythonu")
 

#### <a name="install-packages-from-the-synapse-workspace"></a>Instalace balíčků z pracovního prostoru synapse
Aktualizace nebo přidání dalších knihoven do fondu Spark (Preview) na portálu Azure synapse Analytics:

1.  Přejděte do pracovního prostoru Azure synapse Analytics z Azure Portal.
   
2.  Spusťte pracovní prostor Azure synapse Analytics z Azure Portal.

3.  V hlavním navigačním panelu vyberte **Spravovat** a pak vyberte **fondy Apache Spark**.
   
4. Vyberte jeden fond Spark a nahrajte konfigurační soubor prostředí pomocí voliče souborů v části  **Packages (balíčky** ) stránky.

![Přidání knihoven Pythonu v synapse](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-update.png "Přidat knihovny Pythonu")
   
#### <a name="install-packages-from-the-azure-portal"></a>Instalovat balíčky z Azure Portal
Postup instalace knihovny do fondu Spark (ve verzi Preview) přímo z Azure Portal:
   
 1. Přejděte do pracovního prostoru Azure synapse Analytics z Azure Portal.
   
 2. V části **prostředky synapse** vyberte kartu **fondy Apache Spark** a v seznamu vyberte fond Spark.
   
 3. V části **Nastavení** ve fondu Spark vyberte **balíčky** . 

 4. Nahrajte konfigurační soubor prostředí pomocí voliče souborů.

![Snímek obrazovky, který zvýrazní tlačítko pro konfiguraci souboru pro nahrání prostředí.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Přidat knihovny Pythonu")

### <a name="verify-installed-libraries"></a>Ověřit nainstalované knihovny

Pokud chcete ověřit, jestli jsou nainstalované správné verze správných knihoven, spusťte následující kód.

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```
### <a name="update-python-packages"></a>Aktualizovat balíčky Pythonu
Balíčky je možné přidávat nebo upravovat kdykoli mezi relacemi. Když se nahraje nový konfigurační soubor balíčku, přepíše se stávající balíčky a verze.  

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
Vlastní balíčky kol lze nainstalovat do fondu Apache Spark tím, že nahrajete všechny soubory kol do účtu Azure Data Lake Storage (Gen2), který je propojený s pracovním prostorem synapse. 

Soubory by měly být nahrány do následující cesty ve výchozím kontejneru účtu úložiště: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>sparkpools/<pool_name>libraries/python/
```

>[!IMPORTANT]
>Mezi relacemi lze přidat nebo upravit vlastní balíčky. Budete ale muset počkat na restartování fondu a relace, aby se zobrazil aktualizovaný balíček.

## <a name="next-steps"></a>Další kroky
- Zobrazení výchozích knihoven: [podpora Apache Spark verzí](apache-spark-version-support.md)
