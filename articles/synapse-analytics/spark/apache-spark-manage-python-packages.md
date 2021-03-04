---
title: Správa knihoven Pythonu pro Apache Spark
description: Naučte se přidávat a spravovat knihovny Pythonu používané Apache Spark ve službě Azure synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 4bb323e0e8f72456b6a522ede9a98d193e1c3c7e
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098770"
---
# <a name="manage-python-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Správa knihoven Pythonu pro Apache Spark ve službě Azure synapse Analytics

Knihovny poskytují opakovaně použitelný kód, který můžete chtít zahrnout do svých programů nebo projektů. 

Pro různé důvody možná budete muset aktualizovat prostředí Apache Spark fondů bez serveru. Můžete například zjistit, že:
- jedna z vašich základních závislostí právě vydala novou verzi.
- potřebujete další balíček pro školení modelu Machine Learning nebo přípravu dat.
- našli jste lepší balíček a už nepotřebujete starší balíček.

Chcete-li zpřístupnit aplikaci třetí straně nebo místně sestavený kód, můžete nainstalovat knihovnu do jednoho z Apache Spark fondů a poznámkových bloků bez serveru. V tomto článku se dozvíte, jak můžete spravovat knihovny Pythonu v rámci Apache Sparkho fondu bez serveru.

## <a name="default-installation"></a>Výchozí instalace
Apache Spark ve službě Azure synapse Analytics má úplnou sadu knihoven pro běžné datové inženýry, přípravu dat, strojové učení a vizualizace dat. Seznam úplných knihoven najdete na stránce [podpora Apache Spark verzí](apache-spark-version-support.md). 

Po spuštění instance Spark budou automaticky zahrnuty tyto knihovny. Další Python a vlastní balíčky lze přidat do fondu a úrovně relace Spark.

## <a name="pool-libraries"></a>Knihovny fondů
Jakmile identifikujete knihovny Pythonu, které byste chtěli použít pro aplikaci Spark, můžete je nainstalovat do fondu Spark. Knihovny na úrovni fondu jsou dostupné všem poznámkovým blokům a úlohám, které jsou ve fondu spuštěné.

Existují dva základní způsoby, jak nainstalovat knihovnu v clusteru:
-  Nainstalujte knihovnu pracovního prostoru, která se nahrála jako balíček pracovního prostoru.
-  Poskytněte specifikaci prostředí *requirements.txt* nebo *conda prostředí. yml* pro instalaci balíčků z úložišť, jako je PyPi, conda-zfalšovat a další.

> [!IMPORTANT]
> - Pokud je balíček, který instalujete, velký nebo trvá jeho instalaci dlouhou dobu, bude to mít vliv na počáteční čas instance Spark.
> - Změna verze PySpark, Python, Scala/Java, .NET nebo Spark se nepodporuje.
> - Instalace balíčků z externích úložišť jako PyPI, conda-zfalšovat nebo výchozích kanálů conda není v pracovních prostorech s povoleným programem DEP podporovaná.

### <a name="install-python-packages"></a>Instalovat balíčky Pythonu
Balíčky Pythonu se dají nainstalovat z úložišť jako PyPI a Conda-Forge poskytnutím souboru specifikace prostředí. 

#### <a name="environment-specification-formats"></a>Formáty specifikace prostředí

##### <a name="pip-requirementstxt"></a>requirements.txt PIP
K upgradu prostředí se dá použít soubor *requirements.txt* (výstup z `pip freeze` příkazu). Když se fond aktualizuje, balíčky uvedené v tomto souboru se stáhnou z PyPI. Úplné závislosti jsou následně uloženy do mezipaměti a uloženy pro pozdější opakované použití fondu. 

Následující fragment kódu ukazuje formát souboru požadavků. Název balíčku PyPI je uveden společně s přesnou verzí. Tento soubor se řídí formátem popsaným v referenční dokumentaci k [zablokování PIP](https://pip.pypa.io/en/stable/reference/pip_freeze/) . 

Tento příklad připnete konkrétní verzi. 
```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```
##### <a name="yml-format-preview"></a>Formát YML (Preview)
Kromě toho můžete také poskytnout soubor *prostředí. yml* pro aktualizaci prostředí fondu. Balíčky uvedené v tomto souboru se stáhnou z výchozích kanálů Conda, conda-zfalšovat a PyPI. Pomocí možností konfigurace můžete zadat jiné kanály nebo odebrat výchozí kanály.

Tento příklad určuje kanály a závislosti conda/PyPI. 

```
name: stats2
channels:
  - defaults
dependencies:
  - bokeh=0.9.2
  - numpy=1.9.*
  - flask
  - pip:
    - matplotlib
```
Podrobnosti o vytvoření prostředí z tohoto souboru prostředí. yml najdete v tématu [vytvoření prostředí ze souboru prostředí. yml](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html#creating-an-environment-file-manually).

#### <a name="update-python-packages"></a>Aktualizovat balíčky Pythonu
Jakmile identifikujete soubor specifikace prostředí nebo sadu knihoven, které chcete nainstalovat do fondu Spark, můžete aktualizovat knihovny fondů Spark tak, že přejdete na Azure synapse Studio nebo Azure Portal. Tady můžete zadat specifikaci prostředí a vybrat knihovny pracovních prostorů k instalaci. 

Jakmile se změny uloží, úloha Spark spustí instalaci a uloží výsledné prostředí do mezipaměti pro pozdější použití. Po dokončení úlohy budou nové úlohy Sparku nebo relace poznámkového bloku používat aktualizované knihovny fondu. 

##### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Správa balíčků z Azure synapse studia nebo Azure Portal
Knihovny fondů Spark je možné spravovat buď z Azure synapse studia, nebo Azure Portal. 

Aktualizace nebo přidání knihoven do fondu Spark:
1. Přejděte do pracovního prostoru Azure synapse Analytics z Azure Portal.

    Pokud aktualizujete z **Azure Portal**:

    - V části **prostředky synapse** vyberte kartu **fondy Apache Spark** a v seznamu vyberte fond Spark.
     
    - V části **Nastavení** ve fondu Spark vyberte **balíčky** .
  
    ![Snímek obrazovky, který zvýrazní tlačítko pro nastavení konfiguračního souboru prostředí pro odeslání.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Přidat knihovny Pythonu")
   
    Pokud aktualizujete z nástroje **synapse Studio**:
    - V hlavním navigačním panelu vyberte **Spravovat** a pak vyberte **fondy Apache Spark**.

    - Vyberte oddíl **balíčky** pro konkrétní fond Spark.
    ![Snímek obrazovky, který zvýrazní možnost konfigurace prostředí v studiu.](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "Přidat knihovny Pythonu z studia")
   
2. Nahrajte konfigurační soubor prostředí pomocí voliče souborů v části  **Packages (balíčky** ) na stránce.
3. Můžete také vybrat další **balíčky pracovních prostorů** , které chcete přidat do fondu. 
4. Po uložení změn se aktivuje systémová úloha pro instalaci a ukládání do mezipaměti určených knihoven. Tento proces pomáhá snižovat celkovou dobu spuštění relace. 
5. Po úspěšném dokončení úlohy budou všechny nové relace vyzradit aktualizované knihovny fondu.

> [!IMPORTANT]
> Když vyberete možnost pro **vynucení nových nastavení**, ukončí se všechny aktuální relace pro vybraný fond Spark. Po ukončení relace budete muset počkat na restartování fondu. 
>
> Pokud je toto nastavení nezaškrtnuté, budete muset počkat, až aktuální relace Spark ukončí nebo zastaví ručně. Po ukončení relace budete muset povolit restart fondu.


##### <a name="track-installation-progress-preview"></a>Sledovat průběh instalace (Preview)
Systémová rezervovaná úloha Spark se iniciuje pokaždé, když se fond aktualizuje novou sadou knihoven. Tato úloha Spark pomáhá monitorovat stav instalace knihovny. Pokud se instalace nezdaří kvůli konfliktům knihoven nebo jiným problémům, fond Spark se vrátí k předchozímu nebo výchozímu stavu. 

Kromě toho mohou uživatelé také zkontrolovat protokoly instalace a identifikovat konflikty závislostí nebo zjistit, které knihovny byly během aktualizace fondu nainstalovány.

Postup zobrazení těchto protokolů:
1. Na kartě **monitorování** přejděte na seznam aplikace Spark. 
2. Vyberte úlohu systémové aplikace Spark, která odpovídá vaší aktualizaci fondu. Tyto systémové úlohy se spouštějí pod názvem *SystemReservedJob-LibraryManagement* .
   ![Snímek obrazovky, který zvýrazní rezervovanou systémovou úlohu knihovny.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Zobrazit úlohu systémové knihovny")
3. Přepněte k zobrazení protokolů **ovladače** a **stdout** . 
4. V rámci výsledků se zobrazí protokoly týkající se instalace vašich závislostí.
    ![Snímek obrazovky, který zvýrazní výsledky úlohy rezervované pro systém.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Zobrazit průběh úlohy systémové knihovny")

## <a name="install-wheel-files"></a>Instalovat soubory kol
Soubory kol Pythonu představují běžný způsob balení knihoven Pythonu. V rámci Azure synapse Analytics můžou uživatelé nahrávat své soubory kol do dobře známého umístění, Azure Data Lake Storage účet nebo nahrávat pomocí rozhraní balíčků pracovních prostorů Azure synapse.

### <a name="workspace-packages-preview"></a>Balíčky pracovního prostoru (Preview)
Balíčky pracovního prostoru můžou být soubory vlastního nebo privátního kola. Tyto balíčky můžete nahrát do svého pracovního prostoru a později je přiřadit ke konkrétnímu fondu Spark.

Přidání balíčků pracovního prostoru:
1. Přejděte na kartu **Spravovat**  >  **balíčky pracovních prostorů** .
2. Nahrajte soubory kol pomocí voliče souborů.
3. Po nahrání souborů do pracovního prostoru Azure synapse můžete tyto balíčky přidat do daného fondu Apache Spark.

![Snímek obrazovky, který zvýrazní balíčky pracovních prostorů.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Zobrazit balíčky pracovních prostorů")

### <a name="storage-account"></a>Účet úložiště
Vlastní balíčky kol lze nainstalovat do fondu Apache Spark tím, že všechny soubory kol nahrajete do účtu Azure Data Lake Storage (Gen2), který je propojený s pracovním prostorem synapse. 

Soubory by měly být nahrány do následující cesty ve výchozím kontejneru účtu úložiště: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

Je možné, že budete muset přidat ```python``` složku do ```libraries``` složky, pokud ještě neexistuje.

> [!IMPORTANT]
> Pokud chcete nainstalovat vlastní knihovny pomocí metody úložiště Azure datalake, musíte mít oprávnění správce **dat objektu BLOB úložiště** nebo **vlastníka dat objektu BLOB úložiště** v primárním účtu úložiště Gen2, který je propojený s pracovním prostorem Azure synapse Analytics.

>[!WARNING]
> Když uživatelé zadávají vlastní soubory, nemůžou v účtu úložiště i v rozhraní knihovny pracovních prostorů poskytovat soubory kolečka. Pokud jsou zadány obě, nainstalují se jenom soubory kolečka zadané v seznamu balíčky pracovních prostorů. 

## <a name="session-scoped-packages-preview"></a>Balíčky s rozsahem relace (Preview)
Kromě balíčků na úrovni fondu můžete také na začátku relace poznámkového bloku zadat knihovny s rozsahem relace.  Knihovny s rozsahem relace umožňují zadat a používat vlastní prostředí Pythonu v rámci relace poznámkového bloku. 

Při použití knihoven s rozsahem relace je důležité mít na paměti následující body:
   - Při instalaci knihoven s rozsahem relace má přístup k určeným knihovnám pouze aktuální Poznámkový blok. 
   - Tyto knihovny nebudou mít vliv na ostatní relace nebo úlohy pomocí stejného fondu Spark. 
   - Tyto knihovny jsou nainstalovány nad základní modul runtime a knihovny na úrovni fondu. 
   - Knihovny poznámkových bloků budou mít nejvyšší prioritu.

Určení balíčků s rozsahem relace:
1.  Přejděte do vybraného fondu Spark a ujistěte se, že jste povolili knihovny na úrovni relace.  Toto nastavení můžete povolit tak, že přejdete na kartu **Spravovat**  >  **balíčky fondu Apache Spark**  >   . ![povolíte balíčky relací.](./media/apache-spark-azure-portal-add-libraries/enable-session-packages.png "Povolit balíčky relací")
2.  Po použití nastavení můžete otevřít poznámkový blok a vybrat možnost **Konfigurovat** >  **balíčky** relací.
  ![Zadejte balíčky relací.](./media/apache-spark-azure-portal-add-libraries/update-session-notebook.png "Aktualizovat konfiguraci relace")
3.  Tady můžete nahrát soubor conda *prostředí YML* pro instalaci nebo Upgrade balíčků v rámci relace. Po spuštění relace se nainstalují zadané knihovny. Po ukončení relace už nebudou tyto knihovny k dispozici, protože jsou specifické pro vaši relaci.

## <a name="verify-installed-libraries"></a>Ověřit nainstalované knihovny
Chcete-li ověřit, zda jsou správné verze správných knihoven nainstalovány z PyPI, spusťte následující kód:
```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
V některých případech můžete pro zobrazení verzí balíčku z conda potřebovat zkontrolovat verzi balíčku samostatně.

## <a name="next-steps"></a>Další kroky
- Zobrazení výchozích knihoven: [podpora Apache Spark verzí](apache-spark-version-support.md)
- Řešení chyb při instalaci knihovny: [řešení chyb v knihovně](apache-spark-troubleshoot-library-errors.md)
- Vytvoření privátního conda kanálu pomocí účtu Azure Data Lake Storage: [privátní kanály conda](./spark/../apache-spark-custom-conda-channel.md)
