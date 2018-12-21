---
title: 'Rychlý start: Dotazování dat pomocí knihovny Python Průzkumník dat Azure'
description: V tomto rychlém startu se naučíte dotazovat data z Azure Data Exploreru pomocí Pythonu.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 10/16/2018
ms.openlocfilehash: f3a8dd001f4d1d930c700847f0725e00b7c29ecf
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53715105"
---
# <a name="quickstart-query-data-using-the-azure-data-explorer-python-library"></a>Rychlý start: Dotazování dat pomocí knihovny Python Průzkumník dat Azure

Azure Data Explorer je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Azure Data Explorer poskytuje [datovou klientskou knihovnu pro Python](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data). Pomocí této knihovny můžete data dotazovat z kódu. V tomto rychlém startu se připojíte k tabulce na *clusteru nápovědy*, který jsme zřídili pro podporu výuky. Pak provedete dotaz na tabulku v tomto clusteru a vrátíte výsledky.

Tento rychlý start je dostupný také v podobě [poznámkového bloku Azure](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueryKusto.ipynb).

## <a name="prerequisites"></a>Požadavky

* E-mailový účet organizace, který je členem služby Azure Active Directory (AAD)

* [Python](https://www.python.org/downloads/) nainstalovaný na počítači pro vývoj

## <a name="install-the-data-library"></a>Instalace datové knihovny

Nainstalujte *azure-kusto-data*.

```
pip install azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>Přidání příkazů a konstant pro import

Naimportujte z knihovny třídy a *pandas*, což je knihovna pro analýzu dat.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
import pandas as pd
```

K ověření aplikace používá Azure Data Explorer ID vašeho tenanta AAD. ID tenanta zjistíte pomocí následující adresy URL, ve které *YourDomain* nahradíte svou doménou.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Pokud je vaše doména například *contoso.com*, je adresa URL [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Kliknutím na tuto adresu URL zobrazte výsledky. První řádek vypadá jako v následujícím příkladu.

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

ID tenanta je v tomto případě `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. Před spuštěním tohoto kódu nastavte hodnotu AAD_TENANT_ID.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_CLUSTER = "https://help.kusto.windows.net/"
KUSTO_DATABASE  = "Samples"
```

Teď sestavte připojovací řetězec. V tomto příkladu se pro přístup ke clusteru používá ověřování pomocí zařízení. Můžete také použít [certifikát aplikace AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L24), [klíč aplikace AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L20), a [AAD uživatele a heslo](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L34).

```python
KCSB = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_CLUSTER)
KCSB.authority_id = AAD_TENANT_ID
```

## <a name="connect-to-azure-data-explorer-and-execute-a-query"></a>Připojení k Azure Data Exploreru a spuštění dotazu

Spusťte dotaz vůči clusteru a uložte výstup do datového rámce. Když tento kód se spustí, vrátí se zpráva podobná následující: *Pro přihlášení, použijte webový prohlížeč a otevřete stránku https://microsoft.com/devicelogin a zadejte kód F3W4VWZDM k ověření*. Podle pokynů se přihlaste a pak se vraťte a spusťte další blok kódu.

```python
KUSTO_CLIENT  = KustoClient(KCSB)
KUSTO_QUERY  = "StormEvents | sort by StartTime desc | take 10"

RESPONSE = KUSTO_CLIENT.execute(KUSTO_DATABASE, KUSTO_QUERY)
```

## <a name="explore-data-in-dataframe"></a>Zkoumání dat v datovém rámci

Po přihlášení vrátí tento dotaz výsledky, které jsou uložené v datovém rámci. S výsledky můžete pracovat stejně jako s jakýmkoli jiným datovým rámcem.

```python
df = dataframe_from_result_table(RESPONSE.primary_results[0])
df
```

Měli byste vidět prvních deset výsledků z tabulky StormEvents.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rychlý start: Ingestování dat pomocí knihovny Python Průzkumník dat Azure](python-ingest-data.md)
