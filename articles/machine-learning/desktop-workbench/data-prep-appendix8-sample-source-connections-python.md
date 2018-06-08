---
title: Příklad další data připojení ke zdroji možné přípravy dat Azure Machine Learning | Microsoft Docs
description: Tento dokument obsahuje sadu příklady připojení zdroje dat, které je možné při přípravě dat Azure Machine Learning
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: b87d88a5bb7846894c425e701a073707ddd1f3be
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831180"
---
# <a name="sample-of-custom-source-connections-python"></a>Ukázka připojení vlastní zdroje (Python) 
Před čtením této příloha, přečtěte si [Python rozšiřitelnost přehled](data-prep-python-extensibility-overview.md).

## <a name="load-data-from-dataworld"></a>Načtení dat z data.world

### <a name="prerequisites"></a>Požadavky

#### <a name="register-yourself-at-dataworld"></a>Sami zaregistrovat na data.world
Budete potřebovat token rozhraní API z webu data.world.

#### <a name="install-dataworld-library"></a>Nainstaluje data.world knihovny

Otevřete rozhraní příkazového řádku Azure Machine Learning Workbench výběrem **soubor** > **otevřete rozhraní příkazového řádku**.

```console
pip install git+git://github.com/datadotworld/data.world-py.git
```

Potom spustíte `dw configure` na příkazovém řádku, které vás vyzve k zadání vašeho tokenu. Když zadáte token, .dw nebo adresář bude vytvořen v domovském adresáři a váš token je uložený existuje.

```
API token (obtained at: https://data.world/settings/advanced): <enter API token here>
```
Nyní byste měli mít importovat data.world knihovny.

#### <a name="load-data-into-data-preparation"></a>Načtení dat do data přípravy

Vytvořte transformace transformace toku dat (skript). Potom pomocí následujícího skriptu k načítání dat z data.world.

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#Load the dataset.
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset.
df = lds.dataframes['simpsons_episodes']

```
## <a name="azure-cosmos-db-as-a-data-source-connection"></a>Azure DB Cosmos jako připojení ke zdroji dat
Příklad Azure DB Cosmos jako datové připojení, najdete v tématu [zatížení Azure DB Cosmos jako připojení zdroje dat](data-prep-load-azure-cosmos-db.md)
