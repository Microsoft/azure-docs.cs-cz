---
title: Připojení ke službě Azure Cosmos DB jako zdroje dat v aplikaci Azure Machine Learning Workbench | Dokumentace Microsoftu
description: Tento dokument obsahuje příklad o tom, jak se připojit ke službě Azure Cosmos DB pomocí Azure Machine Learning Workbench
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: 2d9562582a94b7c7aac5b972a7ef57b84eb9aa9e
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35644595"
---
# <a name="connecting-to-azure-cosmos-db-as-a-data-source"></a>Připojení ke službě Azure Cosmos DB jako zdroje dat
Tento článek obsahuje python ukázka umožňuje připojení ke službě Cosmos DB v aplikaci Azure Machine Learning Workbench.

## <a name="load-azure-cosmos-db-data-into-data-preparation"></a>Načtení dat služby Azure Cosmos DB do přípravu dat

Vytvoření nového toku dat založených na skriptech a pak použijte tento skript k načtení dat ze služby Azure Cosmos DB. 

```python
import pydocumentdb
import pydocumentdb.document_client as document_client

import pandas as pd

config = { 
    'ENDPOINT': '<Endpoint>',
    'MASTERKEY': '<Key>',
    'DOCUMENTDB_DATABASE': '<DBName>',
    'DOCUMENTDB_COLLECTION': '<collectionname>'
};

# Initialize the Python DocumentDB client.
client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})

# Read databases and take first since id should not be duplicated.
db = next((data for data in client.ReadDatabases() if data['id'] == config['DOCUMENTDB_DATABASE']))

# Read collections and take first since id should not be duplicated.
coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config['DOCUMENTDB_COLLECTION']))

docs = client.ReadDocuments(coll['_self'])

df = pd.DataFrame(list(docs))
```

## <a name="other-data-source-connections"></a>Další připojení zdrojů dat
Další ukázky, přečtěte si [příklad další zdrojová datová připojení](data-prep-appendix8-sample-source-connections-python.md)
