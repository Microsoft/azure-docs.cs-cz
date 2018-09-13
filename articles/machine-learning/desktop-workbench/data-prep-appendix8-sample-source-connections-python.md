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
ms.openlocfilehash: 89a30c070abe3b10414c7284bb33f2c8216ee0c8
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35643099"
---
# <a name="sample-of-custom-source-connections-python"></a>Ukázka připojení vlastní zdroje (Python) 
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
