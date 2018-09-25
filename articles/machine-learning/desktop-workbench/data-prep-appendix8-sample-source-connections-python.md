---
title: Příklad dalších zdrojových datových připojení s přípravou dat Azure Machine Learning | Dokumentace Microsoftu
description: Tento dokument obsahuje sadu příkladem zdrojového datová připojení, které je možné s přípravou dat Azure Machine Learning
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: ed0e6bc4c506535fcb679fc2b4015a61274a77f3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967689"
---
# <a name="sample-of-custom-source-connections-python"></a>Ukázka připojení vlastní zdroje (Python) 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Předtím, než se pustíte do čtení tohoto dodatku, přečtěte si [přehled rozšíření Python](data-prep-python-extensibility-overview.md).

## <a name="load-data-from-dataworld"></a>Načtení dat z data.world

### <a name="prerequisites"></a>Požadavky

#### <a name="register-yourself-at-dataworld"></a>Zaregistrovat v data.world
Budete potřebovat token rozhraní API z data.world webu.

#### <a name="install-dataworld-library"></a>Nainstalujte knihovnu data.world

Otevřete rozhraní příkazového řádku Azure Machine Learning Workbench výběrem **souboru** > **otevřete rozhraní příkazového řádku**.

```console
pip install git+git://github.com/datadotworld/data.world-py.git
```

Potom spusťte `dw configure` na příkazovém řádku, který vás vyzve k zadání tokenu. Když zadáte token, .dw / adresář bude vytvořen v domovském adresáři a váš token je uložen existuje.

```
API token (obtained at: https://data.world/settings/advanced): <enter API token here>
```
Teď byste měli moci naimportovat data.world knihovny.

#### <a name="load-data-into-data-preparation"></a>Načtení dat do přípravu dat

Vytvoření transformace transformovat tok dat (skript). Pak pomocí následujícího skriptu k načtení dat z data.world.

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#Load the dataset.
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset.
df = lds.dataframes['simpsons_episodes']

```
## <a name="azure-cosmos-db-as-a-data-source-connection"></a>Azure Cosmos DB jako připojení ke zdroji dat
Příklad služby Azure Cosmos DB jako datové připojení, přečtěte si [zatížení Azure Cosmos DB jako zdroje datového připojení](data-prep-load-azure-cosmos-db.md)
