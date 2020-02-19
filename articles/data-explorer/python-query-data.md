---
title: Dotazování na data pomocí knihovny Pythonu v Azure Průzkumník dat
description: V tomto článku se naučíte, jak zadávat dotazy na data z Azure Průzkumník dat pomocí Pythonu.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: ebd65f2dcbb0040b764290627bbfd2901aa9a7d3
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443971"
---
# <a name="query-data-using-the-azure-data-explorer-python-library"></a>Dotazování na data pomocí knihovny Pythonu v Azure Průzkumník dat

V tomto článku se dotazují na data pomocí Průzkumník dat Azure. Azure Data Explorer je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie.

Azure Data Explorer poskytuje [datovou klientskou knihovnu pro Python](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data). Pomocí této knihovny můžete data dotazovat z kódu. Připojte se k tabulce v *clusteru Help* , kterou jsme nastavili na podporu učení. Můžete zadat dotaz na tabulku v tomto clusteru a vrátit výsledky.

Tento článek je také k dispozici jako [notebook Azure](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueryKusto.ipynb).

## <a name="prerequisites"></a>Předpoklady

* [Python 3.4 +](https://www.python.org/downloads/)

* E-mailový účet organizace, který je členem služby Azure Active Directory (AAD)

## <a name="install-the-data-library"></a>Instalace datové knihovny

Nainstalujte *azure-kusto-data*.

```
pip install azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>Přidání příkazů pro import a konstant

Naimportujte z knihovny třídy a *pandas*, což je knihovna pro analýzu dat.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
import pandas as pd
```

K ověření aplikace používá Průzkumník dat Azure ID vašeho tenanta AAD. ID tenanta zjistíte pomocí následující adresy URL, ve které *YourDomain* nahradíte svou doménou.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Pokud je vaše doména například *contoso.com*, je adresa URL [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Kliknutím na tuto adresu URL zobrazte výsledky. První řádek vypadá jako v následujícím příkladu.

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

V tomto případě je ID tenanta `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. Před spuštěním tohoto kódu nastavte hodnotu AAD_TENANT_ID.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_CLUSTER = "https://help.kusto.windows.net/"
KUSTO_DATABASE = "Samples"
```

Teď vytvořte připojovací řetězec. V tomto příkladu se pro přístup ke clusteru používá ověřování pomocí zařízení. Můžete také použít [certifikát aplikace AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L24), [klíč aplikace AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L20)a [uživatele a heslo AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L34).

```python
KCSB = KustoConnectionStringBuilder.with_aad_device_authentication(
    KUSTO_CLUSTER)
KCSB.authority_id = AAD_TENANT_ID
```

## <a name="connect-to-azure-data-explorer-and-execute-a-query"></a>Připojení k Azure Data Exploreru a spuštění dotazu

Spusťte dotaz vůči clusteru a uložte výstup do datového rámce. Když se tento kód spustí, vrátí podobnou zprávu: *Pro přihlášení použijte webový prohlížeč a otevřete stránku https://microsoft.com/devicelogin a zadejte kód F3W4VWZDM k ověření*. Podle pokynů se přihlaste a pak se vraťte a spusťte další blok kódu.

```python
KUSTO_CLIENT = KustoClient(KCSB)
KUSTO_QUERY = "StormEvents | sort by StartTime desc | take 10"

RESPONSE = KUSTO_CLIENT.execute(KUSTO_DATABASE, KUSTO_QUERY)
```

## <a name="explore-data-in-dataframe"></a>Zkoumání dat v datovém rámci

Po přihlášení vrátí tento dotaz výsledky, které jsou uložené v datovém rámci. S výsledky můžete pracovat stejně jako s jakýmkoli jiným datovým rámcem.

```python
df = dataframe_from_result_table(RESPONSE.primary_results[0])
df
```

Měli byste vidět prvních deset výsledků z tabulky StormEvents.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Ingestování dat pomocí knihovny Pythonu v Azure Průzkumník dat](python-ingest-data.md)
