---
title: Data v Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak Azure Machine Learning vzájemně spolupracuje s Vašimi daty a jak se využívají při experimentech v rámci strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 12/09/2019
ms.openlocfilehash: a2af1e87ce7b17183ae09fb02b2652a04f585e84
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128831"
---
# <a name="data-access-in-azure-machine-learning"></a>Přístup k datům v Azure Machine Learning

V tomto článku se dozvíte o řešeních pro správu a integraci dat Azure Machine Learning pro úlohy strojového učení. V tomto článku se předpokládá, že jste už vytvořili [účet Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) a [službu Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction).

Až budete připraveni použít data v úložišti, doporučujeme vám

1. Vytvořte úložiště dat Azure Machine Learning.
2. Z tohoto úložiště dat vytvořte Azure Machine Learning datovou sadu. 
3. Využijte tuto datovou sadu v experimentu ve službě Machine Learning, a to buď 
    1. Připojení k cíli výpočetního cíle experimentu pro školení modelů

        **ANI** 

    1. Přímo v Azure Machine Learning řešeních, jako je automatizované spouštění machine learningu (automatizované ML), kanály strojového učení a [návrhář Azure Machine Learning](concept-designer.md).
4. Vytvořte monitorování datových sad pro výstupní datovou sadu modelu pro detekci posunu dat. 
5. Pokud se zjistí posun dat, aktualizujte vstupní datovou sadu a odpovídajícím způsobem svůj model znovu nahlaste.

Následující diagram nabízí vizuální ukázku tohoto doporučeného pracovního postupu pro přístup k datům.

![Data-koncept-diagram](./media/concept-data/data-concept-diagram.svg)

## <a name="access-data-in-storage"></a>Přístup k datům v úložišti

Pokud chcete získat přístup k datům ve vašem účtu úložiště, Azure Machine Learning nabízí úložiště dat a datové sady. Úložiště dat odpovědět na otázku: jak se dá bezpečně připojit k datům v mém Azure Storage? Úložiště dat poskytují v rámci služby úložiště vrstvu abstrakce. To pomáhá v zabezpečení a snadném přístupu k úložišti, protože informace o připojení jsou uchovávány v úložišti dat a nejsou vystaveny ve skriptech. 

Datové sady odpovědět na otázku postup: Jak získám konkrétní datové soubory v úložišti dat? Datové sady odkazují na konkrétní soubor nebo soubory ve vašem zdrojovém úložišti, které chcete použít pro experimentování ve službě Machine Learning. Úložiště dat a datové sady společně poskytují zabezpečený, škálovatelný a reprodukovatelné pracovní postupy pro doručování dat pro vaše úlohy strojového učení.

### <a name="datastores"></a>Úložiště dat

Úložiště dat Azure Machine Learning je abstrakcí úložiště ve vašich službách Azure Storage. [Zaregistrujte a vytvořte úložiště dat](how-to-access-data.md) , abyste se mohli snadno připojit k účtu služby Azure Storage a přistupovat k datům v základních službách úložiště Azure.

Podporované služby úložiště Azure, které se dají zaregistrovat jako úložiště dat:
+ Kontejner objektů blob Azure
+ Sdílená složka Azure
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL Database
+ Azure Database for PostgreSQL
+ Systém souborů Databricks
+ Azure Database for MySQL

### <a name="datasets"></a>Datové sady

[Vytvořte datovou sadu Azure Machine Learning](how-to-create-register-datasets.md) pro interakci s daty v úložišti dat a zabalení dat do spotřebního objektu pro úlohy strojového učení. Zaregistrujte datovou sadu do svého pracovního prostoru, abyste ji mohli sdílet a opakovaně používat v různých experimentech bez složitosti příjmu dat.

Datové sady je možné vytvořit z místních souborů, veřejných adres URL, [otevřených datových sad Azure](#open)nebo konkrétních souborů v úložišti dat. Chcete-li vytvořit datovou sadu z datového rámce v PANDAS paměti, zapište data do místního souboru, třeba do sdíleného svazku clusteru, a vytvořte datovou sadu z tohoto souboru. Datové sady nejsou kopiemi vašich dat, ale odkazy odkazují na data ve službě úložiště, takže se neúčtují žádné dodatečné náklady na úložiště. 

Následující diagram znázorňuje, že pokud nemáte službu Azure Storage, můžete vytvořit datovou sadu přímo z místních souborů, veřejných adres URL nebo pomocí Azure Open DataSet. Provedete to tak, že datovou sadu připojíte k výchozímu úložišti dat, které bylo automaticky vytvořeno s [Azure Machine Learning pracovním prostorem](concept-workspace.md)experimentu.

![Data-koncept-diagram](./media/concept-data/dataset-workflow.svg)

Další možnosti datových sad najdete v následující dokumentaci:

+ [Verze a sledování](how-to-version-track-datasets.md) řádků datové sady
+ [Monitorujte datovou sadu](how-to-monitor-datasets.md) , abyste usnadnili detekci přenosů dat.
+  V dokumentaci k těmto dvěma typům datových sad si přečtěte následující informace:
    + [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) představuje data v tabulkovém formátu tak, že analyzuje zadaný soubor nebo seznam souborů. Umožňuje vám vyhodnotit data do PANDAS nebo Spark dataframe pro další manipulaci a čištění. Úplný seznam souborů, ze kterých můžete vytvořit TabularDatasets, najdete v tématu [Třída TabularDatasetFactory](https://aka.ms/tabulardataset-api-reference).

    + Soubor [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) odkazuje na jeden nebo více souborů v úložišti dat nebo veřejných adresách URL. Pomocí této metody můžete stáhnout nebo připojit soubory, které zvolíte, do svého cíle výpočetní služby jako objekt DataSet.

## <a name="work-with-your-data"></a>Práce s daty

S datovými sadami můžete provádět řadu úloh strojového učení prostřednictvím bezproblémové integrace s funkcemi Azure Machine Learning. 

+ [Naučte se modely strojového učení](how-to-train-with-datasets.md).
+ Využívání datových sad v 
     + [automatizované experimenty ML](how-to-use-automated-ml-for-ml-models.md)
     + [Návrhář](tutorial-designer-automobile-price-train-score.md#import-data) 
+ Přístup k datovým sadám pro bodování pomocí dávkového odvozování v [kanálech strojového učení](how-to-create-your-first-pipeline.md).
+ Vytvořte [projekt pro popisky dat](#label).
+ Nastavte monitorování datových sad pro detekci [posunu dat](#drift) .

<a name="open"></a>

## <a name="azure-open-datasets"></a>Otevřené datové sady Azure

[Otevřené datové sady v Azure](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets) jsou spravované veřejné datové sady, které můžete použít k přidání funkcí specifických pro konkrétní scénář do řešení Machine Learning pro přesnější modely. Otevřené datové sady jsou v cloudu na Microsoft Azure a jsou integrované do Azure Machine Learning. K datovým sadám můžete také přistupovat prostřednictvím rozhraní API a používat je v jiných produktech, například Power BI a Azure Data Factory.

Azure Open DataSets zahrnuje data z veřejných domén pro počasí, sčítání, svátky, veřejné zabezpečení a umístění, které vám pomůžou naučit modely strojového učení a rozšířit prediktivní řešení. Veřejné datové sady můžete sdílet taky v otevřených datových sadách Azure.

<a name="label"></a>

## <a name="data-labeling"></a>Popisky dat

Popisování velkých objemů dat je často starostí v projektech strojového učení. Ty, které mají součást počítačové vize, jako je například klasifikace obrázku nebo detekce objektu, obvykle vyžadují tisíce imagí a odpovídající popisky.

Azure Machine Learning poskytuje centrální umístění pro vytváření, správu a sledování projektů označování. Označování projektů usnadňuje koordinaci dat, popisků a členů týmu, což vám umožní efektivněji spravovat úlohy označování. Aktuálně podporované úlohy jsou klasifikace obrázku, a to buď pomocí ohraničených polí, nebo s více třídami, nebo s identifikací objektů.

+ Vytvořte [projekt pro označování dat a vypište](how-to-create-labeling-projects.md)datovou sadu pro použití v experimentech Machine Learning.

<a name="drift"></a>

## <a name="data-drift"></a>Posun dat

V kontextu strojového učení je posun dat změnou v modelu vstupní data, která vede k modelování snížení výkonu. Je jedním z hlavních důvodů, které se v průběhu času snižují přesností, a díky tomu sledování posunu dat pomáhá detekovat problémy s výkonem modelu.
Další informace o tom, jak detekovat a upozorňovat na data pro nová data v datové sadě, najdete v článku o [vytvoření objektu pro monitorování datových sad](how-to-monitor-datasets.md) .

## <a name="next-steps"></a>Další kroky 

+ Vytvořte datovou sadu v Azure Machine Learning studiu nebo v sadě Python SDK, [použijte následující postup.](how-to-create-register-datasets.md)
+ Vyzkoušejte si příklady školení datových sad pomocí našich [ukázkových poznámkových bloků](https://aka.ms/dataset-tutorial).
+ Příklady posunu dat najdete v tomto [kurzu pro posun](https://aka.ms/datadrift-notebook)dat.
