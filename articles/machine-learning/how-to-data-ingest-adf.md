---
title: Příjem dat s využitím Azure Data Factory
titleSuffix: Azure Machine Learning
description: Naučte se, jak vytvořit kanál pro příjem dat pomocí Azure Data Factory.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 03/01/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: ad04566699b2eebb0cbd7a9f242de38bc75e2015
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90986398"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Příjem dat s využitím Azure Data Factory

V tomto článku se dozvíte, jak vytvořit kanál pro příjem dat pomocí Azure Data Factory (ADF). Tento kanál slouží k ingestování dat pro použití s Azure Machine Learning. Azure Data Factory umožňuje snadno extrahovat, transformovat a načítat data (ETL). Jakmile se data transformují a načtou do úložiště, dají se využít ke studiu vašich modelů strojového učení.

Jednoduchou transformaci dat je možné zpracovat pomocí nativních aktivit ADF a nástrojů, jako je [tok dat](https://docs.microsoft.com/azure/data-factory/control-flow-execute-data-flow-activity). Pokud se to týče složitějších scénářů, můžete data zpracovat pomocí nějakého vlastního kódu. Například kód Python nebo R.

Existuje několik běžných postupů použití Azure Data Factory k transformaci dat během přijímání. Každá z těchto technik má specialisty a nevýhody, které určují, jestli je vhodná pro konkrétní případ použití:

| Technika | Výhody | Nevýhody |
| ----- | ----- | ----- |
| ADF a Azure Functions | Nízká latence, COMPUTE bez serveru</br>Stavové funkce</br>Opakovaně použitelné funkce | Pouze dobrý pro krátkodobé zpracování |
| ADF + vlastní komponenta | Rozsáhlé paralelní výpočty</br>Vhodné pro těžké algoritmy | Balení kódu do spustitelného souboru</br>Složitost zpracování závislostí a vstupně-výstupních operací |
| Poznámkový blok ADF + Azure Databricks | Apache Spark</br>Nativní prostředí Pythonu | Může být drahý</br>Vytváření clusterů zpočátku trvá čas a přidává latenci.

## <a name="adf-with-azure-functions"></a>ADF s Azure Functions

![Diagram znázorňuje Azure Data Factory kanál, pomocí funkce Azure functions a spuštění kanálu M L a kanálu Azure Machine Learning, s modelem výukového modelu a způsob interakce s nezpracovanými daty a připravenými daty.](media/how-to-data-ingest-adf/adf-function.png)

Azure Functions umožňuje spouštění malých částí kódu (funkcí), aniž byste se museli starat o infrastrukturu aplikace. V této možnosti se data zpracovávají pomocí vlastního kódu Pythonu zabaleného do funkce Azure Functions. 

Tato funkce se vyvolá u [aktivity funkce Azure ADF](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity). Tento přístup je dobrou volbou pro zjednodušené transformace dat. 

* IT
    * Data se zpracovávají na výpočetní úrovni bez serveru s poměrně nízkou latencí.
    * Kanál ADF může vyvolat [trvalou funkci Azure](/azure/azure-functions/durable/durable-functions-overview) , která může implementovat složitý tok transformace dat. 
    * Podrobné informace o transformaci dat jsou vyvolány funkcí Azure Functions, které je možné znovu použít a vyvolat z jiných míst.
* Cons
    * Před používáním ADF musí být vytvořený Azure Functions.
    * Azure Functions je vhodné jenom pro krátkodobé běžící zpracování dat.

## <a name="adf-with-custom-component-activity"></a>ADF s aktivitou vlastní součásti

![Diagram znázorňuje Azure Data Factory kanál s vlastní komponentou a spuštěním kanálu M L a kanálu Azure Machine Learning, s modelem výukového modelu a způsobem, jak komunikují s nezpracovanými daty a připravenými daty.](media/how-to-data-ingest-adf/adf-customcomponent.png)

V této možnosti se data zpracovávají pomocí vlastního kódu Pythonu zabaleného do spustitelného souboru. Vyvolá se s [aktivitou vlastní komponenty ADF](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity). Tento přístup je lépe vhodný pro velké objemy dat, než je předchozí technika.

* IT
    * Data se zpracovávají ve fondu [Azure Batch](https://docs.microsoft.com/azure/batch/batch-technical-overview) , který poskytuje rozsáhlé paralelní a vysoce výkonné výpočetní prostředí.
    * Dá se použít ke spouštění těžkých algoritmů a zpracování velkých objemů dat.
* Cons
    * Fond Azure Batch musí být vytvořen před použitím s ADF.
    * Přes strojírenství související s zabalením kódu Pythonu do spustitelného souboru. Složitost zpracování závislostí a vstupně-výstupních parametrů

## <a name="adf-with-azure-databricks-python-notebook"></a>ADF s Azure Databricks Poznámkový blok Pythonu

![Diagram znázorňuje Azure Data Factory kanál, pomocí Azure Databricks Pythonu a spuštění kanálu M L a kanálu Azure Machine Learning, s modelem výukového modelu a způsobem interakce s nezpracovanými daty a připravenými daty.](media/how-to-data-ingest-adf/adf-databricks.png)

[Azure Databricks](https://azure.microsoft.com/services/databricks/) je analytická platforma založená na Apache Spark v cloudu Microsoftu.

V této technice se transformace dat provádí pomocí [poznámkového bloku Pythonu](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook), který běží v clusteru Azure Databricks. To je pravděpodobně nejběžnější přístup, který využívá plný výkon služby Azure Databricks. Je určený pro distribuované zpracování dat ve velkém měřítku.

* IT
    * Data se transformují na nejvýkonnější službě Azure pro zpracování dat, která je zálohovaná pomocí Apache Sparkho prostředí.
    * Nativní podpora Pythonu spolu s rozhraními a knihovnami pro datové vědy, včetně TensorFlow, PyTorch a scikit – učení
    * Nemusíte zabalit kód Pythonu do functions nebo spustitelných modulů. Kód funguje tak, jak je.
* Cons
    * Před používáním ADF musí být vytvořená Azure Databricks infrastruktura.
    * Může být nákladné v závislosti na konfiguraci Azure Databricks
    * Spuštění výpočetních clusterů z "studeného" režimu trvá určitou dobu, která přináší vysokou latenci řešení. 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>Spotřebovávání dat v Azure Machine Learningch kanálech

![Diagram znázorňuje Azure Data Factory kanál a kanál Azure Machine Learning a jejich interakce s nezpracovanými daty a připravenými daty. Kanál Data Factory kanálu data do připravené databáze dat, která informační kanály ukládá do úložiště dat, které datové sady informačních kanálů v pracovním prostoru Machine Learning.](media/how-to-data-ingest-adf/aml-dataset.png)

Transformovaná data z kanálu ADF se ukládají do úložiště dat (například Azure BLOB). Azure Machine Learning mají přístup k těmto datům pomocí datových [úložišť](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores) a datových [sad](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets).

Pokaždé, když se kanál ADF spustí, data se uloží do jiného umístění v úložišti. Pro předání umístění do Azure Machine Learning zavolá kanál ADF Azure Machine Learning kanál. Při volání kanálu ML se umístění dat a ID spuštění odesílají jako parametry. Kanál ML pak může vytvořit úložiště dat/datovou sadu pomocí umístění dat. 

> [!TIP]
> Datové sady [podporují správu verzí](https://docs.microsoft.com/azure/machine-learning/how-to-version-track-datasets), takže kanál ml může zaregistrovat novou verzi datové sady, která odkazuje na nejnovější data z kanálu ADF.

Jakmile jsou data přístupná prostřednictvím úložiště dat nebo datové sady, můžete je použít ke školení modelu ML. Proces školení může být součástí stejného kanálu ML, který je volán z ADF. Nebo se může jednat o samostatný proces, jako je třeba experimentování v Jupyter poznámkovém bloku.

Vzhledem k tomu, že datové sady podporují správu verzí, a každé spuštění z kanálu vytvoří novou verzi, je snadné pochopit, která verze dat byla použita pro výuku modelu.

## <a name="next-steps"></a>Další kroky

* [Spuštění poznámkového bloku datacihly v Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)
* [Přístup k datům ve službě Azure Storage](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)
* [Výukové modely s datovými sadami v Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)
* [DevOps kanálu pro přijímání dat](https://docs.microsoft.com/azure/machine-learning/how-to-cicd-data-ingestion)

