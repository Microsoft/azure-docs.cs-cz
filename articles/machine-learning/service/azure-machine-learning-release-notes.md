---
title: Co je nového ve službě Azure Machine Learning
description: Tento dokument podrobně popisuje aktualizace pro Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
author: hning86
ms.author: haining
ms.reviewer: j-martens
ms.date: 10/24/2018
ms.openlocfilehash: 52c8eed7f8b5a65ef13215e677d52f44cb95fd64
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284995"
---
# <a name="azure-machine-learning-service-release-notes"></a>Zpráva k vydání verze služby Azure Machine Learning

V tomto článku najdete další informace o vydaných verzích služby Azure Machine Learning. 

## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning sady SDK pro Python v0.1.80

+ **Rozbíjející změny v** 
  * *azureml.Train.Widget* obor názvů se přesunula do *azureml.train*.
  * *azureml.core.compute.AmlCompute* zastarání *azureml.core.compute.BatchAICompute*. Tato třída se odebere v dalších verzích.
### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Sada SDK v0.5.1 pro přípravu dat Azure Machine Learning 
Další informace o sadě SDK pro Data Prep načtením [referenční dokumentace](https://aka.ms/data-prep-sdk).
+ **Nové funkce**
   * Vytvoření nového příkazového řádku přípravy a spouštění balíčků přípravy a zobrazení data profilu pro datovou sadu nebo toku dat
   * Přepracované rozhraní API SetColumnType použitelnosti
   * Přejmenované smart_read_file k auto_read_file
   * Nyní zahrnuje Nerovnoměrná distribuce a míra fluktuace v profil dat
   * Vzorkovat s povoleným vzorkováním vrstveného
   * Může číst ze souborů zip, které obsahují soubory CSV
   * Můžete rozdělit datovým sadám row-wise pomocí náhodného dělení (například do sady testů regrese)
   * Můžete získat všechny sloupce datové typy toku dat nebo profil dat voláním .dtypes
   * Můžete získat počet řádků od toku dat nebo profil dat voláním .row_count

+ **Opravy chyb**
   * Pevně long double převodu 
   * Oprava vyhodnocení po přidání sloupce 
   * Opravili jsme problém s FuzzyGrouping, kde ji nebude skupiny v některých případech rozpoznat
   * Opravené seřadit funkce dodržovat pořadí řazení více sloupců
   * Oprava a/nebo výrazy, které se podobá způsob, jakým je zpracovává Pandas
   * Oprava čtení z dbfs cesty
   * Provedené pomohou lépe porozumět chybové zprávy 
   * Teď už selže při čtení na cílové vzdálené výpočetní prostředí pomocí tokenu AML
   * Teď už na Linuxu DSVM selže
   * Teď už dojde k chybě hodnoty jiné než řetězec jsou v predikátech řetězec
   * Nyní zpracovává kontrolního výrazu chyby při by měl správně selhání toku dat
   * Teď podporuje umístění dbutils připojené úložiště v Azure Databricks

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>portál Azure 
Na webu Azure portal pro službu Azure Machine Learning má následující aktualizace:
  * Nový **kanály** kartu pro publikované kanály.
  * Přidání podpory pro připojení k existujícímu clusteru HDInsight jako cílové výpočetní prostředí.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning sady SDK pro Python v0.1.74

+ **Rozbíjející změny v** 
  * *Workspace.compute_targets, úložišť, experimenty, obrázky, modely* a *webservices* jsou vlastnosti namísto metod. Nahraďte třeba *Workspace.compute_targets()* s *Workspace.compute_targets*.
  * *Run.get_context* zastarání *Run.get_submitted_run*. Druhá metoda odebere v dalších verzích.
  * *PipelineData* třídy nyní očekává, že objekt úložiště dat jako parametr spíše než datastore_name. Obdobně *kanálu* přijímá default_datastore spíše než default_datastore_name.

+ **Nové funkce**
  * Kanály Azure Machine Learning [ukázkový poznámkový blok](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) teď používá MPI kroky.
  * RunDetails widget pro poznámkové bloky Jupyter se aktualizuje a zobrazí vizualizaci kanálu.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Sada SDK v0.4.0 pro přípravu dat Azure Machine Learning 
 
+ **Nové funkce**
  * Počet typů přidat Data profilu 
  * Počet hodnot a Histogram je nyní k dispozici
  * Další percentily v profil dat
  * Je k dispozici v Summarize Medián
  * Python 3.7 se teď podporuje.
  * Při uložení toku dat, která obsahuje úložišť do balíčku přípravy informace úložiště dat bude trvalé jako součást balíčku přípravy
  * Zápis do úložiště dat se teď podporuje. 
        
+ **Chyba opravená**
  * 64bitový celé číslo bez znaménka, které přetečení jsou teď správně zpracovány v Linuxu
  * Oprava nesprávné textový popisek pro soubory ve formátu prostého textu v smart_read
  * Typ sloupce řetězec se zobrazí v zobrazení metrik
  * Počet typů je teď pevně zobrazíte ValueKinds namapován na jeden typ pole namísto jednotlivé značky
  * Write_to_csv už selže, pokud je poskytnuta cesta jako řetězec
  * Při použití nahradit, byste museli opustit "najít" prázdné už neselže 

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning sady SDK pro Python v0.1.68

+ **Nové funkce**
  * Více tenantů podporu při vytváření nového pracovního prostoru.

+ **Opravy chyb**
  * Verze knihovny pynacl už musí být odkazována při nasazování webové front-end služby.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Sada SDK v0.3.0 pro přípravu dat Azure Machine Learning

+ **Nové funkce**
  * Přidat metodu transform_partition_with_file(script_path), což umožňuje uživatelům a zajistěte tak předání cestu k souboru Pythonu ke spuštění

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Azure Machine Learning sady SDK pro Python v0.1.65
[Verze 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) obsahuje nové funkce, další dokumentaci, opravy chyb a další [ukázkové poznámkové bloky](https://aka.ms/aml-notebooks).

Zobrazit [seznam známých problémů](resource-known-issues.md) Další informace o známých chyb a jejich řešení.

+ **Rozbíjející změny v**
  * Workspace.experiments Workspace.models, Workspace.compute_targets, Workspace.images Workspace.web_services návratový slovníku. Tím se předtím vrátila seznam. Zobrazit [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) dokumentace k rozhraní API.

  * Automatizované Machine Learning odebrán směrodatná normalizované chyba primární metriky.

+ **HyperDrive**
  * Různé opravy chyb HyperDrive Bayesova, vylepšení výkonu pro získání metriky volání. 
  * Tensorflow 1.10 upgrade z 1.9 
  * Optimalizovat image dockeru pro úplné spuštění. 
  * Úloha je teď správný stav sestavy i v případě, že ukončit s chybou kódu jiné než 0. 
  * RunConfig atributu validation v sadě SDK. 
  * HyperDrive spustit objekt podporuje Storno podobný regulární spuštění: nemusíte předávat žádné parametry. 
  * Widget vylepšení pro udržování stavu rozevírací seznam hodnot pro distribuované spuštění a HyperDrive spuštění. 
  * TensorBoard a dalších protokolů, které podporují soubory vyřešili serveru parametr. 
  * Podpora MPI Intel(R) na straně služby. 
  * Opravu parametr ladění pro distribuované spuštění oprava během ověřování v BatchAI. 
  * Správce kontextu nyní identifikuje primární instance. 

+ **Azure portal ještě neznáte**
  * log_table() a log_row() jsou podporovány v podrobnostech o spuštění. 
  * Automaticky vytvořte grafy pro tabulky a řádky s 1,2 nebo 3 číselné sloupce a volitelný sloupec zařazené do kategorií.

+ **Automatizované Machine Learning**
  * Vylepšené zpracování chyb a dokumentace 
  * Načtení vlastnosti spuštění opravili problémy s výkonem. 
  * Oprava pokračovat v běhu problém. 
  * Opravili jsme ensembling iterace problémů.
  * Oprava školení Změ chyb v systému MAC OS.
  * Převzorkování – makro průměrné žádosti o přijetí změn a křivka ROC ve scénáři vlastní ověřování.
  * Odebrat další index logiku.
  * Odebrali jsme filtr, z get_output rozhraní API.

+ **Kanály**
  * Přidat metodu Pipeline.publish() do kanálu publikovat přímo, bez nutnosti spuštění se spouští jako první.   
  * Přidat spuštěním metody PipelineRun.get_pipeline_runs() načíst kanálu, které byly vytvořeny z publikovaných kanálu.

+ **Project Brainwave**
  * Aktualizovanou podporu nových modelů AI k dispozici na FPGA.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Sada SDK v0.2.0 pro přípravu dat Azure Machine Learning
[Verze 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) zahrnuje následující funkce a opravy chyb:

+ **Nové funkce**
  * Podpora pro jeden horkou kódování
  * Podpora pro transformaci quantile
   
+ **Chyba opravená:**
  * Funguje s jinou verzí tornádu není nutné přejít na nižší verzi tornádu verzi
  * Četnost hodnot pro všechny hodnoty, ne jenom první tři

## <a name="2018-09-public-preview-refresh"></a>2018-09 (aktualizace verze public preview)

A nové, zcela aktualizovat verzi služby Azure Machine Learning: Další informace o této verzi: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/

## <a name="older-notes-sept-2017---jun-2018"></a>Starší poznámky: září 2017 – červen 2018
### <a name="2018-05-sprint-5"></a>2018-05 (sprint 5)

V této verzi služby Azure Machine Learning můžete:
+ Obrázky Vytrénovaných kvantizované verzi modelem ResNet 50 trénování klasifikátor založených na těchto funkcích a [nasazení modelu FPGA v Azure](../service/how-to-deploy-fpga-web-service.md) pro odvozování mimořádně nízkou latencí.

+ Rychle sestavovat a nasazovat modely obsáhlého learningu pomocí s velmi přesnými strojového učení a [vlastní vydání balíčky Azure Machine Learning](../desktop-workbench/reference-python-package-overview.md) pro tyto domény:
  + [Počítačové zpracování obrazu](../desktop-workbench/how-to-build-deploy-image-classification-models.md)
  + [Analýza textu](../desktop-workbench/how-to-build-deploy-text-classification-models.md)
  + [Prognózování](../desktop-workbench/how-to-build-deploy-forecast-models.md)

### <a name="2018-03-sprint-4"></a>2018-03 (sprintu 4)
**Číslo verze**: 0.1.1801.24353 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([verzi zjistíte](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))


Mnohé z následující aktualizace probíhají jako přímé výsledky váš názor. Prosím Novoroční předsevzetí!

**Důležité nové funkce a změny**

- Podpora pro spouštění vašich skriptů na vzdálenou virtuální počítače s Ubuntu nativně ve svém vlastním prostředí kromě vzdáleného dockeru na základě spuštění.
- Nové prostředí pro prostředí v aplikaci Workbench umožňuje vytvoření cílových výpočetních prostředí a spuštění konfigurace kromě naše prostředí na základě rozhraní příkazového řádku.
![Karta prostředí](media/azure-machine-learning-release-notes/environment-page.png)
- Přizpůsobitelné historie spuštění sestavy ![obrázek nové spuštění sestavy historie](media/azure-machine-learning-release-notes/new-run-history-reports.png)

**Podrobné aktualizace**

Tady je seznam podrobné aktualizace v každé oblasti komponenty služby Azure Machine Learning v tomto sprintu.

#### <a name="workbench-ui"></a>Aplikace Workbench uživatelského rozhraní
- Upravitelné sestavy historie spuštění
  - Konfiguraci vylepšené grafu historie spuštění sestav
    - Využité entrypoints lze změnit.
    - Filtry nejvyšší úrovně lze přidávat a upravovat ![přidat filtry](media/azure-machine-learning-release-notes/add-filters.jpg)
    - Grafy a statistiky můžete přidat nebo upravit (a změnit jejich uspořádání přetažením).
    ![Vytváření nových grafů](media/azure-machine-learning-release-notes/configure-charts.png)

  - CRUD pro sestavy historie spuštění
  - Přesunout všechny stávající zobrazení seznamu historie spuštění konfigurace na straně serveru sestav, který se chová jako kanály na spuštění z bodů vybranou položku.

- Karta prostředí
  - Snadno přidat nové cílové výpočetní prostředí a spusťte konfigurační soubory do projektu ![nový cíl Compute](media/azure-machine-learning-release-notes/add-new-environments.png)
  - Spravovat a aktualizovat konfigurační soubory pomocí jednoduchého, založené na formulářích UX
  - Příprava prostředí pro spuštění nové tlačítko

- Vylepšení výkonu do seznamu souborů na bočním panelu

#### <a name="data-preparation"></a>Příprava dat 
- Azure Machine Learning Workbench umožňuje prohledávat pomocí známých sloupec název pro sloupec.


#### <a name="experimentation"></a>Experimentování
- Azure Machine Learning Workbench teď podporuje spouštění vašich skriptů nativně ve svém vlastním prostředí python nebo pyspark. Pro tuto funkci uživatel vytvoří a spravuje vlastní prostředí na vzdáleném virtuálním počítači a pomocí Azure Machine Learning Workbench můžete spouštět své skripty na, které cílí. Podrobnosti najdete na [konfigurace služby Azure Machine Learning služby experimentování ve službě](../desktop-workbench/experimentation-service-configuration.md) 

#### <a name="model-management"></a>Správa modelů
- Podpora pro vlastní nastavení nasazení kontejnerů: Povolí, přizpůsobení image kontejneru tím, že instalace pomocí apt-get a další externí knihovny. Už nejsou omezené na pip Instalovatelné knihovny. Zobrazit [dokumentaci](../desktop-workbench/model-management-custom-container.md) pro další informace.
  - Použití `--docker-file myDockerStepsFilename` příznak a název souboru manifestu, image nebo příkazy vytváření služby.
  - Mějte na paměti, že základní bitovou kopii se systémem Ubuntu a nemůže být upraven.
  - Příklad příkazu: 
  
      ```shell
      $ az ml image create -n myimage -m mymodel.pkl -f score.py --docker-file mydockerstepsfile
      ```



### <a name="2018-01-sprint-3"></a>2018-01 (sprint 3) 
**Číslo verze**: 0.1.1712.18263 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([verzi zjistíte](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Níže jsou aktualizace a vylepšení v tomto sprintu. U velké části těchto aktualizací probíhají jako přímý výsledek zpětné vazby uživatelů. 


Tady je seznam podrobné aktualizace v každé oblasti komponenty služby Azure Machine Learning v tomto sprintu.

- Aktualizace zásobníku ověřování vynutí výběru přihlášení a účet při spuštění

#### <a name="workbench"></a>Workbench
- Možnost instalace/odinstalace aplikace z panelu Přidat nebo odebrat programy
- Aktualizace zásobníku ověřování vynutí výběru přihlášení a účet při spuštění
- Vylepšené prostředí pro jednotné přihlašování (SSO) ve Windows
- Uživatelé, kteří patří do více tenantů s jinými přihlašovacími údaji teď budou moct přihlásit do aplikace Workbench

#### <a name="ui"></a>UŽIVATELSKÉ ROZHRANÍ
- Obecná vylepšení a opravy chyb

#### <a name="notebooks"></a>Poznámkové bloky
- Obecná vylepšení a opravy chyb

#### <a name="data-preparation"></a>Příprava dat 
- Vylepšené automatické návrhy při provádění transformací například
- Vylepšený algoritmus pro vzor četnosti inspektoru
- Umožňuje odeslat ukázková data a zpětnou vazbu při provádění transformací například ![Image odeslat odkaz pro zasílání názorů na odvození sloupce transformace](media/azure-machine-learning-release-notes/SendFeedbackFromDeriveColumn.png)
- Vylepšení modulu Runtime Spark
- Scala nahradil Pyspark
- Oprava neschopnost zavřete dat není k dispozici pro řadu inspektor čas 
- Oprava zablokuje čas pro spuštění přípravy dat pro Hdinsight

#### <a name="model-management-cli-updates"></a>Aktualizace rozhraní příkazového řádku správy modelů 
  - Vlastnictví předplatného se už nevyžaduje pro zřizování prostředků. Přístup přispěvatele do skupiny prostředků bude stačit k nastavení prostředí nasazení.
  - Povolené místní prostředí nastavení bezplatné předplatné 

### <a name="2017-12-sprint-2-qfe"></a>2017-12 (sprint 2 QFE) 
**Číslo verze**: 0.1.1711.15323 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([verzi zjistíte](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Jedná se o verzi QFE (Quick Fix Engineering), vedlejší verzi. Řeší problémy s několika telemetrie a pomáhá produktovému týmu a lépe pochopit, jak se používá produktu. Ve znalostní bázi můžete přejít do budoucí úsilí pomáhá vylepšovat prostředí produktu. 

Kromě toho existují dvě důležité aktualizace:

- Oprava chyby v přípravy dat, která zabránila zobrazení v balíčků pro přípravu dat řady inspektor čas.
- V nástroji příkazového řádku musíte už být vlastníkem předplatného Azure ke zřízení clusterů Machine Learning Compute ACS. 

### <a name="2017-12-sprint-2"></a>12. 2017 (sprint 2)
**Číslo verze**: 0.1.1711.15263 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([verzi zjistíte](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Vítá vás třetí aktualizace služby Azure Machine Learning. Tato aktualizace zahrnuje vylepšení v aplikaci workbench, rozhraní příkazového řádku (CLI) a back endové služby. Děkujeme pro odesílání úsměvy a frowns. Mnohé z následující aktualizace probíhají jako přímé výsledky váš názor. 

**Důležité nové funkce**
- [Podpora pro SQL Server a Azure SQL DB jako zdroje dat](../desktop-workbench/data-prep-appendix2-supported-data-sources.md#types) 
- [Hloubkové učení ve Sparku s podporou GPU používání MMLSpark](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- [Všechny kontejnery AML musí být kompatibilní s Azure IoT Edge zařízení po nasazení (žádné další kroky vyžadované)](https://aka.ms/aml-iot-edge-blog)
- Registrovanému modelu seznamu a podrobností zobrazení k dispozici webu Azure portal
- Přístup k cílových výpočetních prostředí pomocí ověřování na základě klíčů SSH kromě přístupu pomocí uživatelského jména a hesla. 
- Nový model frekvence inspektoru v datech pro přípravu prostředí. 

**Podrobné aktualizace** následuje seznam podrobné aktualizace v každé oblasti komponenty služby Azure Machine Learning v tomto sprintu.

#### <a name="installer"></a>Instalační program
- Instalační program může vlastní aktualizace, opravy chyb a nové funkce může být podporovaný, aniž by uživatel musel ho znovu nainstalujte

#### <a name="workbench-authentication"></a>Ověřování aplikace Workbench
- Několik oprav ověřovacího systému. Dejte nám vědět, pokud jsou stále setkává s problémy při přihlášení.
- Změny uživatelského rozhraní, které usnadňují vyhledání nastavení správce serveru Proxy.

#### <a name="workbench"></a>Workbench
- Zobrazení souborů jen pro čtení má teď světle modrá na pozadí
- Doprava a usnadňují zjistitelnější přesunutý tlačítko Upravit.
- formáty souborů "ipynb", "dprep" a "dsource" lze vykreslit teď ve formátu raw textu
- Aplikace workbench teď obsahuje nové možnosti úprav, který provede uživatelé pomocí externí prostředí IDE účelem úprav skriptů a pomocí aplikace Workbench pouze můžete upravit typy souborů, které mají bohaté možnosti úprav (například poznámkových bloků, zdroje dat, balíčků pro přípravu dat)
- Načítá se seznam pracovních prostorů a projektů, které má uživatel přístup k je teď mnohem rychlejší

#### <a name="data-preparation"></a>Příprava dat 
- Kontrola frekvence vzor Chcete-li zobrazit tyto vzory se dají řetězec ve sloupci. Můžete také filtrovat data pomocí tyto vzory. Zobrazí zobrazení podobný inspektoru četnost hodnot. Rozdíl je, že vzor četnosti zobrazují počty jedinečné vzory dat, a ne počet jedinečných dat. Můžete také filtrovat snížení nebo navýšení kapacity všechny řádky, které odpovídají určité vzoru.

![Obrázek inspektoru frekvence vzor na číslo produktu](media/azure-machine-learning-release-notes/pattern-inspector-product-number.png)

- Vylepšení výkonu při doporučování hraniční případy ke kontrole v transformaci "odvození sloupce podle příkladu.

- [Podpora pro SQL Server a Azure SQL DB jako zdroje dat](../desktop-workbench/data-prep-appendix2-supported-data-sources.md#types) 

![Obrázek vytvoření nového zdroje dat serveru SQL](media/azure-machine-learning-release-notes/sql-server-data-source.png)

- Povolené "zasáhnout" Zobrazit počty řádků a sloupců

![Obrázek sloupce počet řádků v odkladu](media/azure-machine-learning-release-notes/row-col-count.png)

- Příprava dat je povolený v všechny výpočetní kontext
- Zdroje dat využívající databázi SQL serveru jsou povoleny v všechny výpočetní kontext
- Mřížky, které sloupce by šlo filtrovat podle datového typu pro přípravu dat
- Oprava potíží s převod více sloupců na data
- Opravili jsme chybu, tento uživatel může vybrat výstupního sloupce jako zdroj v odvodit sloupec podle příkladu Pokud uživatel změnil název výstupního sloupce v rozšířeném režimu.

#### <a name="job-execution"></a>Provádění úlohy
Nyní můžete vytvořit a otevřít remotedocker nebo v clusteru cílového typu výpočetního prostředí s pomocí ověřování SSH klíče pomocí následujících kroků:
- Připojit cílové výpočetní prostředí pomocí následujícího příkazu v rozhraní příkazového řádku

    ```azure-cli
    $ az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
    ```
>[!NOTE]
>-k (nebo--použijte azureml-ssh-key) v příkazu určuje pro vygenerování a použití klíče SSH.

- Azure ML Workbench generovat veřejný klíč, který se výstup, který v konzole. Přihlaste se do cílové výpočetní prostředí pomocí stejného uživatelského jména a připojit soubor ~/.ssh/authorized_keys pomocí veřejného klíče.

- Můžete připravit této cílové výpočetní prostředí a použít ho pro spuštění a Azure ML Workbench použije tento klíč pro ověřování.  

Další informace o vytvoření cílových výpočetních prostředí najdete v tématu [konfigurace služby Azure Machine Learning služby experimentování ve službě](../desktop-workbench/experimentation-service-configuration.md)

#### <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools pro AI
- Přidání podpory pro [Visual Studio Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vstoolsai-vs2017). 

#### <a name="command-line-interface-cli"></a>Rozhraní příkazového řádku (CLI)
- Přidání `az ml datasource create` příkaz umožňuje vytvoření zdroje dat z příkazového řádku

#### <a name="model-management-and-operationalization"></a>Správa modelů a Operacionalizace
- [Všechny kontejnery AML musí být kompatibilní s Azure IoT Edge zařízení, když mají zprovoznit (žádné další kroky vyžadované)](https://aka.ms/aml-iot-edge-blog) 
- Vylepšení chybových zpráv v rozhraní příkazového řádku o16n
- Opravy chyb v portálu pro správu modelu uživatelského prostředí  
- Konzistentní písmeno malých a velkých písmen pro atributy model správy na stránce podrobností
- Časový limit volání vyhodnocování v reálném čase nastavena na 60 sekund
- Registrovanému modelu seznamu a podrobností zobrazení k dispozici na webu Azure Portal

![Podrobné informace o modelu na portálu](media/azure-machine-learning-release-notes/model-list.jpg)

![Přehled modelu na portálu](media/azure-machine-learning-release-notes/model-overview-portal.jpg)

#### <a name="mmlspark"></a>MMLSpark
- Hloubkové učení ve Sparku s využitím [podporou GPU](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- Podpora pro šablony Resource Manageru pro nasazení jednoduché prostředků
- Podpora pro ekosystém SparklyR
- [Integrace AZTK](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark)

#### <a name="sample-projects"></a>Ukázkové projekty
- [Iris](https://github.com/Azure/MachineLearningSamples-Iris) a [MMLSpark](https://github.com/Azure/mmlspark) ukázky aktualizovat v nové verzi sady SDK Azure ML

#### <a name="breaking-changes"></a>Změny způsobující chyby
- Povýšen `--type` přepínače v `az ml computetarget attach` k dílčí příkaz. 

    - `az ml computetarget attach --type remotedocker` je teď `az ml computetarget attach remotedocker`
    - `az ml computetarget attach --type cluster` je teď `az ml computetarget attach cluster`

### <a name="2017-11-sprint-1"></a>2017-11 (sprint 1) 
**Číslo verze**: 0.1.1710.31013 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([verzi zjistíte](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

V této verzi jsme provedli jsme vylepšení týkající se zabezpečení, stability a udržovatelnosti v aplikaci workbench, rozhraní příkazového řádku a vrstva back endové služby. Děkujeme, že mnohokrát odesláním úsměvy a frowns. Mnoho níže aktualizace probíhají jako přímé výsledky váš názor. Novoroční předsevzetí!

#### <a name="notable-new-features"></a>Důležité nové funkce
- Služba Azure ML je teď dostupná ve dvou nových oblastech Azure: **západní Evropa** a **jihovýchodní Asie**. Připojí předchozí oblasti **USA – východ 2**, **střed USA – západ**, a **Austrálie – východ**, přináší celkový počet nasazené oblasti na pět.
- Povolili jsme syntaxe kódu Python zvýraznění v aplikaci Workbench, aby bylo snazší číst a upravovat zdrojový kód Pythonu. 
- Teď můžete spustit přímo ze souboru, nikoli z celého projektu oblíbeného prostředí IDE.  Otevření souboru v aplikaci Workbench a pak kliknutím na "Edit" spustí vaše integrované vývojové prostředí (aktuálně VS Code a PyCharm podporuje) k aktuálnímu souboru a projektu.  Můžete také kliknout na šipku vedle tlačítka Upravit upravte soubor v textovém editoru aplikace Workbench.  Soubory jsou jen pro čtení, dokud nekliknete na tlačítko Upravit, prevenci proti náhodným změnám.
- Oblíbené knihovny zobrazování `matplotlib` verze 2.1.0 je nyní dodávána s prostředím aplikace Workbench.
- Budeme upgradovat verzi .NET Core 2.0 pro modul pro přípravu dat. To odebere požadavek brew install openssl během instalace aplikace v systému macOS. To usnadní také cestu pro zajímavější data přípravu funkce přijde v blízké budoucnosti. 
- Zavedli jsme domovskou stránku aplikace specifické pro verzi a tak získat relevantnější zpráva k vydání verze a aktualizaci pokynů podle vaší aktuální verzí aplikace.
- Pokud vaše místní uživatelské jméno obsahuje mezeru, aplikace nyní možné úspěšně nainstalovat. 

#### <a name="detailed-updates"></a>Podrobné aktualizace
Níže je seznam podrobné aktualizace v každé oblasti komponenty služby Azure Machine Learning v tomto sprintu.

##### <a name="installer"></a>Instalační program
- Instalační program aplikace nyní vyčistí instalační_adresář vytvořena pomocí starší verze aplikace.
- Opravili jsme chybu, která vede získávání zablokuje na 100 % v systému macOS High Sierra Instalační služby.
- Je teď přímý odkaz na instalační program adresář pro uživatele, aby v případě selhání instalace, projděte si instalační protokoly.
- Instalace teď funguje pro uživatele, kteří mají místa v jejich uživatelskému jménu.

##### <a name="workbench-authentication"></a>Ověřování aplikace Workbench
- Podpora pro ověřování ve Správci serveru Proxy.
- Protokolování nyní proběhne úspěšně, pokud uživatel je za bránou firewall. 
- Pokud jsou uživatelské účty služby experimentování ve službě v několika oblastech Azure, a pokud jedné oblasti stane nedostupný, přestane reagovat už aplikace.
- Pokud ověřování neproběhne a dialogové okno ověřování je stále zobrazená, aplikace už se pokusí načíst pracovní prostor z místní mezipaměti.

##### <a name="workbench-app"></a>Aplikace Workbench
- Zvýrazňování syntaxe kódu Python je povolená v textovém editoru.
- Tlačítko Upravit v textovém editoru lze upravit soubor v rozhraní IDE (VS Code a PyCharm jsou podporovány) nebo v editoru integrovanou text.
- Textový editor, je v režimu jen pro čtení ve výchozím nastavení. 
- Uložte změny nyní tlačítko vizuálního stavu zakázáno po aktuální soubor je uložený a proto již změny.
- Aplikace Workbench uloží _všechny_ neuložené soubory při zahájení spuštění.
- Aplikace Workbench zapamatuje si, že poslední použitá pracovní prostor v místním počítači, takže se automaticky otevře.
- Pouze jedna instance aplikace Workbench je teď můžou ke spuštění. Může být již v minulosti představila více instancí, které způsobily potíže při fungování na stejném projektu.
- Přejmenované nabídky Soubor "Otevřít projekt..." do "Přidat existující složku jako projekt..." 
- Přepínání karta je teď mnohem rychlejší.
- Odkazy nápovědy jsou přidány do dialogového okna Konfigurace integrovaného vývojového prostředí.
- Formulář zpětné vazby teď si pamatuje e-mailovou adresu zadali naposledy.
- Úsměvy a frowns textová oblast formuláře je nyní větší, tak nám můžete poslat další názor! 
- `--owner` Přepnout text nápovědy v `az ml workspace create` je opravit.
- Přidali jsme "O" dialogové okno s umožňující uživateli snadno zobrazení a zkopírování číslo verze aplikace.
- Položka nabídky "Navrhovat funkce" je přidána do nabídky Nápověda.
- Název experimentálního účtu je nyní v aplikaci záhlaví, předchozí název aplikace "Azure Machine Learning Workbench".
- Domovská stránka specifické pro verzi aplikace se zobrazí, teď na základě verze aplikace zjistila.

##### <a name="data-preparation"></a>Příprava dat 
- Externí web je už načíst z mapový inspektor, aby se zabránilo potenciální problémy se zabezpečením.
- Inspektoři Histogram a počet hodnot má teď možnost zobrazit graf v logaritmickém měřítku.
- Pruh kvality dat, pokud je výpočet probíhající nyní zobrazuje jinou barvou, který signalizuje, že stav "výpočet".
- Sloupec metriky se teď zobrazují statistiky pro sloupce s hodnotami zařazené do kategorií.
- Poslední znak v názvu zdroje dat už je oříznutá.
- Balíček pro přípravu dat teď zůstane otevřená, při přepínání karet, což zisky zřetelný rozdíl ve výkonu.
- Ve zdroji dat, při přepínání mezi zobrazením dat a zobrazení metrik už změny pořadí sloupců nyní.
- Otevírání neplatný `.dprep` nebo `.dsource` souboru již nezpůsobuje, že aplikace Workbench selhání.
- Balíček pro přípravu dat můžete nyní používá relativní cestu pro výstup v _zápisu do sdíleného svazku clusteru_ transformace.
- _Zachovat sloupec_ transformace teď umožňuje uživateli přidat i další sloupce při úpravách.
- _Nahraďte_ nabídky teď ve skutečnosti spustí _nahradit hodnotu_ dialogové okno.
- _Nahraďte hodnotu_ transformace nyní funkce očekávaným namísto vyvolání chyby.
- Balíček pro přípravu dat teď používá absolutní cestu, při odkazování na datových souborů mimo složku projektu, což umožňuje spouštění balíčku v místní kontextu se absolutní cesta k datovému souboru.
- _Úplný soubor_ strategie vzorkování teď podporuje při použití Azure blob jako zdroj dat.
- Generovaný kód Pythonu (z balíčku pro přípravu dat) nyní nese CR a LF, takže popisný ve Windows.
- _Zvolte metriky_ rozevírací seznam nyní skryje vlastnost při přechodu k zobrazení dat.
- Aplikace Workbench můžete nyní soubory parquet procesu i v případě, že ho používá modulu runtime Pythonu. Dříve pouze Spark lze použít při zpracování soubory parquet. 
- Vyfiltrování hodnot ve sloupci s _datum_ datového typu již nezpůsobuje, že modul pro přípravu k selhání.
- Zobrazení metrik nyní respektuje vzorkování strategii aktualizace.
- Vzdálené úlohy vzorkování teď funguje správně.

##### <a name="job-execution"></a>Provádění úlohy
- Argument je nyní zahrnutá v záznamu historie spuštění.
- Úlohy se spustila rozhraní příkazového řádku se zobrazí v panel Historie úlohy spustit automaticky.
- Panel úlohy nyní zobrazuje úloh vytvořených produktem uživatele typu Host do tenanta Azure AD.
- Zrušit panel úlohy a jsou více stabilní akce odstranění.
- Při kliknutí na tlačítko spustit, je nyní spuštěna chybovou zprávu, pokud konfigurační soubory jsou v chybný formát.
- Ukončující aplikace už naruší úloh spuštěna v rozhraní příkazového řádku.
- Úlohy spustila rozhraní příkazového řádku nyní dál rozdělit standardní výstup i po jedné hodině provádění.
- Lepší chybové zprávy se zobrazují, když se nezdaří spustit balíček pro přípravu dat v Pythonu/PySpark.
- `az ml experiment clean` Nyní vyčistí Image Dockeru ve vzdáleném virtuálním počítači.
- `az ml experiment clean` nyní funguje správně pro místní cíl v systému macOS.
- Chybové zprávy při cílení na místním nebo vzdáleném Docker spuštění vyčištěním a snadněji čitelné.
- Lepší chybové zprávy se zobrazí, když název hlavního uzlu clusteru HDInsight není ve správném formátu při připojené jako cíl provádění.
- Lepší chybové zprávy se zobrazí, pokud tajný klíč nebyl nalezen ve službě přihlašovacích údajů. 
- Knihovna MMLSpark se upgraduje na podporu Apache Spark 2.2.
- MMLSpark teď obsahují subjektu kódování transformace (síť kódování) pro lékařské dokumenty.
- `matplotlib` verze 2.1.0 je nyní uvidíte na více instancí na pole pomocí aplikace Workbench.

##### <a name="jupyter-notebook"></a>Poznámkový blok Jupyter
- Vyhledání názvu poznámkového bloku teď funguje správně v zobrazení poznámkových bloků.
- Nyní můžete odstranit poznámkového bloku v zobrazení poznámkových bloků.
- Nové magic `%upload_artifact` je přidaný pro nahrávání souborů vytvořených v prostředí pro spuštění poznámkového bloku do úložiště dat historie spuštění.
- Chyby jádra se nyní zobrazí v stav úlohy poznámkového bloku pro snazší ladění.
- Jupyter nyní správně vypnutí serveru při přihlášení uživatele z aplikace.

##### <a name="azure-portal"></a>portál Azure
- Účet experimentování a účet služby Správa modelů je nyní vytvořit ve dvou nových oblastech Azure: západní Evropa a jihovýchodní Asie.
- Plán DevTest účtu správy modelů teď je k dispozici pouze pokud je první z nich má být vytvořen v rámci předplatného. 
- Odkaz na nápovědu na webu Azure Portal se aktualizuje tak, aby odkazoval na stránku pro správnou dokumentaci.
- Pole popisu je odebrat ze stránky podrobností image Dockeru, protože se nedá použít.
- Podrobnosti, včetně nastavení AppInsights a automatické škálování jsou přidány na stránku podrobností webové služby.
- Stránka pro správu modelu nyní vykreslí i v případě, že soubory cookie třetích stran jsou zakázány v prohlížeči. 

##### <a name="operationalization"></a>Operacionalizace
- Webové služby s využitím "skóre" v názvu již selže.
- Uživatel teď můžete vytvářet prostředí pro nasazení s právě přístup přispěvatele pro skupinu prostředků Azure nebo předplatné. Přístup vlastníka pro celé předplatné je už nepotřebujete.
- Operacionalizace CLI nyní využívá automatické doplňování tabulátorů v Linuxu.
- Služba vytváření bitových kopií teď podporuje sestavování imagí pro služby/zařízení Azure IoT.

##### <a name="sample-projects"></a>Ukázkové projekty
- [_Klasifikace Iris_ ](../desktop-workbench/tutorial-classifying-iris-part-1.md) ukázkového projektu:
    - `iris_pyspark.py` bylo přejmenováno na `iris_spark.py`.
    - `iris_score.py` bylo přejmenováno na `score_iris.py`.
    - `iris.dprep` a `iris.dsource` jsou aktualizovány tak, aby odrážely nejnovější aktualizace dat přípravu modul.
    - `iris.ipynb` Poznámkového bloku se mění v clusteru HDInsight.
    - Je zapnutá historie spuštění `iris.ipynb` buňky poznámkového bloku.
- [_Rozšířené použití Data projektu Bikeshare Data Prep_ ](../desktop-workbench/tutorial-bikeshare-dataprep.md) ukázkový projekt "Zpracování chybovou hodnotu" krok opraveno.
- [_MMLSpark dospělé dat sčítání_ ](https://github.com/Azure/MachineLearningSamples-mmlspark) ukázkový projekt `docker.runconfig` formátu aktualizováno z hodnoty JSON na YAML.
- [_Distribuované Hyperparametrů_ ](../desktop-workbench/scenario-distributed-tuning-of-hyperparameters.md) ukázkový projekt`docker.runconfig` formátu aktualizováno z hodnoty JSON na YAML.
- Nový projekt ukázky [ _klasifikace obrázků s využitím CNTK_](../desktop-workbench/scenario-image-classification-using-cntk.md).


### <a name="2017-10-sprint-0"></a>2017 – 10 (sprint 0) 
**Číslo verze**: 0.1.1710.31013 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([verzi zjistíte](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Vítá vás se nejdřív aktualizovat aplikaci Azure Machine Learning Workbench po naše počáteční verzi public preview na konferenci Microsoft Ignite 2017. Spolehlivost a stabilizace jsou hlavní aktualizace v této verzi opravy.  Důležité problémy, se kterými vyřešili jsme patří:

#### <a name="new-features"></a>Nové funkce
- se teď podporuje macOS High Sierra

#### <a name="bug-fixes"></a>Opravy chyb
##### <a name="workbench-experience"></a>Aplikace Workbench prostředí
- Přetažení souboru do aplikace Workbench způsobí, že se aplikace Workbench a havárií.
- V okně terminálu v nástroji VS Code, nakonfigurované jako integrované vývojové prostředí pro aplikaci Workbench nerozpozná _az ml_ příkazy.

##### <a name="workbench-authentication"></a>Ověřování aplikace Workbench
Jsme provedli několik aktualizací pro zlepšení různých přihlášení a ověření problémy, které nahlásili.
- Okno ověřování zachová automaticky otevíraného up, zejména při připojení k Internetu není stabilní.
- Vylepšení spolehlivosti problémy kolem ověřování vypršení platnosti tokenu.
- V některých případech se vyskytuje dvakrát okno ověřování.
- Hlavního okna aplikace Workbench stále zobrazuje zpráva "ověřování" po dokončení procesu ověřování a místním dialogovém už zrušená.
- Pokud neexistuje žádné připojení k Internetu, ověřování otevře se dialogové okno s prázdnou obrazovku.

##### <a name="data-preparation"></a>Příprava dat 
- Když je filtrovat konkrétní hodnoty, chyby a chybějící hodnoty se taky odfiltrovat.
- Změna strategie vzorkování odebere následné stávající operace spojení.
- Nahrazení chybí hodnota transformace nepřijímá NaN v úvahu.
- Odvození typu datum vyvolá výjimku, když je zjištěna hodnota null.

##### <a name="job-execution"></a>Provádění úlohy
- Při provádění úlohy se nepodařilo nahrát složku projektu, protože překročil limit velikosti není žádná vymazat chybovou zprávu.
- Pokud se skript Pythonu uživatele změní pracovní adresář, nebudou pro účely soubory zapsané do složky výstupy. 
- Pokud aktivní předplatné Azure je jiný než ten, který patří aktuálního projektu, výsledky odeslání úlohy Chyba 403.
- Pokud Docker není k dispozici, žádný vymazat chybovou zprávu je vrácena, jestliže uživatel pokusí použít jako cíl spuštění Docker.
- .runconfig soubor se neuloží automaticky, když uživatel klikne na _spustit_ tlačítko.

##### <a name="jupyter-notebook"></a>Poznámkový blok Jupyter
- Pokud uživatel použije k určitým typům přihlášení nelze spustit server poznámkového bloku.
- Poznámkový blok server chybové zprávy není plochu v protokolech, které jsou viditelné pro uživatele.

##### <a name="azure-portal"></a>portál Azure
- Tmavý motiv z webu Azure portal výběrem způsobí, že okno Správa modelů ve službě a zobrazí černé políčko.

##### <a name="operationalization"></a>Operacionalizace
- Opětovné použití manifest, který chcete aktualizovat webovou službu způsobí, že se nová image Dockeru sestavenou s náhodným názvem.
- Protokoly webové služby nelze načíst z clusteru Kubernetes.
- Zavádějící chybovou zprávu, vytiskne se, když se uživatel pokusí vytvořit účet správy modelů nebo účet pro ML Compute a zaznamená problémy s oprávněními.

## <a name="next-steps"></a>Další postup

Přečtěte si přehled služby [Azure Machine Learning](../service/overview-what-is-azure-ml.md).
