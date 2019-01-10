---
title: Co je nového ve verzi?
titleSuffix: Azure Machine Learning service
description: Další informace o nejnovějších aktualizacích služby Azure Machine Learning a strojové učení a pro přípravu dat sady SDK pro Python.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
author: hning86
ms.author: haining
ms.reviewer: j-martens
ms.date: 12/20/2018
ms.custom: seodec18
ms.openlocfilehash: a43481bf6d9c95efdb9c4bc38ed400c5fe782c17
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157509"
---
# <a name="azure-machine-learning-service-release-notes"></a>Zpráva k vydání verze služby Azure Machine Learning

V tomto článku najdete další informace o vydaných verzích služby Azure Machine Learning. 

## <a name="2018-12-20"></a>2018-12-20: 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning sady SDK pro Python v1.0.6

+ **Referenční dokumentace sady SDK**: https://aka.ms/aml-sdk

+ **Opravy chyb**: Tato verze obsahuje většinou menšími opravami chyb

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Verzi v1.0.4 sady SDK pro přípravu dat Azure Machine Learning

+ **Referenční dokumentace sady SDK**: https://aka.ms/data-prep-sdk

+ **Nové funkce**
  + `to_bool` Funkce teď umožňuje neodpovídající hodnot má být převeden na chybové hodnoty. Toto je nový neshoda výchozí chování pro `to_bool` a `set_column_types`, že bylo předchozí výchozí chování pro převod neodpovídající hodnoty na False.
  + Při volání metody `to_pandas_dataframe`, je nová možnost interpretace hodnotu null nebo chybějící hodnoty v číselné sloupce jako NaN.
  + Přidání možnosti zkontrolujte návratový typ některé výrazy k zajištění konzistence typu a selhat co nejdřív.
  + Nyní můžete volat `parse_json` k parsování hodnot ve sloupci jako objekty JSON a rozšířit do více sloupců.

+ **Opravy chyb**
  + Je opravená chyba, ke které došlo k chybě `set_column_types` v Python verze 3.5.2.
  + Je opravená chyba, ke které došlo k chybě při připojování k úložišti dat pomocí AML image.

+ **Aktualizace**
  * [Příklad poznámkových bloků](https://aka.ms/aml-data-prep-notebooks) získávají začít kurzy, případové studie a Průvodce postupy.

## <a name="2018-12-04-general-availability"></a>2018-12-04: Všeobecná dostupnost

Služba Azure Machine Learning je teď obecně dostupná.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
V této verzi nové spravované výpočetní prostředí pomocí oznamujeme [Azure Machine Learning Compute](how-to-set-up-training-targets.md#amlcompute). Tato cílové výpočetní prostředí nahradí výpočetní prostředky Azure Batch AI pro Azure Machine Learning. 

Tato cílového výpočetního prostředí:
+ Slouží k odvozování model školení a batch
+ Je výpočetní single - do více - node
+ Správa clusteru a úlohy plánování pro uživatele
+ Pravidla automatického škálování provedou ve výchozím nastavení
+ Podpora pro prostředky procesoru a GPU 
+ Umožňuje použití virtuálních počítačů s nízkou prioritou pro snížení nákladů

Azure Machine Learning Compute je možné vytvořit v Pythonu pomocí webu Azure portal nebo rozhraní příkazového řádku. Musí být vytvořené v oblasti vašeho pracovního prostoru a nemůže být připojen k jinému pracovnímu prostoru. Tato cílové výpočetní prostředí pomocí kontejneru Dockeru pro spuštění a balíčky závislostí replikovat stejné prostředí napříč všemi uzly.

> [!Warning]
> Doporučujeme vytvořit nový pracovní prostor používat Azure Machine Learning Compute. Může se vzdálená stát, které uživatelům pokusu o vytvoření aplikace Azure Machine Learning Compute z existujícího pracovního prostoru může zobrazit chyba. By měla fungovat to neovlivní existující výpočetních prostředků ve vašem pracovním prostoru.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Azure Machine Learning sady SDK pro Python v1.0.2
+ **Rozbíjející změny v**
  + V této verzi jsme se odebrání podpory pro vytvoření virtuálního počítače ze služby Azure Machine Learning. Stále můžete připojit existující cloud virtuálního počítače nebo na vzdáleném místním serverem. 
  + Podpora pro BatchAI, které by měl být podporovány prostřednictvím Azure Machine Learning Compute nyní také odebíráme.

+ **Nový**
  + Pro strojové učení kanály:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **Aktualizovat**
  + Pro strojové učení kanály:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) nyní přijímá runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) nyní zkopíruje do a ze zdroje dat SQL
    + Naplánovat funkce v sadě SDK vytvářet a aktualizovat plány pro spouštění publikovaných kanály

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Sada SDK v0.5.2 pro přípravu dat Azure Machine Learning
+ **Rozbíjející změny v** 
  * `SummaryFunction.N` byla přejmenována na `SummaryFunction.Count`.
  
+ **Opravy chyb**
  * Použijte nejnovější AML spustit Token při čtení a zápisu do úložiště o vzdálené spuštění. Dříve Pokud AML spustit Token se aktualizuje v Pythonu, modul runtime přípravy dat nebude aktualizována aktualizované AML spustit Token.
  * Další jasnější chybové zprávy
  * to_spark_dataframe() už havaruje při Spark využívá `Kryo` serializace
  * Počet hodnot inspektoru teď můžete zobrazit více než 1 000 jedinečných hodnot
  * Náhodné rozdělení už selže, pokud původní tok dat nemá název  

+ **Další informace**
  * [Sada SDK pro přípravu dat Azure Machine Learning](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Dokumentace a poznámkové bloky
+ Kanály ML
  + Nové a aktualizované poznámkových bloků pro zahájení práce s kanály, rozsahu služby batch a styl přenos příklady: https://aka.ms/aml-pipeline-notebooks
  + Zjistěte, jak [vytvořit svůj první kanál](how-to-create-your-first-pipeline.md)
  + Zjistěte, jak [spouštění predikcí služby batch pomocí kanálů](how-to-run-batch-predictions.md)
+ Cílové výpočetní prostředí Azure Machine Learning
  + [Ukázkové poznámkové bloky] (https://aka.ms/aml-notebooks) jsou nyní aktualizované na používání nové spravované výpočetní prostředky.
  + [Další informace o tomto výpočetní](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Azure portal: nové funkce
+ Vytvoření a správa [Azure Machine Learning Compute](how-to-set-up-training-targets.md#amlcompute) typy na portálu.
+ Monitorování využití kvóty a [žádost o kvótu](how-to-manage-quotas.md) pro Azure Machine Learning Compute.
+ Zobrazit stav clusteru Azure Machine Learning Compute v reálném čase.
+ Vytvoření Azure Machine Learning Compute a službě Azure Kubernetes Service byla přidána podpora virtuální sítě.
+ Znovu spusťte publikované kanálů s existující parametry.
+ Nové [automatizované machine learning grafy](how-to-track-experiments.md#auto) modelů klasifikace (výtah, zisky, kalibrací, funkce význam graf s modelem explainability) a regresních modelů (zbytky a funkce význam grafu s modelem explainability). 
+ Kanály lze zobrazit na webu Azure portal




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning sady SDK pro Python v0.1.80

+ **Rozbíjející změny v** 
  * *azureml.Train.widgets* obor názvů se přesunula do *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* zastarání následující třídy – *azureml.core.compute.BatchAICompute* a *azureml.core.compute.DSVMCompute*. Tato třída se odebere v dalších verzích. Třída AmlCompute má nyní jednodušší definici a jednoduše potřebuje vm_size a max_nodes a automatické škálování clusteru od 0 do max_nodes, když je úloha odeslána. Naše [ukázkové poznámkové bloky] (https://github.com/Azure/MachineLearningNotebooks/tree/master/training) byly aktualizovány s těmito informacemi a by vám měl dát příklady použití. Věříme, že je třeba toto zjednodušení a spoustu další zajímavé funkce, které chcete se dělí na novější verzi!

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
   * Můžete získat všechny sloupce datové typy toku dat nebo profil dat voláním `.dtypes`
   * Můžete získat počet řádků od toku dat nebo profil dat voláním `.row_count`

+ **Opravy chyb**
   * Pevně long double převodu 
   * Oprava vyhodnocení po přidání sloupce 
   * Opravili jsme problém s FuzzyGrouping, kde ji nebude skupiny v některých případech rozpoznat
   * Opravené seřadit funkce dodržovat pořadí řazení více sloupců
   * Oprava a/nebo výrazy, které se podobá postupu `pandas` je zpracovává
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
  * * Workspace.compute_targets, úložišť, experimenty, obrázky, modely a *webservices* jsou vlastnosti namísto metod. Nahraďte třeba *Workspace.compute_targets()* s *Workspace.compute_targets*.
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
  * 64bitové celé číslo bez znaménka přetečení jsou teď správně zpracovány v Linuxu
  * Oprava nesprávné textový popisek pro soubory ve formátu prostého textu v smart_read
  * Typ sloupce řetězec se zobrazí v zobrazení metrik
  * Počet typů je teď pevně zobrazíte ValueKinds namapován na jeden typ pole namísto jednotlivé značky
  * Write_to_csv už selže, pokud je poskytnuta cesta jako řetězec
  * Při použití nahradit, byste museli opustit "najít" prázdné už neselže 

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning sady SDK pro Python v0.1.68

+ **Nové funkce**
  * Podpora více tenantů při vytváření nového pracovního prostoru.

+ **Opravy chyb**
  * Už nemusíte připnout verzi knihovny pynacl při nasazení webové služby.

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
  * Úlohy teď oznamují správný stav, i když opustí s kódem chyby než 0. 
  * RunConfig atributu validation v sadě SDK. 
  * HyperDrive spustit objekt podporuje Storno podobný regulární spuštění: nemusíte předávat žádné parametry. 
  * Widget vylepšení pro udržování stavu rozevírací seznam hodnot pro distribuované spuštění a HyperDrive spuštění. 
  * TensorBoard a dalších protokolů, které podporují soubory vyřešili serveru parametr. 
  * Podpora MPI Intel(R) na straně služby. 
  * Opravu parametr ladění pro distribuované spuštění oprava během ověřování v BatchAI. 
  * Správce kontextu nyní identifikuje primární instance. 

+ **Azure portal ještě neznáte**
  * log_table() a log_row() jsou podporovány v podrobnostech o spuštění. 
  * Automaticky vytvořte grafy pro tabulky a řádky s 1, 2 nebo 3 číselné sloupce a volitelný sloupec zařazené do kategorií.

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
  * Přidat metodu PipelineRun.get_pipeline_runs() načíst spuštěním kanálu, které byly generovány z publikovaných kanálu.

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

Nové aktualizace verze služby Azure Machine Learning: Další informace o této vydané verzi: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>Další postup

Přehled pro [služby Azure Machine Learning](../service/overview-what-is-azure-ml.md).
