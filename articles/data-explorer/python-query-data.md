---
title: Dotazovat se na data pomocí knihovny Pythonu Průzkumníka dat Azure
description: V tomto článku se dozvíte, jak dotazovat data z Azure Data Explorer pomocí Pythonu.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: ebd65f2dcbb0040b764290627bbfd2901aa9a7d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443971"
---
# <a name="query-data-using-the-azure-data-explorer-python-library"></a>Dotazovat se na data pomocí knihovny Pythonu Průzkumníka dat Azure

V tomto článku můžete dotazovat data pomocí Průzkumníka dat Azure. Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie.

Azure Data Explorer poskytuje [datovou klientskou knihovnu pro Python](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data). Pomocí této knihovny můžete data dotazovat z kódu. Připojte se ke tabulce v *clusteru nápovědy,* kterou jsme nastavili pro pomoc učení. Můžete zadat dotaz na tabulku v tomto clusteru a vrátit výsledky.

Tento článek je taky k dispozici jako [poznámkový blok Azure](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueryKusto.ipynb).

## <a name="prerequisites"></a>Požadavky

* [Python 3.4+](https://www.python.org/downloads/)

* E-mailový účet organizace, který je členem služby Azure Active Directory (AAD)

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
KUSTO_DATABASE = "Samples"
```

Teď sestavte připojovací řetězec. V tomto příkladu se pro přístup ke clusteru používá ověřování pomocí zařízení. Můžete také použít [certifikát aplikace AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L24), [klíč aplikace AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L20)a [uživatele a heslo aheslom AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L34).

```python
KCSB = KustoConnectionStringBuilder.with_aad_device_authentication(
    KUSTO_CLUSTER)
KCSB.authority_id = AAD_TENANT_ID
```

## <a name="connect-to-azure-data-explorer-and-execute-a-query"></a>Připojení k Azure Data Exploreru a spuštění dotazu

Spusťte dotaz vůči clusteru a uložte výstup do datového rámce. Když se tento kód spustí, vrátí podobnou zprávu: *Pokud se chcete přihlásit, otevřete ve webovém prohlížeči stránku https://microsoft.com/devicelogin a zadejte kód F3W4VWZDM k ověření*. Podle pokynů se přihlaste a pak se vraťte a spusťte další blok kódu.

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
> [Ingestování dat pomocí knihovny Pythonu Průzkumníka dat Azure](python-ingest-data.md)
