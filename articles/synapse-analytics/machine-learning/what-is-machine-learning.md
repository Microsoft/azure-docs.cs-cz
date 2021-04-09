---
title: Machine Learning ve službě Azure synapse Analytics
description: Přehled možností strojového učení ve službě Azure synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 68b113de63cfefde805c1c46e9303829c4eb33a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222135"
---
# <a name="machine-learning-capabilities-in-azure-synapse-analytics"></a>Možnosti strojového učení ve službě Azure Synapse Analytics

Azure synapse Analytics nabízí různé možnosti strojového učení. Tento článek poskytuje přehled o tom, jak můžete Machine Learning použít v kontextu služby Azure synapse.

Tento přehled popisuje různé možnosti v synapse týkající se strojového učení, a to z perspektivy procesů pro datové vědy.

Je možné, že budete mít přehled o tom, jak vypadá typický proces pro datové vědy. Jedná se o dobře známý proces, který sleduje většina projektů Machine Learning.

V nejvyšší úrovni proces obsahuje následující kroky:
* (Obchodní porozumění)
* Získávání a pochopení dat
* Modelování
* Nasazení a bodování modelu

Tento článek popisuje možnosti strojového učení Azure synapse v různých analytických modulech z perspektivy procesu datové vědy. Pro každý krok v rámci vědeckého zpracování dat se synapse možnosti Azure, které můžou pomáhat.

## <a name="azure-synapse-machine-learning-capabilities"></a>Možnosti strojového učení pro Azure synapse

### <a name="data-acquisition-and-understanding"></a>Získávání a pochopení dat

Většina projektů strojového učení zahrnuje dobře zavedený postup. jedním z těchto kroků je přístup k datům a jejich pochopení.

#### <a name="data-source-and-pipelines"></a>Zdroje dat a kanály

Díky [Azure Data Factory](../../data-factory/introduction.md)nativně integrovaná součást služby Azure synapse je k dispozici výkonná sada nástrojů pro přijímání dat a kanály orchestrace dat. Díky tomu můžete snadno vytvářet datové kanály pro přístup k datům a jejich transformaci do formátu, který je možné spotřebovat pro strojové učení. [Přečtěte si další informace o datových kanálech](../../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) v synapse. 

#### <a name="data-preparation-and-explorationvisualization"></a>Příprava a průzkum a vizualizace dat

Důležitou součástí procesu strojového učení je pochopit data pomocí průzkumu a vizualizací.

V závislosti na tom, kde jsou data uložená, nabízí synapse sadu různých nástrojů, které můžete prozkoumat a připravit pro účely analýzy a strojového učení. Jedním z nejrychlejších způsobů, jak začít s průzkumem dat, je použití Apache Spark nebo neserverových fondů SQL přímo nad daty v Data Lake.

* [Apache Spark pro Azure synapse](../spark/apache-spark-overview.md) nabízí funkce pro transformaci, přípravu a prohlížení vašich dat ve velkém měřítku. Tyto fondy Spark nabízí nástroje, jako je PySpark/Python, Scala a .NET, pro zpracování dat ve velkém měřítku. S využitím výkonných knihoven vizualizací se můžete seznámit s zkoumáním dat a lépe tak lépe pochopit data. [Přečtěte si další informace o tom, jak prozkoumat a vizualizovat data v synapse pomocí Sparku](../get-started-analyze-spark.md).

* [Fondy SQL serveru](../sql/on-demand-workspace-overview.md) nabízejí způsob, jak prozkoumat data pomocí TSQL přímo přes data Lake. Fondy SQL bez serveru nabízejí také některé integrované vizualizace v synapse studiu. [Přečtěte si další informace o tom, jak prozkoumat data s využitím fondů SQL bez serveru](../get-started-analyze-sql-on-demand.md).

### <a name="modeling"></a>Modelování

Ve službě Azure synapse můžete školicí modely strojového učení provádět na fondech Apache Spark pomocí nástrojů, jako je PySpark/Python, Scala nebo .NET.

#### <a name="train-models-on-spark-pools-with-mllib"></a>Výuka modelů ve fondech Spark pomocí MLlib

Modely strojového učení je možné vyškolet pomocí různých algoritmů a knihoven. [Spark MLlib](http://spark.apache.org/docs/latest/ml-guide.html) nabízí škálovatelné algoritmy strojového učení, které vám pomůžou vyřešit většinu problémů s klasickým strojovým učením. [Tento kurz](../spark/apache-spark-machine-learning-mllib-notebook.md) popisuje, jak vytvořit model pomocí MLlib v synapse.

Kromě MLlib lze také použít k vývoji modelů oblíbené knihovny, jako je například [Scikit](https://scikit-learn.org/stable/) . Podrobnosti o tom, jak nainstalovat knihovny ve fondech synapse Spark, najdete v tématu [Správa knihoven pro Apache Spark ve službě Azure synapse Analytics](../spark/apache-spark-azure-portal-add-libraries.md) .

#### <a name="train-models-with-azure-machine-learning-automated-ml"></a>Výuka modelů pomocí Azure Machine Learning automatizovaného ML

Dalším způsobem, jak naučit modely strojového učení, které nevyžadují v strojovém učení spoustu předchozích znalostí, je použití automatizovaného ML. [Automatizovaná ml](../../machine-learning/concept-automated-ml.md) je funkce, která automaticky nakládá sadu modelů strojového učení a umožňuje uživateli vybrat nejlepší model na základě konkrétních metrik. Díky bezproblémové integraci s Azure Machine Learning z notebooků Azure synapse můžou uživatelé snadno využívat automatizované ML v synapse s ověřováním průchozích Azure Active Directory.  To znamená, že musíte odkazovat na pracovní prostor Azure Machine Learning a nemusíte zadávat žádné přihlašovací údaje. Tady je [kurz automatizovaného ml](../spark/apache-spark-azure-machine-learning-tutorial.md) , který popisuje, jak pomocí Azure Machine Learning automatizovaného ml ve fondech Spark synapse používat automatizovanou analýzu.

### <a name="model-deployment-and-scoring"></a>Nasazení a bodování modelu

Modely, které byly vyškoleny buď v Azure synapse nebo mimo Azure synapse, se dají snadno použít pro dávkové vyhodnocování. V současné době v synapse existují dva způsoby, jak můžete spustit dávkové vyhodnocování.

* Pomocí [funkce prediktivního TSQL](../sql-data-warehouse/sql-data-warehouse-predict.md) ve fondech SQL synapse můžete spustit předpovědi přímo tam, kde vaše data žijí. Tato výkonná a škálovatelná funkce umožňuje rozšířit data bez přesunu dat z datového skladu. Zavádí se nový [model strojového učení s asistencí v nástroji synapse Studio](./tutorial-sql-pool-model-scoring-wizard.md) , kde můžete nasadit model ONNX z registru Azure Machine Learning modelu v synapse fondech SQL pro dávkové vyhodnocování pomocí předpovědi.

* Další možností pro dávkové vyhodnocování modelů strojového učení ve službě Azure synapse je využít fondy Apache Spark pro Azure synapse. V závislosti na knihovnách, které se používají ke vzdělávání modelů, můžete ke spuštění bodování dávky použít prostředí kódu.

## <a name="next-steps"></a>Další kroky

* [Začínáme s Azure Synapse Analytics](../get-started.md)
* [Vytvoření pracovního prostoru](../get-started-create-workspace.md)
* [Rychlý Start: vytvoření nové propojené služby Azure Machine Learning v synapse](quickstart-integrate-azure-machine-learning.md)
* [Kurz: Průvodce bodování modelu Machine Learning – vyhrazený fond SQL](tutorial-sql-pool-model-scoring-wizard.md)