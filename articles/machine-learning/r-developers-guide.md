---
title: Příručka pro vývojáře jazyka R k Azure – programování v jazyce R | Dokumentace Microsoftu
description: Tento článek obsahuje přehled různých způsobů, odborníci přes data můžou využívat své existující dovednosti s R programovací jazyk v Azure. Azure nabízí mnoho služeb, které R vývojáři mohou využít rozšířit své datové vědy úlohy do cloudu.
services: machine-learning
documentationcenter: ''
author: AnalyticJeremy
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: R
ms.topic: article
ms.date: 09/12/2018
ms.author: jepeach
ms.openlocfilehash: 0e5db2c08749a59a03e384c6048e67f7570f6750
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211425"
---
# <a name="r-developers-guide-to-azure"></a>Příručka pro vývojáře jazyka R do Azure
<img src="media/r-developers-guide/logo_r.svg" alt="R logo" align="right" width="200" />

Mnoho vědce zabývající se stále se zvětšujícím objemy dat hledáte způsoby, jak využít výkon cloud computingu pro jejich analýzy.  Tento článek obsahuje přehled různých způsobů, kterými odborníci přes data můžou využívat své stávající znalosti [programovací jazyk R](https://www.r-project.org) v Azure.

Microsoft má plně kterých je založena na programovací jazyk R jako první třídy nástroj pro odborníky přes data.  Tím, že poskytuje mnoho různých možností pro vývojáře jazyka R spouštět svůj kód v Azure, je povolení společnosti datovým vědcům při použití velké projekty rozšířit své datové vědy úlohy do cloudu.

Podívejme se na různé možnosti a nejpůsobivějších scénáře pro každé z nich.

## <a name="azure-services-with-r-language-support"></a>Služby Azure s podporou jazyka R
Tento článek se týká následujících služeb Azure, které podporují jazyk R:

|Služba                                                          |Popis                                                                       |
|-----------------------------------------------------------------|----------------------------------------------------------------------------------|
|[Virtuální počítač pro datové vědy](#data-science-virtual-machine)    |přizpůsobené virtuální počítač pro použití jako pracovní stanici, datové vědy nebo jako cíl pro vlastní výpočet|
|[Služby ML v HDInsight](#ml-services-on-hdinsight)            |systém založený na clusteru pro spouštění analýz R na velkých datových sad napříč mnoha uzly   |
|[Azure Databricks](#azure-databricks)                            |prostředí pro spolupráci Spark, který podporuje R a další jazyky               |
|[Azure Machine Learning Studio](#azure-machine-learning-studio)  |spuštění vlastních skriptů R v Azure se strojovým učením                      |
|[Azure Batch](#azure-batch)                                      |nabízí různé možnosti ekonomicky mezi velký počet uzlů v clusteru spuštění kódu jazyka R|
|[Azure Notebooks](#azure-notebooks)                              |verze založené na cloudu bezplatného poznámkových bloků Jupyter                  |
|[Azure SQL Database](#azure-sql-database)                        |spouštění skriptů jazyka R v databázovém stroji SQL serveru                            |

## <a name="data-science-virtual-machine"></a>Virtuální počítač pro datové vědy
[Virtuální počítač pro datové vědy](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) (DSVM) je přizpůsobená image virtuálního počítače v Azure Cloudová platforma Microsoftu vytvořená speciálně pro datových věd. Má mnoho oblíbených nástrojů pro datové vědy, včetně:
* [Microsoft R Open](https://mran.microsoft.com/open/)
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)
* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop)
* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server)

Datové VĚDY s Windows nebo Linuxem dá zřídit jako operační systém.  Datové VĚDY můžete použít dvěma způsoby: jako interaktivní pracovní stanice nebo jako výpočetní platforma pro vlastní cluster.

### <a name="as-a-workstation"></a>Jako pracovní stanice
Pokud chcete začít pracovat s jazykem R v cloudu snadno a rychle, je nejlepším řešením.  Prostředí budou znát všichni, kdo má ve spolupráci s jazykem R na místní pracovní stanici.  Nemusíte používat místní prostředky, ale prostředí jazyka r. běží na virtuálním počítači v cloudu.  Pokud vaše data jsou už uložená v Azure, to má výhodu v podobě povolení svoje skripty jazyka R spustit "blíže k datům." Namísto použití přenosu dat přes Internet, je přístupný v interní síti Azure, která poskytuje mnohem rychlejší přístup data.

Datové VĚDY, může být zvláště užitečné pro malé týmy vývojářů R.  Místo investic do výkonných pracovních stanic pro každého vývojáře a vyžadování členům týmu. budou synchronizovat na verzích různých softwarových balíčků, které budou používat, každý vývojář aktivovat instance datové VĚDY, kdykoli je to třeba.

### <a name="as-a-compute-platform"></a>Jako výpočetní platforma
Kromě používán jako pracovní stanice, datové VĚDY slouží také jako Elasticky škálovatelné výpočetní platforma pro projekty v R.  Použití <code>[AzureDSVM](https://github.com/Azure/AzureDSVM)</code> balíček R, můžete programově řídit vytváření a odstraňování DSVM instancí.  Můžete tvoří instancí do clusteru a nasadit distribuované analýzy má být provedena v cloudu.  Celý tento proces můžete řídit pomocí R kód spuštěný na místní pracovní stanici.

Další informace o datové VĚDY, najdete ["Úvod do Azure virtuální počítač pro datové vědy pro Linux a Windows."](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview)

## <a name="ml-services-on-hdinsight"></a>Služby ML v HDInsightu
[Služby Microsoft ML](https://docs.microsoft.com/azure/hdinsight/r-server/r-server-overview) poskytuje datovým vědcům, statistikům a programátorům v R přístup na vyžádání ke škálovatelným, distribuovaným analytickým na HDInsight metodám služby.  Toto řešení poskytuje nejnovější funkce pro analýzy založené na jazyce R na datových sadách prakticky jakékoli velikosti, načteno do úložiště objektů Blob v Azure nebo Data Lake.

Toto je podnikové řešení umožňující kódu R škálovat napříč clusterem.  S využitím funkce v Microsoftu
<code>[RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)</code> balíček, svoje skripty jazyka R v HDInsight můžete spustit funkcí pro zpracování dat paralelně napříč mnoha uzly v clusteru.  Díky tomu R pro zpracování dat v mnohem větším měřítku než je možné s jazykem R s jedním vláknem spuštěna na pracovní stanici.

Díky schopnosti škálování je služby ML na HDInsight skvělou možností pro vývojáře jazyka R s obrovských datových sad.  Poskytuje flexibilní a škálovatelná platforma pro spouštění vašich skriptů R v cloudu.

Návod, jak na vytvoření clusteru služby ML, najdete v článku ["Začínáme se službou ML v Azure HDInsight"](https://docs.microsoft.com/azure/hdinsight/r-server/r-server-get-started) článku.

## <a name="azure-databricks"></a>Azure Databricks
[Azure Databricks](https://azure.microsoft.com/services/databricks/) je analytická platforma založená na Apache Spark a optimalizovaná pro platformu cloudových služeb Microsoft Azure.  Platforma Databricks je navržená ve spolupráci se zakladateli Apache Spark a díky integraci s Azure poskytuje nastavení jedním kliknutím, zjednodušené pracovní postupy a interaktivní pracovní prostor, který umožňuje spolupráci mezi datovými vědci, odborníky přes data a obchodními analytiky.

Spolupráce v Databricks je povoleno systémem platformy poznámkového bloku.  Uživatelé můžou vytvářet, sdílet a upravovat poznámkové bloky s ostatními uživateli v systémech.  Tyto poznámkové bloky umožňují napsat kód, který se spustí s clustery Spark v Databricks prostředí spravován.  Tyto poznámkové bloky plně podporují R a uživatelům umožňují přístup k Sparku prostřednictvím i `SparkR` a `sparklyr` balíčky.

Protože Databricks je založený na Spark a má silným zaměřením na spolupráci, se často používá platformu týmy odborníků přes data, které spolupracují na složité analýzy velkých datových sad.  Protože poznámkových bloků v Databricks podporuje další jazyky kromě R, je užitečné zejména pro týmy, které analytici kde použít různé jazyky pro primární práci.

Tento článek ["Co je Azure Databricks?"](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks)
můžete zadat další podrobnosti o platformě a pomůžou vám začít.

## <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[Azure Machine Learning studio](https://azure.microsoft.com/services/machine-learning-studio/) je nástroj pro spolupráci, přetáhněte myší, můžete vytvářet, testovat a nasazovat řešení prediktivní analýzy v cloudu.  Umožňuje nově vznikající odborníci přes data k vytvoření a nasazení modelů strojového učení, aniž byste museli psát většinu kódu.

Azure Machine Learning studio podporuje R a Python.  Použití R s Azure Machine Learning studio dvěma způsoby.

### <a name="custom-r-scripts-in-your-experiments"></a>Vlastní R skripty ve své experimenty
Nejprve můžete rozšířit manipulace s daty a machine learningu možnosti sady ML Studio napsáním vlastní skripty jazyka R.
I když ML Studio obsahuje celou řadu modulů pro přípravu a analýzy dat, nemůže odpovídat funkce až po zralé jazyky jako R.  Proto je služba navržená aby bylo možné zavést svých vlastních skriptů R v případech, kdy zadané moduly nebudou vyhovovat vašim potřebám.

Chcete-li tuto funkci využívat, přetáhněte a umístěte modul "Spustit skript jazyka R" do svého experimentu.  Pak pomocí editoru kódu v podokně "Properties" napsat nový skript R nebo vložte existující skript.  Ve skriptu můžete odkazovat na externí balíčky R.  Skript můžete použít k manipulaci s daty nebo tak moct trénovat složité modely ML, které nejsou součástí standardní knihovny modelu Azure Machine Learning studio.

Důkladné Úvod k používání R v nástroji ML Studio experimenty, podívejte se ["Rychlý úvodní kurz pro R programovací jazyk pro Azure Machine Learning studio."](https://docs.microsoft.com/azure/machine-learning/studio/r-quickstart)

### <a name="create-manage-and-deploy-experiments-from-your-local-r-environment"></a>Vytvořit, spravovat a nasazovat experimenty z vašeho místního prostředí R
Jiným způsobem, že můžou používat R s Azure Machine Learning studio je použít
<code>[AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html)</code> balíček ke sledování a řízení procesu experimentování s programovacího prostředí jazyka r.  Tento balíček, který je spravován společností Microsoft, umožňuje nahrávání a stahování datových sad do a ze služby Azure Machine Learning studio, k dotazování experimenty, chcete-li publikovat R funguje jako webové služby a spustit R data prostřednictvím existující webové služby a načíst výstup.

Tento balíček je mnohem snazší používat Azure Machine Learning studio jako platforma pro škálovatelná nasazení pro váš kód R.  Místo kliknutím a přetažením v uživatelském rozhraní, můžete automatizovat celý proces nasazení pomocí nástroje, které už znáte.

## <a name="azure-batch"></a>Azure Batch
Pro rozsáhlé úlohy R, můžete použít [Azure Batch](https://azure.microsoft.com/services/batch/).  Tato služba poskytuje cloudových úlohy plánování a správa výpočetních úloh R je možné škálovat napříč desítky, stovky nebo tisíce virtuálních počítačů.  Protože je generalizovaný výpočetní platforma, existuje několik možností pro spouštění úloh R v Azure Batch.

Jednou z možností je použití společnosti Microsoft <code>[doAzureParallel](https://github.com/Azure/doAzureParallel)</code> balíčku.  Tento balíček R je paralelního back-endu pro `foreach` balíčku.  Umožňuje, aby každá iterace `foreach` smyčky k paralelnímu spuštění na uzlech v clusteru služby Azure Batch.  Úvod do balíčku, přečtěte si prosím ["balíček doAzureParallel: Využijte flexibilní výpočetní prostředky Azure přímo z relace R"](https://azure.microsoft.com/blog/doazureparallel/) blogový příspěvek.

Další možností pro spuštění skriptu jazyka R ve službě Azure Batch je začlenit svůj kód pomocí "RScript.exe" jako aplikaci služby Batch na webu Azure Portal.  Podrobný návod najdete ["Úlohy v jazyce R v Azure Batch."](https://azure.microsoft.com/blog/r-workloads-on-azure-batch/)

Třetí možností je použít [Azure distribuovaných dat Engineering Toolkit](https://github.com/Azure/aztk) (AZTK), která umožňuje zřízení clusterů Spark na vyžádání pomocí kontejnerů Dockeru ve službě Azure Batch.  To poskytuje ekonomické ke spuštění úlohy Spark v Azure.  S použitím [SparklyR s AZTK](https://github.com/Azure/aztk/wiki/SparklyR-on-Azure-with-AZTK), svoje skripty jazyka R můžete škálovat v cloudu snadno a ekonomicky.

## <a name="azure-notebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com) je s nízkými náklady a bezproblémové metodu pro R vývojáři, kteří dávají přednost práci s poznámkových bloků a použít vlastní kód do Azure.  Jedná se o bezplatnou službu pro vývoj a spouštění kódu v jejich používání prohlížeče [Jupyter](https://jupyter.org/), což je open source projekt umožňující kombinace prose markdownu, spustitelného kódu a grafiky na jedné plátno.

Úroveň free služby poznámkových bloků Azure je vhodným řešením pro rozsáhlé projekty, protože se limity každý Poznámkový blok procesu až 4GB paměti a 1GB datových sad. Pokud potřebujete power výpočetní prostředky a data nad rámec těchto omezení, ale můžete spustit poznámkových bloků v instanci virtuálního počítače pro datové vědy. Další informace najdete v tématu [spravovat a nakonfigurovat projekty poznámkových bloků Azure – výpočetní vrstva](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier).

## <a name="azure-sql-database"></a>Azure SQL Database
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) je inteligentní plně spravovaná relační Cloudová databázová služba od Microsoftu.  Umožňuje plně využijte potenciál systému SQL Server bez jakékoli nepříjemnostem nastavení infrastruktury.  Jedná se o [Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning?view=sql-server-2017), což je jedna z více nedávno přidanými funkcemi do služby SQL Service.

Tato funkce nabízí vložené, prediktivní analýzy a data science modul, který může spustit kód jazyka R v databázi serveru SQL Server jako uložené procedury, jako obsahující R příkazy skriptů T-SQL nebo jako kód jazyka R, který obsahuje T-SQL.  Namísto extrahování dat z databáze a načítání do prostředí R, načtěte kód R přímo do databáze a nechat běžet vpravo vedle data.

Machine Learning Services byl součástí místního SQL serveru od 2016, je relativně nové ke službě Azure SQL Database.  Je aktuálně ve [ve verzi limited preview](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#azure-sql-database-roadmap) ale se dál vyvíjejí.


### <a name="next-steps"></a>Další postup
* [Spuštění kódu jazyka R v Azure s mrsdeploy](http://blog.revolutionanalytics.com/2017/03/running-your-r-code-azure.html)
* [Machine Learning Server v cloudu](https://docs.microsoft.com/machine-learning-server/install/machine-learning-server-in-the-cloud)
* [Další zdroje informací pro Machine Learning Server a Microsoft R](https://docs.microsoft.com/machine-learning-server/resources-more)
* [R v Azure](https://github.com/yueguoguo/r-on-azure) – Přehled balíčků, nástroje a případové studie pro Azure pomocí jazyka R

---

<sub>Logo R &copy; 2016 The R Foundation a používá se v části podmínky [licence Creative Commons Attribution-ShareAlike 4.0 International](https://creativecommons.org/licenses/by-sa/4.0/).</sub>
