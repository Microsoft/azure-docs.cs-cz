---
title: Data v Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Zjistěte, jak se Azure Machine Learning bezpečně připojuje k vašim datům a používá je pro úlohy strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 03/20/2020
ms.openlocfilehash: 982c9c9eadec4403c8116430e1e25092de99f1d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128492"
---
# <a name="data-access-in-azure-machine-learning"></a>Přístup k datům v Azure Machine Learning

Azure Machine Learning usnadňuje připojení k vašim datům v cloudu.  Poskytuje vrstvu abstrakce přes základní službu úložiště, takže můžete bezpečně přistupovat a pracovat s daty bez nutnosti psát kód specifický pro váš typ úložiště. Azure Machine Learning také poskytuje následující datové funkce:

*    Správa verzí a sledování datového původu
*    Označování dat 
*    Monitorování odchylek dat
*    Interoperabilita s pandami a datovými rámy Spark

## <a name="data-workflow"></a>Pracovní postup Data

Až budete připraveni použít data v cloudovém řešení úložiště, doporučujeme následující pracovní postup doručování dat. Tento pracovní postup předpokládá, že máte [účet úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) a data v cloudové službě úložiště v Azure. 

1. Vytvořte úložiště [dat Azure Machine Learning](#datastores) pro ukládání informací o připojení do úložiště Azure.

2. Z tohoto úložiště dat vytvořte [datovou sadu Azure Machine Learning,](#datasets) která bude ukazovat na konkrétní soubor (soubory) ve vašem podkladovém úložišti. 

3. Chcete-li tuto datovou sadu použít v experimentu strojového učení, můžete
    1. Připojte jej k výpočetnímu cíli experimentu pro trénování modelu.

        **Nebo** 

    1. Využijte ji přímo v řešeních Azure Machine [Learning,](concept-designer.md)jako jsou spuštění experimentu automatizovaného strojového učení (automatizovaná ML), kanály strojového učení nebo návrhář Azure Machine Learning .

4. Vytvořte [monitorování datových sad](#data-drift) pro výstupní datovou sadu modelu, která zjišťuje posun dat. 

5. Pokud je zjištěn posun dat, aktualizujte vstupní datovou sadu a odpovídajícím způsobem přetrénte model.

Následující diagram poskytuje vizuální ukázku tohoto doporučeného pracovního postupu.

![Diagram koncepce dat](./media/concept-data/data-concept-diagram.svg)

## <a name="datastores"></a>Úložiště dat

Úložiště dat Azure Machine Learning bezpečně uchovávejte informace o připojení do úložiště Azure, takže je nemusíte kódovat ve skriptech. [Zaregistrujte a vytvořte úložiště dat pro](how-to-access-data.md) snadné připojení k účtu úložiště a přístup k datům ve vaší základní službě úložiště Azure. 

Podporované služby cloudového úložiště v Azure, které se můžou registrovat jako úložiště dat:

+ Kontejner objektů blob Azure
+ Sdílená složka Azure
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL Database
+ Azure Database for PostgreSQL
+ Systém souborů Databricks
+ Azure Database for MySQL

## <a name="datasets"></a>Datové sady

Datové sady Azure Machine Learning jsou odkazy, které odkazují na data ve vaší službě úložiště. Nejedná se o kopie vašich dat, takže nevznikají žádné další náklady na úložiště. Chcete-li pracovat s daty v úložišti, [vytvořte datovou sadu](how-to-create-register-datasets.md) pro sbalení dat do spotřebního objektu pro úlohy strojového učení. Zaregistrujte datovou sadu do pracovního prostoru a sdílejte ji a znovu ji používejte v různých experimentech bez složitosti přiřazování dat.

Datové sady lze vytvářet z místních souborů, veřejných adres URL, [otevřených datových sad Azure](https://azure.microsoft.com/services/open-datasets/)nebo služeb úložiště Azure prostřednictvím datových úložišť. Chcete-li vytvořit datovou sadu z datového rámce pandas v paměti, zapište data do místního souboru, jako jsou parkety, a vytvořte datovou sadu z tohoto souboru.  

Podporujeme 2 typy datových sad: 
+ [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) představuje data v tabulkovém formátu analýzou zadaný soubor nebo seznam souborů. Můžete načíst TabularDataset do Pandas nebo Spark DataFrame pro další manipulaci a čištění. Úplný seznam datových formátů, ze kterých můžete vytvořit TabularDatasets, naleznete v [tématu TabularDatasetFactory class](https://aka.ms/tabulardataset-api-reference).

+ Soubor [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) odkazuje na jeden nebo více souborů ve vašem úložišti dat nebo veřejných adresÁCH URL. [Soubory,](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) na které odkazuje FileDatasets, můžete stáhnout nebo připojit k vašemu výpočetnímu cíli.

Další možnosti datových sad naleznete v následující dokumentaci:

+ [Verze a sledovat](how-to-version-track-datasets.md) datové sady lineage.
+ [Sledujte svou datovou sadu,](how-to-monitor-datasets.md) abyste pomohli s detekcí posunu dat.    

## <a name="work-with-your-data"></a>Práce s daty

Díky datovým sestavám můžete provádět řadu úloh strojového učení prostřednictvím bezproblémové integrace s funkcemi Azure Machine Learning. 

+ Vytvořte [projekt označování dat](#label).
+ Modely strojového učení:
     + [automatizované experimenty ML](how-to-use-automated-ml-for-ml-models.md)
     + [projektant](tutorial-designer-automobile-price-train-score.md#import-data)
     + [poznámkové bloky](how-to-train-with-datasets.md)
     + [Kanály Azure Machine Learning](how-to-create-your-first-pipeline.md)
+ Přístup k datovým souborům pro vyhodnocování s [dávkovým odvozením](how-to-use-parallel-run-step.md) v [kanálech strojového učení](how-to-create-your-first-pipeline.md).
+ Nastavte monitorování datové sady pro detekci [posunu dat.](#drift)

<a name="label"></a>

## <a name="data-labeling"></a>Označování dat

Označování velkého množství dat bylo často bolestmi hlavy v projektech strojového učení. Ti, kteří mají součást počítačového vidění, jako je klasifikace obrázků nebo detekce objektů, obvykle vyžadují tisíce obrázků a odpovídající popisky.

Azure Machine Learning poskytuje centrální umístění pro vytváření, správu a monitorování projektů označování. Projekty označování pomáhají koordinovat data, popisky a členy týmu, což umožňuje efektivnější správu úkolů popisků. Aktuálně podporované úkoly jsou klasifikace obrázků, buď vícepopisek nebo více tříd, a identifikace objektu pomocí ohraničených polí.

Vytvořte [projekt označování dat](how-to-create-labeling-projects.md)a vytvořte datovou sadu pro použití v experimentech strojového učení.

<a name="drift"></a>

## <a name="data-drift"></a>Posun dat

V kontextu strojového učení je posun dat změnou vstupních dat modelu, která vede ke snížení výkonu modelu. Je to jeden z hlavních důvodů, proč přesnost modelu snižuje v průběhu času, a proto sledování dat drift pomáhá detekovat problémy s výkonem modelu.

Další informace o tom, jak zjistit a upozornit na posun dat na nová data v datové sadě, najdete v článku [Vytvoření monitorování datové sady.](how-to-monitor-datasets.md)

## <a name="next-steps"></a>Další kroky 

+ Pomocí [těchto kroků](how-to-create-register-datasets.md) vytvořte datovou sadu ve studiu Azure Machine Learning nebo pomocí sady Python SDK.
+ Vyzkoušejte příklady školení datové sady s [našimi ukázkovými poznámkovými bloky](https://aka.ms/dataset-tutorial).
+ Příklady posunu dat naleznete v tomto [kurzu posunu dat](https://aka.ms/datadrift-notebook).
